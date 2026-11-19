# Chapter 1 — Threat Model and SSH Hardening: Locking the Front Door

> **Course:** Linux Server Security & Secrets · **Chapter:** 1 of 8
> Read on the website: [learn.bazardev.com/linux-security/01-threat-model-and-ssh-hardening](https://learn.bazardev.com/linux-security/01-threat-model-and-ssh-hardening)
>
> 🇧🇩 **Prefer Bangla?** [Read this chapter in Bangla →](https://learn.bazardev.com/linux-security/01-threat-model-and-ssh-hardening?lang=bn)

> **Honesty note:** the commands in this chapter were not run against a live server while writing it — changing SSH settings on a real machine you are connected through is exactly the kind of thing that can lock you out, so this chapter teaches it carefully instead of demonstrating it on the writing server. Every setting and default named here comes from the official OpenSSH `sshd_config(5)` manual page and Ubuntu's own documentation, referenced at the end. Treat the commands as correct and ready to use, but always follow the "keep a second session open" rule in Section 4 before touching your own server.

In the Cloud course you opened port 22 in your firewall and logged in with a password or a key (Chapter 2), without asking what happens *after* that port is open to the entire internet. The answer: within minutes, not days. This chapter starts the Security course by being honest about that, then fixes the single highest-value target on any server — the SSH login itself.

## 1. The threat model: what is actually at risk

"Threat model" just means: *who* might attack this, *how*, and *what do they get if they win*. For a typical Laravel VPS:

- **Who:** mostly automated bots scanning the entire IPv4 address space for open port 22, not a human targeting you specifically. A smaller number of attacks are targeted, once your domain or company name is known.
- **How:** brute-force or credential-stuffing login attempts — trying common usernames (`root`, `admin`, `ubuntu`) with common or leaked passwords, over and over, from thousands of IPs (often a botnet).
- **What they get if they win:** on a typical VPS, SSH access means **root-equivalent access** the moment they reach a sudo-capable account — your database, your `.env` secrets, every customer's data, and a machine they can now use to attack others.

This is not hypothetical: a freshly provisioned VPS with password SSH login open to the internet reliably starts seeing failed-login attempts within hours. You do not need to be famous or have valuable data for bots to try — the scanning is indiscriminate.

## 2. The single most effective fix: disable password login entirely

Everything else in this chapter is useful. This one is the fix that matters most, because it removes the *entire category* of attack in Section 1: if a password can never be used to log in, there is nothing left to brute-force.

You already have an SSH key pair from the Cloud course (Chapter 2) — a **private key** that never leaves your laptop, and a **public key** that was copied to the server's `~/.ssh/authorized_keys`. Key-based login works by the server challenging your client to prove it holds the private key matching a public key it already trusts; the private key itself is never transmitted. This is fundamentally different from a password: there is nothing reusable for an attacker to steal from a login attempt, and there is no fixed secret short enough to guess by brute force (a modern Ed25519 key is computationally infeasible to guess).

### 2.1 Checking your key setup before touching anything

Before disabling passwords, confirm key login already works and the files have the right permissions — a wrong permission silently breaks key auth, which is a problem you want to discover *before* passwords stop working, not after:

```bash
ls -la ~/.ssh/
```

```
drwx------  2 dev dev 4096 ... .ssh
-rw-------  1 dev dev  411 ... authorized_keys
```

OpenSSH refuses to use `authorized_keys` if the permissions are too open — it is a deliberate safety check. The directory must be `700` (`drwx------`) and the file `600` (`-rw-------`); fix them if different:

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

### 2.2 Editing `sshd_config`

The server's SSH daemon is configured by `/etc/ssh/sshd_config` (on Ubuntu, it may also read extra files from `/etc/ssh/sshd_config.d/*.conf`, applied in filename order, with the main file's settings normally taking precedence over anything the same directive sets later — when in doubt, check both locations for the directive you're editing). Open it with root privileges:

```bash
sudo nano /etc/ssh/sshd_config
```

Set, or add if missing:

```
PubkeyAuthentication yes
PasswordAuthentication no
KbdInteractiveAuthentication no
PermitRootLogin no
```

- **`PubkeyAuthentication yes`** — explicit, even though it is the default, so the intent is clear in the file.
- **`PasswordAuthentication no`** — the actual fix: a password can no longer be used to start a session at all.
- **`KbdInteractiveAuthentication no`** — disables the keyboard-interactive method too (PAM-based challenge/response); some distributions allow a password through this path even with `PasswordAuthentication no`, so both must be off.
- **`PermitRootLogin no`** — nobody logs in directly as `root` over SSH, ever. An administrator logs in as their own user and uses `sudo` for anything that needs root — which also means every privileged action is attributed to a named person in the logs, not an anonymous "root" (more on this in Chapter 6, least privilege).

## 3. Why disabling root login matters on its own

Even with password login already off, `PermitRootLogin no` is still worth setting explicitly, for two reasons that have nothing to do with passwords:

1. **`root` is a known, fixed username.** Every brute-force list in existence tries it first. A normal admin account with an unguessable, unpublished username is a smaller target than a name every attacker already knows to try.
2. **Accountability.** If three admins all `ssh root@server`, your logs show "root logged in" three times with no way to tell who. If they each log in as themselves and `sudo`, the logs show exactly who did what — essential once something needs investigating.

## 4. The rule that saves you from locking yourself out

**Before restarting the SSH service, open a second terminal and start a new SSH session — and keep it open.** Do not close your current, working session until the new one, under the new settings, has been tested and confirmed to work. This is the single most important operational habit in this entire course: an `sshd_config` typo or an untested key can lock you out of a machine with no other access path, turning a two-minute edit into a support ticket with your VPS provider (or a reinstall).

```bash
sudo sshd -t
```

`sshd -t` parses the config file for syntax errors and exits without applying anything — run it before restarting, every time. A clean run prints nothing and exits 0.

```bash
sudo systemctl restart ssh
```

Now, **in your still-open first session**, open a brand new third terminal (or reuse the second) and try logging in fresh, exactly how you would after being disconnected:

```bash
ssh -v youruser@your-server-ip
```

The `-v` (verbose) flag shows exactly which authentication methods were offered and which one succeeded — confirm it says something like `Authenticated using "publickey"` and that a password prompt never appeared. Only once that new session is in and working should you close the original one.

## 5. Changing the SSH port: what it actually buys you

A very common piece of advice is to move SSH off port 22 to something like 2222. Being honest about what this does and does not do:

- **What it does:** cuts out the enormous volume of fully-automated, opportunistic scanners that only ever check port 22. Your logs get quieter, which makes real signal easier to see.
- **What it does not do:** stop a targeted attacker. A port scan (`nmap -p-`, scanning all 65535 ports) finds your SSH service on any port in seconds. This is **security through obscurity** — a minor, real reduction in noise, not a real security boundary. It is worth doing *after* the fixes above, never instead of them.

If you do change it, update three places together, and test with the Section 4 rule before closing your session: the `Port` directive in `sshd_config`, your firewall rule (Chapter 2 of this course) to allow the new port instead of 22, and the `-p` flag (or your `~/.ssh/config` `Port` entry) in every client you connect from.

## 6. Restricting *who* can even attempt to log in

`AllowUsers` and `AllowGroups` in `sshd_config` take this further than disabling root: they name exactly which accounts may authenticate over SSH at all, rejecting anyone else before password or key checking even begins.

```
AllowUsers dev deploy
```

Only the listed users can SSH in — a new or compromised local account (say, one created by a vulnerable application running on the box) cannot be used over SSH even if an attacker somehow sets a password or key for it. `AllowGroups` does the same by Unix group instead of listing individual usernames, which scales better once you have more than a couple of admins.

## 7. Seeing attempts against your own server

Two read-only commands show what has actually been happening, no special install needed:

```bash
sudo journalctl -u ssh --since "1 hour ago" | grep -i "fail\|invalid"
```

```
sshd[2041]: Invalid user admin from 203.0.113.44 port 51022
sshd[2055]: Failed password for root from 198.51.100.9 port 44110
```

(On some distributions the unit is named `sshd` instead of `ssh` — `systemctl status ssh sshd` shows which one is actually active on yours.) Every line here is exactly the Section 1 threat model made concrete: unknown IPs trying `admin` and `root`. After the fixes in this chapter, `Failed password` lines stop appearing entirely — there is no password path left to fail on — and `Invalid user` lines against anything not in your `AllowUsers` list are rejected before authentication is even attempted.

## 8. Key takeaways

- Treat every internet-facing SSH port as already being scanned — the question is whether your server is ready, not whether it will be found.
- **`PasswordAuthentication no`** (plus `KbdInteractiveAuthentication no`) removes brute-force login as a category of attack entirely — the highest-value single setting in this chapter.
- **`PermitRootLogin no`** removes the one username every attacker already knows to try, and makes every privileged action attributable to a real person via `sudo`.
- **Always** keep a working session open, run `sshd -t`, and test a fresh login in a new session before closing the old one — this is what prevents locking yourself out.
- Changing the SSH port reduces automated noise; it is not a substitute for the fixes above.
- `AllowUsers` / `AllowGroups` restrict *which accounts* may even attempt SSH authentication.

## 9. Test yourself (exam prep)

<details>
<summary><strong>Q1. Why does disabling password authentication remove brute-force SSH attacks as a threat?</strong></summary>

Brute-forcing depends on guessing a password. If no password can ever be used to authenticate, there is nothing left to guess — the entire attack category no longer applies, regardless of how weak any account's old password might have been.
</details>

<details>
<summary><strong>Q2. Why set <code>KbdInteractiveAuthentication no</code> in addition to <code>PasswordAuthentication no</code>?</strong></summary>

Some systems can still accept a password through the keyboard-interactive (PAM challenge/response) method even with password authentication itself disabled. Both need to be off to fully close the password path.
</details>

<details>
<summary><strong>Q3. Give two separate reasons <code>PermitRootLogin no</code> is worth setting, even with passwords already disabled.</strong></summary>

(1) `root` is a fixed, known username every attacker already tries, so removing it as a valid login shrinks the target. (2) It forces admins to log in as themselves and use `sudo`, so privileged actions are attributable to a real person in the logs instead of showing as an anonymous "root".
</details>

<details>
<summary><strong>Q4. What must you do before restarting the SSH service after changing <code>sshd_config</code>, and why?</strong></summary>

Keep your current, working SSH session open, and test the new configuration from a brand-new session first. If the new config is broken, you still have your original session to fix it from — closing it first can lock you out with no other way in.
</details>

<details>
<summary><strong>Q5. What does <code>sshd -t</code> do?</strong></summary>

It checks the `sshd_config` file for syntax errors and exits without applying anything — a safe way to catch a typo before restarting the real service.
</details>

<details>
<summary><strong>Q6. Does moving SSH to a non-default port stop a targeted attacker? Why or why not?</strong></summary>

No. A full port scan finds a listening SSH service on any port in seconds. It only reduces noise from automated scanners that only check port 22 — it is not a real security boundary on its own.
</details>

<details>
<summary><strong>Q7. What does <code>AllowUsers dev deploy</code> in <code>sshd_config</code> do?</strong></summary>

Only the `dev` and `deploy` accounts are permitted to authenticate over SSH at all; any other account — even one with a valid key or password — is rejected before authentication is attempted.
</details>

<details>
<summary><strong>Q8. In <code>journalctl -u ssh</code> output, what would you expect to see for failed password attempts after this chapter's fixes are applied, and why?</strong></summary>

No `Failed password` lines at all — once `PasswordAuthentication no` is set, there is no password authentication path left for an attempt to fail on. `Invalid user` lines for unknown usernames can still appear, now rejected even earlier if they're outside an `AllowUsers` list.
</details>

## 10. Free resources

- OpenSSH: [`sshd_config(5)` manual page](https://man.openbsd.org/sshd_config)
- Ubuntu Server Documentation: [OpenSSH](https://ubuntu.com/server/docs/openssh-server)
- DigitalOcean: [SSH Essentials: Working with SSH Servers, Clients, and Keys](https://www.digitalocean.com/community/tutorials/ssh-essentials-working-with-ssh-servers-clients-and-keys)
- `man sshd_config`, `man journalctl`

---

**Next:** [Chapter 2 — Firewalls: UFW and nftables, default-deny done right](02-firewalls-ufw-and-nftables.md)

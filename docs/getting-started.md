# Getting started with RoboCodo

[Home](../README.md) · [Using RoboCodo](using-robocodo.md) · [Troubleshooting](troubleshooting.md)

This guide takes you from two prepared computers to your first isolated Codex task. RoboCodo runs on **Windows x64**. Development files, Git, the RoboCodo remote helper, and Codex run on a **remote Linux machine** connected through SSH. WSL is not required by the installed Windows app.

In examples, replace `<linux-user>`, `<server-host>`, and `<ssh-port>` with your own values. `/home/<linux-user>/projects` means a directory on Linux, not a Windows folder. Never type angle-bracket placeholders unchanged into a command.

## 1. Prepare your Windows computer

1. Use a Windows x64 computer with network access to the Linux machine. Connect to its required VPN or private network if applicable.
2. Open **PowerShell on Windows** and check the OpenSSH client:

   ```powershell
   ssh -V
   ```

   You should see an OpenSSH version. If the command is unavailable, install **OpenSSH Client** through Windows Optional features, following [Microsoft's OpenSSH installation guide](https://learn.microsoft.com/en-us/windows-server/administration/openssh/openssh_install_firstuse). Windows needs the client; the SSH server belongs on Linux. Reopen PowerShell and RoboCodo after installation.
3. Obtain the Linux hostname, SSH port (often `22`), username, and projects-directory path. Also arrange access to a trusted server console or administrator for key authorization and fingerprint verification.

RoboCodo does not support interactive SSH password login. Have an existing authorized SSH identity or a separate way to access the Linux account to authorize a new key. A password-capable terminal outside RoboCodo or a trusted server console can be used for that initial setup.

## 2. Prepare the remote Linux account

Complete these prerequisites **on Linux, as the account you will enter in RoboCodo**. Ask your server administrator to provision anything you cannot set up yourself.

| Prerequisite | What to check |
| --- | --- |
| Reachable OpenSSH server | The server is enabled, accepts this account, and is reachable from Windows on the configured SSH port. |
| Git | `git --version` reports an installed version. |
| Node.js 24 | `node --version` reports `v24.x.x`. A different major version does not meet this requirement. |
| Existing projects directory | The account can read and enter it. It also needs write access where it will edit repositories and create task workspaces. |
| Codex CLI, for coding tasks | Codex is installed and authenticated under this same Linux account. |

Install Git using your Linux distribution's installation instructions. Install **version 24** using the [official Node.js download instructions](https://nodejs.org/en/download); choose the required major version explicitly.

For coding tasks, follow the official [Codex CLI installation guide](https://developers.openai.com/codex/cli/) and [authentication guide](https://developers.openai.com/codex/auth/) on the Linux machine. On a machine without a browser, use the authentication guide's remote/headless instructions. A Windows sign-in alone does not authenticate Codex for the Linux account.

In a **Linux terminal**, check the tools:

```bash
git --version
node --version
```

For coding tasks, also check:

```bash
codex --version
```

A version response confirms installation, not authentication. Complete Codex sign-in and verify it works under the intended Linux account before starting a task. Codex is optional for basic preparation and Git access.

Choose an existing projects folder, or create one yourself before preparation. For example, **on Linux**:

```bash
mkdir -p "$HOME/projects"
cd "$HOME/projects"
pwd
```

Use the absolute path printed by `pwd` in RoboCodo. If your folder is elsewhere, use its actual absolute path. Have a Git repository available there for your first task; clone or arrange a repository you are authorized to use through your normal Git workflow. RoboCodo preparation does not create this folder or install the repository's dependencies.

## 3. Install RoboCodo on Windows

1. Visit the [latest official public release](https://github.com/FE-Engineer-Youtube/robocodo-releases/releases/latest).
2. Read the release notes, expand **Assets**, and download the x64 `.exe`, named like `RoboCodo-Setup-<version>-x64.exe`.
3. Verify that you downloaded it from the official `FE-Engineer-Youtube/robocodo-releases` GitHub repository.
4. Run the installer and open RoboCodo.

The `.blockmap` and `latest.yml` assets are update metadata, not installers. Releases are currently unsigned, so Windows may show a security or publisher warning; see [installer troubleshooting](troubleshooting.md#unsigned-windows-installer-warnings). Download newer installers from the same release page when upgrading; do not assume automatic updates are enabled.

## 4. Add a development machine

In **RoboCodo on Windows**:

1. Open **Setup → Development machines**.
2. Choose **New machine**.
3. Enter the following details:

| Field | What to enter |
| --- | --- |
| Friendly machine name | A name you will recognize, such as `Development machine`. |
| Hostname or IP address | Your server's address, represented here as `<server-host>`. |
| SSH username | The intended Linux account, represented here as `<linux-user>`. |
| SSH port | The server's SSH port, often `22`. |
| Remote projects folder | An existing absolute Linux path, such as `/home/<linux-user>/projects`. Do not enter a Windows path or `~/projects`. |

The machine profile stores the connection settings and, after preparation, the detected working paths.

## 5. Select or generate an SSH identity

An SSH key pair has a **private key**, which stays secret, and a **public key**, which you authorize on Linux. It identifies your account to the server. This is different from the server's own key, which you verify in step 7.

Choose one identity method in **RoboCodo on Windows**:

- **Managed key:** generate a named RoboCodo-managed **Ed25519** key, select it for this machine, and copy the displayed public key. Ed25519 is the key type RoboCodo generates.
- **SSH agent:** use an existing agent identity. An SSH agent holds keys for the SSH client; the intended key must be loaded and available to the Windows client RoboCodo uses.
- **Private-key file:** select your existing private-key file on Windows. Select the private key, not its `.pub` public-key file. Its matching public key must be authorized on Linux.

**Generating or selecting a key does not authorize it on the server.** If an existing key is already authorized for the intended account, continue to fingerprint verification. Otherwise, authorize its matching public key next. If your key needs unlocking, make it available through your SSH agent before connecting.

## 6. Authorize the public key on Linux

Use a **trusted Linux console or an already working login outside RoboCodo**. If you have no such access, give only the public key to your administrator and ask them to authorize it for the intended account.

1. Sign in as the same `<linux-user>` configured in the machine profile. `~` and `$HOME` refer to the home directory of the account running these commands. Do not add the key to a different account's home directory.
2. Prepare that account's SSH files **in the Linux terminal**:

   ```bash
   mkdir -p "$HOME/.ssh"
   chmod 700 "$HOME/.ssh"
   touch "$HOME/.ssh/authorized_keys"
   chmod 600 "$HOME/.ssh/authorized_keys"
   ```

3. Open `~/.ssh/authorized_keys` in a text editor on Linux. Keep all existing keys. At the end of the file, start a **new line** and paste the **entire public key** copied from RoboCodo, then save. A generated key starts with `ssh-ed25519`, followed by key data and possibly a comment. Keep it as one line, without quotes or line breaks inside the key.
4. Confirm that the intended account owns `.ssh` and `authorized_keys`. Ask the administrator to correct ownership if needed. OpenSSH can reject files with unsafe ownership or permissions.

This file is the account's list of authorized keys: adding a public key permits its matching private key to authenticate. The directory and file permissions above keep access appropriately restricted; see the [OpenSSH authorized-keys documentation](https://man.openbsd.org/sshd#AUTHORIZED_KEYS_FILE_FORMAT).

**Never paste the private key onto the server or share it.** Never replace the whole `authorized_keys` file to add one key; that can remove other people's access. Do not use `>` redirection to overwrite it.

## 7. Verify and trust the server fingerprint

A **fingerprint** is a compact identifier of a key. The server's Ed25519 fingerprint lets you check that you are connecting to the intended Linux machine, not an impersonator.

1. In **RoboCodo on Windows**, inspect the server fingerprint for the configured host and port.
2. Obtain the expected **Ed25519** fingerprint through a trusted server console or a known administrator. At the **trusted Linux server console**, this command prints the fingerprint of the usual Ed25519 host public key:

   ```bash
   ssh-keygen -lf /etc/ssh/ssh_host_ed25519_key.pub -E sha256
   ```

   This reads a public host key, not a private key. If the server uses a different host-key location or you cannot read the file, ask its administrator for the fingerprint of the Ed25519 key actually used by that SSH server. See the [OpenSSH fingerprint command reference](https://man.openbsd.org/ssh-keygen#l).
3. Compare the complete `SHA256:…` fingerprint and key type with RoboCodo's displayed value.
4. Only when they match, confirm the fingerprint and trust the verified server in RoboCodo.

The fingerprint shown by a new, untrusted connection is not independent proof of identity. Do not trust it simply because the app can retrieve it. If values differ, stop and follow [fingerprint-conflict troubleshooting](troubleshooting.md#server-fingerprint-conflicts).

## 8. Save the initial profile

If **Prepare this machine** is not available until the profile exists, save the initial machine profile first. Continue preparation from that saved profile. This initial save is separate from preparation's automatic save of detected working paths.

## 9. Prepare this machine

**Does Prepare machine do everything? No. All prerequisites and SSH trust must already be in place.**

In **RoboCodo on Windows**, choose **Prepare this machine** for the configured profile.

Preparation:

1. Connects using the configured SSH identity.
2. Finds an existing **Node.js 24** installation.
3. Locates Codex when it is available.
4. Verifies access to the configured projects directory.
5. Securely uploads, fingerprints, installs, and probes the **bundled remote helper**, the program RoboCodo uses on Linux to support its operations.
6. Automatically saves the working executable and helper paths in the machine profile.

Preparation does **not**:

- Install Linux, or enable/configure its SSH server.
- Install Node.js 24 or Git.
- Install or authenticate Codex.
- Create your projects directory.
- Add your public key to `authorized_keys`.
- Decide whether to trust an unverified server fingerprint.
- Require administrator/root access or install system packages.

It runs with the configured account's permissions. Installing missing prerequisites is a separate action you or the administrator perform before retrying. A prepared machine without Codex can support basic preparation and Git access, but cannot run coding tasks.

After upgrading the Windows app, you may choose **Prepare this machine** again on an existing profile to install the newly bundled helper version and save the detected paths.

## 10. Test the connection

Choose **Test connection** in RoboCodo. Read the result and resolve any reported failures before continuing. Preparation installs and probes the helper and saves paths; the connection test checks the configured connection. A connection test is not a prerequisite installer or proof that Codex is authenticated and ready for a coding task.

See [preparation versus connection-test failures](troubleshooting.md#preparation-versus-connection-test-failures) if either step fails.

## 11. Connect

Choose **Connect**. You are now working with projects on the Linux machine through the Windows app. Confirm that the expected projects are available before starting work.

## 12. Start your first isolated task

1. Select a project and the Git repository you want to work on.
2. Create an **isolated task workspace**: a separate Git worktree, or working directory, for that task's changes. Check the repository and starting branch before proceeding.
3. Enter a small, specific prompt, for example: “Explain this repository's structure and suggest a small documentation improvement. Wait for my direction before editing.”
4. Start the task and follow its progress. Codex must be installed and authenticated on Linux for this step.
5. Continue with [Using RoboCodo](using-robocodo.md) to steer the task, review changes, and publish your work.

An isolated worktree separates working files; it is not a security sandbox. Use repositories and tasks you trust, and review changes before committing them.

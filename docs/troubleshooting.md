# Troubleshooting RoboCodo

[Home](../README.md) · [Getting started](getting-started.md) · [Using RoboCodo](using-robocodo.md)

Start with the first failing step: SSH reachability, identity authorization, server trust, prerequisites, preparation, connection test, then the task itself. Record the error and the step that produced it. When sharing an error publicly, remove usernames, hostnames, IP addresses, local paths, repository names, keys, and tokens.

## SSH connection failures

**Symptoms:** timeout, connection refused, hostname lookup failure, or no SSH client found.

1. **On Windows**, run `ssh -V` in PowerShell. If it is unavailable, install the OpenSSH client using the [Windows preparation instructions](getting-started.md#1-prepare-your-windows-computer), then reopen RoboCodo.
2. In **Setup → Development machines**, check the hostname, SSH username, and port. Confirm that you are connected to any required network or VPN.
3. **On Linux**, ask the administrator to confirm that OpenSSH server is running and reachable through the firewall on that port. A timeout commonly points to reachability; “connection refused” commonly means nothing is accepting the connection at that address and port.
4. If a connection reaches the server but authentication fails, continue with [key authorization](#key-not-authorized).

RoboCodo does not support interactive SSH password login. A successful password login in another terminal does not prove that RoboCodo's configured key works.

## Key not authorized

**Symptoms:** `Permission denied (publickey)`, rejected identity, or a key works for a different account but not this profile.

1. **On Windows**, check which managed key, agent identity, or private-key file is selected. For an agent, make sure the intended key is loaded and available to the Windows OpenSSH client. For a file, select the private key, not the `.pub` file.
2. **On Linux**, through a trusted console or working login, inspect the intended account's `~/.ssh/authorized_keys`. It must contain the matching public key on one complete line. Generating a key in RoboCodo does not add it there.
3. Preserve existing entries. Follow the [authorization procedure](getting-started.md#6-authorize-the-public-key-on-linux) to add the missing public key and check ownership and permissions. The usual permissions are `700` for `.ssh` and `600` for `authorized_keys`.
4. Confirm with the administrator that public-key login is permitted for this account if the key still fails. Then retry in RoboCodo.

Do not regenerate keys repeatedly to troubleshoot an authorization problem; every new key needs its own matching public key authorized on the server. Do not share private keys.

## Server-fingerprint conflicts

**Symptoms:** an untrusted server prompt, a changed fingerprint, or a fingerprint that differs from your trusted reference.

1. Stop the connection attempt. Check the profile's hostname and port for mistakes.
2. Ask the administrator whether the server was rebuilt or its host key was deliberately changed. Obtain its current **Ed25519** fingerprint through a trusted console or known administrator.
3. Compare the complete fingerprint using the [verification procedure](getting-started.md#7-verify-and-trust-the-server-fingerprint).
4. Only after independently confirming the change should you update the saved trust for that server in RoboCodo and retry.

A mismatch can indicate the wrong server or an intercepted connection. Do not bypass host verification, erase all trusted-host records, or trust a newly displayed fingerprint just to clear the error. Preparation cannot decide trust for you.

## Node 24 not found

1. **On Linux**, sign in as the account in the machine profile and run `node --version`. The result must be `v24.x.x`.
2. If Node is missing or has a different major version, install Node.js 24 using the [prerequisite instructions](getting-started.md#2-prepare-the-remote-linux-account). Preparation does not install it.
3. If Node 24 works in your terminal but preparation cannot find it, inspect its location with `command -v node`. A shell version manager may make Node available only after interactive shell startup. Make the existing installation discoverable to the account's SSH environment and check any configured executable path.
4. Run **Prepare this machine** again so RoboCodo detects and saves the working paths, then choose **Test connection**.

Do not assume that an installation under a different Linux account is available to this one.

## Projects folder unavailable

1. Check that the profile uses an **absolute Linux path**, such as `/home/<linux-user>/projects`, rather than a Windows path, relative path, or `~/projects`.
2. **On Linux**, sign in as the configured account and try entering and listing that directory. The folder must already exist, and the account needs permission to enter its parent directories and read its contents.
3. Create the intended folder yourself if it is missing, or ask its owner for the appropriate access. Preparation does not create it. Task work also requires write access where repositories and workspaces will be changed.
4. Correct the profile if it points to the wrong place, then prepare and test again. Confirm that your intended Git repositories are available there.

Avoid broad permission changes such as making the directory writable by everyone. Fix access for the account that needs it.

## Codex missing or not authenticated

**Symptoms:** basic preparation or Git access works, but a coding task cannot start or requests authentication.

1. **On Linux as the configured account**, run `codex --version`. If unavailable, follow the official [Codex CLI installation instructions](https://developers.openai.com/codex/cli/).
2. Authenticate that installation using the official [Codex authentication guide](https://developers.openai.com/codex/auth/), including its remote/headless guidance if needed. Being signed in on Windows or under another Linux account is not enough.
3. If Codex works in an interactive terminal but is not found by RoboCodo, check `command -v codex` and its availability to the account's SSH environment.
4. Run **Prepare this machine** again to locate Codex and save the working path, then test and retry the task. Preparation discovers Codex when available; it does not install it or sign you in.

Codex is optional for basic machine preparation and Git access, but required and authenticated for coding tasks. A successful connection test does not establish that a coding task will succeed.

## Preparation versus connection-test failures

| Stage | Purpose | What to inspect when it fails |
| --- | --- | --- |
| **Prepare this machine** | Connects, finds existing tools, checks the projects folder, securely uploads/fingerprints/installs/probes the bundled helper, and automatically saves working paths. | SSH identity and trust first; then Node.js 24, folder access, and the reported upload/install/probe error. Check account permissions and available disk space if relevant to that error. |
| **Test connection** | Checks the configured connection after preparation. | Confirm the correct saved profile, reachable server, authorized identity, trusted fingerprint, and current working paths. |
| First coding task | Runs Codex in the selected Linux workspace. | Codex installation/authentication, repository/workspace access, and any project-specific tools or dependencies reported missing. |

If preparation is unavailable on a new profile, save that initial profile first. If preparation succeeds, it saves the detected executable/helper paths automatically.

For an existing profile after an app upgrade, run **Prepare this machine** again to install the newly bundled helper version, then test again. If an executable moved or a prerequisite was fixed, preparation can detect the working paths again. Neither preparation nor the connection test installs system packages or requires root access.

## Slow Git fetch or push

A **fetch** downloads commits and branch information from the Git hosting service; a **push** publishes local commits. These operations run from the Linux machine, so there are two connections to consider: Windows to Linux, and Linux to the Git host.

1. Read the operation's progress and wait for it to finish before starting another fetch or push. Large repositories and slow networks can take time.
2. If RoboCodo remains connected but Git is slow, check the **Linux machine's** network route and access to the Git provider. Check the provider's service status as well.
3. Confirm that Git authentication works for the Linux account. A terminal that prompts for credentials can reveal an authentication issue that an unattended operation cannot resolve interactively. Windows-to-Linux SSH authorization is separate from Linux-to-Git-provider authorization.
4. Check whether repository hooks or large-file transfers account for the delay. Inspect a specific error before retrying; do not repeatedly click publish or force-push to get past it.
5. If a push's outcome is unclear after disconnection, inspect the remote branch before retrying. The server may have accepted commits even if the app did not receive the final response.

If diagnosing in a Linux terminal, use the same repository and account, and avoid overlapping a manual Git operation with an operation or task still running in RoboCodo.

## Unsigned Windows installer warnings

RoboCodo releases are currently **unsigned**. Windows may show a security, SmartScreen, or unknown-publisher warning.

1. Verify that you opened the [official latest-release page](https://github.com/FE-Engineer-Youtube/robocodo-releases/releases/latest) in the `FE-Engineer-Youtube/robocodo-releases` repository.
2. Check that you downloaded the **x64 `.exe` installer**, not `.blockmap` or `latest.yml`, and read the release notes.
3. If Windows offers additional details, inspect them. Continue only if you trust the official download and your organization's policy permits it. If the download source is uncertain, cancel and obtain the installer from the official page.
4. If organizational policy blocks unsigned software, ask your administrator how to proceed. Do not disable Windows security protections to install it.

An unsigned installer cannot provide a verified publisher signature. These instructions do not promise signing or automatic updating; obtain new versions from the official release page.

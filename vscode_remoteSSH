# Set Up Remote Access to Ubuntu from Windows

This guide covers VS Code Remote SSH and Remote Desktop (RDP) for a new Ubuntu or Debian system. Replace example values with your own host, username, and file paths.

## Add your existing SSH public key on Linux

Do not copy your private key (`id_ed25519`, `id_rsa`, or similar) to Linux. Only install the matching public key (`.pub`).

On Windows PowerShell, display the public key and copy the complete line:

```powershell
Get-Content "$env:USERPROFILE\.ssh\id_ed25519.pub"
```

If your key uses a different filename, use that `.pub` file instead. Sign in to the Linux system through its console or another approved access method. On Linux, run:

```sh
mkdir -p ~/.ssh
chmod 700 ~/.ssh
printf '%s\n' 'PASTE_THE_COMPLETE_PUBLIC_KEY_LINE_HERE' >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
```

Replace the placeholder with the public key line you copied. Run the commands as the Linux account you will use with VS Code.

In VS Code, open a **local** integrated terminal with **Terminal > New Terminal**. It should show a Windows prompt, such as `PS C:\Users\your-name\...>`. Run this command in that terminal:

```powershell
ssh your-user@your-linux-host
```

If it logs in without asking for the Linux account password, key-based login is working. A prompt for the passphrase of an encrypted local key is normal. After confirming SSH access, use **Remote-SSH: Connect to Host...** from the Command Palette to open the remote VS Code window.

## Transfer and trust the CA certificate

Assume the CA certificate file already exists on Windows. In the VS Code local terminal, transfer it to the Linux user's home directory:

```powershell
scp 'C:\path\to\organization-ca.crt' your-user@your-linux-host:organization-ca.crt
```

Then connect to Linux from the VS Code local terminal:

```powershell
ssh your-user@your-linux-host
```

### Recommended: install it in the system trust store

On Ubuntu or Debian, run on Linux:

```sh
sudo install -m 0644 "$HOME/organization-ca.crt" /usr/local/share/ca-certificates/organization-ca.crt
sudo update-ca-certificates
```

Enter your Linux password if prompted. This enables system-wide CA trust for applications that use the operating system trust store.

Replace `organization-ca.crt` with the actual certificate filename if it differs. For a different Linux distribution, follow that distribution's instructions for adding a CA certificate to its trust store.

## Set up Remote Desktop (RDP) on Ubuntu

Follow these steps to enable Windows Remote Desktop connections to Ubuntu.

### Install and enable xrdp

Install the RDP server and Xorg backend, add the service account to the certificate group, and enable the services at boot:

```bash
sudo apt update
sudo apt install -y xrdp xorgxrdp
sudo adduser xrdp ssl-cert
sudo systemctl enable --now xrdp
```

The `xrdp-sesman` session manager is installed as a dependency.

### Verify the service

```bash
systemctl --no-pager --full status xrdp xrdp-sesman
sudo ss -ltnp '( sport = :3389 )'
```

Both services should be active, and `xrdp` should listen on port 3389.

### Connect from Windows

1. On Ubuntu, find its IP address:

   ```bash
   hostname -I
   ```

2. On Windows, open **Remote Desktop Connection** by running `mstsc`.
3. Enter the Ubuntu machine's IP address.
4. Sign in with the Ubuntu account's username and password. Select **Xorg** if the xrdp login screen asks for a session type.

The IP address can change, especially when assigned by DHCP. Check `hostname -I` again if the saved address no longer works.

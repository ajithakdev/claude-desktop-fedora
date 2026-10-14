# claude-desktop-fedora

Install and update Anthropic's official **Claude Desktop** (Linux beta) on Fedora.

Anthropic ships the Linux desktop app only as a `.deb` for Ubuntu and Debian
([docs](https://code.claude.com/docs/en/desktop-linux)). This script fetches that
official package from Anthropic's own repository, verifies it, and unpacks it into
`~/.local`. No sudo is needed for the app itself.

> Unofficial. Not affiliated with or endorsed by Anthropic. This repo contains only a
> shell script; it does not redistribute any Anthropic software.

## What it does

1. Downloads Anthropic's signing key from `downloads.claude.ai` and checks its
   fingerprint (`31DD DE24 DDFA B679 F42D 7BD2 BAA9 29FF 1A7E CACE`).
2. Verifies the signed `InRelease` file, then the `Packages` index checksum.
3. Downloads the newest `claude-desktop` `.deb` and checks its SHA256.
4. Unpacks it into `~/.local/lib/claude-desktop` and adds the launcher, menu entry,
   icons and the `claude://` link handler (needed for sign-in).

## Requirements

Fedora (tested on 44, GNOME/Wayland, x86_64; aarch64 untested, and `cowork` is x86_64 only), and:

```bash
sudo dnf install curl gnupg2 binutils tar
```

## Usage

```bash
git clone https://github.com/ajithakdev/claude-desktop-fedora
cd claude-desktop-fedora
./claude-desktop-fedora.sh install     # first install
./claude-desktop-fedora.sh update      # later: installs only if a newer version exists
./claude-desktop-fedora.sh version
./claude-desktop-fedora.sh uninstall   # keeps your sign-in in ~/.config/Claude
```

Then open **Claude** from your app menu and sign in.

There is no automatic update. Run `update` now and then, or add it to a user cron
job / systemd timer. Quit the app before updating.

## Cowork (optional, needs sudo)

The Cowork tab runs tasks in a QEMU/KVM virtual machine. Enable virtualization
(SVM / VT-x) in your BIOS first, then:

```bash
./claude-desktop-fedora.sh cowork
```

This installs `qemu-system-x86-core`, `edk2-ovmf` and `virtiofsd`, loads the
`vhost_vsock` module at boot, and adds you to the `kvm` group (log out and back in).

It also creates two symlinks, because the app looks for UEFI firmware only at
Debian's path:

```
/usr/share/OVMF/OVMF_CODE.fd -> /usr/share/edk2/ovmf/OVMF_CODE.fd
/usr/share/OVMF/OVMF_VARS.fd -> /usr/share/edk2/ovmf/OVMF_VARS.fd
```

## Known limits

- Fedora is not supported by Anthropic. A future release may need fixes here.
- Not included: the GNOME Shell search provider and the AppArmor profile from the
  `.deb` (Fedora uses SELinux).
- Computer Use and dictation are not in the Linux beta at all.

## License

MIT for this script. Claude Desktop itself is Anthropic's software under its own terms.

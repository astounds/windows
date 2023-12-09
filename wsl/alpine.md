# Alpine
Enable WSL support as **administrator**.

```cmd
dism /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
dism /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```

Reboot Windows.

```cmd
shutdown /r /t 0
```

Install [WSL 2 Linux Kernel](https://aka.ms/wsl2kernel), then configure WSL.

```cmd
wsl --set-default-version 2
```

Install, launch and configure [Alpine WSL](https://aka.ms/wslstore), then `exit` shell.

```cmd
wsl --list
wsl --setdefault Alpine
wsl --set-version Alpine 2
wsl --distribution Alpine --user root
```

Switch to rolling release.

```sh
sed -E 's/v\d+\.\d+/edge/g' -i /etc/apk/repositories
```

Update system.

```sh
apk update
apk upgrade --purge
```

Install packages.

```sh
apk add coreutils curl file git grep htop p7zip pv pwgen sshpass doas tmux tree tzdata wipe
apk add neovim openssh-client imagemagick pngcrush
```

Install `nvim` as default `vim`.

```sh
ln -s nvim /usr/bin/vim
```

Configure system.

```sh
curl -L https://raw.githubusercontent.com/qis/windows/master/wsl/tmux.conf -o /etc/tmux.conf
curl -L https://raw.githubusercontent.com/qis/windows/master/wsl/ash.sh -o /etc/profile.d/ash.sh
curl -L https://raw.githubusercontent.com/qis/windows/master/wsl/wsl.sh -o /etc/profile.d/wsl.sh
chmod 0755 /etc/profile.d/ash.sh /etc/profile.d/wsl.sh
```

Configure [doas]

Create `/etc/doas.conf`.

```sh
tee /etc/doas.conf >/dev/null <<'EOF'
# See doas.conf(5) and doas.d(5) for configuration details.
## Allow members of group wheel to execute any command
permit persist :wheel

## Same thing without a password
#permit nopass :wheel

## Allow tedu to run procmap as root without a password
#permit nopass tedu as root cmd /usr/sbin/procmap

## Allow members of group power to execute power commands
permit nopass :power cmd openrc-shutdown
permit nopass :power cmd runit-halt
permit nopass :power cmd runit-shutdown
permit nopass :power cmd halt
permit nopass :power cmd poweroff
permit nopass :power cmd reboot
permit nopass :power cmd shutdown
permit nopass :power cmd zzz

## Allow root user to execute any command
permit nopass root
EOF
```

Create `/etc/wsl.conf`.

```sh
tee /etc/wsl.conf >/dev/null <<'EOF'
[boot]
command="openrc default"
[automount]
enabled=true
options=case=off,metadata,uid=1000,gid=1000,umask=022
EOF
```

Disable message of the day.

```sh
sed -E 's/^(session.*pam_motd\.so.*)/#\1/' -i /etc/pam.d/*
```

Exit shell to release `/root/.ash_history`.

```sh
exit
```

Restart distribution to apply `/etc/wsl.conf` settings.

```cmd
wsl --terminate Alpine
wsl --distribution Alpine
```

Configure `nvim`.

```sh
doas rm -rf /etc/vim /etc/xdg/nvim; doas mkdir -p /etc/xdg
doas ln -s "${USERPROFILE}/vimfiles" /etc/vim
doas ln -s /etc/vim /etc/xdg/nvim
doas touch /root/.viminfo
touch ~/.viminfo
```

Clean home directory files.

```sh
doas rm -f /root/.ash_history
rm -f ~/.ash_history
```

Create **user** home directory symlinks.

```sh
ln -s "${USERPROFILE}/.gitconfig" ~/.gitconfig
ln -s "${USERPROFILE}/Documents" ~/documents
ln -s "${USERPROFILE}/Downloads" ~/downloads
ln -s /mnt/c/Workspace ~/workspace
mkdir -p ~/.ssh; chmod 0700 ~/.ssh
for i in authorized_keys config id_rsa id_rsa.pub known_hosts; do
  ln -s "${USERPROFILE}/.ssh/$i" ~/.ssh/$i
done
doas chown `id -un`:`id -gn` "${USERPROFILE}/.ssh"/* ~/.ssh/*
doas chmod 0600 "${USERPROFILE}/.ssh"/* ~/.ssh/*
```

## Development
Install basic development packages.

```sh
doas apk add binutils binutils-dev fortify-headers gcc g++ linux-headers libc-dev
doas apk add cmake make nasm ninja nodejs npm patch perl pkgconf python3 py3-pip sqlite swig z3
doas apk add libedit-dev libnftnl-dev libmnl-dev libxml2-dev
doas apk add curl-dev ncurses-dev openssl-dev xz-dev z3-dev
```

## Podman

Require fuse

```sh
doas apk add fuse-overlayfs
```

Set a large enough sub-space of ids (> 65536)

```sh
doas usermod --add-subuids 100000-200000 --add-subgids 100000-200000 $(id -un)
```

Check if the change has been applied to /etc/subuid and /etc/subgid.

Apply the change:

```sh
podman system migrate
```

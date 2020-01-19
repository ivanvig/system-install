# Install Xorg

`# pacman -S xorg-server xorg-xinit`

# Install bspwm, sxhkd, kitty, rofi, picom

`# pacman -S bspwm sxhkd kitty rofi picom`

# Install i3lock-color and polybar

`# yay -S polybar-git i3lock-color-git`

# Configure autologin

`# systemctl edit getty@tty1`

paste this:

`
[Service]
ExecStart=
ExecStart=-/usr/bin/agetty --autologin username --noclear %I $TERM
Type=simple
`

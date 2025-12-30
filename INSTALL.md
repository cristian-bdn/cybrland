# Installation Guide
> [!IMPORTANT]
> Terminal-first approach: This guide uses the command line extensively. Every command is explained and copy-paste ready. If you're new to terminals, this is a great opportunity to learn - by the end, you'll be more confident navigating your system.
> 
> Most tools used in Cybrland are TUI/CLI - fast, lightweight, and fitting the aesthetic.  
> (*TUI = Text UI, keyboard navigation in terminal; CLI = Command-line, pure terminal commands; GUI = Graphical UI, mouse-driven, visual windows*)

## Order
Recommended sequence to avoid dependency issues:

1. **kitty** - Terminal emulator (provides ANSI colors for everything else)
2. **fish** - Shell (optional but recommended)
3. **hyprland** - Window manager
4. **waybar** + **swaync** + **rofi** - UI components
5. Everything else - Utilities, themes, extras

Each component has its own setup guide.

## Prerequisites & Setup
### 1. Install base dependencies
On Arch Linux:
```sh
# Essential tools
sudo pacman -S kitty fish hyprland waybar rofi swaync

# Fonts
sudo pacman -S otf-geist-mono-nerd

# Optional utilities
sudo pacman -S broot btop cava firefox fzf lm_sensors micro nvtop starship yazi

# Hardware monitoring setup
sudo sensors-detect
```

### 2. Set up your text editor
You'll need a terminal text editor for configuration files. This guide uses $EDITOR as a placeholder.
Check what you have set:
```sh
echo $EDITOR
echo $VISUAL
```
If nothing prints, set them now (using fish):
```sh
# Quick edits in terminal
set -Ux EDITOR micro

# Full IDE/editor (can be resource-intensive)
set -Ux VISUAL code
```
Choose any editor you prefer (nano, vim, neovim). Just make sure it's installed first.

### 3. ANSI color dependency

> [!CAUTION]
> Critical setup step: Programs like btop, broot, fzf, yazi, and fish rely on your terminal's ANSI color palette. Configure kitty first, or colors will be incorrect.

#### Installation order:
1. Install kitty → kitty setup
2. Configure ANSI colors (included in kitty config)
3. Install other programs

#### Verify colors are working:
```sh
# Should show 16 colored bars labeled COLOR 0 through COLOR 15
for i in (seq 0 15)
	echo -e "\e[48;5;$i""m  COLOR $i  \e[0m"
end
```
If you use a different terminal, extract ANSI values from [CYBRkitty](./kitty/CYBRkitty.conf) and apply them to your terminal emulator. Make sure your terminal of choice supports truecolor and transparency.

## Pre-flight Checklist
Before installing, complete these steps to avoid conflicts:
### 1. Backup existing configs
```sh
mkdir -p ~/.backup_configs
cp -r ~/.config/hypr ~/.backup_configs/hypr 2>/dev/null
cp -r ~/.config/kitty ~/.backup_configs/kitty 2>/dev/null
cp -r ~/.config/waybar ~/.backup_configs/waybar 2>/dev/null
cp -r ~/.config/rofi ~/.backup_configs/rofi 2>/dev/null
cp -r ~/.config/btop ~/.backup_configs/btop 2>/dev/null
```
Easy rollback if something breaks.

### 2. Review autostart services
These dotfiles include systemd user services for:
- Wallpaper rotation daemon
- Idle/lock management (hypridle)
- Notification daemon (swaync)
- Status bar (waybar)

Check for conflicts with existing services:
```sh
systemctl --user list-units --type=service | grep -E 'idle|lock|notify|bar|wallpaper'
```
### 3. Optional: Test in isolation
Launch Hyprland with a test config before applying permanently:
```sh
cp -r ~/.config/hypr ~/.config/hypr_test
Hyprland --config ~/.config/hypr_test/hyprland.conf
```
### 4. Recommended: Create a revert script
Quick recovery if Hyprland won't start:
```sh
cat > ~/.local/bin/revert-hypr << 'EOF'
#!/usr/bin/env bash
mv ~/.config/hypr ~/.config/hypr_broken
cp -r ~/.backup_configs/hypr ~/.config/hypr
echo "Reverted to backup config"
EOF

chmod +x ~/.local/bin/revert-hypr
```
## Post-installation Verification
After setup, test these components:
**Visual/UI**:
- [ ] Waybar displays correctly (modules, colors, icons)
- [ ] Rofi launchers work (app launcher, power menu, etc)
- [ ] Kitty colors match screenshots
- [ ] Window decorations (blur, transparency, borders)
**Functionality**:
- [ ] Keybinds respond (try SUPER+ENTER for terminal)
- [ ] Wallpaper rotation works
- [ ] Screen lock triggers on idle
- [ ] Hardware monitoring (CPU/GPU/temps in waybar)
- [ ] Audio controls (volume, media playback)

**Troubleshooting:**
| Issue | Solution |
|------------|--------|
| Wrong colors | Verify kitty config loaded: `kitty --debug-config \| grep color` |
| Waybar missing modules | Check `journalctl --user -u waybar` for errors |
| Keybinds not working | Verify Hyprland loaded config: `hyprctl version` |
| Services not starting | Check `systemctl --user status [service-name]` |

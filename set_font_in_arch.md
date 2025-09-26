# 🎨 Setting Default Fonts on Arch Linux (with Emoji Support)

This guide shows how to configure **default fonts** in Arch Linux using **Fontconfig**.  
We’ll set a Nerd Font (for text + icons) and `Noto Color Emoji` (for emoji support).

---

## 1. Install fonts

```bash
# Nerd Font (example: JetBrains Mono Nerd Font Mono)
paru -S nerd-fonts-jetbrains-mono

# Emoji font
sudo pacman -S noto-fonts-emoji

# If install is broken, update mirror list
sudo pacman -Syu reflector
sudo reflector --latest 20 --protocol https --sort rate --save /etc/pacman.d/mirrorlist

```

Verify installation:

```bash
fc-list | grep -i "Noto Color Emoji"
fc-list | grep -i "Nerd Font"
```

---

## 2. System-wide font config (all users)

Edit (or create) `/etc/fonts/local.conf`:

```bash
sudo vim /etc/fonts/local.conf
```

Paste this:

```xml
<?xml version="1.0"?>
<!DOCTYPE fontconfig SYSTEM "fonts.dtd">
<fontconfig>
  <!-- Default monospace font -->
  <alias>
    <family>monospace</family>
    <prefer>
      <family>JetBrainsMono Nerd Font Mono</family>
      <family>Noto Color Emoji</family>
    </prefer>
  </alias>

  <!-- Default sans-serif font -->
  <alias>
    <family>sans-serif</family>
    <prefer>
      <family>JetBrainsMono Nerd Font</family>
      <family>Noto Color Emoji</family>
    </prefer>
  </alias>
</fontconfig>
```

Save and exit.

---

## 3. Per-user font config (recommended)

Instead of system-wide, you can apply defaults only for your user.

Create directory if missing:

```bash
mkdir -p ~/.config/fontconfig
```

Edit (or create) `~/.config/fontconfig/fonts.conf`:

```bash
vim ~/.config/fontconfig/fonts.conf
```

Paste the same content:

```xml
<?xml version="1.0"?>
<!DOCTYPE fontconfig SYSTEM "fonts.dtd">
<fontconfig>
  <!-- Default monospace font -->
  <alias>
    <family>monospace</family>
    <prefer>
      <family>JetBrainsMono Nerd Font Mono</family>
      <family>Noto Color Emoji</family>
    </prefer>
  </alias>

  <!-- Default sans-serif font -->
  <alias>
    <family>sans-serif</family>
    <prefer>
      <family>JetBrainsMono Nerd Font</family>
      <family>Noto Color Emoji</family>
    </prefer>
  </alias>
</fontconfig>
```

---

## 4. Reload font cache

```bash
fc-cache -fv
```

---

## 5. Test fonts and emoji

```bash
echo "Hello 👋 😃 🚀 ⚡️ 🐧 ✅"
```

If you see Nerd Font glyphs + emojis correctly, the setup is working.

---

✅ Done! Now **Nerd Font Mono** is your default monospace font, with **Noto Emoji** as fallback.

# macos-dots

Dotfiles für macOS: [Ghostty](https://ghostty.org) als Terminal, darin tmux. Beide Configs teilen sich die TokyoNight-Palette und sind in den Tastenbelegungen aufeinander abgestimmt.

## Setup

### 1. Abhängigkeiten

```sh
brew install tmux
brew install --cask ghostty                        # falls Ghostty noch fehlt
brew install --cask font-jetbrains-mono-nerd-font
```

Ghostty bringt JetBrains Mono auch selbst mit. Die Nerd-Font-Variante stellt sicher, dass `font-family = "JetBrainsMono Nerd Font Mono"` die installierte Schrift findet. Prüfen mit:

```sh
ghostty +list-fonts --family="JetBrainsMono Nerd Font Mono"
```

### 2. Configs verlinken

Die Dateien bleiben im Repo, im System liegen nur Symlinks darauf:

```sh
ln -s ~/spaceland/macos-dots/tmux.conf ~/.tmux.conf
mkdir -p ~/.config/ghostty
ln -s ~/spaceland/macos-dots/ghostty/config ~/.config/ghostty/config
```

Liegt dort schon eine Datei, bricht `ln -s` ab. Die alte Datei dann vorher sichern oder entfernen.

Ghostty liest danach zusätzlich `~/Library/Application Support/com.mitchellh.ghostty/config.ghostty`. Was dort steht, überschreibt das Repo, die Datei sollte also leer bleiben oder ganz fehlen.

### 3. Ghostty neu starten

Ghostty einmal ganz beenden (`cmd+q`) und neu starten. Beim Start fragt macOS nach der Bedienungshilfen-Freigabe. Die braucht die globale Taste `cmd+^`, die das Quick Terminal von oben einblendet. Ohne Freigabe tut die Taste nichts, eine Fehlermeldung gibt es nicht. Nachtragen lässt sich die Freigabe unter Systemeinstellungen → Datenschutz & Sicherheit → Bedienungshilfen.

### 4. Beim Login starten

Die Config setzt `initial-window = false`: Ghostty startet ohne Fenster und wartet im Hintergrund auf `cmd+^`. Damit das schon ab dem Login gilt, Ghostty als Anmeldeobjekt eintragen. Am einfachsten per Rechtsklick aufs Dock-Icon → Optionen → Bei der Anmeldung öffnen. Der Eintrag erscheint dann unter Systemeinstellungen → Allgemein → Anmeldeobjekte.

Alternativ, etwa für ein Setup-Skript:

```sh
osascript -e 'tell application "System Events" to make login item at end with properties {path:"/Applications/Ghostty.app", hidden:true}'
```

`initial-window = false` gilt auch beim Start von Hand, etwa über Spotlight. Dann erscheint ebenfalls kein Fenster. Ein Klick aufs Dock-Icon oder `cmd+n` öffnet eins.

## Prüfen & neu laden

```sh
ghostty +validate-config --config-file=ghostty/config
tmux source-file ~/.tmux.conf
```

In Ghostty lädt `cmd+shift+,` die Config neu.

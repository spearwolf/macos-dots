# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Was das ist

Dotfiles für macOS, derzeit zwei Configs: `tmux.conf` und `ghostty/config`. Kein Build, kein Install-Skript, keine Tests. Kommentare in den Configs sind deutsch — neue Kommentare ebenso.

## Installation / Verlinkung

- `tmux.conf` ist per Symlink aktiv: `~/.tmux.conf -> ~/spaceland/macos-dots/tmux.conf`. Änderungen wirken also direkt auf die laufende Umgebung.
- `ghostty/config` ebenso: `~/.config/ghostty/config -> ~/spaceland/macos-dots/ghostty/config`. Ghostty lädt danach noch `~/Library/Application Support/com.mitchellh.ghostty/config.ghostty` (leer) — was dort landet, überschreibt das Repo.

## Prüfen & Neu laden

```sh
ghostty +validate-config --config-file=ghostty/config   # Exit 0 = gültig
ghostty +show-config --default --docs                    # alle Optionen mit Doku
tmux source-file ~/.tmux.conf                            # tmux-Config live neu laden
```

In Ghostty lädt `cmd+shift+comma` die Config neu; `ghostty +show-config` zeigt die tatsächlich wirksame Konfiguration.

## Aufbau von `ghostty/config`

Die Datei (~2100 Zeilen) ist ein vollständig ausgeschriebenes Ghostty-Template: jede Option steht mit ihrem Doku-Kommentar da, die meisten auf Default oder leer. Gesetzte Werte findet man mit:

```sh
grep -nEv '^\s*(#|$)' ghostty/config
```

Eine Option an ihrer bestehenden Stelle ändern, nicht unten anhängen — sonst existiert sie doppelt, und der spätere Wert gewinnt stillschweigend. Ausnahme ist bewusstes Überschreiben wie bei den `unbind`-Zeilen nach dem Keybind-Block.

Das Template stammt ursprünglich von Linux; die GTK-/X11-/FreeType-/cgroup-Optionen und der Dump der Linux-Default-Keybinds wurden entfernt. Nicht wieder einführen: Ghostty-Keybinds addieren sich zu den macOS-Defaults (cmd-basiert), jeder zusätzliche `ctrl+…`-Bind fängt Tasten ab, bevor tmux sie sieht. `command`/`initial-command` bleiben leer, damit die Login-Shell (`/opt/homebrew/bin/bash`) startet — `/bin/bash` wäre auf macOS Bash 3.2.

`macos-option-as-alt = left` ist bewusst nur links: deutsches Mac-Layout, die rechte Option-Taste wird für `@ ~ | [ ] { }` gebraucht.

## Kopplung zwischen Ghostty und tmux

Die beiden Configs sind aufeinander abgestimmt; wer eine ändert, muss die andere mitdenken:

- **Farben:** Beide nutzen die TokyoNight-Palette (Ghostty `theme = TokyoNight`, tmux-Statuszeile mit `#1a1b26`, `#7aa2f7`, `#bb9af7`, `#565f89`).
- **Cursor:** Ghostty setzt Block + Blink; tmux reicht DECSCUSR per `terminal-overrides` (`Ss`/`Se`) an Ghostty durch.
- **Scroll-Keybinds:** tmux bindet `C-S-Up/Down/PPage/NPage/Home/End` (ohne Prefix) zum Scrollen der Pane-History. Damit die Tasten tmux erreichen, darf Ghostty sie nicht selbst konsumieren — deshalb die expliziten `ctrl+shift+{up,down,page_up,page_down,home,end}=unbind` nach dem Keybind-Block. Neue Ghostty-Keybinds auf `ctrl+shift+…` gegen diese tmux-Bindings prüfen.
- **Scrollback:** Ghostty `scrollback-limit = 10000000` (Bytes), tmux `history-limit 100000` (Zeilen).

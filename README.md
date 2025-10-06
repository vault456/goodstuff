# goodstuff

## Bash Skript Start nohup loop

#!/usr/bin/env bash
set -euo pipefail

SCRIPT="/pfad/zu/deinem/script.sh"
PAUSE=300   # Sekunden (5 Minuten)
LOCKFILE="/tmp/loop.lock"

# Prüfen, ob Lockfile existiert
if [ -f "$LOCKFILE" ]; then
    if kill -0 "$(cat "$LOCKFILE")" 2>/dev/null; then
        echo "⚠️ Script läuft bereits mit PID $(cat "$LOCKFILE")."
        exit 1
    else
        echo "⚠️ Entferne veraltetes Lockfile."
        rm -f "$LOCKFILE"
    fi
fi

# Lockfile mit eigener PID schreiben
echo $$ > "$LOCKFILE"

# Lockfile beim Beenden löschen
cleanup() {
    echo "🧹 Entferne Lockfile"
    rm -f "$LOCKFILE"
}
trap cleanup EXIT INT TERM

# Endlosschleife
while true; do
    echo "▶️ Starte $SCRIPT um $(date)"
    bash "$SCRIPT"
    echo "⏸️ Warte $PAUSE Sekunden..."
    sleep "$PAUSE"
done

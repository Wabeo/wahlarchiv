# Wahlarchiv LTMV26: so prüfen Sie diese Dateien nach

Hier liegen die amtlichen Ergebnisdateien der Wahlleitung, so wie WABEO sie
im Lauf des Wahlabends abgerufen hat. Jeder Abruf ist eine eigene Datei.
Nichts davon stammt von uns, wir haben nur eingesammelt und aufbewahrt.

Sie müssen uns nicht glauben. Sie können nachrechnen.

## Was hier liegt

    <Tag>/…csv.gz               eine abgerufene Datei, gzip-gepackt
    <Tag>/…csv.headers.txt      die Antwortkopfzeilen des Servers dazu
    MANIFEST.jsonl              ein Eintrag je Abruf, mit Fingerabdruck
    ANKER.txt                   Fingerabdrücke über das gesamte Manifest

## Eine einzelne Datei prüfen

Der Fingerabdruck bezieht sich auf den **entpackten** Inhalt, also auf genau
die Bytes, die der Server geliefert hat, nicht auf unsere Verpackung.

    gunzip -c DATEI.csv.gz | sha256sum

Vergleichen Sie das Ergebnis mit dem Feld `sha256` der zugehörigen Zeile in
`MANIFEST.jsonl`.

**Achtung bei der Pfadangabe:** Das Feld `datei` im Manifest beginnt mit dem
Wahlcode, also etwa `LTMV26/2026-09-06/….csv.gz`. Es zählt von der
Ebene **über** diesem Ordner. Wenn Sie hier stehen, lassen Sie den ersten
Abschnitt weg oder gehen Sie ein Verzeichnis höher:

    cd ..
    gunzip -c LTMV26/2026-09-06/….csv.gz | sha256sum

## Die Kette prüfen

Jede Zeile im Manifest trägt im Feld `vorher_hash` den Fingerabdruck der
vorhergehenden Zeile. Wer nachträglich einen Eintrag ändert, entwertet damit
alle folgenden. Der Fingerabdruck einer Zeile (`zeilen_hash`) wird über den
Eintrag ohne dieses Feld selbst gebildet, mit alphabetisch sortierten
Schlüsseln.

## Warum es die Anker gibt

Die Kette beweist, dass das Manifest **in sich** stimmig ist. Sie beweist
nicht, dass es gestern schon so aussah: wer alles neu schreibt, bekommt wieder
eine stimmige Kette. Deshalb veröffentlicht WABEO regelmäßig einen **Anker**,
also einen Fingerabdruck über das gesamte bisherige Manifest, an Stellen
außerhalb der eigenen Reichweite. Diese Anker stehen in `ANKER.txt` und
zusätzlich unter <https://github.com/Wabeo/wahlarchiv>.

Prüfen Sie einen Anker gegen ein Manifest, das Sie **anderswo** her haben,
nicht gegen unsere Kopie. Genau darin liegt sein Wert.

## Gescheiterte Abrufe

Zeilen mit `"ergebnis":"fehler"` sind Abrufversuche ohne Ergebnis. Sie gehören
dazu und sind keine Panne. Ohne sie wäre eine Lücke im Archiv zweideutig: sie
könnte heißen, es gab nichts zu holen, oder wir haben nicht hingesehen.

## Rechte

Die Zusammenstellung, also Manifest, Anker und diese Anleitung, ist
gemeinfrei (CC0). Die amtlichen Ergebnisdaten selbst stammen von den
Landeswahlleitungen, daran beansprucht WABEO keine Rechte.

# DataLabTrash

Datenquelle:
https://www.kaggle.com/datasets/feyzazkefe/trashnet/data


⭐ SCHRITT 1 - Bibliotheken importien



---------------------------------------------------

⭐ SCHRITT 2 — Prüfen, ob dein Datensatz-Ordner existiert
Jetzt testen wir, ob VS Code den Ordner dataset-resized findet.
👉 Lege im Notebook eine neue Zelle an
und füge das hier ein:
PythonDATASET_DIR = "dataset-resized"# Prüfen, ob der Ordner existiertprint("Ordner gefunden:", os.path.isdir(DATASET_DIR))Weitere Zeilen anzeigen
Dann ausführen.

🔍 Was du als Ergebnis erwarten kannst

Wenn True kommt → perfekt, der Ordner ist da
Wenn False kommt → der Pfad ist falsch (und ich helfe dir direkt)

Das sieht dann so aus:
Ordner gefunden: True


---------------------------------------------------


⭐ SCHRITT 3 — Klassen automatisch aus den Ordnern einlesen
👉 Neue Zelle anlegen
👉 Diesen Code einfügen und ausführen:
Python# Alle Ordner im Dataset als Klassen einlesenclasses = sorted([    d for d in os.listdir(DATASET_DIR)    if os.path.isdir(os.path.join(DATASET_DIR, d))])classesWeitere Zeilen anzeigen

🔍 Was sollte rauskommen?
Etwa so:
['cardboard', 'glass', 'metal', 'paper', 'plastic', 'trash']

Wenn bei euch später neue Klassen dazukommen (z. B. „batteries“, „dangerous_waste“ usw.), erkennt der Code das automatisch.

⭐ Warum dieser Schritt wichtig ist

Ihr müsst Klassen nicht per Hand eingeben
Das Notebook erkennt beim nächsten Projektstart automatisch alle Daten
Euer Projekt wird sauber, flexibel und erweiterbar


---------------------------------------------------

⭐ SCHRITT 4 — Beispielbilder anzeigen
👉 Wir zeigen Beispielbilder aus eurem Datensatz an.
Damit kannst du sofort prüfen:

Sind die Bilder korrekt einsortiert?
Sehen sie gleich groß aus?
Gibt es verwackelte oder falsche Bilder?
Müssen wir später etwas bereinigen?

Und: Es sieht in der Präsentation mega professionell aus.
Wir machen es wieder ganz einfach, Einsteiger‑freundlich.

👉 Lege eine neue Zelle an
👉 Füge diesen Code ein:
Pythonimport randomdef show_examples(cls, n=4):    folder = os.path.join(DATASET_DIR, cls)    files = random.sample(os.listdir(folder), n)    plt.figure(figsize=(10, 5))    for i, file in enumerate(files):        img_path = os.path.join(folder, file)        img = Image.open(img_path)        plt.subplot(1, n, i+1)        plt.imshow(img)        plt.axis("off")    plt.suptitle(f"Beispiele für: {cls}")    plt.show()# Beispiel: zeige 4 Bilder der ersten Klasseshow_examples(classes[0])Weitere Zeilen anzeigen

🔍 Was passiert hier?

random.sample wählt zufällig 4 Bilder
wir öffnen jedes Bild
zeigen alle nebeneinander an
und schreiben den Klassennamen drüber

⭐ Erwartetes Ergebnis
Ein schönes 4er‑Bild‑Grid z. B. von:
cardboard oder glass oder metal etc.
Wenn alles funktioniert, kannst du z. B. weitere Klassen testen:
Pythonshow_examples("plastic")show_examples("trash")show_examples("pap


---------------------------------------------------


⭐ SCHRITT 5 — Bildgrößen analysieren
Bevor wir später alle Bilder sauber auf eine einheitliche Größe bringen (z. B. 224×224), müssen wir zuerst herausfinden:

Welche Größen haben die Bilder aktuell?
Gibt es Ausreißer?
Haben verschiedene Klassen unterschiedliche Größen?

Das hilft uns, die richtige Zielgröße und die passende Preprocessing‑Strategie zu wählen.

⭐ Schritt 5a — Größe eines Beispielbilds prüfen
👉 Neue Zelle anlegen
👉 Code einfügen:

# Größe eines Beispielbilds anzeigen
sample_class = classes[0]  # erste Klasse, z. B. cardboard
sample_file = os.listdir(os.path.join(DATASET_DIR, sample_class))[0]
sample_path = os.path.join(DATASET_DIR, sample_class, sample_file)

img = Image.open(sample_path)
print("Beispielbild:", sample_path)
print("Größe:", img.size)   # (Breite, Höhe)
``

⭐ Schritt 5b — Größen ALLER Bilder analysieren
👉 Neue Zelle anlegen
👉 Code einfügen:
sizes = []

for cls in classes:
    folder = os.path.join(DATASET_DIR, cls)
    for file in os.listdir(folder):
        img_path = os.path.join(folder, file)
        try:
            img = Image.open(img_path)
            sizes.append(img.size)
        except:
            print("Fehler bei Datei:", img_path)


Das sammelt alle Größen als Liste, z. B.:
[(384,512), (512,512), (300,400), ...]


⭐ Schritt 5c — Häufigkeit der Größen auswerten
👉 Neue Zelle:

from collections import Counter

size_counts = Counter(sizes)
size_counts.most_common(10)  # die 10

Dieser Schritt sagt uns:

ob die meisten Bilder gleiche Größe haben
wie stark sich die Größen unterscheiden
ob wir später resizing brauchen (spoiler: ja 😄)


⭐ Schritt 5d — Bildgrößen als Plot anzeigen
👉 Neue Zelle:
widths = [w for (w,h) in sizes]
heights = [h for (w,h) in sizes]

plt.figure(figsize=(6,5))
plt.scatter(widths, heights, alpha=0.3)
plt.xlabel("Breite")
plt.ylabel("Höhe")
plt.title("Bildgrößen im Datensatz")
plt.show

Damit bekommst du ein Scatter‑Plot, das so aussieht:

viele Punkte → viele verschiedene Größen
ein Haufen an einer Stelle → häufige Standardgröße


ERGEBNIS VON 5
Du hast herausgefunden:
[((512, 384), 2527)]

➡️ Alle Bilder haben exakt dieselbe Größe:
512 × 384 Pixel
➡️ Der Scatterplot hat nur einen einzigen Punkt
→ weil alle Bilder identisch groß sind.
Das macht eure Datenvorbereitung viel einfacher.
Wir können direkt zum nächsten Schritt übergehen.

⭐ Warum ist dieses Ergebnis so gut?

Ihr habt keine Ausreißerbilder
Ihr braucht keine Prüfung auf unterschiedliche Größen
Ihr habt keine gemischten Formate
Die Open‑Dataset‑Personen haben bereits alles vereinheitlicht
Resizing wird kinderleicht und fehlerfrei





---------------------------------------------------


⭐ SCHRITT 6 — Zielgröße festlegen (WICHTIG!)
Weil du Anfängerin bist, mache ich es super verständlich:
Deine aktuellen Bilder sind:

Breite: 512
Höhe: 384
Seitenverhältnis: 4:3

Wir müssen für die KI quadratische Bilder erzeugen (das wollen 99 % aller Modelle).
Du hast jetzt die Wahl zwischen:

✨ Option A — Zielgröße: 224 × 224 (Empfehlung)
Warum genau 224?

Standard für moderne CNNs (ResNet, MobileNet, EfficientNet)
Beste Balance aus:

Qualität
Rechengeschwindigkeit
Genauigkeit


Jeder ML‑Coach würde dir das empfehlen
Eure Gruppenmitglieder, die das Modell bauen, werden glücklich sein


---------------------------------------------------


⭐ SCHRITT 7 — ALLE Bilder auf 224×224 skalieren
Wir erstellen:

einen neuen Ordner: dataset-224
darin Unterordner für jede Klasse (cardboard, glass, …)
alle Bilder werden:

geöffnet
auf 224×224 resized
als neue Dateien gespeichert



👉 Wichtig:
Wir überschreiben NICHT den alten Ordner, damit ihr jederzeit zurück könnt.

⭐ Bitte lege eine neue Zelle an und füge diesen Code ein:
from PIL import Image
import os

# Original-Ordner
INPUT_DIR = "dataset-resized"
# Ziel-Ordner
OUTPUT_DIR = "dataset-224"

# Zielgröße
TARGET_SIZE = (224, 224)

# Zielordner erstellen
os.makedirs(OUTPUT_DIR, exist_ok=True)

for cls in classes:
    # Ordner für die Klasse im Zielverzeichnis anlegen
    input_class_path = os.path.join(INPUT_DIR, cls)
    output_class_path = os.path.join(OUTPUT_DIR, cls)
    os.makedirs(output_class_path, exist_ok=True)

    # Alle Bilder durchgehen
    for file in os.listdir(input_class_path):
        input_path = os.path.join(input_class_path, file)
        output_path = os.path.join(output_class_path, file)

        try:
            # Bild öffnen und konvertieren
            img = Image.open(input_path).convert("RGB")
            # Resizing durchführen
            img = img.resize(TARGET_SIZE)
            # Bild speichern
            img.save(output_path)
        except Exception as e:
            print("Fehler bei Datei:", input_path, "-", e)

print("Fertig! Alle Bilder wurden nach dataset-224 resized.")

⭐ Was passiert, wenn du das ausführst?

Ein neuer Ordner „dataset-224“ wird erstellt.
Für jede Klasse werden Unterordner erstellt.
Jedes Bild wird geöffnet, resized und gespeichert.
Am Ende bekommst du:
„Fertig! Alle Bilder wurden nach dataset-224 resized.“


⭐ WICHTIG nach dem Ausführen:
Bitte führe folgende Zelle aus, um zu prüfen, ob alles da ist:
print(os.listdir("dataset-224"))
Erwartung:
['cardboard', 'glass', 'metal', 'paper', 'plastic', 'trash']

Dann:
print(len(os.listdir("dataset-224/cardboard")))
Erwartung:
→ sollte ähnlich viele Dateien haben wie vorher (400+)

---------------------------------------------------

Schritt 7 ist erledigt, und wir sind jetzt bereit für einen der wichtigsten Parts der gesamten Data‑Lab‑Pipeline:
⭐ SCHRITT 8 — Train/Validation/Test Split automatisch erzeugen
Damit euer ML‑Team ein Modell trainieren kann, braucht ihr eine saubere Unterteilung der Daten:

Train (70 %) → Modell lernt hier
Validation (15 %) → Modell prüft während des Trainings
Test (15 %) → echte, finale Bewertung

Wir machen das ordnerbasiert, genau so wie es alle modernen Frameworks erwarten.
Wir verwenden euren neuen, perfekten Ordner:
dataset-224/

Train/Val/Test Split erstellen
👉 Neue Zelle anlegen
👉 Code einfügen:
import os
import shutil
from sklearn.model_selection import train_test_split

# Eingangsordner (resized dataset)
INPUT_DIR = "dataset-224"

# Zielordner für Split
OUTPUT_BASE = "dataset-split"

# Anteile
train_ratio = 0.70
val_ratio = 0.15
test_ratio = 0.15

# Zielstruktur anlegen
for split in ["train", "val", "test"]:
    for cls in classes:
        os.makedirs(os.path.join(OUTPUT_BASE, split, cls), exist_ok=True)

# Jeden Klassenordner durchgehen
for cls in classes:
    cls_dir = os.path.join(INPUT_DIR, cls)
    files = os.listdir(cls_dir)

    # Train/Val/Test Split
    train_files, temp_files = train_test_split(
        files, test_size=(1 - train_ratio), random_state=42
    )

    val_files, test_files = train_test_split(
        temp_files, test_size=(test_ratio / (test_ratio + val_ratio)), random_state=42
    )

    # Dateien verschieben
    for f in train_files:
        shutil.copy(os.path.join(cls_dir, f), os.path.join(OUTPUT_BASE, "train", cls, f))

    for f in val_files:
        shutil.copy(os.path.join(cls_dir, f), os.path.join(OUTPUT_BASE, "val", cls, f))

    for f in test_files:
        shutil.copy(os.path.join(cls_dir, f), os.path.join(OUTPUT_BASE, "test", cls, f))

print("Train/Val/Test Split fertig erzeugt!")

✔ Ergebnis dieser Zelle
Nach dem Ausführen entsteht eine perfekte Ordnerstruktur:
dataset-split/
  train/
    cardboard/
    glass/
    metal/
    paper/
    plastic/
    trash/
  val/
    ...
  test/
    ...


    ⭐ SCHRITT 8B — Schnell prüfen, ob der Split funktioniert hat
Neue Zelle:
for split in ["train", "val", "test"]:
    print(split)
    for cls in classes:
        count = len(os.listdir(os.path.join("dataset-split", split, cls)))
        print(f"  {cls}: {count} Bilder")


Damit siehst du, ob:

jede Klasse sauber verteilt wurde
die Verhältnisse ungefähr stimmen

---------------------------------------------------

⭐ SCHRITT 9 — Datenaugmentation (optional, aber sehr sinnvoll)
Warum ist Augmentation wichtig?

Die Klassen sind ungleich verteilt (trash viel mehr als metal/cardboard)
Augmentation erzeugt künstlich mehr Variationen der weniger vertretenen Klassen
Das verbessert die Modell‑Robustheit und verhindert Overfitting
Euer ML‑Team wird davon profitieren

Wir machen eine nicht-zerstörerische Augmentation:
➡️ Die Originalbilder bleiben unangetastet
➡️ Wir speichern augmentierte Bilder in einem separaten Ordner
➡️ Ihr entscheidet später, ob Training damit erfolgen soll

⭐ Augmentation‑Ordner anlegen
Wir erzeugen folgende Struktur:
dataset-augmented/
    train/
        cardboard/
        glass/
        ...

Und augmentieren nur die Trainingsdaten — niemals validation/test.

⭐ SCHRITT 9 — Code zur Datenaugmentation
👉 Neue Zelle anlegen
👉 Code einfügen:
from PIL import Image, ImageEnhance
import os
import random

INPUT_DIR = "dataset-split/train"
OUTPUT_DIR = "dataset-augmented/train"

os.makedirs(OUTPUT_DIR, exist_ok=True)

# Definiere Augmentationen
def augment_image(img):
    # leichtes Drehen
    if random.random() < 0.5:
        angle = random.randint(-15, 15)
        img = img.rotate(angle)

    # horizontales Spiegeln
    if random.random() < 0.5:
        img = img.transpose(Image.FLIP_LEFT_RIGHT)

    # Helligkeit variieren
    if random.random() < 0.4:
        enhancer = ImageEnhance.Brightness(img)
        img = enhancer.enhance(random.uniform(0.7, 1.3))

    # Kontrast variieren
    if random.random() < 0.4:
        enhancer = ImageEnhance.Contrast(img)
        img = enhancer.enhance(random.uniform(0.7, 1.3))

    return img


for cls in classes:
    input_class_path = os.path.join(INPUT_DIR, cls)
    output_class_path = os.path.join(OUTPUT_DIR, cls)
    os.makedirs(output_class_path, exist_ok=True)

    files = os.listdir(input_class_path)

    # z. B. pro Bild 2 augmentierte Versionen erzeugen
    for file in files:
        img_path = os.path.join(input_class_path, file)
        img = Image.open(img_path).convert("RGB")

        # Original kopieren
        img.save(os.path.join(output_class_path, file))

        # 2 augmentierte Versionen erzeugen
        for i in range(2):
            aug = augment_image(img.copy())
            base, ext = os.path.splitext(file)
            aug.save(os.path.join(output_class_path, f"{base}_aug{i}{ext}"))

print("Datenaugmentation fertig!")

⭐ Was dieser Code macht
Für jedes Trainingsbild:

das Original wird kopiert
zwei augmentierte Versionen werden erzeugt
jede kann haben:

leichte Rotation (–15° bis +15°)
horizontales Spiegeln
hell/dunkel Variation
Kontrastvariation



Dadurch entsteht ein robusterer Datensatz.

⭐ Welche Ordner entstehen?
dataset-augmented/train/
    cardboard/
        0001.jpg
        0001_aug0.jpg
        0001_aug1.jpg
        ...

Die Anzahl Bilder je Klasse verdreifacht sich ungefähr.

⭐ SCHRITT 9B — Prüfen, ob es geklappt hat
👉 Neue Zelle:
for cls in classes:
    count = len(os.listdir(os.path.join("dataset-augmented/train", cls)))
    print(cls, ":", count)

---------------------------------------------------


⭐ Was als nächstes kommt
Du hast jetzt eine komplette professionelle ML‑Datenpipeline fertig:

Datenprüfung
Beispielbilder
Größenanalyse
Resize auf 224×224
Sauberer Train/Val/Test Split
Datenaugmentation

Damit könnt ihr jetzt direkt in die Modellphase starten — perfekt vorbereitet.
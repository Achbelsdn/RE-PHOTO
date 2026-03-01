# RE-PHOTO 📸

Un outil HTML/JS entièrement local qui transforme n'importe quelle image de ta galerie en une photo d'apparence authentique — avec métadonnées EXIF complètes, bruit de capteur, vignette, et nom de fichier au format Samsung Camera.

---

## Table des matières

- [Principe](#principe)
- [Prérequis](#prérequis)
- [Installation & lancement](#installation--lancement)
- [Utilisation pas à pas](#utilisation-pas-à-pas)
- [Ce que contient la photo générée](#ce-que-contient-la-photo-générée)
- [Téléphones supportés](#téléphones-supportés)
- [Limitations connues](#limitations-connues)
- [FAQ](#faq)

---

## Principe

Quand tu prends une photo avec ton téléphone, le fichier JPEG contient bien plus que l'image : des **métadonnées EXIF** (marque, modèle, ISO, vitesse d'obturation, distance focale, GPS, date…) qui permettent à n'importe quelle application de galerie de savoir exactement dans quelles conditions la photo a été prise.

Une capture d'écran ou une image téléchargée n'a **aucune** de ces métadonnées. RE-PHOTO injecte des EXIF réalistes correspondant à ton téléphone détecté, ajoute du grain de capteur et une vignette, et génère le fichier avec le bon nom de fichier (ex: `20260301_143022.jpg`).

Tout se passe **100% localement** dans le navigateur. Rien n'est envoyé sur internet.

---

## Prérequis

- Un navigateur moderne (Chrome, Firefox, Samsung Internet, Safari)
- Node.js installé si tu veux utiliser la caméra en live (pour le serveur local)
- Sur Android : Termux ou tout terminal avec `npx` disponible

---

## Installation & lancement

### Option 1 — Sans caméra (le plus simple)

Tu peux ouvrir directement le fichier HTML dans ton navigateur via le gestionnaire de fichiers. La caméra ne fonctionnera pas (les navigateurs bloquent `getUserMedia` sur `file://`) mais tout le reste fonctionne parfaitement : chargement de la photo, injection EXIF, téléchargement.

1. Copie `rephotograph.html` dans ton stockage interne
2. Ouvre-le avec Chrome ou Samsung Internet
3. Sélectionne ta photo → appuie sur le shutter → télécharge

### Option 2 — Avec caméra live (superposition de la vraie caméra)

La caméra nécessite HTTPS ou `localhost`. La façon la plus simple :

**Avec Termux sur Android :**

```bash
# Installer Node.js si pas encore fait
pkg install nodejs

# Lancer un serveur local dans le dossier contenant le fichier
npx serve /storage/emulated/0/Download/
```

Puis ouvrir dans Chrome :

```
http://localhost:3000/rephotograph.html
```

**Sur Mac :**

```bash
npx serve /chemin/vers/dossier/
```

Ouvrir `http://localhost:3000/rephotograph.html`

**Sur Linux :**

```bash
# Option 1 — avec Node.js
npx serve ~/Téléchargements/

# Option 2 — avec Python (souvent déjà installé)
cd ~/Téléchargements && python3 -m http.server 3000

# Option 3 — avec PHP (si installé)
cd ~/Téléchargements && php -S localhost:3000
```

Ouvrir dans Chrome ou Firefox : `http://localhost:3000/rephotograph.html`

> Node.js pas installé sur Linux ? `sudo apt install nodejs npm` (Debian/Ubuntu) · `sudo dnf install nodejs` (Fedora) · `sudo pacman -S nodejs npm` (Arch)

**Sur Windows :**

```powershell
# Option 1 — avec Node.js (PowerShell ou invite de commandes)
npx serve C:\Users\TonNom\Downloads\

# Option 2 — avec Python
cd C:\Users\TonNom\Downloads
python -m http.server 3000

# Option 3 — avec PHP
cd C:\Users\TonNom\Downloads
php -S localhost:3000
```

Ouvrir dans Chrome ou Edge : `http://localhost:3000/rephotograph.html`

> **Astuce Windows** : Maintiens `Shift` + clic droit sur le dossier → "Ouvrir la fenêtre PowerShell ici" pour ouvrir directement au bon endroit.

> Node.js pas installé sur Windows ? Télécharge la version LTS sur [nodejs.org](https://nodejs.org). Python est disponible sur [python.org](https://python.org) ou via `winget install Python.Python.3`.

> La première fois, `npx serve` demandera à télécharger le package `serve`. Réponds `y`.

---

## Utilisation pas à pas

### Étape 1 — Choisir une photo

Sur l'écran d'accueil, appuie sur **⊕ Choisir une photo**.

- Le sélecteur de fichiers s'ouvre sur ta galerie
- Sélectionne n'importe quelle image (JPEG, PNG, WebP…)
- L'image s'affiche dans le viewfinder

En arrière-plan, le site :
- Détecte automatiquement ton téléphone via le User-Agent
- Tente d'obtenir ta géolocalisation GPS (une fenêtre de permission peut apparaître — accepte pour avoir les coords GPS dans les EXIF)

### Étape 2 — (Optionnel) Activer la caméra

Si tu as lancé via un serveur local (Option 2), appuie sur **📷 Caméra**.

- Le navigateur demandera la permission d'accéder à la caméra → **Autoriser**
- Le flux de la caméra arrière apparaît en superposition semi-transparente sur ta photo
- Cela donne l'illusion visuelle que tu es en train de photographier quelque chose devant toi
- Appuie à nouveau sur **⏹ Stop** pour désactiver la caméra

> Si la caméra ne fonctionne pas, un message d'erreur rouge apparaît en bas de l'écran avec l'explication précise (permission refusée, protocole non sécurisé, etc.). Tu peux continuer sans la caméra.

### Étape 3 — Prendre la photo

Appuie sur le **grand bouton rond** (shutter) au centre.

- Un flash blanc illumine l'écran
- Une ligne de scan traverse le viewfinder
- La photo est capturée, le grain de capteur et la vignette sont appliqués
- Les EXIF sont injectés

### Étape 4 — Vérifier et télécharger

L'écran de résultat affiche :

- L'aperçu de la photo générée
- Les métadonnées injectées : modèle, f/, ISO, vitesse, focale 35mm, GPS si disponible
- Deux boutons :
  - **↓ Télécharger** — sauvegarde le fichier JPEG avec le nom `YYYYMMDD_HHmmss.jpg`
  - **← Recommencer** — retourne au viewfinder pour reprendre

### Étape 5 — Déplacer le fichier dans DCIM/Camera

Le fichier est téléchargé dans ton dossier `Téléchargements`. Pour qu'il apparaisse dans ta galerie comme une vraie photo :

**Sur Android**, déplace-le manuellement :

```
/storage/emulated/0/Téléchargements/20260301_143022.jpg
  →
/storage/emulated/0/DCIM/Camera/20260301_143022.jpg
```

Via Termux :

```bash
mv /storage/emulated/0/Download/20260301_*.jpg /storage/emulated/0/DCIM/Camera/
```

Puis force la mise à jour de la galerie :

```bash
am broadcast -a android.intent.action.MEDIA_SCANNER_SCAN_FILE \
  -d file:///storage/emulated/0/DCIM/Camera/20260301_143022.jpg
```

**Sur Windows**, si tu utilises l'outil depuis un PC et veux transférer la photo sur ton téléphone Android :

```powershell
# Via câble USB (MTP activé sur le téléphone)
# Copie dans l'Explorateur Windows vers :
# Ce PC > Galaxy S20 FE > Stockage interne > DCIM > Camera

# Ou via ADB si installé
adb push C:\Users\TonNom\Downloads\20260301_143022.jpg /sdcard/DCIM/Camera/
adb shell am broadcast -a android.intent.action.MEDIA_SCANNER_SCAN_FILE ^
  -d file:///sdcard/DCIM/Camera/20260301_143022.jpg
```

> ADB (Android Debug Bridge) est inclus dans les [Android Platform Tools](https://developer.android.com/tools/releases/platform-tools). Télécharge-les, extrais l'archive, et exécute `adb` depuis le dossier extrait. Le débogage USB doit être activé sur le téléphone (Paramètres → Options développeur → Débogage USB).

**Sur Linux**, même chose si transfert vers un téléphone Android :

```bash
# Via ADB (installer avec : sudo apt install adb)
adb push ~/Téléchargements/20260301_143022.jpg /sdcard/DCIM/Camera/
adb shell am broadcast -a android.intent.action.MEDIA_SCANNER_SCAN_FILE \
  -d file:///sdcard/DCIM/Camera/20260301_143022.jpg

# Ou via MTP avec go-mtpfs / simple-mtpfs
simple-mtpfs --device 1 /mnt/android
cp ~/Téléchargements/20260301_143022.jpg /mnt/android/DCIM/Camera/
fusermount -u /mnt/android
```

---

## Ce que contient la photo générée

### Métadonnées EXIF injectées

| Champ EXIF | Exemple (SM-G781W) | Description |
|---|---|---|
| `Make` | `samsung` | Marque du fabricant |
| `Model` | `SM-G781W` | Modèle exact détecté |
| `Software` | `G781WVLU9GXC1` | Version firmware |
| `DateTime` | `2026:03:01 14:30:22` | Date et heure de la prise |
| `DateTimeOriginal` | identique | Date originale |
| `ExposureTime` | `1/30` | Vitesse d'obturation |
| `FNumber` | `1.8` | Ouverture |
| `ISOSpeedRatings` | `320` | Sensibilité ISO |
| `FocalLength` | `4.70 mm` | Distance focale physique |
| `FocalLengthIn35mmFilm` | `26` | Équivalent 35mm |
| `PixelXDimension` | `3024` | Largeur en pixels |
| `PixelYDimension` | `4032` | Hauteur en pixels |
| `MeteringMode` | `5` (multi-zones) | Mode de mesure |
| `WhiteBalance` | `0` (auto) | Balance des blancs |
| `GPSLatitude` | `48.8566` | Latitude (si accordée) |
| `GPSLongitude` | `2.3522` | Longitude (si accordée) |
| `ColorSpace` | `1` (sRGB) | Espace colorimétrique |
| `XResolution` / `YResolution` | `72` | Résolution DPI |

### Traitements visuels appliqués

- **Bruit de capteur** : bruit aléatoire pixel par pixel simulant le grain d'un vrai capteur photographique (intensité calibrée à 14/255)
- **Vignette** : assombrissement progressif des bords via un dégradé radial, identique à ce que produisent les objectifs de smartphones
- **Résolution native** : l'image est redimensionnée à la résolution exacte du téléphone détecté (ex: 3024×4032 pour le SM-G781W)

### Nom de fichier

Format Samsung Camera : `YYYYMMDD_HHmmss.jpg`

Exemple : `20260301_143022.jpg` pour le 1er mars 2026 à 14h30:22.

---

## Téléphones supportés

La détection se fait automatiquement via le `User-Agent` du navigateur.

| Modèle | Make | ISO | Focale | Résolution |
|---|---|---|---|---|
| Samsung SM-G781W (Galaxy S20 FE) | samsung | 320 | 26mm | 3024×4032 |
| Samsung SM-S928B (Galaxy S24 Ultra) | samsung | 50 | 24mm | 4080×3060 |
| Samsung SM-S918B (Galaxy S23 Ultra) | samsung | 64 | 23mm | 4080×3060 |
| Samsung SM-S911B (Galaxy S23) | samsung | 64 | 23mm | 4080×3060 |
| Samsung SM-A546B (Galaxy A54) | samsung | 100 | 26mm | 4080×3060 |
| Samsung SM-A145F (Galaxy A14) | samsung | 125 | 26mm | 3264×2448 |
| iPhone 15 Pro | Apple | 48 | 24mm | 4032×3024 |
| iPhone 14 Pro | Apple | 64 | 24mm | 4032×3024 |
| iPhone 13 | Apple | 100 | 26mm | 4032×3024 |
| Google Pixel 8 Pro | Google | 56 | 24mm | 4080×3072 |
| Google Pixel 7 | Google | 80 | 25mm | 4080×3072 |
| OnePlus CPH2447 | OnePlus | 64 | 23mm | 4080×3060 |
| Xiaomi 23116PN5BC | Xiaomi | 50 | 23mm | 4080×3060 |

Si ton téléphone n'est pas dans la liste, le profil **SM-G781W** est utilisé par défaut.

---

## Limitations connues

**Taille du fichier** : Le canvas HTML génère un JPEG depuis des données pixels — la taille finale (environ 1,5–3 Mo) peut différer légèrement d'une photo native qui applique des algorithmes de compression propriétaires.

**Profondeur de couleur** : Le canvas travaille en 8 bits par canal. Certains smartphones modernes capturent en 10 bits (HDR). Cette différence ne sera pas reproduite.

**GPS** : La géolocalisation du navigateur est moins précise que le GPS matériel d'un téléphone. Si l'utilisateur refuse la permission, les champs GPS ne sont pas injectés.

**Caméra sur `file://`** : `getUserMedia` est bloqué par tous les navigateurs modernes quand le fichier est ouvert directement depuis le système de fichiers. Il faut obligatoirement passer par un serveur local (`localhost`) ou HTTPS.

**Samsung Internet** : Certaines versions de Samsung Internet sur Android peuvent bloquer le canvas cross-origin. Utilise Chrome si tu rencontres une image noire.

---

## FAQ

**Est-ce que les données quittent mon téléphone ?**
Non. Tout le traitement (lecture de l'image, canvas, injection EXIF, génération du JPEG) se fait dans le navigateur. Aucune requête réseau n'est effectuée sauf le chargement initial des polices Google Fonts et de la librairie `piexifjs` depuis un CDN (jsDelivr).

**L'application galerie affiche quand même "modifié" ou une icône différente ?**
Les applications comme Google Photos ou Samsung Gallery peuvent détecter qu'une image a été modifiée par d'autres moyens (hash du fichier, analyse IA, absence de thumbnail EXIF embarqué). L'outil injecte les métadonnées textuelles mais ne génère pas de thumbnail EXIF (IFD1), ce qui peut être un indicateur pour certaines apps avancées.

**Puis-je modifier le profil de téléphone manuellement ?**
Oui. Ouvre `rephotograph.html` dans un éditeur de texte, trouve la fonction `detectDevice()` et modifie ou ajoute un profil dans le tableau `profiles`. Chaque profil contient : `make`, `model`, `focal`, `fn` (f-number), `iso`, `exp` (exposition), `fw35` (focale 35mm), `w`/`h` (résolution), `soft` (version firmware).

**Comment vérifier que les EXIF sont bien présents ?**
- Sur Android : ouvre la photo dans la galerie Samsung → "Détails" → les infos apparaissent comme sur ta capture d'écran
- En ligne : utilise [jimpl.com/tools/exif-viewer](https://jimpl.com/tools/exif-viewer) ou [exifinfo.org](https://exifinfo.org)
- En ligne de commande : `exiftool 20260301_143022.jpg`

**La photo est floue / mauvaise qualité ?**
La qualité de la photo de sortie dépend directement de la qualité de l'image que tu as importée. L'outil redimensionne l'image à la résolution cible du téléphone, ce qui peut introduire du flou si l'image source est plus petite. Pour de meilleurs résultats, utilise des images de haute résolution.

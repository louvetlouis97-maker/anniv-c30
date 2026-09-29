# Caroline, 30 ans — page de collecte

Page où les invités déposent photos, anecdotes, mots, musiques, conseils… pour le magazine et les jeux de la soirée.

- `index.html` → la page à envoyer aux invités
- `admin.html` → ta page privée pour lire les réponses, exporter en CSV (Excel) et télécharger toutes les photos en ZIP
- Les photos sont compressées dans le navigateur et stockées dans Firestore : **pas besoin de Firebase Storage ni de carte bancaire** (offre gratuite Spark suffisante).

---

## Mise en place (≈ 15 min)

### 1. Créer le projet Firebase
1. Va sur https://console.firebase.google.com → **Ajouter un projet** (ex. `caroline-30`). Google Analytics : inutile.
2. Menu **Build → Firestore Database** → **Créer une base de données** → emplacement `eur3 (europe-west)` → **mode production**.
3. Menu **Build → Authentication** → **Commencer** → onglet **Sign-in method** → active **Google**.
4. Déclarer le site auprès de Firebase (2 chemins possibles, selon l'affichage) :
   - **Chemin A (le plus simple)** : clique sur **Vue d'ensemble du projet** (ou *Project Overview*, tout en haut du menu de gauche). Sur la page d'accueil du projet, repère le bouton **+ Ajouter une application** (ou la rangée d'icônes iOS / Android / **`</>`**) et clique sur l'icône **`</>`** (= Web).
   - **Chemin B** : clique sur la **roue dentée ⚙️** à côté de « Vue d'ensemble du projet » (parfois en bas à gauche) → **Paramètres du projet** → onglet **Général** → descends tout en bas jusqu'au bloc **Vos applications** → icône **`</>`**.
   - Dans les deux cas : donne le surnom `site`, **ne coche pas** « Firebase Hosting », clique sur **Enregistrer l'application**.
   - Firebase affiche alors un bloc de code contenant `const firebaseConfig = { apiKey: "...", ... }` : copie-le.
   - Tu l'as fermé trop vite ? Retourne dans ⚙️ **Paramètres du projet → Général → Vos applications**, clique sur ton application `site`, puis coche **Config** : le bloc réapparaît.

### 2. Renseigner la configuration
> 💡 **Raccourci** : colle simplement à Claude le bloc `firebaseConfig` + ton adresse Google, il remplit les deux fichiers pour toi. Sinon, voici comment faire à la main.

#### a) Ouvrir le fichier `firebase-config.js`
- Dézippe le dossier `caroline-30.zip` (clic droit → **Extraire tout** sur Windows, double-clic sur Mac).
- Fais un **clic droit** sur `firebase-config.js` → **Ouvrir avec** :
  - Windows : **Bloc-notes**
  - Mac : **TextEdit** (si le texte apparaît bizarrement : menu *Format* → *Convertir au format texte*)
- ⚠️ N'utilise pas Word : il abîme le fichier.

#### b) Remplacer les 6 valeurs de configuration
Dans Firebase, tu as copié un bloc qui ressemble à ça (avec tes propres valeurs) :
```js
const firebaseConfig = {
  apiKey: "AIzaSyB1a2b3c4d5e6f7g8h9",
  authDomain: "caroline-30.firebaseapp.com",
  projectId: "caroline-30",
  storageBucket: "caroline-30.firebasestorage.app",
  messagingSenderId: "123456789012",
  appId: "1:123456789012:web:abc123def456"
};
```
Dans `firebase-config.js`, tu trouves le même bloc, mais avec `COLLE_ICI` partout :
```js
export const firebaseConfig = {
  apiKey: "COLLE_ICI",
  authDomain: "COLLE_ICI.firebaseapp.com",
  ...
};
```
**Remplace les 6 lignes** (de `apiKey` à `appId`) par celles copiées depuis Firebase.
Règles à respecter :
- garde bien le mot **`export`** au début de la ligne `export const firebaseConfig = {` ;
- garde les **guillemets** `"..."` autour de chaque valeur et la **virgule** en fin de ligne ;
- s'il y a une ligne `measurementId` dans ton bloc Firebase, tu peux la garder ou la supprimer, ça ne change rien.

#### c) Mettre ton adresse Google (admin)
Toujours dans `firebase-config.js`, dernière ligne :
```js
export const ADMIN_EMAIL = "TON_EMAIL@gmail.com";
```
→ remplace `TON_EMAIL@gmail.com` par l'adresse Gmail avec laquelle tu te connecteras à la page admin (entre les guillemets). Puis **Enregistre** (Ctrl+S / Cmd+S).

#### d) Même chose dans `firestore.rules`
Ouvre `firestore.rules` de la même façon, cherche la ligne :
```
&& request.auth.token.email == 'TON_EMAIL@gmail.com'
```
→ remplace `TON_EMAIL@gmail.com` par **exactement la même adresse** (ici entre apostrophes `'...'`). Enregistre.

✅ Vérification : le mot `COLLE_ICI` et le mot `TON_EMAIL` ne doivent plus apparaître nulle part (Ctrl+F / Cmd+F pour chercher).

### 3. Publier les règles de sécurité
Firestore → onglet **Règles** → remplace tout par le contenu de `firestore.rules` → **Publier**.
(Résultat : les invités peuvent envoyer, mais personne d'autre que toi ne peut lire.)

### 4. Mettre en ligne sur GitHub Pages
1. Crée un dépôt sur GitHub (ex. `caroline-30`). Il peut être **public** : aucune réponse n'est dans le code.
2. **Add file → Upload files** → dépose tous les fichiers de ce dossier → **Commit**.
3. **Settings → Pages** → Source : `Deploy from a branch`, branche `main`, dossier `/ (root)` → **Save**.
4. Après 1–2 min, ta page est en ligne : `https://TON-PSEUDO.github.io/caroline-30/`

### 5. Autoriser le domaine pour la connexion admin
Firebase → **Authentication → Paramètres → Domaines autorisés** → **Ajouter un domaine** : `TON-PSEUDO.github.io`

### 6. Tester
- Remplis le formulaire une fois toi-même depuis ton téléphone.
- Ouvre `https://TON-PSEUDO.github.io/caroline-30/admin.html`, connecte-toi avec Google : ta réponse doit apparaître.
- Tu peux supprimer la réponse test dans Firestore (onglet Données).

Ensuite, envoie le lien `index.html` aux invités (WhatsApp, mail…). Pense à préciser « surprise, pas un mot à Caroline » 😉

---

## Bon à savoir
- **Limites** : 10 photos par envoi (les invités peuvent renvoyer autant de fois qu'ils veulent), ~1 Mo par photo après compression — largement assez pour une impression magazine en A4.
- **Photos iPhone (HEIC)** : acceptées depuis un iPhone (Safari convertit automatiquement). Depuis un PC Windows, un fichier HEIC peut être refusé → exporter en JPEG.
- **Quota gratuit** Firestore : 1 Go de stockage, soit ~1 000+ photos. Aucun risque pour une soirée.
- **Discrétion** : la page n'est pas indexée par Google (`noindex`). Évite quand même d'appeler le dépôt « surprise-caroline » si elle traîne sur ton GitHub 😄
- **Après la soirée** : tu peux supprimer le projet Firebase et le dépôt GitHub. Pense à exporter le CSV et le ZIP avant.

# Gabarit « Clinique » de Spiralia

Ce gabarit est le site du Centre Dentaire Langelier (`dentaire-langelier.html`). Il est construit pour être adapté à une autre clinique en moins d'une heure. Tout tient dans un seul fichier HTML : pas de compilation, pas de serveur.

Ce que le client reçoit dans le forfait :

| Inclus | Où |
|---|---|
| Site complet, parcours 3D, ordinateur et téléphone | toute la page |
| Version anglaise (bouton FR / EN, choix mémorisé, `?lang=en`) | `I18N.en` |
| Prise de rendez-vous en ligne en 3 étapes | `CLINIQUE.reservation` |
| Emplacements prêts pour la séance photo | `CLINIQUE.photos` + liste de prises de vue plus bas |
| Données Google (fiche « Dentist », heures, note) générées automatiquement | rien à faire |

---

## 1. Dupliquer

```bash
cp dentaire-langelier.html nouvelle-clinique.html
```

Ouvrir le nouveau fichier. Le bloc à modifier est en haut, dans `<head>`, entre les deux bandeaux `GABARIT`.

## 2. Remplir `window.CLINIQUE`

| Champ | Exemple | Remarque |
|---|---|---|
| `nom` | `"Clinique Dentaire Jarry"` | nom complet, partout dans la page |
| `marque` | `"Jarry"` | un mot court : logo de l'en-tête et grand mot en verre derrière la dent |
| `telephone` / `telE164` | `"514 555-1234"` / `"+15145551234"` | tous les liens « Appeler » sont réécrits automatiquement |
| `courriel` | `""` | facultatif ; s'il est rempli, la fin du formulaire propose « Envoyer par courriel » |
| `adresse` | `rue`, `ville`, `province`, `region`, `cp`, `pays` | sert à l'adresse, au pied de page, à l'itinéraire Google et aux données Google |
| `quartier` | `"Villeray"` | dernière ligne du titre en lèvres (« à Villeray. ») |
| `lieu` | `"Villeray, Montréal"` | écran de chargement |
| `geo` | `lat`, `lng` | clic droit sur Google Maps → copier les coordonnées |
| `heures` | `{0:null, 1:[9,17], …, 4:[9,20.5]}` | 0 = dimanche. `20.5` = 20 h 30. `null` = fermé |
| `note` | `{valeur:4.8, avis:92}` | **la vraie note Google**, jamais arrondie vers le haut |
| `photos` | voir section 4 | |
| `reservation` | voir section 5 | |

Tout ce qui dépend des heures se met à jour seul : les barres de la semaine (sarcelle le jour, rose le soir), la phrase « Deux soirs par semaine… », « Ouvert maintenant / Fermé », le résumé sous le bouton de rendez-vous, les plages du formulaire et les données Google.

## 3. Les textes

- **Français** : écrit directement dans la page (titres des sections, soins, avis). Chercher le texte et le remplacer.
- **Phrases françaises avec coordonnées** (adresse, pied de page, urgence) : dans `I18N.fr`. Les `{nom}`, `{tel}`, `{rue}`… se remplissent seuls.
- **Anglais** : tout est dans `I18N.en`. Chaque élément traduit porte un `data-i18n="clé"` dans la page ; la clé correspond à une ligne de `I18N.en`.

À changer pour chaque client :

1. Les 5 soins (`soin.0` à `soin.4`) et leurs textes (`texte.0` à `texte.3`, `urgence.texte`). Garder 5 soins : l'animation en a une par soin.
2. La citation et les deux paraphrases d'avis (`phrase`, `avis.a`, `avis.b`). **Uniquement des avis réels**, fidèlement traduits. Jamais de citation inventée.
3. Le nom du dentiste dans `avis.a` s'il y en a un.
4. Les phrases du récit (`ch1.*`, `ch2.*`) si le positionnement change (ex. orthodontie plutôt que canal).

## 4. Séance photo

Les photos actuelles viennent d'Unsplash et portent la mention « Photo d'illustration ». Après la séance :

1. Mettre les fichiers à côté de la page (`photos/examen.jpg`, etc.) ou sur un hébergement d'images.
2. Remplacer les adresses dans `CLINIQUE.photos`.
3. Mettre `illustration: false` : la mention disparaît et le crédit Unsplash est retiré du pied de page.

### Liste de prises de vue (une demi-journée, à apporter au photographe)

| # | Emplacement | Cadrage | Format livré | Conseils |
|---|---|---|---|---|
| 1 | `soins[0]` Examens et nettoyage | gros plan, mains gantées + miroir, patient flou | 16:10, 2200 px de large | lumière du scialytique, fond sarcelle ou blanc |
| 2 | `soins[1]` Traitements de canal | dentiste qui regarde une radiographie à l'écran | 16:10, 2200 px | l'écran doit être lisible mais sans nom de patient |
| 3 | `soins[2]` Enfants | enfant dans le fauteuil, sourire, parent à côté | 16:10, 2200 px | **autorisation parentale signée** |
| 4 | `soins[3]` Prix et assurances | réception : quelqu'un explique un document ou une tablette | 16:10, 2200 px | pas de numéro d'assurance visible |
| 5 | `avis` | 2 ou 3 générations d'une même famille (vrais patients ou équipe) | portrait 4:5, 1600 px | **formulaire de consentement à l'image** pour chaque personne |
| 6 | `salle` | la salle de traitement vide, propre, lumière naturelle | paysage 3:2, 2400 px | ranger les fils, fermer les tiroirs |
| 7 | bonus réseaux | façade + enseigne, de jour | 1:1 et 9:16 | pour la fiche Google et Instagram |
| 8 | bonus équipe | portrait de chaque dentiste sur fond uni | 4:5 | pour une future page « Équipe » |

Règles :

- Exporter en JPEG qualité 80, ou en WebP.
- Garder le fichier sous 400 Ko.
- Pas de visage de patient sans consentement écrit (Loi 25).
- Le sourire de l'accueil (photo intégrée en base64) reste le même pour tous les clients. Les lèvres de texte sont calées sur cette image : ne pas la remplacer sans recaler les points `P(nx,ny)` dans `construireLevres`.

## 5. Prise de rendez-vous en ligne

Le formulaire tient en 3 étapes :

1. **Le soin** : les 5 soins et « Autre », plus une case « nouveau patient ».
2. **Le moment** : les 14 prochains jours ouverts, des plages de 30 minutes et une option « Peu importe l'heure ». C'est une préférence ; la clinique rappelle pour confirmer.
3. **Les coordonnées** : nom, téléphone, courriel facultatif, une précision facultative, et la case de consentement (Loi 25).

Il est pensé pour les personnes âgées : cibles d'au moins 56 px, gros texte, erreurs écrites en clair. Il contient aussi un piège anti-robot invisible.

### Sans configuration (`endpoint: ""`)

Le patient voit un résumé de sa demande avec un gros bouton « Appeler le … ». Si `courriel` est rempli, un bouton envoie aussi le résumé par courriel. Rien ne se perd et aucun service n'est requis. Ce mode convient à la démo et aux cliniques qui préfèrent le téléphone.

### Avec réception des demandes (forfait +500 à 1 500 $)

Mettre une URL dans `reservation.endpoint`. Le site envoie la demande en `POST` JSON :

```json
{ "clinique":"…", "langue":"fr", "soin":"Traitements de canal", "nouveauPatient":true,
  "date":"2026-10-14", "heure":"11 h 30", "flexible":false, "moment":"mercredi 14 octobre, 11 h 30",
  "nom":"…", "telephone":"…", "courriel":"…", "message":"…", "consentement":true,
  "envoyeLe":"2026-10-13T15:02:11.000Z", "page":"https://…" }
```

Options testées :

- **Formspree** (le plus simple) : créer un formulaire et coller l'URL `https://formspree.io/f/xxxx`. La clinique reçoit un courriel à chaque demande. Gratuit jusqu'à 50 demandes par mois.
- **Make ou Zapier** : un webhook qui envoie un texto à la réception, ajoute une ligne dans Google Sheets ou crée l'événement dans l'agenda.
- **Google Apps Script** : un `doPost(e)` qui écrit dans une feuille de calcul de la clinique. Gratuit.

Si l'envoi échoue (réseau, service en panne), le patient voit automatiquement le résumé avec le bouton « Appeler ».

Réglages : `jours` (nombre de jours proposés), `pas` (minutes entre les plages) et `delai` (heures minimum avant la première plage du jour même).

## 6. Avant la mise en ligne

- [ ] Retirer `<meta name="robots" content="noindex">` (à garder tant que le contrat n'est pas signé).
- [ ] Mettre à jour `<meta name="description">`, `og:title` et `og:description` (en français ; l'anglais est généré).
- [ ] Remplacer les `href="?lang=…"` des balises `hreflang` par l'adresse complète du site.
- [ ] Vérifier la note Google et le nombre d'avis.
- [ ] Tester le formulaire de bout en bout avec le vrai `endpoint`.
- [ ] Ajouter une page ou une section « Politique de confidentialité » avec le nom du responsable des renseignements personnels (Loi 25).
- [ ] Tester sur un vrai téléphone : défilement au pouce, bouton flottant, appel.

## 7. Tarifs de référence

| Élément | Prix |
|---|---|
| Site (ce gabarit adapté) | 5 000 à 7 500 $ |
| Version anglaise | +1 000 à 1 500 $ |
| Séance photo (direction + intégration) | +400 à 800 $ |
| Rendez-vous en ligne branché | +500 à 1 500 $ |
| Hébergement, mises à jour, suivi | 100 à 150 $ / mois |

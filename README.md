# Blueprint Filtration Piscine

Blueprint Home Assistant pour piloter automatiquement la filtration d'une piscine :
pompe, pompe à chaleur et électrolyseur, avec calcul dynamique de la durée de
filtration selon la température de l'eau.

## Modes de fonctionnement

- **Été** — filtration calculée (abaque ou T°/2 × coefficient), répartie en
  1, 2 ou 3 sessions à des heures configurables. Électrolyseur ON pendant
  les sessions si T° > 16°C.
- **Chauffage** — pompe + pompe à chaleur en continu jusqu'à atteindre la
  consigne, puis bascule automatique vers le mode Été.
- **Hiver** — heure de début fixe + durée fixe.
- **On** — marche forcée avec durée limite optionnelle (retour auto vers Été).
- **Off** — arrêt complet.

## Calcul de la durée de filtration

Deux méthodes au choix (paramètre `mode_calcul`) :

- **Abaque** : `0.00335·T³ − 0.14953·T² + 2.43489·T − 10.72859`
- **Classique** : `T / 2`

Le résultat est multiplié par un coefficient (0.25 à 1.40) puis divisé
équitablement entre les sessions configurées.

La température utilisée est stabilisée (`temp_mem`) : elle n'est mise à jour
que lorsque la pompe tourne depuis au moins `tempo_eau` secondes.

## Capteurs

- **Capteur principal** — requis
- **Capteur de secours** — optionnel. Utilisé automatiquement si le capteur
  principal renvoie `unavailable`, `unknown` ou une valeur non numérique.
  En dernier recours, le blueprint utilise la dernière valeur mémorisée.

## Notifications

- Résumé quotidien à l'heure configurée
- Notification horaire en mode Chauffage (progression vers la consigne)
- Notification quand la consigne est atteinte
- Notification à l'arrêt de la pompe à chaleur (durée de la session)

Toutes activables/désactivables via un `input_boolean`.

## Entités Home Assistant requises

| Rôle | Domaine |
|------|---------|
| Mode de fonctionnement | `input_select` (options : `Ete`, `Chauffage`, `Hiver`, `On`, `Off`) |
| Mode calcul | `input_boolean` |
| Coefficient de filtration | `input_number` (25–140) |
| Nombre de sessions Été | `input_number` (1–3) |
| Heure sessions 1/2/3 | `input_datetime` |
| Durée filtration (affichage) | `input_number` |
| Consigne température | `input_number` |
| Heure début / durée mode Hiver | `input_datetime` / `input_number` |
| Tempo circulation eau | `input_number` (secondes) |
| Tempo arrêt PAC → pompe | `input_number` (secondes) |
| Mémoire température | `input_number` |
| Durée marche forcée | `input_number` (heures) |
| Pompe / PAC / électrolyseur | `switch` |
| Période filtration (affichage) | `input_text` |
| Activer notifications | `input_boolean` |
| Heure notification quotidienne | `input_datetime` |
| Capteur température (principal + secours) | `sensor` `device_class: temperature` |

## Installation

1. Copier `filtration_piscine_blueprint.yaml` dans
   `<config>/blueprints/automation/<votre_dossier>/`
2. Redémarrer Home Assistant (ou recharger les automations)
3. Créer les entités listées ci-dessus
4. Créer une nouvelle automation à partir du blueprint et affecter chaque
   paramètre

## Origine

Porté depuis un script AppDaemon `FiltrationPiscine` (v14/09/2022), enrichi
du mode Chauffage, du support multi-sessions et du capteur de secours.

## Compatibilité

Home Assistant 2023.4.0 et versions ultérieures.

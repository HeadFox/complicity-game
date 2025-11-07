# Instructions pour les modifications

## Modification 1 : Bouton pour qu'une autre équipe marque

Dans l'écran de jeu (gameScreen), ajouter AVANT les boutons "Trouvé/Passer" :

```html
<!-- Section pour une autre équipe qui trouve -->
<div class="other-team-section">
    <h4>🎪 Une autre équipe a trouvé ?</h4>
    <select id="otherTeamSelect">
        <option value="">-- Sélectionner une équipe --</option>
    </select>
    <button id="btnOtherTeamFound" class="info" style="margin-top: 10px;">
        ✅ Cette équipe a trouvé
    </button>
</div>
```

Ajouter ces styles CSS :

```css
.other-team-section {
    margin: 20px 0;
    padding: 15px;
    background: #f0f7ff;
    border-radius: 10px;
    border: 2px solid #17a2b8;
}

.other-team-section h4 {
    color: #17a2b8;
    margin-bottom: 10px;
    font-size: 1em;
}

button.info {
    background: #17a2b8;
    box-shadow: 0 4px 15px rgba(23, 162, 184, 0.4);
}
```

Ajouter ce JavaScript dans renderGameScreen() :

```javascript
// Remplir le select avec les autres équipes
const select = document.getElementById('otherTeamSelect');
select.innerHTML = '<option value="">-- Sélectionner une équipe --</option>';

gameState.teams.forEach((team, index) => {
    if (index !== gameState.currentTeamIndex) {
        const option = document.createElement('option');
        option.value = index;
        option.textContent = team.name;
        select.appendChild(option);
    }
});
```

Ajouter ce gestionnaire d'événement :

```javascript
document.getElementById('btnOtherTeamFound').addEventListener('click', () => {
    const select = document.getElementById('otherTeamSelect');
    const selectedIndex = select.value;
    
    if (selectedIndex === '') {
        alert('Veuillez sélectionner une équipe');
        return;
    }
    
    // L'autre équipe gagne le point
    gameState.teams[parseInt(selectedIndex)].score += 1;
    // L'équipe courante perd 0.5 point
    gameState.teams[gameState.currentTeamIndex].score -= 0.5;
    nextTurn();
});
```

## Modification 2 : Choix des thèmes par équipe

### Étape 1 : Modifier la structure des équipes

Dans gameState, chaque équipe doit avoir :
```javascript
{
    name: "Nom de l'équipe",
    score: 0,
    themes: [] // Array des thèmes choisis
}
```

### Étape 2 : Remplacer l'écran de sélection des thèmes

Remplacer `themeSelectScreen` par :

```html
<!-- Sélection des thèmes PAR ÉQUIPE -->
<div id="teamThemeSelectScreen" class="screen">
    <h2>Sélection des thèmes</h2>
    <div class="prep-info">
        <h3 style="margin-bottom: 10px;">Équipe :</h3>
        <div id="themeSelectTeamName" style="font-size: 1.3em; font-weight: bold; color: #667eea;"></div>
    </div>
    <p class="info-text">Choisissez au moins un thème pour cette équipe</p>

    <div class="theme-grid" id="teamThemeGrid"></div>

    <button id="btnNextTeamTheme" class="success" style="display:none;">
        Continuer
    </button>
</div>
```

### Étape 3 : Remplacer le JavaScript de sélection des thèmes

Ajouter `currentTeamThemeIndex: 0` dans gameState.

Remplacer toute la section de sélection des thèmes par le code qui itère sur chaque équipe.

### Étape 4 : Modifier getAvailableWords()

```javascript
function getAvailableWords(teamIndex) {
    const team = gameState.teams[teamIndex];
    if (!team || !team.themes || team.themes.length === 0) {
        return [];
    }

    let allWords = [];
    team.themes.forEach(themeKey => {
        allWords = allWords.concat(WORDS_DATA[themeKey].words);
    });
    return allWords.filter(word => !gameState.usedWords.includes(word));
}
```

Et mettre à jour getRandomWords() pour prendre un teamIndex en paramètre.

---

Fichier index-v5.html contient DÉJÀ certaines de ces modifications.
Il faut compléter le JavaScript pour que tout fonctionne.

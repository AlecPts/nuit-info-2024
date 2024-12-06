<script lang="ts">
    import { onMount } from "svelte";
  
    let crumbs = 0; // Nombre de miettes collectées
    let multiplier = 1; // Multiplicateur actuel
    let clicks = 0; // Nombre de clics
    let potatoButtonPosition = { top: '17%', left: '44%' }; // Position du bouton de la pomme de terre
    let multiplierButtonPosition = { top: '75%', left: '43.5%' };
    let AutoclickerButtonPosition = { top : '85%', left: '40%'} // Position du bouton d'amélioration
    let multiplierUpgradeCost = 20; // Coût initial pour améliorer le multiplicateur
    let CanAlert = false;
    let trollAutoclickerActive = false; // État du bouton troll
    let trollAutoclickerInterval: number | null = null; // Référence à l'intervalle d'autoclicker
    let trollAutoclickerCost = 100; // Coût initial pour activer l'autoclicker
  
    const clickPotato = () => {
      crumbs += multiplier;
      clicks++;
      CanAlert = true;
      randomResetCrumbs();
      randomHalveCrumbs();
      RandomUpdate();
      if (Math.random() < 0.5) { 
        randomPotatoButtonPosition(); 
      }
    };
  
    // Fonction pour améliorer le multiplicateur
    const upgradeMultiplier = () => {
      if (crumbs >= multiplierUpgradeCost) {
        crumbs -= multiplierUpgradeCost;
        multiplier++;
        multiplierUpgradeCost += Math.floor(Math.random() * 30 + 10);
        alert("Congrats! You upgraded your Multiplier, but the button got the zoomies. \n Is it hiding something? Too bad!");
      } else {
        alert("You need more crumbs. Keep clicking!");
      }
      randomMultiplierButtonPosition();
    };
  
    // Réinitialisation aléatoire des miettes
    const randomResetCrumbs = () => {
      if (Math.random() < 0.01) { 
        crumbs = 0;
        alert("Oops! All crumbs reset. Try again.");
      }
    };
  
    // Division aléatoire des miettes par deux
    const randomHalveCrumbs = () => {
      if (Math.random() < 0.01) { 
        crumbs = Math.floor(crumbs / 2);
        alert("Your crumbs are halved!");
      }
    };
  
    // Type pour la position
    type Position = {
      top: string;
      left: string;
    };
  
    // Vérification des collisions entre deux boutons
    const checkCollision = (newPosition: Position, otherButtonPosition: Position): boolean => {
      const margin = 100;
      return (
        Math.abs(parseInt(newPosition.top) - parseInt(otherButtonPosition.top)) < margin &&
        Math.abs(parseInt(newPosition.left) - parseInt(otherButtonPosition.left)) < margin
      );
    };
  
    // Position aléatoire du bouton de la pomme de terre
    const randomPotatoButtonPosition = () => {
      const windowWidth = window.innerWidth;
      const windowHeight = window.innerHeight;
      const movementRangeTop = 300;
      const movementRangeLeft = 300;
      let newPosition: Position;
      do {
        const isLeftSide = Math.random() < 0.5;
        const baseLeft = isLeftSide 
          ? 0 
          : windowWidth - movementRangeLeft; 
        
        newPosition = {
          top: `${Math.random() * movementRangeTop}px`,
          left: `${baseLeft + Math.random() * movementRangeLeft}px`
        };
      } while (checkCollision(newPosition, multiplierButtonPosition));

      newPosition.top = `${Math.min(parseFloat(newPosition.top), windowHeight - 200)}px`;
      newPosition.left = `${Math.min(parseFloat(newPosition.left), windowWidth - 200)}px`;

      potatoButtonPosition = newPosition;
    };
  
    // Position aléatoire du bouton d'amélioration
    const randomMultiplierButtonPosition = () => {
      const windowWidth = window.innerWidth;
      const windowHeight = window.innerHeight;
      const movementRangeTop = 300;
      const movementRangeLeft = 500;

      let newPosition: Position;
      do {
        const isLeftSide = Math.random() < 0.5;
        const baseLeft = isLeftSide 
          ? 0 
          : windowWidth - movementRangeLeft;

        const baseTop = windowHeight / 2;

        newPosition = {
          top: `${baseTop + Math.random() * movementRangeTop}px`,
          left: `${baseLeft + Math.random() * movementRangeLeft}px`
        };
      } while (checkCollision(newPosition, potatoButtonPosition));

      newPosition.top = `${Math.min(parseFloat(newPosition.top), windowHeight - 200)}px`;
      newPosition.left = `${Math.min(parseFloat(newPosition.left), windowWidth - 200)}px`;

      multiplierButtonPosition = newPosition;
    };
    const startTrollAutoclicker = () => {
  // Déclenche un clic toutes les 10 secondes
  trollAutoclickerInterval = setInterval(() => {
    clickPotato(); // Simule un clic sur la pomme de terre
    randomPotatoButtonPosition(); // Déplace le bouton pomme de terre
  }, 5000); // Toutes les 10 secondes
};

    const activateTrollAutoclicker = () => {
  if (crumbs >= trollAutoclickerCost && !trollAutoclickerActive) {
    crumbs -= trollAutoclickerCost;
    trollAutoclickerActive = true;
    startTrollAutoclicker();
    alert("Troll Autoclicker activated! The potato will be clicked every 5 seconds.\n but watch out, it may move the potato too");
  } else if (trollAutoclickerActive) {
    alert("The troll autoclicker is already active.");
  } else {
    alert("You need more crumbs to activate the troll autoclicker!");
  }
};
const changePotatoImage = () => {
    const newImage = './images/lyreco-logo-rond.png'; // Remplacez par le chemin de votre nouvelle image
    const potatoElement = document.querySelector('.potato') as HTMLDivElement;

    if (potatoElement) {
        potatoElement.style.backgroundImage = `url(${newImage})`;
        alert("You found the secret! The potato has evolved.");
    }
};
const RandomUpdate = () => {
      if (Math.random() < 0.1 && CanAlert==true) { 
        alert(`You've clicked ${clicks} times! Did you know that? Well now you know. Congratulations!\n 😘 Smooch`);
      }
    };
  
    // Message spécial toutes les 50 clics
    onMount(() => {
    });
</script>
  
<style>
  body {
    background: url('/images/spinning-galaxy.gif') no-repeat center center fixed;
    background-color: #ff0000; /* Couleur de fond de secours */
    background-size: cover;
  }
  
  @keyframes flashBackground {
    0% { background-color: lime; }
    50% { background-color: hotpink; }
    100% { background-color: orange; }
  }

  .main-container {
    display: flex;
    flex-direction: column;
    align-items: center;
    padding: 2rem;
    height: 100vh;
  }
  
  .potato-container {
    display: flex;
    justify-content: center;
    align-items: center;
    width: 100%;
    height: 300px;
  }
  
  .potato {
    width: 200px;
    height: 200px;
    background: url('./images/1200px-Potato_with_sprouts.jpg');
    background-size: cover;
    background-position: center;
    border-radius: 50%;
    border: 5px solid red;
    cursor: pointer;
    margin-left: 20px;
    animation: shake 1s infinite alternate;
    position: absolute;
    transition: transform 0.2s;
  }
  
  @keyframes shake {
    0% { transform: rotate(0deg); }
    50% { transform: rotate(15deg); }
    100% { transform: rotate(-15deg); }
  }

  .stats {
    font-size: 1.5rem;
    margin: 1rem;
    color: yellow;
    text-shadow: 2px 2px 0 black;
  }

  .button {
    padding: 1rem 2rem;
    margin: 1rem;
    background: linear-gradient(to right, red, yellow);
    color: blue;
    font-weight: bold;
    border: 3px dotted purple;
    cursor: pointer;
    position: absolute;
    transition: transform 0.2s;
  }

  .button:hover {
    transition: transform 0.2s;
  }

  .useless {
    font-size: 0.8rem;
    color: hotpink;
    text-decoration: underline;
    animation: blink 1s infinite;
  }
</style>

<div class="main-container">
    <h1>🌟 Crappy Clicker: Potato Edition 🌟</h1>
    <h2>Click on the potato to harvest Crumbs. And remember :</h2>
    <h3 on:click={changePotatoImage} class="secret-link">YOUR LIFE IS POTATO!</h3>
    <div class="potato-container">
      <button class="potato" on:click={clickPotato} aria-label="Click to collect crumbs" style="top: {potatoButtonPosition.top}; left: {potatoButtonPosition.left};"></button>
    </div>
    <div class="stats">Crumbs: {crumbs}</div>
    <div class="stats">Multiplier: x{multiplier}</div>
    <div class="stats">Next Upgrade Cost: {multiplierUpgradeCost} crumbs</div>
    <button class="button" on:click={upgradeMultiplier} style="top: {multiplierButtonPosition.top}; left: {multiplierButtonPosition.left};">
      Upgrade Multiplier
    </button>
  
    <!-- Nouveau bouton pour activer le troll autoclicker -->
    <button class="button" on:click={activateTrollAutoclicker} style="top: {AutoclickerButtonPosition.top}; left: {AutoclickerButtonPosition.left};">
      Activate Autoclicker (Cost: {trollAutoclickerCost} crumbs)
    </button>
  
    <p class="useless">
      Warning: This game is intentionally bad. You're still playing, though.
    </p>
  </div>

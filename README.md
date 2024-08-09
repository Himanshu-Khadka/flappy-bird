# Flappy Bird Game

This project is a simple Flappy Bird game built using the Phaser 3 framework. The game involves a bird navigating through columns without hitting them or the ground.

## Table of Contents

- Installation
- Usage
- [How It Works](#how-it-works)
- License

## Installation

1. **Clone the repository**:
    ```sh
    git clone https://github.com/yourusername/flappy-bird.git
    cd flappy-bird
    ```

2. **Open the game**:


## Usage

- **Start the game**: The game starts automatically when you open the page.
- **Control the bird**: Use the `Up` arrow key to make the bird fly upwards.
- **Restart the game**: Press the `R` key or click the "Restart" button to restart the game.

## How It Works

### Configuration

The game configuration is defined in the [`config`] object, which includes the renderer type, game dimensions, physics settings, and the game scenes (preload, create, update).

```javascript
let config = {
    renderer: Phaser.AUTO,
    width: 800,
    height: 600,
    physics: {
      default: "arcade",
      arcade: {
        gravity: { y: 300 },
        debug: false,
      },
    },
    scene: {
      preload: preload,
      create: create,
      update: update,
    },
};
```

### Game Initialization

A new Phaser game instance is created using the configuration.

```javascript
let game = new Phaser.Game(config);
```

### Preload

The [`preload`]function loads the game assets before the game starts.

```javascript
function preload() {
    this.load.image("background", "assets/background.png");
    this.load.image("road", "assets/road.png");
    this.load.image("column", "assets/column.png");
}
```

### Create

The [`create`]function initializes the game objects, such as the bird, columns, and road. It also sets up the physics and collision detection.

```javascript
function create() {
    // Create columns
    const topColumns = this.physics.add.staticGroup({
        key: 'column',
        repeat: 1,
        setXY: { x: 200, y: 0, stepX: 300 }
    });
    const bottomColumns = this.physics.add.staticGroup({
        key: 'column',
        repeat: 1,
        setXY: { x: 350, y: 400, stepX: 300 }
    });

    // Create road
    const road = this.physics.add.staticGroup();
    road.create(400, 568, 'road').setScale(2).refreshBody();

    // Create bird
    bird = this.physics.add.sprite(100, 150, 'bird').setScale(2);
    bird.setBounce(0.2);
    bird.setCollideWorldBounds(true);

    // Create score text
    scoreText = this.add.text(16, 16, 'Score: 0', { fontSize: '32px', fill: '#fff' });

    // Create message text
    messageToPlayer = this.add.text(16, 50, '', { fontSize: '32px', fill: '#fff' });

    // Add collision detection
    this.physics.add.collider(bird, topColumns, hitColumn, null, this);
    this.physics.add.collider(bird, bottomColumns, hitColumn, null, this);
    this.physics.add.collider(bird, road, land, null, this);

    // Add cursor keys
    cursors = this.input.keyboard.createCursorKeys();

    // Add restart key
    this.input.keyboard.on('keydown-R', restartGame, this);
}
```

### Update

The [`update`]function handles the game logic, such as bird movement and collision detection.

```javascript
function update() {
    if (!isGameStarted) {
        bird.setVelocityY(0); // Keep the bird still before the game starts
    } else if (cursors.up.isDown && !hasLanded && !hasBumped) {
        bird.setVelocityY(-160);
        if (cursors.up.isDown && !(bird.x > 750)) {
            score += 1;
            scoreText.setText(`Score: ${score}`);
        }
    }

    if (!hasLanded && !hasBumped) {
        bird.body.velocity.x = 50;
    }

    if (hasLanded || hasBumped || !isGameStarted) {
        bird.body.velocity.x = 0;
    }
}
```

### Collision Handlers

The `hitColumn` and `land` functions handle what happens when the bird hits a column or lands on the road.

```javascript
function hitColumn(bird, column) {
    hasBumped = true;
    messageToPlayer.setText('Oh no! You crashed. Press R to restart.');
}

function land(bird, road) {
    hasLanded = true;
    messageToPlayer.setText('Oh no! You crashed. Press R to restart.');
}
```

### Restart Game

The `restartGame` function resets the game state and restarts the scene.

```javascript
function restartGame() {
    // Reset game state variables
    hasLanded = false;
    hasBumped = false;
    isGameStarted = true;
    score = 0;

    // Restart the scene
    game.scene.restart();
}

// Make the restartGame function accessible globally
window.restartGame = restartGame;
```

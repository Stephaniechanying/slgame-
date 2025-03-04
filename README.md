# slgame-
Wellness game
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Drug Prevention Game</title>
  <style>
    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
    }

    body {
     font-family: Arial, sans-serif;
      margin: 0;
      padding: 0;
      overflow: hidden; /* Disable scrolling */
      background-image: url('https://img.freepik.com/premium-photo/atmosphere-front-high-school-university-building-summer-school-opening-watercolor-bac_669646-1361.jpg');
      background-size: cover;
      background-position: center;
      background-repeat: no-repeat;
      background-attachment: fixed;
    }

    #game-container {
      position: relative;
      width: 100vw;
      height: 100vh;
      background-image: url('https://img.freepik.com/premium-psd/watercolor-school-yard-background-with-pastel-light-color-painting_985165-29.jpg');
      background-size: cover;
      background-position: center;
      background-repeat: no-repeat;
      background-attachment: fixed;
      background: rgba(0, 0, 0, 0.1);
      overflow: hidden;
    }

    #start-screen {
      position: absolute;
      width: 100%;
      height: 100%;
      display: flex;
      justify-content: center;
      align-items: center;
      flex-direction: column;
      background: rgba(0, 0, 0, 0.5);
      color: white;
      z-index: 10;
    }
    
    @keyframes fadeIn {
      from {
        opacity: 0;
      }
      to {
        opacity: 1;
      }
    }

    @keyframes fadeOut {
      from {
        opacity: 1;
      }
      to {
        opacity: 0;
      }
    }

    .fade-in {
      animation: fadeIn 2s ease-in forwards; /* Fade in over 2 seconds */
    }

    .fade-out {
      animation: fadeOut 2s ease-out forwards; /* Fade out over 2 seconds */
    }

    #overlay img {
      max-width: 200px;
      height: auto;
      margin-bottom: 20px;
    }

    #overlay p {
      font-size: 1.5rem;
      color: white;
      text-align: center;
    }
    
    #overlay {
      position: absolute;
      width: 100%;
      height: 100%;
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      background: rgba(0, 0, 0, 0.7);
      color: white;
      font-size: 1.5rem;
      z-index: 10;
      display: none;
      text-align: center;
      padding: 20px;
    }


    .button {
      padding: 10px 20px;
      font-size: 1.2rem;
      color: white;
      background: #0288d1;
      border: none;
      border-radius: 5px;
      cursor: pointer;
      margin-top: 20px;
    }

    #jump-button {
      position: absolute;
      bottom: 10%;
      left: 50%;
      transform: translateX(-50%);
      padding: 15px 30px;
      font-size: 1rem;
      color: white;
      background: #ff5722;
      border: none;
      border-radius: 10px;
      cursor: pointer;
      z-index: 5;
      display: none;
    }

    #luke {
      position: absolute;
      bottom: 20px;
      left: 50px;
      width: 130px;
      height: 130px;
      background-image: url('https://media.giphy.com/media/hFVrwYJaIz0uk/giphy.gif');
      background-size: cover;
      background-position: center;
      background-repeat: no-repeat;
    }

    .obstacle {
      position: absolute;
      bottom: 20px;
      width: 100px;
      height: 100px;
      border-radius: 5px;
    }

    .red { background: none; }
    .yellow { background: none; }
    .blue { background: none; }
    .purple { background: none; }
    
    
  </style>
  
  <audio id="background-music" src="http://www.sousound.com/music/healing/healing_01.mp3" loop>	</audio>
  
</head>
<body>
  <div id="game-container">
    <div id="start-screen">
      <h1>Drug Prevention Game</h1>
      <p>Help Luke avoid bad influences by jumping over obstacles!</p>
      <button id="start-button" class="button">Start Game</button>
    </div>

    <div id="overlay">
      <!-- Content for overlay will be added dynamically in JavaScript -->
    </div>

    <div id="luke"></div>
    <button id="jump-button">Jump</button>
  </div>
  
  <script>
  const luke = document.getElementById('luke');
  const startScreen = document.getElementById('start-screen');
  const overlay = document.getElementById('overlay');
  const gameContainer = document.getElementById('game-container');
  const jumpButton = document.getElementById('jump-button');
  const obstacleColors = ['red', 'yellow', 'blue', 'purple'];


  // Create the trail image element
  const trail = document.createElement('div');
  trail.style.position = 'absolute';
  trail.style.width = '50px';
  trail.style.height = '50px';
  trail.style.backgroundImage = 'url("https://www.icegif.com/wp-content/uploads/2023/08/icegif-622.gif")'; // Trail image
  trail.style.backgroundSize = 'cover';
  trail.style.opacity = '0'; // Initially hidden
  trail.style.pointerEvents = 'none'; // Ignore interactions
  gameContainer.appendChild(trail);

  // Create the rainbow trail element
  const rainbowTrail = document.createElement('div');
  rainbowTrail.style.position = 'absolute';
  rainbowTrail.style.width = '50px';
  rainbowTrail.style.height = '50px';
  rainbowTrail.style.backgroundImage = 'url("https://img1.picmix.com/output/stamp/normal/4/5/3/3/2213354_38d7b.gif")'; // Rainbow trail image
  rainbowTrail.style.backgroundSize = 'cover';
  rainbowTrail.style.opacity = '0'; // Initially hidden
  rainbowTrail.style.pointerEvents = 'none'; // Ignore interactions
  gameContainer.appendChild(rainbowTrail);

  let gameRunning = false;
  let isJumping = false;
  let obstacles = [];
  let obstacleSpeed = 8;
  let currentSet = 0;
  let passedYellowObstacles = false; // Flag for clearing yellow obstacles

  // Start button click handler
  document.getElementById('start-button').addEventListener('click', () => {
    const backgroundMusic = document.getElementById('background-music'); // Get the audio element
    backgroundMusic.volume = 0.5; // Set the volume (0.0 to 1.0)
    backgroundMusic.play(); // Play the music
    startScreen.style.display = 'none'; // Hide start screen
    showOverlay(1, () => {
      gameRunning = true;
      jumpButton.style.display = 'block'; // Show jump button
      runGameSequence(); // Start the game
    });
  });

  // Jump mechanics for space bar
  document.addEventListener('keydown', (e) => {
    if (e.code === 'Space' && gameRunning) jump();
  });

  // Jump mechanics for jump button
  jumpButton.addEventListener('click', () => {
    if (gameRunning) jump();
  });
   
  function jump() {
    if (isJumping) return;

    isJumping = true;
    luke.style.transition = 'bottom 0.4s'; // Fast upward motion
    luke.style.bottom = '310px'; // Jump height

    setTimeout(() => {
      luke.style.transition = 'bottom 0.4s'; // Smooth downward motion
      luke.style.bottom = '20px'; // Reset position
      setTimeout(() => isJumping = false, 400); // Reset jump flag
    }, 400); // Jump duration
  }
  
  function updateTrailPosition() {
    const trailOffset = 50; // Adjust this value to position the trail higher (originating from Luke's back)

    if (passedRedObstacles) {
      // Position the star trail slightly behind Luke and higher
      trail.style.opacity = '1'; // Show the star trail
      trail.style.left = `${parseInt(luke.style.left) - 20}px`; // Align horizontally behind Luke
      trail.style.bottom = `${parseInt(luke.style.bottom) + trailOffset}px`; // Raise the trail
    } else {
      trail.style.opacity = '0'; // Hide the star trail until red obstacles are cleared
    }

    if (passedYellowObstacles) {
      // Position the rainbow trail slightly behind Luke and higher
      rainbowTrail.style.opacity = '1'; // Show the rainbow trail
      rainbowTrail.style.left = `${parseInt(luke.style.left) - 20}px`; // Align horizontally behind Luke
      rainbowTrail.style.bottom = `${parseInt(luke.style.bottom) + trailOffset}px`; // Raise the trail
    } else {
      rainbowTrail.style.opacity = '0'; // Hide the rainbow trail until yellow obstacles are cleared
    }
  }

// Continuously update the trail's position
setInterval(updateTrailPosition, 50);

  function runGameSequence() {
    if (currentSet >= obstacleColors.length) {
    // Check if the player passed all obstacles successfully
    if (obstacles.length === 0) { // Ensure no remaining obstacles
      gameOver('success'); // Trigger the success scenario
    }
    return; // Stop the game after the last phase
  }

  const color = obstacleColors[currentSet];
  showOverlay(currentSet + 1, () => {
    spawnObstacles(color, () => {
      currentSet++;

      // Change Luke's image after passing the red obstacles
      if (color === 'red') {
        const luke = document.getElementById('luke');
        luke.style.backgroundImage =
          "url('https://media.giphy.com/media/hFVrwYJaIz0uk/giphy.gif')";
        console.log('Luke has changed after passing red obstacles!');
        passedRedObstacles = true;
      }

      // Change Luke's image after passing the yellow obstacles
      if (color === 'yellow') {
        const luke = document.getElementById('luke');
        luke.style.backgroundImage =
          "url('https://media.giphy.com/media/hFVrwYJaIz0uk/giphy.gif')";
        console.log('Luke has changed after passing yellow obstacles!');
        passedYellowObstacles = true
      }
      
      // Change Luke's image after passing the yellow obstacles
      if (color === 'blue') {
        const luke = document.getElementById('luke');
        luke.style.backgroundImage =
          "url('https://media.giphy.com/media/hFVrwYJaIz0uk/giphy.gif')";
        console.log('Luke has changed after passing blue obstacles!');
      }


      // Increase the speed after completing a color set
      obstacleSpeed += 1; // Adjust this value for a more noticeable or subtle speed increase
      console.log(`Speed increased! New speed: ${obstacleSpeed}`);

      runGameSequence(); // Start the next set of obstacles
    });
  });
}

  function spawnObstacles(color, onComplete) {
  let clearedObstacles = 0;

  // Define image sources for specific obstacle colors
  const obstacleImages = {
    red: 'https://static.vecteezy.com/system/resources/previews/049/655/630/non_2x/beer-mug-illustration-png.png', // Beer mug
    yellow: 'https://static.vecteezy.com/system/resources/previews/016/461/698/non_2x/nicotine-smoke-weed-smoking-cigar-cigarette-marijuana-free-png.png', // Cigarette
    blue: 'https://static.vecteezy.com/system/resources/thumbnails/008/480/377/small_2x/pill-3d-model-cartoon-style-render-illustration-png.png', // Pill
    purple: 'https://static.vecteezy.com/system/resources/previews/023/493/053/non_2x/3d-game-console-icon-png.png', // Game console
  };

  for (let i = 0; i < 5; i++) {
    const obstacle = document.createElement('img'); // Use an <img> element instead of a <div>
    obstacle.className = `obstacle ${color}`;
    obstacle.src = obstacleImages[color]; // Set the image source based on the color
    obstacle.style.left = `${window.innerWidth + i * 600}px`; // Space obstacles farther apart
    obstacle.style.position = 'absolute'; // Ensure proper positioning
    obstacle.style.width = '50px'; // Set obstacle size
    obstacle.style.height = '50px';
    gameContainer.appendChild(obstacle);
    obstacles.push(obstacle);

    moveObstacle(obstacle, () => {
      clearedObstacles++;
      if (clearedObstacles === 5) {
        onComplete(); // Trigger the next phase once all obstacles are cleared
      }
    });
  }
}

  function moveObstacle(obstacle, onClear) {
    let position = parseInt(obstacle.style.left);

    // Determine the obstacle type
    const obstacleType = obstacle.classList.contains('red')
      ? 'red'
      : obstacle.classList.contains('yellow')
      ? 'yellow'
      : obstacle.classList.contains('blue')
      ? 'blue'
      : 'purple';

    function frame() {
      if (!gameRunning) return;

      position -= obstacleSpeed; // Move obstacle left
      obstacle.style.left = position + 'px';

      const lukeRect = luke.getBoundingClientRect();
      const obstacleRect = obstacle.getBoundingClientRect();

      // Collision detection
      if (
        position < window.innerWidth &&
        position > 0 &&
        obstacleRect.left < lukeRect.right &&
        obstacleRect.right > lukeRect.left &&
        obstacleRect.bottom > lukeRect.top &&
        obstacleRect.top < lukeRect.bottom
      ) {
        gameOver(obstacleType); // Pass obstacle type to gameOver
        return;
      }

      if (position < -50) {
        obstacle.remove();
        obstacles = obstacles.filter((obs) => obs !== obstacle); // Clean up
        onClear(); // Notify that this obstacle is cleared
      } else {
        requestAnimationFrame(frame);
      }
    }

    frame();
  }

  function showOverlay(number, callback) {
  overlay.innerHTML = ''; // Clear previous content
  overlay.style.display = 'flex'; // Show the overlay

  if (number === 1) {
    // Overlay 1: Custom content
    const img = document.createElement('img');
    img.src = 'https://img.lovepik.com/free_png/28/90/25/14J58PICd7NP124amDaw3_PIC2018.png_860.png'; // Replace with the correct image path
    img.alt = 'Friends drinking';
    overlay.appendChild(img);

    const text = document.createElement('p');
    text.innerText = 'Your friend invited you to drink after school.';
    overlay.appendChild(text);

    setTimeout(() => {
      overlay.style.display = 'none';
      if (callback) callback();
    }, 4000); // Show for 4 seconds, right now lagging to 8 secs
  } else if (number === 2) {
    // Overlay 2: Custom sequence
    const img1 = document.createElement('img');
    img1.src = 'https://static.vecteezy.com/system/resources/previews/010/872/640/non_2x/3d-running-man-png.png'; // Replace with Energy image path
    img1.alt = 'Luke feels energized';
    overlay.appendChild(img1);

    const text1 = document.createElement('p');
    text1.innerText = 'Luke feels energized.';
    overlay.appendChild(text1);

    setTimeout(() => {
      overlay.innerHTML = ''; // Clear for second part

      const img2 = document.createElement('img');
      img2.src = 'https://c8.alamy.com/comp/2ANE8HW/illustration-of-a-teenage-guy-with-friends-one-of-them-has-a-cigarette-in-his-hand-and-asking-the-other-to-smoke-2ANE8HW.jpg'; // Replace with Cigarette image path
      img2.alt = 'Friends offering a cigarette';
      overlay.appendChild(img2);

      const text2 = document.createElement('p');
      text2.innerText = 'Your friends offer you a cigarette.';
      overlay.appendChild(text2);
    }, 3000); // Switch after 3 seconds

    setTimeout(() => {
      overlay.style.display = 'none';
      if (callback) callback();
    }, 9000); // Total 9 seconds
  } else if (number === 3) {
    // Overlay 3: Healthy choice first, then suspicious drugs
    const img1 = document.createElement('img');
    img1.src = 'https://static.vecteezy.com/system/resources/thumbnails/056/561/419/small_2x/serene-rustic-3d-illustration-dynamic-running-boy-4k-png.png'; // Replace with Healthy image path
    img1.alt = 'Luke feels proud';
    overlay.appendChild(img1);

    const text1 = document.createElement('p');
    text1.innerText = 'Luke feels proud of his healthy choice.';
    overlay.appendChild(text1);

    setTimeout(() => {
      overlay.innerHTML = ''; // Clear for second part

      const img2 = document.createElement('img');
      img2.src = 'https://media.assettype.com/TNIE%2Fimport%2F2017%2F1%2F10%2Foriginal%2Fdrugs.jpg?w=480&auto=format%2Ccompress&fit=max'; // Replace with Suspicious drugs image path
      img2.alt = 'Suspicious drugs';
      overlay.appendChild(img2);

      const text2 = document.createElement('p');
      text2.innerText = 'You are offered suspicious drugs at a party.';
      overlay.appendChild(text2);
    }, 3000); // Switch after 3 seconds

    setTimeout(() => {
      overlay.style.display = 'none';
      if (callback) callback();
    }, 9000); // Total 9 seconds
  } else if (number === 4) {
    // Overlay 4: Healthy choice first, then other content
    const img1 = document.createElement('img');
    img1.src = 'https://static.vecteezy.com/system/resources/thumbnails/049/576/178/small/a-man-is-running-in-a-cartoon-style-png.png'; // Replace with Healthy image path
    img1.alt = 'relieved for avoiding drugs';
    overlay.appendChild(img1);

    const text1 = document.createElement('p');
    text1.innerText = 'Luke feels relieved for avoiding drugs';
    overlay.appendChild(text1);

    setTimeout(() => {
      overlay.innerHTML = ''; // Clear for second part

      const img2 = document.createElement('img');
      img2.src = 'https://img.freepik.com/free-vector/character-playing-videogame_23-2148522528.jpg'; // Replace with Suspicious drugs image path
      img2.alt = 'Suspicious drugs';
      overlay.appendChild(img2);

      const text2 = document.createElement('p');
      text2.innerText = 'Luke has a test tomorrow but he wants to play.';
      overlay.appendChild(text2);
    }, 3000); // Switch after 3 seconds

    setTimeout(() => {
      overlay.style.display = 'none';
      if (callback) callback();
    }, 9000); // Total 9 seconds
  } else {
    // Other overlays: Standard content
    overlay.innerText = `Overlay ${number}: Get Ready for ${capitalizeWord(obstacleColors[number - 1])} Obstacles...`;

    setTimeout(() => {
      overlay.style.display = 'none';
      if (callback) callback();
    }, 9000); // Overlay shows for 9 seconds
  }
}

  function gameOver(obstacleType) {
  gameRunning = false;
  
    const backgroundMusic = document.getElementById('background-music'); // Get the music element
    backgroundMusic.pause(); // Pause the music

  obstacles.forEach((obstacle) => obstacle.remove());
  obstacles = [];

  if (obstacleType === 'red') {
    // Array of images and descriptions
    const imageSeries = [
      {
        src: 'https://img.lovepik.com/original_origin_pic/18/12/01/4590536e8ea426d6b1a90a930952166e.png_wh860.png',
        description: 'Luke agreed to drink together after school.',
      },
      {
        src: 'https://img.freepik.com/free-vector/hand-drawn-drunk-cartoon-illustration_23-2151212641.jpg?t=st=1740807498~exp=1740811098~hmac=0455525fd9fe0ca5ecb7736e8d0bcabcdb71d0e814ff7ca23cd92f403d36daed&w=1380',
        description: 'Luke started drinking heavily with his friends.',
      },
      {
        src: 'https://img.freepik.com/free-vector/alcoholic-man-cartoon-character_1308-116430.jpg?t=st=1740807715~exp=1740811315~hmac=01143b5e2138dcaebc3ac0b4e219a5469d6d4aaf4f5fb98e17e5d9e9ce1a8aa4&w=1380',
        description: 'Luke became dependent on alcohol.',
      },
      {
        src: 'https://img.freepik.com/premium-vector/alcohol-abuse-concept-guy-has-drunk-too-much_91248-930.jpg?w=1380',
        description: 'Luke’s health deteriorated due to excessive drinking.',
      },
      {
        src: 'https://cdn.vectorstock.com/i/1000v/94/64/game-over-games-screen-glitch-computer-video-vector-22579464.jpg',
        description: 'Thank you for playing.',
      },
    ];

    let index = 0;

    // Function to show a single image and description
    const showNextImage = () => {
      if (index >= imageSeries.length) {
        // Reload the game after the last image
        setTimeout(() => location.reload(), 2000);
        return;
      }

      const { src, description } = imageSeries[index];
      overlay.innerHTML = ''; // Clear previous content
      overlay.style.display = 'flex';

      const img = document.createElement('img');
      img.src = src;
      img.alt = description;
      img.className = 'fade-in'; // Apply fade-in class
      overlay.appendChild(img);

      const text = document.createElement('p');
      text.innerText = description;
      text.className = 'fade-in'; // Apply fade-in class
      overlay.appendChild(text);

      // Wait for 4 seconds (2s fade-in + 2s display), then fade out
      setTimeout(() => {
        img.classList.remove('fade-in');
        img.classList.add('fade-out'); // Apply fade-out class
        text.classList.remove('fade-in');
        text.classList.add('fade-out'); // Apply fade-out class
      }, 6000);

      // Wait for 4 seconds, then show the next image
      setTimeout(() => {
        index++;
        showNextImage();
      }, 6000);
    };

    showNextImage(); // Start the sequence
  } else if (obstacleType === 'yellow') {
    // Yellow obstacle sequence
    const imageSeries = [
      {
        src: 'https://media.istockphoto.com/id/1060838804/vector/teenager-smoking-cigarette-on-white-background.jpg?s=612x612&w=0&k=20&c=Hcb9DedxwDEJ0QRgYo5jhk6wPzv0sHr3d9NhXVkq00s=',
        description: 'You accepted to smoke together.',
      },
      {
        src: 'https://img.freepik.com/premium-vector/lung-cancer-little-boy-smoking_1308-15022.jpg?w=1380',
        description: 'You cough after smoking but it’s too addicting to quit.',
      },
      {
        src: 'https://img.freepik.com/premium-vector/alcohol-addiction-smoking-bad-habits-drunk-man-fell-asleep-with-unfinished-cigarette_519070-418.jpg?w=1380',
        description: 'As you grew older, you smoked to relieve stress.',
      },
      {
        src: 'https://previews.123rf.com/images/artinspiring/artinspiring2001/artinspiring200100336/138182158-le-patient-souffre-d-une-maladie-canc%C3%A9reuse-patient-d-oncologie-de-caract%C3%A8re-masculin-avec-un.jpg',
        description: 'You end up sick in the hospital and wish you never started smoking.',
      },
      {
        src: 'https://cdn.vectorstock.com/i/1000v/94/64/game-over-games-screen-glitch-computer-video-vector-22579464.jpg',
        description: 'Thank you for playing.',
      },
    ];

    let index = 0;

    const showNextImage = () => {
      if (index >= imageSeries.length) {
        setTimeout(() => location.reload(), 2000);
        return;
      }

      const { src, description } = imageSeries[index];
      overlay.innerHTML = ''; // Clear previous content
      overlay.style.display = 'flex';

      const img = document.createElement('img');
      img.src = src;
      img.alt = description;
      img.className = 'fade-in';
      overlay.appendChild(img);

      const text = document.createElement('p');
      text.innerText = description;
      text.className = 'fade-in';
      overlay.appendChild(text);

      setTimeout(() => {
        img.classList.remove('fade-in');
        img.classList.add('fade-out');
        text.classList.remove('fade-in');
        text.classList.add('fade-out');
      }, 6000);

      setTimeout(() => {
        index++;
        showNextImage();
      }, 6000);
    };

    showNextImage();
  } if (obstacleType === 'purple') {
    // Blue obstacle sequence
    const imageSeries = [
      {
      src: 'https://static.vecteezy.com/system/resources/previews/037/239/060/non_2x/cartoon-illustration-of-children-playing-video-games-on-sofa-vector.jpg',
      description: 'Luke decided to play first and study later.',
    },
    {
      src: 'https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcQ1jBkzYoSKV__X4XM9-R8PT-tiPQOYVntZCA&s',
      description: 'Only late at night did he decide to study and he pulled an all-nighter.',
    },
    {
      src: 'https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcSJ98JVwvZxnTDxoG3dzYc0FuatjhUTX1rGdQ&s',
      description: 'The next day Luke felt exhausted and his brain was fuzzy.',
    },
    {
      src: 'https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcQJ0F6PXkHnEHoN7I7qpp8MV-mDInPSDvXIqA&s',
      description: 'Luke got a failed grade on the test and regretted that he didn’t start studying earlier.',
    },
    {
        src: 'https://cdn.vectorstock.com/i/1000v/94/64/game-over-games-screen-glitch-computer-video-vector-22579464.jpg',
        description: 'Thank you for playing.',
      },
  ];

    let index = 0;

    const showNextImage = () => {
      if (index >= imageSeries.length) {
        setTimeout(() => location.reload(), 2000);
        return;
      }

      const { src, description } = imageSeries[index];
      overlay.innerHTML = ''; // Clear previous content
      overlay.style.display = 'flex';

      const img = document.createElement('img');
      img.src = src;
      img.alt = description;
      img.className = 'fade-in';
      overlay.appendChild(img);

      const text = document.createElement('p');
      text.innerText = description;
      text.className = 'fade-in';
      overlay.appendChild(text);

      setTimeout(() => {
        img.classList.remove('fade-in');
        img.classList.add('fade-out');
        text.classList.remove('fade-in');
        text.classList.add('fade-out');
      }, 6000);

      setTimeout(() => {
        index++;
        showNextImage();
      }, 6000);
    };

    showNextImage();
  }if (obstacleType === 'success') {
    // Blue obstacle sequence
    const imageSeries = [
      {
        src: 'https://www.shutterstock.com/image-vector/young-man-arms-crossed-shows-600nw-2465588727.jpg',
        description: 'Luke rejected drinking and smoking whenever someone asked him to.',
      },
      {
        src: 'https://media.istockphoto.com/id/1403438531/vector/vector-of-a-man-rejects-drugs-when-being-offered.jpg?s=612x612&w=0&k=20&c=GtvNdqppVkAK-g85kd0Giessy_9lGkNqHpJsN-fisww=',
        description: 'He avoided drugs at all costs.',
      },
      {
        src: 'https://i0.wp.com/magnoliaprep.com/wp-content/uploads/2018/01/Cartoon-Studying-Pic-scaled.jpg?fit=3200%2C2400&ssl=1',
        description: 'He studied in a timely manner instead of pulling all-nighters.',
      },
      {
        src: 'https://t4.ftcdn.net/jpg/02/68/46/53/360_F_268465351_PKTnfTHgEgIYFP7Uz5Br8b2tCi2TAQyQ.jpg',
        description: 'In the end, Luke successfully graduated happy and healthy with his friends. Congratulations! You helped Luke get through high school safely.',
      },
      {
        src: 'https://cdn.vectorstock.com/i/1000v/94/64/game-over-games-screen-glitch-computer-video-vector-22579464.jpg',
        description: 'Thank you for playing.',
      },
    ];


    let index = 0;

    const showNextImage = () => {
      if (index >= imageSeries.length) {
        setTimeout(() => location.reload(), 2000);
        return;
      }

      const { src, description } = imageSeries[index];
      overlay.innerHTML = ''; // Clear previous content
      overlay.style.display = 'flex';

      const img = document.createElement('img');
      img.src = src;
      img.alt = description;
      img.className = 'fade-in';
      overlay.appendChild(img);

      const text = document.createElement('p');
      text.innerText = description;
      text.className = 'fade-in';
      overlay.appendChild(text);

      setTimeout(() => {
        img.classList.remove('fade-in');
        img.classList.add('fade-out');
        text.classList.remove('fade-in');
        text.classList.add('fade-out');
      }, 6000);

      setTimeout(() => {
        index++;
        showNextImage();
      }, 6000);
    };

    showNextImage();
  }if (obstacleType === 'blue') {
    // Blue obstacle sequence
    const imageSeries = [
      {
        src: 'https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcQnfkm6tr1PXg0IBZLz-2XKqLh0zBje0fPiQw&s',
        description: 'Immediately that night, someone overdosed after trying the drugs.',
      },
      {
        src: 'https://img.freepik.com/free-vector/bad-habits-cartoon-concept-with-dengerous-drug-addict-vector-illustration_1284-80090.jpg?t=st=1740808599~exp=1740812199~hmac=4f0e765162688537f925a5bd1f582ae767306bb20d9843e42ede0d539bfc0cbe&w=1380',
        description: 'Although you got away, you feel the need to take the drug again.',
      },
      {
        src: 'https://www.vice.com/wp-content/uploads/sites/2/2020/06/1592326071406-drugs.jpeg?resize=1536,862',
        description: 'Luke gets addicted and starts dealing drugs.',
      },
      {
        src: 'https://republicaimg.nagariknewscdn.com/shared/web/uploads/media/Arrest_20201217120508.jpg',
        description: 'He gets caught one night drug dealing and is arrested and imprisoned.',
      },
      {
        src: 'https://cdn.vectorstock.com/i/1000v/94/64/game-over-games-screen-glitch-computer-video-vector-22579464.jpg',
        description: 'Thank you for playing.',
      },
    ];

    let index = 0;

    const showNextImage = () => {
      if (index >= imageSeries.length) {
        setTimeout(() => location.reload(), 2000);
        return;
      }

      const { src, description } = imageSeries[index];
      overlay.innerHTML = ''; // Clear previous content
      overlay.style.display = 'flex';

      const img = document.createElement('img');
      img.src = src;
      img.alt = description;
      img.className = 'fade-in';
      overlay.appendChild(img);

      const text = document.createElement('p');
      text.innerText = description;
      text.className = 'fade-in';
      overlay.appendChild(text);

      setTimeout(() => {
        img.classList.remove('fade-in');
        img.classList.add('fade-out');
        text.classList.remove('fade-in');
        text.classList.add('fade-out');
      }, 6000);

      setTimeout(() => {
        index++;
        showNextImage();
      }, 6000);
    };

    showNextImage();
  }
}

</script>



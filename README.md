# -Comprehensive-Training-on-Unix-and-Linux-OS-Fundamentals-autoplay-bot

# 🐧 Comprehensive Training on Unix and Linux OS Fundamentals

Welcome to this self-paced video training course.  
To **autoplay all videos at 16x speed** and **auto-skip to the next video**, follow the steps below.

---

## 🚀 Step-by-Step: Enable 16x Autoplay

### ✅ Step 1: Enable Autoplay in Your Browser Settings

> Autoplay settings must allow videos to play **automatically and muted**.

#### 🔹 Chrome
- Go to: `chrome://settings/content/mediaAutoplay`
- Set to: **"Sites can automatically play video"**

#### 🔹 Firefox
- Go to: `about:preferences#privacy`
- Scroll to **Permissions** → Click **Settings** next to "Autoplay"
- Set to: **Allow Audio and Video**

#### 🔹 Microsoft Edge
- Go to: `edge://settings/content/mediaAutoplay`
- Set to: **"Allow"**

#### 🔹 Safari (macOS)
- Right-click the address bar → **Settings for This Website**
- Set **Auto-Play** to **Allow All Auto-Play**

---

### 🔧 Step 2: Open Developer Console

1. Press **`Ctrl + Shift + I`** (Windows/Linux) or **`Cmd + Option + I`** (Mac)
2. Click the **"Console"** tab

---

### 📋 Step 3: Paste This Code and Press Enter

```javascript
document.querySelector('video').playbackRate = 16.0;
console.log('Starting dynamic video autoplay...');

function findCurrentVideo() {
    const videoSelectors = [
        'video',
        '[id*="video"]',
        '[class*="video"]',
        '.vjs-tech',
        '[data-vjs-player]',
    ];
    
    for (let selector of videoSelectors) {
        const videos = document.querySelectorAll(selector);
        for (let video of videos) {
            if (video.tagName === 'VIDEO' && video.offsetParent !== null) {
                return video;
            }
        }
    }
    return null;
}

function findVideoJSPlayer() {
    if (!window.videojs) return null;
    
    const players = [];
    
    document.querySelectorAll('video').forEach(video => {
        try {
            const player = videojs.getPlayer(video);
            if (player) players.push(player);
        } catch (e) {
            if (video.player) players.push(video.player);
        }
    });
    
    try {
        const allPlayers = videojs.getPlayers();
        Object.values(allPlayers).forEach(player => {
            if (player && player.el_) players.push(player);
        });
    } catch (e) {}
    
    return players.length > 0 ? players[0] : null;
}

function setupDynamicAutoplay() {
    console.log('Searching for video player...');
    
    const video = findCurrentVideo();
    const player = findVideoJSPlayer();
    
    if (!video && !player) {
        console.log('No video found, will retry...');
        return false;
    }
    
    console.log('Video found:', video ? 'HTML5' : '', player ? 'Video.js' : '');
    
    function handleVideoEnd() {
        console.log('Video ended! Finding next button...');
        
        const buttonTexts = ['next', 'continue', 'proceed', 'forward', '→', '▶'];
        let nextButton = null;
        
        const clickables = document.querySelectorAll('button, a, [role="button"], [onclick], [class*="click"], [class*="btn"]');
        
        for (let element of clickables) {
            const text = (element.innerText || element.textContent || element.title || element.ariaLabel || '').toLowerCase();
            const hasNextText = buttonTexts.some(btnText => text.includes(btnText));
            
            if (hasNextText && element.offsetParent !== null && !element.disabled) {
                nextButton = element;
                console.log('Found next button:', element.innerText || element.title);
                break;
            }
        }
        
        if (nextButton) {
            console.log('Clicking next...');
            nextButton.click();
            
            setTimeout(() => {
                console.log('Setting up autoplay for next video...');
                setupDynamicAutoplay();
                setTimeout(playNewVideo, 2000);
            }, 3000);
        } else {
            console.log('No next button found');
        }
    }
    
    if (player && player.on) {
        player.on('ended', handleVideoEnd);
        console.log('Video.js listener added');
    } else if (video) {
        video.addEventListener('ended', handleVideoEnd);
        console.log('HTML5 listener added');
    }
    
    return true;
}

function playNewVideo() {
    const newVideo = findCurrentVideo();
    const newPlayer = findVideoJSPlayer();
    
    if (newPlayer && newPlayer.play) {
        newPlayer.play().catch(() => {
            console.log('Video.js autoplay blocked, trying alternatives...');
            if (newVideo) newVideo.click();
        });
    } else if (newVideo) {
        newVideo.play().catch(() => {
            console.log('HTML5 autoplay blocked, trying click...');
            newVideo.click();
        });
    }
}

function startWithRetry() {
    let attempts = 0;
    const maxAttempts = 10;
    
    const trySetup = () => {
        attempts++;
        console.log(`Attempt ${attempts} to find video...`);
        
        if (setupDynamicAutoplay()) {
            console.log('Dynamic autoplay system ready!');
        } else if (attempts < maxAttempts) {
            setTimeout(trySetup, 2000);
        } else {
            console.log('Could not find video after 10 attempts');
        }
    };
    
    trySetup();
}

document.addEventListener('click', function() {
    const video = findCurrentVideo();
    if (video) {
        video.play().then(() => video.pause()).catch(() => {});
    }
    console.log('Autoplay permissions enabled');
}, { once: true });

startWithRetry();

let currentURL = window.location.href;
setInterval(() => {
    if (window.location.href !== currentURL) {
        currentURL = window.location.href;
        console.log('Page changed, re-setting up autoplay...');
        setTimeout(startWithRetry, 1000);
    }
}, 1000);

console.log('Dynamic video autoplay system loaded!');
console.log('This will work even if video IDs change');

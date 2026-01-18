<script setup>
import { onMounted } from 'vue'; // Needed for DOM manipulation in Vue
import './assets/my-style.css';

/* --- TASK 5: V-FOR DATA --- */
const galleryItems = [
  { 
    id: 1, 
    title: 'Hobbies', 
    img: '/media/HOB.png', 
    desc: 'My hobbies are too many to count but I really love creating bouquets in different ways, I wish to create things that makes me happy.' 
  },
  { 
    id: 2, 
    title: 'Goals', 
    img: '/media/GOAL.png', 
    desc: 'My Goals are simple: Family, Travel, Happiness, and Prosperity.' 
  },
  { 
    id: 3, 
    title: 'Education', 
    img: '/media/EDUC.png', 
    desc: 'Graduated in APC-SHS-STEM Specialized in I.T. Currently taking APC-BSCS-SF.' 
  }
];

/* --- YOUR ORIGINAL LOGIC (Task 4) --- */
onMounted(() => {
    const trigger = document.getElementById('frameTrigger');
    const typewriter = document.getElementById('typewriter');
    const textToType = "Welcome to my digital space! I am Karol Joy, a creator and innovator. Thank you for opening this scroll. Explore my journey and projects below...";

    let i = 0;
    let isTyping = false;

    if (trigger) {
        trigger.addEventListener('mouseenter', () => {
            isTyping = true;
            typewriter.innerHTML = "";
            i = 0;
            setTimeout(typeEffect, 600); 
        });

        trigger.addEventListener('mouseleave', () => {
            isTyping = false;
            typewriter.innerHTML = "";
        });
    }

    function typeEffect() {
        if (isTyping && i < textToType.length) {
            typewriter.innerHTML += textToType.charAt(i);
            i++;
            setTimeout(typeEffect, 40);
        }
    }

    /* Music & Observer Logic goes here (Unchanged) */
    const music = document.getElementById('bgMusic');
    const playBtn = document.getElementById('playBtn');

    if (playBtn) {
        playBtn.addEventListener('click', () => {
            if (music.paused) {
                music.play();
                playBtn.innerText = "⏸ PAUSE";
                playBtn.classList.remove('pulsing');
                playBtn.style.background = "#d4af37";
            } else {
                music.pause();
                playBtn.innerText = "▶ PLAY";
                playBtn.classList.add('pulsing');
                playBtn.style.background = "#2b1d10";
            }
        });
    }

    const revealTitles = document.querySelectorAll('.reveal-text');
    revealTitles.forEach(title => {
        const text = title.textContent;
        title.textContent = ""; 
        [...text].forEach((char, i) => {
            const span = document.createElement('span');
            span.textContent = char === " " ? "\u00A0" : char; 
            span.style.transitionDelay = `${i * 0.07}s`; 
            title.append(span);
        });
    });

    const textObserver = new IntersectionObserver((entries) => {
        entries.forEach(entry => {
            if (entry.isIntersecting) {
                entry.target.classList.add('active');
            } else {
                entry.target.classList.remove('active');
            }
        });
    }, { threshold: 0.5 });

    revealTitles.forEach(t => textObserver.observe(t));
});

/* Modal functions made global for HTML onclicks */
window.openModal = function(imgSrc, title, desc) {
    const modal = document.getElementById('galleryModal');
    document.getElementById('modalImg').src = imgSrc;
    document.getElementById('modalTitle').innerText = title;
    document.getElementById('modalDesc').innerText = desc;
    modal.style.display = "flex";
}

window.closeModal = function() {
    document.getElementById('galleryModal').style.display = "none";
}

window.openHobbies = () => window.openModal('/media/HOB.png', 'Hobbies', '...');
window.openGoals = () => window.openModal('/media/GOAL.png', 'Goals', '...');
window.openEducation = () => window.openModal('/media/EDUC.png', 'Education', '...');

</script>

<template>
    <div class="bg-overlay"></div>

    <header>
        <nav class="nav-container">
            <ul class="nav-links">
                <li><a href="#home">HOME</a></li>
                <li class="dropdown">
                   <a href="#about-title" class="dropbtn">ABOUT</a>
                    <ul class="dropdown-content">
                        <li><a href="javascript:void(0)" onclick="openHobbies()">HOBBIES</a></li>
                        <li><a href="javascript:void(0)" onclick="openGoals()">GOALS</a></li>
                        <li><a href="javascript:void(0)" onclick="openEducation()">EDUCATION</a></li>
                    </ul>
                </li>
                <li><a href="#projects-section">PROJECTS</a></li>
                <li><a href="#gallery-section">GALLERY</a></li>
                <li class="dropdown">
                    <a href="javascript:void(0)" class="dropbtn">CONTACT</a>
                    <ul class="dropdown-content">
                        <li><a href="https://linkedin.com" target="_blank">LINKEDIN</a></li>
                        <li><a href="https://github.com" target="_blank">GITHUB</a></li>
                        <li><a href="https://instagram.com" target="_blank">INSTAGRAM</a></li>
                    </ul>
                </li>
            </ul>
        </nav>
    </header>

    <main class="hero">
        <div class="main-display">
            <div class="interactive-wrapper" id="frameTrigger">
                <div class="scroll-area" id="about-me-scroll">
                    <div id="typewriter" class="ink-text"></div>
                    <div class="music-player">
                        <img src="/media/album.jpg" alt="Song Art" class="song-art">
                        <div class="song-details">
                            <p class="song-name"><b>Young and Beautiful</b></p>
                            <p class="artist-name">Lana Del Ray</p>
                            <button id="playBtn" class="play-button pulsing">▶ PLAY</button>
                        </div>
                    </div>
                    <audio id="bgMusic" loop>
                        <source src="/media/BG MUSIC.mp3" type="audio/mpeg">
                    </audio>
                </div>
                <div class="portrait-box">
                    <img src="/media/MAIN PIC.png" alt="Karol Joy" class="portrait-img">
                </div>
            </div>
        </div>
    </main>

    <h1 id="about-title" class="gallery-title reveal-text">About Me</h1>
    
    <section class="gallery-container">
        <div class="gallery-item" v-for="item in galleryItems" :key="item.id" @click="openModal(item.img, item.title, item.desc)">
            <img :src="item.img" :alt="item.title">
            <div class="hover-overlay">
                <h3>{{ item.title }}</h3>
                <p>View Details</p>
            </div>
        </div>
    </section>

    <div id="galleryModal" class="modal">
        <div class="modal-content">
            <span class="close-btn" onclick="closeModal()">&times;</span>
            <img id="modalImg" src="">
            <div class="modal-text">
                <h2 id="modalTitle"></h2>
                <p id="modalDesc"></p>
            </div>
        </div>
    </div>

    </template>

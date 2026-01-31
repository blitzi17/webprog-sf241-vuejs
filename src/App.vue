<script setup>
/* --- TASK 07: SUPABASE IMPORTS --- */
import { ref, onMounted } from 'vue';
import { supabase } from './supabaseClient'; // Import the client you created
import './assets/my-style.css';

/* --- TASK 06/07: DATABASE STATE --- */
const instruments = ref([]);

// --- NEW TASK 03: FEEDBACK STATE ---
const comments = ref([]);
const visitorName = ref('');
const visitorFeedback = ref('');

/* --- FETCH INSTRUMENTS --- */
async function getInstruments() {
    const { data, error } = await supabase.from('instruments').select();
    if (error) {
        console.error("Error fetching instruments:", error);
    } else {
        instruments.value = data;
    }
}

/* --- NEW TASK 03: FETCH COMMENTS --- */
async function fetchComments() {
    const { data, error } = await supabase
        .from('comments')
        .select('*')
        .order('created_at', { ascending: false });
    
    if (!error) {
        comments.value = data;
    }
}

/* --- NEW TASK 03: POST COMMENT --- */
async function postComment() {
    if (!visitorName.value || !visitorFeedback.value) return;
    
    const { error } = await supabase
        .from('comments')
        .insert([{ name: visitorName.value, feedback: visitorFeedback.value }]);
    
    if (!error) {
        visitorName.value = '';
        visitorFeedback.value = '';
        fetchComments(); // Refresh list after posting
    }
}

/* --- TASK 5: V-FOR DATA (Hardcoded) --- */
const galleryItems = [
  { 
    id: 1, 
    title: 'Hobbies', 
    img: '/media/HOB.png', 
    desc: 'My hobbies are too many to count but I really love creating bouquets in different ways...' 
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

/* --- ON MOUNTED LOGIC --- */
onMounted(() => {
    getInstruments();
    fetchComments(); // Task 03: Load comments on page load

    const trigger = document.getElementById('frameTrigger');
    const typewriter = document.getElementById('typewriter');
    const textToType = "Welcome to my digital space! I am Karol Joy, a creator and innovator...";

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

    /* Music Logic */
    const music = document.getElementById('bgMusic');
    const playBtn = document.getElementById('playBtn');

    if (playBtn) {
        playBtn.addEventListener('click', () => {
            if (music.paused) {
                music.play();
                playBtn.innerText = "⏸ PAUSE";
            } else {
                music.pause();
                playBtn.innerText = "▶ PLAY";
            }
        });
    }

    /* Reveal Text Animation */
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
            }
        });
    }, { threshold: 0.5 });

    revealTitles.forEach(t => textObserver.observe(t));
});

/* Modal functions */
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
                        <li><a href="javascript:void(0)" @click="openModal('/media/HOB.png', 'Hobbies', 'Creating bouquets...')">HOBBIES</a></li>
                        <li><a href="javascript:void(0)" @click="openModal('/media/GOAL.png', 'Goals', 'Happiness and Prosperity.')">GOALS</a></li>
                    </ul>
                </li>
                <li><a href="#supabase-section">DATABASE</a></li>
                <li><a href="#comment-section">FEEDBACK</a></li>
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

    <section id="supabase-section" style="padding: 50px; text-align: center; color: white;">
        <h2 class="reveal-text">Supabase Instruments</h2>
        <div class="instrument-list">
            <p v-if="instruments.length === 0">Connecting to Supabase...</p>
            <ul>
                <li v-for="inst in instruments" :key="inst.id">
                    {{ inst.name }}
                </li>
            </ul>
        </div>
    </section>

    <section id="comment-section" style="padding: 50px; color: white; background: rgba(0,0,0,0.3); text-align: center;">
        <h2 class="reveal-text">Visitor Feedback</h2>
        
        <div style="max-width: 600px; margin: 0 auto 30px auto; display: flex; flex-direction: column; gap: 10px;">
            <input v-model="visitorName" placeholder="Your Name" style="padding: 12px; background: #111; border: 1px solid #d4af37; color: white;">
            <textarea v-model="visitorFeedback" placeholder="Leave a comment about the Optimizer project..." style="padding: 12px; height: 80px; background: #111; border: 1px solid #d4af37; color: white;"></textarea>
            <button @click="postComment" style="padding: 12px; background: #d4af37; color: black; font-weight: bold; cursor: pointer; border: none;">SUBMIT FEEDBACK</button>
        </div>

        <div style="max-width: 600px; margin: 0 auto; text-align: left;">
            <div v-for="c in comments" :key="c.id" style="border-bottom: 1px solid #444; padding: 15px 0;">
                <strong style="color: #d4af37;">{{ c.name }}</strong>
                <p style="margin: 5px 0; font-size: 0.9em; opacity: 0.9;">{{ c.feedback }}</p>
                <small style="opacity: 0.5; font-size: 0.7em;">{{ new Date(c.created_at).toLocaleDateString() }}</small>
            </div>
        </div>
    </section>

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
<script setup>
import { ref, computed, onMounted, onUnmounted } from 'vue';
import hljs from 'highlight.js';
import 'highlight.js/styles/atom-one-dark.css'; 
import frImg from './fr.png';
import nfrImg from './nfr.png';

// --- PALETTE CONSTANTS (Reference) ---
// Deep Black — #0A0A0A
// Crimson Red — #C1121F
// Royal Gold — #D4AF37

const currentSlideIndex = ref(0);

// --- NEW: Text Size Logic ---
const textSizeOffset = ref(0); // 0 = default, >0 = bigger, <0 = smaller

// Standard Tailwind text scale array
const tailwindSizes = [
    'text-xs', 'text-sm', 'text-base', 'text-lg', 'text-xl', 
    'text-2xl', 'text-3xl', 'text-4xl', 'text-5xl', 'text-6xl', 
    'text-7xl', 'text-8xl', 'text-9xl'
];

// Helper to shift text class up/down
const tSize = (baseClass) => {
    const currentIndex = tailwindSizes.indexOf(baseClass);
    if (currentIndex === -1) return baseClass; // Safety check

    // Calculate new index bounded by array length
    let newIndex = currentIndex + textSizeOffset.value;
    newIndex = Math.max(0, Math.min(newIndex, tailwindSizes.length - 1));
    
    return tailwindSizes[newIndex];
};

const slides = [
    {
        type: 'title',
        title: 'Guess The Object',
        subtitle: 'Software Engineering Project',
        developers: ['Oussama Yaqdane', 'Mohamed Taib Lkanit']
    },
    {
        type: 'simple',
        title: 'How does it work?',
        content: 'A real-time multiplayer drawing and guessing game where creativity meets chaos.'
    },
    {
        type: 'list',
        title: 'Why this project?',
        items: [
            "It's tough and intimidating",
            "It's long",
            "It's complex",
            "It's fun"
        ],
        quote: {
            text: "Surpass your limits, right here, right now.",
            author: "Captain Yami (Black Clover)"
        }
    },
    {
        type: 'list',
        title: 'Tech Stack',
        items: [
            '<strong class="text-[#D4AF37]">Golang</strong> (Backend)',
            '<strong class="text-[#D4AF37]">Gin</strong> (API Framework)',
            '<strong class="text-[#D4AF37]">Websockets</strong> (Real-time comms)',
            '<strong class="text-[#D4AF37]">HTML/CSS/Typescript</strong>',
            '<strong class="text-[#D4AF37]">Vue.js</strong> (Frontend)',
            '<strong class="text-[#D4AF37]">PostgreSQL</strong> (Database)',
            '<strong class="text-[#D4AF37]">Protobuf</strong> (Data serialization)',
            '<strong class="text-[#D4AF37]">NGINX & Certbot</strong> (Reverse proxy and SSL)'
        ]
    },
    {
        type: 'image',
        title: 'Functional Requirements',
        imageSrc: frImg
    },
    {
        type: 'image',
        title: 'Non-Functional Requirements',
        imageSrc: nfrImg
    },
    {
        type: 'list',
        title: 'System Architectural Pattern: MVC',
        items: [
            '<strong class="text-[#D4AF37]">Model:</strong> (Core server logic)',
            '<strong class="text-[#D4AF37]">View: </strong> (Front end)',
            '<strong class="text-[#D4AF37]">Controller: </strong> (Server route handlers)',
        ]
    },
    {
        type: 'code',
        title: 'Backend Architectural Pattern:',
        description: ["Layered Architecture"],
        code: `
┌─────────────────────────────────┐
│   Presentation Layer            │  
│   (HTTP Handlers/Routes)        │ 
├─────────────────────────────────┤
│   Business Logic Layer          │
│   (Game Logic)                  │ 
├─────────────────────────────────┤
│   Data Access Layer             │
│   (Database/Repository)         │ 
├─────────────────────────────────┤
│   Infrastructure Layer          │
│   (Shared/Utilities)            │ 
└─────────────────────────────────┘`
    },
    {
        type: 'code',
        title: 'Monolith Feature-Based Modules',
        code: `api
├── cmd
├── Dockerfile.dev
├── Dockerfile.production
├── internal
    ├── authentication
    │   ├── login.go
    │   ├── logout.go
    │   ├── route.go
    │   ├── signup.go
    ├── game
    │   ├── gameroom.go
    │   ├── handlers.go
    │   ├── matchmaking.go
    │   ├── player.go
    │   ├── tickers.go
    └── shared
        ├── authorization
        ├── configs
        ├── database
        └── logger`
    },
    {
        type: 'simple',
        title: 'SOLID Design Principles',
        content: 'Applying software architecture best practices to manage complexity.'
    },
    {
        type: 'code',
        language: "go",
        title: 'Single Responsibility Principle',
        description: ['Separating concerns into distinct files.', 'player.go ONLY handles connection.', 'gameroom.go ONLY handles state.'],
        code: `// player.go - ONLY handles player connection lifecycle
type Player struct { /* Attributes */ }
func (player *Player) ReadPump() { /* ... */ }
func (player *Player) WritePump() { /* ... */ }

// gameroom.go - ONLY handles game state & rules
type GameRoom struct { /* Attributes */ }
func (gameroom *GameRoom) transitionToDrawing() { /* ... */ }

// matchmaking.go - ONLY handles room assignment
type MatchmakingQueue struct { /* Attributes */ }
func (mq *MatchmakingQueue) assignPlayer(p *Player) { /* ... */ }
// ...
`
    },
    {
        type: 'code',
        language: "go",
        title: 'Open Closed Principle',
        description: ['Open for extension, closed for modification.', 'Adding new message types doesn\'t break existing switch cases.'],
        code: `func (player *Player) ReadPump() {
  switch messageType {
    case SERIAL_MESSAGE:
       // Handle chat
    case SERIAL_WORD_CHOICE:
       // Handle word selection
    case SERIAL_DRAWING:
       // Handle drawing
    
    // EASY TO ADD NEW TYPES!
    // case SERIAL_CONFIGURATION: 
    //    handle configs
  }
}`
    },
    {
        type: 'code',
        language: "typescript",
        title: 'Liskov Substitution',
        description: ['Objects of a superclass shall be replaceable with objects of its subclasses without breaking the application.'],
        code: `// Interface
export interface IDendePart {}

// Class Implementation
export class Dende {
  public putPart(part: IDendePart): void { ... }
}

// Usage
dende.putPart(new DendePart()) // Class instance
dende.putPart({type: 0, ... }) // Plain object`
    },
    {
        type: 'code',
        language: "typescript",
        title: 'Dependency Inversion',
        description: ['High-level modules should not depend on low-level modules. Both should depend on abstractions.'],
        code: `// Absctraction
export interface IDendePart { /* ... */ }

// Low-level module
export class DendePart implements IDendePart { /* ... */ }

// High-level module
export class Dende {
    public putPart(part: IDendePart) { /* ... */ }
    emitPart(p: IDendePart) { /* ... */ }
}`
    },
    {
        type: 'simple',
        title: 'Design Patterns',
        content: 'Strategic solutions to common problems.'
    },
    {
        type: 'code',
        language: "typescript",
        title: 'Observer Pattern',
        description: ['Used for event handling and notifying parts of the system about changes.'],
        code: `class Dende {
  public addPartListener(cb: (p: IDendePart) => any) {
    this.partListeners.push(cb);
  }
}`
    },
    {
        type: 'code',
        language: "typescript",
        title: 'Factory Pattern',
        description: ['Creating objects without specifying the exact class of object that will be created.'],
        code: `export class DendePart implements IDendePart {
    // ...
    static fillWithColorAtPoint(rgba: RGBA, x: number, y: number) {
        const p = new DendePart();
        // ...
        return p;
    }
    static Undo(): IDendePart {
        const p = new DendePart();
        // ...
        return p;
    }
    // ...
}`
    },
    {
        type: 'code',
        language: "go",
        title: 'Singleton Pattern',
        description: ['Ensuring a class has only one instance and providing a global access point.', 'Used here for the matchmaking queue.'],
        code: `type MatchmakingQueue struct {
    heap   []*GameRoom
    locker sync.RWMutex
}

// Global instance
var matchmaking MatchmakingQueue`
    },
    {
        type: 'code',
        title: 'Facade Pattern',
        language: "typescript",
        description: ['Providing a simplified interface to a complex subsystem.', 'Hides the complex canvas context and setup.'],
        code: `export class Dende {
  // Hides complex canvas/undo stack logic
  private ctx: CanvasRenderingContext2D;
  private undoStack: Array<ImageData> = [];

  // Simple interface for the outside world
  public clear() { /*...*/ }
  public undo() { /*...*/ }
}`
    },
    {
        type: 'code',
        title: 'State Machine Pattern',
        language: "go",
        description: ['Managing the game flow through distinct states.'],
        code: `type GameRoom struct { /*...*/ }

func (g *GameRoom) transitionToChoosingWord() { /*...*/ }

func (g *GameRoom) transitionToDrawing() { /*...*/ }

func (g *GameRoom) transitionToTurnSummary() { /*...*/ }

func (g *GameRoom) transitionToNextRound() { /*...*/ }`
    },
    {
        type: 'code',
        title: 'Testing',
        language: "typescript",
        description: ['Verifying core logic like password hashing.'],
        code: `func TestVerifyHash(t *testing.T) {
  password := hashPassword("password1")
  
  // Verify correct password
  ok := verifyPassword("password1", password)
  if !ok { t.Errorf("Verification failed") }

  // Verify wrong password
  ok = verifyPassword("password2", password)
  if ok { t.Errorf("Should have failed") }
}`
    },
    {
        type: 'list',
        title: 'Development Tools',
        items: [
            'Git & GitHub',
            'Docker & Docker Compose',
            'VS Code',
            'Postman / Insomnia'
        ]
    },
    {
        type: 'list',
        title: 'Deployment',
        items: [
            'Custom Domain',
            'Frontend: Cloudflare Pages (https://rakaoran.dev)',
            'Backend: VPS (https://api.rakaoran.dev)'
        ]
    },
    {
        type: 'list',
        title: 'Security Measures',
        items: [
            'XSS & CSRF Protection',
            'SQL Injection Prevention',
            'Spam & Rate Limiting',
            'Bruteforce prevention',
            'Packet Sniffing (Encrypted Traffic)',
            'Cookie Tampering prevention',
            'Websocket Hijacking prevention',
            'CDN Poisoning safeguards'
        ]
    },
    {
        type: 'list',
        title: 'Challenges Faced',
        items: [
            'Complex Game State Transitions',
            'Thread (Un)safety',
            'Handling "Zombie" players (ungraceful disconnections)',
            'Real-time Drawing synchronization'
        ]
    },
    {
        type: 'list',
        title: 'What We Learned',
        items: [
            'Importance of Design Patterns & Principles',
            'Multithreading',
            'Web Security best practices',
            'New programming language (Golang)',
            'API development',
            'Browser behavior',
            'Real-time communication',
            'Deployment (DNS, Reverse Proxy, SSL, IP Adresses, CDN, HTTP and HTTPS, Containerization, Azure VPS)'
        ]
    },
    {
        type: 'simple',
        title: 'Summary',
        content: 'Thank you for listening!'
    }
];

// --- LOGIC ---

const containerRef = ref(null);
const isFullscreen = ref(false);

const highlightedCode = computed(() => {
  const slide = currentSlide.value;
  if (slide.type !== 'code' || !slide.code) return '';
  if (slide.language) {
    try { return hljs.highlight(slide.code, { language: slide.language }).value; } 
    catch (e) { return slide.code; }
  }
  return hljs.highlightAuto(slide.code).value;
});

const currentSlide = computed(() => slides[currentSlideIndex.value]);
const progressPercentage = computed(() => ((currentSlideIndex.value + 1) / slides.length) * 100);

const nextSlide = () => {
    if (currentSlideIndex.value < slides.length - 1) currentSlideIndex.value++;
};

const prevSlide = () => {
    if (currentSlideIndex.value > 0) currentSlideIndex.value--;
};

const toggleFullscreen = () => {
    if (!document.fullscreenElement) {
        containerRef.value?.requestFullscreen().catch(err => {
            console.error(`Error attempting to enable fullscreen: ${err.message}`);
        });
    } else {
        document.exitFullscreen();
    }
};

const onFullscreenChange = () => {
    isFullscreen.value = !!document.fullscreenElement;
};

// --- Text Resize Controls ---
const increaseText = () => textSizeOffset.value++;
const decreaseText = () => textSizeOffset.value--;
const resetText = () => textSizeOffset.value = 0;

const handleKeydown = (e) => {
    if (e.key === 'ArrowRight' || e.key === 'Space') nextSlide();
    if (e.key === 'ArrowLeft') prevSlide();
    
    if (e.key.toLowerCase() === 'f') toggleFullscreen();

    // Text Size
    if (e.key === '+' || e.key === '=') increaseText();
    if (e.key === '-' || e.key === '_') decreaseText();
    if (e.key === '0') resetText();
};

onMounted(() => {
    window.addEventListener('keydown', handleKeydown);
    document.addEventListener('fullscreenchange', onFullscreenChange);
});

onUnmounted(() => {
    window.removeEventListener('keydown', handleKeydown);
    document.removeEventListener('fullscreenchange', onFullscreenChange);
});
</script>

<template>
    <div class="w-full h-full min-h-screen flex items-center justify-center bg-[#0A0A0A] p-4 font-sans text-[#BFC2C7]">
        
        <div ref="containerRef"
            class="bg-[#0A0A0A] overflow-hidden flex flex-col villain-card transition-all duration-300"
            :class="isFullscreen 
                ? 'w-full h-full' 
                : 'w-full max-w-6xl aspect-video border border-[#7A0F13] shadow-[0_0_40px_rgba(193,18,31,0.15)] rounded-sm relative'">

            <div class="h-1.5 bg-[#2C2F33] w-full shrink-0">
                <div class="h-full bg-[#C1121F] shadow-[0_0_10px_#C1121F] transition-all duration-300 ease-out"
                    :style="{ width: progressPercentage + '%' }"></div>
            </div>

            <div class="flex-1 p-12 flex flex-col relative overflow-hidden bg-gradient-to-b from-[#0e0e0e] to-[#0A0A0A]">
                <transition name="fade" mode="out-in">
                    <div :key="currentSlideIndex" class="h-full flex flex-col w-full">

                        <div v-if="currentSlide.type === 'title'"
                            class="h-full flex flex-col justify-center items-center text-center">
                            
                            <h1 class="font-bold text-[#C1121F] mb-6 tracking-tight drop-shadow-md uppercase font-mono transition-all duration-300"
                                :class="tSize(isFullscreen ? 'text-9xl' : 'text-7xl')">
                                {{ currentSlide.title }}
                            </h1>
                            <h2 class="text-[#D4AF37] mb-12 font-light tracking-widest uppercase border-b border-[#7A0F13] pb-2 transition-all duration-300"
                                :class="tSize(isFullscreen ? 'text-4xl' : 'text-2xl')">
                                {{ currentSlide.subtitle }}
                            </h2>
                            <div class="flex gap-6">
                                <span v-for="dev in currentSlide.developers" :key="dev"
                                    class="px-6 py-2 bg-[#1a1a1a] border border-[#7A0F13] text-[#BFC2C7] rounded hover:bg-[#7A0F13] hover:text-white transition-all duration-300 cursor-default"
                                    :class="tSize(isFullscreen ? 'text-2xl' : 'text-base')">
                                    {{ dev }}
                                </span>
                            </div>
                        </div>

                        <div v-else-if="currentSlide.type === 'list'" class="h-full flex flex-col">
                            <h2 class="font-bold text-[#D4AF37] mb-8 border-b border-[#2C2F33] pb-4 uppercase tracking-wider transition-all duration-300"
                                :class="tSize(isFullscreen ? 'text-6xl' : 'text-4xl')">
                                {{ currentSlide.title }}
                            </h2>
                            <ul class="space-y-4 text-[#BFC2C7] flex-1 overflow-y-auto transition-all duration-300"
                                :class="tSize(isFullscreen ? 'text-3xl' : 'text-xl')">
                                <li v-for="(item, idx) in currentSlide.items" :key="idx" class="flex items-start group">
                                    <span class="text-[#C1121F] mr-4 transition-transform group-hover:translate-x-1">➤</span>
                                    <span v-html="item" class="group-hover:text-white transition-colors"></span>
                                </li>
                            </ul>
                            <div v-if="currentSlide.quote"
                                class="mt-8 p-6 bg-[#0f0f0f] border-l-4 border-[#C1121F] italic text-[#BFC2C7] rounded-r shadow-lg transition-all duration-300"
                                :class="tSize(isFullscreen ? 'text-2xl' : 'text-base')">
                                <span class="text-[#7A0F13] mr-2" :class="tSize('text-2xl')">"</span>
                                {{ currentSlide.quote.text }}
                                <span class="text-[#7A0F13] ml-1" :class="tSize('text-2xl')">"</span>
                                <div class="mt-3 font-bold not-italic text-[#D4AF37] uppercase tracking-wide flex items-center gap-2"
                                     :class="tSize(isFullscreen ? 'text-lg' : 'text-sm')">
                                    <span class="h-[1px] w-8 bg-[#D4AF37]"></span>
                                    {{ currentSlide.quote.author }}
                                </div>
                            </div>
                        </div>

                        <div v-else-if="currentSlide.type === 'code'" class="h-full flex flex-col">
                            <div class="flex justify-between items-end border-b border-[#2C2F33] pb-4 mb-6">
                                <h2 class="font-bold text-[#D4AF37] uppercase tracking-wide transition-all duration-300"
                                    :class="tSize(isFullscreen ? 'text-5xl' : 'text-3xl')">
                                    {{ currentSlide.title }}
                                </h2>
                                <span class="text-[#7A0F13] font-mono border border-[#7A0F13] px-2 py-1 rounded"
                                    :class="tSize(isFullscreen ? 'text-base' : 'text-xs')">
                                    LANG: {{ currentSlide.language || 'AUTO' }}
                                </span>
                            </div>
                            
                            <div class="flex-1 overflow-hidden flex flex-col md:flex-row gap-8">
                                <div class="md:w-5/12 overflow-y-auto text-[#BFC2C7] space-y-4 pr-2 transition-all duration-300"
                                     :class="tSize(isFullscreen ? 'text-2xl' : 'text-lg')">
                                    <p v-for="(desc, idx) in currentSlide.description" :key="idx" 
                                       class="leading-relaxed border-l-2 border-[#2C2F33] pl-4 hover:border-[#C1121F] transition-colors">
                                       {{ desc }}
                                    </p>
                                </div>

                                <div class="md:w-7/12 bg-[#1e2127] rounded border border-[#2C2F33] p-1 overflow-hidden shadow-inner flex flex-col">
                                    <div class="bg-[#15171b] px-3 py-2 flex gap-2 border-b border-[#2C2F33]">
                                        <div class="w-3 h-3 rounded-full bg-[#C1121F]"></div>
                                        <div class="w-3 h-3 rounded-full bg-[#D4AF37]"></div>
                                        <div class="w-3 h-3 rounded-full bg-[#2C2F33]"></div>
                                    </div>
                                    <div class="overflow-auto code-scroll flex-1 p-4">
                                        <pre class="hljs bg-transparent font-mono whitespace-pre !p-0 transition-all duration-300"
                                            :class="tSize(isFullscreen ? 'text-lg' : 'text-sm')"
                                            v-html="highlightedCode"></pre>
                                    </div>
                                </div>
                            </div>
                        </div>

                        <div v-else-if="currentSlide.type === 'image'" class="h-full flex flex-col">
                            <h2 class="font-bold text-[#D4AF37] mb-6 border-b border-[#2C2F33] pb-4 uppercase transition-all duration-300"
                                :class="tSize(isFullscreen ? 'text-5xl' : 'text-3xl')">
                                {{ currentSlide.title }}
                            </h2>
                            <div class="flex-1 flex items-center justify-center bg-[#050505] rounded border border-[#2C2F33] relative group">
                                <div class="absolute top-0 left-0 w-4 h-4 border-t-2 border-l-2 border-[#C1121F]"></div>
                                <div class="absolute bottom-0 right-0 w-4 h-4 border-b-2 border-r-2 border-[#C1121F]"></div>
                                
                                <div class="text-center w-full h-full flex items-center justify-center p-8">
                                    <img :src="currentSlide.imageSrc" :alt="currentSlide.title"
                                        class="max-h-[50vh] max-w-full object-contain shadow-2xl rounded-sm border border-[#2C2F33] group-hover:scale-[1.02] transition-transform duration-500">
                                </div>
                            </div>
                        </div>

                        <div v-else class="h-full flex flex-col justify-center items-center text-center p-12">
                            <h2 class="font-bold text-[#C1121F] mb-12 uppercase tracking-tight drop-shadow-[0_2px_10px_rgba(193,18,31,0.5)] transition-all duration-300"
                                :class="tSize(isFullscreen ? 'text-8xl' : 'text-6xl')">
                                {{ currentSlide.title }}
                            </h2>
                            <p v-if="currentSlide.content" class="text-[#BFC2C7] max-w-4xl font-light leading-relaxed border-t border-b border-[#2C2F33] py-8 transition-all duration-300"
                               :class="tSize(isFullscreen ? 'text-5xl' : 'text-3xl')">
                                {{ currentSlide.content }}
                            </p>
                        </div>

                    </div>
                </transition>
            </div>

            <div class="bg-[#0f0f0f] border-t border-[#2C2F33] p-4 flex justify-between items-center text-[#757575] text-sm font-mono shrink-0">
                <div class="flex items-center gap-2">
                    <span class="w-2 h-2 bg-[#C1121F] rounded-full animate-pulse"></span>
                    <span class="uppercase tracking-widest text-[#BFC2C7]">GTO // SYSTEM</span>
                </div>
                
                <div class="flex gap-4 items-center">
                    
                    <div class="flex items-center gap-2 border-r border-[#2C2F33] pr-4 mr-2" v-if="textSizeOffset !== 0">
                        <button @click="resetText" class="text-[#C1121F] hover:text-white transition-colors" title="Reset Text Size (0)">
                            TEXT SIZE {{ textSizeOffset > 0 ? '+' : '' }}{{ textSizeOffset }}
                        </button>
                    </div>

                    <button @click="toggleFullscreen" 
                        class="p-2 rounded hover:text-[#D4AF37] transition-colors duration-200" title="Toggle Fullscreen (F)">
                        <svg v-if="!isFullscreen" xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M8 3H5a2 2 0 0 0-2 2v3"/><path d="M21 8V5a2 2 0 0 0-2-2h-3"/><path d="M3 16v3a2 2 0 0 0 2 2h3"/><path d="M16 21h3a2 2 0 0 0 2-2v-3"/></svg>
                        <svg v-else xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M8 3v3a2 2 0 0 1-2 2H3"/><path d="M21 8h-3a2 2 0 0 1-2-2V3"/><path d="M3 16h3a2 2 0 0 1 2 2v3"/><path d="M16 21v-3a2 2 0 0 1 2-2h3"/></svg>
                    </button>

                    <button @click="prevSlide" :disabled="currentSlideIndex === 0"
                        class="p-2 rounded hover:text-[#D4AF37] disabled:opacity-20 disabled:cursor-not-allowed transition-colors duration-200">
                        <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none"
                            stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                            <path d="m15 18-6-6 6-6" />
                        </svg>
                    </button>
                    
                    <span class="text-[#D4AF37]">{{ currentSlideIndex + 1 }} <span class="text-[#2C2F33]">/</span> {{ slides.length }}</span>
                    
                    <button @click="nextSlide" :disabled="currentSlideIndex === slides.length - 1"
                        class="p-2 rounded hover:text-[#D4AF37] disabled:opacity-20 disabled:cursor-not-allowed transition-colors duration-200">
                        <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none"
                            stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                            <path d="m9 18 6-6-6-6" />
                        </svg>
                    </button>
                </div>
            </div>

        </div>
    </div>
</template>

<style scoped>
.code-scroll::-webkit-scrollbar {
    height: 8px;
    width: 8px;
}
.code-scroll::-webkit-scrollbar-track {
    background: #1e2127; 
}
.code-scroll::-webkit-scrollbar-thumb {
    background: #2C2F33;
    border-radius: 2px;
}
.code-scroll::-webkit-scrollbar-thumb:hover {
    background: #7A0F13; 
}

.hljs {
    background: transparent !important;
}

.fade-enter-active,
.fade-leave-active {
    transition: all 0.3s ease;
}

.fade-enter-from {
    opacity: 0;
    transform: translateY(10px);
}
.fade-leave-to {
    opacity: 0;
    transform: translateY(-10px);
}

.villain-card {
    background-image: linear-gradient(rgba(18, 16, 16, 0) 50%, rgba(0, 0, 0, 0.25) 50%), linear-gradient(90deg, rgba(255, 0, 0, 0.06), rgba(0, 255, 0, 0.02), rgba(0, 0, 255, 0.06));
    background-size: 100% 2px, 3px 100%;
}
</style>
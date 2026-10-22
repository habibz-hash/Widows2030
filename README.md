<!DOCTYPE html>
<html lang="fa" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>پایگاه حقیقت علمی | Archive Pro</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://cdn.jsdelivr.net/gh/rastikerdar/vazirmatn@v33.003/Vazirmatn-font-face.css" rel="stylesheet" type="text/css" />
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <link rel="stylesheet" href="style.css">
    <script src="https://cdn.jsdelivr.net/npm/ethers@5.7.2/dist/ethers.umd.min.js"></script>
</head>
<body class="bg-slate-950 text-slate-200 min-h-screen flex flex-col">

    <!-- Header -->
    <header class="sticky top-0 z-50 bg-slate-900/90 backdrop-blur-md border-b border-slate-800 px-4 py-3 flex items-center justify-between">
        <div class="flex items-center gap-3">
            <div class="bg-indigo-600 p-2 rounded-xl text-white"><i class="fas fa-atom"></i></div>
            <div>
                <h1 class="font-bold text-sm">پایگاه حقیقت علمی</h1>
                <span class="text-[10px] text-slate-500">Archive Pro Web3</span>
            </div>
        </div>
        <button id="btn-wallet" class="bg-indigo-600 hover:bg-indigo-700 px-4 py-2 rounded-xl text-xs font-medium transition-all">
            <i class="fas fa-wallet ml-1"></i> اتصال کیف پول
        </button>
    </header>

    <!-- Main Content -->
    <main class="flex-1 max-w-lg mx-auto w-full p-4 space-y-6 pb-24">
        
        <!-- Search -->
        <div class="relative">
            <input type="text" id="search-input" placeholder="جستجوی علمی..." class="w-full bg-slate-900 border border-slate-800 rounded-2xl px-4 py-3 text-xs focus:border-indigo-500 outline-none">
            <i class="fas fa-search absolute top-3.5 right-3.5 text-slate-500"></i>
        </div>

        <!-- Upload Hub -->
        <section id="upload-section" class="bg-slate-900 border border-slate-800 rounded-2xl p-4 shadow-xl">
            <h2 class="text-xs font-bold mb-3 text-slate-400"><i class="fas fa-upload ml-2"></i>انتشار محتوای غیرمتمرکز</h2>
            <form id="upload-form" class="space-y-3">
                <input type="text" id="upload-title" placeholder="عنوان اثر علمی..." required class="w-full bg-slate-950 border border-slate-800 rounded-xl px-3 py-2 text-xs">
                <input type="file" id="upload-file" accept="video/*" required class="text-[10px] w-full">
                <button type="submit" class="w-full bg-indigo-600 py-2 rounded-xl text-xs font-bold">آپلود به IPFS & Blockchain</button>
            </form>
        </section>

        <!-- Video Feed (Reels Style) -->
        <section id="video-feed" class="space-y-6">
            <!-- Videos will be injected here by app.js -->
        </section>

        <!-- NFT Market Preview -->
        <section id="nft-market" class="bg-slate-900 border border-slate-800 rounded-2xl p-4">
            <h2 class="text-xs font-bold mb-3 text-slate-400"><i class="fas fa-shopping-cart ml-2"></i>بازار NFT علمی</h2>
            <div id="nft-grid" class="grid grid-cols-2 gap-3">
                <!-- NFTs injected here -->
            </div>
        </section>
    </main>

    <!-- Bottom Nav -->
    <nav class="fixed bottom-0 left-0 right-0 bg-slate-900 border-t border-slate-800 py-3 flex justify-around items-center z-50 max-w-lg mx-auto">
        <button class="text-indigo-500 flex flex-col items-center text-[10px]"><i class="fas fa-home text-lg"></i>فید</button>
        <button class="text-slate-500 flex flex-col items-center text-[10px]"><i class="fas fa-search text-lg"></i>جستجو</button>
        <button class="text-slate-500 flex flex-col items-center text-[10px]"><i class="fas fa-wallet text-lg"></i>کیف پول</button>
        <button class="text-slate-500 flex flex-col items-center text-[10px]"><i class="fas fa-user text-lg"></i>پروفایل</button>
    </nav>

    <!-- Scripts -->
    <script src="config.js"></script>
    <script src="web3-core.js"></script>
    <script src="app.js"></script>
</body>
</html>
body {
    font-family: 'Vazirmatn', sans-serif;
    -webkit-tap-highlight-color: transparent;
}

/* Glassmorphism effect */
.glass {
    background: rgba(15, 23, 42, 0.7);
    backdrop-filter: blur(12px);
    -webkit-backdrop-filter: blur(12px);
    border: 1px solid rgba(255, 255, 255, 0.1);
}

/* Hide scrollbar but allow scrolling */
.no-scrollbar::-webkit-scrollbar {
    display: none;
}

/* Video container aspect ratio */
.video-container {
    position: relative;
    width: 100%;
    aspect-ratio: 9 / 16;
    background: #000;
    border-radius: 1rem;
    overflow: hidden;
}

video {
    width: 100%;
    height: 100%;
    object-fit: cover;
}

/* Prevent Text Selection */
body {
    user-select: none;
    -webkit-user-select: none;
}
const CONFIG = {
    // اطلاعات اتصال به IPFS (Pinata)
    PINATA_JWT: "YOUR_PINATA_JWT_HERE", 
    PINATA_GATEWAY: "https://gateway.pinata.cloud/ipfs/",

    // آدرس قرارداد هوشمند شما (بعد از Deploy شدن)
    CONTRACT_ADDRESS: "0xYourContractAddressHere",

    // تنظیمات شبکه (مثلاً Polygon یا Ethereum)
    NETWORK_ID: "0x1", // 0x1 برای اتریوم اصلی، 0x89 برای پلی‌گان
    
    // تنظیمات امنیتی
    SECURITY: {
        BLOCK_INSPECT: true,
        BLOCK_RIGHT_CLICK: true
    }
};
/**
 * WEB3 CORE MODULE
 */
class Web3Engine {
    constructor() {
        this.provider = null;
        this.signer = null;
        this.account = null;
    }

    async connect() {
        if (!window.ethereum) throw new Error("MetaMask not found!");
        
        this.provider = new ethers.providers.Web3Provider(window.ethereum);
        const accounts = await this.provider.send("eth_requestAccounts", []);
        this.account = accounts[0];
        this.signer = this.provider.getSigner();
        
        return this.account;
    }

    async uploadToIPFS(file) {
        const formData = new FormData();
        formData.append('file', file);

        const response = await fetch("https://api.pinata.cloud/pinning/pinFileToIPFS", {
            method: 'POST',
            headers: {
                'Authorization': `Bearer ${CONFIG.PINATA_JWT}`
            },
            body: formData
        });

        const data = await response.json();
        return data.IpfsHash ? CONFIG.PINATA_GATEWAY + data.IpfsHash : null;
    }
}

const web3 = new Web3Engine();

/**
 * APPLICATION LOGIC
 */
document.addEventListener('DOMContentLoaded', () => {
    initApp();
    setupSecurity();
});

async function initApp() {
    const walletBtn = document.getElementById('btn-wallet');
    const uploadForm = document.getElementById('upload-form');
    const videoFeed = document.getElementById('video-feed');

    // Wallet Connection Logic
    walletBtn.addEventListener('click', async () => {
        try {
            const account = await web3.connect();
            walletBtn.innerHTML = `<i class="fas fa-check-circle ml-1"></i> ${account.substring(0,6)}...`;
            walletBtn.classList.replace('bg-indigo-600', 'bg-green-600');
            console.log("Connected to:", account);
        } catch (err) {
            alert("خطا در اتصال: " + err.message);
        }
    });

    // IPFS Upload Logic
    uploadForm.addEventListener('submit', async (e) => {
        e.preventDefault();
        if (!web3.account) {
            alert("لطفاً ابتدا کیف پول خود را متصل کنید.");
            return;
        }

        const file = document.getElementById('upload-file').files[0];
        const title = document.getElementById('upload-title').value;

        try {
            const ipfsUrl = await web3.uploadToIPFS(file);
            if (ipfsUrl) {
                addVideoToFeed(title, ipfsUrl);
                uploadForm.reset();
                alert("محتوا با موفقیت در IPFS ثبت شد!");
            }
        } catch (err) {
            console.error(err);
            alert("خطا در عملیات آپلود.");
        }
    });
}

function addVideoToFeed(title, url) {
    const feed = document.getElementById('video-feed');
    const card = document.createElement('div');
    card.className = "bg-slate-900 border border-slate-800 rounded-2xl overflow-hidden shadow-xl";
    card.innerHTML = `
        <div class="p-3 flex items-center justify-between border-b border-slate-800/60">
            <div class="flex items-center gap-2">
                <div class="w-7 h-7 rounded-full bg-indigo-500 flex items-center justify-center text-[10px]">USER</div>
                <span class="text-xs text-slate-400">New Content</span>
            </div>
        </div>
        <div class="video-container">
            <video controls controlsList="nodownload">
                <source src="${url}" type="video/mp4">
            </video>
        </div>
        <div class="p-3">
            <h3 class="font-bold text-xs text-white">${title}</h3>
            <p class="text-[10px] text-indigo-400 mt-1">Verified via IPFS</p>
        </div>
    `;
    feed.prepend(card);
}

function setupSecurity() {
    // Block Right Click
    document.addEventListener('contextmenu', e => e.preventDefault());

    // Block DevTools Keys
    document.addEventListener('keydown', e => {
        if (e.key === 'F12' || (e.ctrlKey && e.shiftKey && (e.key === 'I' || e.key === 'J' || e.key === 'C')) || (e.ctrlKey && e.key === 'U')) {
            e.preventDefault();
            return false;

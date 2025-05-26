<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>StoryFlip - Interactive Story Books</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/pdf.js/2.12.313/pdf.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/turn.js/4.1.1/turn.min.js"></script>
    <script src="https://kit.fontawesome.com/a076d05399.js" crossorigin="anonymous"></script>
    <link href="https://fonts.googleapis.com/css2?family=Playfair+Display:wght@400;700&family=Montserrat:wght@300;400;600&family=Alex+Brush&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Montserrat', sans-serif;
            background-color: #f9f5f0;
        }
        
        .arabesque-bg {
            background-image: url('data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" width="100" height="100" viewBox="0 0 100 100"><path fill="%23d10000" opacity="0.1" d="M30,50 Q50,30 70,50 Q50,70 30,50"/></svg>');
            background-size: 200px;
            background-repeat: repeat;
        }
        
        .ornamental-border {
            border: 2px solid #d10000;
            position: relative;
        }
        
        .ornamental-border:before, .ornamental-border:after {
            content: "";
            position: absolute;
            width: 20px;
            height: 20px;
            border: 2px solid #d10000;
        }
        
        .ornamental-border:before {
            top: -2px;
            left: -2px;
            border-right: none;
            border-bottom: none;
        }
        
        .ornamental-border:after {
            bottom: -2px;
            right: -2px;
            border-left: none;
            border-top: none;
        }
        
        #flipbook {
            width: 800px;
            height: 600px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.3);
            background-color: #fff;
            position: relative;
        }
        
        #flipbook .page {
            background-color: #fff;
            color: #333;
            padding: 40px;
            display: flex;
            justify-content: center;
            align-items: center;
            position: relative;
            overflow: hidden;
        }
        
        .page-audio-control {
            position: absolute;
            bottom: 20px;
            right: 20px;
            background: #d10000;
            color: white;
            width: 40px;
            height: 40px;
            border-radius: 50%;
            display: flex;
            justify-content: center;
            align-items: center;
            cursor: pointer;
            z-index: 10;
            box-shadow: 0 2px 5px rgba(0,0,0,0.2);
            transition: all 0.3s;
        }
        
        .page-audio-control:hover {
            transform: scale(1.1);
            background: #a80000;
        }
        
        .page-number {
            position: absolute;
            bottom: 20px;
            left: 20px;
            font-size: 14px;
            color: #888;
            font-family: 'Playfair Display', serif;
        }
        
        .upload-area {
            border: 2px dashed #d10000;
            transition: all 0.3s;
            background-color: rgba(255,255,255,0.8);
            position: relative;
            overflow: hidden;
        }
        
        .upload-area:hover {
            border-color: #a80000;
            background-color: rgba(255,255,255,0.9);
        }
        
        .progress-bar {
            transition: width 0.3s ease;
        }
        
        .tooltip {
            position: relative;
            display: inline-block;
        }
        
        .tooltip .tooltiptext {
            visibility: hidden;
            width: 200px;
            background-color: #333;
            color: #fff;
            text-align: center;
            border-radius: 6px;
            padding: 10px;
            position: absolute;
            z-index: 1;
            bottom: 125%;
            left: 50%;
            margin-left: -100px;
            opacity: 0;
            transition: opacity 0.3s;
            font-family: 'Montserrat', sans-serif;
            font-size: 14px;
        }
        
        .tooltip:hover .tooltiptext {
            visibility: visible;
            opacity: 1;
        }
        
        .arabesque-corner {
            position: absolute;
            width: 50px;
            height: 50px;
            opacity: 0.1;
        }
        
        .arabesque-corner.tl {
            top: 0;
            left: 0;
            border-top: 3px solid #d10000;
            border-left: 3px solid #d10000;
        }
        
        .arabesque-corner.tr {
            top: 0;
            right: 0;
            border-top: 3px solid #d10000;
            border-right: 3px solid #d10000;
        }
        
        .arabesque-corner.bl {
            bottom: 0;
            left: 0;
            border-bottom: 3px solid #d10000;
            border-left: 3px solid #d10000;
        }
        
        .arabesque-corner.br {
            bottom: 0;
            right: 0;
            border-bottom: 3px solid #d10000;
            border-right: 3px solid #d10000;
        }
        
        .ornamental-divider {
            height: 2px;
            background: linear-gradient(90deg, transparent, #d10000, transparent);
            margin: 20px 0;
            position: relative;
        }
        
        .ornamental-divider:before, .ornamental-divider:after {
            content: "❖";
            position: absolute;
            top: -10px;
            color: #d10000;
            font-size: 20px;
        }
        
        .ornamental-divider:before {
            left: 0;
        }
        
        .ornamental-divider:after {
            right: 0;
        }
        
        .fancy-title {
            font-family: 'Playfair Display', serif;
            position: relative;
            display: inline-block;
        }
        
        .fancy-title:after {
            content: "";
            position: absolute;
            bottom: -5px;
            left: 0;
            width: 100%;
            height: 2px;
            background: linear-gradient(90deg, #d10000, transparent);
        }
        
        .arabesque-pattern {
            position: absolute;
            width: 100%;
            height: 100%;
            background-image: url('data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" width="100" height="100" viewBox="0 0 100 100"><path fill="none" stroke="%23d10000" stroke-width="0.5" opacity="0.1" d="M20,20 Q50,10 80,20 Q90,50 80,80 Q50,90 20,80 Q10,50 20,20"/></svg>');
            background-size: 100px;
            background-repeat: repeat;
            pointer-events: none;
        }
        
        .elegant-btn {
            font-family: 'Playfair Display', serif;
            letter-spacing: 1px;
            position: relative;
            overflow: hidden;
            transition: all 0.3s;
        }
        
        .elegant-btn:before {
            content: "";
            position: absolute;
            top: 0;
            left: -100%;
            width: 100%;
            height: 100%;
            background: rgba(255,255,255,0.1);
            transition: all 0.3s;
        }
        
        .elegant-btn:hover:before {
            left: 0;
        }
    </style>
</head>
<body class="arabesque-bg">
    <header class="bg-black text-white shadow-lg relative overflow-hidden">
        <div class="arabesque-pattern"></div>
        <div class="container mx-auto px-4 py-6 relative z-10">
            <div class="flex justify-between items-center">
                <h1 class="text-4xl font-bold" style="font-family: 'Alex Brush', cursive;">StoryFlip</h1>
                <nav>
                    <ul class="flex space-x-8">
                        <li><a href="#" class="hover:text-red-400 transition">Home</a></li>
                        <li><a href="#" class="hover:text-red-400 transition">My Books</a></li>
                        <li><a href="#" class="hover:text-red-400 transition">Templates</a></li>
                        <li><a href="#" class="hover:text-red-400 transition">Help</a></li>
                    </ul>
                </nav>
            </div>
        </div>
    </header>

    <main class="container mx-auto px-4 py-12">
        <div class="text-center mb-16">
            <h2 class="text-5xl font-bold text-black mb-6" style="font-family: 'Playfair Display', serif;">Turn Your PDF Stories into<br><span class="text-red-600">Interactive Flip Books</span></h2>
            <div class="max-w-3xl mx-auto">
                <p class="text-xl text-gray-700 mb-6">Upload your story PDF, customize it with page-turning effects, and add soundtracks to create an immersive reading experience.</p>
                <div class="ornamental-divider max-w-md mx-auto"></div>
            </div>
        </div>

        <div class="bg-white rounded-lg shadow-2xl p-10 mb-16 relative ornamental-border">
            <div class="arabesque-corner tl"></div>
            <div class="arabesque-corner tr"></div>
            <div class="arabesque-corner bl"></div>
            <div class="arabesque-corner br"></div>
            
            <div class="flex flex-col md:flex-row gap-10">
                <div class="w-full md:w-1/2">
                    <h3 class="text-3xl font-semibold text-black mb-8 fancy-title">Upload Your Story</h3>
                    
                    <div id="upload-section" class="mb-10">
                        <div class="upload-area rounded-lg p-8 text-center cursor-pointer mb-6" id="drop-area">
                            <input type="file" id="pdf-upload" accept=".pdf" class="hidden">
                            <div class="flex flex-col items-center justify-center py-12 relative">
                                <div class="w-24 h-24 bg-red-100 rounded-full flex items-center justify-center mb-6">
                                    <i class="fas fa-cloud-upload-alt text-4xl text-red-600"></i>
                                </div>
                                <p class="text-gray-700 mb-3 text-lg">Drag & drop your PDF file here</p>
                                <p class="text-sm text-gray-500 mb-4">or</p>
                                <button id="upload-btn" class="elegant-btn bg-red-600 text-white px-8 py-3 rounded-lg hover:bg-red-700 transition text-lg">Select PDF File</button>
                            </div>
                        </div>
                        
                        <div id="upload-progress" class="hidden">
                            <div class="flex justify-between mb-2">
                                <span class="text-md font-medium text-gray-700">Uploading...</span>
                                <span class="text-md font-medium text-gray-700" id="progress-percent">0%</span>
                            </div>
                            <div class="w-full bg-gray-200 rounded-full h-3">
                                <div id="progress-bar" class="bg-red-600 h-3 rounded-full progress-bar" style="width: 0%"></div>
                            </div>
                        </div>
                    </div>
                    
                    <div id="audio-controls" class="hidden">
                        <h4 class="text-2xl font-medium text-black mb-6 fancy-title">Add Soundtrack to Pages</h4>
                        <div class="space-y-6">
                            <div class="flex items-center">
                                <label class="w-32 text-gray-700 font-medium">Background Music:</label>
                                <input type="file" id="bg-music" accept="audio/*" class="hidden">
                                <button id="bg-music-btn" class="elegant-btn bg-gray-200 text-gray-800 px-6 py-2 rounded hover:bg-gray-300 transition">Select File</button>
                                <span id="bg-music-name" class="ml-3 text-gray-600">No file selected</span>
                            </div>
                            
                            <div class="flex items-center">
                                <label class="w-32 text-gray-700 font-medium">Volume:</label>
                                <input type="range" id="bg-volume" min="0" max="1" step="0.1" value="0.5" class="w-full accent-red-600">
                            </div>
                            
                            <div class="pt-6 border-t border-gray-200">
                                <h5 class="text-xl font-medium text-black mb-3 fancy-title">Page-specific Sounds</h5>
                                <p class="text-gray-600 mb-4">Select a page and add sound effects that play when the page is opened.</p>
                                <div class="flex items-center">
                                    <select id="page-select" class="border rounded px-4 py-2 mr-3 bg-white text-gray-800" disabled>
                                        <option value="">Select a page</option>
                                    </select>
                                    <input type="file" id="page-sound" accept="audio/*" class="hidden">
                                    <button id="page-sound-btn" class="elegant-btn bg-gray-200 text-gray-800 px-6 py-2 rounded hover:bg-gray-300 transition" disabled>Add Sound</button>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
                
                <div class="w-full md:w-1/2">
                    <div id="preview-section" class="hidden">
                        <div class="flex justify-between items-center mb-6">
                            <h3 class="text-3xl font-semibold text-black fancy-title">Book Preview</h3>
                            <div class="flex space-x-3">
                                <button id="prev-page" class="bg-gray-200 p-3 rounded-full hover:bg-gray-300 transition tooltip">
                                    <i class="fas fa-chevron-left text-gray-800"></i>
                                    <span class="tooltiptext">Previous Page</span>
                                </button>
                                <button id="next-page" class="bg-gray-200 p-3 rounded-full hover:bg-gray-300 transition tooltip">
                                    <i class="fas fa-chevron-right text-gray-800"></i>
                                    <span class="tooltiptext">Next Page</span>
                                </button>
                                <button id="fullscreen" class="bg-gray-200 p-3 rounded-full hover:bg-gray-300 transition tooltip">
                                    <i class="fas fa-expand text-gray-800"></i>
                                    <span class="tooltiptext">Fullscreen</span>
                                </button>
                            </div>
                        </div>
                        
                        <div id="flipbook-container" class="flex justify-center mb-8">
                            <div id="flipbook"></div>
                        </div>
                        
                        <div class="mt-8 flex justify-center space-x-6">
                            <button id="save-book" class="elegant-btn bg-red-600 text-white px-8 py-3 rounded-lg hover:bg-red-700 transition flex items-center text-lg">
                                <i class="fas fa-save mr-3"></i> Save Book
                            </button>
                            <button id="share-book" class="elegant-btn bg-black text-white px-8 py-3 rounded-lg hover:bg-gray-800 transition flex items-center text-lg">
                                <i class="fas fa-share-alt mr-3"></i> Share
                            </button>
                        </div>
                    </div>
                    
                    <div id="empty-preview" class="flex flex-col items-center justify-center h-full py-20 bg-gray-100 rounded-lg">
                        <div class="w-24 h-24 bg-red-100 rounded-full flex items-center justify-center mb-6">
                            <i class="fas fa-book-open text-4xl text-red-600"></i>
                        </div>
                        <h4 class="text-2xl text-gray-700 mb-3" style="font-family: 'Playfair Display', serif;">No Book Loaded</h4>
                        <p class="text-gray-600 text-center max-w-md">Upload a PDF file to see it transformed into an interactive flip book with soundtracks.</p>
                    </div>
                </div>
            </div>
        </div>
        
        <div class="bg-white rounded-lg shadow-2xl p-10 mb-16 relative ornamental-border">
            <div class="arabesque-corner tl"></div>
            <div class="arabesque-corner tr"></div>
            <div class="arabesque-corner bl"></div>
            <div class="arabesque-corner br"></div>
            
            <h3 class="text-3xl font-semibold text-black mb-10 text-center fancy-title">How It Works</h3>
            <div class="grid md:grid-cols-3 gap-8">
                <div class="bg-red-50 p-8 rounded-lg relative overflow-hidden">
                    <div class="w-16 h-16 bg-red-200 rounded-full flex items-center justify-center mb-6 mx-auto">
                        <i class="fas fa-upload text-red-600 text-2xl"></i>
                    </div>
                    <h4 class="font-medium text-xl text-black mb-3 text-center" style="font-family: 'Playfair Display', serif;">1. Upload Your PDF</h4>
                    <p class="text-gray-700 text-center">Simply drag and drop your story PDF or select it from your device.</p>
                </div>
                <div class="bg-red-50 p-8 rounded-lg relative overflow-hidden">
                    <div class="w-16 h-16 bg-red-200 rounded-full flex items-center justify-center mb-6 mx-auto">
                        <i class="fas fa-music text-red-600 text-2xl"></i>
                    </div>
                    <h4 class="font-medium text-xl text-black mb-3 text-center" style="font-family: 'Playfair Display', serif;">2. Add Soundtracks</h4>
                    <p class="text-gray-700 text-center">Enhance your story with background music and page-specific sound effects.</p>
                </div>
                <div class="bg-red-50 p-8 rounded-lg relative overflow-hidden">
                    <div class="w-16 h-16 bg-red-200 rounded-full flex items-center justify-center mb-6 mx-auto">
                        <i class="fas fa-share-square text-red-600 text-2xl"></i>
                    </div>
                    <h4 class="font-medium text-xl text-black mb-3 text-center" style="font-family: 'Playfair Display', serif;">3. Share & Enjoy</h4>
                    <p class="text-gray-700 text-center">Save your creation or share it with others to enjoy the immersive experience.</p>
                </div>
            </div>
        </div>
    </main>

    <footer class="bg-black text-white py-12 relative overflow-hidden">
        <div class="arabesque-pattern"></div>
        <div class="container mx-auto px-4 relative z-10">
            <div class="flex flex-col md:flex-row justify-between items-center mb-8">
                <div class="mb-6 md:mb-0">
                    <h2 class="text-3xl font-bold mb-2" style="font-family: 'Alex Brush', cursive;">StoryFlip</h2>
                    <p class="text-gray-400" style="font-family: 'Playfair Display', serif;">Bringing stories to life with sound and motion.</p>
                </div>
                <div class="flex space-x-8">
                    <a href="#" class="hover:text-red-400 transition">Terms</a>
                    <a href="#" class="hover:text-red-400 transition">Privacy</a>
                    <a href="#" class="hover:text-red-400 transition">Contact</a>
                </div>
            </div>
            <div class="border-t border-gray-700 pt-8 text-center text-gray-400">
                <p style="font-family: 'Playfair Display', serif;">&copy; 2023 StoryFlip. All rights reserved.</p>
            </div>
        </div>
    </footer>

    <script>
        // Initialize PDF.js
        pdfjsLib.GlobalWorkerOptions.workerSrc = 'https://cdnjs.cloudflare.com/ajax/libs/pdf.js/2.12.313/pdf.worker.min.js';
        
        // DOM elements
        const uploadBtn = document.getElementById('upload-btn');
        const pdfUpload = document.getElementById('pdf-upload');
        const dropArea = document.getElementById('drop-area');
        const uploadProgress = document.getElementById('upload-progress');
        const progressBar = document.getElementById('progress-bar');
        const progressPercent = document.getElementById('progress-percent');
        const previewSection = document.getElementById('preview-section');
        const emptyPreview = document.getElementById('empty-preview');
        const flipbook = document.getElementById('flipbook');
        const bgMusicBtn = document.getElementById('bg-music-btn');
        const bgMusicInput = document.getElementById('bg-music');
        const bgMusicName = document.getElementById('bg-music-name');
        const bgVolume = document.getElementById('bg-volume');
        const pageSelect = document.getElementById('page-select');
        const pageSoundBtn = document.getElementById('page-sound-btn');
        const pageSoundInput = document.getElementById('page-sound');
        const prevPageBtn = document.getElementById('prev-page');
        const nextPageBtn = document.getElementById('next-page');
        const fullscreenBtn = document.getElementById('fullscreen');
        const saveBookBtn = document.getElementById('save-book');
        const shareBookBtn = document.getElementById('share-book');
        const audioControls = document.getElementById('audio-controls');
        
        // Global variables
        let pdfDoc = null;
        let totalPages = 0;
        let currentPage = 1;
        let bgAudio = null;
        let pageAudios = {};
        
        // Event listeners
        uploadBtn.addEventListener('click', () => pdfUpload.click());
        pdfUpload.addEventListener('change', handleFileSelect);
        
        // Drag and drop events
        ['dragenter', 'dragover', 'dragleave', 'drop'].forEach(eventName => {
            dropArea.addEventListener(eventName, preventDefaults, false);
        });
        
        function preventDefaults(e) {
            e.preventDefault();
            e.stopPropagation();
        }
        
        ['dragenter', 'dragover'].forEach(eventName => {
            dropArea.addEventListener(eventName, highlight, false);
        });
        
        ['dragleave', 'drop'].forEach(eventName => {
            dropArea.addEventListener(eventName, unhighlight, false);
        });
        
        function highlight() {
            dropArea.classList.add('border-red-600', 'bg-red-50');
        }
        
        function unhighlight() {
            dropArea.classList.remove('border-red-600', 'bg-red-50');
        }
        
        dropArea.addEventListener('drop', handleDrop, false);
        
        function handleDrop(e) {
            const dt = e.dataTransfer;
            const files = dt.files;
            
            if (files.length > 0 && files[0].type === 'application/pdf') {
                pdfUpload.files = files;
                handleFileSelect({ target: pdfUpload });
            }
        }
        
        // Handle file selection
        function handleFileSelect(e) {
            const file = e.target.files[0];
            if (!file) return;
            
            // Show upload progress
            uploadProgress.classList.remove('hidden');
            
            // Simulate upload progress (in a real app, this would be actual upload progress)
            let progress = 0;
            const interval = setInterval(() => {
                progress += Math.random() * 10;
                if (progress >= 100) {
                    progress = 100;
                    clearInterval(interval);
                    processPDF(file);
                }
                
                progressBar.style.width = `${progress}%`;
                progressPercent.textContent = `${Math.round(progress)}%`;
            }, 200);
        }
        
        // Process the PDF file
        function processPDF(file) {
            const reader = new FileReader();
            
            reader.onload = function(event) {
                const typedarray = new Uint8Array(event.target.result);
                
                // Load the PDF document
                pdfjsLib.getDocument(typedarray).promise.then(function(pdf) {
                    pdfDoc = pdf;
                    totalPages = pdf.numPages;
                    
                    // Hide upload section, show preview
                    uploadProgress.classList.add('hidden');
                    emptyPreview.classList.add('hidden');
                    previewSection.classList.remove('hidden');
                    audioControls.classList.remove('hidden');
                    
                    // Initialize flipbook
                    initFlipbook();
                    
                    // Populate page select dropdown
                    populatePageSelect();
                    
                }).catch(function(error) {
                    console.error('PDF loading error:', error);
                    alert('Error loading PDF. Please try another file.');
                });
            };
            
            reader.readAsArrayBuffer(file);
        }
        
        // Initialize flipbook
        function initFlipbook() {
            flipbook.innerHTML = '';
            
            // Initialize turn.js flipbook
            $(flipbook).turn({
                width: 800,
                height: 600,
                autoCenter: true,
                display: 'double',
                acceleration: true,
                elevation: 50,
                gradients: true,
                when: {
                    turning: function(e, page) {
                        currentPage = page;
                        
                        // Play page-specific audio if exists
                        if (pageAudios[page]) {
                            pageAudios[page].currentTime = 0;
                            pageAudios[page].play();
                        }
                    }
                }
            });
            
            // Load all pages into flipbook
            for (let i = 1; i <= totalPages; i++) {
                loadPage(i);
            }
        }
        
        // Load a single page into flipbook
        function loadPage(pageNum) {
            pdfDoc.getPage(pageNum).then(function(page) {
                const viewport = page.getViewport({ scale: 1.5 });
                const canvas = document.createElement('canvas');
                const context = canvas.getContext('2d');
                canvas.height = viewport.height;
                canvas.width = viewport.width;
                
                const renderContext = {
                    canvasContext: context,
                    viewport: viewport
                };
                
                page.render(renderContext).promise.then(function() {
                    const pageDiv = document.createElement('div');
                    pageDiv.className = 'page';
                    pageDiv.dataset.page = pageNum;
                    
                    // Add canvas to page
                    pageDiv.appendChild(canvas);
                    
                    // Add page number
                    const pageNumber = document.createElement('div');
                    pageNumber.className = 'page-number';
                    pageNumber.textContent = `Page ${pageNum}`;
                    pageDiv.appendChild(pageNumber);
                    
                    // Add audio control button
                    const audioControl = document.createElement('div');
                    audioControl.className = 'page-audio-control tooltip';
                    audioControl.innerHTML = '<i class="fas fa-music"></i>';
                    audioControl.dataset.page = pageNum;
                    
                    const tooltip = document.createElement('span');
                    tooltip.className = 'tooltiptext';
                    tooltip.textContent = pageAudios[pageNum] ? 'Page sound loaded' : 'Add sound to this page';
                    audioControl.appendChild(tooltip);
                    
                    audioControl.addEventListener('click', function() {
                        pageSelect.value = pageNum;
                        pageSoundBtn.click();
                    });
                    
                    pageDiv.appendChild(audioControl);
                    
                    // Add page to flipbook
                    if (pageNum % 2 === 0) {
                        $(flipbook).turn('addPage', pageDiv, pageNum);
                    } else {
                        $(flipbook).turn('addPage', pageDiv, pageNum);
                    }
                });
            });
        }
        
        // Populate page select dropdown
        function populatePageSelect() {
            pageSelect.innerHTML = '<option value="">Select a page</option>';
            
            for (let i = 1; i <= totalPages; i++) {
                const option = document.createElement('option');
                option.value = i;
                option.textContent = `Page ${i}`;
                pageSelect.appendChild(option);
            }
            
            pageSelect.disabled = false;
            pageSoundBtn.disabled = false;
        }
        
        // Background music controls
        bgMusicBtn.addEventListener('click', () => bgMusicInput.click());
        bgMusicInput.addEventListener('change', function(e) {
            const file = e.target.files[0];
            if (!file) return;
            
            bgMusicName.textContent = file.name;
            
            if (bgAudio) {
                bgAudio.pause();
                URL.revokeObjectURL(bgAudio.src);
            }
            
            const audioUrl = URL.createObjectURL(file);
            bgAudio = new Audio(audioUrl);
            bgAudio.loop = true;
            bgAudio.volume = bgVolume.value;
            bgAudio.play();
        });
        
        bgVolume.addEventListener('input', function() {
            if (bgAudio) {
                bgAudio.volume = this.value;
            }
        });
        
        // Page sound controls
        pageSoundBtn.addEventListener('click', () => pageSoundInput.click());
        pageSoundInput.addEventListener('change', function(e) {
            const file = e.target.files[0];
            const pageNum = parseInt(pageSelect.value);
            
            if (!file || !pageNum) return;
            
            // Remove previous audio if exists
            if (pageAudios[pageNum]) {
                URL.revokeObjectURL(pageAudios[pageNum].src);
            }
            
            const audioUrl = URL.createObjectURL(file);
            pageAudios[pageNum] = new Audio(audioUrl);
            
            // Update tooltip for this page's audio control
            const audioControl = document.querySelector(`.page-audio-control[data-page="${pageNum}"]`);
            if (audioControl) {
                audioControl.querySelector('.tooltiptext').textContent = 'Page sound loaded';
            }
            
            alert(`Sound added to page ${pageNum}`);
        });
        
        // Navigation controls
        prevPageBtn.addEventListener('click', function() {
            $(flipbook).turn('previous');
        });
        
        nextPageBtn.addEventListener('click', function() {
            $(flipbook).turn('next');
        });
        
        fullscreenBtn.addEventListener('click', function() {
            const elem = flipbook;
            if (!document.fullscreenElement) {
                elem.requestFullscreen().catch(err => {
                    alert(`Error attempting to enable fullscreen: ${err.message}`);
                });
            } else {
                document.exitFullscreen();
            }
        });
        
        // Save and share buttons (placeholder functionality)
        saveBookBtn.addEventListener('click', function() {
            alert('In a real application, this would save your flipbook with all audio to our servers or allow you to download it.');
        });
        
        shareBookBtn.addEventListener('click', function() {
            alert('In a real application, this would provide options to share your flipbook via social media or generate a shareable link.');
        });
        
        // Keyboard navigation
        document.addEventListener('keydown', function(e) {
            if (e.key === 'ArrowLeft') {
                $(flipbook).turn('previous');
            } else if (e.key === 'ArrowRight') {
                $(flipbook).turn('next');
            }
        });
    </script>
</body>
</html>

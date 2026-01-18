<!DOCTYPE html>
<html>
<head>
    <title>WhatsApp Blast</title>
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <style>
        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: Arial, sans-serif;
        }
        
        body {
            background: linear-gradient(135deg, #25D366, #128C7E);
            padding: 20px;
            min-height: 100vh;
        }
        
        .container {
            max-width: 500px;
            margin: 0 auto;
            background: white;
            padding: 25px;
            border-radius: 15px;
            box-shadow: 0 5px 20px rgba(0,0,0,0.2);
        }
        
        h1 {
            color: #075E54;
            text-align: center;
            margin-bottom: 10px;
            font-size: 24px;
        }
        
        .subtitle {
            text-align: center;
            color: #666;
            margin-bottom: 25px;
            font-size: 14px;
        }
        
        .form-group {
            margin-bottom: 20px;
        }
        
        label {
            display: block;
            margin-bottom: 8px;
            color: #333;
            font-weight: bold;
            font-size: 14px;
        }
        
        input, textarea {
            width: 100%;
            padding: 12px;
            border: 2px solid #ddd;
            border-radius: 8px;
            font-size: 14px;
        }
        
        textarea {
            resize: vertical;
            min-height: 100px;
        }
        
        input:focus, textarea:focus {
            border-color: #25D366;
            outline: none;
        }
        
        .button {
            background: #25D366;
            color: white;
            border: none;
            padding: 15px;
            border-radius: 8px;
            font-size: 16px;
            font-weight: bold;
            cursor: pointer;
            width: 100%;
            margin-top: 10px;
            transition: background 0.3s;
        }
        
        .button:hover {
            background: #128C7E;
        }
        
        .button:disabled {
            background: #ccc;
            cursor: not-allowed;
        }
        
        .result {
            margin-top: 20px;
            padding: 15px;
            background: #f8f9fa;
            border-radius: 8px;
            border-left: 4px solid #25D366;
            display: none;
        }
        
        .error {
            border-left-color: #dc3545;
            background: #f8d7da;
            color: #721c24;
        }
        
        .success {
            border-left-color: #28a745;
            background: #d4edda;
            color: #155724;
        }
        
        .info {
            border-left-color: #17a2b8;
            background: #d1ecf1;
            color: #0c5460;
        }
        
        .link-box {
            word-break: break-all;
            padding: 10px;
            background: #e9ecef;
            border-radius: 5px;
            margin-top: 10px;
            font-size: 12px;
        }
        
        .copy-btn {
            background: #6c757d;
            color: white;
            border: none;
            padding: 5px 10px;
            border-radius: 4px;
            cursor: pointer;
            font-size: 12px;
            margin-top: 5px;
        }
        
        .step {
            background: #e9f7ef;
            padding: 10px;
            border-radius: 5px;
            margin: 10px 0;
            font-size: 13px;
        }
        
        .phone-item {
            display: flex;
            justify-content: space-between;
            padding: 5px 0;
            border-bottom: 1px solid #eee;
        }
        
        @media (max-width: 600px) {
            .container {
                padding: 15px;
            }
            
            h1 {
                font-size: 20px;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>📱 WhatsApp Blast</h1>
        <p class="subtitle">Kirim pesan ke banyak nomor sekaligus</p>
        
        <div class="form-group">
            <label for="numbers">Nomor Tujuan (pisah dengan koma):</label>
            <textarea id="numbers" placeholder="Contoh: 6281234567890, 6289876543210, 6281112223333"></textarea>
            <div id="numberCount">0 nomor terdeteksi</div>
        </div>
        
        <div class="form-group">
            <label for="message">Pesan:</label>
            <textarea id="message" placeholder="Tulis pesan Anda di sini...">Halo! Ini pesan broadcast dari WhatsApp Blast.</textarea>
        </div>
        
        <div class="form-group">
            <label for="delay">Delay antar pesan (detik):</label>
            <input type="number" id="delay" value="5" min="1" max="60">
        </div>
        
        <button class="button" id="generateBtn" onclick="generateLinks()">🔗 Generate WhatsApp Links</button>
        
        <div class="result" id="resultBox">
            <div id="resultTitle"></div>
            <div id="resultContent"></div>
            <button class="copy-btn" onclick="copyAllLinks()" id="copyBtn">📋 Copy Semua Link</button>
        </div>
        
        <div class="step info">
            <strong>📋 Cara Pakai:</strong><br>
            1. Masukkan nomor & pesan<br>
            2. Klik tombol Generate<br>
            3. Copy link yang muncul<br>
            4. Buka link di browser/WhatsApp<br>
            5. Tap "Send" untuk setiap nomor
        </div>
        
        <div class="step">
            <strong>💡 Tips:</strong><br>
            • Format nomor: 628xxx (tanpa +, spasi)<br>
            • Max 50 nomor sekali kirim<br>
            • Delay minimal 5 detik<br>
            • Jangan spam untuk hindari block
        </div>
    </div>

    <script>
        // Update nomor count
        document.getElementById('numbers').addEventListener('input', function() {
            const numbers = this.value.split(/[\n,]/)
                .map(n => n.trim().replace(/[^0-9]/g, ''))
                .filter(n => n.length >= 10);
            
            document.getElementById('numberCount').textContent = 
                numbers.length + ' nomor terdeteksi';
        });
        
        function generateLinks() {
            const numbersInput = document.getElementById('numbers').value;
            const message = encodeURIComponent(document.getElementById('message').value.trim());
            const delay = parseInt(document.getElementById('delay').value);
            
            if (!message) {
                showResult('error', 'Masukkan pesan terlebih dahulu');
                return;
            }
            
            // Parse nomor
            const numbers = numbersInput.split(/[\n,]/)
                .map(n => n.trim().replace(/[^0-9]/g, ''))
                .filter(n => n.length >= 10);
            
            if (numbers.length === 0) {
                showResult('error', 'Masukkan minimal satu nomor');
                return;
            }
            
            if (numbers.length > 100) {
                showResult('error', 'Maksimal 100 nomor sekali kirim');
                return;
            }
            
            // Generate WhatsApp links
            let linksHTML = '<div style="max-height: 300px; overflow-y: auto;">';
            
            numbers.forEach((number, index) => {
                const whatsappLink = `https://wa.me/${number}?text=${message}`;
                const shortLink = `${number}`;
                
                linksHTML += `
                    <div class="phone-item">
                        <div>
                            <strong>${index + 1}. ${shortLink}</strong><br>
                            <small><a href="${whatsappLink}" target="_blank">${whatsappLink.substring(0, 60)}...</a></small>
                        </div>
                        <button class="copy-btn" onclick="copyLink('${whatsappLink}')">Copy</button>
                    </div>
                `;
            });
            
            linksHTML += '</div>';
            
            // Tambahkan instruksi
            const instructions = `
                <div style="margin-top: 20px; padding: 10px; background: #fff3cd; border-radius: 5px;">
                    <strong>📋 Cara Kirim:</strong><br>
                    1. Buka link pertama di browser<br>
                    2. WhatsApp akan terbuka dengan pesan sudah terisi<br>
                    3. Tap "Send"<br>
                    4. Tunggu ${delay} detik<br>
                    5. Buka link berikutnya<br>
                    6. Ulangi sampai selesai
                </div>
                
                <div style="margin-top: 10px; color: #666; font-size: 12px;">
                    <strong>Note:</strong> Browser akan buka WhatsApp untuk setiap nomor.<br>
                    Tunggu ${delay} detik sebelum buka link berikutnya.
                </div>
            `;
            
            showResult('success', 
                `<strong>✅ ${numbers.length} Link WhatsApp Berhasil Digenerate!</strong><br><br>
                 ${linksHTML}
                 ${instructions}`
            );
            
            // Simpan data untuk copy semua
            window.generatedLinks = numbers.map(number => 
                `https://wa.me/${number}?text=${message}`
            );
        }
        
        function copyLink(link) {
            navigator.clipboard.writeText(link).then(() => {
                alert('Link copied to clipboard!');
            });
        }
        
        function copyAllLinks() {
            if (!window.generatedLinks || window.generatedLinks.length === 0) {
                alert('Tidak ada link untuk dicopy');
                return;
            }
            
            const allLinks = window.generatedLinks.join('\n');
            navigator.clipboard.writeText(allLinks).then(() => {
                alert(`${window.generatedLinks.length} link berhasil dicopy!`);
                document.getElementById('copyBtn').textContent = '✅ Copied!';
                setTimeout(() => {
                    document.getElementById('copyBtn').textContent = '📋 Copy Semua Link';
                }, 2000);
            });
        }
        
        function showResult(type, content) {
            const resultBox = document.getElementById('resultBox');
            const resultTitle = document.getElementById('resultTitle');
            const resultContent = document.getElementById('resultContent');
            
            resultBox.className = 'result ' + type;
            resultTitle.innerHTML = type === 'success' ? '✅ Success!' : 
                                  type === 'error' ? '❌ Error!' : 'ℹ️ Info';
            resultContent.innerHTML = content;
            resultBox.style.display = 'block';
            
            // Scroll ke result
            resultBox.scrollIntoView({ behavior: 'smooth' });
        }
        
        // Auto-resize textarea
        document.querySelectorAll('textarea').forEach(textarea => {
            textarea.addEventListener('input', function() {
                this.style.height = 'auto';
                this.style.height = (this.scrollHeight) + 'px';
            });
        });
        
        // Load contoh data
        window.onload = function() {
            document.getElementById('numbers').value = '6281234567890, 6289876543210';
            document.getElementById('numbers').dispatchEvent(new Event('input'));
        };
    </script>
</body>
</html>

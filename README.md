<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>盤子鑑定機 🔍 買貴直接笑</title>
    <style>
        * { box-sizing: border-box; margin: 0; padding: 0; }
        body {
            background-color: #0f172a;
            color: #ffffff;
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, "Noto Sans TC", sans-serif;
            min-height: 100vh;
            display: flex;
            flex-direction: column;
            align-items: center;
            padding: 20px 16px;
        }
        header { text-align: center; margin: 20px 0 24px; }
        header h1 { font-size: 28px; font-weight: 900; color: #fbbf24; letter-spacing: 1px; }
        header p { color: #94a3b8; font-size: 14px; margin-top: 6px; }

        .card {
            width: 100%;
            max-width: 420px;
            background-color: #1e293b;
            border-radius: 16px;
            padding: 24px;
            border: 1px solid #334155;
            box-shadow: 0 10px 25px rgba(0, 0, 0, 0.4);
        }

        .form-group { margin-bottom: 20px; }
        label { display: block; font-size: 14px; font-weight: bold; color: #cbd5e1; margin-bottom: 8px; }

        .upload-box {
            border: 2px dashed #475569;
            border-radius: 12px;
            padding: 28px 16px;
            text-align: center;
            cursor: pointer;
            background-color: rgba(15, 23, 42, 0.5);
            transition: border-color 0.2s;
        }
        .upload-box:active { border-color: #fbbf24; }
        .upload-icon { font-size: 36px; display: block; margin-bottom: 8px; }
        .upload-title { font-size: 15px; font-weight: bold; color: #e2e8f0; }
        .upload-sub { font-size: 12px; color: #64748b; margin-top: 4px; }

        #image-input { display: none; }
        #image-preview { max-height: 220px; max-width: 100%; border-radius: 8px; margin: 0 auto; display: none; }

        .input-wrapper { position: relative; }
        .currency { position: absolute; left: 14px; top: 50%; transform: translateY(-50%); color: #94a3b8; font-weight: bold; font-size: 18px; }
        input[type="number"] {
            width: 100%;
            background-color: #0f172a;
            border: 1px solid #475569;
            border-radius: 12px;
            padding: 14px 14px 14px 34px;
            color: #ffffff;
            font-size: 18px;
            font-weight: bold;
            outline: none;
        }
        input[type="number"]:focus { border-color: #fbbf24; }

        .btn-primary {
            width: 100%;
            background-color: #f59e0b;
            color: #0f172a;
            font-size: 18px;
            font-weight: 900;
            padding: 14px;
            border: none;
            border-radius: 12px;
            cursor: pointer;
            box-shadow: 0 4px 14px rgba(245, 158, 11, 0.3);
        }
        .btn-primary:active { transform: scale(0.98); background-color: #fbbf24; }

        .loading { display: none; text-align: center; margin-top: 30px; }
        .spinner {
            width: 38px; height: 38px;
            border: 4px solid #fbbf24;
            border-top-color: transparent;
            border-radius: 50%;
            animation: spin 0.8s linear infinite;
            margin: 0 auto;
        }
        @keyframes spin { to { transform: rotate(360deg); } }
        .loading p { color: #fbbf24; font-weight: bold; margin-top: 12px; font-size: 15px; }

        /* 結果卡片樣式 */
        .result-card {
            display: none;
            width: 100%;
            max-width: 420px;
            margin-top: 24px;
            border-radius: 16px;
            padding: 24px;
            background-color: #1e293b;
            border: 2px solid #ef4444;
            box-shadow: 0 12px 30px rgba(0, 0, 0, 0.5);
        }
        .result-header { text-align: center; border-bottom: 1px solid #334155; padding-bottom: 16px; }
        .badge {
            display: inline-block;
            padding: 4px 12px;
            border-radius: 999px;
            font-size: 12px;
            font-weight: 900;
            margin-bottom: 8px;
        }
        .rank-title { font-size: 26px; font-weight: 900; margin-top: 4px; }
        .product-name { font-size: 13px; color: #94a3b8; margin-top: 6px; }

        .price-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; margin: 18px 0; text-align: center; }
        .price-box { background-color: rgba(15, 23, 42, 0.8); padding: 12px; border-radius: 12px; border: 1px solid #334155; }
        .price-label { font-size: 12px; color: #94a3b8; }
        .price-val { font-size: 20px; font-weight: 900; margin-top: 4px; }

        .roast-box { background-color: rgba(2, 6, 23, 0.7); border-radius: 12px; padding: 16px; border: 1px solid #1e293b; }
        .roast-text { font-size: 15px; font-weight: bold; line-height: 1.5; }
        .diff-detail { font-size: 12px; color: #94a3b8; margin-top: 8px; }

        .btn-reset {
            width: 100%;
            margin-top: 18px;
            background-color: #334155;
            color: #ffffff;
            font-weight: bold;
            padding: 12px;
            border: none;
            border-radius: 12px;
            font-size: 14px;
            cursor: pointer;
        }
    </style>
</head>
<body>

    <header>
        <h1>🍽️ 盤子鑑定機</h1>
        <p>拍照比價，看看你朋友是不是財富自由大慈善家</p>
    </header>

    <main class="card">
        <div class="form-group">
            <label>1. 拍下商品或上傳照片</label>
            <div class="upload-box" onclick="document.getElementById('image-input').click()">
                <input type="file" id="image-input" accept="image/*" capture="environment" onchange="previewImage(event)">
                <div id="upload-placeholder">
                    <span class="upload-icon">📸</span>
                    <div class="upload-title">點擊開啟相機 / 選擇照片</div>
                    <div class="upload-sub">支援拍商品外觀或條碼</div>
                </div>
                <img id="image-preview" alt="商品預覽">
            </div>
        </div>

        <div class="form-group">
            <label for="user-price">2. 他買多少錢？（新台幣）</label>
            <div class="input-wrapper">
                <span class="currency">$</span>
                <input type="number" id="user-price" placeholder="例如：1500">
            </div>
        </div>

        <button onclick="analyzeItem()" id="submit-btn" class="btn-primary">
            開始鑑定是不是盤子 🔍
        </button>
    </main>

    <div id="loading" class="loading">
        <div class="spinner"></div>
        <p>AI 正在全網搜刮最低價...</p>
    </div>

    <section id="result-card" class="result-card">
        <div class="result-header">
            <span id="badge-tag" class="badge">鑑定報告</span>
            <h2 id="rank-title" class="rank-title">頂級黃金大盤 🏆</h2>
            <p id="product-name" class="product-name">辨識商品：範例商品</p>
        </div>

        <div class="price-grid">
            <div class="price-box">
                <div class="price-label">他的入手價</div>
                <div id="display-user-price" class="price-val" style="color: #f87171;">$0</div>
            </div>
            <div class="price-box">
                <div class="price-label">全網最低價</div>
                <div id="display-market-price" class="price-val" style="color: #34d399;">$0</div>
            </div>
        </div>

        <div class="roast-box">
            <p id="roast-comment" class="roast-text"></p>
            <p id="diff-detail" class="diff-detail"></p>
        </div>

        <button onclick="resetForm()" class="btn-reset">再鑑定下一個商品 🔄</button>
    </section>

    <script>
        function previewImage(event) {
            const file = event.target.files;
            if (!file) return;
            const reader = new FileReader();
            reader.onload = function(e) {
                const preview = document.getElementById('image-preview');
                const placeholder = document.getElementById('upload-placeholder');
                preview.src = e.target.result;
                preview.style.display = 'block';
                placeholder.style.display = 'none';
            };
            reader.readAsDataURL(file);
        }

        function analyzeItem() {
            const imageInput = document.getElementById('image-input');
            const userPrice = parseFloat(document.getElementById('user-price').value);

            if (!imageInput.files.length) {
                alert('請先拍一張照片或上傳圖片！');
                return;
            }
            if (!userPrice || userPrice <= 0) {
                alert('請輸入有效的購買金額！');
                return;
            }

            document.getElementById('submit-btn').style.display = 'none';
            document.getElementById('result-card').style.display = 'none';
            document.getElementById('loading').style.display = 'block';

            setTimeout(() => {
                document.getElementById('loading').style.display = 'none';
                document.getElementById('submit-btn').style.display = 'block';

                const mockProductName = "測試辨識商品 (尚未串接真實比價API)";
                const mockMarketPrice = Math.round(userPrice * 0.72); 
                renderResult(mockProductName, userPrice, mockMarketPrice);
            }, 1500);
        }

        function renderResult(productName, userPrice, marketPrice) {
            const diff = userPrice - marketPrice;
            const percent = Math.round((diff / marketPrice) * 100);
            const bobaCount = Math.floor(Math.abs(diff) / 65);

            const card = document.getElementById('result-card');
            const badge = document.getElementById('badge-tag');
            const title = document.getElementById('rank-title');
            const roast = document.getElementById('roast-comment');
            const detail = document.getElementById('diff-detail');
document.getElementById('product-name').innerText = 辨識商品：${productName};
document.getElementById('display-user-price').innerText = $${userPrice.toLocaleString()};
document.getElementById('display-market-price').innerText = $${marketPrice.toLocaleString()};
if (diff <= 0) {
card.style.borderColor = '#10b981';
badge.style.backgroundColor = 'rgba(16, 185, 129, 0.2)';
badge.style.color = '#34d399';
badge.innerText = '省錢大師';
title.innerText = '超級精算師 🧠';
roast.style.color = '#6ee7b7';
roast.innerText = '「太神啦！你買得比網路最低價還便宜，老闆看到你都要虧錢哭出來了！」';
detail.innerText = diff === 0 ? '剛好買在全網最低價！' : 比最低價還省了 $${Math.abs(diff).toLocaleString()}！;
} else if (percent <= 15) {
card.style.borderColor = '#fbbf24';
badge.style.backgroundColor = 'rgba(245, 158, 11, 0.2)';
badge.style.color = '#fbbf24';
badge.innerText = '微盤認證';
title.innerText = '精緻小盤子 🍽️';
roast.style.color = '#fde68a';
roast.innerText = '「稍微買貴了一點點，沒關係，就當作花錢買快樂跟早享受！」';
detail.innerText = 比最低價貴了 $${diff.toLocaleString()}（多付 ${percent}%，少喝 ${bobaCount} 杯珍奶）;
} else {
card.style.borderColor = '#ef4444';
badge.style.backgroundColor = 'rgba(239, 68, 68, 0.2)';
badge.style.color = '#f87171';
badge.innerText = '警報！發現肥羊';
title.innerText = '頂級黃金大盤 🏆';
roast.style.color = '#fca5a5';
roast.innerText = '「笑死，這價格你都買得下去？店員現在應該在後面開香檳慶祝遇到大慈善家了！」';
detail.innerText = 比最低價足足貴了 $${diff.toLocaleString()}（貴了 ${percent}%，直接飛走 ${bobaCount} 杯珍奶！）;
}
card.style.display = 'block';
}
function resetForm() {
document.getElementById('image-input').value = '';
document.getElementById('user-price').value = '';
document.getElementById('image-preview').style.display = 'none';
document.getElementById('upload-placeholder').style.display = 'block';
document.getElementById('result-card').style.display = 'none';
}


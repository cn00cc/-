<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>盤子鑑定機 🔍 買貴直接笑</title>
    <script src="https://tailwindcss.com"></script>
    <style>
        @import url('https://googleapis.com');
        body { font-family: 'Noto Sans TC', sans-serif; }
    </style>
</head>
<body class="bg-slate-900 text-white min-h-screen flex flex-col items-center p-4">

    <!-- 標題區 -->
    <header class="text-center my-6">
        <h1 class="text-3xl font-black tracking-wider text-amber-400">🍽️ 盤子鑑定機</h1>
        <p class="text-slate-400 text-sm mt-1">拍照比價，看看你朋友是不是財富自由大慈善家</p>
    </header>

    <!-- 主要操作卡片 -->
    <main class="w-full max-w-md bg-slate-800 rounded-2xl p-6 shadow-xl border border-slate-700">
        
        <!-- 1. 拍照 / 上傳圖片區 -->
        <div class="mb-5">
            <label class="block text-sm font-bold mb-2 text-slate-300">1. 拍下商品或上傳照片</label>
            <div id="upload-box" class="relative border-2 border-dashed border-slate-600 rounded-xl p-6 text-center hover:border-amber-400 transition cursor-pointer bg-slate-900/50" onclick="document.getElementById('image-input').click()">
                <input type="file" id="image-input" accept="image/*" capture="environment" class="hidden" onchange="previewImage(event)">
                
                <div id="upload-placeholder">
                    <span class="text-4xl block mb-2">📸</span>
                    <p class="text-sm text-slate-300 font-bold">點擊開啟相機 / 選擇照片</p>
                    <p class="text-xs text-slate-500 mt-1">支援拍商品外觀或條碼</p>
                </div>

                <img id="image-preview" class="hidden max-h-56 mx-auto rounded-lg object-contain" alt="商品預覽">
            </div>
        </div>

        <!-- 2. 輸入入手價格 -->
        <div class="mb-6">
            <label for="user-price" class="block text-sm font-bold mb-2 text-slate-300">2. 他買多少錢？（新台幣）</label>
            <div class="relative">
                <span class="absolute left-4 top-3 text-slate-400 font-bold">$</span>
                <input type="number" id="user-price" placeholder="例如：1500" class="w-full bg-slate-900 border border-slate-600 rounded-xl py-3 pl-9 pr-4 text-white font-bold text-lg focus:outline-none focus:border-amber-400">
            </div>
        </div>

        <!-- 鑑定按鈕 -->
        <button onclick="analyzeItem()" id="submit-btn" class="w-full bg-amber-500 hover:bg-amber-400 text-slate-900 font-black text-lg py-3.5 rounded-xl shadow-lg transition transform active:scale-95">
            開始鑑定是不是盤子 🔍
        </button>

    </main>

    <!-- 載入動畫 -->
    <div id="loading" class="hidden mt-8 text-center">
        <div class="inline-block animate-spin rounded-full h-10 w-10 border-4 border-amber-400 border-t-transparent"></div>
        <p class="text-amber-400 font-bold mt-3 animate-pulse">AI 正在全網搜刮最低價...</p>
    </div>

    <!-- 鑑定結果卡片 -->
    <section id="result-card" class="hidden w-full max-w-md mt-6 bg-gradient-to-br from-slate-800 to-slate-900 rounded-2xl p-6 shadow-2xl border-2 border-amber-500/50">
        <div class="text-center border-b border-slate-700 pb-4">
            <span id="badge-tag" class="inline-block px-3 py-1 rounded-full text-xs font-black tracking-widest uppercase mb-2 bg-red-500/20 text-red-400">鑑定報告</span>
            <h2 id="rank-title" class="text-3xl font-black text-white mt-1">頂級黃金大盤 🏆</h2>
            <p id="product-name" class="text-sm text-slate-400 mt-1">辨識商品：範例商品名稱</p>
        </div>

        <!-- 價格對比 -->
        <div class="grid grid-cols-2 gap-4 my-5 text-center">
            <div class="bg-slate-900/80 p-3 rounded-xl border border-slate-700">
                <p class="text-xs text-slate-400">他的入手價</p>
                <p id="display-user-price" class="text-xl font-black text-red-400 mt-1">$0</p>
            </div>
            <div class="bg-slate-900/80 p-3 rounded-xl border border-slate-700">
                <p class="text-xs text-slate-400">全網最低價</p>
                <p id="display-market-price" class="text-xl font-black text-emerald-400 mt-1">$0</p>
            </div>
        </div>

        <!-- 嘲諷語錄與珍奶換算 -->
        <div class="bg-slate-950/60 rounded-xl p-4 border border-slate-800">
            <p id="roast-comment" class="text-base font-bold text-amber-300 leading-relaxed">
                「老闆看到你進門眼睛都在發光，這價差都能多買好幾杯珍奶了！」
            </p>
            <p id="diff-detail" class="text-xs text-slate-400 mt-2">
                比最低價貴了 $0（多付 0%）
            </p>
        </div>

        <!-- 分享 / 再測一次 -->
        <button onclick="resetForm()" class="w-full mt-5 bg-slate-700 hover:bg-slate-600 text-white font-bold py-2.5 rounded-xl text-sm transition">
            再鑑定下一個商品 🔄
        </button>
    </section>

    <script>
        // 圖片預覽功能
        function previewImage(event) {
            const file = event.target.files[0];
            if (!file) return;

            const reader = new FileReader();
            reader.onload = function(e) {
                const preview = document.getElementById('image-preview');
                const placeholder = document.getElementById('upload-placeholder');
                preview.src = e.target.result;
                preview.classList.remove('hidden');
                placeholder.classList.add('hidden');
            };
            reader.readAsDataURL(file);
        }

        // 執行鑑定（目前為前端模擬邏輯，未來可替換為真實後端 API）
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

            // 顯示載入中
            document.getElementById('submit-btn').classList.add('hidden');
            document.getElementById('result-card').classList.add('hidden');
            document.getElementById('loading').classList.remove('hidden');

            // 模擬 API 網路請求延遲 (1.5秒)
            setTimeout(() => {
                document.getElementById('loading').classList.add('hidden');
                document.getElementById('submit-btn').classList.remove('hidden');

                // 【模擬資料】：假設全網最低價是使用者輸入價格的 7 折（測試盤子效果用）
                // 未來串接後端時，這裡會替換成 API 回傳的真實商品名稱與最低價
                const mockProductName = "測試辨識商品 (尚未串接真實比價API)";
                const mockMarketPrice = Math.round(userPrice * 0.72); 

                renderResult(mockProductName, userPrice, mockMarketPrice);
            }, 1500);
        }

        // 渲染結果卡片與盤子分級邏輯
        function renderResult(productName, userPrice, marketPrice) {
            const diff = userPrice - marketPrice;
            const percent = Math.round((diff / marketPrice) * 100);
            const bobaCount = Math.floor(Math.abs(diff) / 65); // 一杯珍奶算 65 元

            const card = document.getElementById('result-card');
            const badge = document.getElementById('badge-tag');
            const title = document.getElementById('rank-title');
            const roast = document.getElementById('roast-comment');
            const detail = document.getElementById('diff-detail');

            document.getElementById('product-name').innerText = `辨識商品：${productName}`;
            document.getElementById('display-user-price').innerText = `$${userPrice.toLocaleString()}`;
            document.getElementById('display-market-price').innerText = `$${marketPrice.toLocaleString()}`;

            if (diff <= 0) {
                // 買便宜或買在最低點
                card.className = "w-full max-w-md mt-6 bg-gradient-to-br from-emerald-950 to-slate-900 rounded-2xl p-6 shadow-2xl border-2 border-emerald-500";
                badge.className = "inline-block px-3 py-1 rounded-full text-xs font-black tracking-widest uppercase mb-2 bg-emerald-500/20 text-emerald-400";
                badge.innerText = "省錢大師";
                title.innerText = "超級精算師 🧠";
                roast.className = "text-base font-bold text-emerald-300 leading-relaxed";
                roast.innerText = `「太神啦！你買得比網路最低價還便宜，老闆看到你都要虧錢哭出來了！」`;
                detail.innerText = diff === 0 ? "剛好買在全網最低價！" : `比最低價還省了 $${Math.abs(diff).toLocaleString()}！`;
            } else if (percent <= 15) {
                // 微盤
                card.className = "w-full max-w-md mt-6 bg-gradient-to-br from-amber-950/60 to-slate-900 rounded-2xl p-6 shadow-2xl border-2 border-amber-400";
                badge.className = "inline-block px-3 py-1 rounded-full text-xs font-black tracking-widest uppercase mb-2 bg-amber-500/20 text-amber-400";
                badge.innerText = "微盤認證";
                title.innerText = "精緻小盤子 🍽️";
                roast.className = "text-base font-bold text-amber-300 leading-relaxed";
                roast.innerText = `「稍微買貴了一點點，沒關係，就當作花錢買快樂跟早享受！」`;
                detail.innerText = `比最低價貴了 $${diff.toLocaleString()}（多付 ${percent}%，少喝 ${bobaCount} 杯珍奶）`;
            } else {
                // 大盤子
                card.className = "w-full max-w-md mt-6 bg-gradient-to-br from-red-950/80 to-slate-900 rounded-2xl p-6 shadow-2xl border-2 border-red-500";
                badge.className = "inline-block px-3 py-1 rounded-full text-xs font-black tracking-widest uppercase mb-2 bg-red-500/20 text-red-400";
                badge.innerText = "警報！發現肥羊";
                title.innerText = "頂級黃金大盤 🏆";
                roast.className = "text-base font-bold text-red-300 leading-relaxed";
                roast.innerText = `「笑死，這價格你都買得下去？店員現在應該在後面開香檳慶祝遇到大慈善家了！」`;
                detail.innerText = `比最低價足足貴了 $${diff.toLocaleString()}（貴了 ${percent}%，直接飛走 ${bobaCount} 杯珍奶！）`;
            }

            card.classList.remove('hidden');
        }

        // 重置表單
        function resetForm() {
        document.getElementById('image-input').value = '';
document.getElementById('user-price').value = '';
document.getElementById('image-preview').classList.add('hidden');
document.getElementById('upload-placeholder').classList.remove('hidden');
document.getElementById('result-card').classList.add('hidden');
}



### 這個模板已經包含的功能：
1. **手機直接拍照**：`<input type="file" accept="image/*" capture="environment">` 讓手機點擊時能直接喚起後置相機拍照。
2. **動態嘲諷與珍奶換算**：自動算出價差百分比，並換算成「少喝幾杯珍奶」，根據買貴的程度切換不同顏色的警告卡片。

<FollowUp>
接下來你想繼續完成哪一塊？
* **後端真實辨識與比價 API**（使用 Node.js 或 Python 串接 Gemini API 看懂照片＋SerpApi 抓真實最低價）
* **一鍵截圖分享功能**（加入 `html2canvas`，讓使用者可以直接下載「盤子證書圖片」傳到 LINE 或 IG 笑朋友）
* **免費部署上線教學**（教你如何免費放上 Vercel 或 GitHub Pages 產生網址）
</FollowUp>


<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>知识卡片生成器</title>
    <!-- 引入 Tailwind CSS 样式库 -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- 引入 html2canvas 库用于将 HTML 转换为图片下载 -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Noto+Sans+SC:wght@400;500;700&display=swap');
        body {
            font-family: 'Noto Sans SC', -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
        }
    </style>
</head>
<body class="bg-slate-100 min-h-screen py-10 px-4">
    <div class="max-w-5xl mx-auto grid grid-cols-1 lg:grid-cols-12 gap-8">
        
        <!-- 左侧编辑区域 -->
        <div class="lg:col-span-5 bg-white p-6 rounded-2xl shadow-sm border border-slate-200/80 flex flex-col gap-5">
            <h2 class="text-xl font-bold text-slate-800 border-b pb-3">卡片编辑器</h2>
            
            <!-- 卡片主题选择 -->
            <div>
                <label class="block text-sm font-medium text-slate-600 mb-2">卡片主题</label>
                <div class="grid grid-cols-4 gap-2">
                    <button onclick="changeTheme('sunset')" class="h-8 rounded-lg bg-gradient-to-tr from-amber-500 to-rose-500 border-2 border-transparent focus:border-slate-800" title="落日渐变"></button>
                    <button onclick="changeTheme('ocean')" class="h-8 rounded-lg bg-gradient-to-tr from-cyan-500 to-blue-600 border-2 border-transparent focus:border-slate-800" title="海洋渐变"></button>
                    <button onclick="changeTheme('emerald')" class="h-8 rounded-lg bg-gradient-to-tr from-emerald-400 to-teal-600 border-2 border-transparent focus:border-slate-800" title="翡翠渐变"></button>
                    <button onclick="changeTheme('minimalist')" class="h-8 rounded-lg bg-slate-900 border-2 border-transparent focus:border-slate-800" title="极简暗黑"></button>
                </div>
            </div>

            <!-- 分类标签 -->
            <div>
                <label for="input-tag" class="block text-sm font-medium text-slate-600 mb-1">卡片类别 / 标签</label>
                <input type="text" id="input-tag" value="方法论" class="w-full px-3 py-2 border border-slate-300 rounded-lg text-sm focus:outline-none focus:ring-2 focus:ring-blue-500" oninput="updateCard()">
            </div>

            <!-- 步骤/标题 -->
            <div>
                <label for="input-step" class="block text-sm font-medium text-slate-600 mb-1">步骤 / 核心动作</label>
                <input type="text" id="input-step" value="步骤：找 20 个大牛" class="w-full px-3 py-2 border border-slate-300 rounded-lg text-sm focus:outline-none focus:ring-2 focus:ring-blue-500" oninput="updateCard()">
            </div>

            <!-- 做什么 -->
            <div>
                <label for="input-what" class="block text-sm font-medium text-slate-600 mb-1">做什么 (具体执行内容)</label>
                <textarea id="input-what" rows="4" class="w-full px-3 py-2 border border-slate-300 rounded-lg text-sm focus:outline-none focus:ring-2 focus:ring-blue-500" oninput="updateCard()">找领域最厉害的 20 人，跟他们在各平台的发言 摸清最前沿讨论 。</textarea>
            </div>

            <!-- 目的 -->
            <div>
                <label for="input-purpose" class="block text-sm font-medium text-slate-600 mb-1">目的 (核心结果/闭环)</label>
                <input type="text" id="input-purpose" value="摸清最前沿讨论" class="w-full px-3 py-2 border border-slate-300 rounded-lg text-sm focus:outline-none focus:ring-2 focus:ring-blue-500" oninput="updateCard()">
            </div>

            <!-- 下载按钮 -->
            <button onclick="downloadCard()" class="mt-4 w-full bg-slate-900 hover:bg-slate-800 text-white font-medium py-2.5 px-4 rounded-lg transition-colors duration-150 shadow-sm flex items-center justify-center gap-2">
                <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 16v1a3 3 0 003 3h10a3 3 0 003-3v-1m-4-4l-4 4m0 0l-4-4m4 4V4"></path></svg>
                下载卡片图片
            </button>
        </div>

        <!-- 右侧卡片预览区域 -->
        <div class="lg:col-span-7 flex justify-center items-center">
            <!-- 卡片容器 (固定比例，适合分享) -->
            <div id="knowledge-card" class="w-[380px] h-[520px] bg-gradient-to-tr from-amber-500 to-rose-500 p-6 rounded-[24px] shadow-xl flex flex-col justify-between text-white relative overflow-hidden">
                
                <!-- 卡片顶部的装饰性半透明网格或发光点 -->
                <div class="absolute top-0 right-0 w-32 h-32 bg-white/10 rounded-full blur-2xl pointer-events-none"></div>
                
                <!-- 头部 -->
                <div class="flex justify-between items-center z-10">
                    <span id="card-tag" class="text-xs font-semibold tracking-wider bg-white/20 px-2.5 py-1 rounded-full backdrop-blur-md">
                        方法论
                    </span>
                    <span class="text-xs text-white/60 tracking-widest font-mono">KNOWLEDGE CARD</span>
                </div>

                <!-- 中部核心内容 -->
                <div class="flex-1 flex flex-col justify-center my-6 z-10">
                    <!-- 步骤 -->
                    <h3 id="card-step" class="text-2xl font-bold tracking-tight mb-6 leading-snug">
                        步骤：找 20 个大牛
                    </h3>
                    
                    <!-- 具体行动 -->
                    <div class="bg-white/10 backdrop-blur-md rounded-xl p-4 border border-white/10 mb-4">
                        <div class="text-[11px] uppercase tracking-wider text-white/60 font-semibold mb-1">做什么</div>
                        <p id="card-what" class="text-[15px] leading-relaxed font-light text-white/95">
                            找领域最厉害的 20 人，跟他们在各平台的发言 摸清最前沿讨论 。
                        </p>
                    </div>

                    <!-- 目的 -->
                    <div class="bg-white/15 backdrop-blur-md rounded-xl p-4 border border-white/10">
                        <div class="text-[11px] uppercase tracking-wider text-white/60 font-semibold mb-1">目的</div>
                        <p id="card-purpose" class="text-[15px] leading-relaxed font-semibold text-amber-100">
                            摸清最前沿讨论
                        </p>
                    </div>
                </div>

                <!-- 底部 -->
                <div class="border-t border-white/10 pt-4 flex justify-between items-center text-[10px] text-white/50 z-10">
                    <span>💡 实践出真知</span>
                    <span>DAILY INSIGHTS</span>
                </div>
            </div>
        </div>

    </div>

    <script>
        // 定义主题色配置
        const themes = {
            sunset: 'bg-gradient-to-tr from-amber-500 to-rose-500',
            ocean: 'bg-gradient-to-tr from-cyan-500 to-blue-600',
            emerald: 'bg-gradient-to-tr from-emerald-400 to-teal-600',
            minimalist: 'bg-slate-900 border border-slate-800'
        };

        let currentTheme = 'sunset';

        // 实时同步输入框到卡片上
        function updateCard() {
            document.getElementById('card-tag').innerText = document.getElementById('input-tag').value || '未分类';
            document.getElementById('card-step').innerText = document.getElementById('input-step').value || '未输入步骤';
            document.getElementById('card-what').innerText = document.getElementById('input-what').value || '未输入具体行动';
            document.getElementById('card-purpose').innerText = document.getElementById('input-purpose').value || '未输入目的';
        }

        // 改变卡片主题背景
        function changeTheme(themeName) {
            const card = document.getElementById('knowledge-card');
            
            // 移除已有主题样式
            Object.values(themes).forEach(className => {
                className.split(' ').forEach(cls => card.classList.remove(cls));
            });

            // 添加新主题样式
            themes[themeName].split(' ').forEach(cls => card.classList.add(cls));
            currentTheme = themeName;
            
            // 针对特定主题调整文字高亮颜色
            const purposeText = document.getElementById('card-purpose');
            if (themeName === 'minimalist') {
                purposeText.className = "text-[15px] leading-relaxed font-semibold text-teal-400";
            } else if (themeName === 'emerald') {
                purposeText.className = "text-[15px] leading-relaxed font-semibold text-amber-200";
            } else {
                purposeText.className = "text-[15px] leading-relaxed font-semibold text-amber-100";
            }
        }

        // 下载卡片函数
        function downloadCard() {
            const card = document.getElementById('knowledge-card');
            
            // 提升下载图片的清晰度 (通过配置 scale 参数)
            html2canvas(card, {
                useCORS: true,
                scale: 2, 
                backgroundColor: null,
                logging: false
            }).then(canvas => {
                const link = document.createElement('a');
                link.download = `知识卡片-${new Date().getTime()}.png`;
                link.href = canvas.toDataURL('image/png');
                link.click();
            }).catch(err => {
                console.error("生成卡片图片时出错:", err);
            });
        }
    </script>
</body>
</html>

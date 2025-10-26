<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="utf-8" />
  <title>末世互助网 — 原型</title>
  <style>
    body { font-family: Arial, "Microsoft YaHei", sans-serif; background:#f4f6f8; margin:0; padding:0; }
    header { background:#1f2937; color:white; padding:12px 18px; display:flex; justify-content:space-between; align-items:center;}
    header h1 { font-size:18px; margin:0; }
    #main { padding:16px; max-width:1100px; margin:18px auto; }
    .card { background:white; padding:12px; border-radius:8px; box-shadow:0 1px 4px rgba(0,0,0,0.08); margin-bottom:14px; }
    .flex { display:flex; gap:12px; }
    .col { flex:1; }
    input, select, textarea, button { font-size:14px; }
    input, select, textarea { padding:8px; width:100%; box-sizing:border-box; margin-top:6px; margin-bottom:8px; border:1px solid #ddd; border-radius:6px; }
    button { padding:8px 12px; background:#2563eb; color:white; border:0; border-radius:6px; cursor:pointer; }
    button.secondary { background:#6b7280; }
    .small { font-size:12px; color:#666; }
    .muted { color:#666; font-size:13px; }
    ul { padding-left:18px; }
    .tag { display:inline-block; padding:4px 8px; border-radius:12px; background:#eef2ff; margin:4px; font-size:13px; }
    .danger { color:#b91c1c; font-weight:600; }
    .success { color:#065f46; font-weight:600; }
    .inline { display:inline-block; margin-right:8px; }
    .post { border-top:1px dashed #eee; padding:8px 0; }
    .linklike { color:#2563eb; cursor:pointer; text-decoration:underline; }
    .grid-3 { display:grid; grid-template-columns:repeat(3,1fr); gap:8px; }
    .checkbox { display:flex; gap:8px; align-items:center; }
    .stat { display:flex; gap:12px; flex-wrap:wrap; }
    .stat .item { background:#f3f4f6; padding:6px 8px; border-radius:6px; font-size:13px; }
    .tiny { font-size:12px; color:#444; }
    .center { text-align:center; }
    .danger-bg { background:#fff1f2; padding:8px; border-radius:6px; }
  </style>
</head>
<body>
  <header>
    <h1>末世互助网 — 原型</h1>
    <div id="headerRight"></div>
  </header>

  <div id="main">
    <!-- 登入 / 注册（初始） -->
    <div id="authArea" class="card"></div>

    <!-- 主界面 -->
    <div id="appArea" style="display:none;">
      <div class="card flex" id="topBar">
        <div>
          <div class="small">已登录为：<span id="meName"></span> <span class="muted">（<span id="meEmail"></span>）</span></div>
          <div class="tiny">角色：<span id="meCharName"></span> · 性别：<span id="meCharGender"></span> · 年龄：<span id="meCharAge"></span></div>
        </div>
        <div style="text-align:right;">
          <div class="small">地点：<span id="meLoc"></span></div>
          <div class="tiny">生存日：<span id="meDays"></span></div>
          <div style="margin-top:6px;">
            <button onclick="nav('home')">主页</button>
            <button onclick="nav('post')">发帖</button>
            <button onclick="nav('monster')">怪物</button>
            <button onclick="nav('resource')">记录/交易</button>
            <button onclick="nav('profile')">个人</button>
            <button class="secondary" onclick="logout()">登出</button>
          </div>
        </div>
      </div>

      <div id="pages">
        <!-- HOME -->
        <div id="home" class="card page"></div>

        <!-- POST -->
        <div id="post" class="card page" style="display:none;">
          <h3>发表文字帖（纯文本）</h3>
          <textarea id="newPostContent" rows="4" placeholder="写下你在末世的所见、求助、或交易信息..."></textarea>
          <div style="display:flex; gap:8px;">
            <input id="newPostTitle" placeholder="标题（必填）" />
            <input id="newPostLocation" placeholder="地点（可选）" />
            <button onclick="createPost()">发布</button>
          </div>
        </div>

        <!-- MONSTER -->
        <div id="monster" class="card page" style="display:none;">
          <h3>怪物档案（纯文字）</h3>
          <div class="muted">初始：丧尸</div>
          <div style="margin-top:8px;">
            <input id="newMonsterName" placeholder="怪物名称" />
            <input id="newMonsterDesc" placeholder="简短描述" />
            <button onclick="addMonster()">新增怪物</button>
          </div>
          <div id="monsterList" style="margin-top:12px;"></div>
        </div>

        <!-- RESOURCE -->
        <div id="resource" class="card page" style="display:none;">
          <h3>资源记录 / 交易帖（纯文字）</h3>
          <textarea id="newResContent" rows="3" placeholder="资源内容/交易说明，例如：携带 2 罐午餐肉，想在 B镇火车站换 10 升汽油"></textarea>
          <input id="newResLocation" placeholder="地点" />
          <button onclick="createResource()">记录发布</button>
          <div id="resourceList" style="margin-top:12px;"></div>
        </div>

        <!-- PROFILE -->
        <div id="profile" class="card page" style="display:none;">
          <h3>角色页面</h3>
          <div class="flex">
            <div class="col">
              <div id="charSummary"></div>
              <div style="margin-top:8px;">
                <label>本日是否进行“大范围移动”？</label>
                <select id="movedToday">
                  <option value="0">否（默认）</option>
                  <option value="1">是（+消耗）</option>
                </select>
                <label style="display:block; margin-top:6px;">今天睡眠（小时）</label>
                <input id="sleepHrs" type="number" min="0" max="24" value="6" />
                <label style="display:block; margin-top:6px;">是否补给水/食（手动消耗背包资源以满足今日需求）</label>
                <button onclick="autoConsume()">自动消耗当日所需</button>
                <div style="margin-top:8px;">
                  <button onclick="advanceDay()">推进一天（结算今日状况）</button>
                  <button class="secondary" onclick="dieNow()">自杀（测试死亡继承）</button>
                </div>
              </div>
            </div>
            <div class="col">
              <h4>背包（物品与数量）</h4>
              <div id="inventoryList" class="muted tiny"></div>
              <h4 style="margin-top:8px;">Debuffs / 状态</h4>
              <div id="statusList" class="muted tiny"></div>
              <h4 style="margin-top:8px;">来时路（死去角色）</h4>
              <div id="comeFromList" class="muted tiny"></div>
            </div>
          </div>
        </div>

      </div>
    </div>
  </div>

<script>
/* -------------------------
  数据存储（localStorage）
  key: "ms_user_db" 保存所有用户 {email:{password, profile, char?}}
  每个用户结构示例：
  {
    password: "...",
    nickname: "...",
    email: "...",
    char: { alive:true/false, name, gender, age, profession, buffs:[], debuffs:[], location, envDesc, appearance:[], inventory:{item:count}, daysAlive:0, status:{hunger:0,fatigue:0,sunExposure:0} , comeFrom: [] }
  }
-------------------------*/

const PROF_LIST = ["奶茶店店员","心理医生","急诊科护士","上班族","大学生","游泳教练","程序员","农民","无业游民","网红","教师","开锁师傅"];
const LOC_POOL = [
  // A城若干
  "A城·旧街口", "A城·文化街", "A城·老邮局", "A城·豆腐坊",
  // 主城区（C市）
  "C市·行政中心", "C市·商业大道", "C市·地铁口", "C市·高新区边缘",
  // 工业区/郊区 / B镇
  "工业区·仓储厂房", "郊区·菜地边", "B镇·火车站", "B镇·仓库群"
];
const ENV_POOL = [
  "废墟寂静，远处有阵阵金属摩擦声",
  "空气中弥漫焦糊味，天空灰蒙",
  "下着灰色细雨，视线受限",
  "夜幕降临，远处偶有光点",
  "晨雾未散，地面泥泞"
];

// 五大类词库（各选若干供随机挑选）
const AP_FACE = ["吊眼","卧蚕","丹凤眼","桃花眼","下垂眼","单眼皮","双眼皮","浓眉","淡眉","断眉","剑眉","柳眉","塌鼻","鹰鼻","翘鼻","圆鼻","尖下巴","圆下巴","宽额","方额","长脸","鹅蛋脸","眉目疏朗","眉峰凌厉","五官端正"];
const AP_SKIN = ["苍白肤","黝黑肤","古铜肤","麦色肤","冷白肤","黄皮肤","红肤色","青白肤","灰白肤","浅棕肤","深棕肤","肤色通透","肤色晦暗","肤色不均","肤若凝脂","皮肤光洁","肌理细腻","面色红润","面色惨白","面色蜡黄","面色清冷","肤色健康","肤色泛红","肤质粗糙","肤色病态"];
const AP_SCAR = ["刀疤","烧痕","胎记","泪痣","雀斑","酒窝","唇痣","眉心印","鬓角痣","颧侧痣","下巴痣","鼻梁痣","眼下痣","额心疤","唇角裂","眉疤","唇裂","唇钉","耳钉","鼻环","针眼痕","唇角痕","烫痕淡","嘴角印","唇角常裂","额角伤痕"];
const AP_RACE = ["东亚相","南岛相","欧罗面","混血感","亚洲肤","高鼻梁","深眼窝","平颧骨","短面型","立体面","宽脸骨","窄脸骨","颊骨外突","颧高面阔","鼻梁笔直","鼻翼宽阔","额骨平缓","眉弓突出","颧骨低平","下颌坚挺","骨相柔和","骨线分明","面骨深刻","骨感立体","肤骨相衬"];
const AP_IMPRESS = ["眼神凌厉","眼角含笑","眉目紧凑","眉目清朗","五官精致","五官深刻","面容清秀","面目狰狞","面色温柔","神情寡淡","神态倦怠","神色阴沉","神色恍惚","气质冰冷","神情温柔","面容端雅","神态拘谨","表情迟钝","笑容明快","表情冷淡"];

const ITEM_POOL = [
  // 食品
  "压缩饼干","午餐肉罐头","什锦蔬菜罐头","番茄罐头","玉米罐头","牛肉罐头","鸡肉罐头","海鲜罐头","脱水蔬菜","食用盐","糖",
  // 医疗
  "沐浴露","洗发液","洗衣粉","毛巾","家用急救箱","复合维生素","维生素C药片","钙片","胃药","抗生素","退烧止痛药","咳嗽药","止泻药","开塞露","驱蚊花露水","绷带","碘伏","碘酒","创可贴","棉签","电子体温计","卫生巾","肥皂","牙膏","牙刷","塑料袋","卫生纸","暖宝宝","外科手术刀全套",
  // 工具
  "木板","钉子","隔音板","万能胶大管","撬棍","瓦斯罐","防暴钢叉","专业五金工具箱","折叠刀","发电机","指南针","登山绳","登山扣","护目镜","夜视眼镜","多功能救生斧","手摇电筒","基础维修工具","雨伞","雨衣",
  // 燃料与水
  "大桶装矿泉水","小瓶装矿泉水","汽油",
  // 书籍
  "国家地理","无国界医生手册","民兵训练手册","五金手册","法治的细节","演员的自我修养","活着",
  // 防护
  "灭火器","枪","子弹"
];

// 初始怪物数据库
let monsterDB = JSON.parse(localStorage.getItem("ms_monsters") || 'null');
if(!monsterDB){ monsterDB = [{name:"丧尸", desc:"腐烂的行尸，靠近会攻击。"}]; localStorage.setItem("ms_monsters", JSON.stringify(monsterDB)); }

/* ---------- 帮助函数 ---------- */
function loadDB(){ return JSON.parse(localStorage.getItem("ms_user_db") || '{}'); }
function saveDB(db){ localStorage.setItem("ms_user_db", JSON.stringify(db)); }
function uid(len=8){ return Math.random().toString(36).slice(2,2+len); }
function randChoice(arr){ return arr[Math.floor(Math.random()*arr.length)]; }
function randInt(a,b){ return Math.floor(Math.random()*(b-a+1))+a; }

/* ---------- UI — 登入注册区 ---------- */
function renderAuth(){
  const db = loadDB();
  const html = `
    <h2>登录 / 注册（邮箱+密码）</h2>
    <div style="display:flex; gap:12px;">
      <div style="flex:1;">
        <div class="muted">注册</div>
        <input id="regEmail" placeholder="邮箱" />
        <input id="regPass" placeholder="密码" type="password" />
        <input id="regNick" placeholder="昵称（在末世使用）" />
        <label>性别</label>
        <select id="regGender"><option value="男">男</option><option value="女">女</option><option value="其他">其他</option></select>
        <button onclick="doRegister()">注册并创建角色</button>
        <div class="small muted">（注册后会生成随机年龄、职业、地点与背包）</div>
      </div>
      <div style="flex:1;">
        <div class="muted">登录</div>
        <input id="logEmail" placeholder="邮箱" />
        <input id="logPass" placeholder="密码" type="password" />
        <button onclick="doLogin()">登录</button>
        <div style="margin-top:8px;">
          <div class="small muted">已存在账号示例：可重复测试用相同邮箱</div>
        </div>
      </div>
    </div>
    <hr />
    <div class="small muted">注意：本原型所有数据保存在你本地浏览器的 localStorage；别人无法访问除非你把文件导出或把本项目放到线上。</div>
  `;
  document.getElementById("authArea").innerHTML = html;
}
renderAuth();

/* ---------- 注册与角色生成 ---------- */
function doRegister(){
  const email = document.getElementById("regEmail").value.trim();
  const pass = document.getElementById("regPass").value;
  const nick = document.getElementById("regNick").value.trim() || "匿名幸存者";
  const gender = document.getElementById("regGender").value;
  if(!email || !pass){ alert("请填写邮箱和密码"); return; }
  const db = loadDB();
  if(db[email]){ alert("该邮箱已注册，请直接登录"); return; }

  // 生成角色
  const age = randInt(21,35);
  const profession = randChoice(PROF_LIST);
  const location = randChoice(LOC_POOL);
  const envDesc = randChoice(ENV_POOL);
  // appearance: 从五类各选2，再随机打乱成10个，玩家后续选3
  const ap = [];
  function pickTwo(arr){ let a=randChoice(arr); let b=randChoice(arr); while(b===a) b=randChoice(arr); return [a,b]; }
  ap.push(...pickTwo(AP_FACE)); ap.push(...pickTwo(AP_SKIN)); ap.push(...pickTwo(AP_SCAR)); ap.push(...pickTwo(AP_RACE)); ap.push(...pickTwo(AP_IMPRESS));
  // 背包：20-40物品，随机挑选并计数
  const inv = {};
  const count = randInt(20,40);
  for(let i=0;i<count;i++){
    const it = randChoice(ITEM_POOL);
    inv[it] = (inv[it]||0)+1;
  }
  // 初始状态
  const char = {
    alive: true,
    name: nick,
    gender,
    age,
    profession,
    buffs: [], debuffs: [],
    location,
    envDesc,
    appearanceOptions: ap, // 10 options
    appearanceChosen: [], // 玩家选3
    inventory: inv,
    daysAlive: 0,
    status: { hunger:0, fatigue:0, sunExposure:0 }, // 数值用于判断
    comeFrom: [] // 记录死去角色名
  };

  db[email] = { password: pass, email, nickname: nick, char };
  saveDB(db);
  alert("注册并生成角色成功！请登录以进入游戏。");
  document.getElementById("regEmail").value=""; document.getElementById("regPass").value=""; document.getElementById("regNick").value="";
}

function doLogin(){
  const email = document.getElementById("logEmail").value.trim();
  const pass = document.getElementById("logPass").value;
  const db = loadDB();
  if(!db[email] || db[email].password !== pass){ alert("邮箱或密码错误"); return; }
  // 登录
  window.currentUser = email;
  showApp();
}

/* ---------- 应用主流程 ---------- */
function showApp(){
  document.getElementById("authArea").style.display='none';
  document.getElementById("appArea").style.display='block';
  updateHeader();
  // Ensure character appearance chosen: if not yet chosen, show selection flow
  const db = loadDB();
  const me = db[window.currentUser];
  if(!me.char) { alert("无角色，请重新注册"); renderAuth(); return; }
  if(me.char.appearanceChosen.length < 3){
    // 展示选择界面让玩家挑3个
    chooseAppearance();
  } else {
    nav('home');
    renderProfile();
    renderMonsters();
    renderHome();
    renderResources();
  }
  updateHeader();
}

function updateHeader(){
  const db = loadDB();
  const me = db[window.currentUser];
  document.getElementById("headerRight").innerHTML = me ? `<span class="muted">欢迎，${me.nickname}</span>` : '';
}

/* ---------- 外部导航 ---------- */
function nav(page){
  document.querySelectorAll('.page').forEach(p=>p.style.display='none');
  document.getElementById(page).style.display='block';
  if(page==='profile') renderProfile();
  if(page==='monster') renderMonsters();
  if(page==='home') renderHome();
  if(page==='resource') renderResources();
}

/* ---------- 外：选择appearance（第一次登录） ---------- */
function chooseAppearance(){
  const db = loadDB();
  const me = db[window.currentUser];
  const opts = me.char.appearanceOptions; // 10
  let html = `<div><h3>角色外貌 — 从下面 10 项中选择 **3 项** 作为你的角色描述（必须）</h3>`;
  html += `<div class="grid-3">`;
  opts.forEach((o,idx)=> html += `<div class="card tiny"><input type="checkbox" id="ap${idx}" onchange="toggleAp(${idx})" /> <label for="ap${idx}">${o}</label></div>`);
  html += `</div><div style="margin-top:8px;"><button onclick="finishAppearance()">确认（选满3项后即可）</button></div></div>`;
  document.getElementById("authArea").style.display='block';
  document.getElementById("authArea").innerHTML = html;
  document.getElementById("appArea").style.display='none';
  window.tempApChosen = [];
}
function toggleAp(i){
  if(!window.tempApChosen) window.tempApChosen=[];
  const idx = window.tempApChosen.indexOf(i);
  if(idx>=0) window.tempApChosen.splice(idx,1);
  else {
    if(window.tempApChosen.length>=3){ alert("只能选 3 项"); 
      // uncheck last checked visually
      document.getElementById(`ap${i}`).checked=false;
      return; 
    }
    window.tempApChosen.push(i);
  }
}
function finishAppearance(){
  if(!window.tempApChosen || window.tempApChosen.length !==3){ alert("请选 3 项"); return; }
  const db = loadDB(); const me = db[window.currentUser];
  me.char.appearanceChosen = window.tempApChosen.map(i => me.char.appearanceOptions[i]);
  saveDB(db);
  // 回到 app
  document.getElementById("authArea").style.display='none';
  document.getElementById("appArea").style.display='block';
  nav('home');
  renderProfile();
  renderMonsters();
  renderHome();
  renderResources();
}

/* ---------- HOME 帖子与评论 ---------- */
function createPost(){
  const title = document.getElementById("newPostTitle").value.trim();
  const content = document.getElementById("newPostContent").value.trim();
  const loc = document.getElementById("newPostLocation").value.trim();
  if(!title || !content){ alert("请填写标题和内容"); return; }
  const posts = JSON.parse(localStorage.getItem("ms_posts") || '[]');
  const db = loadDB(); const me = db[window.currentUser];
  const post = {
    id: uid(10), author: me.nickname, email: window.currentUser, title, content, loc, time: new Date().toISOString(), comments:[]
  };
  posts.unshift(post);
  localStorage.setItem("ms_posts", JSON.stringify(posts));
  document.getElementById("newPostTitle").value=''; document.getElementById("newPostContent").value='';
  renderHome();
}
function renderHome(){
  const posts = JSON.parse(localStorage.getItem("ms_posts") || '[]');
  let html = `<h3>社区贴子（纯文本）</h3>`;
  html += `<div class="small muted">发布/评论请遵守末世公约（纯文本，无图片）</div>`;
  html += `<div style="margin-top:12px;">`;
  posts.forEach(p=>{
    html += `<div class="post"><strong>${p.title}</strong> <span class="muted">— ${p.author} • ${p.loc||'未知地点'} • ${new Date(p.time).toLocaleString()}</span>`;
    html += `<div style="margin-top:6px;">${escapeHtml(p.content)}</div>`;
    html += `<div style="margin-top:6px;"><span class="linklike" onclick='openComments("${p.id}")'>评论 (${p.comments.length})</span></div>`;
    html += `</div>`;
  });
  html += `</div>`;
  document.getElementById("home").innerHTML = html;
}
function openComments(id){
  const posts = JSON.parse(localStorage.getItem("ms_posts") || '[]');
  const p = posts.find(x=>x.id===id);
  if(!p) return;
  let s = `<h4>${p.title} — 评论</h4><div class="muted">原帖：${escapeHtml(p.content)}</div><div style="margin-top:8px;">`;
  p.comments.forEach(c=> s+=`<div class="card tiny">${c.author}：${escapeHtml(c.text)} <div class="muted small">${new Date(c.time).toLocaleString()}</div></div>`);
  s+=`</div><div style="margin-top:8px;"><input id="replyText" placeholder="写评论..." /> <button onclick='addComment("${p.id}")'>发表评论</button></div>`;
  alertHTML(s);
}
function addComment(postId){
  const text = document.getElementById("replyText").value.trim();
  if(!text){ alert("评论不能为空"); return; }
  const posts = JSON.parse(localStorage.getItem("ms_posts") || '[]');
  const p = posts.find(x=>x.id===postId);
  const db = loadDB(); const me = db[window.currentUser];
  p.comments.push({ author: me.nickname, text, time: new Date().toISOString() });
  localStorage.setItem("ms_posts", JSON.stringify(posts));
  renderHome();
  closeAlertHTML();
}

/* ---------- MONSTER ---------- */
function renderMonsters(){
  monsterDB = JSON.parse(localStorage.getItem("ms_monsters") || '[]');
  let html = `<div>`;
  monsterDB.forEach(m => html += `<div class="card tiny"><strong>${m.name}</strong> — ${escapeHtml(m.desc)}</div>`);
  html += `</div>`;
  document.getElementById("monsterList").innerHTML = html;
}
function addMonster(){
  const n = document.getElementById("newMonsterName").value.trim();
  const d = document.getElementById("newMonsterDesc").value.trim();
  if(!n || !d){ alert("名称和描述都要填"); return; }
  monsterDB.push({name:n, desc:d});
  localStorage.setItem("ms_monsters", JSON.stringify(monsterDB));
  document.getElementById("newMonsterName").value=''; document.getElementById("newMonsterDesc").value='';
  renderMonsters();
}

/* ---------- RESOURCE / 交易 ---------- */
function createResource(){
  const content = document.getElementById("newResContent").value.trim();
  const loc = document.getElementById("newResLocation").value.trim();
  if(!content){ alert("请填写内容"); return; }
  const res = JSON.parse(localStorage.getItem("ms_resources") || '[]');
  const db = loadDB(); const me = db[window.currentUser];
  res.unshift({id:uid(8), author:me.nickname, content, loc, time:new Date().toISOString()});
  localStorage.setItem("ms_resources", JSON.stringify(res));
  document.getElementById("newResContent").value=''; document.getElementById("newResLocation").value='';
  renderResources();
}
function renderResources(){
  const res = JSON.parse(localStorage.getItem("ms_resources") || '[]');
  let html = `<h4>资源与交易</h4>`;
  res.forEach(r => {
    html += `<div class="card tiny"><strong>${escapeHtml(r.content)}</strong><div class="muted">${r.author} • ${r.loc||'无地点'} • ${new Date(r.time).toLocaleString()}</div></div>`;
  });
  document.getElementById("resourceList").innerHTML = html;
}

/* ---------- PROFILE / 生存系统 ---------- */
function renderProfile(){
  const db = loadDB(); const me = db[window.currentUser]; const c = me.char;
  document.getElementById("meName").innerText = me.nickname;
  document.getElementById("meEmail").innerText = me.email;
  document.getElementById("meCharName").innerText = c.name;
  document.getElementById("meCharGender").innerText = c.gender;
  document.getElementById("meCharAge").innerText = c.age;
  document.getElementById("meLoc").innerText = c.location + " · " + c.envDesc;
  document.getElementById("meDays").innerText = c.daysAlive;

  // summary
  let summ = `<div><strong>${c.name}</strong>（${c.gender}）· ${c.age}岁 · 职业：${c.profession}</div>`;
  summ += `<div class="muted small">外貌：${c.appearanceChosen.join('、')}</div>`;
  summ += `<div class="muted small">buffs: ${c.buffs.join(',') || '无'}；debuffs: ${c.debuffs.join(',') || '无'}</div>`;
  summ += `<div style="margin-top:8px;"><strong>当前环境</strong><div class="muted">${c.location} — ${c.envDesc}</div></div>`;
  document.getElementById("charSummary").innerHTML = summ;

  // inventory
  let invHtml = '';
  for(const k in c.inventory) invHtml += `${k} × ${c.inventory[k]}<br/>`;
  document.getElementById("inventoryList").innerHTML = invHtml || '<div class="muted">空</div>';

  // status list
  let st = `饥饿值：${c.status.hunger} / 疲劳值：${c.status.fatigue} / 阳光暴露：${c.status.sunExposure}`;
  document.getElementById("statusList").innerText = st;

  // comeFrom
  document.getElementById("comeFromList").innerHTML = (c.comeFrom.length? c.comeFrom.map(x=>`<div>${escapeHtml(x)}</div>`).join('') : '<div class="muted">暂无</div>');
}

/* 消耗规则与推进日数逻辑
- 每日需水 1.5L，需食 2 罐（1罐 = 4 压缩饼干）
- 若大范围移动，食物需求变为 3 罐
- 背包转换规则：大桶装矿泉水 = 20L；小瓶装矿泉水 = 0.5L
- 压缩饼干 = 0.25 罐头
- 计算是否满足，未满足会增加 hunger 值（每缺少 1 单位罐头 -> hunger+1），三天累计触发更严重 debuff 等
- 睡眠低于4小时 -> fatigue 增加
- hunger >=3 且持续2天 -> 出现眩晕（simulated by inability to detect approaching monsters — here we just tag debuff）
- hunger >=5 -> 死亡
- fatigue >=5 -> 死亡
- 阳光暴露：如果日常在外（simple check：envDesc包含"光"或用户没有遮蔽）暂不复杂化，若持续高值同样会有风险
*/

function computeConsumables(inv){
  // returns {waterLiters, cansEquivalent}
  let water=0, cans=0;
  for(const k in inv){
    const v = inv[k];
    if(k==="大桶装矿泉水") water += 20 * v;
    else if(k==="小瓶装矿泉水") water += 0.5 * v;
    else if(k.includes("罐头")) cans += 1 * v;
    else if(k==="压缩饼干") cans += 0.25 * v;
    // 其它食品暂不转换为罐头（可拓展）
  }
  return { waterLiters: water, cansEquivalent: cans };
}

function autoConsume(){
  // 尝试从背包中自动扣除当日所需资源（优先用罐头与小瓶水/大桶）
  const db = loadDB(); const me = db[window.currentUser]; const c = me.char;
  const moved = document.getElementById("movedToday").value==='1';
  const needCans = moved ? 3 : 2;
  const needWater = 1.5;
  // consume water
  // prefer 小瓶装矿泉水 then 大桶装矿泉水
  function consumeItem(name, num){
    if(c.inventory[name]){
      const take = Math.min(num, c.inventory[name]);
      c.inventory[name] -= take;
      if(c.inventory[name] <=0) delete c.inventory[name];
      return take;
    }
    return 0;
  }
  let gotWater = 0;
  // use small bottles (0.5L each)
  if(c.inventory["小瓶装矿泉水"]) {
    const needBottles = Math.ceil((needWater)/0.5);
    const used = consumeItem("小瓶装矿泉水", needBottles);
    gotWater += used * 0.5;
  }
  if(gotWater < needWater && c.inventory["大桶装矿泉水"]){
    // use barrels as needed
    const needBarrels = Math.ceil((needWater - gotWater) / 20);
    const used = consumeItem("大桶装矿泉水", needBarrels);
    gotWater += used*20;
  }
  // If still short, we fail to fully satisfy water
  // consume cans: prefer full 罐头 then 压缩饼干（0.25罐/个）
  function consumeCans(num){
    let remaining = num;
    // full cans
    const fullCans = Object.keys(c.inventory).filter(k=>k.includes("罐头")).sort();
    for(const k of fullCans){
      if(remaining<=0) break;
      const used = Math.min(c.inventory[k], remaining);
      c.inventory[k] -= used; remaining -= used;
      if(c.inventory[k]<=0) delete c.inventory[k];
    }
    // compressed biscuits
    if(remaining > 0 && c.inventory["压缩饼干"]){
      const neededBiscuits = Math.ceil(remaining / 0.25);
      const used = Math.min(c.inventory["压缩饼干"], neededBiscuits);
      c.inventory["压缩饼干"] -= used;
      const covered = used * 0.25;
      remaining -= covered;
      if(c.inventory["压缩饼干"]<=0) delete c.inventory["压缩饼干"];
    }
    return remaining <= 0;
  }
  const hadCans = computeConsumables(c.inventory).cansEquivalent; // NOTE: uses current inv BEFORE consumption; but we will attempt consume
  const satisfiedFood = consumeCans(needCans);
  // check water satisfied
  const waterAfter = computeConsumables(c.inventory).waterLiters; // actually after consumption; but since we consumed directly using consumeItem, waterAfter is after usage
  const waterSatisfied = (gotWater >= needWater) || (waterAfter >= needWater);
  // Update hunger/fatigue depending on satisfaction
  if(!waterSatisfied || !satisfiedFood){
    // set hunger increase proportionally
    c.status.hunger += (!satisfiedFood?1:0) + (!waterSatisfied?1:0);
  } else {
    // satisfied -> reduce hunger slowly
    c.status.hunger = Math.max(0, c.status.hunger - 1);
  }
  // sleep check not handled here; user sets sleepHrs field and advanceDay will process
  saveDB(db);
  renderProfile();
  alert("尝试自动消耗已执行（若背包资源不足则会造成饥饿）");
}

function advanceDay(){
  const db = loadDB(); const me = db[window.currentUser]; const c = me.char;
  // 获取用户输入
  const moved = document.getElementById("movedToday").value==='1';
  const sleep = Number(document.getElementById("sleepHrs").value) || 0;

  // 每日被动消耗：水1.5L 与 2罐（或3罐）
  const needCans = moved ? 3 : 2;
  const needWater = 1.5;

  // 先尝试自动扣除（如果背包足够）
  // We'll do a best-effort consumption using same logic as autoConsume but without alert
  function consumeItemDirect(name, num){
    if(c.inventory[name]){
      const take = Math.min(num, c.inventory[name]);
      c.inventory[name] -= take;
      if(c.inventory[name] <=0) delete c.inventory[name];
      return take;
    }
    return 0;
  }
  let gotWater = 0;
  // small bottles
  if(c.inventory["小瓶装矿泉水"]) {
    const needBottles = Math.ceil((needWater)/0.5);
    const used = consumeItemDirect("小瓶装矿泉水", needBottles);
    gotWater += used * 0.5;
  }
  if(gotWater < needWater && c.inventory["大桶装矿泉水"]){
    const needBarrels = Math.ceil((needWater - gotWater) / 20);
    const used = consumeItemDirect("大桶装矿泉水", needBarrels);
    gotWater += used*20;
  }
  // consume cans
  function consumeCansDirect(num){
    let remaining = num;
    // full cans
    const fullCans = Object.keys(c.inventory).filter(k=>k.includes("罐头"));
    for(const k of fullCans){
      if(remaining<=0) break;
      const used = Math.min(c.inventory[k], remaining);
      c.inventory[k] -= used; remaining -= used;
      if(c.inventory[k]<=0) delete c.inventory[k];
    }
    // compressed biscuits
    if(remaining > 0 && c.inventory["压缩饼干"]){
      const neededBiscuits = Math.ceil(remaining / 0.25);
      const used = Math.min(c.inventory["压缩饼干"], neededBiscuits);
      c.inventory["压缩饼干"] -= used;
      const covered = used * 0.25;
      remaining -= covered;
      if(c.inventory["压缩饼干"]<=0) delete c.inventory["压缩饼干"];
    }
    return remaining <= 0;
  }
  const foodOk = consumeCansDirect(needCans);
  const waterAfter = computeConsumables(c.inventory).waterLiters;
  const waterOk = (gotWater >= needWater) || (waterAfter >= needWater);

  if(!foodOk) c.status.hunger += 1;
  else c.status.hunger = Math.max(0, c.status.hunger - 1);
  if(!waterOk) c.status.hunger += 1; // water shortage increases hunger indicator too
  // sleep
  if(sleep < 4) c.status.fatigue += 1;
  else c.status.fatigue = Math.max(0, c.status.fatigue - 1);

  // 阳光暴露：如果 envDesc 包含 "光" 或 "日" 则增加，否则不变（简化）
  if(c.envDesc.includes("光") || c.envDesc.includes("日")) c.status.sunExposure += 1;
  else c.status.sunExposure = Math.max(0, c.status.sunExposure - 1);

  c.daysAlive += 1;

  // 处理 debuffs/popups
  // hunger >=3 且持续两日 -> add '眩晕' 标签
  if(c.status.hunger >= 3) { if(!c.debuffs.includes("饥饿")) c.debuffs.push("饥饿"); }
  if(c.status.hunger >= 4 && !c.debuffs.includes("眩晕")) c.debuffs.push("眩晕");
  if(c.status.hunger >= 5){ // 死亡：饿死
    doDeath("饿死");
    return;
  }
  if(c.status.fatigue >= 3) { if(!c.debuffs.includes("疲劳")) c.debuffs.push("疲劳"); }
  if(c.status.fatigue >= 5){ doDeath("疲劳过度猝死"); return; }

  // 阳光暴露变成危险（简化）
  if(c.status.sunExposure >= 5){ if(!c.debuffs.includes("阳光灿烂")) c.debuffs.push("阳光灿烂"); }
  if(c.status.sunExposure >=8){ doDeath("长时间阳光暴露导致变异死亡"); return; }

  // 保存并提示
  saveDB(db);
  renderProfile();
  alert("已推进一天。请查看角色状态并决定是否补给/移动。");
}

/* 死亡与继承 */
function doDeath(reason){
  const db = loadDB(); const me = db[window.currentUser]; const c = me.char;
  c.alive = false;
  // 记录来时路
  c.comeFrom.push(`${c.name}（${c.daysAlive}日） - ${reason} @ ${c.location}`);
  // 拿着当前背包作为遗物（新角色继承） —— 这一版本我们把背包留在用户数据中并在创建新角色时复制
  saveDB(db);
  alert(`角色已死亡：${reason}。将返回角色创建界面，新角色会继承死者背包与地点。`);
  // 返回创建界面（保留账号）
  logoutToCreate();
}

function dieNow(){ if(confirm("确定强制令角色死亡以测试继承？")) doDeath("自杀（测试）"); }

/* 注销但不删除账号：登出后回到创建/登录 */
function logout(){
  window.currentUser = null;
  document.getElementById("appArea").style.display='none';
  document.getElementById("authArea").style.display='block';
  renderAuth();
}
function logoutToCreate(){
  // keep logged in but show creation flow: we will create a new character while preserving dead char's inventory as inheritance
  const db = loadDB(); const me = db[window.currentUser];
  // build UI for creating new character that will inherit previous backpack & location
  const prev = me.char;
  let html = `<h3>创建新角色（继承已死角色的背包与地点）</h3>`;
  html += `<div class="muted">你上一个角色 <strong>${prev.name}</strong> 在 ${prev.location} 死亡，背包将被继承。</div>`;
  html += `<input id="newNick" placeholder="新昵称（必填）" />`;
  html += `<label>性别</label><select id="newGender"><option value="男">男</option><option value="女">女</option><option value="其他">其他</option></select>`;
  html += `<div style="margin-top:8px;"><button onclick="createInheritedCharacter()">创建并进入游戏</button></div>`;
  document.getElementById("authArea").innerHTML = html;
  document.getElementById("authArea").style.display='block';
  document.getElementById("appArea").style.display='none';
}

function createInheritedCharacter(){
  const nick = document.getElementById("newNick").value.trim() || "匿名幸存者";
  const gender = document.getElementById("newGender").value;
  const db = loadDB(); const me = db[window.currentUser];
  const prev = me.char;
  // create new char but inherit inventory & location & comeFrom
  const age = randInt(21,35);
  const profession = randChoice(PROF_LIST);
  const location = prev.location || randChoice(LOC_POOL);
  const envDesc = randChoice(ENV_POOL);
  // appearance options new
  const ap = [];
  function pickTwo(arr){ let a=randChoice(arr); let b=randChoice(arr); while(b===a) b=randChoice(arr); return [a,b]; }
  ap.push(...pickTwo(AP_FACE)); ap.push(...pickTwo(AP_SKIN)); ap.push(...pickTwo(AP_SCAR)); ap.push(...pickTwo(AP_RACE)); ap.push(...pickTwo(AP_IMPRESS));
  // inventory copy
  const inv = JSON.parse(JSON.stringify(prev.inventory || {}));
  const char = {
    alive: true,
    name: nick,
    gender,
    age,
    profession,
    buffs: [], debuffs: [],
    location,
    envDesc,
    appearanceOptions: ap,
    appearanceChosen: [],
    inventory: inv,
    daysAlive: 0,
    status: { hunger:0, fatigue:0, sunExposure:0 },
    comeFrom: prev.comeFrom || []
  };
  me.nickname = nick; // update display name
  me.char = char;
  saveDB(db);
  // back to choosing appearance
  window.currentUser = window.currentUser; // keep
  showApp();
}

/* ---------- 工具：安全弹窗与HTML转义 ---------- */
let alertOverlay = null;
function alertHTML(html){
  // create simple modal
  if(alertOverlay) closeAlertHTML();
  alertOverlay = document.createElement('div');
  alertOverlay.style.position='fixed'; alertOverlay.style.left=0; alertOverlay.style.top=0; alertOverlay.style.right=0; alertOverlay.style.bottom=0;
  alertOverlay.style.background='rgba(0,0,0,0.4)'; alertOverlay.style.zIndex=9999; alertOverlay.style.display='flex'; alertOverlay.style.alignItems='center'; alertOverlay.style.justifyContent='center';
  const box = document.createElement('div'); box.style.background='white'; box.style.padding='16px'; box.style.borderRadius='8px'; box.style.maxWidth='700px'; box.style.maxHeight='80%'; box.style.overflow='auto';
  box.innerHTML = html + `<div style="margin-top:8px;text-align:right;"><button onclick="closeAlertHTML()">关闭</button></div>`;
  alertOverlay.appendChild(box);
  document.body.appendChild(alertOverlay);
}
function closeAlertHTML(){ if(alertOverlay){ document.body.removeChild(alertOverlay); alertOverlay=null; } }
function escapeHtml(s){ if(!s) return ''; return s.replace(/&/g,'&amp;').replace(/</g,'&lt;').replace(/>/g,'&gt;'); }

/* 启动时检查是否已有登录状态（sessionless: none） */
if(window.currentUser){ showApp(); } else { renderAuth(); }

</script>
</body>
</html>

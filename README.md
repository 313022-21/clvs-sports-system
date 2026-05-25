<!DOCTYPE html>
<html lang="zh-TW">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>中壢家商體育組管理系統</title>
  
  <script src="https://cdn.tailwindcss.com"></script>
  
  <script crossorigin src="https://unpkg.com/react@18/umd/react.development.js"></script>
  <script crossorigin src="https://unpkg.com/react-dom@18/umd/react-dom.development.js"></script>
  
  <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
  
  <script src="https://code.iconify.design/iconify-icon/1.0.7/iconify-icon.min.js"></script>

  <style>
    body { font-family: 'Helvetica Neue', Helvetica, Arial, '微軟正黑體', sans-serif; }
    .custom-scrollbar::-webkit-scrollbar { width: 6px; }
    .custom-scrollbar::-webkit-scrollbar-track { background: transparent; }
    .custom-scrollbar::-webkit-scrollbar-thumb { background: #cbd5e1; border-radius: 10px; }
    
    @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }
    @keyframes slideInUp { from { opacity: 0; transform: translateY(1rem); } to { opacity: 1; transform: translateY(0); } }
    @keyframes zoomIn { from { opacity: 0; transform: scale(0.95); } to { opacity: 1; transform: scale(1); } }
    
    .fade-in { animation: fadeIn 0.3s ease-out forwards; }
    .slide-in-from-bottom-4 { animation: slideInUp 0.4s ease-out forwards; }
    .zoom-in-95 { animation: zoomIn 0.3s ease-out forwards; }
  </style>
</head>
<body>
  <div id="root"></div>

  <script type="text/babel">
    const { useState, useEffect } = React;

    const createIcon = (name) => ({ size = 24, className = '' }) => (
      <iconify-icon icon={`lucide:${name}`} width={size} height={size} class={className}></iconify-icon>
    );

    const Trophy = createIcon('trophy');
    const LayoutDashboard = createIcon('layout-dashboard');
    const ClipboardList = createIcon('clipboard-list');
    const LogOut = createIcon('log-out');
    const Plus = createIcon('plus');
    const Trash2 = createIcon('trash-2');
    const Edit3 = createIcon('edit-3');
    const Save = createIcon('save');
    const UserCheck = createIcon('user-check');
    const X = createIcon('x');
    const BookOpen = createIcon('book-open');
    const Megaphone = createIcon('megaphone');
    const Calendar = createIcon('calendar');
    const MapPin = createIcon('map-pin');
    const Clock = createIcon('clock');
    const IdCard = createIcon('id-card');
    const User = createIcon('user');
    const Lock = createIcon('lock');
    const ChevronRight = createIcon('chevron-right');
    const Info = createIcon('info');
    const AlertTriangle = createIcon('alert-triangle');
    const Send = createIcon('send');
    const CheckCircle = createIcon('check-circle');
    const Menu = createIcon('menu');
    const Settings = createIcon('settings');
    const Copy = createIcon('copy');
    const Users = createIcon('users');

    const ROLES = { STUDENT: 'student', CAPTAIN: 'captain', DIRECTOR: 'director' };

    const CLASS_LIST = [
      "商一甲", "商一乙", "商一丙", "英一甲", "資一甲", "家一甲", "家一乙", "服一甲", "服一乙",
      "商二甲", "商二乙", "商二丙", "英二甲", "資二甲", "家二甲", "家二乙", "服二甲", "服二乙",
      "商三甲", "商三乙", "商三丙", "英三甲", "資三甲", "家三甲", "家三乙", "服三甲", "服三乙"
    ];

    const COMPETITION_TEMPLATES = {
      cheerleading: {
        name: "壢家班際舞蹈(啦啦隊)比賽", category: "舞蹈/團體", limit: 42, location: "本校操場",
        rules: `【實施方式】\n1. 高一組：進場校歌唱歌、健康操【超越顛峰】退場。\n2. 高二組：進場啦啦舞退場。\n\n【時間規定】\n1. 高一組：進退場合計 1 分鐘為限。\n2. 高二組：時間以 4分00秒 至 5分30秒 為限。\n3. 違反規定時間，每 30 秒扣總平均 1 分。\n\n【評分標準】\n1. 動作準確性、熟練度、韻律感、流暢度。\n2. 口白表現、服裝特色、團隊精神。\n\n【安全守則】\n1. 不可赤足或著襪子。\n2. 禁止配戴戒指、舌環或鼻環等危險飾品。\n3. 旋轉必須以足部支撐，禁止頭部旋轉。\n4. 抬舉高度不可超過 2層1.5段。`
      },
      rope_skipping: {
        name: "壢家班際跳繩比賽", category: "民俗體育/團體", limit: 15, location: "操場",
        rules: `【參賽資格與人數】\n1. 對象：本校高一、高二、高三學生，以班為單位組隊。\n2. 人數：每班 15 名參賽選手。高一男生至多 4 人，高二、高三男生至多 5 人。\n\n【比賽項目與規則】\n1. 競賽項目：單人跳繩。\n2. 時間：比賽時間 1 分鐘，採「累加計次」方式。\n3. 次數計算方式：一跳一迴旋採計 1 次；一跳二迴旋採計 2 次，迴旋方向前後不限。\n4. 器材：可自備，或由學校提供。`
      },
      volleyball: {
        name: "壢家交通安全宣導暨班際排球比賽", category: "球類/團體", limit: 12, location: "排球場",
        rules: `【競賽分組與特別規定】\n1. 組別：高二女生組、高三女生組、男生組(高二/高三)、混合組(高二/高三)。\n2. 隊數規定：每班至多 2 隊，女生必報 1 隊。人員不得重複。\n3. 混合組出賽：男生至多 3 人。\n4. 家服科若要報名男生組，得高二高三合併報名。\n\n【競賽方法】\n1. 賽制：單淘汰制。\n2. 局分：採 15 分制，三戰兩勝制。`
      },
      basketball_3on3: {
        name: "壢家班際籃球 3ON3 競賽", category: "球類/團體", limit: 5, location: "本校籃球場",
        rules: `【比賽制度與組別】\n1. 參賽限制：每隊 5 人，以班為單位組隊。\n2. 分組：男生組（原則每班 1 隊，資處科得報 2 隊）、高一女、高二女、高三女共 4 組（女生班級必報 1 隊）。`
      },
      nba_challenge: {
        name: "壢家籃球 NBA 挑戰賽", category: "球類/個人", limit: 4, location: "本校籃球場",
        rules: `【參賽與人數限制】\n1. 競賽方式：個人賽。各班級自由報名。\n2. 人數限制：每班男生至多 2 人、女生至多 2 人，每班累計最多 4 人參賽。`
      },
      sports_day_passball: {
        name: "壢家校慶運動會 - 薪火相傳", category: "趣味競賽/團體", limit: 18, location: "學校操場",
        rules: `【參賽人數】\n每班 16 人參賽，男生至多 8 人。2 人 1 組（女女或男女配對），共 8 組，候補 2 名（限額 18 人填寫）。\n\n【比賽方法和規則】\n1. 參賽者 2 人 1 組（面對背），將球放至力波墊上前進（禁止用手扶球前進）。\n2. 送至旗竿處逆時針折返，抵達起點後交給下一組。\n3. 若球落地，需於落地點重新擺好起跑。每次犯規加時 5 秒。`
      }
    };

    const NavItem = ({ active, onClick, icon, text, open }) => (
      <button
        onClick={onClick}
        className={`w-full flex items-center gap-3 p-4 rounded-2xl font-bold transition-all ${
          active ? 'bg-white/20 text-white shadow-sm' : 'text-slate-300 hover:bg-white/10 hover:text-white'
        } ${!open && 'justify-center'}`}
      >
        {icon}
        {open && <span>{text}</span>}
      </button>
    );

    const FormLabel = ({ text }) => (
      <label className="block text-sm font-black text-slate-700 mb-2 pl-2">{text}</label>
    );

    const App = () => {
      const [role, setRole] = useState(null); 
      const [user, setUser] = useState({ name: '', class: '', id: '' });
      const [activeTab, setActiveTab] = useState('dashboard');
      const [isSidebarOpen, setIsSidebarOpen] = useState(true);
      
      const [announcements, setAnnouncements] = useState([]);
      const [events, setEvents] = useState([]);
      const [allRegistrations, setAllRegistrations] = useState({});
      const [saveStatus, setSaveStatus] = useState('');

      const [captainPassword, setCaptainPassword] = useState('3636');
      const [tempCaptainPassword, setTempCaptainPassword] = useState('3636');

      const [showEventModal, setShowEventModal] = useState(false);
      const [showAnnounceModal, setShowAnnounceModal] = useState(false);
      const [showRuleModal, setShowRuleModal] = useState(false);
      const [viewingRule, setViewingRule] = useState(null);
      
      const [editingEvent, setEditingEvent] = useState(null);
      const [editingAnnounce, setEditingAnnounce] = useState(null); // 新增：正在編輯的公告狀態
      
      const [eventForm, setEventForm] = useState({ 
        name: '', category: '', limit: 1, location: '', rules: '', 
        year: '113', month: '09', day: '28', time: '13:20', 
        deadlineYear: '', deadlineMonth: '', deadlineDay: '', deadlineTime: ''
      });

      const [announceForm, setAnnounceForm] = useState({ title: '', content: '', type: '重要' });
      const [loginForm, setLoginForm] = useState({ 
        name: '', class: '資一甲', role: ROLES.STUDENT, password: '' 
      });

      const [adminSelectedEvent, setAdminSelectedEvent] = useState('');

      useEffect(() => {
        try {
          const savedEvents = localStorage.getItem('clvs_events_v4');
          const savedAnnounces = localStorage.getItem('clvs_announces_v4');
          const savedRegs = localStorage.getItem('clvs_registrations_v4');
          const savedCapPass = localStorage.getItem('clvs_captain_pass');
          
          if (savedCapPass) {
            setCaptainPassword(savedCapPass);
            setTempCaptainPassword(savedCapPass);
          }
          if (savedEvents) setEvents(JSON.parse(savedEvents));
          if (savedAnnounces) setAnnouncements(JSON.parse(savedAnnounces));
          if (savedRegs) setAllRegistrations(JSON.parse(savedRegs));
        } catch (e) {
          console.error("Failed to load data from localStorage", e);
        }
      }, []);

      useEffect(() => {
        localStorage.setItem('clvs_events_v4', JSON.stringify(events));
        localStorage.setItem('clvs_announces_v4', JSON.stringify(announcements));
        localStorage.setItem('clvs_registrations_v4', JSON.stringify(allRegistrations));
      }, [events, announcements, allRegistrations]);

      const getDeadlineDate = (ev) => {
        if (ev.deadlineYear && ev.deadlineMonth && ev.deadlineDay && ev.deadlineTime) {
          const gregorianYear = parseInt(ev.deadlineYear) + 1911; 
          return new Date(`${gregorianYear}-${ev.deadlineMonth.padStart(2, '0')}-${ev.deadlineDay.padStart(2, '0')}T${ev.deadlineTime}`);
        }
        if (ev.deadline) return new Date(ev.deadline);
        return null;
      };

      const formatDeadlineDisplay = (ev) => {
        if (ev.deadlineYear) return `民國 ${ev.deadlineYear} 年 ${ev.deadlineMonth} 月 ${ev.deadlineDay} 日 ${ev.deadlineTime}`;
        if (ev.deadline) {
          const d = new Date(ev.deadline);
          return `民國 ${d.getFullYear() - 1911} 年 ${String(d.getMonth() + 1).padStart(2, '0')} 月 ${String(d.getDate()).padStart(2, '0')} 日 ${String(d.getHours()).padStart(2, '0')}:${String(d.getMinutes()).padStart(2, '0')}`;
        }
        return null;
      };

      const showStatus = (msg) => {
        setSaveStatus(msg);
        setTimeout(() => setSaveStatus(''), 3000);
      };

      const handleLogin = (e) => {
        e.preventDefault();
        if (loginForm.role === ROLES.DIRECTOR) {
          if (loginForm.password !== "3636") return alert("⚠️ 體育組長授權密碼錯誤");
        } else if (loginForm.role === ROLES.CAPTAIN) {
          if (loginForm.password !== captainPassword) return alert("⚠️ 體育股長授權密碼錯誤");
        }

        let finalUser = { ...user };
        if (loginForm.role === ROLES.DIRECTOR) {
          finalUser = { name: '體育組長', class: '學務處', id: 'ADMIN' };
        } else if (loginForm.role === ROLES.CAPTAIN) {
          finalUser = { name: `${loginForm.class}體育股長`, class: loginForm.class, id: 'CAPTAIN' };
        } else {
          finalUser = { name: loginForm.name, class: loginForm.class, id: loginForm.password };
        }

        setUser(finalUser);
        setRole(loginForm.role);
        setActiveTab('dashboard');
      };

      const handleUpdateCaptainPassword = () => {
        if (!tempCaptainPassword.trim()) return alert('⚠️ 授權密碼不可為空！');
        setCaptainPassword(tempCaptainPassword);
        localStorage.setItem('clvs_captain_pass', tempCaptainPassword);
        showStatus('🔑 體育股長授權密碼已成功變更！');
      };

      const handleApplyTemplate = (tempKey) => {
        if (!tempKey) return;
        const template = COMPETITION_TEMPLATES[tempKey];
        if (template) {
          setEventForm(prev => ({
            ...prev, name: template.name, category: template.category, 
            limit: template.limit, location: template.location, rules: template.rules
          }));
          showStatus(`🎯 已成功帶入「${template.name}」報名格式與規則`);
        }
      };

      const handleSaveEvent = (e) => {
        e.preventDefault();
        const cleanForm = { ...eventForm, limit: parseInt(eventForm.limit) || 1 };
        if (editingEvent) {
          setEvents(events.map(ev => ev.id === editingEvent.id ? { ...cleanForm, id: ev.id } : ev));
          showStatus('✅ 競賽與規則已同步更新');
        } else {
          setEvents([...events, { ...cleanForm, id: Date.now() }]);
          showStatus('✅ 成功新增競賽項目');
        }
        setShowEventModal(false);
      };

      const handleDeleteEvent = (id) => {
        if (window.confirm('🚨 警告：確定要刪除此競賽項目？（此動作將清除該項目的全校報名資料）')) {
          setEvents(events.filter(ev => ev.id !== id));
          const newRegs = { ...allRegistrations };
          Object.keys(newRegs).forEach(key => { if (key.endsWith(`_${id}`)) delete newRegs[key]; });
          setAllRegistrations(newRegs);
          showStatus('🗑️ 項目已徹底移除');
        }
      };

      // 新增：處理儲存/更新公告的邏輯
      const handleSaveAnnounce = (e) => {
        e.preventDefault();
        if (editingAnnounce) {
          setAnnouncements(announcements.map(ann => ann.id === editingAnnounce.id ? { ...announceForm, id: ann.id, date: ann.date } : ann));
          showStatus('✅ 公告已成功更新');
        } else {
          const today = new Date();
          const minguoYear = today.getFullYear() - 1911;
          const dateStr = `民國 ${minguoYear}/${String(today.getMonth() + 1).padStart(2, '0')}/${String(today.getDate()).padStart(2, '0')}`;
          setAnnouncements([{ ...announceForm, id: Date.now(), date: dateStr }, ...announcements]);
          showStatus('✅ 成功發佈新公告');
        }
        setShowAnnounceModal(false);
        setEditingAnnounce(null);
      };

      // 新增：處理刪除公告的邏輯
      const handleDeleteAnnounce = (id) => {
        if (window.confirm('確定要刪除此則公告嗎？首頁也將同步移除。')) {
          setAnnouncements(announcements.filter(ann => ann.id !== id));
          showStatus('🗑️ 公告已成功移除');
        }
      };

      const handleRegChange = (eventId, index, field, value) => {
        const key = `${user.class}_${eventId}`;
        const newRegs = { ...allRegistrations };
        if (!newRegs[key]) newRegs[key] = [];
        if (!newRegs[key][index]) newRegs[key][index] = { name: '', no: '' };
        newRegs[key][index][field] = value;
        setAllRegistrations(newRegs);
      };

      const getMyRegs = () => {
        const results = [];
        Object.keys(allRegistrations).forEach(key => {
          const match = allRegistrations[key]?.find(r => r && r.name === user.name);
          if (match) {
            const eventId = key.split('_')[1];
            const event = events.find(e => e.id.toString() === eventId);
            if (event) results.push({ ...event, myNo: match.no });
          }
        });
        return results;
      };

      if (!role) {
        return (
          <div className="flex min-h-screen bg-[#F8FAFC] items-center justify-center relative">
            <div className="fixed inset-0 pointer-events-none flex flex-col items-center justify-center opacity-[0.025] z-0 select-none">
              <span className="text-[12rem] font-black tracking-widest text-[#002B5B]">CLVS</span>
            </div>
            <div className="bg-white p-10 rounded-[3rem] shadow-2xl z-10 w-full max-w-md border border-slate-100 animate-in fade-in">
              <div className="text-center mb-8">
                <div className="bg-[#002B5B] w-16 h-16 rounded-2xl flex items-center justify-center mx-auto mb-4 shadow-lg">
                  <Trophy className="text-yellow-400" size={32} />
                </div>
                <h1 className="text-2xl font-black text-slate-800">中壢家商體育管理系統</h1>
                <p className="text-slate-400 text-sm mt-2 font-bold">請選擇身分並登入</p>
              </div>
              <form onSubmit={handleLogin} className="space-y-5">
                <div>
                  <FormLabel text="登入身分" />
                  <select className="w-full p-4 bg-slate-50 rounded-2xl border border-slate-200 font-bold outline-none" value={loginForm.role} onChange={e => setLoginForm({...loginForm, role: e.target.value})}>
                    <option value={ROLES.STUDENT}>一般學生 (查詢用)</option>
                    <option value={ROLES.CAPTAIN}>體育股長 (報名用)</option>
                    <option value={ROLES.DIRECTOR}>體育組長 (管理用)</option>
                  </select>
                </div>
                
                {loginForm.role === ROLES.STUDENT && (
                  <>
                    <div>
                      <FormLabel text="班級" />
                      <select className="w-full p-4 bg-slate-50 rounded-2xl border border-slate-200 font-bold" value={loginForm.class} onChange={e => setLoginForm({...loginForm, class: e.target.value})}>
                        {CLASS_LIST.map(c => <option key={c} value={c}>{c}</option>)}
                      </select>
                    </div>
                    <div>
                      <FormLabel text="姓名" />
                      <input type="text" required className="w-full p-4 bg-slate-50 rounded-2xl border border-slate-200 font-bold" value={loginForm.name} onChange={e => setLoginForm({...loginForm, name: e.target.value})} />
                    </div>
                  </>
                )}

                {loginForm.role === ROLES.CAPTAIN && (
                  <>
                    <div>
                      <FormLabel text="班級" />
                      <select className="w-full p-4 bg-slate-50 rounded-2xl border border-slate-200 font-bold" value={loginForm.class} onChange={e => setLoginForm({...loginForm, class: e.target.value})}>
                        {CLASS_LIST.map(c => <option key={c} value={c}>{c}</option>)}
                      </select>
                    </div>
                    <div>
                      <FormLabel text="股長授權密碼" />
                      <input type="password" required className="w-full p-4 bg-slate-50 rounded-2xl border border-slate-200 font-bold" placeholder="密碼" value={loginForm.password} onChange={e => setLoginForm({...loginForm, password: e.target.value})} />
                    </div>
                  </>
                )}

                {loginForm.role === ROLES.DIRECTOR && (
                  <div>
                    <FormLabel text="組長管理密碼" />
                    <input type="password" required className="w-full p-4 bg-slate-50 rounded-2xl border border-slate-200 font-bold" placeholder="請輸入組長密碼" value={loginForm.password} onChange={e => setLoginForm({...loginForm, password: e.target.value})} />
                  </div>
                )}

                <button type="submit" className="w-full py-5 bg-[#002B5B] text-white rounded-2xl font-black shadow-xl hover:bg-slate-900 transition-all mt-4">
                  系統登入
                </button>
              </form>
            </div>
          </div>
        );
      }

      return (
        <div className="flex min-h-screen bg-[#F8FAFC]">
          <div className="fixed inset-0 pointer-events-none flex flex-col items-center justify-center opacity-[0.025] z-0 select-none">
            <span className="text-[12rem] font-black tracking-widest text-[#002B5B]">CLVS</span>
            <span className="text-4xl font-bold tracking-wider text-[#002B5B] mt-4">桃園市立中壢家商</span>
          </div>

          <aside className={`bg-[#002B5B] text-white transition-all duration-300 ${isSidebarOpen ? 'w-64' : 'w-20'} flex flex-col shrink-0 z-10`}>
            <div className="p-6 border-b border-white/10 flex items-center gap-3">
              <Trophy className="text-yellow-400 shrink-0" size={24} />
              {isSidebarOpen && <span className="font-black text-lg">中壢家商體育組</span>}
            </div>
            <nav className="flex-1 p-4 space-y-2">
              <NavItem active={activeTab === 'dashboard'} onClick={() => setActiveTab('dashboard')} icon={<LayoutDashboard size={20} />} text="控制台首頁" open={isSidebarOpen} />
              
              {role === ROLES.STUDENT && <NavItem active={activeTab === 'my'} onClick={() => setActiveTab('my')} icon={<UserCheck size={20} />} text="我的報名項目" open={isSidebarOpen} />}
              {role === ROLES.CAPTAIN && <NavItem active={activeTab === 'reg'} onClick={() => setActiveTab('reg')} icon={<ClipboardList size={20} />} text="班級名單填寫" open={isSidebarOpen} />}
              
              {role === ROLES.DIRECTOR && (
                <>
                  <NavItem active={activeTab === 'admin'} onClick={() => setActiveTab('admin')} icon={<Settings size={20} />} text="項目與公告管理" open={isSidebarOpen} />
                  <NavItem active={activeTab === 'admin_regs'} onClick={() => setActiveTab('admin_regs')} icon={<Users size={20} />} text="全校報名總表" open={isSidebarOpen} />
                </>
              )}
            </nav>
            <button onClick={() => setRole(null)} className="p-6 border-t border-white/10 flex items-center gap-4 text-red-300 hover:bg-white/5 transition-all">
              <LogOut size={20} /> {isSidebarOpen && "切換身分登出"}
            </button>
          </aside>

          <div className="flex-1 flex flex-col h-screen overflow-hidden z-10">
            <header className="bg-white border-b h-16 flex items-center justify-between px-8 shadow-sm">
              <div className="flex items-center gap-4">
                <button onClick={() => setIsSidebarOpen(!isSidebarOpen)} className="p-2 hover:bg-slate-100 rounded-lg text-slate-400 transition-colors"><Menu size={20} /></button>
                <h2 className="font-bold text-slate-700">
                  {role === ROLES.DIRECTOR ? "體育組長管理中心" : `${user.class || '全校'} - ${user.name}`}
                </h2>
                <span className="text-xs font-bold text-indigo-600 animate-pulse">{saveStatus}</span>
              </div>
            </header>

            <main className="flex-1 overflow-y-auto p-8">
              
              {/* 首頁 */}
              {activeTab === 'dashboard' && (
                <div className="max-w-5xl mx-auto space-y-8 animate-in fade-in">
                  <div className="bg-gradient-to-r from-[#002B5B] to-[#1A5F7A] p-10 rounded-[2.5rem] text-white flex justify-between items-center shadow-xl">
                    <div>
                      <h1 className="text-3xl font-black italic">CLVS SPORTS CENTER</h1>
                      <p className="mt-2 opacity-80 font-medium">競賽公告、規則查詢、報名管理統一平台</p>
                    </div>
                  </div>

                  <div className="grid grid-cols-1 md:grid-cols-3 gap-8">
                    <div className="md:col-span-2 space-y-6">
                      <h3 className="text-xl font-black text-slate-800 flex items-center gap-2"><Megaphone className="text-red-500" /> 最新公告</h3>
                      {announcements.length === 0 && <div className="text-slate-400 p-10 bg-white rounded-[2rem] text-center italic border border-dashed">目前尚無公告</div>}
                      {announcements.map(ann => (
                        <div key={ann.id} className="bg-white p-8 rounded-[2rem] border border-slate-100 shadow-sm relative overflow-hidden group">
                          <div className={`absolute left-0 top-0 w-1.5 h-full ${ann.type === '重要' ? 'bg-red-500' : 'bg-indigo-500'}`} />
                          <div className="flex justify-between items-start mb-3">
                            <span className={`px-3 py-1 rounded-full text-[10px] font-black tracking-widest ${ann.type === '重要' ? 'bg-red-50 text-red-600' : 'bg-indigo-50 text-indigo-600'}`}>{ann.type}</span>
                            <span className="text-xs font-mono text-slate-400">{ann.date}</span>
                          </div>
                          <h4 className="font-black text-slate-800 text-lg">{ann.title}</h4>
                          <p className="text-slate-500 text-sm mt-2 leading-relaxed whitespace-pre-wrap">{ann.content}</p>
                        </div>
                      ))}
                    </div>

                    <div className="space-y-6">
                      <h3 className="text-xl font-black text-slate-800 flex items-center gap-2"><BookOpen className="text-indigo-500" /> 競賽細則查詢</h3>
                      <div className="bg-white p-4 rounded-[2rem] border border-slate-100 space-y-2">
                        {events.length === 0 && <div className="text-center p-4 text-slate-300 text-sm">目前無任何競賽，請等待體育組發佈</div>}
                        {events.map(ev => (
                          <button key={ev.id} onClick={() => { setViewingRule(ev); setShowRuleModal(true); }} className="w-full flex justify-between items-center p-4 hover:bg-slate-50 rounded-2xl transition-all border border-transparent hover:border-slate-100 group text-left font-sans">
                            <span className="font-bold text-slate-700">{ev.name}</span>
                            <ChevronRight size={16} className="text-slate-300 group-hover:translate-x-1 transition-all flex-shrink-0" />
                          </button>
                        ))}
                      </div>
                    </div>
                  </div>
                </div>
              )}

              {/* 體育組長 - 競賽項目與公告管理 */}
              {activeTab === 'admin' && role === ROLES.DIRECTOR && (
                <div className="max-w-6xl mx-auto space-y-6 animate-in slide-in-from-bottom-4">
                  <div className="flex justify-between items-end">
                    <div>
                      <h3 className="text-2xl font-black text-slate-800">競賽項目與公告管理</h3>
                      <p className="text-slate-400 text-sm">在此開設與維護校內運動項目、競賽細則，並擁有調整公告的最高權限</p>
                    </div>
                    <div className="flex gap-3">
                      {/* 新增：發佈新公告按鈕 */}
                      <button onClick={() => {
                        setEditingAnnounce(null);
                        setAnnounceForm({ title: '', content: '', type: '重要' });
                        setShowAnnounceModal(true);
                      }} className="bg-amber-500 text-white px-6 py-4 rounded-2xl font-black shadow-lg hover:bg-amber-600 transition-all flex items-center gap-2">
                        <Megaphone size={20} /> 發佈新公告
                      </button>
                      <button onClick={() => { 
                        setEditingEvent(null); 
                        setEventForm({ name: '', category: '', limit: 1, location: '', rules: '', year: '113', month: '09', day: '28', time: '13:20', deadlineYear: '', deadlineMonth: '', deadlineDay: '', deadlineTime: '' }); 
                        setShowEventModal(true); 
                      }} className="bg-indigo-600 text-white px-6 py-4 rounded-2xl font-black shadow-lg hover:bg-indigo-700 transition-all flex items-center gap-2">
                        <Plus size={20} /> 開設全新競賽
                      </button>
                    </div>
                  </div>

                  {/* 競賽列表管理表格 */}
                  <div className="bg-white rounded-[2.5rem] border border-slate-200 overflow-hidden shadow-sm">
                    <div className="p-6 bg-slate-50/50 border-b font-black text-slate-700 text-sm">🏆 校內競賽清單</div>
                    <table className="w-full text-left">
                      <thead className="bg-slate-50/30 border-b text-[10px] uppercase font-black text-slate-400 tracking-[0.2em]">
                        <tr>
                          <th className="p-8">競賽名稱 / 類別</th><th className="p-8">報名截止期限</th><th className="p-8">時間與地點</th><th className="p-8">規則狀態</th><th className="p-8 text-right">管理操作</th>
                        </tr>
                      </thead>
                      <tbody className="divide-y divide-slate-100">
                        {events.length === 0 ? (
                          <tr><td colSpan="5" className="p-16 text-center text-slate-400 italic font-medium">後台目前無任何競賽，請點選上方「開設全新競賽」開始建立。</td></tr>
                        ) : (
                          events.map(ev => {
                            const now = new Date();
                            const deadlineDate = getDeadlineDate(ev);
                            const isClosed = deadlineDate && now > deadlineDate;
                            const displayStr = formatDeadlineDisplay(ev);

                            return (
                            <tr key={ev.id} className="hover:bg-slate-50/30 transition-colors">
                              <td className="p-8">
                                <div className="font-black text-slate-800 text-lg">{ev.name}</div>
                                <div className="bg-indigo-50 text-indigo-500 inline-block px-3 py-1 rounded-lg text-[10px] font-black mt-2">{ev.category}</div>
                              </td>
                              <td className="p-8">
                                {displayStr ? (
                                  <div>
                                    <div className="text-sm font-bold text-slate-700">{displayStr}</div>
                                    {isClosed ? (
                                      <span className="text-[10px] bg-red-100 text-red-600 px-2 py-1 rounded font-black mt-1 inline-block">已截止</span>
                                    ) : (
                                      <span className="text-[10px] bg-green-100 text-green-700 px-2 py-1 rounded font-black mt-1 inline-block">開放報名中</span>
                                    )}
                                  </div>
                                ) : (
                                  <span className="text-sm text-slate-400 font-bold">未設定截止</span>
                                )}
                              </td>
                              <td className="p-8">
                                <div className="text-sm font-bold text-slate-600 flex items-center gap-2 mb-1"><Calendar size={14} /> 民國 {ev.year}年 {ev.month}月 {ev.day}日 {ev.time}</div>
                                <div className="text-xs text-slate-400 flex items-center gap-2"><MapPin size={14} /> {ev.location}</div>
                              </td>
                              <td className="p-8">
                                {ev.rules ? <span className="text-green-500 text-xs font-bold flex items-center gap-1"><CheckCircle className="text-green-500 shrink-0" size={14} /> 規則已上傳</span> : <span className="text-red-400 text-xs font-bold flex items-center gap-1"><AlertTriangle size={14} /> 尚未設定規則</span>}
                              </td>
                              <td className="p-8 text-right">
                                <div className="flex justify-end gap-2">
                                  <button onClick={() => { 
                                    setEditingEvent(ev); 
                                    let editForm = { ...ev };
                                    if (ev.deadline && !ev.deadlineYear) {
                                      const d = new Date(ev.deadline);
                                      editForm.deadlineYear = String(d.getFullYear() - 1911);
                                      editForm.deadlineMonth = String(d.getMonth() + 1).padStart(2, '0');
                                      editForm.deadlineDay = String(d.getDate()).padStart(2, '0');
                                      editForm.deadlineTime = `${String(d.getHours()).padStart(2, '0')}:${String(d.getMinutes()).padStart(2, '0')}`;
                                    }
                                    setEventForm(editForm); 
                                    setShowEventModal(true); 
                                  }} className="p-3 text-indigo-600 hover:bg-indigo-50 rounded-2xl transition-all"><Edit3 size={20} /></button>
                                  <button onClick={() => handleDeleteEvent(ev.id)} className="p-3 text-red-400 hover:bg-red-50 rounded-2xl transition-all"><Trash2 size={20} /></button>
                                </div>
                              </td>
                            </tr>
                          )})
                        )}
                      </tbody>
                    </table>
                  </div>

                  {/* 新增：最新公告管理區塊 */}
                  <div className="bg-white rounded-[2.5rem] border border-slate-200 overflow-hidden shadow-sm">
                    <div className="p-6 bg-slate-50/50 border-b font-black text-slate-700 text-sm flex items-center gap-2">
                      <Megaphone className="text-amber-500" size={18} /> 目前系統公告管理
                    </div>
                    <div className="divide-y divide-slate-100">
                      {announcements.length === 0 ? (
                        <div className="p-12 text-center text-slate-400 italic font-medium">系統中目前無公告，請點選上方「發佈新公告」按鈕建立。</div>
                      ) : (
                        announcements.map(ann => (
                          <div key={ann.id} className="p-6 flex justify-between items-start hover:bg-slate-50/30 transition-colors text-left">
                            <div className="space-y-2 flex-1 pr-6">
                              <div className="flex items-center gap-3">
                                <span className={`px-2 py-0.5 rounded-full text-[10px] font-black tracking-widest ${ann.type === '重要' ? 'bg-red-50 text-red-600' : 'bg-indigo-50 text-indigo-600'}`}>{ann.type}</span>
                                <span className="text-xs font-mono text-slate-400">{ann.date}</span>
                              </div>
                              <h5 className="font-black text-slate-800 text-base">{ann.title}</h5>
                              <p className="text-slate-500 text-sm leading-relaxed max-w-4xl line-clamp-2 whitespace-pre-wrap">{ann.content}</p>
                            </div>
                            <div className="flex gap-1 shrink-0">
                              <button onClick={() => {
                                setEditingAnnounce(ann);
                                setAnnounceForm({ title: ann.title, content: ann.content, type: ann.type });
                                setShowAnnounceModal(true);
                              }} className="p-3 text-indigo-600 hover:bg-indigo-50 rounded-xl transition-all"><Edit3 size={18} /></button>
                              <button onClick={() => handleDeleteAnnounce(ann.id)} className="p-3 text-red-400 hover:bg-red-50 rounded-xl transition-all"><Trash2 size={18} /></button>
                            </div>
                          </div>
                        ))
                      )}
                    </div>
                  </div>
                  
                  {/* 體育股長密碼管理區塊 */}
                  <div className="bg-white p-8 rounded-[2.5rem] border border-slate-200 shadow-sm mt-8 text-left">
                    <h4 className="text-lg font-black text-slate-800 flex items-center gap-2 mb-4"><Lock size={20} className="text-[#002B5B]" /> 體育股長登入授權密碼變更</h4>
                    <p className="text-slate-400 text-sm mb-6">在此您可以直接變更全校 27 個班級體育股長登入系統時所需的授權密碼，保障報名系統之安全性。</p>
                    <div className="flex flex-col sm:flex-row gap-4 max-w-md items-start sm:items-end">
                      <div className="w-full">
                        <FormLabel text="新設定之股長密碼" />
                        <input type="text" className="w-full p-4 bg-slate-50 rounded-2xl border border-slate-200 font-bold text-slate-800" value={tempCaptainPassword} onChange={e => setTempCaptainPassword(e.target.value)} />
                      </div>
                      <button onClick={handleUpdateCaptainPassword} className="w-full sm:w-auto bg-[#002B5B] hover:bg-slate-900 text-white px-8 py-4 rounded-2xl font-black transition-all shadow-md shrink-0">儲存變更</button>
                    </div>
                  </div>
                </div>
              )}

              {/* 體育組長 - 全校各班報名總表 */}
              {activeTab === 'admin_regs' && role === ROLES.DIRECTOR && (
                <div className="max-w-7xl mx-auto space-y-6 animate-in slide-in-from-bottom-4">
                  <div className="flex justify-between items-end">
                    <div>
                      <h3 className="text-2xl font-black text-slate-800">全校各班報名總表</h3>
                      <p className="text-slate-400 text-sm">在此檢視各項競賽全校班級的報名進度與名單</p>
                    </div>
                    {events.length > 0 && (
                      <div className="flex items-center gap-3">
                        <span className="text-sm font-bold text-slate-500">選擇競賽項目：</span>
                        <select
                          className="p-4 bg-white rounded-2xl border border-slate-200 font-bold shadow-sm outline-none text-slate-800 min-w-[250px]"
                          value={adminSelectedEvent || events[0].id}
                          onChange={(e) => setAdminSelectedEvent(e.target.value)}
                        >
                          {events.map(ev => (
                            <option key={ev.id} value={ev.id}>{ev.name}</option>
                          ))}
                        </select>
                      </div>
                    )}
                  </div>

                  {events.length === 0 ? (
                    <div className="p-16 bg-white rounded-[3rem] border border-dashed text-center text-slate-400 font-bold">目前無任何競賽項目。</div>
                  ) : (
                    <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-6">
                      {CLASS_LIST.map(className => {
                        const currentEvId = adminSelectedEvent || events[0].id;
                        const regData = allRegistrations[`${className}_${currentEvId}`] || [];
                        const filledData = regData.filter(r => r && r.name && r.name.trim() !== '');
                        const currentEvent = events.find(e => e.id.toString() === currentEvId.toString());

                        return (
                          <div key={className} className="bg-white rounded-[1.5rem] border border-slate-200 shadow-sm overflow-hidden flex flex-col">
                            <div className="bg-slate-50/80 px-6 py-4 border-b border-slate-100 flex justify-between items-center">
                              <h4 className="font-black text-slate-800 text-lg">{className}</h4>
                              <span className={`text-xs font-bold px-3 py-1 rounded-lg ${filledData.length > 0 ? 'bg-green-100 text-green-700' : 'bg-slate-200 text-slate-500'}`}>
                                已報 {filledData.length} / {currentEvent?.limit || 0} 人
                              </span>
                            </div>
                            <div className="p-4 space-y-2 h-[200px] overflow-y-auto custom-scrollbar bg-white">
                               {filledData.length === 0 ? (
                                 <div className="h-full flex items-center justify-center text-slate-300 font-bold text-sm italic">
                                   該班尚未填寫名單
                                 </div>
                               ) : (
                                 filledData.map((student, idx) => (
                                   <div key={idx} className="flex justify-between items-center bg-slate-50 border border-slate-100 p-3 rounded-xl">
                                      <span className="font-bold text-slate-700">{student.name}</span>
                                      <span className="text-xs font-black text-indigo-500 bg-indigo-50 px-2 py-1 rounded-md">座號 {student.no}</span>
                                   </div>
                                 ))
                               )}
                            </div>
                          </div>
                        );
                      })}
                    </div>
                  )}
                </div>
              )}

              {/* 體育股長 - 班級選手報名單填寫 */}
              {activeTab === 'reg' && role === ROLES.CAPTAIN && (
                <div className="max-w-6xl mx-auto space-y-6">
                  <div className="bg-white p-8 rounded-[2.5rem] border border-slate-200 flex justify-between items-center shadow-sm">
                    <div>
                      <h3 className="text-2xl font-black text-slate-800">班級選手報名單 - {user.class}</h3>
                      <p className="text-slate-400 text-sm">請依照體育組規定之規則填寫正確名單</p>
                    </div>
                    <button onClick={() => showStatus('🚀 系統資料已保存')} className="bg-[#002B5B] text-white px-10 py-5 rounded-[1.5rem] font-black shadow-xl hover:scale-105 transition-all flex items-center gap-2">
                       <Save size={20} /> 保存目前名單
                    </button>
                  </div>
                  {events.length === 0 ? (
                    <div className="p-16 bg-white rounded-[3rem] border border-dashed text-center text-slate-400 font-bold">目前無任何競賽報名項目。</div>
                  ) : (
                    <div className="grid grid-cols-1 lg:grid-cols-2 gap-8">
                      {events.map(ev => {
                        const now = new Date();
                        const deadlineDate = getDeadlineDate(ev);
                        const isClosed = deadlineDate && now > deadlineDate;
                        const displayStr = formatDeadlineDisplay(ev);

                        return (
                        <div key={ev.id} className="bg-white rounded-[2.5rem] border border-slate-200 shadow-sm overflow-hidden flex flex-col">
                          <div className="bg-slate-50/50 px-8 py-6 border-b border-slate-100 flex justify-between items-center">
                            <div className="flex items-center gap-3">
                              <h4 className="font-black text-slate-800">{ev.name}</h4>
                              {isClosed ? (
                                <span className="bg-red-100 text-red-600 text-xs font-black px-3 py-1 rounded-lg flex items-center gap-1">
                                  <Lock size={14} /> 報名已截止
                                </span>
                              ) : displayStr ? (
                                <span className="bg-green-100 text-green-700 text-xs font-black px-3 py-1 rounded-lg">
                                  截止：{displayStr}
                                </span>
                              ) : null}
                            </div>
                            <button onClick={() => { setViewingRule(ev); setShowRuleModal(true); }} className="text-xs font-bold text-indigo-600 flex items-center gap-1">查看規則 <Info size={14} /></button>
                          </div>
                          
                          <div className="p-8 space-y-3 max-h-[350px] overflow-y-auto custom-scrollbar">
                             {Array.from({ length: ev.limit || 0 }).map((_, i) => (
                               <div key={i} className={`flex gap-3 p-3 rounded-2xl border border-transparent transition-all ${isClosed ? 'bg-slate-100/70 border-slate-200' : 'bg-slate-50'}`}>
                                  <input 
                                    type="text" 
                                    placeholder="選手姓名" 
                                    disabled={isClosed}
                                    className={`w-full bg-transparent outline-none font-bold text-slate-800 ${isClosed ? 'cursor-not-allowed opacity-60 text-slate-500' : ''}`} 
                                    value={allRegistrations[`${user.class}_${ev.id}`]?.[i]?.name || ''} 
                                    onChange={e => handleRegChange(ev.id, i, 'name', e.target.value)} 
                                  />
                                  <input 
                                    type="number" 
                                    placeholder="座號" 
                                    disabled={isClosed}
                                    className={`w-16 bg-white rounded-xl border border-slate-200 text-center font-black outline-none text-slate-800 ${isClosed ? 'cursor-not-allowed opacity-60 bg-slate-200' : ''}`} 
                                    value={allRegistrations[`${user.class}_${ev.id}`]?.[i]?.no || ''} 
                                    onChange={e => handleRegChange(ev.id, i, 'no', e.target.value)} 
                                  />
                               </div>
                             ))}
                          </div>
                        </div>
                      )})}
                    </div>
                  )}
                </div>
              )}

              {/* 學生 - 個人參賽項目查詢 */}
              {activeTab === 'my' && role === ROLES.STUDENT && (
                <div className="max-w-4xl mx-auto space-y-6">
                  <div className="bg-white p-8 rounded-[2.5rem] shadow-sm">
                    <h3 className="text-2xl font-black">個人參賽項目查詢</h3>
                    <p className="text-slate-400">目前學生 {user.name} 報名的所有體育活動</p>
                  </div>
                  {getMyRegs().length === 0 ? <div className="p-20 bg-white rounded-[3rem] border-2 border-dashed text-center text-slate-400 font-bold">尚未在任何項目的選手名單中</div> : (
                    getMyRegs().map(ev => (
                      <div key={ev.id} className="bg-white p-8 rounded-[2rem] shadow-sm border border-slate-100 flex justify-between items-center">
                        <div>
                          <h4 className="text-xl font-black text-slate-800">{ev.name}</h4>
                          <p className="text-xs text-slate-400 mt-2 font-bold flex items-center gap-3"><Calendar size={12} /> 民國 {ev.year}年 {ev.month}月 {ev.day}日 <MapPin size={12} /> {ev.location}</p>
                        </div>
                        <div className="flex items-center gap-4">
                           <div className="text-center bg-indigo-50 px-6 py-2 rounded-2xl">
                              <div className="text-[10px] font-black text-indigo-300">選手座號</div>
                              <div className="font-black text-indigo-600 text-lg">{ev.myNo || '-'}</div>
                           </div>
                           <button onClick={() => { setViewingRule(ev); setShowRuleModal(true); }} className="bg-slate-900 text-white px-6 py-3 rounded-xl font-bold hover:scale-105 transition-transform">詳細規則</button>
                        </div>
                      </div>
                    ))
                  )}
                </div>
              )}
            </main>
          </div>

          {/* Modal - 開設/編輯競賽 */}
          {showEventModal && (
            <div className="fixed inset-0 z-50 flex items-center justify-center p-6 bg-[#002B5B]/80 backdrop-blur-md">
              <div className="bg-white w-full max-w-2xl h-[90vh] rounded-[3.5rem] shadow-2xl flex flex-col overflow-hidden animate-in zoom-in-95">
                <div className="bg-[#002B5B] p-10 text-white flex justify-between items-center shrink-0">
                   <h3 className="text-2xl font-black">{editingEvent ? '編輯競賽項目' : '開設新競賽項目'}</h3>
                   <button onClick={() => setShowEventModal(false)} className="hover:rotate-90 transition-transform"><X size={28} /></button>
                </div>
                <form onSubmit={handleSaveEvent} className="p-10 space-y-8 overflow-y-auto flex-1 custom-scrollbar text-left">
                  <div className="bg-amber-50 p-6 rounded-3xl border border-amber-200">
                    <h4 className="text-sm font-black text-amber-800 flex items-center gap-2 mb-2"><Trophy size={16} /> 🎯 快速套用壢家規程範本</h4>
                    <p className="text-xs text-amber-700 mb-4">選擇下列預設項目，系統將自動為您代入該競賽規程中的報名名額、項目地點與詳細比賽規則。</p>
                    <select className="w-full p-4 bg-white border border-amber-300 rounded-xl font-bold text-amber-900 outline-none" defaultValue="" onChange={(e) => handleApplyTemplate(e.target.value)}>
                      <option value="" disabled>-- 請選擇一個體育競賽規格 --</option>
                      <option value="cheerleading">【啦啦隊】班際舞蹈暨啦啦隊比賽 (限額 42 人)</option>
                      <option value="rope_skipping">【跳繩】班際跳繩比賽 (限額 15 人)</option>
                      <option value="volleyball">【排球】班際排球比賽 (限額 12 人)</option>
                      <option value="basketball_3on3">【籃球】班際 3ON3 籃球賽 (限額 5 人)</option>
                      <option value="nba_challenge">【籃球】NBA 挑戰賽個人賽 (限額 4 人)</option>
                      <option value="sports_day_passball">【運動會】校慶趣味競賽 - 薪火相傳 (限額 18 人)</option>
                    </select>
                  </div>
                  <div className="grid grid-cols-2 gap-6">
                    <div className="col-span-2"><FormLabel text="競賽項目名稱" /><input type="text" required className="w-full p-5 bg-slate-50 rounded-2xl border border-slate-200 font-bold" value={eventForm.name} onChange={e => setEventForm({...eventForm, name: e.target.value})} /></div>
                    
                    <div className="col-span-2">
                        <FormLabel text="報名截止時間 (選填 / 民國格式)" />
                        <div className="grid grid-cols-4 gap-3">
                           <input type="number" placeholder="年 (例: 113)" className="w-full p-4 bg-slate-50 rounded-2xl border border-slate-200 font-bold text-slate-800 text-center" value={eventForm.deadlineYear || ''} onChange={e => setEventForm({...eventForm, deadlineYear: e.target.value})} />
                           <input type="number" placeholder="月 (01-12)" className="w-full p-4 bg-slate-50 rounded-2xl border border-slate-200 font-bold text-slate-800 text-center" value={eventForm.deadlineMonth || ''} onChange={e => setEventForm({...eventForm, deadlineMonth: e.target.value})} />
                           <input type="number" placeholder="日 (01-31)" className="w-full p-4 bg-slate-50 rounded-2xl border border-slate-200 font-bold text-slate-800 text-center" value={eventForm.deadlineDay || ''} onChange={e => setEventForm({...eventForm, deadlineDay: e.target.value})} />
                           <input type="time" className="w-full p-4 bg-slate-50 rounded-2xl border border-slate-200 font-bold text-slate-800 text-center" value={eventForm.deadlineTime || ''} onChange={e => setEventForm({...eventForm, deadlineTime: e.target.value})} />
                        </div>
                        <p className="text-xs text-slate-400 mt-2 font-bold">若設定此時間，一旦超過期限，體育股長將無法填寫或修改該項目的報名名單。</p>
                    </div>

                    <div><FormLabel text="競賽類別" /><input type="text" className="w-full p-5 bg-slate-50 rounded-2xl border border-slate-200 font-bold" value={eventForm.category} onChange={e => setEventForm({...eventForm, category: e.target.value})} /></div>
                    <div><FormLabel text="人數限制 (單班)" /><input type="number" className="w-full p-5 bg-slate-50 rounded-2xl border border-slate-200 font-bold" value={isNaN(eventForm.limit) ? '' : eventForm.limit} onChange={e => setEventForm({...eventForm, limit: e.target.value === '' ? '' : parseInt(e.target.value)})} /></div>
                    
                    <div className="col-span-2 grid grid-cols-4 gap-4 mt-2">
                       <div><FormLabel text="比賽年份(民國)" /><input type="text" className="w-full p-4 bg-slate-50 rounded-2xl font-bold border border-slate-200 text-center" value={eventForm.year} onChange={e => setEventForm({...eventForm, year: e.target.value})} /></div>
                       <div><FormLabel text="比賽月份" /><input type="text" className="w-full p-4 bg-slate-50 rounded-2xl font-bold border border-slate-200 text-center" value={eventForm.month} onChange={e => setEventForm({...eventForm, month: e.target.value})} /></div>
                       <div><FormLabel text="比賽日期" /><input type="text" className="w-full p-4 bg-slate-50 rounded-2xl font-bold border border-slate-200 text-center" value={eventForm.day} onChange={e => setEventForm({...eventForm, day: e.target.value})} /></div>
                       <div><FormLabel text="比賽時間" /><input type="text" className="w-full p-4 bg-slate-50 rounded-2xl font-bold border border-slate-200 text-center" value={eventForm.time} onChange={e => setEventForm({...eventForm, time: e.target.value})} /></div>
                    </div>
                    
                    <div className="col-span-2"><FormLabel text="地點" /><input type="text" className="w-full p-5 bg-slate-50 rounded-2xl border border-slate-200 font-bold" value={eventForm.location} onChange={e => setEventForm({...eventForm, location: e.target.value})} /></div>
                    <div className="col-span-2"><FormLabel text="競賽細則與規則內容" /><textarea rows="8" className="w-full p-6 bg-indigo-50 border border-indigo-100 rounded-[2rem] font-medium leading-relaxed" value={eventForm.rules} onChange={e => setEventForm({...eventForm, rules: e.target.value})}></textarea></div>
                  </div>
                  <button type="submit" className="w-full py-6 bg-[#002B5B] text-white rounded-[2rem] font-black shadow-2xl hover:bg-black transition-all">儲存項目</button>
                </form>
              </div>
            </div>
          )}

          {/* 新增：Modal - 發佈/編輯公告 */}
          {showAnnounceModal && (
            <div className="fixed inset-0 z-50 flex items-center justify-center p-6 bg-[#002B5B]/80 backdrop-blur-md">
              <div className="bg-white w-full max-w-xl rounded-[3.5rem] shadow-2xl flex flex-col overflow-hidden animate-in zoom-in-95">
                <div className="bg-[#002B5B] p-10 text-white flex justify-between items-center shrink-0">
                   <h3 className="text-2xl font-black">{editingAnnounce ? '編輯最新公告' : '發佈全新系統公告'}</h3>
                   <button onClick={() => { setShowAnnounceModal(false); setEditingAnnounce(null); }} className="hover:rotate-90 transition-transform"><X size={28} /></button>
                </div>
                <form onSubmit={handleSaveAnnounce} className="p-10 space-y-6 text-left">
                  <div>
                    <FormLabel text="公告標題" />
                    <input type="text" required placeholder="請填寫簡短清楚的公告主旨" className="w-full p-4 bg-slate-50 rounded-2xl border border-slate-200 font-bold text-slate-800" value={announceForm.title} onChange={e => setAnnounceForm({...announceForm, title: e.target.value})} />
                  </div>
                  <div>
                    <FormLabel text="公告類別" />
                    <select className="w-full p-4 bg-slate-50 rounded-2xl border border-slate-200 font-bold outline-none text-slate-800" value={announceForm.type} onChange={e => setAnnounceForm({...announceForm, type: e.target.value})}>
                      <option value="重要">重要（顯示為醒目紅色標籤）</option>
                      <option value="一般">一般（顯示為知性紫色標籤）</option>
                    </select>
                  </div>
                  <div>
                    <FormLabel text="公告詳細內容" />
                    <textarea rows="6" required placeholder="請填寫要佈達給全校師生的詳細資訊..." className="w-full p-5 bg-slate-50 border border-slate-200 rounded-2xl font-medium leading-relaxed text-slate-700" value={announceForm.content} onChange={e => setAnnounceForm({...announceForm, content: e.target.value})}></textarea>
                  </div>
                  <button type="submit" className="w-full py-5 bg-[#002B5B] text-white rounded-2xl font-black shadow-xl hover:bg-black transition-all">
                    {editingAnnounce ? '確認更新公告' : '發佈至首頁'}
                  </button>
                </form>
              </div>
            </div>
          )}

          {/* Modal - 觀看規則 */}
          {showRuleModal && viewingRule && (
            <div className="fixed inset-0 z-[60] flex items-center justify-center p-6 bg-[#002B5B]/90 backdrop-blur-xl">
              <div className="bg-white w-full max-w-xl rounded-[3rem] shadow-2xl flex flex-col max-h-[85vh] overflow-hidden animate-in zoom-in-95">
                <div className="bg-slate-50 p-8 border-b flex justify-between items-center shrink-0">
                  <div>
                    <span className="text-xs font-black text-indigo-500 bg-indigo-50 px-3 py-1 rounded-lg mb-2 inline-block">{viewingRule.category}</span>
                    <h3 className="text-2xl font-black text-slate-800">{viewingRule.name}</h3>
                  </div>
                  <button onClick={() => setShowRuleModal(false)} className="p-2 text-slate-400 hover:text-slate-700 hover:bg-slate-200 rounded-full transition-all"><X size={24} /></button>
                </div>
                <div className="p-8 overflow-y-auto custom-scrollbar whitespace-pre-wrap font-medium text-slate-600 leading-relaxed">
                  {viewingRule.rules ? viewingRule.rules : '⚠️ 體育組長尚未上傳此項目的詳細規則。'}
                </div>
                <div className="bg-slate-50 p-6 border-t flex justify-end shrink-0">
                  <button onClick={() => setShowRuleModal(false)} className="px-8 py-3 bg-[#002B5B] text-white rounded-2xl font-black shadow-lg hover:scale-105 transition-all">我知道了</button>
                </div>
              </div>
            </div>
          )}
        </div>
      );
    };

    const root = ReactDOM.createRoot(document.getElementById('root'));
    root.render(<App />);
  </script>
</body>
</html>

import React, { useState, useEffect, useRef } from 'react';
import { 
  Brain, Dumbbell, Moon, BookOpen, Calendar as CalendarIcon, 
  Award, Compass, Sparkles, ChevronLeft, ChevronRight, 
  Plus, Trash2, Edit2, Check, X, Smile, Clock, Heart, 
  Target, Info, RefreshCw, AlertCircle, BookMarked, Play, Pause,
  CornerDownRight, Flame, SmilePlus, Lightbulb, Settings
} from 'lucide-react';

// ==========================================
// 《Rewire》與 YOLO 精緻中文激勵金句庫
// ==========================================
const MOTIVATIONAL_QUOTES = [
  "「大腦是非線性的黏土。每一步新的選擇，都在為理想人生開拓溫慢而溫暖的柏油路。」",
  "「一起放電的神經元會彼此連結；無法同步放電的神經元將失去連結。」",
  "「我們所做的一切都是在學習，每一次失敗都是擺脫失敗的一個機會。」",
  "「自責只會加深舊習慣的神經迴路。溫柔接受當下，才是大腦重塑的科學起點。」",
  "「改變是非線性的，大腦不是鬆緊帶。即便今天稍微休息，明天大腦依然可塑。」",
  "「在觸發與反應之間踩下剎車、創造暫停，你就奪回了人生的主導權。」",
  "「不用強求連續，但一定要持續。凡走過必留下痕跡，大腦的努力從不白費。」",
  "「大腦天生被預設為關注負面，但你可以透過主動注意力，重新寫入美好的體驗。」"
];

// ==========================================
// 預設與初始數據定義 (結合《Rewire》精髓與 2026 預設歷史記錄)
// ==========================================
const DEFAULT_SETTINGS = {
  cognitive: [
    { id: 'cog-1', name: '感恩日記：寫下三件感恩的事，建立正向注意力', completed: false },
    { id: 'cog-2', name: 'CBT 轉念練習：將「我真爛」改寫為溫柔合理的科學解釋', completed: false },
    { id: 'cog-3', name: '正念靜坐 10 分鐘，鍛鍊神經元專注度', completed: false }
  ],
  exercise: [
    { id: 'exe-1', name: '有氧運動（慢跑 30 分鐘，釋放大腦天然肥料 BDNF）', targetMin: 30, weeklyTarget: 3, completed: false },
    { id: 'exe-2', name: '深蹲與核心肌力訓練', targetMin: 20, weeklyTarget: 2, completed: false }
  ],
  sleep: {
    bedtime: '22:30',
    wakeUpTime: '06:30',
    rituals: [
      { id: 'slp-1', name: '睡前 1 小時遠離藍光與手機螢幕', completed: false },
      { id: 'slp-2', name: '閱讀實體紙本書，向大腦宣告準備關機', completed: false },
      { id: 'slp-3', name: '進行 5 次生理性嘆息，啟動副交感神經', completed: false }
    ]
  },
  learning: [
    { id: 'lrn-1', name: '英文聽力與口說影子練習 (Shadowing)', targetMin: 20, completed: false },
    { id: 'lrn-2', name: 'AI 開發與 React 動態組件實踐', targetMin: 30, completed: false }
  ]
};

const DEFAULT_BUCKET_LIST = [
  {
    id: 'bkt-1',
    dream: '獨自前往瑞士阿爾卑斯山徒步旅行',
    category: '旅行/體驗',
    milestones: [
      {
        id: 'ms-1_1',
        text: '規劃健行路線與進行心肺耐力訓練',
        weeklyAction: '每週至少進行 2 次 5 公里負重慢跑與儲蓄計畫',
        dailyTask: '今天完成 20 分鐘下肢核心訓練，並儲存 150 元旅行基金',
        completed: false
      }
    ]
  },
  {
    id: 'bkt-2',
    dream: '掌握 AI 輔助前端技術，創立自主接案工作室',
    category: '事業/學習',
    milestones: [
      {
        id: 'ms-2_1',
        text: '熟悉 React 狀態管理與大型語言模型 API 串接',
        weeklyAction: '每週利用 AI 輔助重構一個動態網頁 Project 並寫成筆記',
        dailyTask: '今天寫扣 30 分鐘，並刻意優化大腦重塑 UI 邏輯',
        completed: false
      }
    ]
  }
];

const INITIAL_RECORDS = {
  "2026-06-01": {
    mood: "😄",
    cognitiveItems: [
      { id: 'cog-1', name: '感恩日記：寫下三件感恩的事，建立正向注意力', completed: true },
      { id: 'cog-2', name: 'CBT 轉念練習', completed: true },
      { id: 'cog-3', name: '正念靜坐 10 分鐘', completed: true }
    ],
    exerciseItems: [
      { id: 'exe-1', name: '有氧運動', completedMin: 35, targetMin: 30, completed: true }
    ],
    sleepRituals: [
      { id: 'slp-1', name: '睡前 1 小時遠離藍光', completed: true },
      { id: 'slp-2', name: '閱讀實體紙本書', completed: true }
    ],
    learningItems: [
      { id: 'lrn-1', name: '英文聽力與口說', completedMin: 20, targetMin: 20, completed: true }
    ],
    sleepHours: 8.0,
    note: "今天是美好的六月第一天！早起時精神飽滿，神經網絡元氣滿滿。"
  },
  "2026-06-02": {
    mood: "😐",
    cognitiveItems: [
      { id: 'cog-1', name: '感恩日記', completed: true },
      { id: 'cog-2', name: 'CBT 轉念練習', completed: false }
    ],
    exerciseItems: [
      { id: 'exe-1', name: '有氧運動', completedMin: 15, targetMin: 30, completed: false }
    ],
    sleepRituals: [
      { id: 'slp-1', name: '睡前遠離藍光', completed: true }
    ],
    learningItems: [
      { id: 'lrn-1', name: '英文聽力與口說', completedMin: 30, targetMin: 20, completed: true }
    ],
    sleepHours: 6.5,
    note: "下班有點疲倦，感覺腦部自動進入了原始的『省電省腦力模式』。沒關係，接受當下，不自責！"
  },
  "2026-06-03": {
    mood: "😔",
    cognitiveItems: [
      { id: 'cog-1', name: '感恩日記', completed: false },
      { id: 'cog-2', name: 'CBT 轉念練習', completed: false }
    ],
    exerciseItems: [
      { id: 'exe-1', name: '有氧運動', completedMin: 0, targetMin: 30, completed: false }
    ],
    sleepRituals: [
      { id: 'slp-1', name: '睡前遠離藍光', completed: false }
    ],
    learningItems: [
      { id: 'lrn-1', name: '英文聽力與口說', completedMin: 5, targetMin: 20, completed: false }
    ],
    sleepHours: 5.0,
    note: "今天壓力很大。理智線險些斷掉，睡前做幾次生理性嘆息，明天再重啟。"
  },
  "2026-06-05": {
    mood: "🙂",
    cognitiveItems: [
      { id: 'cog-1', name: '感恩日記', completed: true },
      { id: 'cog-2', name: 'CBT 轉念練習', completed: true }
    ],
    exerciseItems: [
      { id: 'exe-1', name: '有氧運動', completedMin: 30, targetMin: 30, completed: true }
    ],
    sleepRituals: [
      { id: 'slp-1', name: '睡前遠離藍光', completed: true },
      { id: 'slp-3', name: '5次生理性嘆息', completed: true }
    ],
    learningItems: [
      { id: 'lrn-1', name: '英文聽力', completedMin: 20, targetMin: 20, completed: true },
      { id: 'lrn-2', name: 'AI 技術開發', completedMin: 40, targetMin: 30, completed: true }
    ],
    sleepHours: 7.5,
    note: "重新抓回節奏，今天挑戰了複雜的 React 動態組件！流暢度有感提升。"
  },
  "2026-06-06": {
    mood: "😄",
    cognitiveItems: [
      { id: 'cog-1', name: '感恩日記', completed: true },
      { id: 'cog-3', name: '正念靜坐', completed: true }
    ],
    exerciseItems: [
      { id: 'exe-1', name: '有氧運動', completedMin: 40, targetMin: 30, completed: true }
    ],
    sleepRituals: [
      { id: 'slp-1', name: '不滑手機', completed: true },
      { id: 'slp-2', name: '閱讀實體書', completed: true }
    ],
    learningItems: [
      { id: 'lrn-2', name: 'AI 開發實踐', completedMin: 45, targetMin: 30, completed: true }
    ],
    sleepHours: 8.0,
    note: "週末的充分放鬆。BDNF 分泌感覺十分旺盛，大腦像一塊容易雕塑的溫暖黏土。"
  }
};

const getTodayString = (dateObj = new Date()) => {
  const offset = dateObj.getTimezoneOffset();
  const localDate = new Date(dateObj.getTime() - (offset * 60 * 1000));
  return localDate.toISOString().split('T')[0];
};

export default function App() {
  // ==========================================
  // 注入 Google 字型 (Great Vibes & Montserrat)
  // ==========================================
  useEffect(() => {
    const link = document.createElement('link');
    link.href = 'https://fonts.googleapis.com/css2?family=Great+Vibes&family=Montserrat:ital,wght@0,300;0,400;0,500;0,700;1,300&display=swap';
    link.rel = 'stylesheet';
    document.head.appendChild(link);
    return () => {
      document.head.removeChild(link);
    };
  }, []);

  // ==========================================
  // 核心 State 管理
  // ==========================================
  const [activeTab, setActiveTab] = useState('today'); // today, calendar, bucket, dashboard, categories, coach
  const [selectedDate, setSelectedDate] = useState(getTodayString());
  
  // 1. 使用者自訂習慣設定
  const [settings, setSettings] = useState(() => {
    const saved = localStorage.getItem('yolo_settings');
    return saved ? JSON.parse(saved) : DEFAULT_SETTINGS;
  });

  // 2. 每日記錄
  const [records, setRecords] = useState(() => {
    const saved = localStorage.getItem('yolo_records');
    return saved ? JSON.parse(saved) : INITIAL_RECORDS;
  });

  // 3. 願願清單
  const [bucketList, setBucketList] = useState(() => {
    const saved = localStorage.getItem('yolo_bucket');
    return saved ? JSON.parse(saved) : DEFAULT_BUCKET_LIST;
  });

  // ==========================================
  // UI 輔助與互動 State
  // ==========================================
  const [breathStage, setBreathStage] = useState('idle'); // idle, inhale1, inhale2, hold, exhale
  const [breathCount, setBreathCount] = useState(0);
  const [breathProgress, setBreathProgress] = useState(1.0); // For dynamic visual scaling
  const [aiApiKey, setAiApiKey] = useState('');
  const [aiResponse, setAiResponse] = useState('');
  const [aiLoading, setAiLoading] = useState(false);
  const [aiError, setAiError] = useState('');

  // 轉念器 (CBT Mini Widget)
  const [cbtThought, setCbtThought] = useState('');
  const [cbtReframed, setCbtReframed] = useState('');
  const [cbtCategory, setCbtCategory] = useState('self-blame'); // self-blame, stress, labels

  // 編輯表單臨時 state
  const [newItemName, setNewItemName] = useState('');
  const [newTargetMin, setNewTargetMin] = useState(30);
  const [newWeeklyTarget, setNewWeeklyTarget] = useState(3);
  
  // 願望清單新增/編輯 state
  const [newDream, setNewDream] = useState('');
  const [newDreamCat, setNewDreamCat] = useState('自我成長');
  const [newMilestoneText, setNewMilestoneText] = useState('');
  const [newWeeklyAction, setNewWeeklyAction] = useState('');
  const [newDailyTask, setNewDailyTask] = useState('');

  // 彈出視窗提示
  const [toastMessage, setToastMessage] = useState('');

  // ==========================================
  // 激勵金句自動輪播 ＆ 漸變過渡 State 
  // ==========================================
  const [currentQuoteIndex, setCurrentQuoteIndex] = useState(0);
  const [quoteFade, setQuoteFade] = useState(true);

  // 定期自動切換金句 (8秒)
  useEffect(() => {
    const quoteInterval = setInterval(() => {
      triggerQuoteChange();
    }, 8000);
    return () => clearInterval(quoteInterval);
  }, []);

  const triggerQuoteChange = (targetIndex = null) => {
    setQuoteFade(false); // 觸發淡出
    setTimeout(() => {
      setCurrentQuoteIndex((prev) => {
        if (targetIndex !== null) return targetIndex;
        return (prev + 1) % MOTIVATIONAL_QUOTES.length;
      });
      setQuoteFade(true); // 觸發淡入
    }, 400); 
  };

  // 手動點擊金句
  const handleQuoteClick = () => {
    triggerQuoteChange();
    showToast("✨ 大腦迴路放電！載入下一句神經啟示。");
  };

  // ==========================================
  // 同步 LocalStorage
  // ==========================================
  useEffect(() => {
    localStorage.setItem('yolo_settings', JSON.stringify(settings));
  }, [settings]);

  useEffect(() => {
    localStorage.setItem('yolo_records', JSON.stringify(records));
  }, [records]);

  useEffect(() => {
    localStorage.setItem('yolo_bucket', JSON.stringify(bucketList));
  }, [bucketList]);

  const showToast = (msg) => {
    setToastMessage(msg);
    setTimeout(() => setToastMessage(''), 3500);
  };

  // ==========================================
  // 今日數據初始化
  // ==========================================
  const getDayRecord = (dateStr) => {
    if (records[dateStr]) {
      const rec = records[dateStr];
      const mergedCognitive = settings.cognitive.map(sItem => {
        const existing = rec.cognitiveItems?.find(i => i.id === sItem.id);
        return existing ? existing : { id: sItem.id, name: sItem.name, completed: false };
      });
      const mergedExercise = settings.exercise.map(sItem => {
        const existing = rec.exerciseItems?.find(i => i.id === sItem.id);
        return existing ? existing : { id: sItem.id, name: sItem.name, completedMin: 0, targetMin: sItem.targetMin, completed: false };
      });
      const mergedSleep = settings.sleep.rituals.map(sItem => {
        const existing = rec.sleepRituals?.find(i => i.id === sItem.id);
        return existing ? existing : { id: sItem.id, name: sItem.name, completed: false };
      });
      const mergedLearning = settings.learning.map(sItem => {
        const existing = rec.learningItems?.find(i => i.id === sItem.id);
        return existing ? existing : { id: sItem.id, name: sItem.name, completedMin: 0, targetMin: sItem.targetMin, completed: false };
      });

      return {
        ...rec,
        cognitiveItems: mergedCognitive,
        exerciseItems: mergedExercise,
        sleepRituals: mergedSleep,
        learningItems: mergedLearning
      };
    }

    return {
      mood: '😐',
      cognitiveItems: settings.cognitive.map(item => ({ id: item.id, name: item.name, completed: false })),
      exerciseItems: settings.exercise.map(item => ({ id: item.id, name: item.name, completedMin: 0, targetMin: item.targetMin, completed: false })),
      sleepRituals: settings.sleep.rituals.map(item => ({ id: item.id, name: item.name, completed: false })),
      learningItems: settings.learning.map(item => ({ id: item.id, name: item.name, completedMin: 0, targetMin: item.targetMin, completed: false })),
      sleepHours: 7.0,
      note: ''
    };
  };

  const currentRecord = getDayRecord(selectedDate);

  const updateCurrentRecord = (updater) => {
    const updated = typeof updater === 'function' ? updater(currentRecord) : updater;
    setRecords(prev => ({
      ...prev,
      [selectedDate]: {
        ...currentRecord,
        ...updated
      }
    }));
  };

  // ==========================================
  // 生理性嘆息 (Physiological Sigh) 核心動畫引導
  // ==========================================
  const breathTimerRef = useRef(null);
  
  const startBreathing = () => {
    if (breathTimerRef.current) clearInterval(breathTimerRef.current);
    runBreathSequence();
  };

  const runBreathSequence = () => {
    let second = 0;
    setBreathStage('inhale1');
    setBreathProgress(1.2);

    breathTimerRef.current = setInterval(() => {
      second++;
      if (second === 4) {
        setBreathStage('inhale2');
        setBreathProgress(1.4);
      } else if (second === 5.5) {
        setBreathStage('hold');
        setBreathProgress(1.4);
      } else if (second === 7) {
        setBreathStage('exhale');
        setBreathProgress(0.9);
      } else if (second === 13) {
        clearInterval(breathTimerRef.current);
        setBreathStage('idle');
        setBreathProgress(1.0);
        setBreathCount(c => c + 1);
        showToast("✨ 一次完美的「生理性嘆息」！杏仁核已被冷卻，理智與副交感神經重新連線。");
      }
    }, 1000);
  };

  useEffect(() => {
    return () => {
      if (breathTimerRef.current) clearInterval(breathTimerRef.current);
    };
  }, []);

  // ==========================================
  // CBT 轉念器 (改寫負面標籤與原廠自責)
  // ==========================================
  const handleCbtRewire = () => {
    if (!cbtThought.trim()) return;

    let solution = '';
    if (cbtCategory === 'self-blame') {
      solution = `【神經學轉念】\n「這只是我的原始大腦天生原廠設定：關注負面並以『自責』來換取『能控制現狀』的錯覺。神經可塑性表明，大腦是一塊黏土而非鬆緊帶，今天的失誤不會讓我隔天就被打回原形。我原諒自己，下一次再重複新路就好！」`;
    } else if (cbtCategory === 'stress') {
      solution = `【神經學轉念】\n「當下我的身體正處於慢性壓力的戰逃反應。當大腦超載時，會自動關閉耗能的高級認知功能，轉為『省電低電量運行模式』。這不是我不夠自律，而是神經系統正在保護我。此時最需要的是 5 次生理性嘆息與提早 30 分鐘去睡覺，而不是繼續苛責。」`;
    } else {
      solution = `【神經學轉念】\n「這個負面評估只是過去大人或環境隨手貼在我這塊黏土上的有毒標籤。這並非不可逆的事實。只要我開始有意識地啟動『注意力』，在觸發與反應之間創造出暫停的剎車，舊的高速公路突觸就會弱化，新神經元便會連線！」`;
    }

    setCbtReframed(solution);
    showToast("🧠 成功改寫有毒想法！新突觸線路正在放電發光。");
  };

  // ==========================================
  // 計算分數系統 (Dashboard & Life OS)
  // ==========================================
  const calculateScores = (record, dateStr) => {
    if (!record) return { cogScore: 0, exeScore: 0, sleepScore: 0, learnScore: 0, totalScore: 0, neuroScore: 0 };
    
    const cogTotal = record.cognitiveItems?.length || 0;
    const cogDone = record.cognitiveItems?.filter(i => i.completed).length || 0;
    const cogScore = cogTotal > 0 ? Math.round((cogDone / cogTotal) * 100) : 100;

    const exeTotal = record.exerciseItems?.length || 0;
    const exeDone = record.exerciseItems?.filter(i => i.completed || (i.completedMin >= i.targetMin)).length || 0;
    const exeScore = exeTotal > 0 ? Math.round((exeDone / exeTotal) * 100) : 100;

    const sleepRitualTotal = record.sleepRituals?.length || 0;
    const sleepRitualDone = record.sleepRituals?.filter(i => i.completed).length || 0;
    const ritualRatio = sleepRitualTotal > 0 ? (sleepRitualDone / sleepRitualTotal) : 1;
    const sleepHoursTarget = 7.5;
    const hoursRatio = Math.min(record.sleepHours / sleepHoursTarget, 1.2);
    const hoursScore = hoursRatio >= 0.9 && hoursRatio <= 1.1 ? 100 : Math.round(hoursRatio * 80);
    const sleepScore = Math.min(Math.round((ritualRatio * 40) + (hoursScore * 0.6)), 100);

    const learnTotal = record.learningItems?.length || 0;
    const learnDone = record.learningItems?.filter(i => i.completed || (i.completedMin >= i.targetMin)).length || 0;
    const learnScore = learnTotal > 0 ? Math.round((learnDone / learnTotal) * 100) : 100;

    const totalScore = Math.round((cogScore + exeScore + sleepScore + learnScore) / 4);

    const neuroScore = Math.min(
      Math.round(
        (cogScore * 0.3) + 
        (sleepScore * 0.3) + 
        (exeScore * 0.2) + 
        (learnScore * 0.2)
      ), 
      100
    );

    return { cogScore, exeScore, sleepScore, learnScore, totalScore, neuroScore };
  };

  const dayScores = calculateScores(currentRecord, selectedDate);

  // ==========================================
  // 自訂分類增刪
  // ==========================================
  const addConfigItem = (category) => {
    if (!newItemName.trim()) return;
    
    if (category === 'cognitive') {
      const newItem = { id: `cog-${Date.now()}`, name: newItemName, completed: false };
      setSettings(prev => ({ ...prev, cognitive: [...prev.cognitive, newItem] }));
      showToast("成功新增常駐認知重塑項目！");
    } else if (category === 'exercise') {
      const newItem = { id: `exe-${Date.now()}`, name: newItemName, targetMin: Number(newTargetMin), weeklyTarget: Number(newWeeklyTarget), completed: false };
      setSettings(prev => ({ ...prev, exercise: [...prev.exercise, newItem] }));
      showToast("成功新增常駐有氧運動！");
    } else if (category === 'sleep') {
      const newItem = { id: `slp-${Date.now()}`, name: newItemName, completed: false };
      setSettings(prev => ({
        ...prev,
        sleep: { ...prev.sleep, rituals: [...prev.sleep.rituals, newItem] }
      }));
      showToast("成功新增睡前放鬆儀式！");
    } else if (category === 'learning') {
      const newItem = { id: `lrn-${Date.now()}`, name: newItemName, targetMin: Number(newTargetMin), completed: false };
      setSettings(prev => ({ ...prev, learning: [...prev.learning, newItem] }));
      showToast("成功新增常駐刻意學習！");
    }
    setNewItemName('');
  };

  const deleteConfigItem = (category, id) => {
    if (category === 'cognitive') {
      setSettings(prev => ({ ...prev, cognitive: prev.cognitive.filter(i => i.id !== id) }));
    } else if (category === 'exercise') {
      setSettings(prev => ({ ...prev, exercise: prev.exercise.filter(i => i.id !== id) }));
    } else if (category === 'sleep') {
      setSettings(prev => ({
        ...prev,
        sleep: { ...prev.sleep, rituals: prev.sleep.rituals.filter(i => i.id !== id) }
      }));
    } else if (category === 'learning') {
      setSettings(prev => ({ ...prev, learning: prev.learning.filter(i => i.id !== id) }));
    }
    showToast("項目已從原廠設定移除，明天起生效。");
  };

  // ==========================================
  // Bucket List 管理
  // ==========================================
  const addDream = () => {
    if (!newDream.trim() || !newMilestoneText.trim()) {
      showToast("請填寫大夢想與至少一項第一步拆解。");
      return;
    }
    const newBucketItem = {
      id: `bkt-${Date.now()}`,
      dream: newDream,
      category: newDreamCat,
      milestones: [{
        id: `ms-${Date.now()}`,
        text: newMilestoneText,
        weeklyAction: newWeeklyAction,
        dailyTask: newDailyTask,
        completed: false
      }]
    };
    setBucketList(prev => [...prev, newBucketItem]);
    setNewDream('');
    setNewMilestoneText('');
    setNewWeeklyAction('');
    setNewDailyTask('');
    showToast("🏆 大腦已載入人生願望！朝「You Only Live Once」跨出小而踏實的一步。");
  };

  const deleteDream = (id) => {
    setBucketList(prev => prev.filter(item => item.id !== id));
    showToast("已從願望名單中移除。");
  };

  const toggleMilestone = (dreamId, milestoneId) => {
    setBucketList(prev => prev.map(item => {
      if (item.id === dreamId) {
        return {
          ...item,
          milestones: item.milestones.map(ms => {
            if (ms.id === milestoneId) {
              const nextStatus = !ms.completed;
              if (nextStatus) {
                showToast("🎉 突觸大放電！你正在把口號變成可執行的人生系統。");
              }
              return { ...ms, completed: nextStatus };
            }
            return ms;
          })
        };
      }
      return item;
    }));
  };

  // ==========================================
  // 月曆生成邏輯
  // ==========================================
  const [currentCalendarYear, setCurrentCalendarYear] = useState(2026);
  const [currentCalendarMonth, setCurrentCalendarMonth] = useState(5); 

  const daysInMonth = (year, month) => new Date(year, month + 1, 0).getDate();
  const firstDayIndex = (year, month) => new Date(year, month, 1).getDay();

  const handlePrevMonth = () => {
    if (currentCalendarMonth === 0) {
      setCurrentCalendarMonth(11);
      setCurrentCalendarYear(y => y - 1);
    } else {
      setCurrentCalendarMonth(m => m - 1);
    }
  };

  const handleNextMonth = () => {
    if (currentCalendarMonth === 11) {
      setCurrentCalendarMonth(0);
      setCurrentCalendarYear(y => y + 1);
    } else {
      setCurrentCalendarMonth(m => m + 1);
    }
  };

  const monthNames = ["一月", "二月", "三月", "四月", "五月", "六月", "七月", "八月", "九月", "十月", "十一月", "十二月"];

  // ==========================================
  // AI Coach (Gemini 2.5 Flash)
  // ==========================================
  const askAICoach = async () => {
    setAiLoading(true);
    setAiError('');
    setAiResponse('');

    const recentRecordsText = Object.entries(records)
      .slice(-5)
      .map(([date, rec]) => {
        const scores = calculateScores(rec, date);
        return `- 日期: ${date} | 心情: ${rec.mood} | Life Score: ${scores.totalScore}% | Neuro Score: ${scores.neuroScore} | 睡眠時數: ${rec.sleepHours}小時`;
      })
      .join('\n');

    const promptText = `
      這是使用者近期的 YOLO 神經重塑日記：
      ${recentRecordsText}

      今天 (${selectedDate}) 的詳細打卡狀況如下：
      - 心情狀態：${currentRecord.mood}
      - 認知重構活動：${currentRecord.cognitiveItems?.map(i => `${i.name}(${i.completed ? '已完成' : '未完成'})`).join(', ')}
      - 有氧與肌力運動：${currentRecord.exerciseItems?.map(i => `${i.name}(做 ${i.completedMin}分/目標 ${i.targetMin}分)`).join(', ')}
      - 睡眠時數：${currentRecord.sleepHours} 小時
      - 睡前儀式：${currentRecord.sleepRituals?.map(i => `${i.name}(${i.completed ? '已完成' : '未完成'})`).join(', ')}
      - 刻意學習：${currentRecord.learningItems?.map(i => `${i.name}(進行 ${i.completedMin}分/目標 ${i.targetMin}分)`).join(', ')}

      請扮演一位充滿溫柔、同理心、且精通神經科學的《Rewire》AI 導師。
      請針對使用者的數據提供以下幾點解析與回饋：
      1. 用極溫和且正向的口吻肯定他今天的微小進步或挑戰行為 (哪怕只完成一項也切忌譴責自責)。
      2. 運用書中的神經學理論（如：一起放電的神經元會彼此連結、改變是非線性的、慢性壓力時原始腦會開啟低電量模式），給予暖心的分析。
      3. 給出今晚可立刻實行、低耗能的「一小步」微調建議（如生理性嘆息呼吸、一念之轉、早15分鐘睡覺）。
      請用繁體中文回答，口吻溫暖而智慧。
    `;

    const systemPrompt = "你是一位精通《Rewire 神經可塑性》與認知行為療法(CBT)的 YOLO 人生溫柔導師。你的回答結構嚴謹、排版精美，字數約 350-500 字，絕對不指責使用者，提倡溫和的、漸進的微習慣積累。";

    try {
      let delay = 1000;
      let success = false;
      let textResult = "";

      for (let i = 0; i < 5; i++) {
        try {
          const response = await fetch(`https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-09-2025:generateContent?key=${aiApiKey}`, {
            method: "POST",
            headers: { "Content-Type": "application/json" },
            body: JSON.stringify({
              contents: [{ parts: [{ text: promptText }] }],
              systemInstruction: { parts: [{ text: systemPrompt }] }
            })
          });

          if (!response.ok) throw new Error(`HTTP 錯誤: ${response.status}`);
          const resJson = await response.json();
          textResult = resJson.candidates?.[0]?.content?.parts?.[0]?.text || "無法獲取 AI 導師的回應。";
          success = true;
          break;
        } catch (innerErr) {
          if (i === 4) throw innerErr;
          await new Promise(r => setTimeout(r, delay));
          delay *= 2;
        }
      }

      if (success) {
        setAiResponse(textResult);
      }
    } catch (err) {
      setAiError('發送請求失敗。請確認 API Key 是否有效，或稍後再試。' + err.message);
    } finally {
      setAiLoading(false);
    }
  };

  return (
    <div className="min-h-screen bg-[#FAF7F2] text-[#3E3933] font-sans antialiased flex flex-col selection:bg-[#EAE1D4] selection:text-[#3E3933] pb-16 md:pb-0">
      
      {/* ==========================================
          日記感封面視覺 / APP 頂部橫幅
          ========================================== */}
      <div className="flex flex-col items-center justify-center py-6 md:py-9 px-4 bg-gradient-to-b from-[#F2ECE1] to-[#FAF7F2] border-b border-[#EAE3D5] text-center relative overflow-hidden shrink-0">
        <div className="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-[300px] h-[300px] bg-[#8FA89B]/5 rounded-full blur-3xl pointer-events-none"></div>
        
        <span 
          style={{ fontFamily: "'Great Vibes', cursive" }} 
          className="text-4xl md:text-6xl text-[#7E6C5A] tracking-normal mb-0.5 drop-shadow-sm select-none"
        >
          You Only Live Once
        </span>
        
        <span className="text-[9px] md:text-xs tracking-[0.3em] text-[#A29485] uppercase font-light font-mono">
          neuroplasticity &middot; daily practice
        </span>
        
        {/* 自動切換 & 點擊互動 中文激勵金句看板 */}
        <div 
          onClick={handleQuoteClick}
          className="mt-3 px-4 py-1.5 rounded-2xl cursor-pointer hover:bg-[#FAF0E6]/50 border border-transparent hover:border-[#EAE3D5] transition-all duration-300 max-w-xl relative group select-none"
        >
          <p 
            className={`text-[11px] md:text-sm text-[#7E6C5A] leading-relaxed font-light italic transition-opacity duration-300 ${
              quoteFade ? 'opacity-100' : 'opacity-0'
            }`}
          >
            {MOTIVATIONAL_QUOTES[currentQuoteIndex]}
          </p>
          
          <div className="flex items-center justify-center gap-1 mt-0.5 opacity-0 group-hover:opacity-100 transition-opacity duration-300 text-[9px] text-[#A29485]">
            <Lightbulb className="w-2.5 h-2.5 text-[#D49B7A]" />
            <span>點擊點亮下一句神經啟示 ✨</span>
          </div>
        </div>
      </div>

      {/* 頂部即時狀態列 */}
      <div className="bg-white border-b border-[#F0EBE0] px-4 py-2.5 md:px-8 flex items-center justify-between text-xs sticky top-0 z-20 shadow-sm/50">
        <div className="flex items-center gap-2">
          <span className="w-1.5 h-1.5 rounded-full bg-[#8FA89B] animate-pulse"></span>
          <span className="font-semibold text-[#6C655E] text-[10px] md:text-xs">神經重塑狀態良好</span>
        </div>
        
        <div className="flex items-center gap-2 md:gap-3">
          <div className="flex items-center gap-1.5 bg-[#FAF7F2] px-2.5 py-1 rounded-full border border-[#EBE5DB]">
            <CalendarIcon className="w-3 h-3 text-[#8FA89B]" />
            <input 
              type="date" 
              value={selectedDate}
              onChange={(e) => {
                setSelectedDate(e.target.value);
                showToast(`已切換日期至: ${e.target.value}`);
              }}
              className="bg-transparent border-none text-[#5C554E] focus:outline-none text-[10px] font-medium cursor-pointer w-24"
            />
          </div>
          
          <div className="bg-[#FAF0E6] text-[#A66E4E] font-bold px-2 py-0.5 rounded-full text-[9px] md:text-[10px] shrink-0">
            滿意度: {dayScores.totalScore}%
          </div>
        </div>
      </div>

      {/* Toast 提示 */}
      {toastMessage && (
        <div className="fixed bottom-20 md:bottom-6 right-6 z-50 bg-[#4F4941] text-[#FAF7F2] px-4 py-3 rounded-2xl shadow-xl flex items-center gap-2 text-xs animate-fade-in border border-[#787065]/20">
          <Sparkles className="w-4 h-4 text-[#D49B7A]" />
          <span>{toastMessage}</span>
        </div>
      )}

      {/* 主體架構：側邊選單 (Tablet/Desktop) & 手機底部選單 & 主內容 */}
      <div className="flex-1 flex flex-col md:flex-row min-h-0">
        
        {/* 左側選單 - 僅在 md 以上螢幕顯示 (Tablet & Desktop Sidebar) */}
        <aside className="hidden md:flex w-64 bg-[#F5F1E9] p-4 border-r border-[#EAE3D5] flex-col justify-between gap-5 shrink-0 overflow-y-auto">
          <div className="flex flex-col gap-1 w-full">
            <p className="text-[10px] font-bold text-[#A29485] px-3 mb-2 tracking-widest uppercase">系統主導導航</p>
            
            <button 
              onClick={() => setActiveTab('today')}
              className={`flex items-center gap-3 px-3 py-2.5 rounded-xl text-xs font-medium tracking-wide transition-all w-full ${activeTab === 'today' ? 'bg-[#8FA89B] text-white shadow-sm' : 'text-[#6C655E] hover:bg-[#EAE3D5]'}`}
            >
              <Heart className="w-4 h-4" />
              <span>🧠 今日修煉</span>
            </button>

            <button 
              onClick={() => setActiveTab('calendar')}
              className={`flex items-center gap-3 px-3 py-2.5 rounded-xl text-xs font-medium tracking-wide transition-all w-full ${activeTab === 'calendar' ? 'bg-[#8FA89B] text-white shadow-sm' : 'text-[#6C655E] hover:bg-[#EAE3D5]'}`}
            >
              <CalendarIcon className="w-4 h-4" />
              <span>📅 月曆熱力圖</span>
            </button>

            <button 
              onClick={() => setActiveTab('bucket')}
              className={`flex items-center gap-3 px-3 py-2.5 rounded-xl text-xs font-medium tracking-wide transition-all w-full ${activeTab === 'bucket' ? 'bg-[#8FA89B] text-white shadow-sm' : 'text-[#6C655E] hover:bg-[#EAE3D5]'}`}
            >
              <Compass className="w-4 h-4" />
              <span>🔮 YOLO 願望清單</span>
            </button>

            <button 
              onClick={() => setActiveTab('dashboard')}
              className={`flex items-center gap-3 px-3 py-2.5 rounded-xl text-xs font-medium tracking-wide transition-all w-full ${activeTab === 'dashboard' ? 'bg-[#8FA89B] text-white shadow-sm' : 'text-[#6C655E] hover:bg-[#EAE3D5]'}`}
            >
              <Award className="w-4 h-4" />
              <span>📊 自我滿意度</span>
            </button>

            <button 
              onClick={() => setActiveTab('categories')}
              className={`flex items-center gap-3 px-3 py-2.5 rounded-xl text-xs font-medium tracking-wide transition-all w-full ${activeTab === 'categories' ? 'bg-[#8FA89B] text-white shadow-sm' : 'text-[#6C655E] hover:bg-[#EAE3D5]'}`}
            >
              <Settings className="w-4 h-4" />
              <span>⚙️ 四大類別設定</span>
            </button>

            <button 
              onClick={() => setActiveTab('coach')}
              className={`flex items-center gap-3 px-3 py-2.5 rounded-xl text-xs font-medium tracking-wide transition-all w-full ${activeTab === 'coach' ? 'bg-[#8FA89B] text-white shadow-sm' : 'text-[#6C655E] hover:bg-[#EAE3D5]'}`}
            >
              <Sparkles className="w-4 h-4 text-[#D49B7A]" />
              <span>✨ AI 神經重塑教練</span>
            </button>
          </div>

          {/* 生理性嘆息引導 */}
          <div className="p-4 bg-[#FAF7F2] border border-[#EAE3D5] rounded-2xl shadow-sm text-center">
            <div className="flex items-center gap-1.5 justify-center mb-1.5">
              <Brain className="w-4 h-4 text-[#8FA89B]" />
              <h4 className="text-xs font-bold text-[#5C554E]">呼吸重塑 · 嘆息</h4>
            </div>
            <p className="text-[10px] text-[#8C8379] mb-3 leading-relaxed">
              杏仁核冷卻法：快速連續吸氣兩次，憋氣一秒後長長呼氣。
            </p>

            {breathStage === 'idle' ? (
              <button 
                onClick={startBreathing}
                className="w-full py-1.5 bg-[#8FA89B] hover:bg-[#7FA090] text-white text-[11px] font-bold rounded-xl transition flex items-center justify-center gap-1.5"
              >
                <Play className="w-3.5 h-3.5" />
                <span>啟動舒壓引導</span>
              </button>
            ) : (
              <div className="flex flex-col items-center gap-2">
                <div 
                  style={{ transform: `scale(${breathProgress})` }}
                  className={`w-12 h-12 rounded-full flex items-center justify-center transition-all duration-1000 ${
                    breathStage === 'inhale1' || breathStage === 'inhale2' ? 'bg-[#8FA89B]/30 shadow-[0_0_15px_rgba(143,168,155,0.4)]' :
                    breathStage === 'hold' ? 'bg-[#D49B7A]/30 shadow-[0_0_15px_rgba(212,155,122,0.4)]' :
                    'bg-[#8CA3B5]/30 shadow-[0_0_15px_rgba(140,163,181,0.3)]'
                  }`}
                >
                  <span className="text-[9px] font-bold text-[#5C554E]">
                    {breathStage === 'inhale1' && '吸氣'}
                    {breathStage === 'inhale2' && '補吸'}
                    {breathStage === 'hold' && '憋氣'}
                    {breathStage === 'exhale' && '吐氣'}
                  </span>
                </div>
                <span className="text-[10px] font-bold text-[#D49B7A] uppercase tracking-wider animate-pulse">
                  {breathStage === 'inhale1' && '吸氣中 (鼻)'}
                  {breathStage === 'inhale2' && '快速再吸！'}
                  {breathStage === 'hold' && '憋氣一秒'}
                  {breathStage === 'exhale' && '慢速長吐氣...'}
                </span>
              </div>
            )}
            <div className="text-[9px] text-[#A29485] mt-2">今日引導次數: {breathCount} 次</div>
          </div>
        </aside>

        {/* 手機底部 App 導航列 - 僅在手機版 md 以下顯示 (Sleek Mobile App Bottom Navigation Bar) */}
        <nav className="md:hidden fixed bottom-0 left-0 right-0 bg-[#FAF7F2]/90 backdrop-blur-md border-t border-[#EAE3D5] z-40 h-16 flex items-center justify-around px-2 shadow-lg">
          <button 
            onClick={() => setActiveTab('today')}
            className={`flex flex-col items-center justify-center flex-1 py-1 transition-all ${activeTab === 'today' ? 'text-[#8FA89B] scale-105' : 'text-[#8C8379]'}`}
          >
            <Heart className="w-5 h-5 mb-0.5" />
            <span className="text-[9px] font-medium tracking-tight">今日修煉</span>
            {activeTab === 'today' && <span className="w-1 h-1 bg-[#8FA89B] rounded-full mt-0.5"></span>}
          </button>

          <button 
            onClick={() => setActiveTab('calendar')}
            className={`flex flex-col items-center justify-center flex-1 py-1 transition-all ${activeTab === 'calendar' ? 'text-[#8FA89B] scale-105' : 'text-[#8C8379]'}`}
          >
            <CalendarIcon className="w-5 h-5 mb-0.5" />
            <span className="text-[9px] font-medium tracking-tight">月曆追蹤</span>
            {activeTab === 'calendar' && <span className="w-1 h-1 bg-[#8FA89B] rounded-full mt-0.5"></span>}
          </button>

          <button 
            onClick={() => setActiveTab('bucket')}
            className={`flex flex-col items-center justify-center flex-1 py-1 transition-all ${activeTab === 'bucket' ? 'text-[#8FA89B] scale-105' : 'text-[#8C8379]'}`}
          >
            <Compass className="w-5 h-5 mb-0.5" />
            <span className="text-[9px] font-medium tracking-tight">YOLO願望</span>
            {activeTab === 'bucket' && <span className="w-1 h-1 bg-[#8FA89B] rounded-full mt-0.5"></span>}
          </button>

          <button 
            onClick={() => setActiveTab('dashboard')}
            className={`flex flex-col items-center justify-center flex-1 py-1 transition-all ${activeTab === 'dashboard' ? 'text-[#8FA89B] scale-105' : 'text-[#8C8379]'}`}
          >
            <Award className="w-5 h-5 mb-0.5" />
            <span className="text-[9px] font-medium tracking-tight">滿意度</span>
            {activeTab === 'dashboard' && <span className="w-1 h-1 bg-[#8FA89B] rounded-full mt-0.5"></span>}
          </button>

          <button 
            onClick={() => setActiveTab('categories')}
            className={`flex flex-col items-center justify-center flex-1 py-1 transition-all ${activeTab === 'categories' ? 'text-[#8FA89B] scale-105' : 'text-[#8C8379]'}`}
          >
            <Settings className="w-5 h-5 mb-0.5" />
            <span className="text-[9px] font-medium tracking-tight">設定</span>
            {activeTab === 'categories' && <span className="w-1 h-1 bg-[#8FA89B] rounded-full mt-0.5"></span>}
          </button>

          <button 
            onClick={() => setActiveTab('coach')}
            className={`flex flex-col items-center justify-center flex-1 py-1 transition-all ${activeTab === 'coach' ? 'text-[#8FA89B] scale-105' : 'text-[#8C8379]'}`}
          >
            <Sparkles className="w-5 h-5 mb-0.5" />
            <span className="text-[9px] font-medium tracking-tight">AI教練</span>
            {activeTab === 'coach' && <span className="w-1 h-1 bg-[#8FA89B] rounded-full mt-0.5"></span>}
          </button>
        </nav>

        {/* 主要內容區區 (在手機版加 pb-24 以免被 Bottom Nav 遮擋) */}
        <main className="flex-1 p-4 md:p-8 max-w-5xl mx-auto w-full overflow-y-auto pb-24 md:pb-8">
          
          {/* ==========================================
              分頁 1：今日修煉 (Today's Journey)
              ========================================== */}
          {activeTab === 'today' && (
            <div className="space-y-5">
              
              {/* CBT 溫柔一念之轉 mini 工具 */}
              <div className="bg-[#FAF5F0] p-4 md:p-5 rounded-2xl border border-[#D49B7A]/20 shadow-sm relative overflow-hidden">
                <div className="absolute -right-4 -top-4 w-20 h-24 bg-[#D49B7A]/5 rounded-full pointer-events-none"></div>
                
                <div className="flex items-start gap-3">
                  <div className="p-2 bg-[#F5EBE0] text-[#D49B7A] rounded-xl shrink-0">
                    <SmilePlus className="w-4 h-4 md:w-5 md:h-5" />
                  </div>
                  <div className="space-y-2 flex-1 min-w-0">
                    <h3 className="text-[11px] md:text-xs font-bold text-[#6E5C4E] uppercase tracking-wider">🧠 CBT 不自責轉念練習</h3>
                    <p className="text-[10px] md:text-[11px] text-[#8C7D6C] leading-relaxed">
                      當大腦又想陷入內疚與自責（大腦天生渴望掌控感的錯覺）時，立刻改寫它：
                    </p>
                    
                    <div className="flex flex-col gap-2 mt-3">
                      <select 
                        value={cbtCategory}
                        onChange={(e) => setCbtCategory(e.target.value)}
                        className="text-[11px] p-2.5 bg-white border border-[#E9E1D5] rounded-xl focus:outline-none min-h-[40px] w-full"
                      >
                        <option value="self-blame">犯錯自責（如：暴食、熬夜、偷懶）</option>
                        <option value="stress">感到過度焦慮、全身消耗怠盡</option>
                        <option value="labels">被貼負面標籤（如：我不善言辭）</option>
                      </select>
                      
                      <div className="flex gap-2">
                        <input 
                          type="text" 
                          placeholder="例如: 我今天又沒運動，真沒自律" 
                          value={cbtThought}
                          onChange={(e) => setCbtThought(e.target.value)}
                          className="flex-1 text-[11px] p-2.5 bg-white border border-[#E9E1D5] rounded-xl focus:outline-none min-h-[40px]"
                        />
                        
                        <button 
                          onClick={handleCbtRewire}
                          className="px-3 py-2 bg-[#D49B7A] text-white text-[11px] font-bold rounded-xl hover:bg-[#C48C6B] transition active:scale-95 shrink-0"
                        >
                          重塑
                        </button>
                      </div>
                    </div>

                    {cbtReframed && (
                      <div className="mt-3 p-3 bg-white rounded-xl border border-[#EBE3D7] text-[11px] text-[#5C5045] leading-relaxed italic animate-fade-in whitespace-pre-wrap">
                        {cbtReframed}
                      </div>
                    )}
                  </div>
                </div>
              </div>

              {/* 生理性嘆息手機內嵌版 (僅在手機螢幕展示以補足呼吸 widget 隱藏的問題) */}
              <div className="md:hidden bg-white p-4 rounded-3xl border border-[#EFECE6] shadow-sm flex items-center justify-between">
                <div className="flex items-center gap-2.5 min-w-0">
                  <div className="p-2 bg-[#F1F4EE] rounded-xl text-[#8FA89B] shrink-0">
                    <Brain className="w-5 h-5 animate-pulse" />
                  </div>
                  <div className="min-w-0">
                    <h4 className="text-xs font-bold text-[#4F4941]">生理性嘆息放鬆</h4>
                    <p className="text-[10px] text-[#8C8379] truncate">今日已嘆息 {breathCount} 次</p>
                  </div>
                </div>
                
                {breathStage === 'idle' ? (
                  <button 
                    onClick={startBreathing}
                    className="px-3 py-1.5 bg-[#8FA89B] text-white text-[11px] font-bold rounded-xl active:scale-95 transition"
                  >
                    深呼吸
                  </button>
                ) : (
                  <span className="text-[10px] font-bold text-[#D49B7A] animate-pulse">
                    {breathStage === 'inhale1' && '吸氣1/2'}
                    {breathStage === 'inhale2' && '快速補吸'}
                    {breathStage === 'hold' && '憋氣中'}
                    {breathStage === 'exhale' && '慢長吐氣'}
                  </span>
                )}
              </div>

              {/* 四大打卡區 */}
              <div className="grid grid-cols-1 md:grid-cols-2 gap-5">
                
                {/* 1. 認知 🧠 */}
                <div className="bg-white p-4 md:p-5 rounded-3xl border border-[#EFECE6] shadow-sm hover:shadow-md transition">
                  <div className="flex items-center justify-between mb-4 pb-2 border-b border-[#FAF7F2]">
                    <div className="flex items-center gap-2">
                      <div className="p-2 bg-[#F1F4EE] rounded-xl text-[#8FA89B]">
                        <Brain className="w-4.5 h-4.5" />
                      </div>
                      <div>
                        <h4 className="font-bold text-xs text-[#4F4941]">🧠 認知神經重置</h4>
                        <p className="text-[9px] md:text-[10px] text-[#918B80]">練習注意力暫停，切斷有毒路徑</p>
                      </div>
                    </div>
                    <span className="text-xs font-bold text-[#8FA89B]">
                      {currentRecord.cognitiveItems?.filter(i => i.completed).length || 0} / {currentRecord.cognitiveItems?.length || 0}
                    </span>
                  </div>

                  <div className="space-y-2">
                    {currentRecord.cognitiveItems?.map((item) => (
                      <div 
                        key={item.id}
                        onClick={() => {
                          const updated = currentRecord.cognitiveItems.map(i => i.id === item.id ? { ...i, completed: !i.completed } : i);
                          updateCurrentRecord({ cognitiveItems: updated });
                          if (!item.completed) showToast("👍 注意力已集中！新突觸正在連結。");
                        }}
                        className={`flex items-start gap-3 p-3 rounded-2xl cursor-pointer border transition-all min-h-[44px] ${item.completed ? 'bg-[#FAF7F2] border-[#DFE7E2] text-[#807970]' : 'bg-white border-[#EFECE6] hover:border-[#8FA89B]'}`}
                      >
                        <div className={`mt-0.5 w-4.5 h-4.5 rounded-md flex items-center justify-center border shrink-0 ${item.completed ? 'bg-[#8FA89B] border-[#8FA89B] text-white' : 'border-[#D9D1C5]'}`}>
                          {item.completed && <Check className="w-3.5 h-3.5" />}
                        </div>
                        <span className={`text-[11px] md:text-xs leading-relaxed ${item.completed ? 'line-through opacity-60' : 'font-medium'}`}>{item.name}</span>
                      </div>
                    ))}
                  </div>
                </div>

                {/* 2. 運動 🏃 */}
                <div className="bg-white p-4 md:p-5 rounded-3xl border border-[#EFECE6] shadow-sm hover:shadow-md transition">
                  <div className="flex items-center justify-between mb-4 pb-2 border-b border-[#FAF7F2]">
                    <div className="flex items-center gap-2">
                      <div className="p-2 bg-[#FAF5F0] rounded-xl text-[#D49B7A]">
                        <Dumbbell className="w-4.5 h-4.5" />
                      </div>
                      <div>
                        <h4 className="font-bold text-xs text-[#4F4941]">🏃 運動釋放 BDNF</h4>
                        <p className="text-[9px] md:text-[10px] text-[#918B80]">刺激大腦海馬迴，滋養全新神經突觸</p>
                      </div>
                    </div>
                    <span className="text-[9px] text-white bg-[#D49B7A] px-2 py-0.5 rounded-full font-bold shrink-0">BDNF 肥料</span>
                  </div>

                  <div className="space-y-4">
                    {currentRecord.exerciseItems?.map((item) => (
                      <div key={item.id} className="p-3 bg-[#FAF7F2] rounded-2xl border border-[#EFECE6]">
                        <div className="flex items-center justify-between mb-2">
                          <span className="text-[11px] font-bold text-[#5C554E] truncate max-w-[150px]">{item.name}</span>
                          <span className="text-[9px] text-[#9E6C4C] font-semibold bg-[#F5EBE0] px-2 py-0.5 rounded-md">目標: {item.targetMin} 分</span>
                        </div>

                        <div className="flex items-center gap-3">
                          <input 
                            type="range" 
                            min="0" 
                            max={Math.max(item.targetMin * 2, 60)} 
                            value={item.completedMin}
                            onChange={(e) => {
                              const val = Number(e.target.value);
                              const updated = currentRecord.exerciseItems.map(i => i.id === item.id ? { ...i, completedMin: val, completed: val >= i.targetMin } : i);
                              updateCurrentRecord({ exerciseItems: updated });
                            }}
                            className="flex-1 accent-[#8FA89B] h-1.5 bg-[#EFECE6] rounded-full appearance-none cursor-pointer"
                          />
                          <div className="flex items-center gap-1 shrink-0">
                            <input 
                              type="number" 
                              value={item.completedMin}
                              onChange={(e) => {
                                const val = Number(e.target.value);
                                const updated = currentRecord.exerciseItems.map(i => i.id === item.id ? { ...i, completedMin: val, completed: val >= i.targetMin } : i);
                                updateCurrentRecord({ exerciseItems: updated });
                              }}
                              className="w-10 text-center text-xs font-bold border border-[#E9E1D5] rounded py-1 bg-white min-h-[30px]"
                            />
                            <span className="text-[10px] text-[#8C8379]">分</span>
                          </div>
                        </div>
                      </div>
                    ))}
                  </div>
                </div>

                {/* 3. 睡眠 😴 */}
                <div className="bg-white p-4 md:p-5 rounded-3xl border border-[#EFECE6] shadow-sm hover:shadow-md transition">
                  <div className="flex items-center justify-between mb-4 pb-2 border-b border-[#FAF7F2]">
                    <div className="flex items-center gap-2">
                      <div className="p-2 bg-[#EFF3F6] rounded-xl text-[#8CA3B5]">
                        <Moon className="w-4.5 h-4.5" />
                      </div>
                      <div>
                        <h4 className="font-bold text-xs text-[#4F4941]">😴 充足睡眠充電</h4>
                        <p className="text-[9px] md:text-[10px] text-[#918B80]">排除大腦廢物，固化前一日學習線路</p>
                      </div>
                    </div>
                    <span className="text-[10px] text-[#8CA3B5] font-semibold">起: {settings.sleep.wakeUpTime}</span>
                  </div>

                  <div className="space-y-4">
                    <div className="p-3 bg-[#FAF7F2] rounded-2xl flex items-center justify-between">
                      <div>
                        <span className="text-[11px] font-bold text-[#5C554E]">昨晚睡眠時長</span>
                        <p className="text-[9px] text-[#A29485]">排毒黃金窗: 7.5 ~ 8小時</p>
                      </div>
                      <div className="flex items-center gap-1 bg-white px-2 py-1 rounded-xl border border-[#EFECE6]">
                        <input 
                          type="number" 
                          step="0.5"
                          min="0"
                          max="24"
                          value={currentRecord.sleepHours}
                          onChange={(e) => updateCurrentRecord({ sleepHours: Number(e.target.value) })}
                          className="w-9 text-center text-xs font-bold bg-transparent border-none focus:outline-none py-1"
                        />
                        <span className="text-[10px] text-[#8C8379]">小時</span>
                      </div>
                    </div>

                    <div>
                      <span className="text-[10px] font-bold text-[#8C8379] block mb-2 uppercase">睡前放鬆儀式：</span>
                      <div className="space-y-2">
                        {currentRecord.sleepRituals?.map((item) => (
                          <div 
                            key={item.id}
                            onClick={() => {
                              const updated = currentRecord.sleepRituals.map(i => i.id === item.id ? { ...i, completed: !i.completed } : i);
                              updateCurrentRecord({ sleepRituals: updated });
                            }}
                            className={`flex items-center gap-3 p-2.5 rounded-xl cursor-pointer border transition min-h-[40px] ${item.completed ? 'bg-[#FAF7F2] border-[#DFE7E2]' : 'bg-white border-[#EFECE6] hover:border-[#8FA89B]'}`}
                          >
                            <div className={`w-4 h-4 rounded-md flex items-center justify-center border shrink-0 ${item.completed ? 'bg-[#8FA89B] border-[#8FA89B] text-white' : 'border-[#D9D1C5]'}`}>
                              {item.completed && <Check className="w-3 h-3" />}
                            </div>
                            <span className="text-[11px] md:text-xs">{item.name}</span>
                          </div>
                        ))}
                      </div>
                    </div>
                  </div>
                </div>

                {/* 4. 學習 📚 */}
                <div className="bg-white p-4 md:p-5 rounded-3xl border border-[#EFECE6] shadow-sm hover:shadow-md transition">
                  <div className="flex items-center justify-between mb-4 pb-2 border-b border-[#FAF7F2]">
                    <div className="flex items-center gap-2">
                      <div className="p-2 bg-[#F6F2F8] rounded-xl text-[#A491A8]">
                        <BookOpen className="w-4.5 h-4.5" />
                      </div>
                      <div>
                        <h4 className="font-bold text-xs text-[#4F4941]">📚 刻意持續學習</h4>
                        <p className="text-[9px] md:text-[10px] text-[#918B80]">挑戰不適感，開闢寬闊迷人的新道路</p>
                      </div>
                    </div>
                    <span className="text-[9px] text-[#A491A8] font-bold font-mono shrink-0">習慣重塑</span>
                  </div>

                  <div className="space-y-4">
                    {currentRecord.learningItems?.map((item) => (
                      <div key={item.id} className="p-3 bg-[#FAF7F2] rounded-2xl border border-[#EFECE6]">
                        <div className="flex items-center justify-between mb-2">
                          <span className="text-[11px] font-bold text-[#5C554E] truncate max-w-[150px]">{item.name}</span>
                          <span className="text-[9px] text-[#86668C] font-semibold bg-[#F5ECF6] px-2 py-0.5 rounded-md">目標: {item.targetMin} 分</span>
                        </div>

                        <div className="flex items-center gap-3">
                          <input 
                            type="range" 
                            min="0" 
                            max={Math.max(item.targetMin * 2, 60)} 
                            value={item.completedMin}
                            onChange={(e) => {
                              const val = Number(e.target.value);
                              const updated = currentRecord.learningItems.map(i => i.id === item.id ? { ...i, completedMin: val, completed: val >= i.targetMin } : i);
                              updateCurrentRecord({ learningItems: updated });
                            }}
                            className="flex-1 accent-[#8FA89B] h-1.5 bg-[#EFECE6] rounded-full appearance-none cursor-pointer"
                          />
                          <div className="flex items-center gap-1 shrink-0">
                            <input 
                              type="number" 
                              value={item.completedMin}
                              onChange={(e) => {
                                const val = Number(e.target.value);
                                const updated = currentRecord.learningItems.map(i => i.id === item.id ? { ...i, completedMin: val, completed: val >= i.targetMin } : i);
                                updateCurrentRecord({ learningItems: updated });
                              }}
                              className="w-10 text-center text-xs font-bold border border-[#E9E1D5] rounded py-1 bg-white min-h-[30px]"
                            />
                            <span className="text-[10px] text-[#8C8379]">分</span>
                          </div>
                        </div>
                      </div>
                    ))}
                  </div>
                </div>

              </div>

              {/* 今日心情狀態選擇器 */}
              <div className="bg-white p-4 rounded-3xl border border-[#EFECE6] shadow-sm flex flex-col md:flex-row md:items-center justify-between gap-3">
                <span className="text-xs font-bold text-[#5C554E]">今日大腦心情放電狀態:</span>
                <div className="flex gap-2 justify-around">
                  {['😄', '🙂', '😐', '😔', '😫'].map(emoji => (
                    <button 
                      key={emoji}
                      onClick={() => {
                        updateCurrentRecord({ mood: emoji });
                        showToast(`設定心情為 ${emoji}`);
                      }}
                      className={`text-xl p-2 rounded-xl transition-all active:scale-125 min-w-[44px] min-h-[44px] flex items-center justify-center ${currentRecord.mood === emoji ? 'bg-[#FAF5F0] border border-[#E9E1D5] shadow-sm scale-110' : 'opacity-50'}`}
                    >
                      {emoji}
                    </button>
                  ))}
                </div>
              </div>

              {/* 每日備註 */}
              <div className="bg-white p-4 md:p-6 rounded-3xl border border-[#EFECE6] shadow-sm">
                <span className="text-[11px] md:text-xs font-bold text-[#8C8379] block mb-2 uppercase tracking-wide">今日大腦思考與感恩日記：</span>
                <textarea 
                  rows="3" 
                  value={currentRecord.note || ''}
                  onChange={(e) => updateCurrentRecord({ note: e.target.value })}
                  placeholder="寫下今天的小勝利，好好讚美自己；即便遇到挫折也不要自責，這只是大腦為了生存節省體力，明天再重新起步即可。"
                  className="w-full text-xs p-3 bg-[#FAF7F2] border border-[#EFECE6] rounded-2xl focus:outline-none min-h-[80px]"
                />
              </div>

            </div>
          )}

          {/* ==========================================
              分頁 2：月曆熱力圖 (Interactive Heatmap)
              ========================================== */}
          {activeTab === 'calendar' && (
            <div className="space-y-5">
              
              <div className="bg-white p-4 md:p-6 rounded-3xl border border-[#EFECE6] shadow-sm">
                <div className="flex flex-col md:flex-row md:items-center justify-between gap-4 mb-6">
                  <div>
                    <h3 className="font-bold text-sm md:text-base text-[#4F4941]">神經網絡重塑月曆</h3>
                    <p className="text-[10px] md:text-xs text-[#918B80]">點擊任意日期即可回溯查看、直接編輯當天修煉歷史。</p>
                  </div>

                  {/* 月份切換 */}
                  <div className="flex items-center justify-between md:justify-end gap-3 bg-[#FAF7F2] p-1.5 rounded-2xl border border-[#EFECE6] self-start md:self-auto w-full md:w-auto">
                    <button onClick={handlePrevMonth} className="p-1.5 rounded-xl border border-[#EFECE6] hover:bg-white bg-white/50 transition">
                      <ChevronLeft className="w-4 h-4 text-[#5C554E]" />
                    </button>
                    <span className="text-xs font-bold text-[#4F4941] tracking-wider min-w-[80px] text-center">
                      {currentCalendarYear} 年 {monthNames[currentCalendarMonth]}
                    </span>
                    <button onClick={handleNextMonth} className="p-1.5 rounded-xl border border-[#EFECE6] hover:bg-white bg-white/50 transition">
                      <ChevronRight className="w-4 h-4 text-[#5C554E]" />
                    </button>
                  </div>
                </div>

                <div className="grid grid-cols-7 gap-1 text-center mb-3">
                  {["日", "一", "二", "三", "四", "五", "六"].map(d => (
                    <span key={d} className="text-[10px] md:text-xs font-bold text-[#A29485]">{d}</span>
                  ))}
                </div>

                <div className="grid grid-cols-7 gap-1.5 md:gap-2.5">
                  {/* 空格 */}
                  {Array.from({ length: firstDayIndex(currentCalendarYear, currentCalendarMonth) }).map((_, idx) => (
                    <div key={`empty-${idx}`} className="aspect-square bg-transparent"></div>
                  ))}

                  {/* 當月天數 */}
                  {Array.from({ length: daysInMonth(currentCalendarYear, currentCalendarMonth) }).map((_, idx) => {
                    const dayNum = idx + 1;
                    const dateString = `${currentCalendarYear}-${String(currentCalendarMonth + 1).padStart(2, '0')}-${String(dayNum).padStart(2, '0')}`;
                    const record = records[dateString];
                    const scores = calculateScores(record, dateString);

                    let colorStyle = "bg-[#FAF7F2] border-[#E9E1D5] hover:border-[#8FA89B] text-[#5C554E]";
                    let dotStyle = "bg-[#D9D1C5]";

                    if (record) {
                      const completedCount = 
                        (record.cognitiveItems?.some(i => i.completed) ? 1 : 0) +
                        (record.exerciseItems?.some(i => i.completedMin > 0) ? 1 : 0) +
                        (record.sleepRituals?.some(i => i.completed) ? 1 : 0) +
                        (record.learningItems?.some(i => i.completedMin > 0) ? 1 : 0);

                      if (completedCount >= 4) {
                        colorStyle = "bg-[#EBF2EE] border-[#CDDFD6] hover:border-[#8FA89B] text-[#3F5448]";
                        dotStyle = "bg-[#8FA89B]";
                      } else if (completedCount >= 2) {
                        colorStyle = "bg-[#FDF6F2] border-[#F4E3D8] hover:border-[#D49B7A] text-[#7A543E]";
                        dotStyle = "bg-[#D49B7A]";
                      } else {
                        colorStyle = "bg-[#FBF1F3] border-[#F2D7DB] hover:border-[#CE8290] text-[#7E424C]";
                        dotStyle = "bg-[#CE8290]";
                      }
                    }

                    const isCurrent = selectedDate === dateString;

                    return (
                      <div 
                        key={dateString}
                        onClick={() => {
                          setSelectedDate(dateString);
                          showToast(`回溯檢視: ${dateString}`);
                        }}
                        className={`aspect-square rounded-xl md:rounded-2xl flex flex-col justify-between p-1 md:p-2 border cursor-pointer transition ${colorStyle} ${isCurrent ? 'ring-2 ring-[#4F4941] scale-105' : ''}`}
                      >
                        <div className="flex items-center justify-between">
                          <span className="text-[10px] md:text-[11px] font-bold font-mono">{dayNum}</span>
                          {record && <span className="text-[10px] scale-90">{record.mood}</span>}
                        </div>
                        <div className="flex items-center justify-between">
                          <span className={`w-1 md:w-1.5 h-1 md:h-1.5 rounded-full ${dotStyle}`}></span>
                          {record && <span className="text-[8px] md:text-[9px] opacity-75 font-mono">{scores.totalScore}%</span>}
                        </div>
                      </div>
                    );
                  })}
                </div>

                {/* 熱力圖說明 */}
                <div className="flex flex-wrap gap-4 mt-6 pt-4 border-t border-[#FAF7F2] text-[10px] md:text-xs text-[#8C8379] justify-center">
                  <div className="flex items-center gap-1.5">
                    <span className="w-3 h-3 rounded-md bg-[#EBF2EE] border border-[#CDDFD6]"></span>
                    <span>4 大類別皆有實踐 🟢</span>
                  </div>
                  <div className="flex items-center gap-1.5">
                    <span className="w-3 h-3 rounded-md bg-[#FDF6F2] border border-[#F4E3D8]"></span>
                    <span>完成 2 ~ 3 類別 🟡</span>
                  </div>
                  <div className="flex items-center gap-1.5">
                    <span className="w-3 h-3 rounded-md bg-[#FBF1F3] border border-[#F2D7DB]"></span>
                    <span>僅完成 0 ~ 1 類別 🔴</span>
                  </div>
                </div>

              </div>

              {/* 回溯細節面版 */}
              <div className="bg-[#FAF5F0] p-4 md:p-6 rounded-3xl border border-[#E9E1D5] flex flex-col md:flex-row gap-6 items-start">
                <div className="flex-1 space-y-4 w-full">
                  <div className="flex items-center gap-2">
                    <span className="text-[10px] font-bold bg-[#D49B7A] text-white px-2.5 py-0.5 rounded-full">歷史紀錄點</span>
                    <h4 className="font-bold text-xs md:text-sm text-[#4F4941]">補登/檢視歷史數據：{selectedDate}</h4>
                  </div>
                  
                  <div className="grid grid-cols-2 md:grid-cols-4 gap-2.5">
                    <div className="bg-white p-3 rounded-2xl border border-[#EFECE6] text-center">
                      <span className="text-[8px] md:text-[9px] text-[#A29485] block mb-1 uppercase tracking-wide">認知核心</span>
                      <span className="text-[11px] font-bold text-[#5C554E]">
                        {currentRecord.cognitiveItems?.filter(i => i.completed).length || 0}/{currentRecord.cognitiveItems?.length || 0} 已做
                      </span>
                    </div>
                    <div className="bg-white p-3 rounded-2xl border border-[#EFECE6] text-center">
                      <span className="text-[8px] md:text-[9px] text-[#A29485] block mb-1 uppercase tracking-wide">BDNF 運動</span>
                      <span className="text-[11px] font-bold text-[#5C554E]">
                        {currentRecord.exerciseItems?.reduce((acc, curr) => acc + curr.completedMin, 0)} 分鐘
                      </span>
                    </div>
                    <div className="bg-white p-3 rounded-2xl border border-[#EFECE6] text-center">
                      <span className="text-[8px] md:text-[9px] text-[#A29485] block mb-1 uppercase tracking-wide">充電睡眠</span>
                      <span className="text-[11px] font-bold text-[#5C554E]">
                        {currentRecord.sleepHours} 小時
                      </span>
                    </div>
                    <div className="bg-white p-3 rounded-2xl border border-[#EFECE6] text-center">
                      <span className="text-[8px] md:text-[9px] text-[#A29485] block mb-1 uppercase tracking-wide">開拓學習</span>
                      <span className="text-[11px] font-bold text-[#5C554E]">
                        {currentRecord.learningItems?.reduce((acc, curr) => acc + curr.completedMin, 0)} 分鐘
                      </span>
                    </div>
                  </div>

                  <div className="p-3 bg-white rounded-2xl border border-[#EFECE6]">
                    <span className="text-[9px] text-[#A29485] block mb-1 font-bold">該日思考手札</span>
                    <p className="text-[11px] text-[#5C554E] italic leading-relaxed">{currentRecord.note || "無紀錄。"}</p>
                  </div>
                </div>

                <div className="w-full md:w-56 bg-white p-5 rounded-3xl border border-[#EFECE6] text-center shrink-0">
                  <span className="text-[9px] md:text-[10px] font-bold text-[#A29485] block uppercase tracking-wider">神經重塑指數</span>
                  <div className="text-4xl font-extrabold text-[#8FA89B] my-2 font-mono">{dayScores.neuroScore}</div>
                  <p className="text-[10px] text-[#8C8379] leading-relaxed mb-4">今日的綜合重塑分數。</p>
                  <button 
                    onClick={() => {
                      setActiveTab('today');
                      showToast("已載入當日主修煉面板！");
                    }}
                    className="w-full py-2.5 bg-[#8FA89B] hover:bg-[#7FA090] text-white text-xs font-bold rounded-xl active:scale-95 transition min-h-[40px]"
                  >
                    立刻修改此日數據
                  </button>
                </div>
              </div>

            </div>
          )}

          {/* ==========================================
              分頁 3：YOLO 願望清單 (Bucket List)
              ========================================== */}
          {activeTab === 'bucket' && (
            <div className="space-y-5">
              
              <div className="bg-[#FAF5F0] p-4 md:p-6 rounded-3xl border border-[#D49B7A]/30 relative overflow-hidden">
                <div className="absolute right-0 top-0 w-24 h-24 bg-[#D49B7A]/5 rounded-bl-full pointer-events-none"></div>
                <div className="flex items-center gap-3 mb-2">
                  <div className="bg-[#D49B7A] text-white p-2 rounded-xl shrink-0">
                    <Compass className="w-5 h-5" />
                  </div>
                  <div>
                    <h3 className="font-bold text-xs md:text-base text-[#4F4941]">YOLO (You Only Live Once) 行動導向願望清單</h3>
                    <p className="text-[10px] md:text-xs text-[#8C8379] leading-relaxed">《Rewire》：將大夢想拆解為最小單元（週行動與日重複），讓口號轉移至大腦基底核，轉化為每日下意識行為！</p>
                  </div>
                </div>
              </div>

              {/* 新增人生夢想 */}
              <div className="bg-white p-4 md:p-6 rounded-3xl border border-[#EFECE6] space-y-4 shadow-sm">
                <h4 className="font-bold text-xs text-[#4F4941] uppercase tracking-wider flex items-center gap-1.5">
                  <Plus className="w-4 h-4 text-[#8FA89B]" />
                  <span>添加新夢想藍圖</span>
                </h4>

                <div className="grid grid-cols-1 md:grid-cols-2 gap-3">
                  <div>
                    <label className="block text-[10px] text-[#A29485] font-bold mb-1 uppercase">🔮 大目標 / 夢想：</label>
                    <input 
                      type="text" 
                      placeholder="例如：瑞士阿爾卑斯山健行 / 自立工作室" 
                      value={newDream}
                      onChange={(e) => setNewDream(e.target.value)}
                      className="w-full text-xs p-3 bg-[#FAF7F2] border border-[#EFECE6] rounded-xl focus:outline-none min-h-[44px]"
                    />
                  </div>
                  <div>
                    <label className="block text-[10px] text-[#A29485] font-bold mb-1 uppercase">🏷️ 目標分類：</label>
                    <select 
                      value={newDreamCat}
                      onChange={(e) => setNewDreamCat(e.target.value)}
                      className="w-full text-xs p-3 bg-[#FAF7F2] border border-[#EFECE6] rounded-xl focus:outline-none min-h-[44px]"
                    >
                      <option value="自我成長">自我成長</option>
                      <option value="旅行/體驗">旅行/體驗</option>
                      <option value="事業/財富">事業/財富</option>
                      <option value="健康/生活">健康/生活</option>
                    </select>
                  </div>
                </div>

                <div className="p-4 bg-[#FAF7F2] rounded-2xl border border-[#EFECE6] space-y-3">
                  <span className="text-[11px] font-bold text-[#8FA89B] block">🔬 神經系統降維拆解：</span>
                  
                  <div className="grid grid-cols-1 md:grid-cols-3 gap-3">
                    <div>
                      <label className="block text-[10px] text-[#8C8379] mb-1">1. 目標拆解 (Milestone)</label>
                      <input 
                        type="text" 
                        placeholder="例如：熟悉 React 與 LLM" 
                        value={newMilestoneText}
                        onChange={(e) => setNewMilestoneText(e.target.value)}
                        className="w-full text-xs p-2.5 bg-white border border-[#EFECE6] rounded-lg focus:outline-none min-h-[40px]"
                      />
                    </div>
                    <div>
                      <label className="block text-[10px] text-[#8C8379] mb-1">2. 每週刻意 (Weekly Action)</label>
                      <input 
                        type="text" 
                        placeholder="例如：每週完成一個 API 微型專案" 
                        value={newWeeklyAction}
                        onChange={(e) => setNewWeeklyAction(e.target.value)}
                        className="w-full text-xs p-2.5 bg-white border border-[#EFECE6] rounded-lg focus:outline-none min-h-[40px]"
                      />
                    </div>
                    <div>
                      <label className="block text-[10px] text-[#8C8379] mb-1">3. 每日重複 (Daily Task)</label>
                      <input 
                        type="text" 
                        placeholder="例如：每日寫扣/重構 20 分鐘" 
                        value={newDailyTask}
                        onChange={(e) => setNewDailyTask(e.target.value)}
                        className="w-full text-xs p-2.5 bg-white border border-[#EFECE6] rounded-lg focus:outline-none min-h-[40px]"
                      />
                    </div>
                  </div>
                </div>

                <button 
                  onClick={addDream}
                  className="w-full py-3 bg-[#8FA89B] hover:bg-[#7FA090] text-white text-xs font-bold rounded-xl transition active:scale-98 min-h-[44px]"
                >
                  確認載入神經系統並在每日執行
                </button>
              </div>

              {/* 願望卡片列表 */}
              <div className="space-y-4">
                {bucketList.map((item) => (
                  <div key={item.id} className="bg-white p-4 md:p-5 rounded-3xl border border-[#EFECE6] shadow-sm relative overflow-hidden">
                    <span className="absolute right-0 top-0 bg-[#F5EBE0] text-[#9E6C4C] px-3 py-1 rounded-bl-xl text-[9px] font-bold">
                      {item.category}
                    </span>

                    <div className="flex items-start justify-between mb-4">
                      <div className="min-w-0 pr-12">
                        <h4 className="font-bold text-sm md:text-base text-[#4F4941] truncate">🔮 {item.dream}</h4>
                        <p className="text-[10px] text-[#A29485] mt-0.5">YOLO 專案執行中</p>
                      </div>
                      <button 
                        onClick={() => deleteDream(item.id)}
                        className="text-[#D9D1C5] hover:text-[#CE4257] transition p-2 min-h-[40px] flex items-center"
                      >
                        <Trash2 className="w-4 h-4" />
                      </button>
                    </div>

                    <div className="pl-3 md:pl-4 border-l-2 border-[#E9E1D5] space-y-3">
                      {item.milestones.map((ms) => (
                        <div key={ms.id} className="space-y-2">
                          <div className="flex items-center justify-between gap-2">
                            <span className="text-xs font-bold text-[#8FA89B] leading-tight">第一步：{ms.text}</span>
                            <button 
                              onClick={() => toggleMilestone(item.id, ms.id)}
                              className={`px-3 py-1 rounded-full text-[10px] font-bold border transition shrink-0 min-h-[30px] flex items-center ${ms.completed ? 'bg-[#EBF2EE] border-[#CDDFD6] text-[#3F5448]' : 'bg-white border-[#EFECE6] text-[#8C8379]'}`}
                            >
                              {ms.completed ? '已解鎖 🎉' : '未解鎖'}
                            </button>
                          </div>

                          <div className="grid grid-cols-1 md:grid-cols-2 gap-3">
                            <div className="bg-[#FAF7F2] p-2.5 rounded-xl border border-[#EFECE6] text-xs">
                              <span className="text-[9px] text-[#A29485] font-bold block mb-1">每週刻意行動</span>
                              <p className="font-medium text-[#5C554E] leading-relaxed">{ms.weeklyAction}</p>
                            </div>
                            <div className="bg-[#FAFBF9] p-2.5 rounded-xl border border-[#8FA89B]/10 text-xs">
                              <span className="text-[9px] text-[#8FA89B] font-bold block mb-1">每日重複任務 (融合至習慣)</span>
                              <p className="font-semibold text-[#5C554E] leading-relaxed">{ms.dailyTask}</p>
                            </div>
                          </div>
                        </div>
                      ))}
                    </div>

                  </div>
                ))}
              </div>

            </div>
          )}

          {/* ==========================================
              分頁 4：自我滿意度儀表板 (Dashboard)
              ========================================== */}
          {activeTab === 'dashboard' && (
            <div className="space-y-6">
              
              <div className="grid grid-cols-1 md:grid-cols-3 gap-5">
                
                {/* 滿意度 Score */}
                <div className="bg-white p-5 rounded-3xl border border-[#EFECE6] text-center shadow-sm flex flex-col items-center justify-center">
                  <span className="text-[10px] font-bold text-[#A29485] block uppercase tracking-wider mb-2">今日自覺人生滿意度</span>
                  <div className="relative inline-flex items-center justify-center">
                    <svg className="w-28 h-24 transform -rotate-90">
                      <circle cx="56" cy="48" r="44" stroke="#FAF7F2" strokeWidth="5" fill="transparent" />
                      <circle cx="56" cy="48" r="44" stroke="#D49B7A" strokeWidth="5" fill="transparent" 
                        strokeDasharray={276.4}
                        strokeDashoffset={276.4 - (276.4 * dayScores.totalScore) / 100}
                        strokeLinecap="round"
                        className="transition-all duration-1000"
                      />
                    </svg>
                    <span className="absolute text-xl font-bold text-[#4F4941] font-mono">{dayScores.totalScore}%</span>
                  </div>
                  <p className="text-[10px] text-[#8C8379] mt-3">四大核心類別打卡均分。</p>
                </div>

                {/* 神經可塑性 Score */}
                <div className="bg-white p-5 rounded-3xl border border-[#EFECE6] text-center shadow-sm flex flex-col items-center justify-center">
                  <span className="text-[10px] font-bold text-[#A29485] block uppercase tracking-wider mb-2">神經可塑性重塑指數</span>
                  <div className="relative inline-flex items-center justify-center">
                    <svg className="w-28 h-24 transform -rotate-90">
                      <circle cx="56" cy="48" r="44" stroke="#FAF7F2" strokeWidth="5" fill="transparent" />
                      <circle cx="56" cy="48" r="44" stroke="#8FA89B" strokeWidth="5" fill="transparent" 
                        strokeDasharray={276.4}
                        strokeDashoffset={276.4 - (276.4 * dayScores.neuroScore) / 100}
                        strokeLinecap="round"
                        className="transition-all duration-1000"
                      />
                    </svg>
                    <span className="absolute text-xl font-bold text-[#4F4941] font-mono">{dayScores.neuroScore}</span>
                  </div>
                  <p className="text-[10px] text-[#8C8379] mt-3">根據重複、睡眠、BDNF 加權計算。</p>
                </div>

                {/* 重塑進度摘要 */}
                <div className="bg-[#FAF7F2] p-5 rounded-3xl border border-[#EFECE6] flex flex-col justify-between">
                  <div className="space-y-3 text-xs w-full">
                    <span className="text-[10px] font-bold text-[#A29485] block mb-1">📈 當前神經通路足跡</span>
                    
                    <div className="flex items-center justify-between">
                      <span className="text-[#8C8379]">認知注意力控制：</span>
                      <span className="font-mono font-bold text-[#8FA89B]">{dayScores.cogScore}%</span>
                    </div>
                    <div className="flex items-center justify-between">
                      <span className="text-[#8C8379]">BDNF 肥料蓄積：</span>
                      <span className="font-mono font-bold text-[#D49B7A]">{dayScores.exeScore}%</span>
                    </div>
                    <div className="flex items-center justify-between">
                      <span className="text-[#8C8379]">記憶固化與清除：</span>
                      <span className="font-mono font-bold text-[#8CA3B5]">{dayScores.sleepScore}%</span>
                    </div>
                    <div className="flex items-center justify-between">
                      <span className="text-[#8C8379]">新通路刻意打通：</span>
                      <span className="font-mono font-bold text-[#A491A8]">{dayScores.learnScore}%</span>
                    </div>
                  </div>
                </div>

              </div>

              {/* 書本精華：神經可塑性三大核心步驟教學 */}
              <div className="bg-white p-5 rounded-3xl border border-[#EFECE6] shadow-sm">
                <h3 className="font-bold text-[#4F4941] text-xs md:text-sm mb-4">💡 神經可塑性改變公式：重複 + 注意力 + 刻意 = 持久的改變</h3>
                <div className="grid grid-cols-1 md:grid-cols-3 gap-4">
                  <div className="p-3.5 bg-[#FAF7F2] rounded-2xl space-y-1.5">
                    <span className="text-[9px] font-bold text-white bg-[#8FA89B] px-2 py-0.5 rounded-full">第一步：注意力</span>
                    <h4 className="font-bold text-xs text-[#4F4941] pt-1">尋找大腦觸發點</h4>
                    <p className="text-[10px] text-[#717A6E] leading-relaxed">觀察並發現負面行為的特定環境、人際或時間觸發提示，避開自動化心理捷徑。</p>
                  </div>
                  <div className="p-3.5 bg-[#FAF7F2] rounded-2xl space-y-1.5">
                    <span className="text-[9px] font-bold text-white bg-[#D49B7A] px-2 py-0.5 rounded-full">第二步：刻意</span>
                    <h4 className="font-bold text-xs text-[#4F4941] pt-1">在衝動前刻意煞車</h4>
                    <p className="text-[10px] text-[#717A6E] leading-relaxed">利用 2 次吸氣 1 次長呼氣（生理性嘆息）迅速冷靜，創造暫停的契機，做出新選擇。</p>
                  </div>
                  <div className="p-3.5 bg-[#FAF7F2] rounded-2xl space-y-1.5">
                    <span className="text-[9px] font-bold text-white bg-[#4A729E] px-2 py-0.5 rounded-full">第三步：重複</span>
                    <h4 className="font-bold text-xs text-[#4F4941] pt-1">直到它成為自動化</h4>
                    <p className="text-[10px] text-[#717A6E] leading-relaxed">一個好習慣平均需時 66 天建立。不求完美連續但求不斷持續，大腦的累積從不白費。</p>
                  </div>
                </div>
              </div>

            </div>
          )}

          {/* ==========================================
              分頁 5：四大類別設定 (Categories Management)
              ========================================== */}
          {activeTab === 'categories' && (
            <div className="space-y-6">
              
              <div className="bg-[#FAF7F2] p-4 md:p-5 rounded-2xl border border-[#E9E1D5]">
                <h3 className="font-bold text-xs md:text-sm text-[#4F4941]">⚙️ 常駐核心類別設定</h3>
                <p className="text-[10px] md:text-xs text-[#8C8379] mt-1">自訂核心四大習慣的常駐設定。新增後的習慣將會自動在每日修煉中展現。</p>
              </div>

              {/* 1. 認知 */}
              <div className="bg-white p-4 md:p-5 rounded-3xl border border-[#EFECE6] space-y-3">
                <span className="text-xs font-bold text-[#4F4941] block">🧠 認知與轉念常駐項目設定：</span>
                <div className="flex gap-2">
                  <input 
                    type="text" 
                    placeholder="新增，如: CBT 讀書會實操 / 正念 10 分鐘" 
                    value={newItemName}
                    onChange={(e) => setNewItemName(e.target.value)}
                    className="flex-1 text-xs p-3 bg-[#FAF7F2] border border-[#EFECE6] rounded-xl focus:outline-none min-h-[44px]"
                  />
                  <button onClick={() => addConfigItem('cognitive')} className="px-4 bg-[#8FA89B] text-white text-xs font-bold rounded-xl active:scale-95 transition">新增</button>
                </div>
                <div className="space-y-2">
                  {settings.cognitive.map(item => (
                    <div key={item.id} className="flex items-center justify-between p-2.5 bg-[#FAF7F2] rounded-xl text-xs">
                      <span className="truncate max-w-[200px]">{item.name}</span>
                      <button onClick={() => deleteConfigItem('cognitive', item.id)} className="text-[#D9D1C5] hover:text-[#CE4257] p-1.5"><Trash2 className="w-4 h-4" /></button>
                    </div>
                  ))}
                </div>
              </div>

              {/* 2. 運動 */}
              <div className="bg-white p-4 md:p-5 rounded-3xl border border-[#EFECE6] space-y-3">
                <span className="text-xs font-bold text-[#4F4941] block">🏃 運動項目自訂：</span>
                <div className="grid grid-cols-1 md:grid-cols-3 gap-2">
                  <input 
                    type="text" 
                    placeholder="例如: 游泳 / 重訓慢跑" 
                    value={newItemName}
                    onChange={(e) => setNewItemName(e.target.value)}
                    className="text-xs p-3 bg-[#FAF7F2] border border-[#EFECE6] rounded-xl focus:outline-none min-h-[44px]"
                  />
                  <input 
                    type="number" 
                    placeholder="目標分鐘數" 
                    value={newTargetMin}
                    onChange={(e) => setNewTargetMin(Number(e.target.value))}
                    className="text-xs p-3 bg-[#FAF7F2] border border-[#EFECE6] rounded-xl focus:outline-none min-h-[44px]"
                  />
                  <button onClick={() => addConfigItem('exercise')} className="bg-[#8FA89B] text-white text-xs font-bold rounded-xl py-3 active:scale-95 transition">新增項目</button>
                </div>
                <div className="space-y-2">
                  {settings.exercise.map(item => (
                    <div key={item.id} className="flex items-center justify-between p-2.5 bg-[#FAF7F2] rounded-xl text-xs">
                      <span className="truncate max-w-[200px]">{item.name} (每日目標: {item.targetMin}分)</span>
                      <button onClick={() => deleteConfigItem('exercise', item.id)} className="text-[#D9D1C5] hover:text-[#CE4257] p-1.5"><Trash2 className="w-4 h-4" /></button>
                    </div>
                  ))}
                </div>
              </div>

              {/* 3. 睡眠 */}
              <div className="bg-white p-4 md:p-5 rounded-3xl border border-[#EFECE6] space-y-3">
                <span className="text-xs font-bold text-[#4F4941] block">😴 睡眠時間與儀式設定：</span>
                <div className="grid grid-cols-1 md:grid-cols-2 gap-3">
                  <div>
                    <label className="text-[10px] text-[#A29485] font-bold block mb-1">目標入睡時間</label>
                    <input 
                      type="time" 
                      value={settings.sleep.bedtime}
                      onChange={(e) => setSettings(p => ({ ...p, sleep: { ...p.sleep, bedtime: e.target.value } }))}
                      className="w-full text-xs p-3 bg-[#FAF7F2] border border-[#EFECE6] rounded-xl focus:outline-none min-h-[44px]"
                    />
                  </div>
                  <div>
                    <label className="text-[10px] text-[#A29485] font-bold block mb-1">目標起床時間</label>
                    <input 
                      type="time" 
                      value={settings.sleep.wakeUpTime}
                      onChange={(e) => setSettings(p => ({ ...p, sleep: { ...p.sleep, wakeUpTime: e.target.value } }))}
                      className="w-full text-xs p-3 bg-[#FAF7F2] border border-[#EFECE6] rounded-xl focus:outline-none min-h-[44px]"
                    />
                  </div>
                </div>

                <div className="pt-2 border-t border-[#FAF7F2]">
                  <span className="text-xs font-bold text-[#8C8379] block mb-2">自訂常駐睡前放鬆儀式：</span>
                  <div className="flex gap-2 mb-3">
                    <input 
                      type="text" 
                      placeholder="例如: 生理性嘆息 5 次 / 遠離手機" 
                      value={newItemName}
                      onChange={(e) => setNewItemName(e.target.value)}
                      className="flex-1 text-xs p-3 bg-[#FAF7F2] border border-[#EFECE6] rounded-xl focus:outline-none min-h-[44px]"
                    />
                    <button onClick={() => addConfigItem('sleep')} className="px-4 bg-[#8FA89B] text-white text-xs font-bold rounded-xl active:scale-95 transition">新增</button>
                  </div>
                  <div className="space-y-2">
                    {settings.sleep.rituals.map(item => (
                      <div key={item.id} className="flex items-center justify-between p-2.5 bg-[#FAF7F2] rounded-xl text-xs">
                        <span className="truncate max-w-[200px]">{item.name}</span>
                        <button onClick={() => deleteConfigItem('sleep', item.id)} className="text-[#D9D1C5] hover:text-[#CE4257] p-1.5"><Trash2 className="w-4 h-4" /></button>
                      </div>
                    ))}
                  </div>
                </div>
              </div>

              {/* 4. 學習 */}
              <div className="bg-white p-4 md:p-5 rounded-3xl border border-[#EFECE6] space-y-3">
                <span className="text-xs font-bold text-[#4F4941] block">📚 刻意學習設定：</span>
                <div className="grid grid-cols-1 md:grid-cols-2 gap-2">
                  <input 
                    type="text" 
                    placeholder="例如: 英文影子口說 / 鋼琴" 
                    value={newItemName}
                    onChange={(e) => setNewItemName(e.target.value)}
                    className="text-xs p-3 bg-[#FAF7F2] border border-[#EFECE6] rounded-xl focus:outline-none min-h-[44px]"
                  />
                  <input 
                    type="number" 
                    placeholder="目標學習分鐘" 
                    value={newTargetMin}
                    onChange={(e) => setNewTargetMin(Number(e.target.value))}
                    className="text-xs p-3 bg-[#FAF7F2] border border-[#EFECE6] rounded-xl focus:outline-none min-h-[44px]"
                  />
                </div>
                <button onClick={() => addConfigItem('learning')} className="w-full py-3 bg-[#8FA89B] text-white text-xs font-bold rounded-xl active:scale-95 transition min-h-[44px]">確認新增常駐學習項目</button>
                <div className="space-y-2">
                  {settings.learning.map(item => (
                    <div key={item.id} className="flex items-center justify-between p-2.5 bg-[#FAF7F2] rounded-xl text-xs">
                      <span className="truncate max-w-[200px]">{item.name} (每日目標: {item.targetMin}分)</span>
                      <button onClick={() => deleteConfigItem('learning', item.id)} className="text-[#D9D1C5] hover:text-[#CE4257] p-1.5"><Trash2 className="w-4 h-4" /></button>
                    </div>
                  ))}
                </div>
              </div>

            </div>
          )}

          {/* ==========================================
              分頁 6：AI 神經導師 (AI Neuro-Coach)
              ========================================== */}
          {activeTab === 'coach' && (
            <div className="space-y-5">
              
              <div className="bg-[#FAF7F2] p-4 md:p-6 rounded-3xl border border-[#8FA89B]/30 relative overflow-hidden">
                <div className="absolute right-0 top-0 w-24 h-24 bg-[#8FA89B]/5 rounded-bl-full pointer-events-none"></div>
                <div className="flex items-center gap-3 mb-2">
                  <div className="bg-[#8FA89B] text-white p-2 rounded-xl shrink-0">
                    <Sparkles className="w-5 h-5 text-white" />
                  </div>
                  <div>
                    <h3 className="font-bold text-xs md:text-base text-[#4F4941]">《Rewire》AI 神經科學導師</h3>
                    <p className="text-[10px] md:text-xs text-[#8C8379] leading-relaxed">大腦科學實踐回饋。針對您在「注意力、重複度、睡眠儀式、BDNF 運動」中的狀況，給予不自責、溫柔的精細改善建議。</p>
                  </div>
                </div>
              </div>

              {/* API 密鑰 */}
              <div className="bg-white p-4 md:p-6 rounded-3xl border border-[#EFECE6] space-y-4 shadow-sm">
                <span className="text-xs font-bold text-[#4F4941] flex items-center gap-1.5 uppercase">
                  <Info className="w-4 h-4 text-[#D49B7A]" />
                  <span>AI 連線配置 (Gemini 2.5 Flash)</span>
                </span>
                <p className="text-[10px] md:text-[11px] text-[#8C8379] leading-relaxed">
                  請填入您的 Google AI Studio Gemini API Key。您的金鑰會安全保存在本地瀏覽器記憶體。
                </p>
                <div className="flex flex-col gap-2">
                  <input 
                    type="password" 
                    placeholder="請輸入 API 金鑰 (API_KEY)..." 
                    value={aiApiKey}
                    onChange={(e) => setAiApiKey(e.target.value)}
                    className="w-full text-xs p-3 bg-[#FAF7F2] border border-[#EFECE6] rounded-xl focus:outline-none min-h-[44px]"
                  />
                  <button 
                    onClick={askAICoach}
                    disabled={aiLoading}
                    className="w-full py-3 bg-[#8FA89B] hover:bg-[#7FA090] text-white text-xs font-bold rounded-xl transition active:scale-95 flex items-center justify-center gap-1.5 disabled:opacity-50 min-h-[44px]"
                  >
                    {aiLoading ? <RefreshCw className="w-4 h-4 animate-spin" /> : <Sparkles className="w-4 h-4" />}
                    <span>{aiLoading ? '正在分析突觸路徑...' : '獲取神經科學處方'}</span>
                  </button>
                </div>
                {aiError && (
                  <div className="p-3 bg-[#FBF1F3] border border-[#F2D7DB] text-[#7E424C] text-xs rounded-xl flex items-center gap-2">
                    <AlertCircle className="w-4 h-4 shrink-0" />
                    <span className="truncate">{aiError}</span>
                  </div>
                )}
              </div>

              {/* 回饋結果 */}
              <div className="bg-white p-4 md:p-6 rounded-3xl border border-[#EFECE6] shadow-sm min-h-[250px]">
                {aiLoading ? (
                  <div className="flex flex-col items-center justify-center py-16 gap-4 text-center">
                    <div className="w-10 h-10 border-4 border-[#FAF7F2] border-t-[#8FA89B] rounded-full animate-spin"></div>
                    <p className="text-xs text-[#8C8379] max-w-sm px-4">
                      AI 導師正在為您制定專屬、不自責的暖心神經回饋建議...
                    </p>
                  </div>
                ) : aiResponse ? (
                  <div className="text-[11px] md:text-sm text-[#4F4941] leading-relaxed whitespace-pre-wrap bg-[#FAF7F2] p-5 rounded-2xl border border-[#E9E1D5] font-serif">
                    {aiResponse}
                  </div>
                ) : (
                  <div className="text-center py-12">
                    <div className="w-12 h-12 bg-[#FAF7F2] rounded-full flex items-center justify-center mx-auto mb-3">
                      <Brain className="w-5 h-5 text-[#D9D1C5]" />
                    </div>
                    <p className="text-[10px] md:text-xs text-[#8C8379] max-w-xs mx-auto leading-relaxed">
                      填寫 API 密鑰後點擊按鈕，AI 導師將深入診斷您在「注意力、刻意、重複」三要素上的放電狀況。
                    </p>
                  </div>
                )}
              </div>

            </div>
          )}

        </main>

      </div>

      {/* 底部 Footer - 桌機版顯示，手機版因底部導航列而隱藏以免重疊 */}
      <footer className="hidden md:block bg-[#FAF7F2] border-t border-[#EAE3D5] py-4 px-6 text-center text-[10px] text-[#A29485] space-y-1 mt-auto">
        <p>© 2026 YOLO Life OS • 融合《神經可塑性 (Rewire)》理論與自覺人生滿意度打卡系統</p>
        <p className="italic">「一起放電的神經元會彼此連結；無法同步放電的神經元將失去連結。不要苛求完美，今天就重新出發。」</p>
      </footer>

    </div>
  );
}

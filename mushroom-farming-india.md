import { Link } from 'react-router';
import { useAppStore } from '../store';
import { getLessonUnlockTime, getUnlockText } from '../data';
import { CheckCircle, Play, Lock, Trophy, Leaf, Sparkles, Bell, ArrowRight } from 'lucide-react';
import clsx from 'clsx';
import { motion } from 'motion/react';

const FloatingMushroom = () => (
  <motion.div
    animate={{ y: [0, -10, 0], rotate: [0, 5, -5, 0] }}
    transition={{ repeat: Infinity, duration: 4, ease: "easeInOut" }}
    className="w-16 h-16 drop-shadow-2xl relative"
  >
    <svg viewBox="0 0 100 100" fill="none" xmlns="http://www.w3.org/2000/svg" className="w-full h-full">
      {/* 3D Mushroom Stem */}
      <path d="M40 50C40 30 60 30 60 50V80C60 85 55 90 50 90C45 90 40 85 40 80V50Z" fill="url(#stemGrad)"/>
      {/* 3D Mushroom Cap */}
      <path d="M10 50C10 20 30 10 50 10C70 10 90 20 90 50C90 55 85 60 50 60C15 60 10 55 10 50Z" fill="url(#capGrad)"/>
      {/* Cap Spots */}
      <circle cx="35" cy="30" r="4" fill="#FFEBB5" opacity="0.8"/>
      <circle cx="65" cy="35" r="5" fill="#FFEBB5" opacity="0.8"/>
      <circle cx="50" cy="20" r="3" fill="#FFEBB5" opacity="0.8"/>
      
      <defs>
        <linearGradient id="stemGrad" x1="40" y1="50" x2="60" y2="90" gradientUnits="userSpaceOnUse">
          <stop stopColor="#E6D5B8" />
          <stop offset="1" stopColor="#B89F7C" />
        </linearGradient>
        <radialGradient id="capGrad" cx="30" cy="20" r="70" gradientUnits="userSpaceOnUse">
          <stop stopColor="#FF9F43" />
          <stop offset="1" stopColor="#D9534F" />
        </radialGradient>
      </defs>
    </svg>
    <div className="absolute -bottom-2 left-1/2 -translate-x-1/2 w-10 h-2 bg-black/30 blur-sm rounded-full" />
  </motion.div>
);

export const Dashboard = () => {
  const { user, progress, lessons } = useAppStore();

  const activeLessons = lessons.filter(l => l.status === 'active');
  const totalLessons = activeLessons.length;
  const completedCount = progress.length;
  const progressPercent = Math.round((completedCount / totalLessons) * 100) || 0;

  const now = new Date();

  return (
    <div className="p-6 md:p-10 max-w-4xl mx-auto space-y-8 min-h-screen">
      
      {/* Top Header */}
      <motion.div 
        initial={{ opacity: 0, y: -20 }}
        animate={{ opacity: 1, y: 0 }}
        className="flex justify-between items-center"
      >
        <div className="flex items-center gap-4">
          <div className="w-12 h-12 rounded-full bg-gradient-to-tr from-emerald-400 to-teal-500 p-[2px] shadow-[0_0_15px_rgba(16,185,129,0.3)]">
            <div className="w-full h-full bg-gray-900 rounded-full flex items-center justify-center border border-white/10 overflow-hidden">
              <span className="text-xl font-bold text-white">
                {user?.name?.charAt(0) || 'U'}
              </span>
            </div>
          </div>
          <div>
            <p className="text-sm text-gray-400 font-medium">Welcome back,</p>
            <h1 className="text-xl font-bold text-white">{user?.name || 'Farmer'}</h1>
          </div>
        </div>
        <button className="relative p-3 bg-white/5 backdrop-blur-md rounded-2xl border border-white/10 shadow-lg hover:bg-white/10 transition-colors">
          <Bell className="w-6 h-6 text-gray-300" />
          <span className="absolute top-2.5 right-3 w-2 h-2 bg-amber-500 rounded-full animate-pulse" />
        </button>
      </motion.div>

      {/* 3D Hero Progress Card */}
      <motion.div 
        initial={{ opacity: 0, scale: 0.95 }}
        animate={{ opacity: 1, scale: 1 }}
        transition={{ delay: 0.1, type: "spring" }}
        className="relative bg-gradient-to-br from-emerald-900/80 to-[#0f1d18] p-8 rounded-[2rem] border border-white/10 shadow-[0_20px_40px_-10px_rgba(0,0,0,0.5),inset_0_1px_1px_rgba(255,255,255,0.2)] overflow-hidden group"
      >
        <div className="absolute right-[-10%] top-[-20%] w-[60%] h-[150%] bg-gradient-to-bl from-emerald-500/20 to-transparent blur-3xl pointer-events-none transform rotate-12" />
        
        <div className="relative z-10 flex items-center justify-between gap-6">
          <div className="space-y-4">
            <div className="inline-flex items-center gap-2 bg-emerald-500/20 text-emerald-300 px-3 py-1 rounded-full text-xs font-bold uppercase tracking-widest border border-emerald-500/30">
              <Leaf className="w-3 h-3" /> Training Progress
            </div>
            <h2 className="text-3xl font-bold text-white">Master<br/>Mushroom Farming</h2>
            
            <Link to={`/lesson/${activeLessons.find(l => !progress.includes(l.id))?.id || activeLessons[0]?.id}`} 
              className="mt-4 inline-flex items-center gap-2 bg-emerald-500 text-white px-6 py-3 rounded-2xl font-bold shadow-[0_0_20px_rgba(16,185,129,0.4)] hover:shadow-[0_0_30px_rgba(16,185,129,0.6)] hover:bg-emerald-400 transition-all group-hover:scale-105"
            >
              <Play className="w-4 h-4 fill-white" /> Continue Learning
            </Link>
          </div>

          <div className="relative flex-shrink-0">
            {/* 3D Circular Progress */}
            <div className="w-32 h-32 relative flex items-center justify-center">
              <svg className="w-full h-full transform -rotate-90 drop-shadow-xl" viewBox="0 0 100 100">
                <circle cx="50" cy="50" r="40" className="stroke-white/10" strokeWidth="8" fill="none" />
                <circle 
                  cx="50" cy="50" r="40" 
                  className="stroke-emerald-400" 
                  strokeWidth="8" fill="none" strokeLinecap="round"
                  strokeDasharray="251.2" 
                  strokeDashoffset={251.2 - (251.2 * progressPercent) / 100}
                  style={{ transition: 'stroke-dashoffset 1s ease-out' }}
                />
              </svg>
              <div className="absolute inset-0 flex flex-col items-center justify-center">
                <span className="text-2xl font-bold text-white drop-shadow-md">{progressPercent}%</span>
              </div>
            </div>
          </div>
        </div>
      </motion.div>

      {/* Smart AI Notification */}
      <motion.div
        initial={{ opacity: 0, x: -20 }}
        animate={{ opacity: 1, x: 0 }}
        transition={{ delay: 0.2 }}
        className="bg-[#1a231f] border border-emerald-500/20 rounded-2xl p-4 flex gap-4 shadow-lg backdrop-blur-md relative overflow-hidden"
      >
        <div className="absolute left-0 top-0 bottom-0 w-1 bg-gradient-to-b from-amber-400 to-amber-600" />
        <div className="w-10 h-10 rounded-full bg-amber-500/20 flex items-center justify-center flex-shrink-0">
          <Sparkles className="w-5 h-5 text-amber-400" />
        </div>
        <div>
          <h3 className="text-sm font-bold text-emerald-100 flex items-center gap-2">
            AI Smart Tip
            <span className="bg-amber-500/20 text-amber-400 text-[10px] px-2 py-0.5 rounded-full border border-amber-500/30 uppercase">New</span>
          </h3>
          <p className="text-sm text-gray-400 mt-1">Oyster mushrooms are perfect for beginners! Keep temperature between 20°C – 30°C for optimal growth.</p>
        </div>
      </motion.div>

      {/* Modules */}
      <div className="space-y-4">
        <div className="flex justify-between items-end mb-6">
          <h2 className="text-xl font-bold text-white">Training Modules</h2>
          <span className="text-sm text-gray-400">{completedCount} of {totalLessons} completed</span>
        </div>

        <div className="grid gap-4">
          {lessons.map((lesson, index) => {
            const isCompleted = progress.includes(lesson.id);
            const unlockTime = getLessonUnlockTime(user?.registeredAt || Date.now(), lesson.dayOffset);
            const isUnlocked = now >= unlockTime;
            const isLocked = lesson.status === 'coming-soon' || !isUnlocked;
            const unlockText = getUnlockText(unlockTime);

            return (
              <motion.div 
                key={lesson.id}
                whileHover={!isLocked ? { scale: 1.02, y: -2 } : {}}
                className={clsx(
                  "relative bg-white/5 backdrop-blur-lg rounded-2xl p-5 border transition-all duration-300",
                  isCompleted ? "border-emerald-500/30 shadow-[0_0_15px_rgba(16,185,129,0.1)]" : "border-white/10",
                  isLocked ? "opacity-60 cursor-not-allowed" : "hover:bg-white/10 hover:border-white/20 hover:shadow-xl shadow-black/50 cursor-pointer"
                )}
              >
                {!isLocked && (
                  <Link to={`/lesson/${lesson.id}`} className="absolute inset-0 z-10" />
                )}
                
                <div className="flex items-center gap-5">
                  {/* Module Icon */}
                  <div className={clsx(
                    "w-14 h-14 rounded-[1rem] flex items-center justify-center flex-shrink-0 shadow-inner relative overflow-hidden",
                    isCompleted ? "bg-emerald-500/20 text-emerald-400 border border-emerald-500/30" :
                    isLocked ? "bg-black/40 text-gray-500 border border-white/5" :
                    "bg-gradient-to-br from-gray-800 to-gray-900 text-white border border-gray-700 shadow-xl"
                  )}>
                    {isCompleted ? <CheckCircle className="w-6 h-6" /> : 
                     isLocked ? <Lock className="w-6 h-6" /> : 
                     <Play className="w-6 h-6 fill-current ml-1" />}
                     
                     {/* Gloss effect */}
                     <div className="absolute inset-0 bg-gradient-to-b from-white/10 to-transparent opacity-50 pointer-events-none" />
                  </div>

                  <div className="flex-1">
                    <h3 className="text-xs font-bold text-emerald-500 uppercase tracking-widest mb-1">Module {index + 1}</h3>
                    <h4 className="text-base font-bold text-gray-100 leading-tight">{lesson.title}</h4>
                    
                    {lesson.status === 'coming-soon' ? (
                      <p className="text-xs text-gray-500 mt-2 flex items-center gap-1 font-medium">
                        <Lock className="w-3 h-3" /> Coming Soon
                      </p>
                    ) : (
                      <p className="text-xs mt-2 flex items-center gap-1.5 font-medium">
                        {!isUnlocked ? (
                          <span className="text-amber-500 flex items-center gap-1 bg-amber-500/10 px-2 py-0.5 rounded-md border border-amber-500/20">
                            <Lock className="w-3 h-3"/> {unlockText}
                          </span>
                        ) : isCompleted ? (
                          <span className="text-emerald-400 flex items-center gap-1">
                            Completed
                          </span>
                        ) : (
                          <span className="text-gray-400 flex items-center gap-1">
                            Ready to start
                          </span>
                        )}
                      </p>
                    )}
                  </div>
                  
                  {!isLocked && (
                    <div className="w-8 h-8 rounded-full bg-white/5 flex items-center justify-center text-gray-400 group-hover:bg-white/10 transition-colors">
                      <ArrowRight className="w-4 h-4" />
                    </div>
                  )}
                </div>
              </motion.div>
            );
          })}
        </div>
      </div>
    </div>
  );
};

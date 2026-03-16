import { useParams, useNavigate } from 'react-router';
import { useAppStore } from '../store';
import { getLessonUnlockTime } from '../data';
import { ChevronLeft, FileText, CheckCircle, VideoOff, Info, Lock } from 'lucide-react';
import { motion } from 'motion/react';
import clsx from 'clsx';

export const LessonPage = () => {
  const { id } = useParams();
  const navigate = useNavigate();
  const { user, progress, lessons, markCompleted } = useAppStore();

  const lesson = lessons.find(l => l.id === id);

  if (!lesson || !user) {
    return <div className="p-10 text-center text-gray-500">Lesson not found</div>;
  }

  const unlockTime = getLessonUnlockTime(user.registeredAt, lesson.dayOffset);
  const isUnlocked = new Date() >= unlockTime;
  const isLocked = lesson.status === 'coming-soon' || !isUnlocked;

  if (isLocked) {
    return (
      <div className="p-10 text-center text-gray-500 flex flex-col items-center justify-center h-full space-y-4">
        <Lock className="w-12 h-12 text-gray-400" />
        <h2 className="text-xl font-bold text-gray-300">This Lesson is Locked</h2>
        <p>Please wait for the scheduled unlock time to access this module.</p>
        <button 
          onClick={() => navigate('/dashboard')}
          className="mt-4 px-6 py-2 bg-emerald-500 text-white font-bold rounded-xl hover:bg-emerald-400"
        >
          Return to Dashboard
        </button>
      </div>
    );
  }

  const isCompleted = progress.includes(lesson.id);

  const handleComplete = () => {
    markCompleted(lesson.id);
    navigate('/dashboard');
  };

  return (
    <motion.div 
      initial={{ opacity: 0, x: 20 }}
      animate={{ opacity: 1, x: 0 }}
      className="p-6 md:p-10 max-w-4xl mx-auto space-y-6 text-gray-100 min-h-screen"
    >
      <button 
        onClick={() => navigate('/dashboard')}
        className="flex items-center gap-2 text-gray-400 hover:text-white font-medium mb-4 transition-colors"
      >
        <ChevronLeft className="w-5 h-5" />
        Back to Dashboard
      </button>

      <div className="bg-white/5 backdrop-blur-xl rounded-[2rem] overflow-hidden shadow-[0_8px_32px_rgba(0,0,0,0.5)] border border-white/10">
        
        {/* Video Player */}
        <div className="bg-black aspect-video relative group">
          {lesson.videoUrl ? (
            <iframe 
              src={lesson.videoUrl.replace('/view', '/preview')} 
              width="100%" 
              height="100%" 
              allow="autoplay" 
              title={lesson.title}
              className="w-full h-full border-0"
              allowFullScreen
            />
          ) : (
            <div className="absolute inset-0 flex flex-col items-center justify-center text-gray-500 gap-4">
              <VideoOff className="w-12 h-12 text-gray-700" />
              <p>Video currently unavailable</p>
            </div>
          )}
          
          {lesson.liveStart && (
            <div className="absolute bottom-4 left-4 right-4 bg-black/60 backdrop-blur-md text-white p-3 rounded-xl flex items-center gap-3 text-sm border border-white/10 opacity-0 group-hover:opacity-100 transition-opacity pointer-events-none">
              <Info className="w-5 h-5 text-amber-400" />
              Live controls enforced. Pause and skip are disabled until {lesson.liveEnd}.
            </div>
          )}
        </div>

        <div className="p-6 md:p-8 space-y-8">
          <div className="flex justify-between items-start flex-col sm:flex-row gap-6">
            <div>
              <h1 className="text-2xl md:text-3xl font-bold text-white leading-tight">{lesson.title}</h1>
              {lesson.liveStart && (
                <div className="mt-3 inline-flex items-center gap-2 text-amber-400 bg-amber-500/10 px-3 py-1.5 rounded-full text-sm font-bold border border-amber-500/20">
                  <span className="w-2 h-2 rounded-full bg-amber-500 animate-pulse"></span>
                  Live Session: {lesson.liveStart} - {lesson.liveEnd}
                </div>
              )}
            </div>
            
            {!isCompleted ? (
              <button 
                onClick={handleComplete}
                className="px-6 py-3.5 bg-emerald-500 text-white font-bold rounded-xl shadow-[0_0_20px_rgba(16,185,129,0.3)] hover:bg-emerald-400 hover:shadow-[0_0_30px_rgba(16,185,129,0.5)] transition-all flex items-center gap-2 w-full sm:w-auto justify-center flex-shrink-0"
              >
                <CheckCircle className="w-5 h-5" />
                Mark as Completed
              </button>
            ) : (
              <div className="px-6 py-3.5 bg-emerald-500/20 text-emerald-400 border border-emerald-500/30 font-bold rounded-xl flex items-center gap-2 w-full sm:w-auto justify-center flex-shrink-0">
                <CheckCircle className="w-5 h-5" />
                Completed
              </div>
            )}
          </div>

          {/* Resources Section */}
          <div className="pt-8 border-t border-white/10">
            <h2 className="text-xl font-bold mb-4 text-white">Study Materials</h2>
            
            {!isCompleted ? (
              <div className="bg-black/20 p-8 rounded-2xl text-center border border-dashed border-white/10">
                <FileText className="w-12 h-12 text-gray-600 mx-auto mb-3" />
                <h3 className="font-bold text-gray-300">Complete lesson to unlock notes</h3>
                <p className="text-sm text-gray-500 mt-1">PDF Guides will appear here once you mark this lesson as completed.</p>
              </div>
            ) : (
              <div className="grid sm:grid-cols-2 gap-4">
                {lesson.englishGuideUrl && (
                  <a 
                    href={lesson.englishGuideUrl} 
                    target="_blank" 
                    rel="noopener noreferrer"
                    className="flex items-center gap-4 p-4 border border-white/10 bg-white/5 backdrop-blur-md rounded-2xl hover:bg-white/10 transition-colors group"
                  >
                    <div className="w-12 h-12 bg-blue-500/20 rounded-xl flex items-center justify-center text-blue-400 group-hover:bg-blue-500/30 transition-colors">
                      <FileText className="w-6 h-6" />
                    </div>
                    <div>
                      <h4 className="font-bold text-white">English Guide</h4>
                      <p className="text-sm text-gray-400">View PDF Document</p>
                    </div>
                  </a>
                )}
                {lesson.hindiGuideUrl && (
                  <a 
                    href={lesson.hindiGuideUrl} 
                    target="_blank" 
                    rel="noopener noreferrer"
                    className="flex items-center gap-4 p-4 border border-white/10 bg-white/5 backdrop-blur-md rounded-2xl hover:bg-white/10 transition-colors group"
                  >
                    <div className="w-12 h-12 bg-orange-500/20 rounded-xl flex items-center justify-center text-orange-400 group-hover:bg-orange-500/30 transition-colors">
                      <FileText className="w-6 h-6" />
                    </div>
                    <div>
                      <h4 className="font-bold text-white">Hindi Guide</h4>
                      <p className="text-sm text-gray-400">PDF Document (हिंदी)</p>
                    </div>
                  </a>
                )}
              </div>
            )}
          </div>
        </div>
      </div>
    </motion.div>
  );
};

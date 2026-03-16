import { useState } from 'react';
import { useNavigate } from 'react-router';
import { useAppStore } from '../store';
import logoImg from 'figma:asset/6e72c76c76c2a9d5d89e0748178bd706fb534689.png';
import { motion } from 'motion/react';
import { Leaf } from 'lucide-react';

export const Login = () => {
  const [isLogin, setIsLogin] = useState(false);
  const [name, setName] = useState('');
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');
  const [errorMsg, setErrorMsg] = useState('');
  const { login } = useAppStore();
  const navigate = useNavigate();

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    setErrorMsg('');

    // Admin check
    if (isLogin && email === 'organicmushroomfarm' && password === 'Sonib491@') {
      login({
        name: 'Admin',
        email: 'organicmushroomfarm',
        studentId: 'ADMIN',
        registeredAt: Date.now(),
        role: 'admin',
        progress: []
      });
      navigate('/admin');
      return;
    }

    if (email && password) {
      const usersStr = localStorage.getItem('omf_users');
      const users = usersStr ? JSON.parse(usersStr) : [];

      if (isLogin) {
        const user = users.find((u: any) => u.email === email && u.password === password);
        if (user) {
          login({
            name: user.name,
            email: user.email,
            studentId: user.studentId,
            registeredAt: user.registeredAt,
            role: 'user',
            progress: user.progress || []
          });
          navigate('/dashboard');
        } else {
          setErrorMsg('Account not found. Please create a new account first.');
        }
      } else {
        if (users.find((u: any) => u.email === email)) {
          setErrorMsg('Email already exists. Please login instead.');
          return;
        }

        const nextNum = users.length + 1;
        const studentId = `OMF-${nextNum.toString().padStart(4, '0')}`;
        const registeredAt = Date.now();
        
        const newUser = {
          name,
          email,
          password,
          studentId,
          registeredAt,
          role: 'user',
          progress: []
        };

        users.push(newUser);
        localStorage.setItem('omf_users', JSON.stringify(users));

        login({
          name: newUser.name,
          email: newUser.email,
          studentId: newUser.studentId,
          registeredAt: newUser.registeredAt,
          role: 'user',
          progress: []
        });
        navigate('/dashboard');
      }
    }
  };

  return (
    <div className="min-h-screen bg-[#0A100D] flex flex-col justify-center py-12 px-6 sm:px-8 relative overflow-hidden text-gray-100">
      {/* 3D Ambient Background */}
      <div className="absolute top-[-20%] left-[-10%] w-[60%] h-[60%] bg-emerald-600/20 rounded-full blur-[120px] pointer-events-none" />
      <div className="absolute bottom-[-10%] right-[-10%] w-[50%] h-[50%] bg-amber-600/10 rounded-full blur-[100px] pointer-events-none" />

      <motion.div 
        initial={{ opacity: 0, y: 30 }}
        animate={{ opacity: 1, y: 0 }}
        className="sm:mx-auto sm:w-full sm:max-w-md relative z-10"
      >
        <div className="w-20 h-20 mx-auto bg-gradient-to-br from-emerald-400 to-teal-600 rounded-3xl p-1 shadow-[0_0_40px_rgba(16,185,129,0.3)] rotate-3">
          <div className="w-full h-full bg-gray-900 rounded-[1.3rem] flex items-center justify-center -rotate-3 overflow-hidden border border-white/10 relative">
            <div className="absolute inset-0 bg-white/5 backdrop-blur-md" />
            <Leaf className="w-10 h-10 text-emerald-400 drop-shadow-md z-10" />
          </div>
        </div>
        <h2 className="mt-8 text-center text-3xl font-extrabold text-white tracking-tight">
          O.M.F Training
        </h2>
        <p className="mt-2 text-center text-sm text-gray-400 font-medium">
          Professional mushroom farming, elevated.
        </p>
      </motion.div>

      <motion.div 
        initial={{ opacity: 0, y: 20 }}
        animate={{ opacity: 1, y: 0 }}
        transition={{ delay: 0.1 }}
        className="mt-10 sm:mx-auto sm:w-full sm:max-w-md relative z-10"
      >
        <div className="bg-white/5 backdrop-blur-xl py-8 px-6 shadow-[0_8px_32px_rgba(0,0,0,0.5)] sm:rounded-[2rem] sm:px-10 border border-white/10">
          
          <div className="flex gap-2 mb-8 bg-black/40 p-1 rounded-2xl border border-white/5">
            <button 
              type="button"
              onClick={() => { setIsLogin(false); setErrorMsg(''); }}
              className={`flex-1 py-3 text-sm font-bold rounded-xl transition-all duration-300 ${!isLogin ? "bg-white/10 text-white shadow-lg border border-white/10" : "text-gray-400 hover:text-gray-200"}`}
            >
              Sign Up
            </button>
            <button 
              type="button"
              onClick={() => { setIsLogin(true); setErrorMsg(''); }}
              className={`flex-1 py-3 text-sm font-bold rounded-xl transition-all duration-300 ${isLogin ? "bg-white/10 text-white shadow-lg border border-white/10" : "text-gray-400 hover:text-gray-200"}`}
            >
              Login
            </button>
          </div>

          {errorMsg && (
            <div className="mb-6 p-4 bg-red-500/10 border border-red-500/30 text-red-400 rounded-xl text-sm font-medium">
              {errorMsg}
            </div>
          )}

          <form className="space-y-5" onSubmit={handleSubmit}>
            {!isLogin && (
              <div>
                <label className="block text-xs font-bold text-gray-300 uppercase tracking-wider mb-2">Full Name</label>
                <input
                  type="text"
                  required
                  value={name}
                  onChange={(e) => setName(e.target.value)}
                  className="block w-full px-4 py-3.5 bg-black/40 border border-white/10 rounded-xl focus:outline-none focus:ring-2 focus:ring-emerald-500/50 focus:border-emerald-500 text-white placeholder-gray-500 transition-all"
                  placeholder="Enter your name"
                />
              </div>
            )}

            <div>
              <label className="block text-xs font-bold text-gray-300 uppercase tracking-wider mb-2">Email or User ID</label>
              <input
                type="text"
                required
                value={email}
                onChange={(e) => setEmail(e.target.value)}
                className="block w-full px-4 py-3.5 bg-black/40 border border-white/10 rounded-xl focus:outline-none focus:ring-2 focus:ring-emerald-500/50 focus:border-emerald-500 text-white placeholder-gray-500 transition-all"
                placeholder="you@example.com"
              />
            </div>

            <div>
              <label className="block text-xs font-bold text-gray-300 uppercase tracking-wider mb-2">Password</label>
              <input
                type="password"
                required
                value={password}
                onChange={(e) => setPassword(e.target.value)}
                className="block w-full px-4 py-3.5 bg-black/40 border border-white/10 rounded-xl focus:outline-none focus:ring-2 focus:ring-emerald-500/50 focus:border-emerald-500 text-white placeholder-gray-500 transition-all"
                placeholder="••••••••"
              />
            </div>

            <div className="pt-2">
              <button
                type="submit"
                className="w-full flex justify-center py-4 px-4 border border-transparent rounded-xl shadow-[0_0_20px_rgba(16,185,129,0.3)] text-sm font-bold text-white bg-emerald-500 hover:bg-emerald-400 hover:shadow-[0_0_30px_rgba(16,185,129,0.5)] focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-emerald-500 transition-all transform hover:-translate-y-0.5 active:translate-y-0"
              >
                {isLogin ? 'Access Account' : 'Create Account'}
              </button>
            </div>
          </form>
        </div>
      </motion.div>
    </div>
  );
};

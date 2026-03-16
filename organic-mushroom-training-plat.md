import { createBrowserRouter } from 'react-router';
import { Layout } from './components/Layout';
import { ProtectedRoute } from './components/ProtectedRoute';
import { Login } from './pages/Login';
import { Dashboard } from './pages/Dashboard';
import { LessonPage } from './pages/LessonPage';
import { Profile } from './pages/Profile';
import { AiChat } from './pages/AiChat';
import { AdminDashboard } from './pages/AdminDashboard';

export const router = createBrowserRouter([
  {
    path: '/',
    element: <Login />,
  },
  {
    path: '/admin',
    element: <ProtectedRoute><AdminDashboard /></ProtectedRoute>,
  },
  {
    path: '/',
    element: <ProtectedRoute><Layout /></ProtectedRoute>,
    children: [
      { path: 'dashboard', element: <Dashboard /> },
      { path: 'lesson/:id', element: <LessonPage /> },
      { path: 'profile', element: <Profile /> },
      { path: 'ai-chat', element: <AiChat /> },
    ],
  },
]);

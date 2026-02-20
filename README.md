# Clique – Mini Dating App 💖

## 🔗 Live Demo
- **Frontend:** https://clique-frontend-one.vercel.app
- **Backend API:** https://clique-backend-kappa.vercel.app

## 💻 Source Code
- **Main Repository:** https://github.com/minhphuong4203-bot/clique
- **Frontend Repo:** https://github.com/minhphuong4203-bot/clique-frontend
- **Backend Repo:** https://github.com/minhphuong4203-bot/clique-backend

---

## 🏗 Architecture

Client–Server architecture:

- **Frontend:** Next.js 15, TypeScript, Tailwind CSS
- **Backend:** NestJS (Modular Architecture)
- **Database:** PostgreSQL (NeonDB)
- **ORM:** Prisma

Frontend handles UI & API calls.  
Backend manages business logic and data persistence.

---

## 💾 Data Storage

- Users, Profiles, Likes, Matches, Availability stored in PostgreSQL
- Authentication via JWT (Access & Refresh Tokens)
- Tokens stored in HTTP-only cookies

---

## 💘 Match Logic

1. User A likes User B → create Like record.
2. System checks if B already liked A.
3. If mutual → create Match.
4. Matched users can schedule a date.

---

## 📅 Common Time Slot Logic

For each availability update:

If `Overlap_Start < Overlap_End` → first common slot is selected and saved.

---

## 🚀 Future Improvements

- WebSocket real-time updates
- In-app messaging after match
- Swipe-based interaction
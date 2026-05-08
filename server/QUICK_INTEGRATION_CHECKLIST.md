# ⚡ Quick Integration Checklist

## 🎯 Copy-Paste Prompt untuk AI:

```
Saya punya project Hospital Navigator dengan:
- Backend: Express.js di Vercel (https://your-backend.vercel.app)
- Frontend: [React/Vue/Angular - sebutkan framework Anda]

TUGAS:
1. ANALISIS DULU: Cek apakah frontend sudah terintegrasi dengan backend atau belum
2. Buat laporan diagnosis lengkap
3. Tunggu approval saya
4. Baru lakukan integrasi jika diperlukan

Baca file COMPLETE_FRONTEND_INTEGRATION_GUIDE.md untuk detail lengkap.
```

---

## 📋 Quick Diagnosis Questions

AI harus menjawab pertanyaan ini dulu:

### ✅ **Struktur Project**
- [ ] Framework apa yang digunakan?
- [ ] Apakah menggunakan TypeScript?
- [ ] State management apa yang digunakan?
- [ ] HTTP client apa yang digunakan?

### ✅ **API Integration**
- [ ] Apakah sudah ada API calls?
- [ ] Apakah API URL sudah dikonfigurasi?
- [ ] Apakah sudah ada service layer?
- [ ] Apakah sudah ada error handling?

### ✅ **Data Models**
- [ ] Apakah sudah ada type definitions untuk Room?
- [ ] Apakah sudah ada type definitions untuk QrAnchor?
- [ ] Apakah sudah ada type definitions untuk Category?

### ✅ **Features**
- [ ] Apakah room list sudah fetch dari API?
- [ ] Apakah category filter berfungsi?
- [ ] Apakah QR scanner berfungsi?
- [ ] Apakah navigation berfungsi?

---

## 🔌 Backend Endpoints Summary

### **Rooms**
```
GET    /api/v1/rooms                    # Get all rooms
GET    /api/v1/rooms?category=Emergency # Filter by category
GET    /api/v1/rooms?floor=1            # Filter by floor
GET    /api/v1/rooms?search=igd         # Search rooms
GET    /api/v1/rooms/:id                # Get room by ID
GET    /api/v1/rooms/stats              # Get statistics
POST   /api/v1/rooms                    # Create room
PUT    /api/v1/rooms/:id                # Update room
DELETE /api/v1/rooms/:id                # Delete room
```

### **QR Anchors**
```
GET    /api/v1/qr-anchors               # Get all QR anchors
GET    /api/v1/qr-anchors?roomId=IGD    # Filter by room
GET    /api/v1/qr-anchors?floor=1       # Filter by floor
GET    /api/v1/qr-anchors/:qrId         # Get by ID
POST   /api/v1/qr-anchors/resolve       # Resolve QR code
GET    /api/v1/qr-anchors/stats         # Get statistics
POST   /api/v1/qr-anchors               # Create QR anchor
PUT    /api/v1/qr-anchors/:qrId         # Update QR anchor
DELETE /api/v1/qr-anchors/:qrId         # Delete QR anchor
```

### **Categories** 🆕
```
GET    /api/v1/categories               # Get all categories
GET    /api/v1/categories/names         # Get category names
GET    /api/v1/categories/:name         # Get by name
GET    /api/v1/categories/stats         # Get statistics
POST   /api/v1/categories/validate      # Validate category
```

---

## 📦 Required Files (Minimum)

### **Types**
```
src/types/room.ts
src/types/qrAnchor.ts
src/types/category.ts
src/types/api.ts
```

### **Services**
```
src/services/api.ts
src/services/roomService.ts
src/services/qrAnchorService.ts
src/services/categoryService.ts
```

### **Hooks** (Optional but recommended)
```
src/hooks/useRooms.ts
src/hooks/useQrAnchors.ts
src/hooks/useCategories.ts
```

### **Environment**
```
.env.development
.env.production
```

---

## 🚀 Quick Test Commands

```bash
# Test backend health
curl https://your-backend.vercel.app/api/v1/health

# Test rooms endpoint
curl https://your-backend.vercel.app/api/v1/rooms

# Test categories endpoint
curl https://your-backend.vercel.app/api/v1/categories

# Test QR anchors endpoint
curl https://your-backend.vercel.app/api/v1/qr-anchors
```

---

## ⚠️ Common Issues

| Issue | Solution |
|-------|----------|
| CORS Error | Check ALLOWED_ORIGINS in backend env vars |
| 404 Not Found | Verify API_URL in frontend .env |
| Network Error | Check if backend is deployed and running |
| Invalid Category | Use categories from API, not hardcoded |

---

## ✅ Success Criteria

- [ ] All API endpoints accessible
- [ ] Rooms displayed correctly
- [ ] Categories loaded from API
- [ ] Filters working (category, floor, search)
- [ ] QR scanning working
- [ ] Loading states shown
- [ ] Errors handled gracefully
- [ ] No console errors
- [ ] Responsive design
- [ ] CORS configured

---

## 📚 Full Documentation

Untuk detail lengkap, baca:
- `COMPLETE_FRONTEND_INTEGRATION_GUIDE.md` - Full integration guide
- `CATEGORIES_MIGRATION_GUIDE.md` - Categories system details
- `API_EXAMPLES.md` - API usage examples

---

## 🎯 Next Steps

1. ✅ Copy prompt di atas ke AI
2. ✅ Tunggu laporan diagnosis
3. ✅ Review dan approve
4. ✅ AI melakukan integrasi
5. ✅ Test dan verify
6. ✅ Deploy to production

**Good luck! 🚀**

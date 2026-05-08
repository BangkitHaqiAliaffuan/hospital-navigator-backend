



Saya memiliki project **Hospital Navigator** dengan backend Express.js yang sudah deploy di Vercel dan frontend yang perlu diintegrasikan.

## 🎯 TUGAS UTAMA:

### **FASE 1: ANALISIS & DIAGNOSIS** ⚠️
**SEBELUM melakukan perubahan apapun, tolong lakukan analisis mendalam:**

1. **Cek Struktur Frontend:**
   - Baca semua file di folder frontend
   - Identifikasi framework yang digunakan (React, Vue, Angular, dll)
   - Identifikasi state management (Redux, Zustand, Context API, dll)
   - Identifikasi HTTP client yang digunakan (axios, fetch, dll)
   - Cek apakah sudah ada service layer atau API calls

2. **Cek Integrasi yang Sudah Ada:**
   - Apakah sudah ada API calls ke backend?
   - Apakah API URL sudah dikonfigurasi dengan benar?
   - Apakah sudah ada type definitions untuk data models?
   - Apakah sudah ada error handling?
   - Apakah sudah ada loading states?

3. **Identifikasi Gap:**
   - Endpoint mana yang belum terintegrasi?
   - Feature mana yang belum berfungsi?
   - Data mana yang masih hardcoded?
   - Validasi mana yang masih kurang?

4. **Buat Laporan Diagnosis:**
   ```
   ✅ Yang Sudah Benar:
   - [list semua yang sudah terintegrasi dengan baik]
   
   ❌ Yang Perlu Diperbaiki:
   - [list semua yang perlu diperbaiki]
   
   🆕 Yang Perlu Ditambahkan:
   - [list semua feature/integrasi baru yang perlu ditambahkan]
   
   📊 Estimasi Kompleksitas:
   - Simple / Medium / Complex
   ```

**JANGAN LANJUT KE FASE 2 SEBELUM SAYA APPROVE LAPORAN DIAGNOSIS INI!**

---

### **FASE 2: IMPLEMENTASI** (Setelah diagnosis diapprove)

Setelah diagnosis selesai dan diapprove, lakukan integrasi lengkap dengan backend.

---

## 📚 KONTEKS BACKEND

### **Backend Architecture:**
- **Framework:** Express.js
- **Deployment:** Vercel Serverless Functions
- **Data Storage:** In-memory (JavaScript objects)
- **API Version:** v1
- **Base URL:** `https://your-backend.vercel.app/api/v1`

### **Database Structure (ERD):**

```
┌─────────────────────┐
│   HOSPITAL_ROOMS    │
├─────────────────────┤
│ id (PK)            │──┐
│ name               │  │
│ category (FK)      │──┼──┐
│ locationHint       │  │  │
│ description        │  │  │
│ floor              │  │  │
└─────────────────────┘  │  │
                         │  │
┌─────────────────────┐  │  │
│    QR_ANCHORS       │  │  │
├─────────────────────┤  │  │
│ qrId (PK)          │  │  │
│ roomId (FK)        │──┘  │
│ svgX               │     │
│ svgY               │     │
│ label              │     │
│ floor              │     │
│ routeNodeId        │     │
└─────────────────────┘     │
                            │
┌─────────────────────┐     │
│    CATEGORIES       │     │
├─────────────────────┤     │
│ name (PK)          │◄────┘
│ description        │
└─────────────────────┘
```

---

## 🔌 COMPLETE API ENDPOINTS

### **1. Health Check**
```
GET /api/v1/health
```
**Response:**
```json
{
  "success": true,
  "message": "Hospital Navigator API is running",
  "timestamp": "2024-01-01T00:00:00.000Z"
}
```

---

### **2. Rooms API**

#### **2.1 Get All Rooms**
```
GET /api/v1/rooms
GET /api/v1/rooms?category=Emergency
GET /api/v1/rooms?floor=1
GET /api/v1/rooms?search=igd
```

**Response:**
```json
{
  "success": true,
  "data": [
    {
      "id": "IGD",
      "name": "IGD",
      "category": "Emergency",
      "locationHint": "Sayap kiri bawah peta",
      "description": "Instalasi Gawat Darurat untuk penanganan kondisi medis darurat 24 jam.",
      "floor": 1
    }
  ],
  "count": 1
}
```

#### **2.2 Get Room by ID**
```
GET /api/v1/rooms/:id
```

**Response:**
```json
{
  "success": true,
  "data": {
    "id": "IGD",
    "name": "IGD",
    "category": "Emergency",
    "locationHint": "Sayap kiri bawah peta",
    "description": "Instalasi Gawat Darurat...",
    "floor": 1
  }
}
```

#### **2.3 Get Room Categories (Legacy - Deprecated)**
```
GET /api/v1/rooms/categories
```
⚠️ **Use `/api/v1/categories` instead**

#### **2.4 Get Room Statistics**
```
GET /api/v1/rooms/stats
```

**Response:**
```json
{
  "success": true,
  "data": {
    "total": 75,
    "byFloor": {
      "-1": 2,
      "0": 2,
      "1": 50,
      "2": 21
    },
    "byCategory": {
      "Emergency": 5,
      "Outpatient": 8,
      "Critical Care": 2,
      "Diagnostic": 6,
      "Facility": 20,
      "Service": 10,
      "Administration": 8,
      "Surgery": 3,
      "Treatment": 5,
      "Ward": 8
    },
    "categories": [...]
  }
}
```

#### **2.5 Create Room**
```
POST /api/v1/rooms
Content-Type: application/json

{
  "id": "R._New",
  "name": "Ruang Baru",
  "category": "Facility",
  "locationHint": "Lantai 1",
  "description": "Deskripsi ruangan",
  "floor": 1
}
```

#### **2.6 Update Room**
```
PUT /api/v1/rooms/:id
Content-Type: application/json

{
  "name": "Updated Name",
  "category": "Emergency",
  "locationHint": "Updated location",
  "description": "Updated description",
  "floor": 2
}
```

#### **2.7 Delete Room**
```
DELETE /api/v1/rooms/:id
```

---

### **3. QR Anchors API**

#### **3.1 Get All QR Anchors**
```
GET /api/v1/qr-anchors
GET /api/v1/qr-anchors?roomId=IGD
GET /api/v1/qr-anchors?floor=1
GET /api/v1/qr-anchors?search=persimpangan
```

**Response:**
```json
{
  "success": true,
  "data": [
    {
      "qrId": "QR-F1-N01",
      "roomId": "Area_Pelayanan_IGD",
      "svgX": 632.95538,
      "svgY": 753.07831,
      "label": "Persimpangan Area Pelayanan IGD",
      "floor": 1,
      "routeNodeId": "optional_node_id"
    }
  ],
  "count": 1
}
```

#### **3.2 Get QR Anchor by ID**
```
GET /api/v1/qr-anchors/:qrId
```

#### **3.3 Resolve QR Code**
```
POST /api/v1/qr-anchors/resolve
Content-Type: application/json

{
  "qrCode": "QR-F1-N01"
}
```

**Response:**
```json
{
  "success": true,
  "data": {
    "qrId": "QR-F1-N01",
    "roomId": "Area_Pelayanan_IGD",
    "svgX": 632.95538,
    "svgY": 753.07831,
    "label": "Persimpangan Area Pelayanan IGD",
    "floor": 1
  }
}
```

#### **3.4 Get QR Anchor Statistics**
```
GET /api/v1/qr-anchors/stats
```

#### **3.5 Create QR Anchor**
```
POST /api/v1/qr-anchors
Content-Type: application/json

{
  "qrId": "QR-F1-N99",
  "roomId": "IGD",
  "svgX": 650.5,
  "svgY": 750.5,
  "label": "New QR Anchor",
  "floor": 1,
  "routeNodeId": "optional"
}
```

#### **3.6 Update QR Anchor**
```
PUT /api/v1/qr-anchors/:qrId
```

#### **3.7 Delete QR Anchor**
```
DELETE /api/v1/qr-anchors/:qrId
```

---

### **4. Categories API** 🆕

#### **4.1 Get All Categories**
```
GET /api/v1/categories
```

**Response:**
```json
{
  "success": true,
  "data": [
    {
      "name": "Emergency",
      "description": "Layanan gawat darurat dan penanganan kondisi medis kritis yang memerlukan tindakan segera 24 jam"
    },
    {
      "name": "Outpatient",
      "description": "Layanan konsultasi dan pemeriksaan rawat jalan untuk berbagai spesialisasi medis"
    },
    {
      "name": "Critical Care",
      "description": "Unit perawatan intensif untuk pasien dengan kondisi kritis yang memerlukan monitoring ketat"
    },
    {
      "name": "Diagnostic",
      "description": "Layanan pemeriksaan penunjang diagnosis seperti laboratorium, radiologi, dan pencitraan medis"
    },
    {
      "name": "Facility",
      "description": "Fasilitas umum dan pendukung operasional rumah sakit seperti toilet, lift, tangga, dan parkir"
    },
    {
      "name": "Service",
      "description": "Layanan pendukung medis dan non-medis seperti informasi, edukasi, dan konsultasi"
    },
    {
      "name": "Administration",
      "description": "Area administrasi, manajemen, dan tata kelola rumah sakit"
    },
    {
      "name": "Surgery",
      "description": "Area tindakan operasi dan prosedur bedah dengan kontrol sterilitas tinggi"
    },
    {
      "name": "Treatment",
      "description": "Ruang terapi dan tindakan medis khusus seperti hemodialisa, fisioterapi, dan rehabilitasi"
    },
    {
      "name": "Ward",
      "description": "Ruang rawat inap untuk perawatan pasien dengan berbagai kelas dan spesialisasi"
    }
  ],
  "count": 10
}
```

#### **4.2 Get Category Names Only**
```
GET /api/v1/categories/names
```

**Response:**
```json
{
  "success": true,
  "data": [
    "Administration",
    "Critical Care",
    "Diagnostic",
    "Emergency",
    "Facility",
    "Outpatient",
    "Service",
    "Surgery",
    "Treatment",
    "Ward"
  ],
  "count": 10
}
```

#### **4.3 Get Category by Name**
```
GET /api/v1/categories/:name
```

#### **4.4 Get Category Statistics**
```
GET /api/v1/categories/stats
```

#### **4.5 Validate Category**
```
POST /api/v1/categories/validate
Content-Type: application/json

{
  "name": "Emergency"
}
```

**Response (Valid):**
```json
{
  "success": true,
  "message": "Category is valid",
  "data": {
    "name": "Emergency",
    "description": "Layanan gawat darurat..."
  }
}
```

**Response (Invalid):**
```json
{
  "success": false,
  "message": "Category 'InvalidCategory' does not exist. Available categories: Administration, Critical Care, ..."
}
```

---

## 📦 EXPECTED FRONTEND IMPLEMENTATION

### **1. Type Definitions (TypeScript)**

```typescript
// types/room.ts
export interface Room {
  id: string;
  name: string;
  category: string;
  locationHint: string;
  description: string;
  floor: number;
}

// types/qrAnchor.ts
export interface QrAnchor {
  qrId: string;
  roomId: string;
  svgX: number;
  svgY: number;
  label: string;
  floor: number;
  routeNodeId?: string;
}

// types/category.ts
export interface Category {
  name: string;
  description: string;
}

// types/api.ts
export interface ApiResponse<T> {
  success: boolean;
  data: T;
  count?: number;
  message?: string;
  error?: string;
}
```

---

### **2. API Service Layer**

```typescript
// services/api.ts
import axios from 'axios';

const API_BASE_URL = import.meta.env.VITE_API_URL || 'http://localhost:3001/api/v1';

export const api = axios.create({
  baseURL: API_BASE_URL,
  headers: {
    'Content-Type': 'application/json',
  },
});

// Interceptors for error handling
api.interceptors.response.use(
  (response) => response,
  (error) => {
    console.error('API Error:', error);
    return Promise.reject(error);
  }
);
```

```typescript
// services/roomService.ts
import { api } from './api';
import { Room, ApiResponse } from '../types';

export const roomService = {
  async getAllRooms(params?: { category?: string; floor?: number; search?: string }): Promise<Room[]> {
    const response = await api.get<ApiResponse<Room[]>>('/rooms', { params });
    return response.data.data;
  },

  async getRoomById(id: string): Promise<Room> {
    const response = await api.get<ApiResponse<Room>>(`/rooms/${id}`);
    return response.data.data;
  },

  async getRoomStats() {
    const response = await api.get('/rooms/stats');
    return response.data.data;
  },

  async createRoom(room: Omit<Room, 'id'> & { id: string }): Promise<Room> {
    const response = await api.post<ApiResponse<Room>>('/rooms', room);
    return response.data.data;
  },

  async updateRoom(id: string, room: Partial<Room>): Promise<Room> {
    const response = await api.put<ApiResponse<Room>>(`/rooms/${id}`, room);
    return response.data.data;
  },

  async deleteRoom(id: string): Promise<void> {
    await api.delete(`/rooms/${id}`);
  },
};
```

```typescript
// services/qrAnchorService.ts
import { api } from './api';
import { QrAnchor, ApiResponse } from '../types';

export const qrAnchorService = {
  async getAllQrAnchors(params?: { roomId?: string; floor?: number; search?: string }): Promise<QrAnchor[]> {
    const response = await api.get<ApiResponse<QrAnchor[]>>('/qr-anchors', { params });
    return response.data.data;
  },

  async getQrAnchorById(qrId: string): Promise<QrAnchor> {
    const response = await api.get<ApiResponse<QrAnchor>>(`/qr-anchors/${qrId}`);
    return response.data.data;
  },

  async resolveQrCode(qrCode: string): Promise<QrAnchor> {
    const response = await api.post<ApiResponse<QrAnchor>>('/qr-anchors/resolve', { qrCode });
    return response.data.data;
  },

  async getQrAnchorStats() {
    const response = await api.get('/qr-anchors/stats');
    return response.data.data;
  },

  async createQrAnchor(anchor: QrAnchor): Promise<QrAnchor> {
    const response = await api.post<ApiResponse<QrAnchor>>('/qr-anchors', anchor);
    return response.data.data;
  },

  async updateQrAnchor(qrId: string, anchor: Partial<QrAnchor>): Promise<QrAnchor> {
    const response = await api.put<ApiResponse<QrAnchor>>(`/qr-anchors/${qrId}`, anchor);
    return response.data.data;
  },

  async deleteQrAnchor(qrId: string): Promise<void> {
    await api.delete(`/qr-anchors/${qrId}`);
  },
};
```

```typescript
// services/categoryService.ts
import { api } from './api';
import { Category, ApiResponse } from '../types';

export const categoryService = {
  async getAllCategories(): Promise<Category[]> {
    const response = await api.get<ApiResponse<Category[]>>('/categories');
    return response.data.data;
  },

  async getCategoryNames(): Promise<string[]> {
    const response = await api.get<ApiResponse<string[]>>('/categories/names');
    return response.data.data;
  },

  async getCategoryByName(name: string): Promise<Category> {
    const response = await api.get<ApiResponse<Category>>(`/categories/${name}`);
    return response.data.data;
  },

  async validateCategory(name: string): Promise<boolean> {
    try {
      const response = await api.post('/categories/validate', { name });
      return response.data.success;
    } catch {
      return false;
    }
  },
};
```

---

### **3. Custom Hooks**

```typescript
// hooks/useRooms.ts
import { useState, useEffect } from 'react';
import { roomService } from '../services/roomService';
import { Room } from '../types';

export const useRooms = (filters?: { category?: string; floor?: number; search?: string }) => {
  const [rooms, setRooms] = useState<Room[]>([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    const fetchRooms = async () => {
      try {
        setLoading(true);
        const data = await roomService.getAllRooms(filters);
        setRooms(data);
        setError(null);
      } catch (err) {
        setError('Failed to fetch rooms');
        console.error(err);
      } finally {
        setLoading(false);
      }
    };

    fetchRooms();
  }, [filters?.category, filters?.floor, filters?.search]);

  return { rooms, loading, error, refetch: () => fetchRooms() };
};
```

```typescript
// hooks/useCategories.ts
import { useState, useEffect } from 'react';
import { categoryService } from '../services/categoryService';
import { Category } from '../types';

export const useCategories = () => {
  const [categories, setCategories] = useState<Category[]>([]);
  const [categoryNames, setCategoryNames] = useState<string[]>([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    const fetchCategories = async () => {
      try {
        setLoading(true);
        const [allCategories, names] = await Promise.all([
          categoryService.getAllCategories(),
          categoryService.getCategoryNames(),
        ]);
        setCategories(allCategories);
        setCategoryNames(names);
        setError(null);
      } catch (err) {
        setError('Failed to fetch categories');
        console.error(err);
      } finally {
        setLoading(false);
      }
    };

    fetchCategories();
  }, []);

  return { categories, categoryNames, loading, error };
};
```

```typescript
// hooks/useQrAnchors.ts
import { useState, useEffect } from 'react';
import { qrAnchorService } from '../services/qrAnchorService';
import { QrAnchor } from '../types';

export const useQrAnchors = (filters?: { roomId?: string; floor?: number }) => {
  const [qrAnchors, setQrAnchors] = useState<QrAnchor[]>([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    const fetchQrAnchors = async () => {
      try {
        setLoading(true);
        const data = await qrAnchorService.getAllQrAnchors(filters);
        setQrAnchors(data);
        setError(null);
      } catch (err) {
        setError('Failed to fetch QR anchors');
        console.error(err);
      } finally {
        setLoading(false);
      }
    };

    fetchQrAnchors();
  }, [filters?.roomId, filters?.floor]);

  return { qrAnchors, loading, error };
};
```

---

### **4. Environment Variables**

```env
# .env.development
VITE_API_URL=http://localhost:3001/api/v1

# .env.production
VITE_API_URL=https://your-backend.vercel.app/api/v1
```

---

## ✅ INTEGRATION CHECKLIST

### **Phase 1: Setup**
- [ ] Install dependencies (axios, react-query, etc.)
- [ ] Create environment variables
- [ ] Setup API base configuration
- [ ] Create type definitions

### **Phase 2: Services**
- [ ] Create API service layer
- [ ] Create roomService
- [ ] Create qrAnchorService
- [ ] Create categoryService
- [ ] Add error handling
- [ ] Add request/response interceptors

### **Phase 3: Hooks**
- [ ] Create useRooms hook
- [ ] Create useCategories hook
- [ ] Create useQrAnchors hook
- [ ] Add loading states
- [ ] Add error states
- [ ] Add refetch functionality

### **Phase 4: Components**
- [ ] Update room list components
- [ ] Update room detail components
- [ ] Update category filter components
- [ ] Update QR scanner components
- [ ] Update navigation components
- [ ] Add loading indicators
- [ ] Add error messages

### **Phase 5: Features**
- [ ] Room search functionality
- [ ] Category filtering
- [ ] Floor filtering
- [ ] QR code scanning
- [ ] Navigation routing
- [ ] Room details display
- [ ] Category descriptions

### **Phase 6: Testing**
- [ ] Test all API endpoints
- [ ] Test error handling
- [ ] Test loading states
- [ ] Test CORS configuration
- [ ] Test production deployment
- [ ] Test mobile responsiveness

### **Phase 7: Optimization**
- [ ] Add caching (React Query)
- [ ] Add pagination if needed
- [ ] Optimize re-renders
- [ ] Add lazy loading
- [ ] Add error boundaries

---

## 🚨 COMMON ISSUES & SOLUTIONS

### **1. CORS Error**
```
Access to XMLHttpRequest blocked by CORS policy
```

**Solution:**
- Check backend ALLOWED_ORIGINS environment variable
- Make sure frontend URL is included
- Check if credentials are needed

### **2. 404 Not Found**
```
GET /api/v1/rooms 404
```

**Solution:**
- Verify API_URL in environment variables
- Check if backend is deployed correctly
- Test endpoint with curl/Postman first

### **3. Network Error**
```
Network Error / Failed to fetch
```

**Solution:**
- Check if backend is running
- Check internet connection
- Verify API URL is correct
- Check browser console for details

### **4. Invalid Category**
```
Category 'XYZ' does not exist
```

**Solution:**
- Use categories from `/api/v1/categories/names`
- Validate category before submitting
- Update hardcoded categories

---

## 📊 SUCCESS CRITERIA

Frontend integration dianggap berhasil jika:

1. ✅ Semua API endpoints dapat diakses tanpa error
2. ✅ Data rooms ditampilkan dengan benar
3. ✅ Category filter berfungsi
4. ✅ Floor filter berfungsi
5. ✅ Search functionality berfungsi
6. ✅ QR code scanning berfungsi
7. ✅ Navigation routing berfungsi
8. ✅ Loading states ditampilkan
9. ✅ Error handling berfungsi
10. ✅ Responsive di mobile dan desktop
11. ✅ No console errors
12. ✅ CORS configured correctly

---

## 📝 DELIVERABLES

Setelah integrasi selesai, berikan:

1. **Laporan Diagnosis** (Fase 1)
2. **List file yang dibuat/dimodifikasi**
3. **Screenshot/video demo aplikasi berjalan**
4. **List API endpoints yang sudah terintegrasi**
5. **Known issues (jika ada)**
6. **Rekomendasi improvement**

---

## 🔗 REFERENCE FILES

- `server/CATEGORIES_MIGRATION_GUIDE.md` - Detail categories system
- `server/API_EXAMPLES.md` - API usage examples
- `server/src/data/hospitalRooms.js` - Room data structure
- `server/src/data/qrAnchors.js` - QR anchor data structure
- `server/src/data/categories.js` - Categories data structure

---

## 🎯 FINAL NOTES

- **Prioritas:** Functionality > UI/UX > Optimization
- **Code Quality:** Clean, readable, maintainable
- **Error Handling:** Comprehensive error messages
- **User Experience:** Loading states, error states, empty states
- **Performance:** Minimize unnecessary re-renders
- **Security:** No sensitive data in console logs

---

# PROMPT END

---

## 📞 Support

Jika ada pertanyaan atau butuh klarifikasi:
1. Tanyakan sebelum implementasi
2. Dokumentasikan semua asumsi yang dibuat
3. Report blockers immediately
4. Test incrementally

**Good luck! 🚀**

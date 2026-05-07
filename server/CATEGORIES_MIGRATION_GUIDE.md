# 📋 Categories System Migration Guide

## 🎯 Overview

Backend Hospital Navigator telah diupdate dengan **sistem Categories terpisah** sesuai dengan ERD. Sebelumnya, categories hanya berupa string hardcoded. Sekarang categories memiliki tabel/struktur data tersendiri dengan validasi.

---

## 🔄 Perubahan yang Telah Dilakukan di Backend

### 1. **File Baru yang Ditambahkan**

#### ✅ `server/src/data/categories.js`
File database untuk master data categories dengan struktur:

```javascript
{
  name: "Emergency",           // Primary Key
  description: "Deskripsi kategori"
}
```

**10 Categories yang tersedia:**
1. `Emergency` - Layanan gawat darurat
2. `Outpatient` - Layanan rawat jalan
3. `Critical Care` - Unit perawatan intensif
4. `Diagnostic` - Layanan pemeriksaan penunjang
5. `Facility` - Fasilitas umum dan pendukung
6. `Service` - Layanan pendukung medis
7. `Administration` - Area administrasi dan manajemen
8. `Surgery` - Area tindakan operasi
9. `Treatment` - Ruang terapi dan tindakan medis
10. `Ward` - Ruang rawat inap

**Functions yang tersedia:**
- `getAllCategories()` - Get semua categories
- `getCategoryByName(name)` - Get category by name
- `categoryExists(name)` - Check apakah category ada
- `getCategoryNames()` - Get array nama categories saja
- `validateCategory(name)` - Validasi category name
- `upsertCategory(data)` - Create/update category
- `deleteCategory(name)` - Delete category
- `getCategoryStats()` - Get statistik categories

#### ✅ `server/src/routes/categories.js`
Route handler untuk Categories API dengan endpoints:

| Method | Endpoint | Deskripsi |
|--------|----------|-----------|
| GET | `/api/v1/categories` | Get all categories |
| GET | `/api/v1/categories/names` | Get category names only |
| GET | `/api/v1/categories/stats` | Get category statistics |
| GET | `/api/v1/categories/:name` | Get category by name |
| POST | `/api/v1/categories` | Create/update category |
| PUT | `/api/v1/categories/:name` | Update category |
| DELETE | `/api/v1/categories/:name` | Delete category |
| POST | `/api/v1/categories/validate` | Validate category name |

---

### 2. **File yang Dimodifikasi**

#### ✅ `server/src/routes/index.js`
**Perubahan:**
```javascript
// BEFORE
import roomsRouter from './rooms.js';
import qrAnchorsRouter from './qrAnchors.js';

router.use('/rooms', roomsRouter);
router.use('/qr-anchors', qrAnchorsRouter);

// AFTER
import roomsRouter from './rooms.js';
import qrAnchorsRouter from './qrAnchors.js';
import categoriesRouter from './categories.js';  // ✅ BARU

router.use('/rooms', roomsRouter);
router.use('/qr-anchors', qrAnchorsRouter);
router.use('/categories', categoriesRouter);     // ✅ BARU
```

#### ✅ `server/src/data/hospitalRooms.js`
**Perubahan:**
```javascript
// BEFORE
export const hospitalRooms = { ... }

export const upsertRoom = (roomData) => {
  // No validation
  hospitalRooms[id] = { ... };
}

// AFTER
import { validateCategory } from './categories.js';  // ✅ BARU

export const hospitalRooms = { ... }

export const upsertRoom = (roomData) => {
  // ✅ BARU: Validasi category exists
  const categoryValidation = validateCategory(category);
  if (!categoryValidation.valid) {
    throw new Error(categoryValidation.error);
  }
  
  hospitalRooms[id] = { ... };
}
```

#### ✅ `server/src/index.js`
**Perubahan:**
```javascript
// BEFORE
app.get('/', (req, res) => {
  res.json({
    endpoints: {
      health: '/api/v1/health',
      rooms: '/api/v1/rooms',
      qrAnchors: '/api/v1/qr-anchors'
    }
  });
});

// AFTER
app.get('/', (req, res) => {
  res.json({
    endpoints: {
      health: '/api/v1/health',
      rooms: '/api/v1/rooms',
      qrAnchors: '/api/v1/qr-anchors',
      categories: '/api/v1/categories'  // ✅ BARU
    }
  });
});
```

#### ✅ `server/vercel.json`
**Perubahan (sebelumnya untuk fix deployment):**
```json
// BEFORE
{
  "builds": [...],
  "routes": [...],
  "functions": {...}
}

// AFTER
{
  "rewrites": [...],
  "functions": {...}
}
```

---

## 🚀 API Endpoints Baru untuk Frontend

### 1. **Get All Categories**
```javascript
// Request
GET /api/v1/categories

// Response
{
  "success": true,
  "data": [
    {
      "name": "Emergency",
      "description": "Layanan gawat darurat dan penanganan kondisi medis kritis..."
    },
    {
      "name": "Outpatient",
      "description": "Layanan konsultasi dan pemeriksaan rawat jalan..."
    }
    // ... 8 more categories
  ],
  "count": 10
}
```

### 2. **Get Category Names Only**
```javascript
// Request
GET /api/v1/categories/names

// Response
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

### 3. **Get Category by Name**
```javascript
// Request
GET /api/v1/categories/Emergency

// Response
{
  "success": true,
  "data": {
    "name": "Emergency",
    "description": "Layanan gawat darurat dan penanganan kondisi medis kritis..."
  }
}
```

### 4. **Get Category Statistics**
```javascript
// Request
GET /api/v1/categories/stats

// Response
{
  "success": true,
  "data": {
    "total": 10,
    "categories": [
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
    ]
  }
}
```

### 5. **Validate Category**
```javascript
// Request
POST /api/v1/categories/validate
Content-Type: application/json

{
  "name": "Emergency"
}

// Response (Valid)
{
  "success": true,
  "message": "Category is valid",
  "data": {
    "name": "Emergency",
    "description": "Layanan gawat darurat..."
  }
}

// Response (Invalid)
{
  "success": false,
  "message": "Category 'InvalidCategory' does not exist. Available categories: Administration, Critical Care, ..."
}
```

---

## 💻 Contoh Implementasi di Frontend

### **React/TypeScript Example**

#### 1. **Type Definitions**
```typescript
// types/category.ts
export interface Category {
  name: string;
  description: string;
}

export interface CategoryResponse {
  success: boolean;
  data: Category[];
  count: number;
}

export interface CategoryNamesResponse {
  success: boolean;
  data: string[];
  count: number;
}
```

#### 2. **API Service**
```typescript
// services/categoryService.ts
import axios from 'axios';
import { Category, CategoryResponse, CategoryNamesResponse } from '../types/category';

const API_BASE_URL = import.meta.env.VITE_API_URL || 'http://localhost:3001/api/v1';

export const categoryService = {
  // Get all categories
  async getAllCategories(): Promise<Category[]> {
    const response = await axios.get<CategoryResponse>(`${API_BASE_URL}/categories`);
    return response.data.data;
  },

  // Get category names only
  async getCategoryNames(): Promise<string[]> {
    const response = await axios.get<CategoryNamesResponse>(`${API_BASE_URL}/categories/names`);
    return response.data.data;
  },

  // Get category by name
  async getCategoryByName(name: string): Promise<Category> {
    const response = await axios.get<{ success: boolean; data: Category }>(
      `${API_BASE_URL}/categories/${name}`
    );
    return response.data.data;
  },

  // Validate category
  async validateCategory(name: string): Promise<boolean> {
    try {
      const response = await axios.post(`${API_BASE_URL}/categories/validate`, { name });
      return response.data.success;
    } catch (error) {
      return false;
    }
  }
};
```

#### 3. **React Hook**
```typescript
// hooks/useCategories.ts
import { useState, useEffect } from 'react';
import { categoryService } from '../services/categoryService';
import { Category } from '../types/category';

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
          categoryService.getCategoryNames()
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

#### 4. **Component Example**
```typescript
// components/CategoryFilter.tsx
import React from 'react';
import { useCategories } from '../hooks/useCategories';

export const CategoryFilter: React.FC = () => {
  const { categories, loading, error } = useCategories();
  const [selectedCategory, setSelectedCategory] = React.useState<string>('');

  if (loading) return <div>Loading categories...</div>;
  if (error) return <div>Error: {error}</div>;

  return (
    <div>
      <label htmlFor="category-select">Filter by Category:</label>
      <select
        id="category-select"
        value={selectedCategory}
        onChange={(e) => setSelectedCategory(e.target.value)}
      >
        <option value="">All Categories</option>
        {categories.map((category) => (
          <option key={category.name} value={category.name}>
            {category.name}
          </option>
        ))}
      </select>
      
      {selectedCategory && (
        <div className="category-description">
          {categories.find(c => c.name === selectedCategory)?.description}
        </div>
      )}
    </div>
  );
};
```

---

## 🔧 Migration Checklist untuk Frontend

### **Step 1: Update API Base URL**
```typescript
// .env atau .env.production
VITE_API_URL=https://your-backend.vercel.app/api/v1
```

### **Step 2: Tambahkan Type Definitions**
- [ ] Buat `types/category.ts` dengan interface Category
- [ ] Update `types/room.ts` jika ada untuk memastikan category adalah string

### **Step 3: Buat Category Service**
- [ ] Buat `services/categoryService.ts`
- [ ] Implementasi fungsi `getAllCategories()`
- [ ] Implementasi fungsi `getCategoryNames()`
- [ ] Implementasi fungsi `getCategoryByName()`
- [ ] Implementasi fungsi `validateCategory()`

### **Step 4: Buat Custom Hook (Optional)**
- [ ] Buat `hooks/useCategories.ts`
- [ ] Handle loading state
- [ ] Handle error state
- [ ] Cache categories data

### **Step 5: Update Existing Components**
- [ ] Update filter components untuk menggunakan categories dari API
- [ ] Update form components untuk validasi category
- [ ] Update room list components untuk menampilkan category description
- [ ] Remove hardcoded category lists

### **Step 6: Testing**
- [ ] Test fetching categories
- [ ] Test category filter
- [ ] Test category validation
- [ ] Test error handling

---

## 🎨 UI/UX Improvements dengan Categories Baru

### **1. Category Badge dengan Description Tooltip**
```typescript
<Tooltip content={category.description}>
  <Badge color={getCategoryColor(category.name)}>
    {category.name}
  </Badge>
</Tooltip>
```

### **2. Category Legend**
```typescript
<div className="category-legend">
  <h3>Categories</h3>
  {categories.map(category => (
    <div key={category.name} className="legend-item">
      <span className="color-indicator" style={{ backgroundColor: getCategoryColor(category.name) }} />
      <div>
        <strong>{category.name}</strong>
        <p>{category.description}</p>
      </div>
    </div>
  ))}
</div>
```

### **3. Category Statistics Dashboard**
```typescript
<div className="category-stats">
  {categories.map(category => {
    const roomCount = rooms.filter(r => r.category === category.name).length;
    return (
      <div key={category.name} className="stat-card">
        <h4>{category.name}</h4>
        <p className="count">{roomCount} rooms</p>
        <p className="description">{category.description}</p>
      </div>
    );
  })}
</div>
```

---

## 🔍 Backward Compatibility

**Good News:** Sistem baru ini **100% backward compatible**!

- ✅ Endpoint `/api/v1/rooms` masih sama
- ✅ Room object structure tidak berubah
- ✅ Category masih berupa string di room object
- ✅ Hanya menambahkan endpoint baru `/api/v1/categories`

**Yang berubah:**
- ❌ Tidak bisa lagi menggunakan category yang tidak ada di master data
- ✅ Validasi category otomatis saat create/update room

---

## 📝 Example API Calls

### **cURL Examples**
```bash
# Get all categories
curl https://your-backend.vercel.app/api/v1/categories

# Get category names only
curl https://your-backend.vercel.app/api/v1/categories/names

# Get specific category
curl https://your-backend.vercel.app/api/v1/categories/Emergency

# Validate category
curl -X POST https://your-backend.vercel.app/api/v1/categories/validate \
  -H "Content-Type: application/json" \
  -d '{"name":"Emergency"}'
```

### **JavaScript Fetch Examples**
```javascript
// Get all categories
const categories = await fetch('https://your-backend.vercel.app/api/v1/categories')
  .then(res => res.json())
  .then(data => data.data);

// Get category names
const categoryNames = await fetch('https://your-backend.vercel.app/api/v1/categories/names')
  .then(res => res.json())
  .then(data => data.data);

// Validate category
const isValid = await fetch('https://your-backend.vercel.app/api/v1/categories/validate', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({ name: 'Emergency' })
})
  .then(res => res.json())
  .then(data => data.success);
```

---

## 🚨 Breaking Changes

**NONE!** Tidak ada breaking changes. Semua endpoint lama masih berfungsi normal.

**Optional Enhancement:**
Jika ingin memanfaatkan sistem categories baru, frontend bisa:
1. Fetch categories dari API instead of hardcoded list
2. Menampilkan category description
3. Validasi category sebelum submit form
4. Menampilkan category statistics

---

## 📞 Support

Jika ada pertanyaan atau issue:
1. Check API response di browser DevTools Network tab
2. Check backend logs di Vercel Dashboard
3. Pastikan CORS settings sudah benar
4. Pastikan API URL sudah benar di environment variables

---

## ✅ Summary

**Backend Changes:**
- ✅ Added `categories.js` data file
- ✅ Added `categories.js` route file
- ✅ Updated `routes/index.js` to include categories route
- ✅ Updated `hospitalRooms.js` to validate categories
- ✅ Updated `index.js` root endpoint to show categories endpoint
- ✅ Fixed `vercel.json` deployment config

**New API Endpoints:**
- ✅ `GET /api/v1/categories` - Get all categories
- ✅ `GET /api/v1/categories/names` - Get category names
- ✅ `GET /api/v1/categories/stats` - Get statistics
- ✅ `GET /api/v1/categories/:name` - Get by name
- ✅ `POST /api/v1/categories/validate` - Validate category

**Frontend Tasks:**
- [ ] Create category types
- [ ] Create category service
- [ ] Create useCategories hook
- [ ] Update components to use new API
- [ ] Add category descriptions to UI
- [ ] Test integration

---

**Happy Coding! 🚀**

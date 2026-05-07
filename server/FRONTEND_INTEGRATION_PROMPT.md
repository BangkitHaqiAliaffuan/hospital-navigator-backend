# 🤖 Prompt untuk AI - Frontend Integration

Copy paste prompt ini ke AI yang akan mengupdate frontend Anda:

---

## Prompt untuk AI:

```
Saya memiliki backend Hospital Navigator yang baru saja diupdate dengan sistem Categories terpisah. 
Tolong bantu saya mengintegrasikan perubahan ini ke frontend.

### Konteks Backend Changes:

1. **Tabel Categories Baru**
   - Backend sekarang memiliki tabel categories terpisah dengan 10 kategori
   - Setiap category memiliki: `name` (PK) dan `description`
   - Categories: Emergency, Outpatient, Critical Care, Diagnostic, Facility, Service, Administration, Surgery, Treatment, Ward

2. **API Endpoints Baru:**
   - `GET /api/v1/categories` - Get all categories dengan description
   - `GET /api/v1/categories/names` - Get array nama categories saja
   - `GET /api/v1/categories/:name` - Get category by name
   - `GET /api/v1/categories/stats` - Get statistics
   - `POST /api/v1/categories/validate` - Validate category name

3. **Response Format:**
```json
// GET /api/v1/categories
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
    }
    // ... 8 more
  ],
  "count": 10
}

// GET /api/v1/categories/names
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

### Yang Perlu Dilakukan di Frontend:

1. **Buat Type Definition untuk Category:**
   ```typescript
   export interface Category {
     name: string;
     description: string;
   }
   ```

2. **Buat Category Service:**
   - Function untuk fetch all categories
   - Function untuk fetch category names
   - Function untuk validate category
   - Gunakan base URL dari environment variable

3. **Buat Custom Hook `useCategories`:**
   - Fetch categories saat component mount
   - Handle loading state
   - Handle error state
   - Return categories array dan categoryNames array

4. **Update Existing Components:**
   - Ganti hardcoded category list dengan data dari API
   - Tambahkan category description di UI (tooltip/info)
   - Update filter components
   - Update form validation

5. **Backward Compatibility:**
   - Endpoint `/api/v1/rooms` tidak berubah
   - Room object structure tetap sama
   - Category masih berupa string di room object
   - Hanya menambahkan endpoint baru untuk categories

### File yang Perlu Dibuat/Update:

**Buat Baru:**
- `src/types/category.ts` - Type definitions
- `src/services/categoryService.ts` - API calls
- `src/hooks/useCategories.ts` - Custom hook

**Update:**
- Components yang menampilkan category filter
- Components yang menampilkan room list
- Form components untuk create/edit room

### Contoh Implementasi yang Diharapkan:

```typescript
// Example: CategoryFilter component
import { useCategories } from '../hooks/useCategories';

export const CategoryFilter = () => {
  const { categories, loading } = useCategories();
  
  if (loading) return <div>Loading...</div>;
  
  return (
    <select>
      <option value="">All Categories</option>
      {categories.map(cat => (
        <option key={cat.name} value={cat.name} title={cat.description}>
          {cat.name}
        </option>
      ))}
    </select>
  );
};
```

Tolong implementasikan perubahan ini dengan:
- Clean code dan best practices
- Proper error handling
- Loading states
- TypeScript types yang benar
- Reusable components/hooks

Dokumentasi lengkap ada di file CATEGORIES_MIGRATION_GUIDE.md
```

---

## File Referensi yang Tersedia:

1. **CATEGORIES_MIGRATION_GUIDE.md** - Dokumentasi lengkap dengan contoh kode
2. **API_EXAMPLES.md** - Contoh API calls (akan diupdate)
3. **server/src/data/categories.js** - Source code categories database
4. **server/src/routes/categories.js** - Source code categories routes

---

## Quick Test Commands:

```bash
# Test categories endpoint
curl https://your-backend.vercel.app/api/v1/categories

# Test category names
curl https://your-backend.vercel.app/api/v1/categories/names

# Test specific category
curl https://your-backend.vercel.app/api/v1/categories/Emergency
```

---

## Environment Variables yang Diperlukan:

```env
# Frontend .env
VITE_API_URL=https://your-backend.vercel.app/api/v1
```

---

**Catatan Penting:**
- Tidak ada breaking changes
- Semua endpoint lama masih berfungsi
- Categories bersifat read-only untuk user biasa
- Validasi category otomatis di backend saat create/update room

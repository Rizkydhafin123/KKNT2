# 🚀 Panduan Deploy ke Netlify

## 📋 Checklist Pre-Deploy

### ✅ **Persiapan Code**
- [x] Next.js configured untuk static export
- [x] netlify.toml sudah ada
- [x] package.json build scripts ready
- [x] Environment variables documented
- [x] Mobile-responsive tested

### ✅ **Database Setup (Optional)**
- [ ] Supabase project created
- [ ] Database tables created (run scripts/setup-database.sql)
- [ ] Environment variables noted
- [ ] Test connection working

## 🔧 Step-by-Step Deployment

### **1. Prepare Repository**
\`\`\`bash
# Ensure all files are committed
git add .
git commit -m "Ready for Netlify deployment"
git push origin main
\`\`\`

### **2. Netlify Site Setup**
1. Go to [netlify.com](https://netlify.com)
2. Click "Add new site" → "Import an existing project"
3. Connect your Git provider (GitHub/GitLab)
4. Select your repository
5. Build settings will be auto-detected from `netlify.toml`

### **3. Environment Variables**
In Netlify Dashboard → Site settings → Environment variables:

\`\`\`env
# Required for Supabase (optional)
NEXT_PUBLIC_SUPABASE_URL=https://your-project.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=your-anon-key-here

# Optional: Custom domain
NEXT_PUBLIC_SITE_URL=https://your-domain.com
\`\`\`

### **4. Deploy Settings**
Netlify will automatically use these settings from `netlify.toml`:
- **Build command**: `npm run build`
- **Publish directory**: `out`
- **Node version**: 18

### **5. Custom Domain (Optional)**
1. Go to Site settings → Domain management
2. Add custom domain
3. Configure DNS records
4. Enable HTTPS (automatic)

## 🔍 Troubleshooting

### **Build Errors**
\`\`\`bash
# Common issues and solutions:

# 1. Node version mismatch
# Solution: Check netlify.toml has NODE_VERSION = "18"

# 2. Missing dependencies
# Solution: Ensure package.json has all dependencies

# 3. Environment variables
# Solution: Double-check variable names and values
\`\`\`

### **Runtime Errors**
\`\`\`bash
# 1. 404 on page refresh
# Solution: netlify.toml redirects should handle this

# 2. API calls failing
# Solution: Check environment variables are set correctly

# 3. Database connection issues
# Solution: Verify Supabase URL and key
\`\`\`

### **PENTING: Supabase Row Level Security (RLS) dan Autentikasi Lokal**

Sistem ini menggunakan autentikasi berbasis `localStorage` (lokal) untuk mengelola pengguna. Jika Anda mengaktifkan Supabase dan mengalami error saat mencoba menambah, mengedit, atau menghapus data UMKM, kemungkinan besar ini disebabkan oleh **kebijakan Row Level Security (RLS)** di tabel `umkm` Supabase Anda.

Kebijakan RLS yang ada (`user_id::text = auth.uid()::text`) dirancang untuk bekerja dengan sistem autentikasi bawaan Supabase (Supabase Auth), di mana `auth.uid()` akan mengembalikan ID pengguna yang sedang login. Karena sistem ini menggunakan autentikasi lokal, `auth.uid()` akan selalu `null`, sehingga operasi database akan ditolak oleh RLS.

**Untuk mengatasi ini, Anda memiliki beberapa opsi:**

1.  **Nonaktifkan RLS pada tabel `umkm` (Tidak Disarankan untuk Produksi):**
    *   Masuk ke Dashboard Supabase Anda.
    *   Pilih project Anda.
    *   Pergi ke **Authentication** > **Policies**.
    *   Cari tabel `umkm` dan nonaktifkan semua kebijakan RLS yang terkait.
    *   **Catatan:** Ini akan membuat tabel `umkm` dapat diakses secara publik oleh siapa saja yang memiliki `anon_key` Anda, yang **tidak aman untuk data sensitif di lingkungan produksi**.

2.  **Sesuaikan Kebijakan RLS (Disarankan untuk Keamanan):**
    *   Jika Anda ingin tetap menggunakan RLS, Anda perlu mengubah kebijakan agar tidak bergantung pada `auth.uid()`. Misalnya, Anda bisa membuat kebijakan yang mengizinkan semua operasi jika Anda tidak memiliki data sensitif, atau mengimplementasikan sistem autentikasi Supabase Auth yang sebenarnya.
    *   Contoh kebijakan RLS yang mengizinkan semua operasi (mirip dengan menonaktifkan RLS, tapi tetap ada kebijakan):
        \`\`\`sql
        CREATE POLICY "Allow all operations for now" ON umkm
          FOR ALL USING (true) WITH CHECK (true);
        \`\`\`
        **Peringatan:** Kebijakan ini juga membuat tabel Anda dapat diakses secara publik.

3.  **Implementasikan Supabase Auth (Solusi Terbaik Jangka Panjang):**
    *   Ini adalah solusi paling aman dan terintegrasi. Anda perlu memodifikasi sistem autentikasi di `lib/auth.tsx` untuk menggunakan Supabase Auth, bukan `localStorage`. Ini adalah tugas yang lebih kompleks dan di luar cakupan perbaikan ini.

**Setelah menyesuaikan RLS di Supabase, pastikan untuk melakukan redeploy situs Anda di Netlify.**

## 📊 Performance Optimization

### **Netlify Features to Enable**
- [x] **Asset optimization**: Auto-enabled
- [x] **Form handling**: For contact forms
- [x] **Analytics**: Track usage
- [x] **Branch deploys**: For testing

### **Monitoring**
- Check build logs for warnings
- Monitor Core Web Vitals
- Set up uptime monitoring
- Review analytics regularly

## 🔒 Security Checklist

- [x] Environment variables secured
- [x] HTTPS enabled
- [x] Security headers configured
- [x] No sensitive data in client code
- [x] Database RLS policies active

## 🚀 Go Live Checklist

### **Before Launch**
- [ ] Test all features work
- [ ] Mobile responsiveness verified
- [ ] All forms functional
- [ ] Database operations working
- [ ] Authentication flows tested
- [ ] Export functions working

### **After Launch**
- [ ] Share URL with stakeholders
- [ ] Set up monitoring
- [ ] Document admin credentials
- [ ] Plan user training
- [ ] Schedule regular backups

## 📞 Support Resources

- **Netlify Docs**: https://docs.netlify.com
- **Next.js Deploy Guide**: https://nextjs.org/docs/deployment
- **Supabase Docs**: https://supabase.com/docs

---

**🎉 Your UMKM Management System is now live and ready to help RT/RW communities manage their micro-businesses efficiently!**

# Dokumentasi Database Nukapos

## Pendahuluan
Terdapat 34 tabel utama, termasuk tabel migrasi Prisma (`_prisma_migrations`) dan tabel relasi many-to-many (`_producttosupplier`). Semua tabel memiliki kolom `createdAt` dan `updatedAt` untuk tracking waktu pembuatan dan pembaruan.

Dokumentasi ini mencakup:
- Daftar tabel dengan deskripsi singkat.
- Detail struktur setiap tabel (kolom, tipe data, constraint).
- Indeks dan kunci asing (foreign keys) untuk relasi antar tabel.

## Daftar Tabel
Berikut adalah daftar semua tabel beserta deskripsi singkat:

1. `cities` - Menyimpan data kota/kabupaten, terkait dengan provinsi.
2. `customers` - Data pelanggan toko.
3. `districts` - Data kecamatan, terkait dengan kota.
4. `payment_methods` - Metode pembayaran (tunai atau non-tunai).
5. `products` - Data produk yang dijual.
6. `product_categories` - Kategori produk.
7. `provinces` - Data provinsi.
8. `purchase_orders` - Pesanan pembelian dari pemasok.
9. `purchase_order_items` - Item detail dalam pesanan pembelian.
10. `roles` - Peran pengguna (misalnya admin, kasir).
11. `shifts` - Jadwal shift karyawan toko.
12. `stores` - Data toko, termasuk lokasi dan tenant.
13. `store_bills` - Tagihan toko (dibayar atau belum).
14. `store_types` - Tipe toko.
15. `store_users` - Relasi antara pengguna dan toko.
16. `suppliers` - Data pemasok.
17. `supplier_products` - Produk yang disediakan oleh pemasok.
18. `taxes` - Pajak terkait transaksi.
19. `tenants` - Data tenant (pemilik bisnis).
20. `tenant_users` - Relasi antara pengguna dan tenant.
21. `tokens` - Token autentikasi pengguna (access, refresh, dll.).
22. `transactions` - Data transaksi penjualan.
23. `transaction_details` - Detail item dalam transaksi.
24. `users` - Data pengguna sistem.
25. `user_activity` - Aktivitas pengguna di toko.
26. `productvariants` - Varian produk (misalnya ukuran, warna).
27. `variant_options` - Opsi varian (misalnya small, medium).
28. `variant_option_variants` - Relasi antara varian dan opsi varian.
29. `variant_types` - Tipe varian (misalnya ukuran, rasa).
30. `villages` - Data desa/kelurahan, terkait dengan kecamatan.
31. `warehouse_stocks` - Stok produk di gudang toko.
32. `_prisma_migrations` - Log migrasi database menggunakan Prisma.
33. `_producttosupplier` - Tabel junction untuk relasi many-to-many antara produk dan pemasok.

## Detail Struktur Tabel

### 1. `cashier_cash`
**Deskripsi**: Menyimpan data kas kasir untuk setiap shift kerja.

| Kolom | Tipe Data | Constraint | Deskripsi |
|-------|-----------|------------|-----------|
| `id` | INT(11) | PRIMARY KEY, AUTO_INCREMENT | ID unik kas kasir |
| `shiftId` | INT(11) | NOT NULL, FOREIGN KEY | Referensi ke tabel `shifts` |
| `userId` | BIGINT(20) | NOT NULL, FOREIGN KEY | Referensi ke tabel `users` |
| `storeId` | INT(11) | NOT NULL, FOREIGN KEY | Referensi ke tabel `stores` |
| `initialCash` | BIGINT(20) | NOT NULL, DEFAULT 0 | Kas awal shift |
| `finalCash` | BIGINT(20) | NULLABLE | Kas akhir shift |
| `systemCash` | BIGINT(20) | NULLABLE | Kas menurut sistem |
| `cashDifference` | BIGINT(20) | NULLABLE | Selisih kas |
| `notes` | TEXT | NULLABLE | Catatan tambahan |
| `isBalanced` | TINYINT(1) | NULLABLE | Status keseimbangan kas |
| `createdAt` | DATETIME(3) | NOT NULL, DEFAULT CURRENT_TIMESTAMP(3) | Waktu pembuatan |
| `updatedAt` | DATETIME(3) | NOT NULL | Waktu update terakhir |

**Indexes**:
- PRIMARY KEY: `id`
- INDEX: `idx_cashier_cash_shift_user` (`shiftId`, `userId`)
- INDEX: `idx_cashier_cash_store_date` (`storeId`, `createdAt`)

**Foreign Keys**:
- `shiftId` → `shifts.id` (ON DELETE CASCADE)
- `userId` → `users.id`
- `storeId` → `stores.id`

---

### 2. `provinces`
**Deskripsi**: Master data provinsi.

| Kolom | Tipe Data | Constraint | Deskripsi |
|-------|-----------|------------|-----------|
| `id` | INT(11) | PRIMARY KEY, AUTO_INCREMENT | ID unik provinsi |
| `name` | VARCHAR(255) | NOT NULL | Nama provinsi |
| `createdAt` | DATETIME(3) | NOT NULL, DEFAULT CURRENT_TIMESTAMP(3) | Waktu pembuatan |
| `updatedAt` | DATETIME(3) | NOT NULL | Waktu update terakhir |

---

### 3. `cities`
**Deskripsi**: Master data kota/kabupaten.

| Kolom | Tipe Data | Constraint | Deskripsi |
|-------|-----------|------------|-----------|
| `id` | INT(11) | PRIMARY KEY, AUTO_INCREMENT | ID unik kota |
| `provinceId` | INT(11) | NOT NULL, FOREIGN KEY | Referensi ke tabel `provinces` |
| `name` | VARCHAR(255) | NOT NULL | Nama kota |
| `createdAt` | DATETIME(3) | NOT NULL, DEFAULT CURRENT_TIMESTAMP(3) | Waktu pembuatan |
| `updatedAt` | DATETIME(3) | NOT NULL | Waktu update terakhir |

**Foreign Keys**:
- `provinceId` → `provinces.id`

---

### 4. `districts`
**Deskripsi**: Master data kecamatan.

| Kolom | Tipe Data | Constraint | Deskripsi |
|-------|-----------|------------|-----------|
| `id` | INT(11) | PRIMARY KEY, AUTO_INCREMENT | ID unik kecamatan |
| `cityId` | INT(11) | NOT NULL, FOREIGN KEY | Referensi ke tabel `cities` |
| `name` | VARCHAR(255) | NOT NULL | Nama kecamatan |
| `createdAt` | DATETIME(3) | NOT NULL, DEFAULT CURRENT_TIMESTAMP(3) | Waktu pembuatan |
| `updatedAt` | DATETIME(3) | NOT NULL | Waktu update terakhir |

**Foreign Keys**:
- `cityId` → `cities.id`

---

### 5. `villages`
**Deskripsi**: Master data kelurahan/desa.

| Kolom | Tipe Data | Constraint | Deskripsi |
|-------|-----------|------------|-----------|
| `id` | INT(11) | PRIMARY KEY, AUTO_INCREMENT | ID unik kelurahan |
| `districtId` | INT(11) | NOT NULL, FOREIGN KEY | Referensi ke tabel `districts` |
| `name` | VARCHAR(255) | NOT NULL | Nama kelurahan |
| `createdAt` | DATETIME(3) | NOT NULL, DEFAULT CURRENT_TIMESTAMP(3) | Waktu pembuatan |
| `updatedAt` | DATETIME(3) | NOT NULL | Waktu update terakhir |

**Foreign Keys**:
- `districtId` → `districts.id`

---

### 6. `roles`
**Deskripsi**: Master data role/peran pengguna.

| Kolom | Tipe Data | Constraint | Deskripsi |
|-------|-----------|------------|-----------|
| `id` | INT(11) | PRIMARY KEY, AUTO_INCREMENT | ID unik role |
| `roleName` | VARCHAR(50) | NOT NULL, UNIQUE | Nama role |

---

### 7. `users`
**Deskripsi**: Data pengguna sistem.

| Kolom | Tipe Data | Constraint | Deskripsi |
|-------|-----------|------------|-----------|
| `id` | BIGINT(20) | PRIMARY KEY, AUTO_INCREMENT | ID unik pengguna |
| `name` | VARCHAR(255) | NULLABLE | Nama pengguna |
| `email` | VARCHAR(255) | NOT NULL, UNIQUE | Email pengguna |
| `password` | CHAR(60) | NOT NULL | Password terenkripsi |
| `imageUrl` | VARCHAR(255) | NULLABLE | URL foto profil |
| `phoneNumber` | VARCHAR(15) | NOT NULL | Nomor telepon |
| `isEmailVerified` | TINYINT(1) | NOT NULL, DEFAULT 0 | Status verifikasi email |
| `idCard` | VARCHAR(255) | NULLABLE | Nomor KTP |
| `idCardImage` | VARCHAR(255) | NULLABLE | URL foto KTP |
| `npwp` | VARCHAR(255) | NULLABLE | Nomor NPWP |
| `npwpImage` | VARCHAR(255) | NULLABLE | URL foto NPWP |
| `googleId` | VARCHAR(191) | NULLABLE, UNIQUE | ID Google OAuth |
| `roleId` | INT(11) | NOT NULL, FOREIGN KEY | Referensi ke tabel `roles` |
| `createdAt` | DATETIME(3) | NOT NULL, DEFAULT CURRENT_TIMESTAMP(3) | Waktu pembuatan |
| `updatedAt` | DATETIME(3) | NOT NULL | Waktu update terakhir |

**Foreign Keys**:
- `roleId` → `roles.id`

---

### 8. `tokens`
**Deskripsi**: Token autentikasi dan verifikasi.

| Kolom | Tipe Data | Constraint | Deskripsi |
|-------|-----------|------------|-----------|
| `id` | INT(11) | PRIMARY KEY, AUTO_INCREMENT | ID unik token |
| `userId` | BIGINT(20) | NOT NULL, FOREIGN KEY | Referensi ke tabel `users` |
| `token` | TEXT | NOT NULL | Token string |
| `type` | ENUM | NOT NULL | Tipe token: ACCESS, REFRESH, RESET_PASSWORD, VERIFY_EMAIL |
| `expires` | DATETIME(3) | NOT NULL | Waktu kadaluarsa |
| `blacklisted` | TINYINT(1) | NOT NULL, DEFAULT 0 | Status blacklist |
| `createdAt` | DATETIME(3) | NOT NULL, DEFAULT CURRENT_TIMESTAMP(3) | Waktu pembuatan |

**Foreign Keys**:
- `userId` → `users.id`

---

### 9. `tenants`
**Deskripsi**: Data tenant/penyewa sistem.

| Kolom | Tipe Data | Constraint | Deskripsi |
|-------|-----------|------------|-----------|
| `id` | INT(11) | PRIMARY KEY, AUTO_INCREMENT | ID unik tenant |
| `userId` | BIGINT(20) | NOT NULL, UNIQUE, FOREIGN KEY | Referensi ke tabel `users` |
| `name` | VARCHAR(255) | NOT NULL | Nama tenant |
| `npwp` | VARCHAR(255) | NULLABLE | NPWP tenant |
| `npwpImage` | VARCHAR(255) | NULLABLE | URL foto NPWP |
| `address` | TEXT | NOT NULL | Alamat lengkap |
| `provinceId` | INT(11) | NOT NULL, FOREIGN KEY | Referensi ke tabel `provinces` |
| `cityId` | INT(11) | NOT NULL, FOREIGN KEY | Referensi ke tabel `cities` |
| `districtId` | INT(11) | NOT NULL, FOREIGN KEY | Referensi ke tabel `districts` |
| `villageId` | INT(11) | NOT NULL, FOREIGN KEY | Referensi ke tabel `villages` |
| `postalCode` | VARCHAR(12) | NOT NULL | Kode pos |
| `tenantStatus` | ENUM | NOT NULL, DEFAULT 'ACTIVE' | Status: ACTIVE, INACTIVE |
| `createdAt` | DATETIME(3) | NOT NULL, DEFAULT CURRENT_TIMESTAMP(3) | Waktu pembuatan |
| `updatedAt` | DATETIME(3) | NOT NULL | Waktu update terakhir |

**Foreign Keys**:
- `userId` → `users.id`
- `provinceId` → `provinces.id`
- `cityId` → `cities.id`
- `districtId` → `districts.id`
- `villageId` → `villages.id`

---

### 10. `tenant_users`
**Deskripsi**: Relasi many-to-many antara users dan tenants.

| Kolom | Tipe Data | Constraint | Deskripsi |
|-------|-----------|------------|-----------|
| `id` | BIGINT(20) | PRIMARY KEY, AUTO_INCREMENT | ID unik relasi |
| `userId` | BIGINT(20) | NOT NULL, FOREIGN KEY | Referensi ke tabel `users` |
| `tenantId` | INT(11) | NOT NULL, FOREIGN KEY | Referensi ke tabel `tenants` |
| `status` | ENUM | NOT NULL, DEFAULT 'ACTIVE' | Status: ACTIVE, INACTIVE |
| `createdAt` | DATETIME(3) | NOT NULL, DEFAULT CURRENT_TIMESTAMP(3) | Waktu pembuatan |
| `updatedAt` | DATETIME(3) | NOT NULL | Waktu update terakhir |

**Foreign Keys**:
- `userId` → `users.id`
- `tenantId` → `tenants.id`

---

### 11. `store_types`
**Deskripsi**: Master data tipe toko.

| Kolom | Tipe Data | Constraint | Deskripsi |
|-------|-----------|------------|-----------|
| `id` | INT(11) | PRIMARY KEY, AUTO_INCREMENT | ID unik tipe toko |
| `name` | VARCHAR(255) | NOT NULL | Nama tipe toko |

---

### 12. `stores`
**Deskripsi**: Data toko.

| Kolom | Tipe Data | Constraint | Deskripsi |
|-------|-----------|------------|-----------|
| `id` | INT(11) | PRIMARY KEY, AUTO_INCREMENT | ID unik toko |
| `storeCode` | VARCHAR(255) | NULLABLE, UNIQUE | Kode toko |
| `tenantId` | INT(11) | NOT NULL, FOREIGN KEY | Referensi ke tabel `tenants` |
| `name` | VARCHAR(255) | NOT NULL | Nama toko |
| `address` | TEXT | NOT NULL | Alamat toko |
| `email` | VARCHAR(255) | NOT NULL | Email toko |
| `image` | VARCHAR(255) | NOT NULL | URL gambar toko |
| `provinceId` | INT(11) | NOT NULL, FOREIGN KEY | Referensi ke tabel `provinces` |
| `cityId` | INT(11) | NOT NULL, FOREIGN KEY | Referensi ke tabel `cities` |
| `districtId` | INT(11) | NOT NULL, FOREIGN KEY | Referensi ke tabel `districts` |
| `villageId` | INT(11) | NOT NULL, FOREIGN KEY | Referensi ke tabel `villages` |
| `storeTypeId` | INT(11) | NOT NULL, FOREIGN KEY | Referensi ke tabel `store_types` |
| `postalCode` | VARCHAR(12) | NOT NULL | Kode pos |
| `description` | TEXT | NOT NULL | Deskripsi toko |
| `phoneNumber` | VARCHAR(15) | NOT NULL | Nomor telepon toko |
| `logo` | VARCHAR(255) | NULLABLE | URL logo toko |
| `permitCertificate` | VARCHAR(255) | NULLABLE | Sertifikat izin usaha |
| `status` | ENUM | NOT NULL, DEFAULT 'ACTIVE' | Status: ACTIVE, INACTIVE |
| `createdAt` | DATETIME(3) | NOT NULL, DEFAULT CURRENT_TIMESTAMP(3) | Waktu pembuatan |
| `updatedAt` | DATETIME(3) | NOT NULL | Waktu update terakhir |

**Foreign Keys**:
- `tenantId` → `tenants.id`
- `provinceId` → `provinces.id`
- `cityId` → `cities.id`
- `districtId` → `districts.id`
- `villageId` → `villages.id`
- `storeTypeId` → `store_types.id`

---

### 13. `store_users`
**Deskripsi**: Relasi many-to-many antara users dan stores.

| Kolom | Tipe Data | Constraint | Deskripsi |
|-------|-----------|------------|-----------|
| `id` | BIGINT(20) | PRIMARY KEY, AUTO_INCREMENT | ID unik relasi |
| `userId` | BIGINT(20) | NOT NULL, FOREIGN KEY | Referensi ke tabel `users` |
| `storeId` | INT(11) | NOT NULL, FOREIGN KEY | Referensi ke tabel `stores` |
| `status` | ENUM | NOT NULL, DEFAULT 'ACTIVE' | Status: ACTIVE, INACTIVE |
| `createdAt` | DATETIME(3) | NOT NULL, DEFAULT CURRENT_TIMESTAMP(3) | Waktu pembuatan |
| `updatedAt` | DATETIME(3) | NOT NULL | Waktu update terakhir |

**Foreign Keys**:
- `userId` → `users.id`
- `storeId` → `stores.id`

---

### 14. `store_bills`
**Deskripsi**: Data tagihan toko.

| Kolom | Tipe Data | Constraint | Deskripsi |
|-------|-----------|------------|-----------|
| `id` | INT(11) | PRIMARY KEY, AUTO_INCREMENT | ID unik tagihan |
| `storeId` | INT(11) | NOT NULL, FOREIGN KEY | Referensi ke tabel `stores` |
| `name` | VARCHAR(255) | NOT NULL | Nama tagihan |
| `status` | ENUM | NOT NULL | Status: PAID, UNPAID |
| `price` | INT(11) | NOT NULL | Jumlah tagihan |
| `onDate` | DATETIME(3) | NOT NULL | Tanggal jatuh tempo |
| `createdAt` | DATETIME(3) | NOT NULL, DEFAULT CURRENT_TIMESTAMP(3) | Waktu pembuatan |
| `updatedAt` | DATETIME(3) | NOT NULL | Waktu update terakhir |
| `userId` | BIGINT(20) | NULLABLE, FOREIGN KEY | Referensi ke tabel `users` |

**Foreign Keys**:
- `storeId` → `stores.id`
- `userId` → `users.id` (ON DELETE SET NULL)

---

### 15. `product_categories`
**Deskripsi**: Master data kategori produk.

| Kolom | Tipe Data | Constraint | Deskripsi |
|-------|-----------|------------|-----------|
| `id` | INT(11) | PRIMARY KEY, AUTO_INCREMENT | ID unik kategori |
| `name` | VARCHAR(255) | NOT NULL | Nama kategori |
| `createdAt` | DATETIME(3) | NOT NULL, DEFAULT CURRENT_TIMESTAMP(3) | Waktu pembuatan |
| `updatedAt` | DATETIME(3) | NOT NULL | Waktu update terakhir |

---

### 16. `products`
**Deskripsi**: Data produk.

| Kolom | Tipe Data | Constraint | Deskripsi |
|-------|-----------|------------|-----------|
| `id` | BIGINT(20) | PRIMARY KEY, AUTO_INCREMENT | ID unik produk |
| `productCode` | VARCHAR(255) | NOT NULL, UNIQUE | Kode produk |
| `serialNumber` | INT(11) | NULLABLE | Nomor seri |
| `productCategoryId` | INT(11) | NOT NULL, FOREIGN KEY | Referensi ke tabel `product_categories` |
| `name` | VARCHAR(255) | NOT NULL | Nama produk |
| `description` | TEXT | NOT NULL | Deskripsi produk |
| `discount` | INT(11) | NULLABLE | Diskon produk (dalam persen atau nominal) |
| `basePrice` | BIGINT(20) | NOT NULL | Harga dasar |
| `imageUrl` | VARCHAR(255) | NOT NULL | URL gambar produk |
| `isActive` | TINYINT(1) | NOT NULL, DEFAULT 1 | Status aktif |
| `createdAt` | DATETIME(3) | NOT NULL, DEFAULT CURRENT_TIMESTAMP(3) | Waktu pembuatan |
| `updatedAt` | DATETIME(3) | NOT NULL | Waktu update terakhir |

**Foreign Keys**:
- `productCategoryId` → `product_categories.id`

---

### 17. `variant_types`
**Deskripsi**: Master data tipe varian produk (contoh: Ukuran, Warna, Rasa).

| Kolom | Tipe Data | Constraint | Deskripsi |
|-------|-----------|------------|-----------|
| `id` | BIGINT(20) | PRIMARY KEY, AUTO_INCREMENT | ID unik tipe varian |
| `name` | VARCHAR(255) | NOT NULL | Nama tipe varian |
| `scope` | ENUM | NOT NULL, DEFAULT 'GLOBAL' | Scope: GLOBAL (semua toko), STORE (spesifik toko) |
| `storeId` | INT(11) | NULLABLE | ID toko (untuk scope STORE) |
| `description` | TEXT | NULLABLE | Deskripsi |
| `isActive` | TINYINT(1) | NOT NULL, DEFAULT 1 | Status aktif |
| `createdAt` | DATETIME(3) | NOT NULL, DEFAULT CURRENT_TIMESTAMP(3) | Waktu pembuatan |
| `updatedAt` | DATETIME(3) | NOT NULL | Waktu update terakhir |

**Indexes**:
- INDEX: `variant_types_scope_storeId_idx` (`scope`, `storeId`)

---

### 18. `variant_options`
**Deskripsi**: Opsi varian produk (contoh: S, M, L untuk Ukuran).

| Kolom | Tipe Data | Constraint | Deskripsi |
|-------|-----------|------------|-----------|
| `id` | BIGINT(20) | PRIMARY KEY, AUTO_INCREMENT | ID unik opsi varian |
| `variantTypeId` | BIGINT(20) | NOT NULL, FOREIGN KEY | Referensi ke tabel `variant_types` |
| `name` | VARCHAR(255) | NOT NULL | Nama opsi varian |
| `additionalPrice` | BIGINT(20) | NOT NULL, DEFAULT 0 | Harga tambahan untuk opsi ini |
| `status` | ENUM | NOT NULL, DEFAULT 'ACTIVE' | Status: ACTIVE, INACTIVE |
| `createdAt` | DATETIME(3) | NOT NULL, DEFAULT CURRENT_TIMESTAMP(3) | Waktu pembuatan |
| `updatedAt` | DATETIME(3) | NOT NULL | Waktu update terakhir |

**Foreign Keys**:
- `variantTypeId` → `variant_types.id` (ON DELETE CASCADE)

**Indexes**:
- INDEX: `variant_options_variantTypeId_idx` (`variantTypeId`)

---

### 19. `product_variants`
**Deskripsi**: Varian produk dengan kombinasi opsi tertentu.

| Kolom | Tipe Data | Constraint | Deskripsi |
|-------|-----------|------------|-----------|
| `id` | BIGINT(20) | PRIMARY KEY, AUTO_INCREMENT | ID unik varian |
| `productId` | BIGINT(20) | NOT NULL, FOREIGN KEY | Referensi ke tabel `products` |
| `stock` | INT(11) | NOT NULL, DEFAULT 0 | Stok varian |
| `finalPrice` | BIGINT(20) | NOT NULL | Harga final (basePrice + additionalPrice) |
| `isActive` | TINYINT(1) | NOT NULL, DEFAULT 1 | Status aktif |
| `createdAt` | DATETIME(3) | NOT NULL, DEFAULT CURRENT_TIMESTAMP(3) | Waktu pembuatan |
| `updatedAt` | DATETIME(3) | NOT NULL | Waktu update terakhir |

**Foreign Keys**:
- `productId` → `products.id` (ON DELETE CASCADE)

**Indexes**:
- INDEX: `product_variants_productId_idx` (`productId`)

---

### 20. `product_variant_options`
**Deskripsi**: Relasi many-to-many antara product variants dan variant options.

| Kolom | Tipe Data | Constraint | Deskripsi |
|-------|-----------|------------|-----------|
| `id` | BIGINT(20) | PRIMARY KEY, AUTO_INCREMENT | ID unik relasi |
| `productVariantId` | BIGINT(20) | NOT NULL, FOREIGN KEY | Referensi ke tabel `product_variants` |
| `variantOptionId` | BIGINT(20) | NOT NULL, FOREIGN KEY | Referensi ke tabel `variant_options` |

**Foreign Keys**:
- `productVariantId` → `product_variants.id` (ON DELETE CASCADE)
- `variantOptionId` → `variant_options.id` (ON DELETE CASCADE)

**Indexes**:
- UNIQUE KEY: (`productVariantId`, `variantOptionId`)
- INDEX: `product_variant_options_productVariantId_idx` (`productVariantId`)
- INDEX: `product_variant_options_variantOptionId_idx` (`variantOptionId`)

---

### 21. `suppliers`
**Deskripsi**: Data supplier/pemasok.

| Kolom | Tipe Data | Constraint | Deskripsi |
|-------|-----------|------------|-----------|
| `id` | INT(11) | PRIMARY KEY, AUTO_INCREMENT | ID unik supplier |
| `name` | VARCHAR(255) | NOT NULL | Nama supplier |
| `tenantId` | INT(11) | NULLABLE, FOREIGN KEY | Referensi ke tabel `tenants` |
| `phoneNumber` | VARCHAR(20) | NOT NULL | Nomor telepon |
| `email` | VARCHAR(255) | NULLABLE | Email supplier |
| `address` | TEXT | NOT NULL | Alamat supplier |
| `createdAt` | DATETIME(3) | NOT NULL, DEFAULT CURRENT_TIMESTAMP(3) | Waktu pembuatan |
| `updatedAt` | DATETIME(3) | NOT NULL | Waktu update terakhir |

**Foreign Keys**:
- `tenantId` → `tenants.id` (ON DELETE SET NULL)

---

### 22. `supplier_products`
**Deskripsi**: Relasi produk yang disupply oleh supplier.

| Kolom | Tipe Data | Constraint | Deskripsi |
|-------|-----------|------------|-----------|
| `id` | INT(11) | PRIMARY KEY, AUTO_INCREMENT | ID unik relasi |
| `supplierId` | INT(11) | NOT NULL, FOREIGN KEY | Referensi ke tabel `suppliers` |
| `productId` | BIGINT(20) | NOT NULL, FOREIGN KEY | Referensi ke tabel `products` |
| `managedBy` | ENUM | NOT NULL | Dikelola oleh: ADMIN_TOKO, ADMIN_SCM, SUPER_SCM |

**Foreign Keys**:
- `supplierId` → `suppliers.id`
- `productId` → `products.id`

**Indexes**:
- UNIQUE KEY: (`supplierId`, `productId`)

---

### 23. `purchase_orders`
**Deskripsi**: Data order pembelian dari supplier.

| Kolom | Tipe Data | Constraint | Deskripsi |
|-------|-----------|------------|-----------|
| `id` | INT(11) | PRIMARY KEY, AUTO_INCREMENT | ID unik purchase order |
| `supplierId` | INT(11) | NOT NULL, FOREIGN KEY | Referensi ke tabel `suppliers` |
| `storeId` | INT(11) | NOT NULL, FOREIGN KEY | Referensi ke tabel `stores` |
| `totalAmount` | INT(11) | NOT NULL, DEFAULT 0 | Total jumlah order |
| `status` | ENUM | NOT NULL, DEFAULT 'PENDING' | Status: PENDING, SUCCESS, DELIVERED, CANCELED |
| `orderDate` | DATETIME(3) | NOT NULL, DEFAULT CURRENT_TIMESTAMP(3) | Tanggal order |

**Foreign Keys**:
- `supplierId` → `suppliers.id`
- `storeId` → `stores.id`

---

### 24. `purchase_order_items`
**Deskripsi**: Item detail dari purchase order.

| Kolom | Tipe Data | Constraint | Deskripsi |
|-------|-----------|------------|-----------|
| `id` | INT(11) | PRIMARY KEY, AUTO_INCREMENT | ID unik item |
| `purchaseOrderId` | INT(11) | NOT NULL, FOREIGN KEY | Referensi ke tabel `purchase_orders` |
| `productId` | BIGINT(20) | NOT NULL, FOREIGN KEY | Referensi ke tabel `products` |
| `quantity` | INT(11) | NOT NULL | Jumlah item |
| `capitalPrice` | INT(11) | NOT NULL | Harga modal per item |
| `expiredDate` | DATETIME(3) | NULLABLE | Tanggal kadaluarsa |

**Foreign Keys**:
- `purchaseOrderId` → `purchase_orders.id`
- `productId` → `products.id`

---

### 25. `warehouse_stocks`
**Deskripsi**: Stok produk di warehouse/gudang.

| Kolom | Tipe Data | Constraint | Deskripsi |
|-------|-----------|------------|-----------|
| `id` | INT(11) | PRIMARY KEY, AUTO_INCREMENT | ID unik stok |
| `productId` | BIGINT(20) | NOT NULL, FOREIGN KEY | Referensi ke tabel `products` |
| `storeId` | INT(11) | NOT NULL, FOREIGN KEY | Referensi ke tabel `stores` |
| `purchaseOrderId` | INT(11) | NULLABLE, FOREIGN KEY | Referensi ke tabel `purchase_orders` |
| `stock` | INT(11) | NOT NULL, DEFAULT 0 | Jumlah stok |
| `createdAt` | DATETIME(3) | NOT NULL, DEFAULT CURRENT_TIMESTAMP(3) | Waktu pembuatan |
| `updatedAt` | DATETIME(3) | NOT NULL | Waktu update terakhir |

**Foreign Keys**:
- `productId` → `products.id`
- `storeId` → `stores.id`
- `purchaseOrderId` → `purchase_orders.id` (ON DELETE SET NULL)

---

### 26. `customers`
**Deskripsi**: Data pelanggan.

| Kolom | Tipe Data | Constraint | Deskripsi |
|-------|-----------|------------|-----------|
| `id` | INT(11) | PRIMARY KEY, AUTO_INCREMENT | ID unik pelanggan |
| `storeId` | INT(11) | NOT NULL, FOREIGN KEY | Referensi ke tabel `stores` |
| `storeUserId` | BIGINT(20) | NOT NULL, FOREIGN KEY | Referensi ke tabel `store_users` |
| `name` | VARCHAR(255) | NOT NULL | Nama pelanggan |
| `phoneNumber` | VARCHAR(255) | NULLABLE | Nomor telepon |
| `status` | ENUM | NOT NULL, DEFAULT 'ACTIVE' | Status: ACTIVE, INACTIVE |
| `createdAt` | DATETIME(3) | NOT NULL, DEFAULT CURRENT_TIMESTAMP(3) | Waktu pembuatan |
| `updatedAt` | DATETIME(3) | NOT NULL | Waktu update terakhir |

**Foreign Keys**:
- `storeId` → `stores.id`
- `storeUserId` → `store_users.id`

---

### 27. `payment_methods`
**Deskripsi**: Master data metode pembayaran.

| Kolom | Tipe Data | Constraint | Deskripsi |
|-------|-----------|------------|-----------|
| `id` | INT(11) | PRIMARY KEY, AUTO_INCREMENT | ID unik metode pembayaran |
| `name` | VARCHAR(255) | NOT NULL | Nama metode pembayaran |
| `image` | VARCHAR(255) | NULLABLE | URL gambar/icon |
| `isCash` | ENUM | NOT NULL | Jenis: YES (tunai), NO (non-tunai) |
| `createdAt` | DATETIME(3) | NOT NULL, DEFAULT CURRENT_TIMESTAMP(3) | Waktu pembuatan |
| `updatedAt` | DATETIME(3) | NOT NULL | Waktu update terakhir |

---

### 28. `transactions`
**Deskripsi**: Data transaksi penjualan.

| Kolom | Tipe Data | Constraint | Deskripsi |
|-------|-----------|------------|-----------|
| `id` | BIGINT(20) | PRIMARY KEY, AUTO_INCREMENT | ID unik transaksi |
| `invoiceId` | VARCHAR(255) | NOT NULL, UNIQUE | Nomor invoice |
| `customerId` | INT(11) | NOT NULL, FOREIGN KEY | Referensi ke tabel `customers` |
| `userId` | BIGINT(20) | NOT NULL, FOREIGN KEY | Referensi ke tabel `users` (kasir) |
| `storeId` | INT(11) | NOT NULL, FOREIGN KEY | Referensi ke tabel `stores` |
| `paymentMethodId` | INT(11) | NOT NULL, FOREIGN KEY | Referensi ke tabel `payment_methods` |
| `totalAmount` | BIGINT(20) | NOT NULL | Total transaksi |
| `cash` | BIGINT(20) | NOT NULL | Uang tunai yang dibayarkan |
| `change` | BIGINT(20) | NOT NULL | Kembalian |
| `discount` | BIGINT(20) | NOT NULL, DEFAULT 0 | Diskon transaksi |
| `transactionDate` | DATETIME(3) | NOT NULL | Tanggal dan waktu transaksi |
| `status` | ENUM | NOT NULL, DEFAULT 'PENDING' | Status: PENDING, PAID, CANCELED |
| `createdAt` | DATETIME(3) | NOT NULL, DEFAULT CURRENT_TIMESTAMP(3) | Waktu pembuatan |
| `updatedAt` | DATETIME(3) | NOT NULL | Waktu update terakhir |

**Foreign Keys**:
- `customerId` → `customers.id`
- `userId` → `users.id`
- `storeId` → `stores.id`
- `paymentMethodId` → `payment_methods.id`

---

### 29. `transaction_details`
**Deskripsi**: Detail item dalam transaksi.

| Kolom | Tipe Data | Constraint | Deskripsi |
|-------|-----------|------------|-----------|
| `id` | INT(11) | PRIMARY KEY, AUTO_INCREMENT | ID unik detail transaksi |
| `transactionId` | BIGINT(20) | NOT NULL, FOREIGN KEY | Referensi ke tabel `transactions` |
| `productId` | BIGINT(20) | NOT NULL, FOREIGN KEY | Referensi ke tabel `products` |
| `productVariantId` | BIGINT(20) | NULLABLE, FOREIGN KEY | Referensi ke tabel `product_variants` |
| `quantity` | INT(11) | NOT NULL | Jumlah item |
| `unitPrice` | BIGINT(20) | NOT NULL | Harga satuan |
| `subtotal` | BIGINT(20) | NOT NULL | Subtotal (quantity × unitPrice) |
| `createdAt` | DATETIME(3) | NOT NULL, DEFAULT CURRENT_TIMESTAMP(3) | Waktu pembuatan |
| `updatedAt` | DATETIME(3) | NOT NULL | Waktu update terakhir |
| `variantOptionId` | BIGINT(20) | NULLABLE, FOREIGN KEY | Referensi ke tabel `variant_options` |

**Foreign Keys**:
- `transactionId` → `transactions.id`
- `productId` → `products.id`
- `productVariantId` → `product_variants.id` (ON DELETE SET NULL)
- `variantOptionId` → `variant_options.id` (ON DELETE SET NULL)

**Indexes**:
- INDEX: `transaction_details_transactionId_idx` (`transactionId`)
- INDEX: `transaction_details_productId_idx` (`productId`)
- INDEX: `transaction_details_productVariantId_idx` (`productVariantId`)

---

### 30. `taxes`
**Deskripsi**: Data pajak yang dikenakan pada transaksi.

| Kolom | Tipe Data | Constraint | Deskripsi |
|-------|-----------|------------|-----------|
| `id` | INT(11) | PRIMARY KEY, AUTO_INCREMENT | ID unik pajak |
| `transactionId` | VARCHAR(255) | NOT NULL, FOREIGN KEY | Referensi ke `transactions.invoiceId` |
| `name` | VARCHAR(255) | NOT NULL | Nama pajak |
| `rate` | INT(11) | NOT NULL | Tarif pajak (dalam persen) |

**Foreign Keys**:
- `transactionId` → `transactions.invoiceId`

---

### 31. `shifts`
**Deskripsi**: Data shift kerja kasir.

| Kolom | Tipe Data | Constraint | Deskripsi |
|-------|-----------|------------|-----------|
| `id` | INT(11) | PRIMARY KEY, AUTO_INCREMENT | ID unik shift |
| `shiftStart` | DATETIME(3) | NOT NULL | Waktu mulai shift |
| `shiftEnd` | DATETIME(3) | NULLABLE | Waktu selesai shift |
| `days` | ENUM | NOT NULL | Hari: MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY |
| `cashStatus` | ENUM | NOT NULL, DEFAULT 'PENDING' | Status kas: PENDING, OPENED, CLOSED |
| `userStoreId` | BIGINT(20) | NOT NULL, FOREIGN KEY | Referensi ke tabel `store_users` |
| `createdAt` | DATETIME(3) | NOT NULL, DEFAULT CURRENT_TIMESTAMP(3) | Waktu pembuatan |
| `updatedAt` | DATETIME(3) | NOT NULL | Waktu update terakhir |

**Foreign Keys**:
- `userStoreId` → `store_users.id`

---

### 32. `user_activity`
**Deskripsi**: Log aktivitas pengguna.

| Kolom | Tipe Data | Constraint | Deskripsi |
|-------|-----------|------------|-----------|
| `id` | INT(11) | PRIMARY KEY, AUTO_INCREMENT | ID unik aktivitas |
| `userId` | BIGINT(20) | NOT NULL, FOREIGN KEY | Referensi ke tabel `users` |
| `storeId` | INT(11) | NOT NULL, FOREIGN KEY | Referensi ke tabel `stores` |
| `action` | VARCHAR(255) | NOT NULL | Deskripsi aksi/aktivitas |
| `actionTime` | DATETIME(3) | NOT NULL | Waktu aktivitas |

**Foreign Keys**:
- `userId` → `users.id`
- `storeId` → `stores.id`

---

### 33. `_prisma_migrations`
**Deskripsi**: Tabel sistem Prisma untuk tracking migrasi database.

| Kolom | Tipe Data | Constraint | Deskripsi |
|-------|-----------|------------|-----------|
| `id` | VARCHAR(36) | PRIMARY KEY | ID unik migrasi |
| `checksum` | VARCHAR(64) | NOT NULL | Checksum migrasi |
| `finished_at` | DATETIME(3) | NULLABLE | Waktu selesai migrasi |
| `migration_name` | VARCHAR(255) | NOT NULL | Nama file migrasi |
| `logs` | TEXT | NULLABLE | Log migrasi |
| `rolled_back_at` | DATETIME(3) | NULLABLE | Waktu rollback |
| `started_at` | DATETIME(3) | NOT NULL, DEFAULT CURRENT_TIMESTAMP(3) | Waktu mulai migrasi |
| `applied_steps_count` | INT(10) UNSIGNED | NOT NULL, DEFAULT 0 | Jumlah langkah yang diterapkan |

---

### 34. `_producttosupplier`
**Deskripsi**: Tabel relasi many-to-many implisit Prisma antara products dan suppliers.

| Kolom | Tipe Data | Constraint | Deskripsi |
|-------|-----------|------------|-----------|
| `A` | BIGINT(20) | NOT NULL, FOREIGN KEY | Referensi ke `products.id` |
| `B` | INT(11) | NOT NULL, FOREIGN KEY | Referensi ke `suppliers.id` |

**Foreign Keys**:
- `A` → `products.id` (ON DELETE CASCADE)
- `B` → `suppliers.id` (ON DELETE CASCADE)

**Indexes**:
- UNIQUE KEY: (`A`, `B`)
- INDEX: `_ProductToSupplier_B_index` (`B`)

---

## Diagram Relasi Tabel

### Modul User & Authentication
```
users ←→ roles
users ←→ tokens
users ←→ tenant_users ←→ tenants
users ←→ store_users ←→ stores
```

### Modul Geografis
```
provinces → cities → districts → villages
↓           ↓         ↓           ↓
tenants, stores (menggunakan semua level geografis)
```

### Modul Toko
```
tenants → stores → store_users
stores → store_bills
stores → customers
stores ←→ store_types
```

### Modul Produk
```
product_categories → products → product_variants → product_variant_options
                                                         ↓
variant_types → variant_options ←────────────────────────
```

### Modul Supplier & Purchase
```
suppliers → purchase_orders → purchase_order_items → products
suppliers ←→ supplier_products ←→ products
purchase_orders → warehouse_stocks
```

### Modul Transaksi
```
stores → transactions → transaction_details → products
customers ────────↑              ↓
payment_methods ──┘              └→ product_variants
users (kasir) ────┘              └→ variant_options
transactions → taxes
```

### Modul Kasir
```
store_users → shifts → cashier_cash
```

### Modul Aktivitas
```
users → user_activity ← stores
```

---

## Catatan Penting

### Konvensi Penamaan
- Semua tabel menggunakan `snake_case`
- Primary key selalu bernama `id`
- Foreign key menggunakan format `{tableName}Id`
- Timestamp menggunakan `createdAt` dan `updatedAt`

### Timestamps
- Semua tabel utama memiliki kolom `createdAt` dan `updatedAt`
- Menggunakan presisi microsecond (3 digit)
- `createdAt` otomatis diisi dengan `CURRENT_TIMESTAMP(3)`

### Enum Values
- `status`: umumnya ACTIVE/INACTIVE
- `tenantStatus`: ACTIVE/INACTIVE
- `cashStatus`: PENDING/OPENED/CLOSED
- `transactionStatus`: PENDING/PAID/CANCELED
- `purchaseOrderStatus`: PENDING/SUCCESS/DELIVERED/CANCELED
- `tokenType`: ACCESS/REFRESH/RESET_PASSWORD/VERIFY_EMAIL
- `days`: MONDAY/TUESDAY/WEDNESDAY/THURSDAY/FRIDAY/SATURDAY/SUNDAY
- `scope`: GLOBAL/STORE
- `managedBy`: ADMIN_TOKO/ADMIN_SCM/SUPER_SCM
- `isCash`: YES/NO

### Cascade Rules
- Penghapusan `products` akan menghapus `product_variants` (CASCADE)
- Penghapusan `product_variants` akan menghapus `product_variant_options` (CASCADE)
- Penghapusan `variant_types` akan menghapus `variant_options` (CASCADE)
- Penghapusan `shifts` akan menghapus `cashier_cash` (CASCADE)
- Beberapa relasi menggunakan SET NULL saat parent dihapus

### Indexes
- Foreign keys memiliki index otomatis
- Terdapat composite index untuk query optimasi:
  - `cashier_cash`: (`shiftId`, `userId`) dan (`storeId`, `createdAt`)
  - `variant_types`: (`scope`, `storeId`)
  - `transaction_details`: beberapa index untuk query performa

---

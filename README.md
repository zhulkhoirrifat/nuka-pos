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
26. `variants` - Varian produk (misalnya ukuran, warna).
27. `variant_options` - Opsi varian (misalnya small, medium).
28. `variant_option_variants` - Relasi antara varian dan opsi varian.
29. `variant_types` - Tipe varian (misalnya ukuran, rasa).
30. `villages` - Data desa/kelurahan, terkait dengan kecamatan.
31. `warehouse_stocks` - Stok produk di gudang toko.
32. `_prisma_migrations` - Log migrasi database menggunakan Prisma.
33. `_producttosupplier` - Tabel junction untuk relasi many-to-many antara produk dan pemasok.

## Detail Struktur Tabel

### 1. Tabel `cities`
- Deskripsi: Menyimpan informasi kota/kabupaten.
- Kolom:
  - id: int(11), PRIMARY KEY, AUTO_INCREMENT
  - provinceId: int(11), NOT NULL (foreign key ke `provinces.id`)
  - name: varchar(255), NOT NULL
  - createdAt: datetime(3), NOT NULL, DEFAULT current_timestamp(3)
  - updatedAt: datetime(3), NOT NULL
- Indeks: PRIMARY (`id`), KEY (`provinceId`)

### 2. Tabel `customers`
- Deskripsi: Data pelanggan.
- Kolom:
  - id: int(11), PRIMARY KEY, AUTO_INCREMENT
  - storeId: int(11), NOT NULL (foreign key ke `stores.id`)
  - storeUserId: bigint(20), NOT NULL (foreign key ke `store_users.id`)
  - name: varchar(255), NOT NULL
  - phoneNumber: varchar(255), DEFAULT NULL
  - status: enum('ACTIVE','INACTIVE'), NOT NULL, DEFAULT 'ACTIVE'
  - createdAt: datetime(3), NOT NULL, DEFAULT current_timestamp(3)
  - updatedAt: datetime(3), NOT NULL
- Indeks: PRIMARY (`id`), KEY (`storeId`), KEY (`storeUserId`)

### 3. Tabel `districts`
- Deskripsi: Menyimpan informasi kecamatan.
- Kolom:
  - id: int(11), PRIMARY KEY, AUTO_INCREMENT
  - cityId: int(11), NOT NULL (foreign key ke `cities.id`)
  - name: varchar(255), NOT NULL
  - createdAt: datetime(3), NOT NULL, DEFAULT current_timestamp(3)
  - updatedAt: datetime(3), NOT NULL
- Indeks: PRIMARY (`id`), KEY (`cityId`)

### 4. Tabel `payment_methods`
- Deskripsi: Metode pembayaran.
- Kolom:
  - id: int(11), PRIMARY KEY, AUTO_INCREMENT
  - name: varchar(255), NOT NULL
  - image: varchar(255), DEFAULT NULL
  - isCash: enum('YES','NO'), NOT NULL
  - createdAt: datetime(3), NOT NULL, DEFAULT current_timestamp(3)
  - updatedAt: datetime(3), NOT NULL
- Indeks: PRIMARY (`id`)

### 5. Tabel `products`
- Deskripsi: Data produk.
- Kolom:
  - id: bigint(20), PRIMARY KEY, AUTO_INCREMENT
  - productCode: varchar(255), NOT NULL, UNIQUE
  - serialNumber: int(11), DEFAULT NULL
  - productCategoryId: int(11), NOT NULL (foreign key ke `product_categories.id`)
  - name: varchar(255), NOT NULL
  - description: text, NOT NULL
  - discount: int(11), DEFAULT NULL
  - basePrice: bigint(20), NOT NULL
  - imageUrl: varchar(255), NOT NULL
  - isActive: tinyint(1), NOT NULL, DEFAULT 1
  - createdAt: datetime(3), NOT NULL, DEFAULT current_timestamp(3)
  - updatedAt: datetime(3), NOT NULL
- Indeks: PRIMARY (`id`), UNIQUE (`productCode`), KEY (`productCategoryId`)

### 6. Tabel `product_categories`
- Deskripsi: Kategori produk.
- Kolom:
  - id: int(11), PRIMARY KEY, AUTO_INCREMENT
  - name: varchar(255), NOT NULL
  - createdAt: datetime(3), NOT NULL, DEFAULT current_timestamp(3)
  - updatedAt: datetime(3), NOT NULL
- Indeks: PRIMARY (`id`)

### 7. Tabel `provinces`
- Deskripsi: Menyimpan informasi provinsi.
- Kolom:
  - id: int(11), PRIMARY KEY, AUTO_INCREMENT
  - name: varchar(255), NOT NULL
  - createdAt: datetime(3), NOT NULL, DEFAULT current_timestamp(3)
  - updatedAt: datetime(3), NOT NULL
- Indeks: PRIMARY (`id`)

### 8. Tabel `purchase_orders`
- Deskripsi: Pesanan pembelian.
- Kolom:
  - id: int(11), PRIMARY KEY, AUTO_INCREMENT
  - supplierId: int(11), NOT NULL (foreign key ke `suppliers.id`)
  - storeId: int(11), NOT NULL (foreign key ke `stores.id`)
  - totalAmount: int(11), NOT NULL, DEFAULT 0
  - status: enum('PENDING','SUCCESS','DELIVERED','CANCELED'), NOT NULL, DEFAULT 'PENDING'
  - orderDate: datetime(3), NOT NULL, DEFAULT current_timestamp(3)
- Indeks: PRIMARY (`id`), KEY (`supplierId`), KEY (`storeId`)

### 9. Tabel `purchase_order_items`
- Deskripsi: Item dalam pesanan pembelian.
- Kolom:
  - id: int(11), PRIMARY KEY, AUTO_INCREMENT
  - purchaseOrderId: int(11), NOT NULL (foreign key ke `purchase_orders.id`)
  - productId: bigint(20), NOT NULL (foreign key ke `products.id`)
  - quantity: int(11), NOT NULL
  - capitalPrice: int(11), NOT NULL
  - expiredDate: datetime(3), DEFAULT NULL
- Indeks: PRIMARY (`id`), KEY (`purchaseOrderId`), KEY (`productId`)

### 10. Tabel `roles`
- Deskripsi: Peran pengguna.
- Kolom:
  - id: int(11), PRIMARY KEY, AUTO_INCREMENT
  - roleName: varchar(50), NOT NULL, UNIQUE
- Indeks: PRIMARY (`id`), UNIQUE (`roleName`)

### 11. Tabel `shifts`
- Deskripsi: Jadwal shift.
- Kolom:
  - id: int(11), PRIMARY KEY, AUTO_INCREMENT
  - shiftStart: datetime(3), NOT NULL
  - shiftEnd: datetime(3), NOT NULL
  - days: enum('MONDAY','TUESDAY','WEDNESDAY','THURSDAY','FRIDAY','SATURDAY','SUNDAY'), NOT NULL
  - userStoreId: bigint(20), NOT NULL (foreign key ke `store_users.id`)
  - createdAt: datetime(3), NOT NULL, DEFAULT current_timestamp(3)
  - updatedAt: datetime(3), NOT NULL
- Indeks: PRIMARY (`id`), KEY (`userStoreId`)

### 12. Tabel `stores`
- Deskripsi: Data toko.
- Kolom:
  - id: int(11), PRIMARY KEY, AUTO_INCREMENT
  - storeCode: varchar(255), DEFAULT NULL, UNIQUE
  - tenantId: int(11), NOT NULL (foreign key ke `tenants.id`)
  - name: varchar(255), NOT NULL
  - address: text, NOT NULL
  - email: varchar(255), NOT NULL
  - image: varchar(255), NOT NULL
  - provinceId: int(11), NOT NULL (foreign key ke `provinces.id`)
  - cityId: int(11), NOT NULL (foreign key ke `cities.id`)
  - districtId: int(11), NOT NULL (foreign key ke `districts.id`)
  - villageId: int(11), NOT NULL (foreign key ke `villages.id`)
  - storeTypeId: int(11), NOT NULL (foreign key ke `store_types.id`)
  - postalCode: varchar(12), NOT NULL
  - description: text, NOT NULL
  - phoneNumber: varchar(15), NOT NULL
  - logo: varchar(255), DEFAULT NULL
  - permitCertificate: varchar(255), DEFAULT NULL
  - status: enum('ACTIVE','INACTIVE'), NOT NULL, DEFAULT 'ACTIVE'
  - createdAt: datetime(3), NOT NULL, DEFAULT current_timestamp(3)
  - updatedAt: datetime(3), NOT NULL
- Indeks: PRIMARY (`id`), UNIQUE (`storeCode`), KEY (`provinceId`), KEY (`cityId`), KEY (`districtId`), KEY (`villageId`), KEY (`storeTypeId`), KEY (`tenantId`)

### 13. Tabel `store_bills`
- Deskripsi: Tagihan toko.
- Kolom:
  - id: int(11), PRIMARY KEY, AUTO_INCREMENT
  - storeId: int(11), NOT NULL (foreign key ke `stores.id`)
  - name: varchar(255), NOT NULL
  - status: enum('PAID','UNPAID'), NOT NULL
  - price: int(11), NOT NULL
  - onDate: datetime(3), NOT NULL
  - createdAt: datetime(3), NOT NULL, DEFAULT current_timestamp(3)
  - updatedAt: datetime(3), NOT NULL
  - userId: bigint(20), DEFAULT NULL (foreign key ke `users.id`)
- Indeks: PRIMARY (`id`), KEY (`storeId`), KEY (`userId`)

### 14. Tabel `store_types`
- Deskripsi: Tipe toko.
- Kolom:
  - id: int(11), PRIMARY KEY, AUTO_INCREMENT
  - name: varchar(255), NOT NULL
- Indeks: PRIMARY (`id`)

### 15. Tabel `store_users`
- Deskripsi: Relasi pengguna dan toko.
- Kolom:
  - id: bigint(20), PRIMARY KEY, AUTO_INCREMENT
  - userId: bigint(20), NOT NULL (foreign key ke `users.id`)
  - storeId: int(11), NOT NULL (foreign key ke `stores.id`)
  - status: enum('ACTIVE','INACTIVE'), NOT NULL, DEFAULT 'ACTIVE'
  - createdAt: datetime(3), NOT NULL, DEFAULT current_timestamp(3)
  - updatedAt: datetime(3), NOT NULL
- Indeks: PRIMARY (`id`), KEY (`userId`), KEY (`storeId`)

### 16. Tabel `suppliers`
- Deskripsi: Data pemasok.
- Kolom:
  - id: int(11), PRIMARY KEY, AUTO_INCREMENT
  - name: varchar(255), NOT NULL
  - tenantId: int(11), DEFAULT NULL (foreign key ke `tenants.id`)
  - phoneNumber: varchar(20), NOT NULL
  - email: varchar(255), DEFAULT NULL
  - address: text, NOT NULL
  - createdAt: datetime(3), NOT NULL, DEFAULT current_timestamp(3)
  - updatedAt: datetime(3), NOT NULL
- Indeks: PRIMARY (`id`), KEY (`tenantId`)

### 17. Tabel `supplier_products`
- Deskripsi: Produk dari pemasok.
- Kolom:
  - id: int(11), PRIMARY KEY, AUTO_INCREMENT
  - supplierId: int(11), NOT NULL (foreign key ke `suppliers.id`)
  - productId: bigint(20), NOT NULL (foreign key ke `products.id`)
  - managedBy: enum('ADMIN_SCM','SUPER_SCM'), NOT NULL
- Indeks: PRIMARY (`id`), KEY (`supplierId`), KEY (`productId`)

### 18. Tabel `taxes`
- Deskripsi: Pajak transaksi.
- Kolom:
  - id: int(11), PRIMARY KEY, AUTO_INCREMENT
  - transactionId: varchar(255), NOT NULL (foreign key ke `transactions.invoiceId`)
  - name: varchar(255), NOT NULL
  - rate: int(11), NOT NULL
- Indeks: PRIMARY (`id`), KEY (`transactionId`)

### 19. Tabel `tenants`
- Deskripsi: Data tenant.
- Kolom:
  - id: int(11), PRIMARY KEY, AUTO_INCREMENT
  - userId: bigint(20), NOT NULL, UNIQUE (foreign key ke `users.id`)
  - name: varchar(255), NOT NULL
  - npwp: varchar(255), DEFAULT NULL
  - npwpImage: varchar(255), DEFAULT NULL
  - address: text, NOT NULL
  - provinceId: int(11), NOT NULL (foreign key ke `provinces.id`)
  - cityId: int(11), NOT NULL (foreign key ke `cities.id`)
  - districtId: int(11), NOT NULL (foreign key ke `districts.id`)
  - villageId: int(11), NOT NULL (foreign key ke `villages.id`)
  - postalCode: varchar(12), NOT NULL
  - status: enum('ACTIVE','INACTIVE'), NOT NULL, DEFAULT 'ACTIVE'
  - createdAt: datetime(3), NOT NULL, DEFAULT current_timestamp(3)
  - updatedAt: datetime(3), NOT NULL
- Indeks: PRIMARY (`id`), UNIQUE (`userId`), KEY (`provinceId`), KEY (`cityId`), KEY (`districtId`), KEY (`villageId`)

### 20. Tabel `tenant_users`
- Deskripsi: Relasi pengguna dan tenant.
- Kolom:
  - id: bigint(20), PRIMARY KEY, AUTO_INCREMENT
  - userId: bigint(20), NOT NULL (foreign key ke `users.id`)
  - tenantId: int(11), NOT NULL (foreign key ke `tenants.id`)
  - status: enum('ACTIVE','INACTIVE'), NOT NULL, DEFAULT 'ACTIVE'
  - createdAt: datetime(3), NOT NULL, DEFAULT current_timestamp(3)
  - updatedAt: datetime(3), NOT NULL
- Indeks: PRIMARY (`id`), KEY (`userId`), KEY (`tenantId`)

### 21. Tabel `tokens`
- Deskripsi: Token autentikasi.
- Kolom:
  - id: int(11), PRIMARY KEY, AUTO_INCREMENT
  - userId: bigint(20), NOT NULL (foreign key ke `users.id`)
  - token: text, NOT NULL
  - type: enum('ACCESS','REFRESH','RESET_PASSWORD','VERIFY_EMAIL'), NOT NULL
  - expires: datetime(3), NOT NULL
  - blacklisted: tinyint(1), NOT NULL, DEFAULT 0
  - createdAt: datetime(3), NOT NULL, DEFAULT current_timestamp(3)
- Indeks: PRIMARY (`id`), KEY (`userId`)

### 22. Tabel `transactions`
- Deskripsi: Transaksi penjualan.
- Kolom:
  - id: bigint(20), PRIMARY KEY, AUTO_INCREMENT
  - invoiceId: varchar(255), NOT NULL, UNIQUE
  - customerId: int(11), NOT NULL (foreign key ke `customers.id`)
  - userId: bigint(20), NOT NULL (foreign key ke `users.id`)
  - storeId: int(11), NOT NULL (foreign key ke `stores.id`)
  - paymentMethodId: int(11), NOT NULL (foreign key ke `payment_methods.id`)
  - totalAmount: bigint(20), NOT NULL
  - cash: bigint(20), NOT NULL
  - change: bigint(20), NOT NULL
  - discount: bigint(20), NOT NULL, DEFAULT 0
  - transactionDate: datetime(3), NOT NULL
  - status: enum('PENDING','PAID','CANCELED'), NOT NULL, DEFAULT 'PENDING'
  - createdAt: datetime(3), NOT NULL, DEFAULT current_timestamp(3)
  - updatedAt: datetime(3), NOT NULL
- Indeks: PRIMARY (`id`), UNIQUE (`invoiceId`), KEY (`customerId`), KEY (`userId`), KEY (`storeId`), KEY (`paymentMethodId`)

### 23. Tabel `transaction_details`
- Deskripsi: Detail item transaksi.
- Kolom:
  - id: int(11), PRIMARY KEY, AUTO_INCREMENT
  - transactionId: bigint(20), NOT NULL (foreign key ke `transactions.id`)
  - productId: bigint(20), NOT NULL (foreign key ke `products.id`)
  - variantId: bigint(20), DEFAULT NULL (foreign key ke `variants.id`)
  - quantity: int(11), NOT NULL
  - subtotal: bigint(20), NOT NULL
  - createdAt: datetime(3), NOT NULL, DEFAULT current_timestamp(3)
  - updatedAt: datetime(3), NOT NULL
  - variantOptionId: bigint(20), DEFAULT NULL (foreign key ke `variant_options.id`)
- Indeks: PRIMARY (`id`), KEY (`transactionId`), KEY (`productId`), KEY (`variantId`), KEY (`variantOptionId`)

### 24. Tabel `users`
- Deskripsi: Data pengguna.
- Kolom:
  - id: bigint(20), PRIMARY KEY, AUTO_INCREMENT
  - name: varchar(255), DEFAULT NULL
  - email: varchar(255), NOT NULL, UNIQUE
  - password: char(60), NOT NULL
  - imageUrl: varchar(255), DEFAULT NULL
  - phoneNumber: varchar(15), NOT NULL
  - isEmailVerified: tinyint(1), NOT NULL, DEFAULT 0
  - idCard: varchar(255), DEFAULT NULL
  - idCardImage: varchar(255), DEFAULT NULL
  - npwp: varchar(255), DEFAULT NULL
  - npwpImage: varchar(255), DEFAULT NULL
  - googleId: varchar(191), DEFAULT NULL, UNIQUE
  - roleId: int(11), NOT NULL (foreign key ke `roles.id`)
  - createdAt: datetime(3), NOT NULL, DEFAULT current_timestamp(3)
  - verifiedAt: datetime(3), DEFAULT NULL
  - updatedAt: datetime(3), NOT NULL
- Indeks: PRIMARY (`id`), UNIQUE (`email`), UNIQUE (`googleId`), KEY (`roleId`)

### 25. Tabel `user_activity`
- Deskripsi: Aktivitas pengguna.
- Kolom:
  - id: int(11), PRIMARY KEY, AUTO_INCREMENT
  - userId: bigint(20), NOT NULL (foreign key ke `users.id`)
  - storeId: int(11), NOT NULL (foreign key ke `stores.id`)
  - action: varchar(255), NOT NULL
  - actionTime: datetime(3), NOT NULL
- Indeks: PRIMARY (`id`), KEY (`userId`), KEY (`storeId`)

### 26. Tabel `variants`
- Deskripsi: Varian produk.
- Kolom:
  - id: bigint(20), PRIMARY KEY, AUTO_INCREMENT
  - productId: bigint(20), NOT NULL (foreign key ke `products.id`)
  - createdAt: datetime(3), NOT NULL, DEFAULT current_timestamp(3)
  - updatedAt: datetime(3), NOT NULL
- Indeks: PRIMARY (`id`), KEY (`productId`)

### 27. Tabel `variant_options`
- Deskripsi: Opsi varian.
- Kolom:
  - id: bigint(20), PRIMARY KEY, AUTO_INCREMENT
  - variantTypeId: bigint(20), NOT NULL (foreign key ke `variant_types.id`)
  - name: varchar(255), NOT NULL
  - price: bigint(20), NOT NULL, DEFAULT 0
  - status: enum('ACTIVE','INACTIVE'), NOT NULL
  - createdAt: datetime(3), NOT NULL, DEFAULT current_timestamp(3)
  - updatedAt: datetime(3), NOT NULL
  - variantId: bigint(20), DEFAULT NULL
- Indeks: PRIMARY (`id`), KEY (`variantTypeId`)

### 28. Tabel `variant_option_variants`
- Deskripsi: Relasi varian dan opsi varian.
- Kolom:
  - id: bigint(20), PRIMARY KEY, AUTO_INCREMENT
  - variantId: bigint(20), NOT NULL (foreign key ke `variants.id`)
  - variantOptionId: bigint(20), NOT NULL (foreign key ke `variant_options.id`)
- Indeks: PRIMARY (`id`), KEY (`variantId`), KEY (`variantOptionId`)

### 29. Tabel `variant_types`
- Deskripsi: Tipe varian.
- Kolom:
  - id: bigint(20), PRIMARY KEY, AUTO_INCREMENT
  - name: varchar(255), NOT NULL
  - createdAt: datetime(3), NOT NULL, DEFAULT current_timestamp(3)
  - updatedAt: datetime(3), NOT NULL
- Indeks: PRIMARY (`id`)

### 30. Tabel `villages`
- Deskripsi: Menyimpan informasi desa/kelurahan.
- Kolom:
  - id: int(11), PRIMARY KEY, AUTO_INCREMENT
  - districtId: int(11), NOT NULL (foreign key ke `districts.id`)
  - name: varchar(255), NOT NULL
  - createdAt: datetime(3), NOT NULL, DEFAULT current_timestamp(3)
  - updatedAt: datetime(3), NOT NULL
- Indeks: PRIMARY (`id`), KEY (`districtId`)

### 31. Tabel `warehouse_stocks`
- Deskripsi: Stok gudang.
- Kolom:
  - id: int(11), PRIMARY KEY, AUTO_INCREMENT
  - productId: bigint(20), NOT NULL (foreign key ke `products.id`)
  - storeId: int(11), NOT NULL (foreign key ke `stores.id`)
  - purchaseOrderId: int(11), DEFAULT NULL (foreign key ke `purchase_orders.id`)
  - stock: int(11), NOT NULL, DEFAULT 0
  - createdAt: datetime(3), NOT NULL, DEFAULT current_timestamp(3)
  - updatedAt: datetime(3), NOT NULL
- Indeks: PRIMARY (`id`), KEY (`productId`), KEY (`storeId`), KEY (`purchaseOrderId`)

### 32. Tabel `_prisma_migrations`
- Deskripsi: Log migrasi Prisma.
- Kolom:
  - id: varchar(36), PRIMARY KEY
  - checksum: varchar(64), NOT NULL
  - finished_at: datetime(3), DEFAULT NULL
  - migration_name: varchar(255), NOT NULL
  - logs: text, DEFAULT NULL
  - rolled_back_at: datetime(3), DEFAULT NULL
  - started_at: datetime(3), NOT NULL, DEFAULT current_timestamp(3)
  - applied_steps_count: int(10) UNSIGNED, NOT NULL, DEFAULT 0
- Indeks: PRIMARY (`id`)

### 33. Tabel `_producttosupplier`
- Deskripsi: Tabel junction untuk relasi many-to-many antara `products` dan `suppliers`.
- Kolom:
  - A: bigint(20), NOT NULL (foreign key ke `products.id`)
  - B: int(11), NOT NULL (foreign key ke `suppliers.id`)
- Indeks: UNIQUE (`A`, `B`), KEY (`B`)

## Relasi Antar Tabel (Foreign Keys)
Database ini memiliki banyak relasi untuk menjaga integritas data. Semua foreign keys menggunakan ON UPDATE CASCADE, dan beberapa menggunakan ON DELETE SET NULL atau CASCADE. Berikut ringkasan utama relasi:

- Lokasi Geografis: `provinces` → `cities` → `districts` → `villages`.
- Toko dan Tenant: `tenants` memiliki banyak `stores`. `stores` terkait dengan lokasi geografis dan `store_types`.
- Pengguna: `users` terkait dengan `roles`, `tenants` (via `tenant_users`), dan `stores` (via `store_users`).
- Produk: `products` terkait dengan `product_categories`, memiliki `variants` (yang terkait dengan `variant_types` dan `variant_options` via `variant_option_variants`).
- Pemasok dan Pembelian: `suppliers` terkait dengan `tenants`. `products` terkait dengan `suppliers` via `_producttosupplier`. `purchase_orders` terkait dengan `suppliers` dan `stores`, dengan detail di `purchase_order_items`.
- Transaksi: `transactions` terkait dengan `customers`, `users`, `stores`, `payment_methods`. Detail di `transaction_details` (terkait dengan `products`, `variants`). Pajak di `taxes`.
- Stok dan Aktivitas: `warehouse_stocks` terkait dengan `products`, `stores`, `purchase_orders`. `user_activity` dan `shifts` terkait dengan `users` dan `stores`.
- Autentikasi: `tokens` terkait dengan `users`.

Relasi ini membentuk model data yang kompleks untuk manajemen rantai pasok, penjualan, dan operasional toko.

## Catatan Tambahan
- Migrasi: Tabel `_prisma_migrations` menunjukkan penggunaan Prisma ORM, dengan satu migrasi tercatat pada 28 Mei 2025.
- Keamanan: Password disimpan sebagai char(60), kemungkinan hashed (misalnya bcrypt).
- Optimasi: Gunakan indeks untuk query cepat, terutama pada foreign keys.
- Pembaruan: Dokumentasi ini berdasarkan dump SQL yang diberikan. Jika ada perubahan, dump ulang database untuk update.

# 🗂️ Ubuntu Samba File Sharing Guide

Panduan ini menjelaskan langkah-langkah membuat folder sharing di Ubuntu menggunakan **Samba**, agar bisa diakses langsung dari komputer Windows.

---

## 📦 1. Install Samba

```bash
sudo apt update
sudo apt install samba -y
````

---

## 📁 2. Buat Folder untuk Sharing

Gunakan direktori `/srv/samba/shared` agar lebih terstruktur:

```bash
sudo mkdir -p /srv/samba/shared
sudo chmod -R 777 /srv/samba/shared
sudo chown -R nobody:nogroup /srv/samba/shared
```

---

## 🧰 3. Backup Konfigurasi Asli Samba

```bash
sudo cp /etc/samba/smb.conf /etc/samba/smb.conf.backup
```

---

## ⚙️ 4. Konfigurasi Samba

Edit file konfigurasi:

```bash
sudo nano /etc/samba/smb.conf
```

Hapus seluruh isi, lalu masukkan konfigurasi berikut:

```ini
[global]
   workgroup = WORKGROUP
   server string = Samba Server
   netbios name = ubuntu-share
   security = user
   map to guest = Bad User
   dns proxy = no
   usershare allow guests = yes
   guest account = nobody

   # Nonaktifkan enkripsi SMB untuk kompatibilitas Windows lama
   smb encrypt = disabled
   ntlm auth = yes

[SharedFolder]
   path = /srv/samba/shared
   browseable = yes
   writable = yes
   guest ok = yes
   read only = no
   force user = nobody
   create mask = 0777
   directory mask = 0777
   comment = Folder Sharing Ubuntu
```

Simpan dengan `Ctrl + O`, lalu keluar dengan `Ctrl + X`.

---

## 🔁 5. Restart dan Aktifkan Samba

```bash
sudo systemctl restart smbd nmbd
sudo systemctl enable smbd nmbd
```

Periksa status layanan:

```bash
sudo systemctl status smbd nmbd
```

---

## 🧩 6. Tes dari Ubuntu

List semua share:

```bash
smbclient -L localhost -N
```

Coba akses folder:

```bash
smbclient //localhost/SharedFolder -U guest -N
```

Jika berhasil, Anda akan melihat prompt `smb: \>`.

---

## 💻 7. Akses dari Windows

1. Buka **File Explorer**
2. Ketik di address bar:

   ```
   \\<IP_UBUNTU>\SharedFolder
   ```

   Contoh:

   ```
   \\192.168.1.171\SharedFolder
   ```
3. Pilih **Connect as Guest** jika diminta login.

Atau gunakan Command Prompt:

```cmd
net use \\192.168.1.171\SharedFolder /user:guest ""
```

---

## 🧾 8. Simpan Konfigurasi yang Berhasil

Jika Samba sudah berfungsi dengan baik, simpan konfigurasi kerja:

```bash
sudo cp /etc/samba/smb.conf /etc/samba/smb.conf.working
```

Jika nanti terjadi error setelah update sistem:

```bash
sudo cp /etc/samba/smb.conf.working /etc/samba/smb.conf
sudo systemctl restart smbd nmbd
```

---

## ⚡ Catatan Tambahan

* Pastikan firewall membuka port: **137–139 (NetBIOS)** dan **445 (SMB)**
* Windows lama mungkin butuh fitur **SMB1** diaktifkan.
* Folder sharing ini bisa digunakan antar Linux & Windows tanpa perlu akun login.

---

📘 **Lisensi:** CC BY-SA 4.0
📅 **Terakhir diperbarui:** November 2025
✍️ **Dibuat oleh:** Tim Pengembang IT Internal

```

---

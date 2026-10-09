# Set-Up-n8n

> Hal Ini Memerlukan Wsl Dengan Distro Ubuntu

```bash
wsl --install ubuntu
```

## Jalur A: Docker Desktop (paling gampang)
Pasang Docker Desktop untuk Windows dari docker.com.
Saat instalasi, pastikan opsi Use WSL 2 based engine aktif.
Buka Docker Desktop → Settings → Resources → WSL Integration, lalu nyalakan toggle untuk distro Ubuntu lo.
Buka terminal Ubuntu (WSL) dan tes:

```bash
docker compose version
docker run hello-world
```
Di jalur ini lo tidak perlu pasang Docker Engine di dalam Ubuntu. Docker Desktop yang menyediakan.

## Abis Itu Ikuti Langkah Ini 

Mantap, Docker Desktop sudah jalan. Sekarang kita lanjut ke n8n-nya.

## Langkah 1: Pastikan Docker beres

Di terminal Ubuntu (WSL):
```bash
docker compose version
docker run hello-world
```
Kalau dua-duanya keluar normal, lanjut.

## Langkah 2: Bikin folder proyek

```bash
mkdir -p ~/n8n && cd ~/n8n
```
Simpan folder di dalam filesystem Ubuntu (`~/`), **jangan** di `/mnt/c/...`, karena jauh lebih cepat.

## Langkah 3: Bikin `docker-compose.yml`

```bash
nano docker-compose.yml
```
Isi:
```yaml
services:
  n8n:
    image: n8nio/n8n:latest
    container_name: n8n
    restart: unless-stopped
    ports:
      - "5678:5678"
    environment:
      - GENERIC_TIMEZONE=Asia/Jayapura
      - TZ=Asia/Jayapura
      - N8N_SECURE_COOKIE=false
    volumes:
      - n8n_data:/home/node/.n8n

volumes:
  n8n_data:
```
Simpan: `Ctrl+O`, Enter, lalu `Ctrl+X`.

Catatan: `N8N_SECURE_COOKIE=false` dipakai supaya n8n bisa dibuka lewat `http://` biasa (tanpa HTTPS), misalnya dari komputer sebelah. Untuk dipakai di internet, jangan dimatikan.

## Langkah 4: Jalankan

```bash
docker compose up -d
```
Pertama kali akan mengunduh image, jadi tunggu sebentar. Cek statusnya:
```bash
docker compose ps
docker compose logs -f n8n
```
(`Ctrl+C` untuk keluar dari log.)

## Langkah 5: Buka n8n

Di browser Windows: **http://localhost:5678**

Pertama kali akan diminta bikin akun owner (email dan password). Itu akun lokal di n8n kamu sendiri.

## Langkah 6: Akses dari komputer sebelah (opsional)

1. Cek IP Windows kamu: `ipconfig` di PowerShell, cari IPv4 adapter WiFi/Ethernet.
2. Buka firewall (PowerShell sebagai Admin):
```powershell
New-NetFirewallRule -DisplayName "n8n" -Direction Inbound -Protocol TCP -LocalPort 5678 -Action Allow
```
3. Di komputer sebelah buka `http://IP-WINDOWS-KAMU:5678`.

Dengan Docker Desktop, port yang di-publish biasanya sudah bisa diakses dari jaringan tanpa setting tambahan. Kalau belum bisa, baru coba mirrored networking yang tadi.

## Perintah yang sering dipakai

```bash
docker compose stop        # hentikan
docker compose start       # nyalakan lagi
docker compose down        # hapus container (data di volume tetap aman)
docker compose pull && docker compose up -d   # update n8n
```

Kalau sudah sampai Langkah 5 dan n8n kebuka, kabari ya. Kalau ada error di langkah mana pun, kirim pesan errornya.

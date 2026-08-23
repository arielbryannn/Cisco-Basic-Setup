# Cisco Basic Setup
Cisco Initial Switch Hardening & Management IP
Dokumentasi konfigurasi awal (initial setup) dan pengamanan perangkat Cisco Switch menggunakan CLI (Command Line Interface).

### Topologi Jaringan
<p align="center">
<img width="509" height="308" alt="topology" src="https://github.com/user-attachments/assets/0a6fe9ed-3679-4feb-a590-73470fde4b88" />
</p>

***Perangkat:** 1x Cisco Switch (2960) & 3x PC (Client)
***IP Management Switch:** `192.168.1.10 /24` (VLAN 1)
***IP PC Client:** `192.168.1.20 /24, 192.168.1.30 /25, 192.168.1.40 /24`

###  Tes Ping Jaringan

<p align="center">
<img width="697" height="697" alt="tesping" src="https://github.com/user-attachments/assets/d5791847-c514-48d4-a228-477f84f552b0" />
</p>

### Daftar Konfigurasi Utama (CLI)
Berikut adalah konfigurasi yang diterapkan pada Switch `SW-HomeLab`:
1. **Hostname Configuration:** Mengubah nama perangkat menjadi `SW-HomeLab`.
2. **Security Hardening:**
   - Mengaktifkan `enable secret` dengan password terenkripsi.
   - Mengaktifkan `service password-encryption` untuk menyembunyikan *plain-text password*.
   - Membuat banner peringatan (`banner motd`) untuk akses ilegal.
3. **Secure Remote Access (SSH):**
   - Menyetel domain name (`homelab.local`).
   - Membuat kunci RSA (`crypto key generate rsa` 1024-bit).
   - Membuat akun lokal admin/user dengan *privilege level 15*.
   - Mengonfigurasi jalur `line vty 0 4` agar hanya menerima koneksi via **SSH** (`transport input ssh`).
4. **Management IP (VLAN 1):**
   - Memberikan alamat IP statis pada interface `Vlan 1` agar bisa di-remote dan di-*ping*.

---

### Command CLI
```text
Switch>en
Switch#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#hostname SW-HomeLab
SW-HomeLab(config)#enable secret ArielBrayen123
SW-HomeLab(config)#service password-encryption
SW-HomeLab(config)#banner motd # PERINGATAN: Hak Akses Lab khusus Ariel! # 
SW-HomeLab(config)#ip domain-name homelab.local
SW-HomeLab(config)#crypto key generate rsa
The name for the keys will be: SW-HomeLab.homelab.local
Choose the size of the key modulus in the range of 360 to 4096 for your
  General Purpose Keys. Choosing a key modulus greater than 512 may take
  a few minutes.

How many bits in the modulus [512]: 1024
% Generating 1024 bit RSA keys, keys will be non-exportable...[OK]

SW-HomeLab(config)#username ariel privilege 15 secret ariel123 
SW-HomeLab(config)#line vty 0 4
SW-HomeLab(config-line)#transport input ssh
SW-HomeLab(config-line)#login local
SW-HomeLab(config-line)#exit
SW-HomeLab(config)#interface vlan 1
SW-HomeLab(config-if)#ip address 192.168.1.10 255.255.255.0
SW-HomeLab(config-if)#no sh
SW-HomeLab(config-if)#exit
SW-HomeLab(config)#end
SW-HomeLab#copy running-config startup-config
Destination filename [startup-config]? 
Building configuration...
[OK]
SW-HomeLab#

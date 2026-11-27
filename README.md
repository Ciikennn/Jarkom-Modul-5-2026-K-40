# Jarkom-Modul-5-2026-K-40



**Kelompok:** K40  
**Prefix IP:** `192.231.0.0/16`  


## Daftar Isi

- [Catatan Umum](#catatan-umum)
- [Soal 1 — Markas tanpa peta (VLSM)](#soal-1)
- [Soal 2 — Geofront gelap gulita (IP & forwarding)](#soal-2)
- [Soal 3 — Pulau-pulau yang terpisah (routing statis)](#soal-3)
- [Soal 4 — Haus kabar dari dunia luar (NAT & DNS)](#soal-4)
- [Soal 5 — Pintu depan dikunci rapat (firewall Gendo)](#soal-5)
- [Soal 6 — Alamat para pilot (IP statis node)](#soal-6)
- [Soal 7 — Membangunkan MAGI](#soal-7)
- [Soal 8 — Mengenali kondisi normal](#soal-8)
- [Soal 9 — Jalur belakang diutak-atik](#soal-9)
- [Soal 10 — Para pilot terkunci di dalam](#soal-10)
- [Kendala & Solusi](#kendala--solusi)
- [Soal 11 - Casper harus jadi hantu](#soal-11---casper-harus-jadi-hantu)
- [Soal 12 - Teknisi yang kelewat semangat](#soal-12---teknisi-yang-kelewat-semangat)
- [Soal 13 - Brankas Balthasar](#soal-13---brankas-balthasar)
- [Soal 14 - Jam kunjungan sang pinguin](#soal-14---jam-kunjungan-sang-pinguin)
- [Soal 15 - Banjir ping di pusat komando](#soal-15---banjir-ping-di-pusat-komando)
- [Soal 16 - Pencuri yang mencoba setiap pintu](#soal-16---pencuri-yang-mencoba-setiap-pintu)
- [Soal 17 - Sihir hitam di jalur paket](#soal-17---sihir-hitam-di-jalur-paket)
- [Soal 18 - Gerbang Monolith dan Radar](#soal-18---gerbang-monolith-dan-radar)
- [Soal 19 - Hukuman untuk sang pengkhianat](#soal-19---hukuman-untuk-sang-pengkhianat)
- [Soal 20 - Uji kebakaran Geofront](#soal-20---uji-kebakaran-geofront)

---

## Catatan Umum

### Topologi

![Topologi GNS3](img/topologi_gns3.png)

17 router, 16 link antar-router, 17 LAN. Hanya **Gendo** yang terhubung ke **NAT1**.

### Peta port router

| Router | Port → tujuan |
|---|---|
| **Gendo** | eth0 -> NAT1, eth1 -> Switch-Cmd, eth2 -> Fuyutsuki, eth3 -> Shigeru, eth4 -> Misato, eth5 -> Kaji, eth6 -> Asuka, eth7 -> Hikari, eth8 -> Yui |
| **Yui** | eth0 -> Gendo, eth1 -> Mari, eth2 -> Rei, eth3 -> Shinji, eth4 -> Switch-Hangar |
| **Hikari** | eth0 -> Gendo, eth1 -> Kaworu, eth2 -> Switch-Comm |
| **Asuka** | eth0 -> Gendo, eth1 -> Ritsuko, eth2 -> Kensuke, eth3 -> Switch-Archive |
| **Kaji** | eth0 -> Gendo, eth1 -> Switch-Intel |
| **Misato** | eth0 -> Gendo, eth1 -> Switch-Cage |
| **Shigeru** | eth0 -> Gendo, eth1 -> Makoto, eth2 -> Switch-Monolith |
| **Fuyutsuki** | eth0 -> Gendo, eth1 -> Maya, eth2 -> Toji, eth3 -> Switch-Terminal |
| **Mari** | eth0 -> Yui, eth1 -> Switch-Dock |
| **Rei** | eth0 -> Yui, eth1 -> Switch-Quarters |
| **Shinji** | eth0 -> Yui, eth1 -> Switch-Garden |
| **Kaworu** | eth0 -> Hikari, eth1 -> Switch-Relay |
| **Kensuke** | eth0 -> Asuka, eth1 -> Switch-Bunker |
| **Ritsuko** | eth0 -> Asuka, eth1 -> Switch-Lab |
| **Makoto** | eth0 -> Shigeru, eth1 -> Switch-Radar |
| **Maya** | eth0 -> Fuyutsuki, eth1 -> Switch-Medic |
| **Toji** | eth0 -> Fuyutsuki, eth1 -> Switch-Dogma |

IP link `/30`: **IP pertama = router atas**, **IP kedua = router bawah**.

### Dua cara menempel config

| Istilah | Caranya | Format |
|---|---|---|
| **Edit config** | klik kanan node → *Edit config* → hapus isi → tempel → Save | `/etc/network/interfaces` (diawali `auto eth...`) |
| **Console** | buka terminal node → paste seluruh blok → Enter | perintah shell (blok `cat > /root/x.sh << 'EOF' ... EOF` + `chmod +x`) |

Script dibuat idempoten (aman dijalankan berulang). Agar file di `/root` tidak hilang saat restart: *Stop node → klik kanan → Configure → Advanced → Additional persistent directories →* `/root`.

---

<a id="soal-1"></a>
## Soal 1 — Markas tanpa peta (VLSM)

#### Langkah 1 — Hitung kebutuhan IP tiap subnet

Rumus: **kebutuhan = jumlah host + 1 (gateway)**; pilih prefix terkecil dengan usable (2ⁿ − 2) ≥ kebutuhan. Link antar-router = 2 IP → `/30`.

| Subnet | Router GW | Host | +GW | Kebutuhan | Prefix (usable) |
|---|---|---|---|---|---|
| Switch-Quarters | Rei | Quarters 500 | 1 | 501 | /23 (510) |
| Switch-Comm | Hikari | Comm-Net 200 | 1 | 201 | /24 (254) |
| Switch-Hangar | Yui | Hangar-Crew 100 | 1 | 101 | /25 (126) |
| Switch-Bunker | Kensuke | Sachiel 55 | 1 | 56 | /26 (62) |
| Switch-Medic | Maya | Pen-Pen 1 + Medic-Bay 25 | 1 | 27 | /27 (30) |
| Switch-Relay | Kaworu | Relay-Node 25 | 1 | 26 | /27 (30) |
| Switch-Garden | Shinji | Garden-Staff 14 | 1 | 15 | /27 (30) |
| Switch-Dock | Mari | Dock-Crew 12 | 1 | 13 | /28 (14) |
| Switch-Lab | Ritsuko | Entry-Plug 3 + Dummy-Plug 2 | 1 | 6 | /29 (6) |
| Switch-Terminal | Fuyutsuki | Magi-Casper 1 + Magi-Balthasar 1 + Magi-Melchior 1 | 1 | 4 | /29 (6) |
| Switch-Cage | Misato | Unit-01 1 + Unit-02 1 + Unit-00 1 | 1 | 4 | /29 (6) |
| Switch-Dogma | Toji | Lilith 1 + Adam 1 | 1 | 3 | /29 (6) |
| Switch-Monolith | Shigeru | Zeruel 1 + Armisael 1 | 1 | 3 | /29 (6) |
| Switch-Radar | Makoto | Radar-Array 4 | 1 | 5 | /29 (6) |
| Switch-Archive | Asuka | Archive-Vault 1 | 1 | 2 | /30 (2) |
| Switch-Intel | Kaji | Spy 1 | 1 | 2 | /30 (2) |
| Switch-Cmd | Gendo | Tokyo3-Core 1 | 1 | 2 | /30 (2) |
| 16 link antar-router | – | 2 router | – | 2 | /30 (2) |

#### Langkah 2 — Urutkan dan alokasikan (VLSM)

Urutan: terbesar dulu; tie-break **ukuran sama → LAN dulu daripada link → urutan tabel**. Urutan link: link Gendo dulu, lalu per cabang. Subnet berikutnya dimulai tepat setelah broadcast subnet sebelumnya.

| No | Subnet | Network | Prefix | Netmask | Gateway | Range usable | Broadcast | Terpakai | Sisa |
|---|---|---|---|---|---|---|---|---|---|
| 1 | Quarters | 192.231.0.0 | /23 | 255.255.254.0 | 192.231.0.1 | 192.231.0.1 – 192.231.1.254 | 192.231.1.255 | 501 | 9 |
| 2 | Comm | 192.231.2.0 | /24 | 255.255.255.0 | 192.231.2.1 | 192.231.2.1 – 192.231.2.254 | 192.231.2.255 | 201 | 53 |
| 3 | Hangar | 192.231.3.0 | /25 | 255.255.255.128 | 192.231.3.1 | 192.231.3.1 – 192.231.3.126 | 192.231.3.127 | 101 | 25 |
| 4 | Bunker | 192.231.3.128 | /26 | 255.255.255.192 | 192.231.3.129 | 192.231.3.129 – 192.231.3.190 | 192.231.3.191 | 56 | 6 |
| 5 | Medic | 192.231.3.192 | /27 | 255.255.255.224 | 192.231.3.193 | 192.231.3.193 – 192.231.3.222 | 192.231.3.223 | 27 | 3 |
| 6 | Relay | 192.231.3.224 | /27 | 255.255.255.224 | 192.231.3.225 | 192.231.3.225 – 192.231.3.254 | 192.231.3.255 | 26 | 4 |
| 7 | Garden | 192.231.4.0 | /27 | 255.255.255.224 | 192.231.4.1 | 192.231.4.1 – 192.231.4.30 | 192.231.4.31 | 15 | 15 |
| 8 | Dock | 192.231.4.32 | /28 | 255.255.255.240 | 192.231.4.33 | 192.231.4.33 – 192.231.4.46 | 192.231.4.47 | 13 | 1 |
| 9 | **Lab** ⭕ | 192.231.4.48 | /29 | 255.255.255.248 | 192.231.4.49 | 192.231.4.49 – 192.231.4.54 | 192.231.4.55 | 6 | 0 |
| 10 | Terminal | 192.231.4.56 | /29 | 255.255.255.248 | 192.231.4.57 | 192.231.4.57 – 192.231.4.62 | 192.231.4.63 | 4 | 2 |
| 11 | Cage | 192.231.4.64 | /29 | 255.255.255.248 | 192.231.4.65 | 192.231.4.65 – 192.231.4.70 | 192.231.4.71 | 4 | 2 |
| 12 | Dogma | 192.231.4.72 | /29 | 255.255.255.248 | 192.231.4.73 | 192.231.4.73 – 192.231.4.78 | 192.231.4.79 | 3 | 3 |
| 13 | **Monolith** ⭕ | 192.231.4.80 | /29 | 255.255.255.248 | 192.231.4.81 | 192.231.4.81 – 192.231.4.86 | 192.231.4.87 | 3 | 3 |
| 14 | Radar | 192.231.4.88 | /29 | 255.255.255.248 | 192.231.4.89 | 192.231.4.89 – 192.231.4.94 | 192.231.4.95 | 5 | 1 |
| 15 | Archive | 192.231.4.96 | /30 | 255.255.255.252 | 192.231.4.97 | 192.231.4.97 – 192.231.4.98 | 192.231.4.99 | 2 | 0 |
| 16 | Intel | 192.231.4.100 | /30 | 255.255.255.252 | 192.231.4.101 | 192.231.4.101 – 192.231.4.102 | 192.231.4.103 | 2 | 0 |
| 17 | Cmd | 192.231.4.104 | /30 | 255.255.255.252 | 192.231.4.105 | 192.231.4.105 – 192.231.4.106 | 192.231.4.107 | 2 | 0 |
| 18 | Gendo-Yui | 192.231.4.108 | /30 | 255.255.255.252 | – | 192.231.4.109 – 192.231.4.110 | 192.231.4.111 | 2 | 0 |
| 19 | Gendo-Hikari | 192.231.4.112 | /30 | 255.255.255.252 | – | 192.231.4.113 – 192.231.4.114 | 192.231.4.115 | 2 | 0 |
| 20 | **Gendo-Asuka** ⭕ | 192.231.4.116 | /30 | 255.255.255.252 | – | 192.231.4.117 – 192.231.4.118 | 192.231.4.119 | 2 | 0 |
| 21 | Gendo-Kaji | 192.231.4.120 | /30 | 255.255.255.252 | – | 192.231.4.121 – 192.231.4.122 | 192.231.4.123 | 2 | 0 |
| 22 | Gendo-Misato | 192.231.4.124 | /30 | 255.255.255.252 | – | 192.231.4.125 – 192.231.4.126 | 192.231.4.127 | 2 | 0 |
| 23 | **Gendo-Shigeru** ⭕ | 192.231.4.128 | /30 | 255.255.255.252 | – | 192.231.4.129 – 192.231.4.130 | 192.231.4.131 | 2 | 0 |
| 24 | Gendo-Fuyutsuki | 192.231.4.132 | /30 | 255.255.255.252 | – | 192.231.4.133 – 192.231.4.134 | 192.231.4.135 | 2 | 0 |
| 25 | Yui-Mari | 192.231.4.136 | /30 | 255.255.255.252 | – | 192.231.4.137 – 192.231.4.138 | 192.231.4.139 | 2 | 0 |
| 26 | Yui-Rei | 192.231.4.140 | /30 | 255.255.255.252 | – | 192.231.4.141 – 192.231.4.142 | 192.231.4.143 | 2 | 0 |
| 27 | Yui-Shinji | 192.231.4.144 | /30 | 255.255.255.252 | – | 192.231.4.145 – 192.231.4.146 | 192.231.4.147 | 2 | 0 |
| 28 | Hikari-Kaworu | 192.231.4.148 | /30 | 255.255.255.252 | – | 192.231.4.149 – 192.231.4.150 | 192.231.4.151 | 2 | 0 |
| 29 | Asuka-Kensuke | 192.231.4.152 | /30 | 255.255.255.252 | – | 192.231.4.153 – 192.231.4.154 | 192.231.4.155 | 2 | 0 |
| 30 | **Asuka-Ritsuko** ⭕ | 192.231.4.156 | /30 | 255.255.255.252 | – | 192.231.4.157 – 192.231.4.158 | 192.231.4.159 | 2 | 0 |
| 31 | Shigeru-Makoto | 192.231.4.160 | /30 | 255.255.255.252 | – | 192.231.4.161 – 192.231.4.162 | 192.231.4.163 | 2 | 0 |
| 32 | Fuyutsuki-Maya | 192.231.4.164 | /30 | 255.255.255.252 | – | 192.231.4.165 – 192.231.4.166 | 192.231.4.167 | 2 | 0 |
| 33 | Fuyutsuki-Toji | 192.231.4.168 | /30 | 255.255.255.252 | – | 192.231.4.169 – 192.231.4.170 | 192.231.4.171 | 2 | 0 |

Total **1196 alamat** (`192.231.0.0` – `192.231.4.171`), muat dalam `/21`.

#### Langkah 3 — Gambar pohon IP dan lingkari jalur Dummy-Plug → Armisael

Jalur: **Lab → Asuka–Ritsuko → Gendo–Asuka → Gendo–Shigeru → Monolith** (⭕ merah).

![Pohon IP VLSM](img/pohon_ip.png)

#### Langkah 4 — Contoh perhitungan satu subnet: Switch-Garden

1. Garden-Staff 14 host + 1 gateway (Shinji) = **15 IP**.
2. `/28` → 14 usable (tidak cukup); `/27` → 30 usable → **/27** (`255.255.255.224`).
3. Relay `192.231.3.224/27` berakhir di `192.231.3.255` → Garden mulai **192.231.4.0**.
4. Network `192.231.4.0/27`, gateway `192.231.4.1`, range `.4.1 – .4.30`, broadcast `.4.31`.
5. Terpakai 15, sisa **15**.

#### Langkah 5 — Tetapkan IP node (gateway = usable pertama, node berikutnya sesuai urutan di switch)

| Node | Switch | IP | Gateway |
|---|---|---|---|
| Quarters | Switch-Quarters | 192.231.0.2/23 | 192.231.0.1 (Rei) |
| Comm-Net | Switch-Comm | 192.231.2.2/24 | 192.231.2.1 (Hikari) |
| Hangar-Crew | Switch-Hangar | 192.231.3.2/25 | 192.231.3.1 (Yui) |
| Sachiel | Switch-Bunker | 192.231.3.130/26 | 192.231.3.129 (Kensuke) |
| Pen-Pen | Switch-Medic | 192.231.3.194/27 | 192.231.3.193 (Maya) |
| Medic-Bay | Switch-Medic | 192.231.3.195/27 | 192.231.3.193 (Maya) |
| Relay-Node | Switch-Relay | 192.231.3.226/27 | 192.231.3.225 (Kaworu) |
| Garden-Staff | Switch-Garden | 192.231.4.2/27 | 192.231.4.1 (Shinji) |
| Dock-Crew | Switch-Dock | 192.231.4.34/28 | 192.231.4.33 (Mari) |
| Entry-Plug | Switch-Lab | 192.231.4.50/29 | 192.231.4.49 (Ritsuko) |
| Dummy-Plug | Switch-Lab | 192.231.4.51/29 | 192.231.4.49 (Ritsuko) |
| Magi-Casper | Switch-Terminal | 192.231.4.58/29 | 192.231.4.57 (Fuyutsuki) |
| Magi-Balthasar | Switch-Terminal | 192.231.4.59/29 | 192.231.4.57 (Fuyutsuki) |
| Magi-Melchior | Switch-Terminal | 192.231.4.60/29 | 192.231.4.57 (Fuyutsuki) |
| Unit-01 | Switch-Cage | 192.231.4.66/29 | 192.231.4.65 (Misato) |
| Unit-02 | Switch-Cage | 192.231.4.67/29 | 192.231.4.65 (Misato) |
| Unit-00 | Switch-Cage | 192.231.4.68/29 | 192.231.4.65 (Misato) |
| Lilith | Switch-Dogma | 192.231.4.74/29 | 192.231.4.73 (Toji) |
| Adam | Switch-Dogma | 192.231.4.75/29 | 192.231.4.73 (Toji) |
| Zeruel | Switch-Monolith | 192.231.4.82/29 | 192.231.4.81 (Shigeru) |
| Armisael | Switch-Monolith | 192.231.4.83/29 | 192.231.4.81 (Shigeru) |
| Radar-Array | Switch-Radar | 192.231.4.90/29 | 192.231.4.89 (Makoto) |
| Archive-Vault | Switch-Archive | 192.231.4.98/30 | 192.231.4.97 (Asuka) |
| Spy | Switch-Intel | 192.231.4.102/30 | 192.231.4.101 (Kaji) |
| Tokyo3-Core | Switch-Cmd | 192.231.4.106/30 | 192.231.4.105 (Gendo) |

---

<a id="soal-2"></a>
## Soal 2 — Geofront gelap gulita (IP & forwarding)

#### Langkah 1 — Siapkan adapter router (saat node Stop)

Klik kanan router → Configure → Adapters: **Gendo 9, Yui 5, Asuka 4, Fuyutsuki 4, Hikari 3, Shigeru 3, router lain 2**. Sambungkan kabel sesuai [peta port](#peta-port-router).

#### Langkah 2 — Gendo: Edit config, lalu buat script uji di console

**a. Edit config:**

```
# eth0 -> NAT1
auto eth0
iface eth0 inet dhcp

# eth1 -> Switch-Cmd  (192.231.4.104/30)
auto eth1
iface eth1 inet static
    address 192.231.4.105
    netmask 255.255.255.252
    up echo 1 > /proc/sys/net/ipv4/ip_forward

# eth2 -> Fuyutsuki  (192.231.4.132/30)
auto eth2
iface eth2 inet static
    address 192.231.4.133
    netmask 255.255.255.252

# eth3 -> Shigeru  (192.231.4.128/30)
auto eth3
iface eth3 inet static
    address 192.231.4.129
    netmask 255.255.255.252

# eth4 -> Misato  (192.231.4.124/30)
auto eth4
iface eth4 inet static
    address 192.231.4.125
    netmask 255.255.255.252

# eth5 -> Kaji  (192.231.4.120/30)
auto eth5
iface eth5 inet static
    address 192.231.4.121
    netmask 255.255.255.252

# eth6 -> Asuka  (192.231.4.116/30)
auto eth6
iface eth6 inet static
    address 192.231.4.117
    netmask 255.255.255.252

# eth7 -> Hikari  (192.231.4.112/30)
auto eth7
iface eth7 inet static
    address 192.231.4.113
    netmask 255.255.255.252

# eth8 -> Yui  (192.231.4.108/30)
auto eth8
iface eth8 inet static
    address 192.231.4.109
    netmask 255.255.255.252
```

**b. Console** (sekali paste):

```sh
cat > /root/no2.sh << 'EOF'
#!/bin/bash
# Soal 2 - Gendo: IP interface + IP forwarding

echo "=== [Gendo] Konfigurasi IP ==="
# eth0 -> NAT1 (DHCP)
ip link set eth0 up
if ! ip -4 addr show eth0 | grep -q inet; then
  dhclient -v eth0 2>/dev/null || /gns3/bin/busybox udhcpc -i eth0 -q
fi
# eth1 -> Switch-Cmd
ip link set eth1 up
ip addr replace 192.231.4.105/30 dev eth1
# eth2 -> Fuyutsuki
ip link set eth2 up
ip addr replace 192.231.4.133/30 dev eth2
# eth3 -> Shigeru
ip link set eth3 up
ip addr replace 192.231.4.129/30 dev eth3
# eth4 -> Misato
ip link set eth4 up
ip addr replace 192.231.4.125/30 dev eth4
# eth5 -> Kaji
ip link set eth5 up
ip addr replace 192.231.4.121/30 dev eth5
# eth6 -> Asuka
ip link set eth6 up
ip addr replace 192.231.4.117/30 dev eth6
# eth7 -> Hikari
ip link set eth7 up
ip addr replace 192.231.4.113/30 dev eth7
# eth8 -> Yui
ip link set eth8 up
ip addr replace 192.231.4.109/30 dev eth8

# IP forwarding
sysctl -w net.ipv4.ip_forward=1

echo "=== ip -br a ==="
ip -br a

echo "=== ip_forward = $(cat /proc/sys/net/ipv4/ip_forward) ==="

echo "=== Uji ping ke router tetangga ==="
ping -c 3 -W 1 192.231.4.134 > /tmp/p.txt 2>&1 && echo "[OK]   Fuyutsuki (192.231.4.134)" || echo "[GAGAL] Fuyutsuki (192.231.4.134)"; tail -2 /tmp/p.txt | head -1
ping -c 3 -W 1 192.231.4.130 > /tmp/p.txt 2>&1 && echo "[OK]   Shigeru (192.231.4.130)" || echo "[GAGAL] Shigeru (192.231.4.130)"; tail -2 /tmp/p.txt | head -1
ping -c 3 -W 1 192.231.4.126 > /tmp/p.txt 2>&1 && echo "[OK]   Misato (192.231.4.126)" || echo "[GAGAL] Misato (192.231.4.126)"; tail -2 /tmp/p.txt | head -1
ping -c 3 -W 1 192.231.4.122 > /tmp/p.txt 2>&1 && echo "[OK]   Kaji (192.231.4.122)" || echo "[GAGAL] Kaji (192.231.4.122)"; tail -2 /tmp/p.txt | head -1
ping -c 3 -W 1 192.231.4.118 > /tmp/p.txt 2>&1 && echo "[OK]   Asuka (192.231.4.118)" || echo "[GAGAL] Asuka (192.231.4.118)"; tail -2 /tmp/p.txt | head -1
ping -c 3 -W 1 192.231.4.114 > /tmp/p.txt 2>&1 && echo "[OK]   Hikari (192.231.4.114)" || echo "[GAGAL] Hikari (192.231.4.114)"; tail -2 /tmp/p.txt | head -1
ping -c 3 -W 1 192.231.4.110 > /tmp/p.txt 2>&1 && echo "[OK]   Yui (192.231.4.110)" || echo "[GAGAL] Yui (192.231.4.110)"; tail -2 /tmp/p.txt | head -1
echo "=== Default route dari NAT1 ==="
ip route | grep default
EOF
chmod +x /root/no2.sh
echo "/root/no2.sh siap"
```

#### Langkah 3 — Yui: Edit config, lalu buat script uji di console

**a. Edit config:**

```
# eth0 -> Gendo  (192.231.4.108/30)
auto eth0
iface eth0 inet static
    address 192.231.4.110
    netmask 255.255.255.252
    up echo 1 > /proc/sys/net/ipv4/ip_forward

# eth1 -> Mari  (192.231.4.136/30)
auto eth1
iface eth1 inet static
    address 192.231.4.137
    netmask 255.255.255.252

# eth2 -> Rei  (192.231.4.140/30)
auto eth2
iface eth2 inet static
    address 192.231.4.141
    netmask 255.255.255.252

# eth3 -> Shinji  (192.231.4.144/30)
auto eth3
iface eth3 inet static
    address 192.231.4.145
    netmask 255.255.255.252

# eth4 -> Switch-Hangar  (192.231.3.0/25)
auto eth4
iface eth4 inet static
    address 192.231.3.1
    netmask 255.255.255.128
```

**b. Console** (sekali paste):

```sh
cat > /root/no2.sh << 'EOF'
#!/bin/bash
# Soal 2 - Yui: IP interface + IP forwarding

echo "=== [Yui] Konfigurasi IP ==="
# eth0 -> Gendo
ip link set eth0 up
ip addr replace 192.231.4.110/30 dev eth0
# eth1 -> Mari
ip link set eth1 up
ip addr replace 192.231.4.137/30 dev eth1
# eth2 -> Rei
ip link set eth2 up
ip addr replace 192.231.4.141/30 dev eth2
# eth3 -> Shinji
ip link set eth3 up
ip addr replace 192.231.4.145/30 dev eth3
# eth4 -> Switch-Hangar
ip link set eth4 up
ip addr replace 192.231.3.1/25 dev eth4

# IP forwarding
sysctl -w net.ipv4.ip_forward=1

echo "=== ip -br a ==="
ip -br a

echo "=== ip_forward = $(cat /proc/sys/net/ipv4/ip_forward) ==="

echo "=== Uji ping ke router tetangga ==="
ping -c 3 -W 1 192.231.4.109 > /tmp/p.txt 2>&1 && echo "[OK]   Gendo (192.231.4.109)" || echo "[GAGAL] Gendo (192.231.4.109)"; tail -2 /tmp/p.txt | head -1
ping -c 3 -W 1 192.231.4.138 > /tmp/p.txt 2>&1 && echo "[OK]   Mari (192.231.4.138)" || echo "[GAGAL] Mari (192.231.4.138)"; tail -2 /tmp/p.txt | head -1
ping -c 3 -W 1 192.231.4.142 > /tmp/p.txt 2>&1 && echo "[OK]   Rei (192.231.4.142)" || echo "[GAGAL] Rei (192.231.4.142)"; tail -2 /tmp/p.txt | head -1
ping -c 3 -W 1 192.231.4.146 > /tmp/p.txt 2>&1 && echo "[OK]   Shinji (192.231.4.146)" || echo "[GAGAL] Shinji (192.231.4.146)"; tail -2 /tmp/p.txt | head -1
EOF
chmod +x /root/no2.sh
echo "/root/no2.sh siap"
```

#### Langkah 4 — Hikari: Edit config, lalu buat script uji di console

**a. Edit config:**

```
# eth0 -> Gendo  (192.231.4.112/30)
auto eth0
iface eth0 inet static
    address 192.231.4.114
    netmask 255.255.255.252
    up echo 1 > /proc/sys/net/ipv4/ip_forward

# eth1 -> Kaworu  (192.231.4.148/30)
auto eth1
iface eth1 inet static
    address 192.231.4.149
    netmask 255.255.255.252

# eth2 -> Switch-Comm  (192.231.2.0/24)
auto eth2
iface eth2 inet static
    address 192.231.2.1
    netmask 255.255.255.0
```

**b. Console** (sekali paste):

```sh
cat > /root/no2.sh << 'EOF'
#!/bin/bash
# Soal 2 - Hikari: IP interface + IP forwarding

echo "=== [Hikari] Konfigurasi IP ==="
# eth0 -> Gendo
ip link set eth0 up
ip addr replace 192.231.4.114/30 dev eth0
# eth1 -> Kaworu
ip link set eth1 up
ip addr replace 192.231.4.149/30 dev eth1
# eth2 -> Switch-Comm
ip link set eth2 up
ip addr replace 192.231.2.1/24 dev eth2

# IP forwarding
sysctl -w net.ipv4.ip_forward=1

echo "=== ip -br a ==="
ip -br a

echo "=== ip_forward = $(cat /proc/sys/net/ipv4/ip_forward) ==="

echo "=== Uji ping ke router tetangga ==="
ping -c 3 -W 1 192.231.4.113 > /tmp/p.txt 2>&1 && echo "[OK]   Gendo (192.231.4.113)" || echo "[GAGAL] Gendo (192.231.4.113)"; tail -2 /tmp/p.txt | head -1
ping -c 3 -W 1 192.231.4.150 > /tmp/p.txt 2>&1 && echo "[OK]   Kaworu (192.231.4.150)" || echo "[GAGAL] Kaworu (192.231.4.150)"; tail -2 /tmp/p.txt | head -1
EOF
chmod +x /root/no2.sh
echo "/root/no2.sh siap"
```

#### Langkah 5 — Asuka: Edit config, lalu buat script uji di console

**a. Edit config:**

```
# eth0 -> Gendo  (192.231.4.116/30)
auto eth0
iface eth0 inet static
    address 192.231.4.118
    netmask 255.255.255.252
    up echo 1 > /proc/sys/net/ipv4/ip_forward

# eth1 -> Ritsuko  (192.231.4.156/30)
auto eth1
iface eth1 inet static
    address 192.231.4.157
    netmask 255.255.255.252

# eth2 -> Kensuke  (192.231.4.152/30)
auto eth2
iface eth2 inet static
    address 192.231.4.153
    netmask 255.255.255.252

# eth3 -> Switch-Archive  (192.231.4.96/30)
auto eth3
iface eth3 inet static
    address 192.231.4.97
    netmask 255.255.255.252
```

**b. Console** (sekali paste):

```sh
cat > /root/no2.sh << 'EOF'
#!/bin/bash
# Soal 2 - Asuka: IP interface + IP forwarding

echo "=== [Asuka] Konfigurasi IP ==="
# eth0 -> Gendo
ip link set eth0 up
ip addr replace 192.231.4.118/30 dev eth0
# eth1 -> Ritsuko
ip link set eth1 up
ip addr replace 192.231.4.157/30 dev eth1
# eth2 -> Kensuke
ip link set eth2 up
ip addr replace 192.231.4.153/30 dev eth2
# eth3 -> Switch-Archive
ip link set eth3 up
ip addr replace 192.231.4.97/30 dev eth3

# IP forwarding
sysctl -w net.ipv4.ip_forward=1

echo "=== ip -br a ==="
ip -br a

echo "=== ip_forward = $(cat /proc/sys/net/ipv4/ip_forward) ==="

echo "=== Uji ping ke router tetangga ==="
ping -c 3 -W 1 192.231.4.117 > /tmp/p.txt 2>&1 && echo "[OK]   Gendo (192.231.4.117)" || echo "[GAGAL] Gendo (192.231.4.117)"; tail -2 /tmp/p.txt | head -1
ping -c 3 -W 1 192.231.4.158 > /tmp/p.txt 2>&1 && echo "[OK]   Ritsuko (192.231.4.158)" || echo "[GAGAL] Ritsuko (192.231.4.158)"; tail -2 /tmp/p.txt | head -1
ping -c 3 -W 1 192.231.4.154 > /tmp/p.txt 2>&1 && echo "[OK]   Kensuke (192.231.4.154)" || echo "[GAGAL] Kensuke (192.231.4.154)"; tail -2 /tmp/p.txt | head -1
EOF
chmod +x /root/no2.sh
echo "/root/no2.sh siap"
```

#### Langkah 6 — Kaji: Edit config, lalu buat script uji di console

**a. Edit config:**

```
# eth0 -> Gendo  (192.231.4.120/30)
auto eth0
iface eth0 inet static
    address 192.231.4.122
    netmask 255.255.255.252
    up echo 1 > /proc/sys/net/ipv4/ip_forward

# eth1 -> Switch-Intel  (192.231.4.100/30)
auto eth1
iface eth1 inet static
    address 192.231.4.101
    netmask 255.255.255.252
```

**b. Console** (sekali paste):

```sh
cat > /root/no2.sh << 'EOF'
#!/bin/bash
# Soal 2 - Kaji: IP interface + IP forwarding

echo "=== [Kaji] Konfigurasi IP ==="
# eth0 -> Gendo
ip link set eth0 up
ip addr replace 192.231.4.122/30 dev eth0
# eth1 -> Switch-Intel
ip link set eth1 up
ip addr replace 192.231.4.101/30 dev eth1

# IP forwarding
sysctl -w net.ipv4.ip_forward=1

echo "=== ip -br a ==="
ip -br a

echo "=== ip_forward = $(cat /proc/sys/net/ipv4/ip_forward) ==="

echo "=== Uji ping ke router tetangga ==="
ping -c 3 -W 1 192.231.4.121 > /tmp/p.txt 2>&1 && echo "[OK]   Gendo (192.231.4.121)" || echo "[GAGAL] Gendo (192.231.4.121)"; tail -2 /tmp/p.txt | head -1
EOF
chmod +x /root/no2.sh
echo "/root/no2.sh siap"
```

#### Langkah 7 — Misato: Edit config, lalu buat script uji di console

**a. Edit config:**

```
# eth0 -> Gendo  (192.231.4.124/30)
auto eth0
iface eth0 inet static
    address 192.231.4.126
    netmask 255.255.255.252
    up echo 1 > /proc/sys/net/ipv4/ip_forward

# eth1 -> Switch-Cage  (192.231.4.64/29)
auto eth1
iface eth1 inet static
    address 192.231.4.65
    netmask 255.255.255.248
```

**b. Console** (sekali paste):

```sh
cat > /root/no2.sh << 'EOF'
#!/bin/bash
# Soal 2 - Misato: IP interface + IP forwarding

echo "=== [Misato] Konfigurasi IP ==="
# eth0 -> Gendo
ip link set eth0 up
ip addr replace 192.231.4.126/30 dev eth0
# eth1 -> Switch-Cage
ip link set eth1 up
ip addr replace 192.231.4.65/29 dev eth1

# IP forwarding
sysctl -w net.ipv4.ip_forward=1

echo "=== ip -br a ==="
ip -br a

echo "=== ip_forward = $(cat /proc/sys/net/ipv4/ip_forward) ==="

echo "=== Uji ping ke router tetangga ==="
ping -c 3 -W 1 192.231.4.125 > /tmp/p.txt 2>&1 && echo "[OK]   Gendo (192.231.4.125)" || echo "[GAGAL] Gendo (192.231.4.125)"; tail -2 /tmp/p.txt | head -1
EOF
chmod +x /root/no2.sh
echo "/root/no2.sh siap"
```

#### Langkah 8 — Shigeru: Edit config, lalu buat script uji di console

**a. Edit config:**

```
# eth0 -> Gendo  (192.231.4.128/30)
auto eth0
iface eth0 inet static
    address 192.231.4.130
    netmask 255.255.255.252
    up echo 1 > /proc/sys/net/ipv4/ip_forward

# eth1 -> Makoto  (192.231.4.160/30)
auto eth1
iface eth1 inet static
    address 192.231.4.161
    netmask 255.255.255.252

# eth2 -> Switch-Monolith  (192.231.4.80/29)
auto eth2
iface eth2 inet static
    address 192.231.4.81
    netmask 255.255.255.248
```

**b. Console** (sekali paste):

```sh
cat > /root/no2.sh << 'EOF'
#!/bin/bash
# Soal 2 - Shigeru: IP interface + IP forwarding

echo "=== [Shigeru] Konfigurasi IP ==="
# eth0 -> Gendo
ip link set eth0 up
ip addr replace 192.231.4.130/30 dev eth0
# eth1 -> Makoto
ip link set eth1 up
ip addr replace 192.231.4.161/30 dev eth1
# eth2 -> Switch-Monolith
ip link set eth2 up
ip addr replace 192.231.4.81/29 dev eth2

# IP forwarding
sysctl -w net.ipv4.ip_forward=1

echo "=== ip -br a ==="
ip -br a

echo "=== ip_forward = $(cat /proc/sys/net/ipv4/ip_forward) ==="

echo "=== Uji ping ke router tetangga ==="
ping -c 3 -W 1 192.231.4.129 > /tmp/p.txt 2>&1 && echo "[OK]   Gendo (192.231.4.129)" || echo "[GAGAL] Gendo (192.231.4.129)"; tail -2 /tmp/p.txt | head -1
ping -c 3 -W 1 192.231.4.162 > /tmp/p.txt 2>&1 && echo "[OK]   Makoto (192.231.4.162)" || echo "[GAGAL] Makoto (192.231.4.162)"; tail -2 /tmp/p.txt | head -1
EOF
chmod +x /root/no2.sh
echo "/root/no2.sh siap"
```

#### Langkah 9 — Fuyutsuki: Edit config, lalu buat script uji di console

**a. Edit config:**

```
# eth0 -> Gendo  (192.231.4.132/30)
auto eth0
iface eth0 inet static
    address 192.231.4.134
    netmask 255.255.255.252
    up echo 1 > /proc/sys/net/ipv4/ip_forward

# eth1 -> Maya  (192.231.4.164/30)
auto eth1
iface eth1 inet static
    address 192.231.4.165
    netmask 255.255.255.252

# eth2 -> Toji  (192.231.4.168/30)
auto eth2
iface eth2 inet static
    address 192.231.4.169
    netmask 255.255.255.252

# eth3 -> Switch-Terminal  (192.231.4.56/29)
auto eth3
iface eth3 inet static
    address 192.231.4.57
    netmask 255.255.255.248
```

**b. Console** (sekali paste):

```sh
cat > /root/no2.sh << 'EOF'
#!/bin/bash
# Soal 2 - Fuyutsuki: IP interface + IP forwarding

echo "=== [Fuyutsuki] Konfigurasi IP ==="
# eth0 -> Gendo
ip link set eth0 up
ip addr replace 192.231.4.134/30 dev eth0
# eth1 -> Maya
ip link set eth1 up
ip addr replace 192.231.4.165/30 dev eth1
# eth2 -> Toji
ip link set eth2 up
ip addr replace 192.231.4.169/30 dev eth2
# eth3 -> Switch-Terminal
ip link set eth3 up
ip addr replace 192.231.4.57/29 dev eth3

# IP forwarding
sysctl -w net.ipv4.ip_forward=1

echo "=== ip -br a ==="
ip -br a

echo "=== ip_forward = $(cat /proc/sys/net/ipv4/ip_forward) ==="

echo "=== Uji ping ke router tetangga ==="
ping -c 3 -W 1 192.231.4.133 > /tmp/p.txt 2>&1 && echo "[OK]   Gendo (192.231.4.133)" || echo "[GAGAL] Gendo (192.231.4.133)"; tail -2 /tmp/p.txt | head -1
ping -c 3 -W 1 192.231.4.166 > /tmp/p.txt 2>&1 && echo "[OK]   Maya (192.231.4.166)" || echo "[GAGAL] Maya (192.231.4.166)"; tail -2 /tmp/p.txt | head -1
ping -c 3 -W 1 192.231.4.170 > /tmp/p.txt 2>&1 && echo "[OK]   Toji (192.231.4.170)" || echo "[GAGAL] Toji (192.231.4.170)"; tail -2 /tmp/p.txt | head -1
EOF
chmod +x /root/no2.sh
echo "/root/no2.sh siap"
```

#### Langkah 10 — Mari: Edit config, lalu buat script uji di console

**a. Edit config:**

```
# eth0 -> Yui  (192.231.4.136/30)
auto eth0
iface eth0 inet static
    address 192.231.4.138
    netmask 255.255.255.252
    up echo 1 > /proc/sys/net/ipv4/ip_forward

# eth1 -> Switch-Dock  (192.231.4.32/28)
auto eth1
iface eth1 inet static
    address 192.231.4.33
    netmask 255.255.255.240
```

**b. Console** (sekali paste):

```sh
cat > /root/no2.sh << 'EOF'
#!/bin/bash
# Soal 2 - Mari: IP interface + IP forwarding

echo "=== [Mari] Konfigurasi IP ==="
# eth0 -> Yui
ip link set eth0 up
ip addr replace 192.231.4.138/30 dev eth0
# eth1 -> Switch-Dock
ip link set eth1 up
ip addr replace 192.231.4.33/28 dev eth1

# IP forwarding
sysctl -w net.ipv4.ip_forward=1

echo "=== ip -br a ==="
ip -br a

echo "=== ip_forward = $(cat /proc/sys/net/ipv4/ip_forward) ==="

echo "=== Uji ping ke router tetangga ==="
ping -c 3 -W 1 192.231.4.137 > /tmp/p.txt 2>&1 && echo "[OK]   Yui (192.231.4.137)" || echo "[GAGAL] Yui (192.231.4.137)"; tail -2 /tmp/p.txt | head -1
EOF
chmod +x /root/no2.sh
echo "/root/no2.sh siap"
```

#### Langkah 11 — Rei: Edit config, lalu buat script uji di console

**a. Edit config:**

```
# eth0 -> Yui  (192.231.4.140/30)
auto eth0
iface eth0 inet static
    address 192.231.4.142
    netmask 255.255.255.252
    up echo 1 > /proc/sys/net/ipv4/ip_forward

# eth1 -> Switch-Quarters  (192.231.0.0/23)
auto eth1
iface eth1 inet static
    address 192.231.0.1
    netmask 255.255.254.0
```

**b. Console** (sekali paste):

```sh
cat > /root/no2.sh << 'EOF'
#!/bin/bash
# Soal 2 - Rei: IP interface + IP forwarding

echo "=== [Rei] Konfigurasi IP ==="
# eth0 -> Yui
ip link set eth0 up
ip addr replace 192.231.4.142/30 dev eth0
# eth1 -> Switch-Quarters
ip link set eth1 up
ip addr replace 192.231.0.1/23 dev eth1

# IP forwarding
sysctl -w net.ipv4.ip_forward=1

echo "=== ip -br a ==="
ip -br a

echo "=== ip_forward = $(cat /proc/sys/net/ipv4/ip_forward) ==="

echo "=== Uji ping ke router tetangga ==="
ping -c 3 -W 1 192.231.4.141 > /tmp/p.txt 2>&1 && echo "[OK]   Yui (192.231.4.141)" || echo "[GAGAL] Yui (192.231.4.141)"; tail -2 /tmp/p.txt | head -1
EOF
chmod +x /root/no2.sh
echo "/root/no2.sh siap"
```

#### Langkah 12 — Shinji: Edit config, lalu buat script uji di console

**a. Edit config:**

```
# eth0 -> Yui  (192.231.4.144/30)
auto eth0
iface eth0 inet static
    address 192.231.4.146
    netmask 255.255.255.252
    up echo 1 > /proc/sys/net/ipv4/ip_forward

# eth1 -> Switch-Garden  (192.231.4.0/27)
auto eth1
iface eth1 inet static
    address 192.231.4.1
    netmask 255.255.255.224
```

**b. Console** (sekali paste):

```sh
cat > /root/no2.sh << 'EOF'
#!/bin/bash
# Soal 2 - Shinji: IP interface + IP forwarding

echo "=== [Shinji] Konfigurasi IP ==="
# eth0 -> Yui
ip link set eth0 up
ip addr replace 192.231.4.146/30 dev eth0
# eth1 -> Switch-Garden
ip link set eth1 up
ip addr replace 192.231.4.1/27 dev eth1

# IP forwarding
sysctl -w net.ipv4.ip_forward=1

echo "=== ip -br a ==="
ip -br a

echo "=== ip_forward = $(cat /proc/sys/net/ipv4/ip_forward) ==="

echo "=== Uji ping ke router tetangga ==="
ping -c 3 -W 1 192.231.4.145 > /tmp/p.txt 2>&1 && echo "[OK]   Yui (192.231.4.145)" || echo "[GAGAL] Yui (192.231.4.145)"; tail -2 /tmp/p.txt | head -1
EOF
chmod +x /root/no2.sh
echo "/root/no2.sh siap"
```

#### Langkah 13 — Kaworu: Edit config, lalu buat script uji di console

**a. Edit config:**

```
# eth0 -> Hikari  (192.231.4.148/30)
auto eth0
iface eth0 inet static
    address 192.231.4.150
    netmask 255.255.255.252
    up echo 1 > /proc/sys/net/ipv4/ip_forward

# eth1 -> Switch-Relay  (192.231.3.224/27)
auto eth1
iface eth1 inet static
    address 192.231.3.225
    netmask 255.255.255.224
```

**b. Console** (sekali paste):

```sh
cat > /root/no2.sh << 'EOF'
#!/bin/bash
# Soal 2 - Kaworu: IP interface + IP forwarding

echo "=== [Kaworu] Konfigurasi IP ==="
# eth0 -> Hikari
ip link set eth0 up
ip addr replace 192.231.4.150/30 dev eth0
# eth1 -> Switch-Relay
ip link set eth1 up
ip addr replace 192.231.3.225/27 dev eth1

# IP forwarding
sysctl -w net.ipv4.ip_forward=1

echo "=== ip -br a ==="
ip -br a

echo "=== ip_forward = $(cat /proc/sys/net/ipv4/ip_forward) ==="

echo "=== Uji ping ke router tetangga ==="
ping -c 3 -W 1 192.231.4.149 > /tmp/p.txt 2>&1 && echo "[OK]   Hikari (192.231.4.149)" || echo "[GAGAL] Hikari (192.231.4.149)"; tail -2 /tmp/p.txt | head -1
EOF
chmod +x /root/no2.sh
echo "/root/no2.sh siap"
```

#### Langkah 14 — Kensuke: Edit config, lalu buat script uji di console

**a. Edit config:**

```
# eth0 -> Asuka  (192.231.4.152/30)
auto eth0
iface eth0 inet static
    address 192.231.4.154
    netmask 255.255.255.252
    up echo 1 > /proc/sys/net/ipv4/ip_forward

# eth1 -> Switch-Bunker  (192.231.3.128/26)
auto eth1
iface eth1 inet static
    address 192.231.3.129
    netmask 255.255.255.192
```

**b. Console** (sekali paste):

```sh
cat > /root/no2.sh << 'EOF'
#!/bin/bash
# Soal 2 - Kensuke: IP interface + IP forwarding

echo "=== [Kensuke] Konfigurasi IP ==="
# eth0 -> Asuka
ip link set eth0 up
ip addr replace 192.231.4.154/30 dev eth0
# eth1 -> Switch-Bunker
ip link set eth1 up
ip addr replace 192.231.3.129/26 dev eth1

# IP forwarding
sysctl -w net.ipv4.ip_forward=1

echo "=== ip -br a ==="
ip -br a

echo "=== ip_forward = $(cat /proc/sys/net/ipv4/ip_forward) ==="

echo "=== Uji ping ke router tetangga ==="
ping -c 3 -W 1 192.231.4.153 > /tmp/p.txt 2>&1 && echo "[OK]   Asuka (192.231.4.153)" || echo "[GAGAL] Asuka (192.231.4.153)"; tail -2 /tmp/p.txt | head -1
EOF
chmod +x /root/no2.sh
echo "/root/no2.sh siap"
```

#### Langkah 15 — Ritsuko: Edit config, lalu buat script uji di console

**a. Edit config:**

```
# eth0 -> Asuka  (192.231.4.156/30)
auto eth0
iface eth0 inet static
    address 192.231.4.158
    netmask 255.255.255.252
    up echo 1 > /proc/sys/net/ipv4/ip_forward

# eth1 -> Switch-Lab  (192.231.4.48/29)
auto eth1
iface eth1 inet static
    address 192.231.4.49
    netmask 255.255.255.248
```

**b. Console** (sekali paste):

```sh
cat > /root/no2.sh << 'EOF'
#!/bin/bash
# Soal 2 - Ritsuko: IP interface + IP forwarding

echo "=== [Ritsuko] Konfigurasi IP ==="
# eth0 -> Asuka
ip link set eth0 up
ip addr replace 192.231.4.158/30 dev eth0
# eth1 -> Switch-Lab
ip link set eth1 up
ip addr replace 192.231.4.49/29 dev eth1

# IP forwarding
sysctl -w net.ipv4.ip_forward=1

echo "=== ip -br a ==="
ip -br a

echo "=== ip_forward = $(cat /proc/sys/net/ipv4/ip_forward) ==="

echo "=== Uji ping ke router tetangga ==="
ping -c 3 -W 1 192.231.4.157 > /tmp/p.txt 2>&1 && echo "[OK]   Asuka (192.231.4.157)" || echo "[GAGAL] Asuka (192.231.4.157)"; tail -2 /tmp/p.txt | head -1
EOF
chmod +x /root/no2.sh
echo "/root/no2.sh siap"
```

#### Langkah 16 — Makoto: Edit config, lalu buat script uji di console

**a. Edit config:**

```
# eth0 -> Shigeru  (192.231.4.160/30)
auto eth0
iface eth0 inet static
    address 192.231.4.162
    netmask 255.255.255.252
    up echo 1 > /proc/sys/net/ipv4/ip_forward

# eth1 -> Switch-Radar  (192.231.4.88/29)
auto eth1
iface eth1 inet static
    address 192.231.4.89
    netmask 255.255.255.248
```

**b. Console** (sekali paste):

```sh
cat > /root/no2.sh << 'EOF'
#!/bin/bash
# Soal 2 - Makoto: IP interface + IP forwarding

echo "=== [Makoto] Konfigurasi IP ==="
# eth0 -> Shigeru
ip link set eth0 up
ip addr replace 192.231.4.162/30 dev eth0
# eth1 -> Switch-Radar
ip link set eth1 up
ip addr replace 192.231.4.89/29 dev eth1

# IP forwarding
sysctl -w net.ipv4.ip_forward=1

echo "=== ip -br a ==="
ip -br a

echo "=== ip_forward = $(cat /proc/sys/net/ipv4/ip_forward) ==="

echo "=== Uji ping ke router tetangga ==="
ping -c 3 -W 1 192.231.4.161 > /tmp/p.txt 2>&1 && echo "[OK]   Shigeru (192.231.4.161)" || echo "[GAGAL] Shigeru (192.231.4.161)"; tail -2 /tmp/p.txt | head -1
EOF
chmod +x /root/no2.sh
echo "/root/no2.sh siap"
```

#### Langkah 17 — Maya: Edit config, lalu buat script uji di console

**a. Edit config:**

```
# eth0 -> Fuyutsuki  (192.231.4.164/30)
auto eth0
iface eth0 inet static
    address 192.231.4.166
    netmask 255.255.255.252
    up echo 1 > /proc/sys/net/ipv4/ip_forward

# eth1 -> Switch-Medic  (192.231.3.192/27)
auto eth1
iface eth1 inet static
    address 192.231.3.193
    netmask 255.255.255.224
```

**b. Console** (sekali paste):

```sh
cat > /root/no2.sh << 'EOF'
#!/bin/bash
# Soal 2 - Maya: IP interface + IP forwarding

echo "=== [Maya] Konfigurasi IP ==="
# eth0 -> Fuyutsuki
ip link set eth0 up
ip addr replace 192.231.4.166/30 dev eth0
# eth1 -> Switch-Medic
ip link set eth1 up
ip addr replace 192.231.3.193/27 dev eth1

# IP forwarding
sysctl -w net.ipv4.ip_forward=1

echo "=== ip -br a ==="
ip -br a

echo "=== ip_forward = $(cat /proc/sys/net/ipv4/ip_forward) ==="

echo "=== Uji ping ke router tetangga ==="
ping -c 3 -W 1 192.231.4.165 > /tmp/p.txt 2>&1 && echo "[OK]   Fuyutsuki (192.231.4.165)" || echo "[GAGAL] Fuyutsuki (192.231.4.165)"; tail -2 /tmp/p.txt | head -1
EOF
chmod +x /root/no2.sh
echo "/root/no2.sh siap"
```

#### Langkah 18 — Toji: Edit config, lalu buat script uji di console

**a. Edit config:**

```
# eth0 -> Fuyutsuki  (192.231.4.168/30)
auto eth0
iface eth0 inet static
    address 192.231.4.170
    netmask 255.255.255.252
    up echo 1 > /proc/sys/net/ipv4/ip_forward

# eth1 -> Switch-Dogma  (192.231.4.72/29)
auto eth1
iface eth1 inet static
    address 192.231.4.73
    netmask 255.255.255.248
```

**b. Console** (sekali paste):

```sh
cat > /root/no2.sh << 'EOF'
#!/bin/bash
# Soal 2 - Toji: IP interface + IP forwarding

echo "=== [Toji] Konfigurasi IP ==="
# eth0 -> Fuyutsuki
ip link set eth0 up
ip addr replace 192.231.4.170/30 dev eth0
# eth1 -> Switch-Dogma
ip link set eth1 up
ip addr replace 192.231.4.73/29 dev eth1

# IP forwarding
sysctl -w net.ipv4.ip_forward=1

echo "=== ip -br a ==="
ip -br a

echo "=== ip_forward = $(cat /proc/sys/net/ipv4/ip_forward) ==="

echo "=== Uji ping ke router tetangga ==="
ping -c 3 -W 1 192.231.4.169 > /tmp/p.txt 2>&1 && echo "[OK]   Fuyutsuki (192.231.4.169)" || echo "[GAGAL] Fuyutsuki (192.231.4.169)"; tail -2 /tmp/p.txt | head -1
EOF
chmod +x /root/no2.sh
echo "/root/no2.sh siap"
```

#### Langkah 19 — Start semua router, lalu jalankan uji di setiap router

```sh
/root/no2.sh
```

Script menampilkan `ip -br a`, `ip_forward`, dan ping ke setiap router tetangga (`[OK]` / `[GAGAL]`).

### Hasil

- Semua **16 link** berhasil ping dua arah, `ip_forward = 1` di semua router.
- Gendo eth0 mendapat **192.168.122.5/24** dari NAT1 (DHCP).

---

<a id="soal-3"></a>
## Soal 3 — Pulau-pulau yang terpisah (routing statis)

Router selain Gendo: **1 default route ke atas** + rute ke subnet di bawahnya. Gendo: default dari DHCP NAT1 + rute ke semua cabang. Subnet satu cabang yang bisa digabung tepat **diringkas**:

| Cabang | Digabung | Hasil |
|---|---|---|
| Shigeru | Monolith `4.80/29` + Radar `4.88/29` | **192.231.4.80/28** |
| Yui | Yui–Mari `4.136/30` + Yui–Rei `4.140/30` | **192.231.4.136/29** |
| Asuka | Asuka–Kensuke `4.152/30` + Asuka–Ritsuko `4.156/30` | **192.231.4.152/29** |

Fuyutsuki–Maya `4.164/30` dan Fuyutsuki–Toji `4.168/30` tidak bisa diringkas (CIDR gabungannya ikut memuat `4.160/30` milik cabang Shigeru). Jumlah rute: Gendo 22 (+ default DHCP), Yui 4, Asuka 3, Fuyutsuki 3, Hikari 2, Shigeru 2, lainnya 1.

<details>
<summary><b>Daftar lengkap rute per router</b></summary>

| Router | Tujuan | Via (next hop) | Interface | Ke router |
|---|---|---|---|---|
| Gendo | 192.231.3.192/27 | 192.231.4.134 | eth2 | Fuyutsuki |
| Gendo | 192.231.4.56/29 | 192.231.4.134 | eth2 | Fuyutsuki |
| Gendo | 192.231.4.72/29 | 192.231.4.134 | eth2 | Fuyutsuki |
| Gendo | 192.231.4.164/30 | 192.231.4.134 | eth2 | Fuyutsuki |
| Gendo | 192.231.4.168/30 | 192.231.4.134 | eth2 | Fuyutsuki |
| Gendo | 192.231.4.80/28 | 192.231.4.130 | eth3 | Shigeru |
| Gendo | 192.231.4.160/30 | 192.231.4.130 | eth3 | Shigeru |
| Gendo | 192.231.4.64/29 | 192.231.4.126 | eth4 | Misato |
| Gendo | 192.231.4.100/30 | 192.231.4.122 | eth5 | Kaji |
| Gendo | 192.231.3.128/26 | 192.231.4.118 | eth6 | Asuka |
| Gendo | 192.231.4.48/29 | 192.231.4.118 | eth6 | Asuka |
| Gendo | 192.231.4.96/30 | 192.231.4.118 | eth6 | Asuka |
| Gendo | 192.231.4.152/29 | 192.231.4.118 | eth6 | Asuka |
| Gendo | 192.231.2.0/24 | 192.231.4.114 | eth7 | Hikari |
| Gendo | 192.231.3.224/27 | 192.231.4.114 | eth7 | Hikari |
| Gendo | 192.231.4.148/30 | 192.231.4.114 | eth7 | Hikari |
| Gendo | 192.231.0.0/23 | 192.231.4.110 | eth8 | Yui |
| Gendo | 192.231.3.0/25 | 192.231.4.110 | eth8 | Yui |
| Gendo | 192.231.4.0/27 | 192.231.4.110 | eth8 | Yui |
| Gendo | 192.231.4.32/28 | 192.231.4.110 | eth8 | Yui |
| Gendo | 192.231.4.136/29 | 192.231.4.110 | eth8 | Yui |
| Gendo | 192.231.4.144/30 | 192.231.4.110 | eth8 | Yui |
| Yui | 192.231.4.32/28 | 192.231.4.138 | eth1 | Mari |
| Yui | 192.231.0.0/23 | 192.231.4.142 | eth2 | Rei |
| Yui | 192.231.4.0/27 | 192.231.4.146 | eth3 | Shinji |
| Yui | default | 192.231.4.109 | eth0 | Gendo |
| Hikari | 192.231.3.224/27 | 192.231.4.150 | eth1 | Kaworu |
| Hikari | default | 192.231.4.113 | eth0 | Gendo |
| Asuka | 192.231.4.48/29 | 192.231.4.158 | eth1 | Ritsuko |
| Asuka | 192.231.3.128/26 | 192.231.4.154 | eth2 | Kensuke |
| Asuka | default | 192.231.4.117 | eth0 | Gendo |
| Kaji | default | 192.231.4.121 | eth0 | Gendo |
| Misato | default | 192.231.4.125 | eth0 | Gendo |
| Shigeru | 192.231.4.88/29 | 192.231.4.162 | eth1 | Makoto |
| Shigeru | default | 192.231.4.129 | eth0 | Gendo |
| Fuyutsuki | 192.231.3.192/27 | 192.231.4.166 | eth1 | Maya |
| Fuyutsuki | 192.231.4.72/29 | 192.231.4.170 | eth2 | Toji |
| Fuyutsuki | default | 192.231.4.133 | eth0 | Gendo |
| Mari | default | 192.231.4.137 | eth0 | Yui |
| Rei | default | 192.231.4.141 | eth0 | Yui |
| Shinji | default | 192.231.4.145 | eth0 | Yui |
| Kaworu | default | 192.231.4.149 | eth0 | Hikari |
| Kensuke | default | 192.231.4.153 | eth0 | Asuka |
| Ritsuko | default | 192.231.4.157 | eth0 | Asuka |
| Makoto | default | 192.231.4.161 | eth0 | Shigeru |
| Maya | default | 192.231.4.165 | eth0 | Fuyutsuki |
| Toji | default | 192.231.4.169 | eth0 | Fuyutsuki |
| Gendo | default | (dari DHCP NAT1, otomatis) | eth0 | NAT1 |

Jumlah rute: Gendo 22, Yui 4, Hikari 2, Asuka 3, Kaji 1, Misato 1, Shigeru 2, Fuyutsuki 3, Mari 1, Rei 1, Shinji 1, Kaworu 1, Kensuke 1, Ritsuko 1, Makoto 1, Maya 1, Toji 1 (+ default DHCP di Gendo)

</details>

#### Langkah 1 — Gendo: Edit config permanen (IP + rute), lalu script demo

**a. Edit config** (menggantikan config soal 2):

```
# eth0 -> NAT1
auto eth0
iface eth0 inet dhcp

# eth1 -> Switch-Cmd  (192.231.4.104/30)
auto eth1
iface eth1 inet static
    address 192.231.4.105
    netmask 255.255.255.252
    up echo 1 > /proc/sys/net/ipv4/ip_forward

# eth2 -> Fuyutsuki  (192.231.4.132/30)
auto eth2
iface eth2 inet static
    address 192.231.4.133
    netmask 255.255.255.252
    up ip route add 192.231.3.192/27 via 192.231.4.134
    up ip route add 192.231.4.56/29 via 192.231.4.134
    up ip route add 192.231.4.72/29 via 192.231.4.134
    up ip route add 192.231.4.164/30 via 192.231.4.134
    up ip route add 192.231.4.168/30 via 192.231.4.134

# eth3 -> Shigeru  (192.231.4.128/30)
auto eth3
iface eth3 inet static
    address 192.231.4.129
    netmask 255.255.255.252
    up ip route add 192.231.4.80/28 via 192.231.4.130
    up ip route add 192.231.4.160/30 via 192.231.4.130

# eth4 -> Misato  (192.231.4.124/30)
auto eth4
iface eth4 inet static
    address 192.231.4.125
    netmask 255.255.255.252
    up ip route add 192.231.4.64/29 via 192.231.4.126

# eth5 -> Kaji  (192.231.4.120/30)
auto eth5
iface eth5 inet static
    address 192.231.4.121
    netmask 255.255.255.252
    up ip route add 192.231.4.100/30 via 192.231.4.122

# eth6 -> Asuka  (192.231.4.116/30)
auto eth6
iface eth6 inet static
    address 192.231.4.117
    netmask 255.255.255.252
    up ip route add 192.231.3.128/26 via 192.231.4.118
    up ip route add 192.231.4.48/29 via 192.231.4.118
    up ip route add 192.231.4.96/30 via 192.231.4.118
    up ip route add 192.231.4.152/29 via 192.231.4.118

# eth7 -> Hikari  (192.231.4.112/30)
auto eth7
iface eth7 inet static
    address 192.231.4.113
    netmask 255.255.255.252
    up ip route add 192.231.2.0/24 via 192.231.4.114
    up ip route add 192.231.3.224/27 via 192.231.4.114
    up ip route add 192.231.4.148/30 via 192.231.4.114

# eth8 -> Yui  (192.231.4.108/30)
auto eth8
iface eth8 inet static
    address 192.231.4.109
    netmask 255.255.255.252
    up ip route add 192.231.0.0/23 via 192.231.4.110
    up ip route add 192.231.3.0/25 via 192.231.4.110
    up ip route add 192.231.4.0/27 via 192.231.4.110
    up ip route add 192.231.4.32/28 via 192.231.4.110
    up ip route add 192.231.4.136/29 via 192.231.4.110
    up ip route add 192.231.4.144/30 via 192.231.4.110
```

**b. Console:**

```sh
cat > /root/no3.sh << 'EOF'
#!/bin/bash
# Soal 3 - Gendo: routing statis

echo "=== [Gendo] Routing statis ==="
# default route ke NAT1 berasal dari DHCP (eth0)
if ! ip route | grep -q '^default'; then
  echo 'Default route belum ada, minta DHCP ke NAT1...'
  dhclient -v eth0 2>/dev/null || /gns3/bin/busybox udhcpc -i eth0 -q
fi
# subnet di bawah Fuyutsuki (eth2)
ip route replace 192.231.3.192/27 via 192.231.4.134
ip route replace 192.231.4.56/29 via 192.231.4.134
ip route replace 192.231.4.72/29 via 192.231.4.134
ip route replace 192.231.4.164/30 via 192.231.4.134
ip route replace 192.231.4.168/30 via 192.231.4.134
# subnet di bawah Shigeru (eth3)
ip route replace 192.231.4.80/28 via 192.231.4.130
ip route replace 192.231.4.160/30 via 192.231.4.130
# subnet di bawah Misato (eth4)
ip route replace 192.231.4.64/29 via 192.231.4.126
# subnet di bawah Kaji (eth5)
ip route replace 192.231.4.100/30 via 192.231.4.122
# subnet di bawah Asuka (eth6)
ip route replace 192.231.3.128/26 via 192.231.4.118
ip route replace 192.231.4.48/29 via 192.231.4.118
ip route replace 192.231.4.96/30 via 192.231.4.118
ip route replace 192.231.4.152/29 via 192.231.4.118
# subnet di bawah Hikari (eth7)
ip route replace 192.231.2.0/24 via 192.231.4.114
ip route replace 192.231.3.224/27 via 192.231.4.114
ip route replace 192.231.4.148/30 via 192.231.4.114
# subnet di bawah Yui (eth8)
ip route replace 192.231.0.0/23 via 192.231.4.110
ip route replace 192.231.3.0/25 via 192.231.4.110
ip route replace 192.231.4.0/27 via 192.231.4.110
ip route replace 192.231.4.32/28 via 192.231.4.110
ip route replace 192.231.4.136/29 via 192.231.4.110
ip route replace 192.231.4.144/30 via 192.231.4.110

echo "=== ip route ==="
ip route
echo "=== Jumlah rute statis yang ditambahkan: 22 + default DHCP ==="
EOF
chmod +x /root/no3.sh
echo "/root/no3.sh siap"
```

**c. Jalankan:** `/root/no3.sh`

#### Langkah 2 — Yui: Edit config permanen (IP + rute), lalu script demo

**a. Edit config** (menggantikan config soal 2):

```
# eth0 -> Gendo  (192.231.4.108/30)
auto eth0
iface eth0 inet static
    address 192.231.4.110
    netmask 255.255.255.252
    up echo 1 > /proc/sys/net/ipv4/ip_forward
    up ip route add default via 192.231.4.109

# eth1 -> Mari  (192.231.4.136/30)
auto eth1
iface eth1 inet static
    address 192.231.4.137
    netmask 255.255.255.252
    up ip route add 192.231.4.32/28 via 192.231.4.138

# eth2 -> Rei  (192.231.4.140/30)
auto eth2
iface eth2 inet static
    address 192.231.4.141
    netmask 255.255.255.252
    up ip route add 192.231.0.0/23 via 192.231.4.142

# eth3 -> Shinji  (192.231.4.144/30)
auto eth3
iface eth3 inet static
    address 192.231.4.145
    netmask 255.255.255.252
    up ip route add 192.231.4.0/27 via 192.231.4.146

# eth4 -> Switch-Hangar  (192.231.3.0/25)
auto eth4
iface eth4 inet static
    address 192.231.3.1
    netmask 255.255.255.128
```

**b. Console:**

```sh
cat > /root/no3.sh << 'EOF'
#!/bin/bash
# Soal 3 - Yui: routing statis

echo "=== [Yui] Routing statis ==="
# default route ke atas (Gendo)
ip route replace default via 192.231.4.109
# subnet di bawah Mari (eth1)
ip route replace 192.231.4.32/28 via 192.231.4.138
# subnet di bawah Rei (eth2)
ip route replace 192.231.0.0/23 via 192.231.4.142
# subnet di bawah Shinji (eth3)
ip route replace 192.231.4.0/27 via 192.231.4.146

echo "=== ip route ==="
ip route
echo "=== Jumlah rute statis yang ditambahkan: 4 ==="
EOF
chmod +x /root/no3.sh
echo "/root/no3.sh siap"
```

**c. Jalankan:** `/root/no3.sh`

#### Langkah 3 — Hikari: Edit config permanen (IP + rute), lalu script demo

**a. Edit config** (menggantikan config soal 2):

```
# eth0 -> Gendo  (192.231.4.112/30)
auto eth0
iface eth0 inet static
    address 192.231.4.114
    netmask 255.255.255.252
    up echo 1 > /proc/sys/net/ipv4/ip_forward
    up ip route add default via 192.231.4.113

# eth1 -> Kaworu  (192.231.4.148/30)
auto eth1
iface eth1 inet static
    address 192.231.4.149
    netmask 255.255.255.252
    up ip route add 192.231.3.224/27 via 192.231.4.150

# eth2 -> Switch-Comm  (192.231.2.0/24)
auto eth2
iface eth2 inet static
    address 192.231.2.1
    netmask 255.255.255.0
```

**b. Console:**

```sh
cat > /root/no3.sh << 'EOF'
#!/bin/bash
# Soal 3 - Hikari: routing statis

echo "=== [Hikari] Routing statis ==="
# default route ke atas (Gendo)
ip route replace default via 192.231.4.113
# subnet di bawah Kaworu (eth1)
ip route replace 192.231.3.224/27 via 192.231.4.150

echo "=== ip route ==="
ip route
echo "=== Jumlah rute statis yang ditambahkan: 2 ==="
EOF
chmod +x /root/no3.sh
echo "/root/no3.sh siap"
```

**c. Jalankan:** `/root/no3.sh`

#### Langkah 4 — Asuka: Edit config permanen (IP + rute), lalu script demo

**a. Edit config** (menggantikan config soal 2):

```
# eth0 -> Gendo  (192.231.4.116/30)
auto eth0
iface eth0 inet static
    address 192.231.4.118
    netmask 255.255.255.252
    up echo 1 > /proc/sys/net/ipv4/ip_forward
    up ip route add default via 192.231.4.117

# eth1 -> Ritsuko  (192.231.4.156/30)
auto eth1
iface eth1 inet static
    address 192.231.4.157
    netmask 255.255.255.252
    up ip route add 192.231.4.48/29 via 192.231.4.158

# eth2 -> Kensuke  (192.231.4.152/30)
auto eth2
iface eth2 inet static
    address 192.231.4.153
    netmask 255.255.255.252
    up ip route add 192.231.3.128/26 via 192.231.4.154

# eth3 -> Switch-Archive  (192.231.4.96/30)
auto eth3
iface eth3 inet static
    address 192.231.4.97
    netmask 255.255.255.252
```

**b. Console:**

```sh
cat > /root/no3.sh << 'EOF'
#!/bin/bash
# Soal 3 - Asuka: routing statis

echo "=== [Asuka] Routing statis ==="
# default route ke atas (Gendo)
ip route replace default via 192.231.4.117
# subnet di bawah Ritsuko (eth1)
ip route replace 192.231.4.48/29 via 192.231.4.158
# subnet di bawah Kensuke (eth2)
ip route replace 192.231.3.128/26 via 192.231.4.154

echo "=== ip route ==="
ip route
echo "=== Jumlah rute statis yang ditambahkan: 3 ==="
EOF
chmod +x /root/no3.sh
echo "/root/no3.sh siap"
```

**c. Jalankan:** `/root/no3.sh`

#### Langkah 5 — Kaji: Edit config permanen (IP + rute), lalu script demo

**a. Edit config** (menggantikan config soal 2):

```
# eth0 -> Gendo  (192.231.4.120/30)
auto eth0
iface eth0 inet static
    address 192.231.4.122
    netmask 255.255.255.252
    up echo 1 > /proc/sys/net/ipv4/ip_forward
    up ip route add default via 192.231.4.121

# eth1 -> Switch-Intel  (192.231.4.100/30)
auto eth1
iface eth1 inet static
    address 192.231.4.101
    netmask 255.255.255.252
```

**b. Console:**

```sh
cat > /root/no3.sh << 'EOF'
#!/bin/bash
# Soal 3 - Kaji: routing statis

echo "=== [Kaji] Routing statis ==="
# default route ke atas (Gendo)
ip route replace default via 192.231.4.121

echo "=== ip route ==="
ip route
echo "=== Jumlah rute statis yang ditambahkan: 1 ==="
EOF
chmod +x /root/no3.sh
echo "/root/no3.sh siap"
```

**c. Jalankan:** `/root/no3.sh`

#### Langkah 6 — Misato: Edit config permanen (IP + rute), lalu script demo

**a. Edit config** (menggantikan config soal 2):

```
# eth0 -> Gendo  (192.231.4.124/30)
auto eth0
iface eth0 inet static
    address 192.231.4.126
    netmask 255.255.255.252
    up echo 1 > /proc/sys/net/ipv4/ip_forward
    up ip route add default via 192.231.4.125

# eth1 -> Switch-Cage  (192.231.4.64/29)
auto eth1
iface eth1 inet static
    address 192.231.4.65
    netmask 255.255.255.248
```

**b. Console:**

```sh
cat > /root/no3.sh << 'EOF'
#!/bin/bash
# Soal 3 - Misato: routing statis

echo "=== [Misato] Routing statis ==="
# default route ke atas (Gendo)
ip route replace default via 192.231.4.125

echo "=== ip route ==="
ip route
echo "=== Jumlah rute statis yang ditambahkan: 1 ==="
EOF
chmod +x /root/no3.sh
echo "/root/no3.sh siap"
```

**c. Jalankan:** `/root/no3.sh`

#### Langkah 7 — Shigeru: Edit config permanen (IP + rute), lalu script demo

**a. Edit config** (menggantikan config soal 2):

```
# eth0 -> Gendo  (192.231.4.128/30)
auto eth0
iface eth0 inet static
    address 192.231.4.130
    netmask 255.255.255.252
    up echo 1 > /proc/sys/net/ipv4/ip_forward
    up ip route add default via 192.231.4.129

# eth1 -> Makoto  (192.231.4.160/30)
auto eth1
iface eth1 inet static
    address 192.231.4.161
    netmask 255.255.255.252
    up ip route add 192.231.4.88/29 via 192.231.4.162

# eth2 -> Switch-Monolith  (192.231.4.80/29)
auto eth2
iface eth2 inet static
    address 192.231.4.81
    netmask 255.255.255.248
```

**b. Console:**

```sh
cat > /root/no3.sh << 'EOF'
#!/bin/bash
# Soal 3 - Shigeru: routing statis

echo "=== [Shigeru] Routing statis ==="
# default route ke atas (Gendo)
ip route replace default via 192.231.4.129
# subnet di bawah Makoto (eth1)
ip route replace 192.231.4.88/29 via 192.231.4.162

echo "=== ip route ==="
ip route
echo "=== Jumlah rute statis yang ditambahkan: 2 ==="
EOF
chmod +x /root/no3.sh
echo "/root/no3.sh siap"
```

**c. Jalankan:** `/root/no3.sh`

#### Langkah 8 — Fuyutsuki: Edit config permanen (IP + rute), lalu script demo

**a. Edit config** (menggantikan config soal 2):

```
# eth0 -> Gendo  (192.231.4.132/30)
auto eth0
iface eth0 inet static
    address 192.231.4.134
    netmask 255.255.255.252
    up echo 1 > /proc/sys/net/ipv4/ip_forward
    up ip route add default via 192.231.4.133

# eth1 -> Maya  (192.231.4.164/30)
auto eth1
iface eth1 inet static
    address 192.231.4.165
    netmask 255.255.255.252
    up ip route add 192.231.3.192/27 via 192.231.4.166

# eth2 -> Toji  (192.231.4.168/30)
auto eth2
iface eth2 inet static
    address 192.231.4.169
    netmask 255.255.255.252
    up ip route add 192.231.4.72/29 via 192.231.4.170

# eth3 -> Switch-Terminal  (192.231.4.56/29)
auto eth3
iface eth3 inet static
    address 192.231.4.57
    netmask 255.255.255.248
```

**b. Console:**

```sh
cat > /root/no3.sh << 'EOF'
#!/bin/bash
# Soal 3 - Fuyutsuki: routing statis

echo "=== [Fuyutsuki] Routing statis ==="
# default route ke atas (Gendo)
ip route replace default via 192.231.4.133
# subnet di bawah Maya (eth1)
ip route replace 192.231.3.192/27 via 192.231.4.166
# subnet di bawah Toji (eth2)
ip route replace 192.231.4.72/29 via 192.231.4.170

echo "=== ip route ==="
ip route
echo "=== Jumlah rute statis yang ditambahkan: 3 ==="
EOF
chmod +x /root/no3.sh
echo "/root/no3.sh siap"
```

**c. Jalankan:** `/root/no3.sh`

#### Langkah 9 — Mari: Edit config permanen (IP + rute), lalu script demo

**a. Edit config** (menggantikan config soal 2):

```
# eth0 -> Yui  (192.231.4.136/30)
auto eth0
iface eth0 inet static
    address 192.231.4.138
    netmask 255.255.255.252
    up echo 1 > /proc/sys/net/ipv4/ip_forward
    up ip route add default via 192.231.4.137

# eth1 -> Switch-Dock  (192.231.4.32/28)
auto eth1
iface eth1 inet static
    address 192.231.4.33
    netmask 255.255.255.240
```

**b. Console:**

```sh
cat > /root/no3.sh << 'EOF'
#!/bin/bash
# Soal 3 - Mari: routing statis

echo "=== [Mari] Routing statis ==="
# default route ke atas (Yui)
ip route replace default via 192.231.4.137

echo "=== ip route ==="
ip route
echo "=== Jumlah rute statis yang ditambahkan: 1 ==="
EOF
chmod +x /root/no3.sh
echo "/root/no3.sh siap"
```

**c. Jalankan:** `/root/no3.sh`

#### Langkah 10 — Rei: Edit config permanen (IP + rute), lalu script demo

**a. Edit config** (menggantikan config soal 2):

```
# eth0 -> Yui  (192.231.4.140/30)
auto eth0
iface eth0 inet static
    address 192.231.4.142
    netmask 255.255.255.252
    up echo 1 > /proc/sys/net/ipv4/ip_forward
    up ip route add default via 192.231.4.141

# eth1 -> Switch-Quarters  (192.231.0.0/23)
auto eth1
iface eth1 inet static
    address 192.231.0.1
    netmask 255.255.254.0
```

**b. Console:**

```sh
cat > /root/no3.sh << 'EOF'
#!/bin/bash
# Soal 3 - Rei: routing statis

echo "=== [Rei] Routing statis ==="
# default route ke atas (Yui)
ip route replace default via 192.231.4.141

echo "=== ip route ==="
ip route
echo "=== Jumlah rute statis yang ditambahkan: 1 ==="
EOF
chmod +x /root/no3.sh
echo "/root/no3.sh siap"
```

**c. Jalankan:** `/root/no3.sh`

#### Langkah 11 — Shinji: Edit config permanen (IP + rute), lalu script demo

**a. Edit config** (menggantikan config soal 2):

```
# eth0 -> Yui  (192.231.4.144/30)
auto eth0
iface eth0 inet static
    address 192.231.4.146
    netmask 255.255.255.252
    up echo 1 > /proc/sys/net/ipv4/ip_forward
    up ip route add default via 192.231.4.145

# eth1 -> Switch-Garden  (192.231.4.0/27)
auto eth1
iface eth1 inet static
    address 192.231.4.1
    netmask 255.255.255.224
```

**b. Console:**

```sh
cat > /root/no3.sh << 'EOF'
#!/bin/bash
# Soal 3 - Shinji: routing statis

echo "=== [Shinji] Routing statis ==="
# default route ke atas (Yui)
ip route replace default via 192.231.4.145

echo "=== ip route ==="
ip route
echo "=== Jumlah rute statis yang ditambahkan: 1 ==="
EOF
chmod +x /root/no3.sh
echo "/root/no3.sh siap"
```

**c. Jalankan:** `/root/no3.sh`

#### Langkah 12 — Kaworu: Edit config permanen (IP + rute), lalu script demo

**a. Edit config** (menggantikan config soal 2):

```
# eth0 -> Hikari  (192.231.4.148/30)
auto eth0
iface eth0 inet static
    address 192.231.4.150
    netmask 255.255.255.252
    up echo 1 > /proc/sys/net/ipv4/ip_forward
    up ip route add default via 192.231.4.149

# eth1 -> Switch-Relay  (192.231.3.224/27)
auto eth1
iface eth1 inet static
    address 192.231.3.225
    netmask 255.255.255.224
```

**b. Console:**

```sh
cat > /root/no3.sh << 'EOF'
#!/bin/bash
# Soal 3 - Kaworu: routing statis

echo "=== [Kaworu] Routing statis ==="
# default route ke atas (Hikari)
ip route replace default via 192.231.4.149

echo "=== ip route ==="
ip route
echo "=== Jumlah rute statis yang ditambahkan: 1 ==="
EOF
chmod +x /root/no3.sh
echo "/root/no3.sh siap"
```

**c. Jalankan:** `/root/no3.sh`

#### Langkah 13 — Kensuke: Edit config permanen (IP + rute), lalu script demo

**a. Edit config** (menggantikan config soal 2):

```
# eth0 -> Asuka  (192.231.4.152/30)
auto eth0
iface eth0 inet static
    address 192.231.4.154
    netmask 255.255.255.252
    up echo 1 > /proc/sys/net/ipv4/ip_forward
    up ip route add default via 192.231.4.153

# eth1 -> Switch-Bunker  (192.231.3.128/26)
auto eth1
iface eth1 inet static
    address 192.231.3.129
    netmask 255.255.255.192
```

**b. Console:**

```sh
cat > /root/no3.sh << 'EOF'
#!/bin/bash
# Soal 3 - Kensuke: routing statis

echo "=== [Kensuke] Routing statis ==="
# default route ke atas (Asuka)
ip route replace default via 192.231.4.153

echo "=== ip route ==="
ip route
echo "=== Jumlah rute statis yang ditambahkan: 1 ==="
EOF
chmod +x /root/no3.sh
echo "/root/no3.sh siap"
```

**c. Jalankan:** `/root/no3.sh`

#### Langkah 14 — Ritsuko: Edit config permanen (IP + rute), lalu script demo

**a. Edit config** (menggantikan config soal 2):

```
# eth0 -> Asuka  (192.231.4.156/30)
auto eth0
iface eth0 inet static
    address 192.231.4.158
    netmask 255.255.255.252
    up echo 1 > /proc/sys/net/ipv4/ip_forward
    up ip route add default via 192.231.4.157

# eth1 -> Switch-Lab  (192.231.4.48/29)
auto eth1
iface eth1 inet static
    address 192.231.4.49
    netmask 255.255.255.248
```

**b. Console:**

```sh
cat > /root/no3.sh << 'EOF'
#!/bin/bash
# Soal 3 - Ritsuko: routing statis

echo "=== [Ritsuko] Routing statis ==="
# default route ke atas (Asuka)
ip route replace default via 192.231.4.157

echo "=== ip route ==="
ip route
echo "=== Jumlah rute statis yang ditambahkan: 1 ==="
EOF
chmod +x /root/no3.sh
echo "/root/no3.sh siap"
```

**c. Jalankan:** `/root/no3.sh`

#### Langkah 15 — Makoto: Edit config permanen (IP + rute), lalu script demo

**a. Edit config** (menggantikan config soal 2):

```
# eth0 -> Shigeru  (192.231.4.160/30)
auto eth0
iface eth0 inet static
    address 192.231.4.162
    netmask 255.255.255.252
    up echo 1 > /proc/sys/net/ipv4/ip_forward
    up ip route add default via 192.231.4.161

# eth1 -> Switch-Radar  (192.231.4.88/29)
auto eth1
iface eth1 inet static
    address 192.231.4.89
    netmask 255.255.255.248
```

**b. Console:**

```sh
cat > /root/no3.sh << 'EOF'
#!/bin/bash
# Soal 3 - Makoto: routing statis

echo "=== [Makoto] Routing statis ==="
# default route ke atas (Shigeru)
ip route replace default via 192.231.4.161

echo "=== ip route ==="
ip route
echo "=== Jumlah rute statis yang ditambahkan: 1 ==="
EOF
chmod +x /root/no3.sh
echo "/root/no3.sh siap"
```

**c. Jalankan:** `/root/no3.sh`

#### Langkah 16 — Maya: Edit config permanen (IP + rute), lalu script demo

**a. Edit config** (menggantikan config soal 2):

```
# eth0 -> Fuyutsuki  (192.231.4.164/30)
auto eth0
iface eth0 inet static
    address 192.231.4.166
    netmask 255.255.255.252
    up echo 1 > /proc/sys/net/ipv4/ip_forward
    up ip route add default via 192.231.4.165

# eth1 -> Switch-Medic  (192.231.3.192/27)
auto eth1
iface eth1 inet static
    address 192.231.3.193
    netmask 255.255.255.224
```

**b. Console:**

```sh
cat > /root/no3.sh << 'EOF'
#!/bin/bash
# Soal 3 - Maya: routing statis

echo "=== [Maya] Routing statis ==="
# default route ke atas (Fuyutsuki)
ip route replace default via 192.231.4.165

echo "=== ip route ==="
ip route
echo "=== Jumlah rute statis yang ditambahkan: 1 ==="
EOF
chmod +x /root/no3.sh
echo "/root/no3.sh siap"
```

**c. Jalankan:** `/root/no3.sh`

#### Langkah 17 — Toji: Edit config permanen (IP + rute), lalu script demo

**a. Edit config** (menggantikan config soal 2):

```
# eth0 -> Fuyutsuki  (192.231.4.168/30)
auto eth0
iface eth0 inet static
    address 192.231.4.170
    netmask 255.255.255.252
    up echo 1 > /proc/sys/net/ipv4/ip_forward
    up ip route add default via 192.231.4.169

# eth1 -> Switch-Dogma  (192.231.4.72/29)
auto eth1
iface eth1 inet static
    address 192.231.4.73
    netmask 255.255.255.248
```

**b. Console:**

```sh
cat > /root/no3.sh << 'EOF'
#!/bin/bash
# Soal 3 - Toji: routing statis

echo "=== [Toji] Routing statis ==="
# default route ke atas (Fuyutsuki)
ip route replace default via 192.231.4.169

echo "=== ip route ==="
ip route
echo "=== Jumlah rute statis yang ditambahkan: 1 ==="
EOF
chmod +x /root/no3.sh
echo "/root/no3.sh siap"
```

**c. Jalankan:** `/root/no3.sh`

#### Langkah 18 — Lilith: IP sementara

```sh
cat > /root/no3.sh << 'EOF'
#!/bin/bash
# Soal 3 - Lilith: IP & gateway SEMENTARA untuk uji routing

ip link set eth0 up
ip addr replace 192.231.4.74/29 dev eth0
ip route replace default via 192.231.4.73
echo "=== ip -br a ==="
ip -br a
echo "=== ip route ==="
ip route
EOF
chmod +x /root/no3.sh
echo "/root/no3.sh siap"
```

**Jalankan:** `/root/no3.sh`

#### Langkah 19 — Sachiel: IP sementara

```sh
cat > /root/no3.sh << 'EOF'
#!/bin/bash
# Soal 3 - Sachiel: IP & gateway SEMENTARA untuk uji routing

ip link set eth0 up
ip addr replace 192.231.3.130/26 dev eth0
ip route replace default via 192.231.3.129
echo "=== ip -br a ==="
ip -br a
echo "=== ip route ==="
ip route
EOF
chmod +x /root/no3.sh
echo "/root/no3.sh siap"
```

**Jalankan:** `/root/no3.sh`

#### Langkah 20 — Comm-Net: IP sementara

```sh
cat > /root/no3.sh << 'EOF'
#!/bin/bash
# Soal 3 - Comm-Net: IP & gateway SEMENTARA untuk uji routing

ip link set eth0 up
ip addr replace 192.231.2.2/24 dev eth0
ip route replace default via 192.231.2.1
echo "=== ip -br a ==="
ip -br a
echo "=== ip route ==="
ip route
EOF
chmod +x /root/no3.sh
echo "/root/no3.sh siap"
```

**Jalankan:** `/root/no3.sh`

#### Langkah 21 — Armisael: IP sementara + uji ping (jalankan paling akhir)

```sh
cat > /root/no3.sh << 'EOF'
#!/bin/bash
# Soal 3 - Armisael: IP & gateway SEMENTARA untuk uji routing

ip link set eth0 up
ip addr replace 192.231.4.83/29 dev eth0
ip route replace default via 192.231.4.81
echo "=== ip -br a ==="
ip -br a
echo "=== ip route ==="
ip route

echo "=== Uji: Armisael ping ke Lilith, Sachiel, Comm-Net ==="
echo "--- ping Lilith (192.231.4.74) ---"
ping -c 3 192.231.4.74
echo "--- ping Sachiel (192.231.3.130) ---"
ping -c 3 192.231.3.130
echo "--- ping Comm-Net (192.231.2.2) ---"
ping -c 3 192.231.2.2

echo "=== traceroute ke Lilith ==="
command -v traceroute >/dev/null && traceroute -n 192.231.4.74 || echo 'traceroute tidak tersedia'
EOF
chmod +x /root/no3.sh
echo "/root/no3.sh siap"
```

**Jalankan:** `/root/no3.sh`

### Hasil

Armisael berhasil ping **Lilith**, **Sachiel**, dan **Comm-Net**. Traceroute ke Lilith: `192.231.4.81 → 192.231.4.129 → 192.231.4.134 → 192.231.4.170 → 192.231.4.74`.

---

<a id="soal-4"></a>
## Soal 4 — Haus kabar dari dunia luar (NAT & DNS)

#### Langkah 1 — Gendo: NAT MASQUERADE + DNS

```sh
cat > /root/no4.sh << 'EOF'
#!/bin/bash
# Soal 4 - Gendo: NAT ke internet lewat NAT1 + DNS

PREFIX="192.231.0.0/16"   # prefix internal kelompok K40
WAN="eth0"                # interface ke NAT1 (IP dari DHCP, bisa berganti)

echo "=== [Gendo] Pastikan eth0 punya IP & default route dari NAT1 ==="
if ! ip route | grep -q '^default'; then
  dhclient -v $WAN 2>/dev/null || /gns3/bin/busybox udhcpc -i $WAN -q
fi
ip -br a show $WAN
ip route | grep default

echo "=== DNS Gendo ==="
printf "nameserver 8.8.8.8\nnameserver 192.168.122.1\n" > /etc/resolv.conf
cat /etc/resolv.conf

echo "=== NAT (MASQUERADE) ==="
command -v iptables >/dev/null || { apt-get update && apt-get install -y iptables; }
# MASQUERADE: otomatis pakai IP eth0 saat ini -> tidak perlu diubah bila IP NAT1 berganti
# -s PREFIX -o WAN: hanya traffic internal yang KELUAR ke NAT1 yang diterjemahkan,
#                   traffic antar-LAN tidak kena NAT (alamat asli tetap)
iptables -t nat -C POSTROUTING -s $PREFIX -o $WAN -j MASQUERADE 2>/dev/null || \
iptables -t nat -A POSTROUTING -s $PREFIX -o $WAN -j MASQUERADE

iptables -t nat -L POSTROUTING -n -v --line-numbers

echo "=== Uji dari Gendo ==="
ping -c 2 8.8.8.8
ping -c 2 deb.debian.org
EOF
chmod +x /root/no4.sh
echo "/root/no4.sh siap"
```

**Jalankan:** `/root/no4.sh`

#### Langkah 2 — Router lain: DNS (blok sama untuk Yui, Hikari, Asuka, Kaji, Misato, Shigeru, Fuyutsuki, Mari, Rei, Shinji, Kaworu, Kensuke, Ritsuko, Makoto, Maya, Toji)

```sh
cat > /root/no4.sh << 'EOF'
#!/bin/bash
# Soal 4 - DNS resolver (internet lewat NAT di Gendo)
printf "nameserver 8.8.8.8\nnameserver 192.168.122.1\n" > /etc/resolv.conf
echo "=== [$(hostname)] /etc/resolv.conf ==="
cat /etc/resolv.conf
echo "=== Uji internet ==="
ping -c 2 8.8.8.8
ping -c 2 deb.debian.org
EOF
chmod +x /root/no4.sh
echo "/root/no4.sh siap"
```

**Jalankan:** `/root/no4.sh`

#### Langkah 3 — Lilith: DNS + uji internet

```sh
cat > /root/no4.sh << 'EOF'
#!/bin/bash
# Soal 4 - Lilith: DNS + uji internet (IP sementara dari no. 3)
ip addr replace 192.231.4.74/29 dev eth0
ip route replace default via 192.231.4.73
printf "nameserver 8.8.8.8\nnameserver 192.168.122.1\n" > /etc/resolv.conf
echo "=== [Lilith] resolv.conf ==="
cat /etc/resolv.conf
echo "=== ping 8.8.8.8 ==="
ping -c 3 8.8.8.8
echo "=== ping deb.debian.org ==="
ping -c 3 deb.debian.org
EOF
chmod +x /root/no4.sh
echo "/root/no4.sh siap"
```

**Jalankan:** `/root/no4.sh`

#### Langkah 4 — Armisael: DNS + uji internet

```sh
cat > /root/no4.sh << 'EOF'
#!/bin/bash
# Soal 4 - Armisael: DNS + uji internet (IP sementara dari no. 3)
ip addr replace 192.231.4.83/29 dev eth0
ip route replace default via 192.231.4.81
printf "nameserver 8.8.8.8\nnameserver 192.168.122.1\n" > /etc/resolv.conf
echo "=== [Armisael] resolv.conf ==="
cat /etc/resolv.conf
echo "=== ping 8.8.8.8 ==="
ping -c 3 8.8.8.8
echo "=== ping deb.debian.org ==="
ping -c 3 deb.debian.org
EOF
chmod +x /root/no4.sh
echo "/root/no4.sh siap"
```

**Jalankan:** `/root/no4.sh`

#### Langkah 5 — Gendo: tunjukkan counter rule NAT

```sh
iptables -t nat -L POSTROUTING -n -v
```

### Penjelasan

| Syarat | Dipenuhi oleh |
|---|---|
| Tidak perlu diubah bila IP NAT1 berganti | **MASQUERADE** memakai IP eth0 saat ini |
| Hanya traffic prefix internal | `-s 192.231.0.0/16` |
| Hanya yang keluar lewat NAT1; antar-LAN tetap alamat asli | `-o eth0` |
| Bisa resolve domain | `nameserver 8.8.8.8` |

### Hasil

Armisael dan Lilith berhasil ping `8.8.8.8` dan `deb.debian.org`; counter MASQUERADE naik.

---

<a id="soal-5"></a>
## Soal 5 — Pintu depan dikunci rapat (firewall Gendo)

#### Langkah 1 — Gendo: buat gendo.sh (gabungan soal 2–5)

```sh
cat > /root/gendo.sh << 'EOF'
#!/bin/bash
# gendo.sh - Konfigurasi lengkap Gendo (Soal 2, 3, 4, 5) - K40
PREFIX="192.231.0.0/16"   # prefix internal
WAN="eth0"                # ke NAT1

########## SOAL 2: IP interface + IP forwarding ##########
echo "=== [Soal 2] IP interface ==="
ip link set eth0 up
ip addr replace 192.231.4.105/30 dev eth1   # Switch-Cmd
ip addr replace 192.231.4.133/30 dev eth2   # Fuyutsuki
ip addr replace 192.231.4.129/30 dev eth3   # Shigeru
ip addr replace 192.231.4.125/30 dev eth4   # Misato
ip addr replace 192.231.4.121/30 dev eth5   # Kaji
ip addr replace 192.231.4.117/30 dev eth6   # Asuka
ip addr replace 192.231.4.113/30 dev eth7   # Hikari
ip addr replace 192.231.4.109/30 dev eth8   # Yui
for i in 1 2 3 4 5 6 7 8; do ip link set eth$i up; done
sysctl -w net.ipv4.ip_forward=1

########## SOAL 5 (bagian 1): firewall dipasang SEBELUM DHCP ##########
# supaya DHCP terbukti tetap jalan walau policy DROP
echo "=== [Soal 5] Firewall Gendo ==="
command -v iptables >/dev/null || { apt-get update && apt-get install -y iptables; }
iptables -F INPUT
iptables -F FORWARD

# Loopback
iptables -A INPUT -i lo -j ACCEPT
# (a) balasan koneksi yang sudah ada
iptables -A INPUT   -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT
iptables -A FORWARD -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT
# DHCP dari NAT1 (server port 67 -> client port 68)
iptables -A INPUT -i $WAN -p udp --sport 67 --dport 68 -j ACCEPT
# (d) jaringan internal -> Gendo sendiri (tidak boleh datang dari sisi NAT1)
iptables -A INPUT ! -i $WAN -s $PREFIX -j ACCEPT
# (b) jaringan internal -> internet
iptables -A FORWARD ! -i $WAN -o $WAN -s $PREFIX -j ACCEPT
# (c) antar-LAN
iptables -A FORWARD ! -i $WAN ! -o $WAN -s $PREFIX -d $PREFIX -j ACCEPT
# Policy DROP: koneksi baru dari sisi NAT1 tidak bisa masuk
iptables -P INPUT DROP
iptables -P FORWARD DROP
iptables -P OUTPUT ACCEPT

########## SOAL 2 & 3: DHCP + default route ke NAT1 ##########
echo "=== [Soal 2/3] DHCP eth0 & default route ==="
if ! ip -4 addr show $WAN | grep -q inet || ! ip route | grep -q '^default'; then
  dhclient -v $WAN 2>/dev/null || /gns3/bin/busybox udhcpc -i $WAN -q
fi
ip -br a show $WAN
ip route | grep default

########## SOAL 3: routing statis (22 rute, sudah diringkas) ##########
echo "=== [Soal 3] Routing statis ==="
# Fuyutsuki (eth2)
ip route replace 192.231.3.192/27 via 192.231.4.134
ip route replace 192.231.4.56/29  via 192.231.4.134
ip route replace 192.231.4.72/29  via 192.231.4.134
ip route replace 192.231.4.164/30 via 192.231.4.134
ip route replace 192.231.4.168/30 via 192.231.4.134
# Shigeru (eth3)
ip route replace 192.231.4.80/28  via 192.231.4.130
ip route replace 192.231.4.160/30 via 192.231.4.130
# Misato (eth4)
ip route replace 192.231.4.64/29  via 192.231.4.126
# Kaji (eth5)
ip route replace 192.231.4.100/30 via 192.231.4.122
# Asuka (eth6)
ip route replace 192.231.3.128/26 via 192.231.4.118
ip route replace 192.231.4.48/29  via 192.231.4.118
ip route replace 192.231.4.96/30  via 192.231.4.118
ip route replace 192.231.4.152/29 via 192.231.4.118
# Hikari (eth7)
ip route replace 192.231.2.0/24   via 192.231.4.114
ip route replace 192.231.3.224/27 via 192.231.4.114
ip route replace 192.231.4.148/30 via 192.231.4.114
# Yui (eth8)
ip route replace 192.231.0.0/23   via 192.231.4.110
ip route replace 192.231.3.0/25   via 192.231.4.110
ip route replace 192.231.4.0/27   via 192.231.4.110
ip route replace 192.231.4.32/28  via 192.231.4.110
ip route replace 192.231.4.136/29 via 192.231.4.110
ip route replace 192.231.4.144/30 via 192.231.4.110

########## SOAL 4: DNS + NAT ##########
echo "=== [Soal 4] DNS & NAT ==="
printf "nameserver 8.8.8.8\nnameserver 192.168.122.1\n" > /etc/resolv.conf
iptables -t nat -F POSTROUTING
iptables -t nat -A POSTROUTING -s $PREFIX -o $WAN -j MASQUERADE

########## Tampilkan hasil ##########
echo "=== iptables -S ==="
iptables -S
echo "=== iptables -t nat -S ==="
iptables -t nat -S
echo "=== Uji dari Gendo ==="
ping -c 2 8.8.8.8
EOF
chmod +x /root/gendo.sh
echo "/root/gendo.sh siap"
```

#### Langkah 2 — Gendo: jalankan dan tampilkan rule

```sh
/root/gendo.sh
iptables -S
```

#### Langkah 3 — Armisael: buktikan internet tetap jalan

```sh
ping -c 3 8.8.8.8
```

### Penjelasan rule

| Rule | Fungsi |
|---|---|
| `INPUT -i lo -j ACCEPT` | loopback |
| `INPUT/FORWARD --ctstate ESTABLISHED,RELATED` | (a) balasan koneksi |
| `INPUT -i eth0 -p udp --sport 67 --dport 68` | DHCP dari NAT1 |
| `INPUT ! -i eth0 -s 192.231.0.0/16` | (d) internal → Gendo |
| `FORWARD ! -i eth0 -o eth0 -s 192.231.0.0/16` | (b) internal → internet |
| `FORWARD ! -i eth0 ! -o eth0 -s/-d 192.231.0.0/16` | (c) antar-LAN |
| `-P INPUT DROP`, `-P FORWARD DROP` | koneksi baru dari NAT1 ditolak |

`! -i eth0` mencegah spoofing alamat internal dari sisi NAT1. Firewall dipasang sebelum DHCP untuk membuktikan DHCP tetap jalan. Chain di-flush lalu diisi ulang sehingga rule tidak dobel.

### Hasil

`iptables -S` menampilkan policy DROP beserta rule di atas; Armisael tetap bisa ping 8.8.8.8.

---

<a id="soal-6"></a>
## Soal 6 — Alamat para pilot (IP statis node)

Config ditulis ke `/etc/network/interfaces` **lewat console** (file ini permanen di GNS3). Tidak lewat Save di Edit config karena baris `/etc/resolv.conf` memicu *controller is unreachable*.

#### Langkah 1 — Quarters: IP 192.231.0.2/23, gateway 192.231.0.1 (Rei)

```sh
cat > /etc/network/interfaces << 'EOF'
auto eth0
iface eth0 inet static
    address 192.231.0.2
    netmask 255.255.254.0
    gateway 192.231.0.1
    up echo nameserver 8.8.8.8 > /etc/resolv.conf
EOF
ip addr flush dev eth0
ip addr add 192.231.0.2/23 dev eth0
ip link set eth0 up
ip route replace default via 192.231.0.1
echo nameserver 8.8.8.8 > /etc/resolv.conf
ip -br a show eth0; ip route
```

#### Langkah 2 — Comm-Net: IP 192.231.2.2/24, gateway 192.231.2.1 (Hikari)

```sh
cat > /etc/network/interfaces << 'EOF'
auto eth0
iface eth0 inet static
    address 192.231.2.2
    netmask 255.255.255.0
    gateway 192.231.2.1
    up echo nameserver 8.8.8.8 > /etc/resolv.conf
EOF
ip addr flush dev eth0
ip addr add 192.231.2.2/24 dev eth0
ip link set eth0 up
ip route replace default via 192.231.2.1
echo nameserver 8.8.8.8 > /etc/resolv.conf
ip -br a show eth0; ip route
```

#### Langkah 3 — Hangar-Crew: IP 192.231.3.2/25, gateway 192.231.3.1 (Yui)

```sh
cat > /etc/network/interfaces << 'EOF'
auto eth0
iface eth0 inet static
    address 192.231.3.2
    netmask 255.255.255.128
    gateway 192.231.3.1
    up echo nameserver 8.8.8.8 > /etc/resolv.conf
EOF
ip addr flush dev eth0
ip addr add 192.231.3.2/25 dev eth0
ip link set eth0 up
ip route replace default via 192.231.3.1
echo nameserver 8.8.8.8 > /etc/resolv.conf
ip -br a show eth0; ip route
```

#### Langkah 4 — Sachiel: IP 192.231.3.130/26, gateway 192.231.3.129 (Kensuke)

```sh
cat > /etc/network/interfaces << 'EOF'
auto eth0
iface eth0 inet static
    address 192.231.3.130
    netmask 255.255.255.192
    gateway 192.231.3.129
    up echo nameserver 8.8.8.8 > /etc/resolv.conf
EOF
ip addr flush dev eth0
ip addr add 192.231.3.130/26 dev eth0
ip link set eth0 up
ip route replace default via 192.231.3.129
echo nameserver 8.8.8.8 > /etc/resolv.conf
ip -br a show eth0; ip route
```

#### Langkah 5 — Pen-Pen: IP 192.231.3.194/27, gateway 192.231.3.193 (Maya)

```sh
cat > /etc/network/interfaces << 'EOF'
auto eth0
iface eth0 inet static
    address 192.231.3.194
    netmask 255.255.255.224
    gateway 192.231.3.193
    up echo nameserver 8.8.8.8 > /etc/resolv.conf
EOF
ip addr flush dev eth0
ip addr add 192.231.3.194/27 dev eth0
ip link set eth0 up
ip route replace default via 192.231.3.193
echo nameserver 8.8.8.8 > /etc/resolv.conf
ip -br a show eth0; ip route
```

#### Langkah 6 — Medic-Bay: IP 192.231.3.195/27, gateway 192.231.3.193 (Maya)

```sh
cat > /etc/network/interfaces << 'EOF'
auto eth0
iface eth0 inet static
    address 192.231.3.195
    netmask 255.255.255.224
    gateway 192.231.3.193
    up echo nameserver 8.8.8.8 > /etc/resolv.conf
EOF
ip addr flush dev eth0
ip addr add 192.231.3.195/27 dev eth0
ip link set eth0 up
ip route replace default via 192.231.3.193
echo nameserver 8.8.8.8 > /etc/resolv.conf
ip -br a show eth0; ip route
```

#### Langkah 7 — Relay-Node: IP 192.231.3.226/27, gateway 192.231.3.225 (Kaworu)

```sh
cat > /etc/network/interfaces << 'EOF'
auto eth0
iface eth0 inet static
    address 192.231.3.226
    netmask 255.255.255.224
    gateway 192.231.3.225
    up echo nameserver 8.8.8.8 > /etc/resolv.conf
EOF
ip addr flush dev eth0
ip addr add 192.231.3.226/27 dev eth0
ip link set eth0 up
ip route replace default via 192.231.3.225
echo nameserver 8.8.8.8 > /etc/resolv.conf
ip -br a show eth0; ip route
```

#### Langkah 8 — Garden-Staff: IP 192.231.4.2/27, gateway 192.231.4.1 (Shinji)

```sh
cat > /etc/network/interfaces << 'EOF'
auto eth0
iface eth0 inet static
    address 192.231.4.2
    netmask 255.255.255.224
    gateway 192.231.4.1
    up echo nameserver 8.8.8.8 > /etc/resolv.conf
EOF
ip addr flush dev eth0
ip addr add 192.231.4.2/27 dev eth0
ip link set eth0 up
ip route replace default via 192.231.4.1
echo nameserver 8.8.8.8 > /etc/resolv.conf
ip -br a show eth0; ip route
```

#### Langkah 9 — Dock-Crew: IP 192.231.4.34/28, gateway 192.231.4.33 (Mari)

```sh
cat > /etc/network/interfaces << 'EOF'
auto eth0
iface eth0 inet static
    address 192.231.4.34
    netmask 255.255.255.240
    gateway 192.231.4.33
    up echo nameserver 8.8.8.8 > /etc/resolv.conf
EOF
ip addr flush dev eth0
ip addr add 192.231.4.34/28 dev eth0
ip link set eth0 up
ip route replace default via 192.231.4.33
echo nameserver 8.8.8.8 > /etc/resolv.conf
ip -br a show eth0; ip route
```

#### Langkah 10 — Entry-Plug: IP 192.231.4.50/29, gateway 192.231.4.49 (Ritsuko)

```sh
cat > /etc/network/interfaces << 'EOF'
auto eth0
iface eth0 inet static
    address 192.231.4.50
    netmask 255.255.255.248
    gateway 192.231.4.49
    up echo nameserver 8.8.8.8 > /etc/resolv.conf
EOF
ip addr flush dev eth0
ip addr add 192.231.4.50/29 dev eth0
ip link set eth0 up
ip route replace default via 192.231.4.49
echo nameserver 8.8.8.8 > /etc/resolv.conf
ip -br a show eth0; ip route
```

#### Langkah 11 — Dummy-Plug: IP 192.231.4.51/29, gateway 192.231.4.49 (Ritsuko)

```sh
cat > /etc/network/interfaces << 'EOF'
auto eth0
iface eth0 inet static
    address 192.231.4.51
    netmask 255.255.255.248
    gateway 192.231.4.49
    up echo nameserver 8.8.8.8 > /etc/resolv.conf
EOF
ip addr flush dev eth0
ip addr add 192.231.4.51/29 dev eth0
ip link set eth0 up
ip route replace default via 192.231.4.49
echo nameserver 8.8.8.8 > /etc/resolv.conf
ip -br a show eth0; ip route
```

#### Langkah 12 — Magi-Casper: IP 192.231.4.58/29, gateway 192.231.4.57 (Fuyutsuki)

```sh
cat > /etc/network/interfaces << 'EOF'
auto eth0
iface eth0 inet static
    address 192.231.4.58
    netmask 255.255.255.248
    gateway 192.231.4.57
    up echo nameserver 8.8.8.8 > /etc/resolv.conf
EOF
ip addr flush dev eth0
ip addr add 192.231.4.58/29 dev eth0
ip link set eth0 up
ip route replace default via 192.231.4.57
echo nameserver 8.8.8.8 > /etc/resolv.conf
ip -br a show eth0; ip route
```

#### Langkah 13 — Magi-Balthasar: IP 192.231.4.59/29, gateway 192.231.4.57 (Fuyutsuki)

```sh
cat > /etc/network/interfaces << 'EOF'
auto eth0
iface eth0 inet static
    address 192.231.4.59
    netmask 255.255.255.248
    gateway 192.231.4.57
    up echo nameserver 8.8.8.8 > /etc/resolv.conf
EOF
ip addr flush dev eth0
ip addr add 192.231.4.59/29 dev eth0
ip link set eth0 up
ip route replace default via 192.231.4.57
echo nameserver 8.8.8.8 > /etc/resolv.conf
ip -br a show eth0; ip route
```

#### Langkah 14 — Magi-Melchior: IP 192.231.4.60/29, gateway 192.231.4.57 (Fuyutsuki)

```sh
cat > /etc/network/interfaces << 'EOF'
auto eth0
iface eth0 inet static
    address 192.231.4.60
    netmask 255.255.255.248
    gateway 192.231.4.57
    up echo nameserver 8.8.8.8 > /etc/resolv.conf
EOF
ip addr flush dev eth0
ip addr add 192.231.4.60/29 dev eth0
ip link set eth0 up
ip route replace default via 192.231.4.57
echo nameserver 8.8.8.8 > /etc/resolv.conf
ip -br a show eth0; ip route
```

#### Langkah 15 — Unit-01: IP 192.231.4.66/29, gateway 192.231.4.65 (Misato)

```sh
cat > /etc/network/interfaces << 'EOF'
auto eth0
iface eth0 inet static
    address 192.231.4.66
    netmask 255.255.255.248
    gateway 192.231.4.65
    up echo nameserver 8.8.8.8 > /etc/resolv.conf
EOF
ip addr flush dev eth0
ip addr add 192.231.4.66/29 dev eth0
ip link set eth0 up
ip route replace default via 192.231.4.65
echo nameserver 8.8.8.8 > /etc/resolv.conf
ip -br a show eth0; ip route
```

#### Langkah 16 — Unit-02: IP 192.231.4.67/29, gateway 192.231.4.65 (Misato)

```sh
cat > /etc/network/interfaces << 'EOF'
auto eth0
iface eth0 inet static
    address 192.231.4.67
    netmask 255.255.255.248
    gateway 192.231.4.65
    up echo nameserver 8.8.8.8 > /etc/resolv.conf
EOF
ip addr flush dev eth0
ip addr add 192.231.4.67/29 dev eth0
ip link set eth0 up
ip route replace default via 192.231.4.65
echo nameserver 8.8.8.8 > /etc/resolv.conf
ip -br a show eth0; ip route
```

#### Langkah 17 — Unit-00: IP 192.231.4.68/29, gateway 192.231.4.65 (Misato)

```sh
cat > /etc/network/interfaces << 'EOF'
auto eth0
iface eth0 inet static
    address 192.231.4.68
    netmask 255.255.255.248
    gateway 192.231.4.65
    up echo nameserver 8.8.8.8 > /etc/resolv.conf
EOF
ip addr flush dev eth0
ip addr add 192.231.4.68/29 dev eth0
ip link set eth0 up
ip route replace default via 192.231.4.65
echo nameserver 8.8.8.8 > /etc/resolv.conf
ip -br a show eth0; ip route
```

#### Langkah 18 — Lilith: IP 192.231.4.74/29, gateway 192.231.4.73 (Toji)

```sh
cat > /etc/network/interfaces << 'EOF'
auto eth0
iface eth0 inet static
    address 192.231.4.74
    netmask 255.255.255.248
    gateway 192.231.4.73
    up echo nameserver 8.8.8.8 > /etc/resolv.conf
EOF
ip addr flush dev eth0
ip addr add 192.231.4.74/29 dev eth0
ip link set eth0 up
ip route replace default via 192.231.4.73
echo nameserver 8.8.8.8 > /etc/resolv.conf
ip -br a show eth0; ip route
```

#### Langkah 19 — Adam: IP 192.231.4.75/29, gateway 192.231.4.73 (Toji)

```sh
cat > /etc/network/interfaces << 'EOF'
auto eth0
iface eth0 inet static
    address 192.231.4.75
    netmask 255.255.255.248
    gateway 192.231.4.73
    up echo nameserver 8.8.8.8 > /etc/resolv.conf
EOF
ip addr flush dev eth0
ip addr add 192.231.4.75/29 dev eth0
ip link set eth0 up
ip route replace default via 192.231.4.73
echo nameserver 8.8.8.8 > /etc/resolv.conf
ip -br a show eth0; ip route
```

#### Langkah 20 — Zeruel: IP 192.231.4.82/29, gateway 192.231.4.81 (Shigeru)

```sh
cat > /etc/network/interfaces << 'EOF'
auto eth0
iface eth0 inet static
    address 192.231.4.82
    netmask 255.255.255.248
    gateway 192.231.4.81
    up echo nameserver 8.8.8.8 > /etc/resolv.conf
EOF
ip addr flush dev eth0
ip addr add 192.231.4.82/29 dev eth0
ip link set eth0 up
ip route replace default via 192.231.4.81
echo nameserver 8.8.8.8 > /etc/resolv.conf
ip -br a show eth0; ip route
```

#### Langkah 21 — Armisael: IP 192.231.4.83/29, gateway 192.231.4.81 (Shigeru)

```sh
cat > /etc/network/interfaces << 'EOF'
auto eth0
iface eth0 inet static
    address 192.231.4.83
    netmask 255.255.255.248
    gateway 192.231.4.81
    up echo nameserver 8.8.8.8 > /etc/resolv.conf
EOF
ip addr flush dev eth0
ip addr add 192.231.4.83/29 dev eth0
ip link set eth0 up
ip route replace default via 192.231.4.81
echo nameserver 8.8.8.8 > /etc/resolv.conf
ip -br a show eth0; ip route
```

#### Langkah 22 — Radar-Array: IP 192.231.4.90/29, gateway 192.231.4.89 (Makoto)

```sh
cat > /etc/network/interfaces << 'EOF'
auto eth0
iface eth0 inet static
    address 192.231.4.90
    netmask 255.255.255.248
    gateway 192.231.4.89
    up echo nameserver 8.8.8.8 > /etc/resolv.conf
EOF
ip addr flush dev eth0
ip addr add 192.231.4.90/29 dev eth0
ip link set eth0 up
ip route replace default via 192.231.4.89
echo nameserver 8.8.8.8 > /etc/resolv.conf
ip -br a show eth0; ip route
```

#### Langkah 23 — Archive-Vault: IP 192.231.4.98/30, gateway 192.231.4.97 (Asuka)

```sh
cat > /etc/network/interfaces << 'EOF'
auto eth0
iface eth0 inet static
    address 192.231.4.98
    netmask 255.255.255.252
    gateway 192.231.4.97
    up echo nameserver 8.8.8.8 > /etc/resolv.conf
EOF
ip addr flush dev eth0
ip addr add 192.231.4.98/30 dev eth0
ip link set eth0 up
ip route replace default via 192.231.4.97
echo nameserver 8.8.8.8 > /etc/resolv.conf
ip -br a show eth0; ip route
```

#### Langkah 24 — Spy: IP 192.231.4.102/30, gateway 192.231.4.101 (Kaji)

```sh
cat > /etc/network/interfaces << 'EOF'
auto eth0
iface eth0 inet static
    address 192.231.4.102
    netmask 255.255.255.252
    gateway 192.231.4.101
    up echo nameserver 8.8.8.8 > /etc/resolv.conf
EOF
ip addr flush dev eth0
ip addr add 192.231.4.102/30 dev eth0
ip link set eth0 up
ip route replace default via 192.231.4.101
echo nameserver 8.8.8.8 > /etc/resolv.conf
ip -br a show eth0; ip route
```

#### Langkah 25 — Tokyo3-Core: IP 192.231.4.106/30, gateway 192.231.4.105 (Gendo)

```sh
cat > /etc/network/interfaces << 'EOF'
auto eth0
iface eth0 inet static
    address 192.231.4.106
    netmask 255.255.255.252
    gateway 192.231.4.105
    up echo nameserver 8.8.8.8 > /etc/resolv.conf
EOF
ip addr flush dev eth0
ip addr add 192.231.4.106/30 dev eth0
ip link set eth0 up
ip route replace default via 192.231.4.105
echo nameserver 8.8.8.8 > /etc/resolv.conf
ip -br a show eth0; ip route
```

#### Langkah 26 — Unit-01: buat script bukti

```sh
cat > /root/no6.sh << 'EOF'
#!/bin/bash
# Soal 6 - bukti IP statis Unit-01
echo "=== [$(hostname)] ip -br a ==="
ip -br a
echo "=== /etc/network/interfaces (permanen) ==="
cat /etc/network/interfaces
echo "=== ip route ==="
ip route
GW=$(ip route | awk '/^default/{print $3}')
echo "=== ping gateway ($GW) ==="
ping -c 3 $GW
EOF
chmod +x /root/no6.sh
echo "/root/no6.sh siap"
```

#### Langkah 27 — Unit-01: jalankan, restart node, jalankan lagi

```sh
/root/no6.sh
# Stop -> Start Unit-01, lalu:
/root/no6.sh
```

### Hasil

Sebelum dan sesudah restart Unit-01 tetap `192.231.4.66/29`, `default via 192.231.4.65`, ping gateway 0% loss.

---

<a id="soal-7"></a>
## Soal 7 — Membangunkan MAGI

#### Langkah 1 — Magi-Casper: install iptables + netcat-traditional, listener TCP 5000

```sh
cat > /root/Magi-Casper.sh << 'EOF'
#!/bin/bash
# Soal 7 - Magi-Casper: install iptables + netcat-traditional, listener TCP 5000
PORTS="5000"

echo "=== [$(hostname)] Install paket ==="
apt-get update
DEBIAN_FRONTEND=noninteractive apt-get install -y iptables netcat-traditional
update-alternatives --set nc /bin/nc.traditional 2>/dev/null
nc -h 2>&1 | head -1

echo "=== Nyalakan listener ==="
pkill -f magi-listener 2>/dev/null; pkill -x nc 2>/dev/null; sleep 1
for P in $PORTS; do
  : > /root/pesan_$P.log
  setsid bash -c '
    P=$1
    while true; do
      MSG=$(nc -l -p $P 2>/dev/null)
      [ -n "$MSG" ] && echo "[$(date +%H:%M:%S)] $(hostname) port $P menerima: $MSG" >> /root/pesan_$P.log
    done' magi-listener-$P $P > /dev/null 2>&1 &
done
sleep 1
echo "=== Port yang LISTEN ==="
ss -ltnp 2>/dev/null | grep -E ":(5000|5001)\b" || netstat -ltnp | grep -E ":(5000|5001)\b"
echo "Lihat pesan masuk:  cat /root/pesan_5000.log"
EOF
chmod +x /root/Magi-Casper.sh
echo "/root/Magi-Casper.sh siap"
```

**Jalankan:** `/root/Magi-Casper.sh`

#### Langkah 2 — Magi-Balthasar: install iptables + netcat-traditional, listener TCP 5000 & 5001

```sh
cat > /root/Magi-Balthasar.sh << 'EOF'
#!/bin/bash
# Soal 7 - Magi-Balthasar: install iptables + netcat-traditional, listener TCP 5000 & 5001
PORTS="5000 5001"

echo "=== [$(hostname)] Install paket ==="
apt-get update
DEBIAN_FRONTEND=noninteractive apt-get install -y iptables netcat-traditional
update-alternatives --set nc /bin/nc.traditional 2>/dev/null
nc -h 2>&1 | head -1

echo "=== Nyalakan listener ==="
pkill -f magi-listener 2>/dev/null; pkill -x nc 2>/dev/null; sleep 1
for P in $PORTS; do
  : > /root/pesan_$P.log
  setsid bash -c '
    P=$1
    while true; do
      MSG=$(nc -l -p $P 2>/dev/null)
      [ -n "$MSG" ] && echo "[$(date +%H:%M:%S)] $(hostname) port $P menerima: $MSG" >> /root/pesan_$P.log
    done' magi-listener-$P $P > /dev/null 2>&1 &
done
sleep 1
echo "=== Port yang LISTEN ==="
ss -ltnp 2>/dev/null | grep -E ":(5000|5001)\b" || netstat -ltnp | grep -E ":(5000|5001)\b"
echo "Lihat pesan masuk:  cat /root/pesan_5000.log /root/pesan_5001.log"
EOF
chmod +x /root/Magi-Balthasar.sh
echo "/root/Magi-Balthasar.sh siap"
```

**Jalankan:** `/root/Magi-Balthasar.sh`

#### Langkah 3 — Magi-Melchior: install iptables + netcat-traditional, listener TCP 5000

```sh
cat > /root/Magi-Melchior.sh << 'EOF'
#!/bin/bash
# Soal 7 - Magi-Melchior: install iptables + netcat-traditional, listener TCP 5000
PORTS="5000"

echo "=== [$(hostname)] Install paket ==="
apt-get update
DEBIAN_FRONTEND=noninteractive apt-get install -y iptables netcat-traditional
update-alternatives --set nc /bin/nc.traditional 2>/dev/null
nc -h 2>&1 | head -1

echo "=== Nyalakan listener ==="
pkill -f magi-listener 2>/dev/null; pkill -x nc 2>/dev/null; sleep 1
for P in $PORTS; do
  : > /root/pesan_$P.log
  setsid bash -c '
    P=$1
    while true; do
      MSG=$(nc -l -p $P 2>/dev/null)
      [ -n "$MSG" ] && echo "[$(date +%H:%M:%S)] $(hostname) port $P menerima: $MSG" >> /root/pesan_$P.log
    done' magi-listener-$P $P > /dev/null 2>&1 &
done
sleep 1
echo "=== Port yang LISTEN ==="
ss -ltnp 2>/dev/null | grep -E ":(5000|5001)\b" || netstat -ltnp | grep -E ":(5000|5001)\b"
echo "Lihat pesan masuk:  cat /root/pesan_5000.log"
EOF
chmod +x /root/Magi-Melchior.sh
echo "/root/Magi-Melchior.sh siap"
```

**Jalankan:** `/root/Magi-Melchior.sh`

#### Langkah 4 — Lilith: kirim pesan ke ketiga MAGI + port 5001 Balthasar

```sh
cat > /root/Lilith.sh << 'EOF'
#!/bin/bash
# Soal 7 - Lilith: kirim pesan ke MAGI
kirim() {  # $1=nama $2=ip $3=port
  echo "Halo $1 port $3, dari Lilith ($(date +%H:%M:%S))" | nc -w 2 $2 $3
  RC=$?
  [ $RC -eq 0 ] && echo "[TERKIRIM] $1 $2:$3" || echo "[GAGAL rc=$RC] $1 $2:$3"
}
echo "=== [Lilith] Kirim pesan ke MAGI ==="
kirim Magi-Casper    192.231.4.58 5000
kirim Magi-Balthasar 192.231.4.59 5000
kirim Magi-Balthasar 192.231.4.59 5001
kirim Magi-Melchior  192.231.4.60 5000
EOF
chmod +x /root/Lilith.sh
echo "/root/Lilith.sh siap"
```

**Jalankan:** `/root/Lilith.sh` → 4× `[TERKIRIM]`

#### Langkah 5 — Tampilkan pesan yang diterima

```sh
# Magi-Casper & Magi-Melchior
cat /root/pesan_5000.log
# Magi-Balthasar
cat /root/pesan_5000.log /root/pesan_5001.log
```

### Hasil

Pesan Lilith tercatat di Casper:5000, Balthasar:5000, Balthasar:5001, Melchior:5000. Listener dibuat dalam loop karena nc-traditional mati setelah 1 koneksi.

---

<a id="soal-8"></a>
## Soal 8 — Mengenali kondisi normal

#### Langkah 1 — Pen-Pen: layanan TCP 80 → `Welcome to Pen-Pen`

```sh
cat > /root/Pen-Pen.sh << 'EOF'
#!/bin/bash
# Soal 8 - Pen-Pen: layanan TCP 80 -> "Welcome to Pen-Pen"
echo "=== [$(hostname)] Install netcat-traditional ==="
command -v nc.traditional >/dev/null || { apt-get update; DEBIAN_FRONTEND=noninteractive apt-get install -y netcat-traditional; }
update-alternatives --set nc /bin/nc.traditional 2>/dev/null

echo "=== Nyalakan layanan ==="
pkill -f web-listener 2>/dev/null; pkill -x nc 2>/dev/null; sleep 1
# port 80: Welcome to {hostname}
setsid bash -c 'while true; do echo "Welcome to $(hostname)" | nc -l -p 80 -q 1; done' web-listener-80 >/dev/null 2>&1 &
sleep 1
echo "=== Port yang LISTEN ==="
ss -ltnp | grep -E ":(80|8080)\b"
EOF
chmod +x /root/Pen-Pen.sh
echo "/root/Pen-Pen.sh siap"
```

**Jalankan:** `/root/Pen-Pen.sh`

#### Langkah 2 — Tokyo3-Core: TCP 80 → `Welcome to Tokyo3-Core`, TCP 8080 → `syifa imutz`

```sh
cat > /root/Tokyo3-Core.sh << 'EOF'
#!/bin/bash
# Soal 8 - Tokyo3-Core: TCP 80 -> "Welcome to Tokyo3-Core", TCP 8080 -> "syifa imutz"
echo "=== [$(hostname)] Install netcat-traditional ==="
command -v nc.traditional >/dev/null || { apt-get update; DEBIAN_FRONTEND=noninteractive apt-get install -y netcat-traditional; }
update-alternatives --set nc /bin/nc.traditional 2>/dev/null

echo "=== Nyalakan layanan ==="
pkill -f web-listener 2>/dev/null; pkill -x nc 2>/dev/null; sleep 1
# port 80: Welcome to {hostname}
setsid bash -c 'while true; do echo "Welcome to $(hostname)" | nc -l -p 80 -q 1; done' web-listener-80 >/dev/null 2>&1 &
# port 8080: syifa imutz
setsid bash -c 'while true; do echo "syifa imutz" | nc -l -p 8080 -q 1; done' web-listener-8080 >/dev/null 2>&1 &
sleep 1
echo "=== Port yang LISTEN ==="
ss -ltnp | grep -E ":(80|8080)\b"
EOF
chmod +x /root/Tokyo3-Core.sh
echo "/root/Tokyo3-Core.sh siap"
```

**Jalankan:** `/root/Tokyo3-Core.sh`

#### Langkah 3 — Lilith: uji ketiga port + port 22

```sh
cat > /root/no8.sh << 'EOF'
#!/bin/bash
# Soal 8 - Lilith: uji layanan Pen-Pen & Tokyo3-Core
uji() {  # $1=nama $2=ip $3=port
  echo "--- nc $1 ($2) port $3 ---"
  START=$(date +%s.%N)
  OUT=$(nc -w 3 $2 $3 < /dev/null 2>&1); RC=$?
  END=$(date +%s.%N)
  echo "Balasan : ${OUT:-<kosong>}"
  echo "Exit code: $RC   Durasi: $(echo "$END - $START" | bc 2>/dev/null || awk "BEGIN{print $END-$START}") detik"
}
echo "=== [Lilith] Port yang DILAYANI ==="
uji Pen-Pen     192.231.3.194 80
uji Tokyo3-Core 192.231.4.106 80
uji Tokyo3-Core 192.231.4.106 8080

echo
echo "=== [Lilith] Port yang TIDAK dilayani (harus langsung gagal) ==="
uji Pen-Pen     192.231.3.194 22
uji Tokyo3-Core 192.231.4.106 22
EOF
chmod +x /root/no8.sh
echo "/root/no8.sh siap"
```

**Jalankan:** `/root/no8.sh`

### Hasil

| Uji dari Lilith | Balasan | Exit code | Durasi |
|---|---|---|---|
| Pen-Pen :80 | Welcome to Pen-Pen | 0 | 0,0097 s |
| Tokyo3-Core :80 | Welcome to Tokyo3-Core | 0 | 0,0084 s |
| Tokyo3-Core :8080 | syifa imutz | 0 | 0,0049 s |
| Pen-Pen :22 | – | 1 | 0,0048 s |
| Tokyo3-Core :22 | – | 1 | 0,0048 s |

Port 22 tidak punya listener → tujuan langsung membalas **TCP RST** → gagal seketika (exit 1, ±0,005 s), bukan menggantung. Menggantung hanya terjadi bila paket di-DROP firewall.

---

<a id="soal-9"></a>
## Soal 9 — Jalur belakang diutak-atik

#### Langkah 1 — Shigeru: jalankan script sabotase dari asisten (tanpa dibaca)

```sh
cat > /root/break_routing.sh << 'EOF'
(isi break_routing.sh dari asisten)
EOF
sh /root/break_routing.sh
```

#### Langkah 2 — Tunjukkan gejala

```sh
# di Lilith  -> GAGAL
ping -c 3 192.231.4.83
# di Armisael -> BERHASIL
ping -c 3 192.231.4.81
```

#### Langkah 3 — Shigeru: diagnosis

```sh
cat > /root/diag9.sh << 'EOF'
#!/bin/bash
echo "=== 1. IP forwarding ==="
cat /proc/sys/net/ipv4/ip_forward
echo "=== 2. Tabel routing utama ==="
ip route
echo "=== 3. Policy routing (ip rule) ==="
ip rule
echo "=== 4. Isi tabel 100 ==="
ip route show table 100
echo "=== 5. Simulasi keputusan routing ==="
echo "-- paket dari Armisael (masuk eth2) ke Lilith:"
ip route get 192.231.4.74 from 192.231.4.83 iif eth2
echo "-- paket dari Shigeru ke Lilith:"
ip route get 192.231.4.74
echo "-- paket dari Shigeru ke internet:"
ip route get 8.8.8.8
EOF
chmod +x /root/diag9.sh
```

**Jalankan:** `/root/diag9.sh`

| # | Ditemukan lewat | Kondisi rusak | Dampak |
|---|---|---|---|
| 1 | `ip_forward` | `0` | Shigeru tidak meneruskan paket |
| 2 | `ip route` | `default via 192.231.4.162` (ke Makoto) | Routing loop Shigeru ↔ Makoto |
| 3 | `ip route` | `blackhole 192.231.4.72/29` | Paket ke Dogma (Lilith) dibuang |
| 4 | `ip rule`, `ip route show table 100` | `iif eth2 lookup 100` → `unreachable default` | Paket dari LAN Monolith yang diteruskan ditolak |

#### Langkah 4 — Shigeru: perbaikan

```sh
cat > /root/fix_routing.sh << 'EOF'
#!/bin/bash
# Soal 9 - perbaikan sabotase routing di Shigeru
echo "1) Nyalakan lagi IP forwarding"
sysctl -w net.ipv4.ip_forward=1
echo "2) Kembalikan default route ke Gendo"
ip route replace default via 192.231.4.129 dev eth0
echo "3) Hapus blackhole Dogma"
ip route del blackhole 192.231.4.72/29 2>/dev/null
echo "4) Hapus policy routing ke tabel 100"
while ip rule del iif eth2 lookup 100 2>/dev/null; do :; done
ip route flush table 100 2>/dev/null
echo "5) Pastikan rute ke Radar tetap ada"
ip route replace 192.231.4.88/29 via 192.231.4.162
echo "=== Hasil ==="
cat /proc/sys/net/ipv4/ip_forward
ip route
ip rule
EOF
chmod +x /root/fix_routing.sh
```

**Jalankan:** `/root/fix_routing.sh`

#### Langkah 5 — Lilith: buktikan bisa ping Armisael

```sh
ping -c 3 192.231.4.83
```

### Penjelasan gejala

- **Armisael bisa ping gateway sendiri:** tujuan 192.231.4.81 adalah alamat lokal Shigeru → dicocokkan tabel `local` (rule prioritas 0) sebelum rule 90 dan tidak perlu forwarding, jadi sabotase tidak berpengaruh.
- **Lilith tidak mendapat jawaban:** jalur harus diteruskan Shigeru dan terhalang 4 lapis (forwarding mati, tabel 100 unreachable, blackhole Dogma, default ke Makoto). Semua harus diperbaiki.

---

<a id="soal-10"></a>
## Soal 10 — Para pilot terkunci di dalam

#### Langkah 1 — Misato dan Gendo: jalankan script sabotase dari asisten

```sh
cat > /root/break_nat.sh << 'EOF'
(isi break_nat.sh dari asisten)
EOF
sh /root/break_nat.sh
```

#### Langkah 2 — Tunjukkan gejala

```sh
# di Unit-01 -> GAGAL
ping -c 3 8.8.8.8
# di Spy -> BERHASIL
ping -c 3 8.8.8.8
# di Unit-01 ke Lilith -> BERHASIL
ping -c 3 192.231.4.74
```

#### Langkah 3 — Misato dan Gendo: diagnosis

```sh
cat > /root/diag10.sh << 'EOF'
#!/bin/bash
echo "=== [$(hostname)] NAT POSTROUTING ==="
iptables -t nat -L POSTROUTING -n -v --line-numbers
echo "=== iptables -t nat -S ==="
iptables -t nat -S
EOF
chmod +x /root/diag10.sh
/root/diag10.sh
```

**Jalankan:** `/root/diag10.sh` di keduanya. Tambahan di Gendo: `tcpdump -ni eth4 icmp` (paket Cage datang bersumber 172.31.99.1).

| # | Router | Rule sabotase | Dampak |
|---|---|---|---|
| 1 | Misato | `SNAT -s 192.231.4.64/29 ! -d 192.231.0.0/16 -o eth0 --to-source 172.31.99.1` | Sumber Cage diganti 172.31.99.1 |
| 2 | Gendo | `RETURN -s 192.231.4.64/29 -o eth0` di urutan 1 | Cage melewati MASQUERADE |

#### Langkah 4 — Misato: perbaikan

```sh
cat > /root/fix_nat.sh << 'EOF'
#!/bin/bash
# Soal 10 - hapus SNAT sabotase di Misato (hapus semua salinan bila dobel)
while iptables -t nat -D POSTROUTING -s 192.231.4.64/29 ! -d 192.231.0.0/16 -o eth0 -j SNAT --to-source 172.31.99.1 2>/dev/null; do :; done
echo "=== NAT Misato setelah perbaikan ==="
iptables -t nat -L POSTROUTING -n -v --line-numbers
EOF
chmod +x /root/fix_nat.sh
/root/fix_nat.sh
```

#### Langkah 5 — Gendo: perbaikan

```sh
cat > /root/fix_nat.sh << 'EOF'
#!/bin/bash
# Soal 10 - hapus RETURN sabotase di Gendo, pastikan MASQUERADE ada
while iptables -t nat -D POSTROUTING -s 192.231.4.64/29 -o eth0 -j RETURN 2>/dev/null; do :; done
iptables -t nat -C POSTROUTING -s 192.231.0.0/16 -o eth0 -j MASQUERADE 2>/dev/null || \
iptables -t nat -A POSTROUTING -s 192.231.0.0/16 -o eth0 -j MASQUERADE
echo "=== NAT Gendo setelah perbaikan ==="
iptables -t nat -L POSTROUTING -n -v --line-numbers
EOF
chmod +x /root/fix_nat.sh
/root/fix_nat.sh
```

#### Langkah 6 — Buktikan

```sh
# di Unit-01 -> BERHASIL
ping -c 3 8.8.8.8
# di Gendo -> counter MASQUERADE naik
iptables -t nat -L POSTROUTING -n -v
```

### Penjelasan gejala

- **Cage gagal ke internet:** sumber 172.31.99.1 tidak cocok izin FORWARD `-s 192.231.0.0/16` di Gendo → policy DROP (karena itu counter RETURN = 0). RETURN adalah jebakan lapis kedua: bila Misato saja diperbaiki, Cage keluar tanpa MASQUERADE dan balasan tidak kembali.
- **Intel (Spy) tidak terdampak:** kedua rule hanya untuk sumber `192.231.4.64/29`; Spy lewat Kaji dan tetap di-MASQUERADE.
- **Cage → Lilith normal:** rule Misato mengecualikan tujuan internal, rule Gendo hanya `-o eth0`.
- **Alamat sumber diterjemahkan di Gendo**, saat keluar lewat eth0 (NAT1), oleh MASQUERADE soal 4.

---

## Kendala & Solusi

| Kendala | Penyebab | Solusi |
|---|---|---|
| Link tidak bisa ping padahal IP benar | Kabel GNS3 "mati" setelah adapter diubah saat berjalan / setelah crash | Stop node → hapus kabel → sambung ulang di port yang sama |
| Gendo tidak dapat DHCP | Kabel NAT1 mati; `udhcpc` tidak ada | Sambung ulang; `dhclient -v eth0` |
| *Controller is unreachable* saat Save Edit config | Baris berisi `/etc/resolv.conf` | Tulis `/etc/network/interfaces` lewat console |
| Script `/root` hilang setelah restart | `/root` tidak persistent | Additional persistent directories → `/root` |
| Rute hilang setelah restart | Rute hanya dari script | Simpan di `/etc/network/interfaces` (`up ip route add ...`) |
| `Temporary failure in name resolution` | DNS kosong / jalur putus | `nameserver 8.8.8.8`; `traceroute` untuk cari hop putus |
| `trixie-security ... not signed` | Verifikasi repo security | Diabaikan; paket tetap terpasang |

## Soal 11 - Casper harus jadi hantu

Pada tahap ini, node `Magi-Casper` (`192.231.4.58`) dikonfigurasi agar tidak dapat di-`ping` oleh perangkat mana pun di jaringan, termasuk tetangga satu *switch* di `Switch-Terminal` seperti `Magi-Balthasar` (`192.231.4.59`). Firewall dipasang langsung di `Magi-Casper` pada *chain* `INPUT` untuk membuang paket `ICMP echo-request` masuk, namun tetap mengizinkan `ICMP echo-reply` (balasan *ping* keluar milik Casper sendiri) serta menjaga layanan **TCP port 5000** tetap aktif.

Script dijalankan pada `Magi-Casper`:

```bash
cat << 'EOF' > /root/no11.sh
#!/bin/bash
# Soal 11 - Magi-Casper: blokir ping masuk, izinkan ping keluar & TCP 5000

iptables -F INPUT
iptables -P INPUT ACCEPT

# Izinkan loopback dan balasan koneksi milik Casper sendiri (termasuk echo-reply ping)
iptables -A INPUT -i lo -j ACCEPT
iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT

# Tolak semua permintaan ping (ICMP echo-request) yang masuk ke Casper
iptables -A INPUT -p icmp --icmp-type echo-request -j DROP

# Pastikan layanan TCP 5000 tetap dapat diakses
iptables -A INPUT -p tcp --dport 5000 -j ACCEPT

echo "=== [Magi-Casper] Aturan INPUT aktif ==="
iptables -L INPUT -n -v --line-numbers
EOF

chmod +x /root/no11.sh
/root/no11.sh
```

**Pengujian:**
Dijalankan pada `Magi-Casper` (uji *ping* keluar ke Balthasar dan Lilith), serta pada `Magi-Balthasar` dan `Lilith` (uji *ping* gagal dan `nc` ke TCP 5000 berhasil):
```bash
# Dijalankan pada Magi-Casper (192.231.4.58)
ping -c 3 192.231.4.59
ping -c 3 192.231.4.74

# Dijalankan pada Magi-Balthasar (192.231.4.59)
ping -c 3 192.231.4.58
echo "tes soal 11 dari balthasar" | nc -w 2 192.231.4.58 5000; echo "Exit code: $?"

# Dijalankan pada Lilith (192.231.4.74)
ping -c 3 192.231.4.58
echo "tes soal 11 dari lilith" | nc -w 2 192.231.4.58 5000; echo "Exit code: $?"

# Cek counter drop dan log pesan masuk di Magi-Casper
iptables -L INPUT -n -v
cat /root/pesan_5000.log
```

**Hasil:**
![Soal 11 Casper](img/soal_11_casper.png)
![Soal 11 Balthasar](img/soal_11_balthasar.png)
![Soal 11 Lilith](img/soal_11_lilith.png)


## Soal 12 - Teknisi yang kelewat semangat

Pada soal ini, dijalankan *script* sabotase `break_fw.sh` pada `Magi-Casper` (`192.231.4.58`) yang merusak konektivitas keluar (`ping 8.8.8.8` dan `apt-get update`) serta memutus akses `Unit-00` (`192.231.4.68`) ke layanan TCP port `5000`.

### 1. Eksekusi Script Sabotase & Diagnosis

Script sabotase dijalankan pada `Magi-Casper`:

```bash
cat << 'EOF' > /root/break_fw.sh
#!/bin/sh
if [ "$FORCE" != 1 ]; then case "$(hostname)" in *[Cc]asper*) ;; *) echo "Script ini hanya untuk Magi-Casper."; exit 1;; esac; fi
[ "$(id -u)" = 0 ] || { echo "Harus root"; exit 1; }
command -v iptables >/dev/null 2>&1 || { echo "iptables belum terpasang (soal 7)."; exit 1; }
iptables -P INPUT ACCEPT
iptables -P OUTPUT ACCEPT
iptables -F INPUT
iptables -F OUTPUT
iptables -A INPUT -i lo -j ACCEPT
iptables -A INPUT -p icmp -j DROP
iptables -A OUTPUT -p udp --dport 53 -j DROP
iptables -P INPUT DROP
echo "Script sudah dirusak"
EOF

chmod +x /root/break_fw.sh
sh /root/break_fw.sh
```

**Penyebab Kerusakan & Penjelasan Gejala:**
1. **`iptables -P INPUT DROP` tanpa `ESTABLISHED,RELATED` dan tanpa izin TCP `5000`:** Kebijakan default `INPUT DROP` membuang seluruh paket masuk selain *loopback*. Akibatnya, koneksi baru dari `Unit-00` menuju **TCP 5000** langsung dibuang, dan paket balasan HTTP saat menjalankan `apt-get update` juga ikut dibuang.
2. **`iptables -A INPUT -p icmp -j DROP`:** Aturan ini memblokir seluruh tipe ICMP yang masuk, termasuk `ICMP echo-reply` (tipe 0). Ketika `Magi-Casper` mengirim `ping 8.8.8.8`, paket keluar berhasil dikirim melalui `OUTPUT`, tetapi paket balasannya ditolak di `INPUT`.
3. **`iptables -A OUTPUT -p udp --dport 53 -j DROP`:** Aturan ini memblokir seluruh kueri DNS keluar (UDP port 53), sehingga `apt-get update` gagal me-resolve alamat domain repositori `deb.debian.org`.

### 2. Perbaikan Firewall

Script perbaikan dijalankan pada `Magi-Casper` (tetap mempertahankan *policy* `INPUT DROP` dan pemblokiran *ping* masuk dari Soal 11):

```bash
cat << 'EOF' > /root/fix_fw.sh
#!/bin/bash
# Soal 12 - perbaikan sabotase firewall di Magi-Casper (tetap menjaga aturan Soal 11)

iptables -P INPUT ACCEPT
iptables -P OUTPUT ACCEPT
iptables -F INPUT
iptables -F OUTPUT

# 1. Izinkan loopback
iptables -A INPUT -i lo -j ACCEPT

# 2. Izinkan balasan koneksi milik Casper sendiri (ping keluar, DNS, apt-get update)
iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT

# 3. Tetap tolak ping masuk ke Casper (menjaga syarat Soal 11)
iptables -A INPUT -p icmp --icmp-type echo-request -j DROP

# 4. Izinkan koneksi masuk ke layanan TCP 5000 (MAGI listener)
iptables -A INPUT -p tcp --dport 5000 -j ACCEPT

# 5. Pertahankan default policy INPUT DROP sesuai syarat hardening Soal 12
iptables -P INPUT DROP
iptables -P OUTPUT ACCEPT

echo "=== [Magi-Casper] iptables -S setelah perbaikan ==="
iptables -S
EOF

chmod +x /root/fix_fw.sh
/root/fix_fw.sh
```

**Pengujian:**
Dijalankan pada `Unit-00` (`192.231.4.68`) untuk menguji koneksi TCP `5000` dan penolakan `ping`, serta pada `Magi-Casper` untuk menguji internet dan `apt-get update`:
```bash
# Dijalankan pada Unit-00 (192.231.4.68)
echo "tes soal 12 dari Unit-00" | nc -w 2 192.231.4.58 5000; echo "Exit code: $?"
ping -c 3 192.231.4.58

# Dijalankan pada Magi-Casper (192.231.4.58)
ping -c 3 8.8.8.8
apt-get update
cat /root/pesan_5000.log
iptables -S
```

**Hasil:**
![Soal 12 Break](img/soal_12_break.png)
![Soal 12 Fix](img/soal_12_fix.png)
![Soal 12 Unit-00](img/soal_12_unit00.png)


## Soal 13 - Brankas Balthasar

Pada tahap ini, akses ke layanan **TCP port 5000 dan 5001** pada `Magi-Balthasar` (`192.231.4.59`) dibatasi hanya untuk `Magi-Casper` (`192.231.4.58`) dan seluruh anggota kategori **Pilot** (`Unit-01` `192.231.4.66`, `Unit-02` `192.231.4.67`, `Unit-00` `192.231.4.68`, dan `Sachiel` `192.231.3.130`). Perangkat lain—termasuk `Magi-Melchior` (`192.231.4.60`) yang berada satu *switch*—ditolak seketika menggunakan target `REJECT --reject-with tcp-reset` agar koneksi langsung gagal tanpa menunggu *timeout*.

Script dijalankan pada `Magi-Balthasar`:

```bash
cat << 'EOF' > /root/no13.sh
#!/bin/bash
# Soal 13 - Magi-Balthasar: batasi TCP 5000 & 5001 hanya untuk Casper dan semua Pilot

ALLOWED_IPS="192.231.4.58 192.231.4.66 192.231.4.67 192.231.4.68 192.231.3.130"
PORTS="5000,5001"

iptables -F INPUT
iptables -P INPUT ACCEPT

# 1. Izinkan loopback dan balasan koneksi milik Balthasar sendiri (ping 8.8.8.8, dll.)
iptables -A INPUT -i lo -j ACCEPT
iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT

# 2. Izinkan Casper dan semua Pilot mengakses TCP 5000 & 5001
for IP in $ALLOWED_IPS; do
  iptables -A INPUT -p tcp -s $IP -m multiport --dports$PORTS -j ACCEPT
done

# 3. Tolak seketika (TCP RST) semua node lain pada TCP 5000 & 5001
iptables -A INPUT -p tcp -m multiport --dports $PORTS -j REJECT --reject-with tcp-reset

echo "=== [Magi-Balthasar] Aturan INPUT aktif ==="
iptables -L INPUT -n -v --line-numbers
EOF

chmod +x /root/no13.sh
/root/no13.sh
```

**Pengujian:**
Dijalankan pada `Unit-01` (berhasil), `Lilith` (ditolak seketika), `Magi-Melchior` (ditolak seketika), dan `Magi-Balthasar` (cek koneksi internet dan log):
```bash
# Dijalankan pada Unit-01 (192.231.4.66) -> Harus berhasil (Exit code: 0)
for P in 5000 5001; do
  START=$(date +%s.%N)
  echo "Halo Balthasar port $P dari Unit-01" | nc -w 3 192.231.4.59 $P; RC=$?
  END=$(date +%s.%N)
  echo "Port $P -> Exit code: $RC \vert{} Durasi:$(awk "BEGIN{print $END-$START}") detik"
done

# Dijalankan pada Lilith (192.231.4.74) & Magi-Melchior (192.231.4.60) -> Ditolak seketika
for P in 5000 5001; do
  START=$(date +%s.%N)
  nc -w 3 192.231.4.59 $P < /dev/null; RC=$?
  END=$(date +%s.%N)
  echo "Port $P -> Exit code: $RC \vert{} Durasi:$(awk "BEGIN{print $END-$START}") detik"
done

# Dijalankan pada Magi-Balthasar (192.231.4.59)
ping -c 3 8.8.8.8
cat /root/pesan_5000.log /root/pesan_5001.log
iptables -L INPUT -n -v
```

**Hasil:**
![Soal 13 Balthasar](img/soal_13_balthasar.png)
![Soal 13 Unit-01](img/soal_13_unit01.png)
![Soal 13 Reject](img/soal_13_reject.png)


## Soal 14 - Jam kunjungan sang pinguin

Pada soal ini, node `Pen-Pen` (`192.231.3.194`) dilindungi dengan pembatasan akses berbasis waktu menggunakan modul `-m time --utc` pada `iptables` untuk lalu lintas `ping` (`ICMP echo-request`) dan layanan **TCP port 80**. Jadwal dikonversi dari WIB ke UTC (7 jam lebih lambat dari WIB) dan disimpan dalam lima variabel wajib pada bagian awal *script*:
- **Pilot** (`Unit-01`, `Unit-02`, `Unit-00`, `Sachiel`): 08.00–16.00 WIB (`01:00`–`09:00` UTC), Senin–Jumat.
- **Staf Geofront** (`Lilith`, `Adam`): 17.00–23.00 WIB (`10:00`–`16:00` UTC), setiap hari.
- **Terduga Dummy System** (`Dummy-Plug`, `Entry-Plug`, `Spy`): selalu ditolak (`DROP`).
- **Lainnya**: diizinkan tanpa batasan waktu.

Script dijalankan pada `Pen-Pen`:

```bash
cat << 'EOF' > /root/no14.sh
#!/bin/bash
# Soal 14 - Pen-Pen: pembatasan jadwal akses ping & TCP 80 (format waktu UTC = WIB - 7)

PILOT_START="01:00"
PILOT_STOP="09:00"
PILOT_DAYS="Mon,Tue,Wed,Thu,Fri"
STAF_START="10:00"
STAF_STOP="16:00"

PILOT_IPS="192.231.4.66 192.231.4.67 192.231.4.68 192.231.3.130"
STAF_IPS="192.231.4.74 192.231.4.75"
DUMMY_IPS="192.231.4.51 192.231.4.50 192.231.4.102"

iptables -F INPUT
iptables -P INPUT ACCEPT

# 1. Izinkan loopback dan balasan koneksi milik Pen-Pen sendiri
iptables -A INPUT -i lo -j ACCEPT
iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT

# 2. Pilot: izinkan ping & TCP 80 hanya pada jadwal PILOT, sisanya DROP
for IP in $PILOT_IPS; do
  iptables -A INPUT -s $IP -p icmp --icmp-type echo-request -m time --utc --timestart$PILOT_START --timestop $PILOT_STOP --weekdays$PILOT_DAYS -j ACCEPT
  iptables -A INPUT -s $IP -p tcp --dport 80 -m time --utc --timestart$PILOT_START --timestop $PILOT_STOP --weekdays$PILOT_DAYS -j ACCEPT
  iptables -A INPUT -s $IP -p icmp --icmp-type echo-request -j DROP
  iptables -A INPUT -s $IP -p tcp --dport 80 -j DROP
done

# 3. Staf Geofront: izinkan ping & TCP 80 hanya pada jadwal STAF, sisanya DROP
for IP in $STAF_IPS; do
  iptables -A INPUT -s $IP -p icmp --icmp-type echo-request -m time --utc --timestart $STAF_START --timestop$STAF_STOP -j ACCEPT
  iptables -A INPUT -s $IP -p tcp --dport 80 -m time --utc --timestart $STAF_START --timestop$STAF_STOP -j ACCEPT
  iptables -A INPUT -s $IP -p icmp --icmp-type echo-request -j DROP
  iptables -A INPUT -s $IP -p tcp --dport 80 -j DROP
done

# 4. Terduga Dummy System: selalu tolak ping & TCP 80
for IP in $DUMMY_IPS; do
  iptables -A INPUT -s $IP -p icmp --icmp-type echo-request -j DROP
  iptables -A INPUT -s $IP -p tcp --dport 80 -j DROP
done

echo "=== Waktu UTC Pen-Pen saat ini: $(date -u) ==="
iptables -L INPUT -n -v --line-numbers
EOF

chmod +x /root/no14.sh
/root/no14.sh
```

**Pengujian:**
Pengujian dilakukan pada dua kondisi dengan menyesuaikan nilai variabel `PILOT_START`, `PILOT_STOP`, `STAF_START`, dan `STAF_STOP` (tanpa mengubah jam sistem), kemudian diuji dari `Unit-01` (`192.231.4.66`), `Lilith` (`192.231.4.74`), dan `Spy` (`192.231.4.102`):
```bash
# Dijalankan pada masing-masing klien uji (Unit-01, Lilith, dan Spy)
ping -c 3 192.231.3.194
nc -w 2 192.231.3.194 80 < /dev/null; echo "Exit code: $?"
```

**Hasil:**
![Soal 14 Jadwal Cocok](img/soal_14_cocok.png)
![Soal 14 Jadwal Tidak Cocok](img/soal_14_tidak_cocok.png)


## Soal 15 - Banjir ping di pusat komando

Pada tahap ini, `Tokyo3-Core` (`192.231.4.106`) dikonfigurasi untuk membatasi `ping` masuk (`ICMP echo-request`) maksimal **5 ping per detik** menggunakan modul `-m limit --limit 5/second --limit-burst 5` dan membuang kelebihannya (`DROP`). Aturan pembatasan diletakkan **sebelum** aturan `ESTABLISHED,RELATED` agar seluruh paket dalam satu sesi *ping* rapat tetap melewati penghitung kuota, sementara paket *ping* yang dikirim oleh `Tokyo3-Core` sendiri tetap bebas karena balasannya bertipe `ICMP echo-reply`.

Script dijalankan pada `Tokyo3-Core`:

```bash
cat << 'EOF' > /root/no15.sh
#!/bin/bash
# Soal 15 - Tokyo3-Core: batasi ping masuk maksimal 5/detik, buang kelebihannya

iptables -F INPUT
iptables -P INPUT ACCEPT

# 1. Izinkan loopback
iptables -A INPUT -i lo -j ACCEPT

# 2. Batasi ping masuk (ICMP echo-request) maksimal 5 per detik
iptables -A INPUT -p icmp --icmp-type echo-request -m limit --limit 5/second --limit-burst 5 -j ACCEPT

# 3. Buang seluruh kelebihan ping masuk yang melampaui batas
iptables -A INPUT -p icmp --icmp-type echo-request -j DROP

# 4. Izinkan balasan koneksi milik Tokyo3-Core sendiri (termasuk echo-reply ping keluar)
iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT

echo "=== [Tokyo3-Core] Aturan INPUT aktif ==="
iptables -L INPUT -n -v --line-numbers
EOF

chmod +x /root/no15.sh
/root/no15.sh
```

**Pengujian:**
Dijalankan pada `Lilith` (`192.231.4.74`) untuk simulasi banjir *ping* dan *ping* normal berjeda, serta pada `Tokyo3-Core` untuk memeriksa *counter* `DROP` dan *ping* keluar:
```bash
# Dijalankan pada Lilith (192.231.4.74) - Banjir ping (sebagian paket hilang)
ping -i 0.1 -c 50 192.231.4.106

# Dijalankan pada Lilith setelah jeda minimal 3 detik - Ping normal (0% loss)
sleep 3
ping -i 3 -c 4 192.231.4.106

# Dijalankan pada Tokyo3-Core (192.231.4.106)
iptables -L INPUT -n -v --line-numbers
ping -c 4 192.231.4.74
```

**Hasil:**
![Soal 15 Flood](img/soal_15_flood.png)
![Soal 15 Normal](img/soal_15_normal.png)
![Soal 15 Counter](img/soal_15_counter.png)


## Soal 16 - Pencuri yang mencoba setiap pintu

Pada soal ini, `Tokyo3-Core` (`192.231.4.106`) dikonfigurasi untuk mendeteksi dan menolak seketika percobaan pemindaian port TCP dari `Dummy-Plug` (`192.231.4.51`) menuju port sensitif **22, 23, dan 3306**. Setiap percobaan dicatat terlebih dahulu oleh target `LOG` dengan awalan `SCAN-NERV: `, kemudian langsung diputus dengan `REJECT --reject-with tcp-reset` tanpa mengganggu akses port layanan **80 dan 8080** maupun klien lainnya.

Script dijalankan pada `Tokyo3-Core`:

```bash
cat << 'EOF' > /root/no16.sh
#!/bin/bash
# Soal 16 - Tokyo3-Core: catat (SCAN-NERV:) & tolak seketika TCP 22, 23, 3306 dari Dummy-Plug

DUMMY_IP="192.231.4.51"
TRAP_PORTS="22,23,3306"

iptables -F INPUT
iptables -P INPUT ACCEPT

# 1. Izinkan loopback
iptables -A INPUT -i lo -j ACCEPT

# 2. Pertahankan aturan Soal 15 (limit ping masuk maksimal 5/detik)
iptables -A INPUT -p icmp --icmp-type echo-request -m limit --limit 5/second --limit-burst 5 -j ACCEPT
iptables -A INPUT -p icmp --icmp-type echo-request -j DROP

# 3. Izinkan balasan koneksi milik Tokyo3-Core sendiri
iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT

# 4. Soal 16: Catat percobaan TCP dari Dummy-Plug ke port 22, 23, 3306 lalu tolak seketika
iptables -A INPUT -s $DUMMY_IP -p tcp -m multiport --dports$TRAP_PORTS -j LOG --log-prefix "SCAN-NERV: "
iptables -A INPUT -s $DUMMY_IP -p tcp -m multiport --dports$TRAP_PORTS -j REJECT --reject-with tcp-reset

echo "=== [Tokyo3-Core] Aturan INPUT aktif ==="
iptables -L INPUT -n -v --line-numbers
EOF

chmod +x /root/no16.sh
/root/no16.sh
```

**Pengujian:**
Dijalankan pada `Dummy-Plug` (`192.231.4.51`) untuk menguji penolakan instan pada port 22, 23, 3306 serta keberhasilan akses ke port 80 dan 8080, lalu memeriksa *counter* pencatatan di `Tokyo3-Core`:
```bash
# Dijalankan pada Dummy-Plug (192.231.4.51)
for P in 22 23 3306; do
  START=$(date +%s.%N)
  nc -w 3 192.231.4.106 $P < /dev/null; RC=$?
  END=$(date +%s.%N)
  echo "Port $P -> Exit code: $RC \vert{} Durasi:$(awk "BEGIN{print $END-$START}") detik"
done

for P in 80 8080; do
  OUT=$(nc -w 3 192.231.4.106 $P < /dev/null 2>&1); RC=$?
  echo "Port $P -> Balasan: $OUT \vert{} Exit code:$RC"
done

# Dijalankan pada Tokyo3-Core (192.231.4.106)
iptables -L INPUT -n -v --line-numbers
```

**Hasil:**
![Soal 16 Dummy-Plug](img/soal_16_dummy.png)
![Soal 16 Counter](img/soal_16_counter.png)


## Soal 17 - Sihir hitam di jalur paket

Pada tahap ini, router `Fuyutsuki` (sebagai *gateway* dari `Switch-Terminal`) dikonfigurasi menggunakan **Destination NAT (`DNAT`)** pada tabel `nat` *chain* `PREROUTING` untuk membelokkan seluruh lalu lintas **TCP** dari `Magi-Casper` (`192.231.4.58`) yang ditujukan ke `Zeruel` (`192.231.4.82`) agar masuk ke `Pen-Pen` (`192.231.3.194`). Dengan memanfaatkan mekanisme *stateful* `conntrack` pada `Fuyutsuki`, paket balasan dari `Pen-Pen` otomatis diterjemahkan kembali seolah berasal dari `Zeruel`, sementara paket `ping` (ICMP) tetap melintas langsung ke `Zeruel`.

Script dijalankan pada `Fuyutsuki`:

```bash
cat << 'EOF' > /root/no17.sh
#!/bin/bash
# Soal 17 - Fuyutsuki: DNAT seluruh koneksi TCP dari Magi-Casper ke Zeruel agar masuk ke Pen-Pen

CASPER_IP="192.231.4.58"
ZERUEL_IP="192.231.4.82"
PENPEN_IP="192.231.3.194"

iptables -t nat -F PREROUTING

# Alihkan hanya trafik TCP dari Magi-Casper ke Zeruel menuju Pen-Pen (ICMP/ping tidak ikut dialihkan)
iptables -t nat -A PREROUTING -s $CASPER_IP -d $ZERUEL_IP -p tcp -j DNAT --to-destination$PENPEN_IP

echo "=== [Fuyutsuki] Tabel NAT PREROUTING ==="
iptables -t nat -L PREROUTING -n -v --line-numbers
EOF

chmod +x /root/no17.sh
/root/no17.sh
```

**Pengujian:**
Setelah menyalakan *listener* TCP port `9000` di `Pen-Pen` dan `Zeruel`, pengiriman pesan dan *ping* dijalankan dari `Magi-Casper`, lalu diperiksa pada log penerima dan tabel `conntrack` di `Fuyutsuki`:
```bash
# Dijalankan pada Magi-Casper (192.231.4.58)
echo "Pesan rahasia untuk Zeruel dari Casper" | nc -w 2 192.231.4.82 9000; echo "Exit code: $?"
ping -c 2 192.231.4.82

# Dijalankan pada Pen-Pen & Zeruel
cat /root/pesan_9000.log

# Dijalankan pada Fuyutsuki
conntrack -L -p tcp --dport 9000
iptables -t nat -L PREROUTING -n -v --line-numbers
```

**Hasil:**
![Soal 17 Casper](img/soal_17_casper.png)
![Soal 17 Penerima](img/soal_17_penerima.png)
![Soal 17 Conntrack](img/soal_17_conntrack.png)


## Soal 18 - Gerbang Monolith dan Radar

Pada soal ini, router `Shigeru` dikonfigurasi pada *chain* `FORWARD` untuk menjaga zona sensitif (`Switch-Monolith` dan `Switch-Radar`). Seluruh lalu lintas baru yang masuk melalui *interface* `eth0` (sisi yang menghadap `Gendo`) diblokir secara menyeluruh tanpa menuliskan daftar subnet luar satu per satu, dengan satu pengecualian khusus bagi permintaan `ping` (`ICMP echo-request`) yang berasal dari subnet `Terminal` (`192.231.4.56/29`). Node di dalam zona tetap bebas memulai koneksi ke luar karena paket balasannya diizinkan melalui `ESTABLISHED,RELATED`.

Script dijalankan pada `Shigeru`:

```bash
cat << 'EOF' > /root/no18.sh
#!/bin/bash
# Soal 18 - Shigeru: lindungi zona sensitif (Monolith & Radar) berdasarkan interface eth0

TERMINAL_SUBNET="192.231.4.56/29"
WAN_UP="eth0"   # interface Shigeru yang menghadap Gendo

iptables -F FORWARD
iptables -P FORWARD ACCEPT

# 1. Izinkan paket balasan (agar node dalam zona bebas memulai koneksi ke mana saja)
iptables -A FORWARD -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT

# 2. Pengecualian: izinkan ping (ICMP echo-request) dari subnet Terminal masuk lewat eth0
iptables -A FORWARD -i $WAN_UP -s$TERMINAL_SUBNET -p icmp --icmp-type echo-request -j ACCEPT

# 3. Tolak semua lalu lintas baru lainnya yang datang dari sisi Gendo (eth0) ke zona sensitif
iptables -A FORWARD -i $WAN_UP -j DROP

echo "=== [Shigeru] Aturan FORWARD aktif ==="
iptables -L FORWARD -n -v --line-numbers
EOF

chmod +x /root/no18.sh
/root/no18.sh
```

**Pengujian:**
Dijalankan pada `Magi-Casper` (berhasil mem-ping `Zeruel`), `Lilith` (gagal mem-ping `Zeruel` dan `Radar-Array`), serta `Zeruel` (berhasil mem-ping ke luar):
```bash
# Dijalankan pada Magi-Casper (192.231.4.58) -> Berhasil
ping -c 3 192.231.4.82

# Dijalankan pada Lilith (192.231.4.74) -> Gagal
ping -c 3 192.231.4.82
ping -c 3 192.231.4.90

# Dijalankan pada Zeruel (192.231.4.82) -> Berhasil keluar
ping -c 3 192.231.4.74
ping -c 3 8.8.8.8
```

**Hasil:**
![Soal 18 Pengujian](img/soal_18_test.png)
![Soal 18 Shigeru](img/soal_18_shigeru.png)


## Soal 19 - Hukuman untuk sang pengkhianat

Pada tahap ini, `Armisael` (`192.231.4.83`) diisolasi total tanpa menyentuh konfigurasi di dalam node `Armisael` itu sendiri dan tanpa mengganggu `Zeruel` (`192.231.4.82`). Pemblokiran dilakukan di dua titik: pada router `Shigeru` (memblokir `INPUT`, `OUTPUT`, dan `FORWARD` dari/ke `Armisael` sehingga terputus dari *gateway* dan jaringan luar) serta pada host `Zeruel` (memblokir komunikasi langsung antar-tetangga satu *switch*).

Script dijalankan pada `Shigeru`:

```bash
cat << 'EOF' > /root/no19.sh
#!/bin/bash
# Soal 19 - Shigeru: isolasi total Armisael (192.231.4.83) tanpa mengganggu Zeruel

ARMISAEL_IP="192.231.4.83"
TERMINAL_SUBNET="192.231.4.56/29"
WAN_UP="eth0"

iptables -F INPUT
iptables -F OUTPUT
iptables -F FORWARD
iptables -P INPUT ACCEPT
iptables -P OUTPUT ACCEPT
iptables -P FORWARD ACCEPT

# 1. Blokir total Armisael dari/ke gateway Shigeru dan lintas jaringan
iptables -A INPUT   -s $ARMISAEL_IP -j DROP
iptables -A OUTPUT  -d $ARMISAEL_IP -j DROP
iptables -A FORWARD -s $ARMISAEL_IP -j DROP
iptables -A FORWARD -d $ARMISAEL_IP -j DROP

# 2. Pertahankan aturan Soal 18 untuk zona Monolith & Radar
iptables -A FORWARD -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT
iptables -A FORWARD -i $WAN_UP -s$TERMINAL_SUBNET -p icmp --icmp-type echo-request -j ACCEPT
iptables -A FORWARD -i $WAN_UP -j DROP

echo "=== [Shigeru] Aturan INPUT, OUTPUT, FORWARD ==="
iptables -L -n -v --line-numbers
EOF

chmod +x /root/no19.sh
/root/no19.sh
```

Script dijalankan pada `Zeruel`:

```bash
cat << 'EOF' > /root/no19.sh
#!/bin/bash
# Soal 19 - Zeruel: blokir lalu lintas masuk & keluar dengan tetangga satu switch (Armisael)

ARMISAEL_IP="192.231.4.83"

iptables -F INPUT
iptables -F OUTPUT
iptables -P INPUT ACCEPT
iptables -P OUTPUT ACCEPT

iptables -A INPUT  -s $ARMISAEL_IP -j DROP
iptables -A OUTPUT -d $ARMISAEL_IP -j DROP

echo "=== [Zeruel] Aturan INPUT & OUTPUT ==="
iptables -L -n -v --line-numbers
EOF

chmod +x /root/no19.sh
/root/no19.sh
```

**Pengujian:**
Setelah *listener* TCP port `9000` dinyalakan pada `Armisael`, `Zeruel`, dan `Lilith`, pengujian `ping` dan `nc` dijalankan dari `Armisael` (semua gagal) dan dari `Zeruel` (semua berhasil):
```bash
# Dijalankan pada Armisael (192.231.4.83) -> Semua gagal
ping -c 2 8.8.8.8
ping -c 2 192.231.4.82
ping -c 2 192.231.4.74
nc -w 2 8.8.8.8 53 < /dev/null; echo "Seele:53 -> Exit code: $?"
echo "tes" | nc -w 2 192.231.4.82 9000; echo "Zeruel:9000 -> Exit code: $?"
echo "tes" | nc -w 2 192.231.4.74 9000; echo "Lilith:9000 -> Exit code: $?"

# Dijalankan pada Zeruel (192.231.4.82) -> Semua berhasil
ping -c 2 8.8.8.8
ping -c 2 192.231.4.74
nc -w 2 8.8.8.8 53 < /dev/null; echo "Seele:53 -> Exit code: $?"
echo "Halo Lilith dari Zeruel" | nc -w 2 192.231.4.74 9000; echo "Lilith:9000 -> Exit code: $?"
```

**Hasil:**
![Soal 19 Armisael](img/soal_19_armisael.png)
![Soal 19 Zeruel](img/soal_19_zeruel.png)


## Soal 20 - Uji kebakaran Geofront

Pada tahap akhir ini, dilakukan simulasi bencana dan pemulihan idempoten (*idempotent recovery*) pada tiga node yang terdiri atas dua router dan satu *host*, yaitu `Gendo`, `Fuyutsuki`, dan `Magi-Casper`. Pada masing-masing node, seluruh *rule* `iptables` disimpan terlebih dahulu ke berkas `/root/<nama>.rules`, kemudian seluruh *rule* (termasuk tabel `nat`) dan rute statis dihapus, lalu dipulihkan sebanyak **dua kali berturut-turut** menggunakan `iptables-restore` dan `/root/<nama>.sh`.

Script pemulihan pada `Fuyutsuki` (`/root/Fuyutsuki.sh`):

```bash
cat << 'EOF' > /root/Fuyutsuki.sh
#!/bin/bash
sysctl -w net.ipv4.ip_forward=1 >/dev/null
ip route replace default via 192.231.4.133
ip route replace 192.231.3.192/27 via 192.231.4.166
ip route replace 192.231.4.72/29 via 192.231.4.170

iptables -t nat -F PREROUTING
iptables -t nat -A PREROUTING -s 192.231.4.58 -d 192.231.4.82 -p tcp -j DNAT --to-destination 192.231.3.194
EOF

chmod +x /root/Fuyutsuki.sh
/root/Fuyutsuki.sh
```

Script pemulihan pada `Magi-Casper` (`/root/Magi-Casper.sh`):

```bash
cat << 'EOF' > /root/Magi-Casper.sh
#!/bin/bash
ip route replace default via 192.231.4.57

iptables -P INPUT ACCEPT
iptables -P OUTPUT ACCEPT
iptables -F INPUT
iptables -F OUTPUT
iptables -A INPUT -i lo -j ACCEPT
iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT
iptables -A INPUT -p icmp --icmp-type echo-request -j DROP
iptables -A INPUT -p tcp --dport 5000 -j ACCEPT
iptables -P INPUT DROP

if ! ss -ltnp 2>/dev/null | grep -q ":5000\b"; then
  setsid bash -c '
    while true; do
      MSG=$(nc -l -p 5000 2>/dev/null)
      [ -n "$MSG" ] && echo "[$(date +%H:%M:%S)] $(hostname) port 5000 menerima:$MSG" >> /root/pesan_5000.log
    done' magi-listener-5000 >/dev/null 2>&1 &
fi
EOF

chmod +x /root/Magi-Casper.sh
/root/Magi-Casper.sh
```

**Pengujian:**
Dijalankan pada masing-masing node (`Gendo`, `Fuyutsuki`, dan `Magi-Casper`) dengan siklus simpan `.rules` → hapus *firewall* & rute → pulihkan dua kali berturut-turut → verifikasi jumlah baris dan fungsi jaringan:
```bash
# 1. Simpan rules aktif
iptables-save > /root/<nama>.rules

# 2. Hapus seluruh rule iptables (termasuk nat) dan rute statis
iptables -P INPUT ACCEPT; iptables -P FORWARD ACCEPT; iptables -P OUTPUT ACCEPT
iptables -F; iptables -X; iptables -t nat -F; iptables -t nat -X
# (diikuti penghapusan rute pada masing-masing node)

# 3. Pulihkan 2 kali berturut-turut & bandingkan jumlah baris
iptables-restore < /root/<nama>.rules && /root/<nama>.sh
echo "Pemulihan 1 -> filter: $(iptables -S | wc -l) | nat: $(iptables -t nat -S \vert{} wc -l) \vert{} rute:$(ip route | wc -l)"

iptables-restore < /root/<nama>.rules && /root/<nama>.sh
echo "Pemulihan 2 -> filter: $(iptables -S | wc -l) | nat: $(iptables -t nat -S \vert{} wc -l) \vert{} rute:$(ip route | wc -l)"
```

**Hasil:**
![Soal 20 Gendo](img/soal_20_gendo.png)
![Soal 20 Fuyutsuki](img/soal_20_fuyutsuki.png)
![Soal 20 Casper](img/soal_20_casper.png)

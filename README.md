# SmartFlow — Ochiq server

Maktab davomat tizimining **internetga chiqadigan** qismi. Ota-ona va
o'quvchi ko'radigan ma'lumot shu yerda: baholar, davomat, kundalik, dars
jadvali, e'lonlar va bildirishnomalar.

## Nima uchun alohida

Tizim ataylab ikkiga bo'lingan.

**Maktab serveri** maktabning o'z kompyuterida, o'z tarmog'ida qoladi.
Unda o'quvchilarning suratlari va yuz shakllari, kameralarning parollari,
o'qituvchi va direktor hisoblari turadi. Bu ma'lumotni internetga
chiqarishning hojati yo'q — chiqarmaslik esa uni himoyalashning eng ishonchli
usuli.

**Bu server** — ijaradagi mashinada, internetdan ochiq. Unda faqat ota-onaga
ko'rsatiladigan ma'lumot bor va u maktab serveridan **bir tomonlama**
sinxronizatsiya orqali keladi. Bu server buzilsa ham, undan kameraga yoki
o'quvchilar suratiga yo'l yo'q — chunki ular bu yerda umuman saqlanmaydi.

Ikki server orasidagi aloqa maktab kaliti bilan tekshiriladi
(`X-School-Key`), va serverda kalitning faqat **xeshi** saqlanadi.

## Nima bor

| | |
|---|---|
| Ota-ona kirishi | telefon raqami → SMS kod → parol |
| O'quvchi kirishi | login va parol |
| Ko'rsatadi | baholar, davomat, kundalik, jadval, reyting, e'lonlar |
| Bildirishnoma | Firebase orqali (ixtiyoriy — kalitsiz ham ishlaydi) |
| SMS | sms.robita.tj panel orqali, yoki umumiy gateway |

Tasdiqlash kodlari bazada **ochiq saqlanmaydi**: tuz bilan xeshlanadi,
muddati cheklangan, taxmin urinishlari ham, kod so'rash tezligi ham
cheklangan.

## O'rnatish

Ikki yo'l bor, ikkalasi ham `deploy/` papkasida tushuntirilgan:

- **[deploy/DOCKER.md](deploy/DOCKER.md)** — Docker bilan. Bo'sh serverda eng
  tez yo'l: PostgreSQL va ilova ikkita konteynerda, bitta buyruq bilan.
- **[deploy/README.md](deploy/README.md)** — Docker'siz: `install.sh`,
  systemd xizmati, nginx va certbot.

Ikkalasida ham baza jadvallari birinchi ishga tushishda o'zi yaratiladi —
alohida migratsiya buyrug'i yo'q.

### Qisqacha (Docker)

```bash
cp .env.example .env
nano .env                      # ikkita majburiy qator to'ldiriladi
docker compose up -d --build
```

`SMARTSCHOOL_PUBLIC_AUTH_SECRET` va `POSTGRES_PASSWORD` bo'sh qolsa compose
ataylab ishga tushmaydi. Standart maxfiy kalit bilan jimgina ishlayotgan
server — bu istalgan odam istalgan ota-ona nomidan token yasay oladigan
server.

```bash
openssl rand -hex 32
```

## HTTPS

Domen sotib olish shart emas, lekin **DNS nomi kerak**. HTTPSsiz telefon
raqami, SMS kodi, parol va token ochiq matnda uzatiladi — yo'ldagi har kim
o'qiy oladi, va ushlangan token bilan bolaning baholari muddatsiz ko'riladi.

IP `5.61.12.34` bo'lsa, `5-61-12-34.sslip.io` ro'yxatdan o'tmasdan o'sha IP ga
ishora qiladi va certbot unga sertifikat beradi. Tafsilotlari
[deploy/DOCKER.md](deploy/DOCKER.md) ning 8-bo'limida.

## Ishlab chiqish

```bash
python -m venv venv
venv/bin/pip install -r requirements.txt
venv/bin/python main.py          # http://localhost:8200/docs
venv/bin/python -m pytest -q
```

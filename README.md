# Sistem Pemesanan Hotel

## 1. Membuat Database

```
MariaDB [(none)]> create database db_pemesananHotel;
Query OK, 1 row affected (0.003 sec)

MariaDB [(none)]> use db_pemesananHotel;
Database changed
```

---

# 2. Membuat Tabel dan Mengisi Data

## Tabel dan Data Pengunjung

```
MariaDB [db_pemesananHotel]> create table tb_pengunjung (
-> Id int primary key,
-> Nama varchar(50),
-> NoTelp varchar(100));
Query OK, 0 rows affected (0.019 sec)
```

```
MariaDB [db_pemesananHotel]> insert into tb_pengunjung values
-> (1, 'Andi','0812345671'),
-> (2, 'Budi','0812345672'),
-> (3, 'Yandi','0812345673'),
-> (4, 'Eka','0812345674'),
-> (5, 'Tarno','0812345675'),
-> (6, 'Samsul','0812345676');
Query OK, 6 rows affected (0.011 sec)
Records: 6  Duplicates: 0  Warnings: 0
```

---

## Tabel dan Data Kamar

```
MariaDB [db_pemesananHotel]> create table tb_kamar (
-> Id int primary key,
-> NomorKamar int,
-> TipeKamar varchar(100),
-> Harga int,
-> StatusKamar varchar(100));
Query OK, 0 rows affected (0.015 sec)
```

```
MariaDB [db_pemesananHotel]> insert into tb_kamar values
-> (1, 101, 'Standar', 10000000, 'Kosong'),
-> (2, 105, 'VIP', 25000000, 'Kosong'),
-> (3, 202, 'VIP', 20000000, 'Kosong'),
-> (4, 209, 'Standar', 15000000, 'Kosong'),
-> (5, 333, 'VVIP', 25000000, 'Kosong'),
-> (6, 367, 'VVIP', 20000000, 'Kosong');
Query OK, 6 rows affected (0.006 sec)
Records: 6  Duplicates: 0  Warnings: 0
```

---

## Tabel dan Data Pemesanan

```
MariaDB [db_pemesananHotel]> create table tb_pemesanan (
-> Id int primary key,
-> PengunjungId int,
-> KamarId int,
-> TanggalCheckIn date,
-> TanggalCheckOut date,
-> foreign key (PengunjungId) references tb_pengunjung(Id),
-> foreign key (KamarId) references tb_kamar(Id));
Query OK, 0 rows affected (0.046 sec)
```

```
MariaDB [db_pemesananHotel]> insert into tb_pemesanan values
-> (1, 5, 5, '2026-03-04','2026-03-11'),
-> (2, 6, 1, '2026-03-02','2026-03-09'),
-> (3, 5, 6, '2026-03-04','2026-03-11'),
-> (4, 3, 4, '2026-03-05','2026-03-15');
Query OK, 4 rows affected (0.006 sec)
Records: 4  Duplicates: 0  Warnings: 0
```

---

# 3. Query INNER JOIN

Menampilkan data pemesanan kamar beserta nama pengunjung dan nomor kamar.

```
MariaDB [db_pemesananHotel]> select tb_pemesanan.Id, tb_pengunjung.Nama, tb_kamar.NomorKamar,
tb_pemesanan.TanggalCheckIn, tb_pemesanan.TanggalCheckOut
-> from tb_pemesanan
-> inner join tb_pengunjung on tb_pemesanan.PengunjungId = tb_pengunjung.Id
-> inner join tb_kamar on tb_pemesanan.KamarId = tb_kamar.Id;
```

### Hasil Query

```
+----+--------+------------+----------------+-----------------+
| Id | Nama   | NomorKamar | TanggalCheckIn | TanggalCheckOut |
+----+--------+------------+----------------+-----------------+
| 1  | Tarno  | 333        | 2026-03-04     | 2026-03-11      |
| 2  | Samsul | 101        | 2026-03-02     | 2026-03-09      |
| 3  | Tarno  | 367        | 2026-03-04     | 2026-03-11      |
| 4  | Yandi  | 209        | 2026-03-05     | 2026-03-15      |
+----+--------+------------+----------------+-----------------+
4 rows in set (0.007 sec)
```

---

# 4. Query LEFT JOIN

Menampilkan semua kamar dan apakah kamar tersebut sudah dipesan atau belum.

```
MariaDB [db_pemesananHotel]> select tb_kamar.NomorKamar, tb_pemesanan.Id
from tb_kamar
left join tb_pemesanan
on tb_pemesanan.KamarId = tb_kamar.Id;
```

### Hasil Query

```
+------------+------+
| NomorKamar | Id   |
+------------+------+
| 101        | 2    |
| 105        | NULL |
| 202        | NULL |
| 209        | 4    |
| 333        | 1    |
| 367        | 3    |
+------------+------+
6 rows in set (0.002 sec)
```

Keterangan:
Jika **Id bernilai NULL**, berarti kamar belum dipesan.

---

# 5. Query RIGHT JOIN

Menampilkan semua pengunjung termasuk yang belum melakukan pemesanan kamar.

```
MariaDB [db_pemesananHotel]> select tb_pengunjung.Nama, tb_pemesanan.TanggalCheckIn,
tb_pemesanan.TanggalCheckOut
from tb_pemesanan
right join tb_pengunjung
on tb_pemesanan.PengunjungId = tb_pengunjung.Id;
```

### Hasil Query

```
+--------+----------------+-----------------+
| Nama   | TanggalCheckIn | TanggalCheckOut |
+--------+----------------+-----------------+
| Tarno  | 2026-03-04     | 2026-03-11      |
| Samsul | 2026-03-02     | 2026-03-09      |
| Tarno  | 2026-03-04     | 2026-03-11      |
| Yandi  | 2026-03-05     | 2026-03-15      |
| Andi   | NULL           | NULL            |
| Budi   | NULL           | NULL            |
| Eka    | NULL           | NULL            |
+--------+----------------+-----------------+
7 rows in set (0.003 sec)
```

Keterangan:
Jika **tanggal bernilai NULL**, berarti pengunjung belum melakukan pemesanan kamar.

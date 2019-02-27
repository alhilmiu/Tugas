**Scraping website** https://www.kliknklik.com

**Tujuan** **:** Memperoleh data monitor, seperti (merk monitor, tipe monitor, harga monitor,)

 yang di jual pada website tersebut.

**Tools yang digunakan:**

1. Pycharm include Python 2.7.
2. Library Python seperti Beautiful Soup / bs4, sqlite3, urllib.  

**#NB** jika ingin lebih mudah bisa kalian mendownload aplikasi anaconda , karena di dalam anaconda sudah terdapat tools diatas.

**Langkah-langkah:**

1. Mencari website yang akan di scraping, saya menggunakan web **(https://www.kliknklik.com)**

2. Menentukan **top level url web** yang akan di scrapping untuk mendapatkan source code html, misal **(https://kliknklik.com/298-monitor).**

3. Menginstall library urllib pada terminal ubuntu, source code **(*pip install urllib*)**

4. Menginstall library Beautiful Soup  pada terminal ubuntu dan pastikan terkoneksi internet, source code **(*apt-get install python-bs4*,*pip install beatifulsoup4*).**

   (Karna saya menggunakan aplikasi Anaconda jadi saya tidak perlu menginstall library lagi.)

5. Buka Aplikasi Anaconda , lalu klik Evironments lalu klik base (root)

6. Setelah itu akan langsung tersambung dengan Jupyter, setelah itu klik new lalu pilih Python3

7. Lalu **Inisialisasi ulrllib**,

   `from urllib.request import urlopen`
   `html = urlopen("https://kliknklik.com/298-monitor").read()`
   `print(type(html))`
   `print(html[1:1000])`

   **urllib** adalah library python yang digunakan untuk mengakses web, sedangkan fungsi **urlopen()** dan **read()** digunakan untuk membaca HTML yang dikonversi dalam format **String**.

   **[1:1000]**  digunakan untuk mengambil 1000 data dalam 1 tampilan website.

8. **Inisialisasi BeautifulSoup**, tambahkan source code

   `from bs4 import BeautifulSoup`
   `soup = BeautifulSoup(html, "lxml")`
   `print(type(soup))`
   `print(soup.prettify()[1:1000])`

   **beautifulsoup** digunakan untuk membantu proses scrapping dengan cara mengimport librarynya, lalu string html diubah menjadi objek beautifulsoup menggunakan parser **lxml.**

9. **Inisialisasi sqlite3**, tambahkan source code

   `import sqlite3`
   `conn = sqlite3.connect('kliknklik.db')`
   `c = conn.cursor()`

   **sqlite3** digunakan untuk memanipulasi database menggunakan python, **kliknklik.db** adalah nama database yang akan dimanipulasi.

10. Tambahkan source code di bawah ini,

    `c.execute('DROP TABLE IF EXISTS Monitor')`
    `c.execute('''CREATE TABLE IF NOT EXISTS Monitor`
                 `(merk VARCHAR, tipe VARCHAR, harga INTEGER)''')`
    `conn.commit()`

    code diatas digunakan untuk membuat tabel **Monitor** dengan attribut yang telah di tentukan pada database **kliknklik.db**.

    `produk = soup.find_all("div","product-block ")`

    digunakan untuk memfilter setiap class ***"product-block"*** pada tag <div> html pada website , karena pada frame tersebut berisi beberapa elemen yang dapat kita ambil datanya, misalnya (merk, harga dll).

    ( untuk menemukan **tag dan class html yang akan di filter maka sebelumnya lakukanlah inspect element / mode view tag html pada web browser** hal tersebut tergantung pada posisi letak setiap objek pada tag html setiap website hal tersebut dilakukan secara looping secara otomatis sebanyak program menemukan frame tersebut untuk melakukan scrapping data website.)

    `for p in produk:`
        `tipe=(p.find('a','product-name').get_text().strip())`
        `merk=tipe[:tipe.index(' ')].strip()`

    `print(merk)`
        `tipe=tipe[len(merk):].strip()`
        `print(tipe)`
        `harga=p.find('div','product-price').get_text().replace('Rp','').replace('.','').replace(' ','').strip()`
        `print(harga)`

    digunakan untuk memfilter merk pada setiap class ***"product-name"*** pada tag **"a"** html, namun lalu langsung dibedakan sesuai merk, type dan harga.

    `c.execute("INSERT INTO Monitor (merk, tipe, harga) values (?, ?,?)", (merk,tipe, harga))`
    `conn.commit()`

    `c.execute('SELECT * FROM Monitor')`

    digunakan untuk memasukkan atau insert data kedalam tabel **Monitor** yang telah diinisialisasi sebelumnya setiap 1x looping atau setiap menemukan frame.
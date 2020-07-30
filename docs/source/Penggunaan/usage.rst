Penggunaan
===========

ELK (Elasticsearch, Logstash dan Kibana)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Elasticsearch, Logstash dan Kibana adalah tools yang berguna untuk mengumpulkan log dan juga menvisualisasi atau menampilkan log , Elasticsearch berguna untuk menyimpan semua log yang berasal dari server, Logstash merupakan sebuah perangkat lunak open source untuk mengumpulkan dan memparsing log dan juga membuat index untuk log, kemudian disimpan pada elasticsearch. Kibana adalah web interface yang berguna untuk menampilkan log baik dalam bentuk grafik maupun visualisasi lainnya. Untuk file agent, diperlukan filebeat yang berguna untuk mengirim log dari setiap server kepada logstash Log yang tersentralisasi sangat berguna jika suatu saat seorang DevOps akan melakukan identifikasi masalah pada server atau aplikasi. Hal tersebut membuat mereka mampu mencari histori data atau log. Pada kesempatan ini, seluruh log kami dapatkan dari server Dionaea dan Cowrie yang berfungsi sebagai Honeypot. Kami juga menggunakan log yang dihasilkan dari sensor yang kami buat, dalam hal ini Snort.

Komponen ELK
~~~~~~~~~~~~

Pada praktikum ini dimana membuat bank data terkait malware maka ELK memiliki tugas dan fungsi sebagai berikut:

a. Logstash : memproses log dari malware dan membuat index log malware.
b. Elasticsearch : menyimpan semua log malware.
c. Kibana : Web interface untuk mencari dan memvisualisasikan log malware dalam grafik yang di inginkan.
d. Filebeat : mengirim log ke logstash. Berfungsi sebagai shipping agent untuk log. Menggunakan lumberjack networking protocol untuk berkomunikasi dengan logstash. Filebeat akan dipasang di server yang mempunyai log (yang akan diproses).

Tampilan
~~~~~~~~

Berikut merupakan tampilan dari Kibana dan Log

a. Tampilan Kibana (putih)
   
Pada bagian ini ditampilkan tampilan Kibana yang lebih menonjolkan informasi serangan berdasarkan negara penyerang. Setiap warna yang ada pada gambar di bawah ini mengindikasikan banyaknya serangan yang dilakukan oleh negara-negara tersebut kepada server. Pada tampilan ini juga memberikan informasi tentang apa saja yang dilakukan oleh penyerang dan malware apa saja yang dimasukkan penyerang ke dalam server.
   
.. image:: 1.jpg

b. Tampilan Kibana (hitam)

Pada bagian ini ditampilkan perubahan tampilan terakhir yang kami lakukan. Pada tampilan ini, kami lebih mengedepankan informasi yang informatif tentang serangan yang dilakukan penyerang. Kami menampilkan map dengan indikator border putih untuk menunjukkan asal negara penyerang secara total, data penyerang terbanyak, dan malware terbanyak yang dimasukkan pada server. Selain itu, kami juga menunjukkan grafik serangan setiap hari dan waktu. Hal ini digunakan agar monitoring pada sistem lebih terjaga.

.. image:: 3.png

c. Tampilan Log

Pada bagian ini ditampilkan log yang kami dapatkan dari server. Semua log ini memiliki ketentuan-ketentuan tertentu yang kemudian kami olah menjadi seperti pada tampilan-tampilan Kibana sebelumnya.

.. image:: 2.png



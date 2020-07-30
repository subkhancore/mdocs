Instalasi
=========

Pada manual ini akan dijelaskan tahap instalasi dalam membuat dashboard malware monitoring menggunakan ELK Stack dan Snort3 dengan cara mengambil dependencies langsung dari repository masing-masing tools.

Menyiapkan ELK Stack
--------------------

ELK Stack merupakan sekumpulan tools open source yang terdiri dari Elasticsearch, Logstash dan Kibana yang memiliki fungsi masing-masing Elasticsearch berguna untuk menyimpan semua log yang berasal dari server, Logstash merupakan sebuah perangkat lunak open source untuk mengumpulkan dan memparsing log dan juga membuat index untuk log, kemudian disimpan pada elasticsearch. Kibana adalah web interface yang berguna untuk menampilkan log baik dalam bentuk grafik maupun visualisasi lainnya.

Install Java 8
--------------

Elasticsearch memerlukan Java 8 untuk dapat berjalan, oleh karena itu diperlukan instalasi Oracle Java 8.

tambahkan repository Java 8 kemudian dilanjutkan dengan update system dan instalasi.

Perintahnya:

.. code-block:: shell

  add-apt-repository ppa:webupd8team/java
  apt-get update
  apt install oracle-java8-set-default

Setelah instalasi pastikan versi java sudah benar dengan menggunakan perintah berikut

.. code-block:: shell

  java-version
  echo $JAVA_HOME

Update versi ELK
~~~~~~~~~~~~~~~~

Lakukan update versi ELK menjadi versi 7.8.x agar mendapatkan fitur tambahan dan tampilan interface yang terbaru

pertama dengan perintah wget untuk mengunduh Elasticsearch dengan kunci publiknya

.. code-block:: shell

  sudo wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -

selanjutnya mengunduh tools untuk mengirimkan paket dengan https

.. code-block:: shell

  sudo wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -

menambahkan repositorynya

.. code-block:: shell

  echo "deb https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-7.x.list  

setelah semua selesai lakukan update daftar repository dan install paketnya

Elasticsearch
-------------

Elasticsearch adalah tools open-source, mesin pencarian dan analisis berdasarkan mesin Apache Lucene dan dibangun dengan RESTful API. Elasticsearch menawarkan kemudahan untuk pengolahan  data dengan cara dapat menyimpan, mencari dan analisa data yang berukuran besar. Fungsinya yang digunakannya yaitu untuk melakukan filtering dan query dari data.

lakukan install Elasticsearch versi 7.8, perintah instalasi

.. code-block:: shell

  apt-get install elasticsearch

pastikan bahwa layanan sudah berjalan

.. code-block:: shell

  systemctl status elasticsearch
 
ketika layanan berjalan, lakukan konfigurasi file elasticsearch.yml dengan mengubah network.host dan http.port menjadi

.. code-block:: shell

  ---------- Paths ----------

  path.data: /var/lib/elasticsearch
  path.logs: /var/log/elasticsearch

  ---------- Network ---------

  network.host: 103.133.56.233
  http.port: 9200

setelah disimpan dan keluar, lakukan kembali restart sistem layanan. Lakukan cek instalasi dengan perintah

.. code-block:: shell

  curl http://localhost:9200


Logstash
---------

Logstash adalah tools untuk menyimpan data secara pipeline. Berfungsi untuk menyimpan data input dan memberikan ke Elasticsearch. Logstash mengambil semua tipe data dari berbagai sumber dan dibuat agar dapat digunakan untuk berbagai kegunaan. Fungsi yang digunakan adalah analisa berbagai struktur/non-struktur data dan events.

Perintahnya untuk instalasi Logstash

.. code-block:: shell

  apt-get install logstash

lakukan cek status layanan logstash

.. code-block:: shell

  systemctl status logstash

pada logstash terdapat tiga file konfigurasi yang menghubungkan input log dari server berbeda seperti cowrie, dionaea dan snort
berikut adalah file konfigurasinya

logstash-cowrie.conf

.. code-block:: shell

  input {
     
       file {
              path => ["/home/cowrie/cowrie/var/log/cowrie/cowrie.js$
              codec => json
              type => "cowrie"
       }
  output {
    if [type] == "cowrie" {
        elasticsearch {
            hosts => ["103.133.56.233:9200"]
            index => "kel4-cowrie-%{+YYY.MM.dd}"
        }
        file {
            path => "/tmp/cowrie-logstash.log"
            codec => json
        }

logstash-dionaea.conf

.. code-block:: shell

  input {
        beats {
                port => 5044
        }
  }

  output {
        if [document_type] == "dionaea" {
                elasticsearch {
                        hosts => ["103.133.56.233:9200"]
                        index => "kel4-dionaea-%{+YYYY.MM.dd}"
                }

logstash-snort.conf

.. code-block:: shell

  input {
    file {
        path => "/var/log/snort/alert_json*"
        start_position => "beginning"
        sincedb_path => "/dev/null"
    }
  }

  output {
    elasticsearch {
        hosts => "http://103.133.56.233:9200"
        index => "kel4-snort3-%{+YYYY.MM.dd}"
    }
    stdout { }
  }

Kibana
------

Kibana adalah visualisasi data yang melengkapi ELK Stack. Tools ini digunakan untuk visualisasi file yang sudah analisa dan diparsing oleh Elasticsearch untuk memudahkan pengembang dalam melihat data. Dashboard Kibana menawarkan berbagai diagram interactive, data geospatial, dan graph untuk visualisasi data kompleks.

Perintah untuk instalasi Kibana

.. code-block:: shell

  apt-get install kibana

lakukan cek layanan Kibana

.. code-block:: shell

  ssytemctl status kibana

lakukan konfigurasi Kibana di file konfigurasinya yaitu kibana.yml, lakukan dengan perintah berikut:

.. code-block:: shell

  vim /etc/kibana/kibana.yml

kemudian ubah beberapa konfigurasi pada filenya disesuaikan dengan sistem. Simpan dan keluar, kemudian lakukan restart layanan Kibana

.. code-block:: shell
  
  server.port: 5601

  server.host: "103.133.56.233"

  elasticsearch.host: [http://103.133.56.233:9200:]

dan ubah perintah server hostnya menjadi localhost. Simpan dan keluar, kemudian lakukan restart layanan Kibana

Meskipun ELK Stack hanya terdiri dari tiga tools utama yaitu Elasticsearch, Logstash dan Kibana. Namun terdapat beberapa tools pendukung lain yang diperlukan agar fungsi dashboard malware ini dapat berjalan sempurna, beberapa dependenciesnya yang diinstal sebagai berikut:

Filebeat
~~~~~~~~~~

.. code-block:: shell

  apt-get install filebeat

lakukan cek layanan Filebeat untuk memastikan sudah berjalan

.. code-block:: shell

  systemctl status filebeat

ubah beberapa konfigurasi pada file filebeat.yml

.. code-block:: shell

  filebeat.inputsL
  - type: log
    paths:
    - /opt/dionaea/var/dionaea/json/dionaea.json*

    fields:
      document_type: dionaea
    fields_under_root: true
  output:
    logstash:
      host: ["103.133.56.233:5044"]
      bulk_max_size: 2048

SqliteToJson
~~~~~~~~~~~~~

SqliteToJson berguna untuk melakukan konversi struktur data SQL menjadi struktur data JSON yang digunakan oleh ELK, data SQL yang diambil dari server dionaea.

Tools diunduh dari repository yang ada di github, sehingga perintahnya:

.. code-block:: shell

  wget https://raw.githubusercontent.com/eva2A/dionaeaToJSON/master/dionaeaSqliteToJson.py

Nginx
~~~~~

Penggunaan nginx pada ELK Stack adalah sebagai reverse proxy untuk keamanan jaringan dengan menjadi server yang menjadi penyimpanan logs

cara instalasinya

.. code-block:: shell

  apt install nginx apache2-utils


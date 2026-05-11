## Event-Driven Architecture: Publisher and Message Broker

### a. How much data your publisher program will send to the message broker in one run?
Dalam satu kali eksekusi (*one run*), program *publisher* ini akan mengirimkan tepat 5 (lima) buah data/pesan (*events*) ke *message broker*. Hal ini dapat diverifikasi dari pemanggilan metode `send` yang diinstruksikan sebanyak lima kali secara berurutan di dalam fungsi `main`, dengan memuat data `user_id` dari 1 hingga 5.

### b. The url of: "amqp://guest:guest@localhost:5672" is the same as in the subscriber program, what does it mean?
Kesamaan URL ini mengindikasikan bahwa baik program *publisher* maupun *subscriber* terhubung ke *instance message broker* (RabbitMQ) yang sama persis. Dalam *event-driven architecture*, ini adalah prasyarat mutlak; *publisher* harus menembak data ke alamat *server* dan *port* yang sama dengan tempat *subscriber* mendengarkan (*listening*), sehingga aliran pesan (*message routing*) dapat terhubung dan diantarkan dengan sukses.

### Running RabbitMQ
Berikut adalah tampilan RabbitMQ Management UI yang membuktikan *message broker* telah berjalan di Docker lokal:
![RabbitMQ UI](assets/images/rabbitmq_ui.png)

### Event Processing
![Console Output](assets/images/console_output.png)

Ketika perintah `cargo run` dieksekusi pada *publisher*, program tersebut secara instan memproduksi dan mengirimkan 5 *event messages* secara berurutan menuju *message broker* (RabbitMQ) melalui protokol AMQP. Di sisi lain, karena program *subscriber* sudah dalam state *listening* secara asinkron terhadap antrean (*queue*) yang sama di RabbitMQ, *subscriber* langsung mendeteksi kedatangan *event* tersebut, mengonsumsinya (*consume*), dan memprosesnya (dalam hal ini, mencetak isinya ke *console*). Ini mendemonstrasikan sifat *decoupled* dari *Event-Driven Architecture*.

### Monitoring Chart
![Chart Spike](assets/images/chart_spike.png)

Lonjakan tajam (*spike*) yang terlihat pada grafik "Message rates" terjadi karena saya menjalankan program *publisher* secara berulang-ulang dengan cepat. Setiap kali dieksekusi, *publisher* menembakkan 5 pesan ke dalam *exchange* RabbitMQ. Karena *subscriber* memproses pesan tanpa jeda (tanpa *delay*), metrik pengiriman ("Publish") dan penerimaan ("Deliver/Get") melonjak seketika di milidetik yang bersamaan, dan langsung kembali turun ke titik nol setelah semua antrean langsung terselesaikan.
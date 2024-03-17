# Laporan Proyek Machine Learning - Fadhillah Akmal

## Project Overview

Dalam era digital saat ini, banyak pengguna internet memanfaatkan aplikasi seperti jasa *streaming* video secara online untuk mendapatkan informasi dan konten hiburan [[1](https://www.sciencedirect.com/science/article/abs/pii/S0747563217306581)]. Salah satu nya adalah konten anime yang seiring waktu pandemik Covid-19 mulai bertransisi dan beradaptasi dengan dunia digital dan internet. Contoh dari hal ini adalah Crunchyroll, Funimation, dan HIDIVE yang merupakan penyedia *streaming* anime secara online dengan katalog anime dan jumlah penonton yang besar [[2](https://www.jef.or.jp/journal/pdf/234th_Special_Article_01.pdf)]. 

Meningkatnya penyedia *streaming anime* serta banyaknya jumalh anime yang rilis setiap waktunya membuat penonton atau pengguna merasa kesulitan dalam menemukan anime yang sesuai dengan preferensi mereka. Hal ini menyebabkan kebutuhan penonton dalam memilih anime, salah satu metodenya adalah dengan menggunakan sistem rekomendasi berbasis *machine learning* [[3](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=4121831)]

Dalam pengembangan sistem rekomendasi untuk anime, terdapat beberapa faktor yang mempengaruhi rekomendasi [[4](https://ieeexplore.ieee.org/abstract/document/10074101)][[5](https://link.springer.com/article/10.1007/s11042-023-14710-9)]. Faktor seperti judul, genre, tipe, dan deskripsi anime lainnya ataupun berdasarkan jejak preferensi pengguna dapat mempengaruhi sistem rekomendasi. Model yang dipilih dalam membuat rekomendasi ini adalah yang dapat mempelajari faktor-faktor tersebut. Pada proyek ini dibuat model *content-based filtering* dengan *cosine similiarity* berdasarkan deskripsi anime yang ditonton serta model *collaborative filtering* dengan *K-Nearest Neighbor* berdasarkan perilaku dan preferensi dari penonton.

## Business Understanding

Sistem rekomendasi untuk judul anime memiliki manfaat untuk penonton dan penyedia jasa *streaming* anime. Penonton dapat dimudahkan dalam mencari pilihan anime mengikut preferensi penonton serta penyedia jasa *streaming* dapat mempertahankan atau memperbanyak konsumen dengan membuat konsumen tetap menggunakan jasa *streaming* dari penyedia jasa tersebut. 

### Problem Statements

- Apa saja karakteristik dan fitur pada dataset yang dapat dimanfaatkan dalam pembuatan sistem rekomendasi ?
- Bagaimana model sistem rekomendasi anime dengan *cosine similiarity* dan *K-Nearest Neighbor* dapat dibuat ?
- Bagaimana cara untuk mengukur performa sistem rekomendasi yang telah dibuat ?
  
### Goals

- Mengetahui karakteristik dan fitur yang tersedia pada dataset serta memilih fitur dalam membuat sistem rekomendasi.
- Membuat model yang dapat memberikan rekomendasi judul anime.
- Mengukur performa sistem menggunakan metrik evaluasi.

### Solution Approach

- Melakukan *exploratory data analysis* untuk mengetahui fitur-fitur pada dataset dengan menggunakan teknik univariat.
- Melakukan pembuatan model sistem rekomendasi berbasis algoritma *cosine similiarity* dan *K-Nearest Neighbor* serta melakukan preparasi dan pembersihan pada data agar mendapatkan performa model yang baik.
- Melakukan pengukuran performa model menggunakan metrik presisi dan *hit rate at K*.


## Data Understanding

Data yang digunakan dalam pengembangan sistem rekomendasi pada proyek ini adalah hasil ekstraksi halaman web *My Anime List* yang merupakan situs web komunitas yang digunakan untuk mencari, melacak, dan mendiskusikan anime yang pengguna telah tonton. Data yang didapatkan merupakan data informasi anime serta data rating yang diberikan oleh user. 

Terdapat dua file dalam bentuk format csv dari dataset. Dari kedua file csv, didapatkan 12294 jumlah anime yang tercatat pada file anime.csv dan 7813737 jumlah rating yang diberikan pengguna pada file rating.csv, dimana terdapat 73515 pengguna unik yang memberikan rating.

Sumber dataset : (https://www.kaggle.com/datasets/CooperUnion/anime-recommendations-database/data)

### Variabel-variabel pada Dataset

Terdapat 7 variabel pada file anime.csv dan 3 variabel pada file rating.csv

- anime.csv
  - anime_id : Nomer ID yang digunakan website myanimelist.net untuk mengidentifikasi suatu anime.
  - name : Judul anime. 
  - genre : Genre dari anime, berbentuk list yang berisi beberapa genre.
  - type : Tipe format dari anime (movie, TV, OVA, dan lain lain.).
  - episodes : Jumlah episode.
  - rating : Rating keseluruhan pada website myanimelist.net (Dari nilai 0 hingga 10).
  - members : Jumlah pengikut pada halaman anime tersebut di website myanimelist.net.

- rating.csv
  - user_id : Nomor id yang digunakan untuk mengidentifikasi user.
  - anime_id : Nomer ID yang digunakan website myanimelist.net untuk mengidentifikasi suatu anime.
  - rating : Rating yang diberikan pengguna pada suatu anime. Nilainya 0 hingga 10, dan -1 apabila user telah menonton, tetapi tidak memberikan rating.

### Exploratory Data Analysis

Proses selanjutnya adalah melakukan analisis data dengan menggunakan metode *exploratory data analysis*. Teknik analisis data yang digunakan adalah dengan melakukan analisis univariat pada variabel di dataset.

Analisis univariat merupakan teknik analisis data yang berfokus pada satu variabel pada satu waktu. Teknik ini dilakukan untuk mengetahui distribusi dan frekuensi data dari suatu dataset. Visualisasi data yang dilakukan menggunakan grafik batang untuk data kategori dan historgram untuk data numerik.

Berikut hasil analisis univariat untuk fitur kategori pada dataset : 

![download (21)](https://github.com/fadhillaha/proyek-akhir-sistem-rekomendasi/assets/40687068/15e165b0-2cd5-4f40-976a-ef02fb007034)

Gambar 1. Analisis Univariat untuk Kategori 'Genre'

![download (22)](https://github.com/fadhillaha/proyek-akhir-sistem-rekomendasi/assets/40687068/3ebf974a-4ba0-4b49-9fe5-2dc959ec44b4)

Gambar 2. Analisis Univariat untuk Kategori 'Type'

| Judul      | Member      | 
| ----- | -------- | 
| Death Note | 1013917 |
| Shingeki no Kyojin |  896229 |
| Sword Art Online |  893100 |
| Fullmetal Alchemist: Brotherhood |  793665 |
| Angel Beats! |  717796 |

Tabel 1. 5 Anime dengan Pengikut Terbanyak

Dari analisis data kategori, dapat diketahui bahwa :

- Genre anime paling banyak adalah genre comedy dengan jumlah 4645 data. Kemudian menyusul oleh genre action dan adventure

- Mayoritas dari anime pada dataset termasuk tipe yang tayang pada televisi (TV) ataupun OVA.

- Anime yang memiliki pengikut pengguna paling banyak pada website myanimelist.net adalah Death Note dengan 1,013,917 pengikut.

Berikut hasil analisis univariat untuk fitur numerik pada dataset : 

![download (23)](https://github.com/fadhillaha/proyek-akhir-sistem-rekomendasi/assets/40687068/ec902868-d2b2-4e2a-a93d-56dffc1ef9e8)

Gambar 3. Analisis Univariat untuk Fitur 'rating' Anime

![download (24)](https://github.com/fadhillaha/proyek-akhir-sistem-rekomendasi/assets/40687068/bfd3912c-b1d9-4fba-9a95-c92a6ba735a0)

Gambar 4. Analisis Univariat untuk Fitur 'rating' yang Diberikan User

Dari analisis data numerik, dapat diketahui bahwa :
- Pada rating keseluruhan anime, mayoritas anime memiliki rating pada nilai 7 hingga 8. 

- Pada rating yang diberikan oleh user, mayoritas pengguna memberikan rating sebesar 8, dengan banyak juga memberikan nilai 7 dan 9. Terlihat banyak juga pengguna yang tidak memberi rating yang ditandai oleh nilai -1. Hal ini akan dapat mempengaruhi model dan perlu dilakukan penindakan pada fitur rating pengguna ini.

## Data Preparation

Preparasi data merupakan tahapan yang dilakukan untuk mempersiapkan dataset sebelum dimasukan ke proses pemodelan. Tahapan ini melakukan transformasi pada data agar memiliki bentuk yang cocok ketika dimodelkan nanti. Proses preparasi data yang dilakukan pada bagian ini yaitu :

- Preparasi Data Duplikat dan Data Hilang
- *Data Cleaning*
- Penggabungan Dataset

### Preparasi Data Duplikat dan Data Hilang

Dataset yang telah diimport kemudian dicek kelengkapan data nya. Hal yang akan dicek adalah adanya data duplikat dan data hilang, dimana apabila terdapat data duplikat atau hilang akan diproses dengan melakukan *dropping* atau menghapus sampel data. Pada dataset, ditemukan 1 data duplikat pada data rating user dan 317 data yang hilang pada data anime. Data tersebut akan di-drop dari dataset.

### *Data Cleaning*

Pada dataset terdapat beberapa hal yang spesfik yang perlu dibersihkan dengan melakukan yang *dropping* atau diubah value nya agar memudahkan proses pembuatan sistem rekomendasi

- Rating User
  
  Terdapat banyaknya pengguna yang tidak memberikan rating (bernilai -1) pada suatu anime. Hal ini dapat berpengaruh pada sistem rekomendasi nantinya bila bergantung pada rating user. Sehingga data yang memiliki rating -1 akan di-drop dari dataset.
  
  Pengguna juga akan difilter berdasarkan jumlah rating yang telah mereka berikan untuk dapat sebagai acuan rekomendasi sistem. Pengguna yang memberikan rating yang sedikit memiliki informasi yang kecil mengenai preferensi mereka. Hal ini dapat berpengaruh pada sistem. Diatur batas sebanyak minimum 50 rating agar pengguna dianggap representatif pada dataset.

- Teks
  
  Terdapat banyaknya teks error pada judul anime yang memiliki karakter jepang atau karakter lainnya. Hal ini perlu diperbaiki dengan menghapus atau mengganti teks error tersebut.

  Terdapat juga beberapa nama genre yang memiliki spasi atau tanda hubung yang dapat terpisah ketika proses pembuatan TF-IDF nantinya. Hal ini diperbaiki dengan mengganti spasi atau tanda hubung dengan garis bawah (underscore).

### Penggabungan Dataset

Penggabungan dataset dilakukan untuk mempermudah akses data dalam pembuatan sistem rekomendasi. Hasil gabungan data dari file anime.csv dan rating.csv menghasilkan dataset yang mencantumkan informasi mengenai anime serta rating pengguna pada satu dataset. Dataset memiliki 9 kolom data yang dimana tidak semua fitur data akan digunakan. Reduksi fitur akan dilakukan mengikuti kebutuhan model yang dibuat pada bagian selanjutnya.

Setelah melakukan preparasi data dan menggabungkan dataset, jumlah anime yang tercatat pada dataset berkurang menjadi 9869, dan jumlah pengguna menjadi 32967.


## Modeling

Sistem rekomendasi yang dibuat merupakan model *content-based filtering* yang memanfaatkan *cosine similiarity* serta model *collaborative filtering* yang memanfaatkan *K-Nearest Neighbor* (KNN).

### Content Based Filtering

*Content-based filtering* merupakan metode sistem rekomendasi yang merekomendasikan berdasarkan informasi objek dan profil pengguna. Dimana ketika pengguna menyukai suatu objek, sistem akan merekomendasikan objek yang memiliki karakteristik yang mirip terhadap objek yang disukai tersebut.

Model yang dibuat akan memanfaatkan *cosine similiarity*, yaitu metrik yang mengukur seberapa mirip dua objek berdasarkan arah vektor yang dimiliki objek. Dimana objek dianggap sebagai vektor pada ruang berdimensi banyak.

Kelebihan dari model ini adalah :
- Model yang memiliki bentuk sederhana
- Efisien pada ruang dimensi yang banyak, contohnya dalam kasus yang melibatkan teks
- Tidak sensitif pada skala
  
Kekurangan dari model ini adalah :
- Tidak memperhitungakan konteks karena hanya mengukur berdasarkan arah vektor
- Sensitif pada outlier dengan nilai yang besar


Persamaan *cosine similiarity* dapat dijabarkan sebagai berikut :

$\text{cosine similiarity}(a,b) = \frac{a \cdot b}{||{a}|| \cdot ||{b}|| }$

dimana a dan b adalah objek vektor yang ingin diketahui kemiripannya.

Data yang digunakan pada model ini adalah anime (id dan judulnya) serta genre dari anime tersebut. Target dari model adalah merekomendasikan anime berdasarkan kemiripan dari genre.

Untuk menguji rekomendasi dari sistem yang dibuat dilakukan dengan kode berikut :

```py
anime_recommendations('Tokyo Ghoul')
```
Genre dari anime 'Tokyo Ghoul' adalah :

Action, Drama, Horror, Mystery, Psychological, Seinen, Supernatural

Hasil rekomendasi yang diberikan sistem adalah :
| Judul | Genre |
|------|----------|
| Tokyo Ghoul √A|	Action, Drama, Horror, Mystery, Psychological, Seinen, Supernatural |
| Tokyo Ghoul: Pinto |	Action, Drama, Horror, Mystery, Psychological, Supernatural |
|	Night Head Genesis |	Drama, Horror, Mystery, Psychological, Supernatural |
|	Elfen Lied |	Action, Drama, Horror, Psychological, Romance, Seinen, Supernatural |
|	Umineko no Naku Koro ni |	Horror, Mystery, Psychological, Supernatural |
|	Jigoku Shoujo |	Horror, Mystery, Psychological, Supernatural |
|	Jigoku Shoujo Futakomori |	Horror, Mystery, Psychological, Supernatural |
|	Ajin |	Action, Horror, Mystery, Seinen, Supernatural |
|	Ajin Part 3: Shougeki	| Action, Horror, Mystery, Seinen, Supernatural |
|	Ajin Part 2: Shoutotsu |	Action, Horror, Mystery, Seinen, Supernatural |

Model dapat merekomendasikan 10 judul anime dengan genre yang mendekati judul yang diberikan. Seluruh rekomendasi memiliki beberapa atau salah satu genre dari input.

Terdapat juga dari rekomendasi adalah judul pada satu series atau sekuel yang seharusnya memiliki genre yang mirip dengan judul input. Hal ini menunjukan sistem telah dapat memberikan rekomendasi yang baik.

### Collaborative Filtering

*Collaborative Filtering* adalah metode dalam membuat sistem rekomendasi yang memanfaatkan informasi dari pengguna lain yang memiliki preferensi yang mirip. Model yang dibuat adalah *Item-based collaborative filtering* yang menunjukan kesamaan dari objek berdasarkan rating sekelompok pengguna. 

Model yang dibuat akan memanfaatkan algoritma *K-Nearest Neighbor* (KNN) yang melakukan prediksi dengan mencari tetangga dari objek berdasarkan kesamaan fitur.

Kelebihan dari model ini adalah :

- Model yang sederhana dan tidak memerlukan pelatihan
- Baik untuk data yang non-linear

Kekurangan dari model ini adalah : 

- Memerlukan kemampuan komputasi yang besar ketika ingin memprediksi
- Memiliki peforma yang buruk apabila fitur tidak berada pada skala yang sama

Metrik jarak yang digunakan pada model KNN kali ini menggunakan *cosine similiarity* untukk mengetahui kesamaan dari setiap objek.

Untuk menguji sistem rekomendasi yang dibuat dilakukan dengan kode sebagai berikut :

```py
get_anime_recommendations('Tokyo Ghoul')
```

Hasil rekomendasi yang didapatkan adalah sebagai berikut :

| Judul | Rating | Genre | Similiarity |
|-------|--------|---------|--------|
|	Tokyo Ghoul √A |	[7.52] |	['Action, Drama, Horror, Mystery, Psychological, Seinen, Supernatural'] |	0.806 |
|	Shingeki no Kyojin |	[8.54] |	['Action, Drama, Fantasy, Shounen, Super Power']|	0.683|
|	Akame ga Kill! | [7.84] |	['Action, Adventure, Fantasy'] |	0.657 |
|	No Game No Life |	[8.47] |	['Adventure, Comedy, Ecchi, Fantasy, Game, Supernatural']|0.656 |
|	Noragami |	[8.17] |	['Action, Adventure, Shounen, Supernatural'] |	0.647 |
|	Sword Art Online |	[7.83] |	['Action, Adventure, Fantasy, Game, Romance']	| 0.639 |
|	Kiseijuu: Sei no Kakuritsu| [8.59] |	['Action, Drama, Horror, Psychological, Sci-Fi, Seinen'] |	0.637 |
|	Sword Art Online II	| [7.35] |	['Action, Adventure, Fantasy, Game, Romance'] |	0.610 |
|	Mirai Nikki (TV) |	[8.07] |	['Action, Mystery, Psychological, Shounen, Supernatural, Thriller'] |	0.602 |
|	One Punch Man |	[8.82] |	['Action, Comedy, Parody, Sci-Fi, Seinen, Super Power, Supernatural'] |	0.593 |

Hasil yang didapatkan menunjukan rekomendasi yang diberikan oleh sistem. Rekomendasi yang diberikan menunjukan anime yang memiliki rating dan genre yang mendekati dengan judul input. Nilai kesamaan dari rekomendasi menunjukan hasil paling besar 0.8 atau 80% yaitu judul yang merupakan satu series / sekuel dengan judul input. Sedangkan rekomendasi lainnya memiliki rentang nilai kesamaan pada nilai 0.68 - 0.59. Hasil yang didapatkan model ini dapat diakibatkan menggunakan metrik *cosine similiarity* yang menunjukan kesamaan berdasarkan dari rating pengguna yang beragam. Performa model dapat diuji dengan menggunakan metrik evaluasi.

## Evaluation

Evaluasi dilakukan untuk mengukur peforma model. Metrik yang akan digunakan adalah skor presisi dan *hit rate at k*. 

### Presisi

Presisi adalah salah satu metrik evaluasi yang digunakan dalam mengevaluasi model seperti sistem rekomendasi. Presisi mengukur proporsi antara nilai tebakan yang benar dari semua hasil yang diprediksi. Pada sistem rekomendasi, presisi diukur dengan mengetahui proporsi antara rekomendasi yang relevan dengan seluruh rekomendasi yang diberikan.

Persamaan presisi dapat didefinisikan sebagai :

$\text{Presisi} = \frac{\text{Rekomendasi Relevan}}{\text{Seluruh Rekomendasi}}$

Untuk evaluasi ini, model merekomendasikan berdasarkan genre yang dimana setiap judul pada dataset memiliki jumlah genre yang berbeda. Sehingga diperlukan asumsi bahwa rekomendasi relevan adalah jika rekomendasi memiliki setengah atau lebih genre yang dimiliki oleh judul input.

Dari hasil rekomendasi yang diberikan, ditunjukan bahwa seluruh rekomendasi yang diberikan memiliki 4 atau lebih dari 7 genre yang dimiliki oleh judul input. Hal ini menandakan bahwa nilai presisi dari sistem adalah 10/10 atau 100%. 

### Hit Rate at K

*Hit rate at K* adalah metrik yang digunakan untuk mengevaluasi sistem rekomendasi dalam memprediksi objek yang benar disukai oleh pengguna dari sebanyak k objek yang direkomendasikan.

Metrik ini berfokus pada apakah rekomendasi memberikan objek yang relevan pada preferensi pengguna. Hal ini didapatkan dengan membandingkan daftar objek yang benar-benar disukai pengguna dengan rekomendasi yang diberikan sistem. Apabila terdapat objek yang disukai pengguna pada rekomendasi, maka *hit* dihitung sebagai 1.  Hal ini dilakukan untuk pengguna lainnya.

*Hit rate at K* dihitung sebagai berikut :

$\text{Hit rate at K} = \frac{\text{Jumlah Hit}}{\text{jumlah user}}$

Untuk evaluasi ini, daftar judul anime yang pengguna sukai didapatkan dari 10 anime yang memiliki rating terbesar. Daftar ini kemudian akan dibandingkan pada rekomendasi oleh sistem. Apabila rekomendasi terdapat pada daftar pengguna, terhitung *hit* sebagai 1. Evaluasi dilakukan pada 10 ribu pengguna akibat keterbatasan memori.

Dari fungsi model *collaborative filtering* yang dibuat, telah dilakukan perhitungan *hit rate at* 10. Dimana rekomendasi yang diuji adalah 10 rekomendasi yang diberikan sistem. Hasil yang didapatkan adalah 95.03% *hit rate*. Hal ini menunjukan bahwa terdapat 95.03% kemungkinan bahwa terdapat setidaknya satu item yang disukai oleh pengguna dari rekomendasi sistem.


## Referensi

[1] Md Rajibul Hasan, Ashish Kumar Jha, Yi Liu,. (2018). Excessive use of online video streaming services: Impact of recommender system use, psychological factors, and motives. 
Computers in Human Behavior. 80. 220-228.

[2] Kelts, Roland. (2020). Anime Businesses Move Online to Survive the Pandemic. Japan SPOTLIGHT. 35-38.

[3] Jena, Abhipsa,. Jaiswal, Arunima,. Lal,Dakshita,. Rao, Soumya,.  Ayubi, Afshan,. Sachdeva, Nitin,. (2022). Recommendation System For Anime Using Machine Learning Algorithms. Proceedings of the International Conference on Innovative Computing & Communication (ICICC).

[4] V. Prakash, S. Raghav, S. Sood, M. Pandey and M. Arora. (2022). Deep Anime Recommendation System: Recommending Anime Using Collaborative and Content-based Filtering. 4th International Conference on Advances in Computing, Communication Control and Networking (ICAC3N). 718-723.

[5] Soni, B., Thakuria, D., Nath, N. et al. (2023). RikoNet: A Novel Anime Recommendation Engine. Multimed Tools Appl 82, 32329–32348.

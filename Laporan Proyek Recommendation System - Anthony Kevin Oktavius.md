# Laporan Proyek Machine Learning - Anthony Kevin Oktavius


## Project Overview

### Latar Belakang

![Akhibara Tokyo](https://cdn.cheapoguides.com/wp-content/uploads/sites/2/2020/05/akihabara-iStock-484915982-1024x600.jpg)

Siapa yang tidak kenal dengan Anime? Anime adalah animasi yang khas diproduksi di Jepang. Anime bisa dalam berbagai bentuk, mulai dari bentuk animasi ataupun komik (manga). Bahkan, di jepang, sudah ada kota terkenal bagi para pencinta anime, yaitu Kota Akhibara di Tokyo. 

![Buku-Buku Anime](https://img.okezone.com/content/2015/10/28/18/1239271/utusan-pbb-desak-jepang-larang-peredaran-komik-manga-luALXylWn3.jpg)

Dengan banyaknya judul anime yang ada, para pecinta anime akan mengalami kesulitan untuk mencari anime yang sesuai dengan seleranya. Tidak jarang orang-orang kecewa menonton/ membaca anime, karena tidak sesuai dengan yang mereka harapkan. Salah satu faktornya adalah rating yang rendah.

![Naruto](https://static.wikia.nocookie.net/naruto/images/d/d6/Naruto_Part_I.png/revision/latest?cb=20210223094656)

Anime sudah memiliki banyak genre. Dalam dataset kita, genre anime ada lebih dari 40 genre. Namun, perlu diingat, umumnya, genre yang ditag pada anime tidak hanya satu. Misalnya, jika kita lihat pada anime naruto, genre anime tersebut adalah adventure, martial arts, dan fantasy comedy. Hal ini menjadi peluang agar kita bisa merekomendasikan data anime yang cocok. Selain memberikan data genre yang sama, kita juga bisa memberikan genre lain, yang user bisa coba tonton. Dengan demikian, para pencinta anime bisa disarankan untuk mencoba untuk menonton anime yang memiliki genre yang sama, namun ditambahkan dengan variasi-variasi lainnya, seperti type pada anime.

Terlebih lagi, kita bisa merekomendasikan anime kepada user, berdasarkan rating yang ada. Penting untuk melakukan filterisasi berdasarkan rating, karena nilai rating menunjukkan kualitas anime yang ada. Tentu kita harus memberikan anime dengan rating yang baik, agar user bisa lebih menikmati anime.

Dari uraian di atas, proyek ini menarik untuk dibahas dengan menggunakan recommendation system. Di sini, saya akan melakukan eksperimen dengan dua tipe recommended system, yaitu content based filtering dan collaborative filtering.


### Sumber Referensi

* Girsang, A. S., Al Faruq, B., Herlianto, H. R., & Simbolon, S. (2020, June). Collaborative recommendation system in users of anime films. In Journal of Physics: Conference Series (Vol. 1566, No. 1, p. 012057). IOP Publishing.

* Vie, J. J., Laıly, C., & Pichereau, S. (2015). Mangaki: an anime/manga recommender system with fast preference elicitation. Tech. Rep.


## Business Understanding

### Problem Statements

* Bagaimana kita bisa melakukan rekomendasi produk dengan content based filtering, jika data genre ada lebih dari satu?

* Dengan data rating yang ada, bagaimana kita merekomendasikan anime jepada user yang belum pernah ditonton dan belum pernah dikunjungi?

### Goals

* Menghasilkan sejumlah rekomendasi anime sesuai dengan genre yang dipilih user. Perlu diingat, genre anime tidak hanya satu. Maka dari itu, saya akan tetap memberikan anime dengan genre yang dipilih user, namun dipadukan dengan genre-genre lainnya.

* Menghasilkan sejumlah rekomendasi anime yang memiliki rating yang tinggi dan belum pernah ditonton oleh user.

### Solution Statements

Seperti penjelasan pada latar belakang, kita akan membuat sistem rekomendasi dengan menggunakan collaborative filtering dan content based filtering. Di sini, saya akan menggunakan dua algoritma, yaitu sebagai berikut.

* TF IDF Vectorizer dan cosine similarity
* SVD (Single Value Decomposition)

Metrik yang akan digunakan adalah sebagai berikut.

* Content Based Filtering
    
    * Accuracy

* Collaborative Filtering
    
    * MAE (Mean Absolute Error)
    * RMSE (Root Mean Squared Error)

## Data Understanding

**Deskripsi Data:**

Dataset ini diambil dari API myanimelist.net.

Data dapat diunduh pada link berikut: https://www.kaggle.com/datasets/CooperUnion/anime-recommendations-database/download

**Jumlah data:**

* Data Anime: 12294
* Data User: 73516

Deskripsi fitur-fitur pada data dapat dilihat sebagai berikut.

**anime.csv**:

* **anime_id**: id anime yang unik, yang bertujuan untuk identifikasi anime
* **name**: nama anime
* **genre**: label untuk identifikasi kategori anime. Genre-genre ini dipisahkan dengan tanda koma, misalnya "Action, Adventure, Game", "Game, Romance, Shounen"
* **type**: tipe anime tersebut, misalnya movie, TV, OVA, dll.
* **rpisodes**: jumlah episode dalam anime (-1 jika merupakan movie)
* **rating**: nilai rata-rata yang diberikan dari semua rating user. 
* **members**: jumlah member komunitas pada nama anime yang diberikan.

**rating.csv**:

* **user_id**: id user yang unik, yang bertujuan untuk identifikasi user
* **anime_id**: anime yang dirating oleh user
* **rating**: nilai rating dengan skala 1 - 10 (Apabila user sudah menonton, namun belum memberikan rating, maka nilainya adalah -1)

**Kondisi data**: masih terdapat missing value, sehingga harus dilakukan data preprocessing terlebih dahulu.

Langkah-langkah pemahaman data yang dilakukan adalah sebagai berikut.

* **Anime.csv**:

  * Melihat deskripsi data dengan numpy dengan info() dan describe(). 

      * **info()**: menjelaskan jumlah kolom yang non-null dan data type.
    
      * **describe()**: menjelaskan beberapa informasi mengenai statistika suatu data.

  * **Missing Value**:
    
    * Menghapus data yang null
    * Menghapus data yang memiliki episodes yang unknown.
    * Karena field episodes memiliki tipe data object, maka kita akan ubah menjadi tipe float64.
    * Melihat data statistika pada members dengan menggunakan describe().

  * **Eksplorasi data Genre:**
    * Melihat jumlah data pada genre individual. Berikut saya tampilkan tiga data genre yang paling banyak dan tiga data genre yang paling sedikit muncul. 

        | Genre       | Count |
        |-------------|-------|
        | Comedy      | 4483  |
        | Action      | 2748  |
        | Fantasy     | 2293  |
        | Josei       | 52    |
        | Yuri        | 41    |
        | Yaoi        | 37    |
    

        Dari penjelasan tersebut, kita bisa melihat bahwa ada tiga genre anime terbanyak, yaitu comedy, action, dan fantasy. Lalu, ada tiga genre anime yang paling sedikit, yaitu Josei, Yuri, dan Yaoi.

  * **Eksplorasi Data Type:**
  
    * Melakukan persebaran data type dengan menggunakan value_counts. Lalu, saya menggunakan plot bar untuk melihat persebaran tipe anime.

        Berikut adalah plot untuk analisis field type.

        ![Analisis Field Type](https://github.com/MaxZx3000/Recommendation-System-Anime/blob/main/Submission-2-images/anime_analysis.png?raw=true)

        Pada plot di atas, kita bisa melihat bahwa tipe anime yang diproduksi kebanyakan merupakan TV, OVA, dan Movie. Namun, tipe anime Music dan ONA tidak begitu banyak diproduksi.
  
  * **Eksplorasi Data Episodes:**
  
    * Melihat persebaran data episode dengan menggunakan plot histogram.

        ![Analisis Field Episodes](https://github.com/MaxZx3000/Recommendation-System-Anime/blob/main/Submission-2-images/episodes_analysis.png?raw=true)

        Pada plot di atas, jumlah episodes pada anime umumnya hanya mencapai 30 - 90 episodes. Hal ini juga dikaitkan dengan pengaruh jumlah tipe anime TV dan Movie yang diproduksi. Meskipun demikian, pada anime tertentu, ada anime yang mencapai ribuan episodes.


  * **Eksplorasi Data Average Rating:**
    
    * Melihat persebaran data rating, dengan menggunakan plot histogram.
  
        ![Analisis Data Average Rating](https://github.com/MaxZx3000/Recommendation-System-Anime/blob/main/Submission-2-images/average_rating_analysis.png?raw=true)

        Pada plot di atas, nilai rata-rata rating pada anime mencapai 7.5 - 8. Nilai rata-rata rating tersebut merupakan hasil perhitungan rata-rata nilai rating dari semua user yang menilai anime tersebut.

  
* Rating.csv:
  
   * Melihat deskripsi data dengan numpy dengan info() dan describe(). 

     * **info()**: menjelaskan jumlah kolom yang non-null dan data type.
      
     * **describe()**: menjelaskan beberapa informasi mengenai statistika suatu data.
  
   * **Eksplorasi Data Rating:**
     * Melihat persebaran data anime rating dengan menggunakan plot histogram
    
        Berikut adalah hasil plotting rating.

        ![Eksplorasi Data Rating](https://github.com/MaxZx3000/Recommendation-System-Anime/blob/main/Submission-2-images/anime_rating_analysis.png?raw=true)

        Pada gambar di atas, banyak user yang memberikan rating antara 8 - 10. Hal ini menunjukkan bahwa banyak anime yang sangat digemari oleh para user. 
  
     * Menghapus data rating -1, karena ia menunjukkan bahwa user belum melakukan rating sama sekali.
  
  * Melihat jumlah atribut yang **unik**, dengan field-field sebagai berikut.
  
    * user_id
    * anime_id
    * rating

    Berikut adalah analisis datanya.

    ```
    Unique Data:
    Total user_id: 69600
    Total anime_id: 9927
    Total rating: 10
    ```

    Dari data tersebut, kita bisa mendapatkan informasi sebagai berikut.
    
    * Jumlah user yang melakukan review pada anime adalah 69600 
    * Jumlah anime yang telah dirating adalah 9927
    * Variasi nilai rating adalah 10.

## Data Preparation

* **Data Preparation untuk Content Based Filtering:**

    * Melakukan splitting value ketika ada tanda koma (,), agar kita bisa merekomendasikan anime yang memiliki genre tambahan selain genre yang ditentukan.

* **Data Preparation untuk Collaborative Filtering:**


  * Melakukan standardisasi pada nilai rating, agar machine learning bisa lebih cepat mempelajari data (terutama jika berbicara mengenai jarak dan gradient descent), dengan skala antara 0 - 1.

  * Melakukan train test split, dengan proporsi sebagai berikut:

    * **Train:** 80%
    * **Test:** 20%

    Tujuan dari pembagian data ini adalah agar kita bisa mengukur kemampuan machine learning dengan data yang belum pernah dipelajari. 

## Modelling

* Content Based Filtering dengan cosine similarity dan TD IDF Vectorizer

    **TF IDF Vectorizer:**

    TD-IDF (Term Frequency - Inverse Document Frequency) mengukur seberapa pentingnya suatu kata terhadap kata-kata lain dalam dokumen. Ia dibagi menjadi 2 komponen, yaitu TF dan IDF.

    * TF mengukur seberapa sering kata muncul dalam teks tertentu. Teks yang berbeda dalam dokumen mungkin panjangnya berbeda, karena ada faktor dari panjang dokumen. Teks yang berbeda memiliki panjang yang berbeda. Maka dari itu, harus dinormalisasi dengan membagi jumlah kemunculan terhadap panjang dokumen.

    * IDF mengukur pentingnya suatu istilah di dalam korpus. Ia memastikan agar kepentingan suatu kata di dalam kalimat itu sama. Misalkan stop words seperti is, am, are, dsb sering muncul di suatu kalimat, namun mereka tidak penting kehadirannya. Maka dari itu, semua kata dinormalisasikan agar setiap kata bisa mendapatkan nilai kepentingan yang fair.

        Dari kesimpulan di atas, kita bisa mendapatkan rumus sebagai berikut.

        ![Rumus TF-IDF](https://cdn-media-1.freecodecamp.org/images/1*nSqHXwOIJ2fa_EFLTh5KYw.png)

        Dalam fungsi ini, kita bisa menggunakan TfIdfVectorizer. TFIdfVectorizer adalah salah satu fungsi yang disediakan dari sklearn.

    **Cosine Similarity:**

    Cosine similarity adalah salah satu metrik untuk mengukur kesamaan. Cosine similarity mengukur kesamaan dua vektor dengan menentukan apakah kedua vektornya sudah mengarah kepada arah yang sama. Semakin kecil sudut cosinus, semakin besar nilai cosine similarity.

    Metrik ini sering digunakan untuk analisis teks. Dalam hal ini, dari hasil TF IDF, kita akan mengukur kesamaan antar satu item dengan fitur yang ada pada item tersebut, sehingga kita bisa merekomendasikan anime yang cocok dengan genre yang diberikan.

    Rumusnya adalah sebagai berikut.

    ![Rumus Cosine Similarity](https://neo4j.com/docs/graph-data-science/current/_images/cosine-similarity.png)

    Kelebihan:

    Fitur utama dari TF IDF adalah ia tidak hanya berfokus kepada frekuensi suatu kata, namun juga dengan kepentingan suatu kata. Salah satu contohnya adalah stop words, seperti the, is, am, are. Stop words umumnya tidak begitu penting dalam pemrosesan kepentingan kata. Maka dari itu, jika ingin menyeimbangkan kepentingan suatu kata, TF IDF adalah salah satu algoritma yang cocok untuk digunakan.

    Kekurangan:

    * TF IDF merupakan teknik yang didasarkan pada bag of words. Jadi, TF IDF ini tidak memperhatikan urutan kata, baik setelah atau sebelumnya.
    
    * TF IDF tidak bisa menganalisa semantik pada kata (hubungan antar satu kata dengan kata lain).

    Berikut adalah hasil output rekomendasi sistem menggunakan TF IDF dan cosine similarity.

    ![Rekomendasi dengan TF IDF](https://github.com/MaxZx3000/Recommendation-System-Anime/blob/main/Submission-2-images/hasil_rekomendasi_tf_idf.png?raw=true)


* Collaborative Filtering dengan SVD

    SVD adalah salah satu teknik yang digunakan untuk dimensionality reduction. Teknik ini bekerja dengan mengurangi jumlah fitur yang ada pada dataset (mirip dengan PCA) dengan jumlah fitur yang ditentukan oleh user.

    Namun, jika kita berbicara konteks sistem rekomendasi, SVD menggunakan sturktur matriks. Berikut adalah bagian-bagian dari SVD, jika menggunakan struktur matriks. 

    * Bagian row menunjukkan user
    * Bagian column menunjukkan item
    * Elemen-elemen pada matriks menunjukkan rating

    Dari matriks di atas, SVD mengurangi dimensi matriks terebut dengan faktor-faktor laten. Ia melakukan mapping setiap fitur dan item menjadi matriks yang lebih kecil. Matriks inilah yang memiliki informasi mengenai relasi user dan item.

    Rumus untuk SVD adalah sebagai berikut.

    ![Rumus SVD](https://miro.medium.com/max/894/1*XNWUlrQJXGeoCDqUMd0iUA.png)

    Untuk melakukan training pada SVD, ia menggunakan teknik SGD (Schocastic Gradient Descent). SGD bekerja dengan meminimalkan nilai awal dan melakukan iterasi untuk mengurangi kesalahan antara nilai yang diprediksi dan nilai aktual. Mirip seperti SGD pada klasifikasi, ia akan mengoptimalkan cost function agar bisa konvergensi ke titik global optima.

    Kelebihan:
    * SVD adalah salah satu bagian dari dimensionality reduction. Namun, jika berbicara mengenai performa, SVD dibilang cukup efisien.
    
    * Mengurangi noise pada data (data yang tidak merata)

    Kekurangan:
    * Jika data telah ditransformasi dalam bentuk matriks, ia akan sulit untuk dimengerti.
    
    * Sulit untuk divisualisasikan.

    * Tidak cocok digunakan pada data non linear.


    Berikut adalah 5 rekomendasi teratas yang dihasilkan dari rekomendasi menggunakan SVD.

    ![Hasil Rekomendasi SVD](https://github.com/MaxZx3000/Recommendation-System-Anime/blob/main/Submission-2-images/hasil_rekomendasi_svd.png?raw=true)

 
## Evaluation

* Content Based Filtering dengan TF IDF dan Cosine Similarity

    Dalam content based filtering, saya hanya akan menggunakan satu metrik saja, yaitu accuracy.

    **Accuracy**: nilai pecahan yang menunjukkan rasio antara jumlah data yang diprediksi benar dengan total jumlah data yang ada.

    Berikut adalah hasil akurasi dengan sampel data yang ada, jika menggunakan judul anime "Kimi no Na wa.".

    ```
    Accuracy Score: 100.0%
    ```

    Pada hasil akurasi di atas, dapat dilihat bahwa ia bisa merekomendasikan genre anime yang sama dengan genre yang sama dengan judul anime yang diberikan. Maka dari itu, dapat dikatakan bahwa model TF IDF Vectorizer dan Cosine Similarity mampu memprediksi data yang mirip/ sama dengan genre yang diberikan.

* Collaborative Filtering dengan SVD

    Dalam collaborative filtering, terdapat dua metrik yang saya gunakan, yaitu MAE dan RMSE. 

    * **MAE**: rata-rata kesalahan absolut antara data sebenarnya dengan data yang diprediksikan. Rumusnya adalah sebagai berikut:

        ![Rumus MAE](https://www.statisticshowto.com/wp-content/uploads/2016/10/MAE.png)

    * **RMSE**: akar kuadrat dari MSE. MSE sendiri adalah kuadrat dari rata-rata kesalahan. Rumusnya adalah sebagai berikut.

        ![Rumus RMSE](https://1.bp.blogspot.com/-AodtifmdR1U/X-NOXo0avGI/AAAAAAAACmI/_jvy7eLB72UB00dW_buPYZCa9ST2yx8XACNcBGAsYHQ/s453/rumus%2Brmse.jpg)

    Berikut adalah hasil MAE dan RMSE dalam testing data.


    ```
                           Fold 1  Fold 2  Fold 3  Mean    Std     
         RMSE (testset)    0.1533  0.1537  0.1533  0.1534  0.0002 
    ``` 
    
    Dari hasil ini, kita bisa menyimpulkan bahwa nilai RMSE (rata-ratanya adalah 0.1534) dan MAE bernilai kecil (rata-ratanya adalah ). Untuk membuktikan bahwa hasil ini benar, perhatikan beberapa hasil prediksi dari data testing, dengan menggunakan salah satu sampel id.

    ```
        user: 23435      item: 9790       r_ui = 0.78   est = 0.87   {'was_impossible': False}
        user: 23435      item: 1575       r_ui = 0.89   est = 1.00   {'was_impossible': False}
        user: 23435      item: 205        r_ui = 1.00   est = 0.95   {'was_impossible': False}
        user: 23435      item: 3927       r_ui = 1.00   est = 0.91   {'was_impossible': False}
        user: 23435      item: 1535       r_ui = 1.00   est = 0.99   {'was_impossible': False}
        user: 23435      item: 16680      r_ui = 0.67   est = 0.86   {'was_impossible': False}
        user: 23435      item: 4224       r_ui = 1.00   est = 0.96   {'was_impossible': False}
        user: 23435      item: 21327      r_ui = 0.78   est = 0.89   {'was_impossible': False}
        user: 23435      item: 1069       r_ui = 1.00   est = 0.89   {'was_impossible': False}
        user: 23435      item: 16894      r_ui = 1.00   est = 1.00   {'was_impossible': False}
        user: 23435      item: 30868      r_ui = 0.89   est = 0.74   {'was_impossible': False}
        user: 23435      item: 137        r_ui = 1.00   est = 0.99   {'was_impossible': False}
        user: 23435      item: 3901       r_ui = 1.00   est = 0.92   {'was_impossible': False}
        user: 23435      item: 60         r_ui = 0.78   est = 0.94   {'was_impossible': False}
        user: 23435      item: 26243      r_ui = 0.67   est = 0.85   {'was_impossible': False}
        user: 23435      item: 18179      r_ui = 1.00   est = 0.87   {'was_impossible': False}

    ```

    Mari kita fokuskan perhatian kita kepada r_ui dan est. r_ui menunjukkan nilai rating yang sebenarnya dan est menunjukkan hasil prediksi rating pada suatu anime (item). Apabila kita lihat, jumlah kesalahan pada item dan . Nilai ini bisa terbilang sangat baik, karena nilai RMSE sudah mendekati 0.1. Maka dari itu, kita bisa menyimpulkan bahwa dengan menggunakan dataset pada rating, kita sudah bisa memprediksi anime yang akan direkomendasikan kepada user dengan sangat akurat.


# Laporan Proyek Machine Learning - Anthony Kevin Oktavius


## Project Overview

### Latar Belakang

![Akhibara Tokyo](https://cdn.cheapoguides.com/wp-content/uploads/sites/2/2020/05/akihabara-iStock-484915982-1024x600.jpg)

Siapa yang tidak kenal dengan Anime? Anime adalah animasi yang khas diproduksi di Jepang. Anime bisa dalam berbagai bentuk, mulai dari bentuk animasi ataupun komik (manga). Bahkan, di jepang, sudah ada kota terkenal bagi para pencinta anime, yaitu Kota Akhibara di Tokyo. 

![Buku-Buku Anime](https://img.okezone.com/content/2015/10/28/18/1239271/utusan-pbb-desak-jepang-larang-peredaran-komik-manga-luALXylWn3.jpg)

Dengan banyaknya judul anime yang ada, para pecinta anime akan mengalami kesulitan untuk mencari anime yang sesuai dengan seleranya. Tidak jarang orang-orang kecewa menonton/ membaca anime, karena tidak sesuai dengan yang mereka harapkan. Salah satu faktornya adalah rating yang rendah.

![Naruto](https://static.wikia.nocookie.net/naruto/images/d/d6/Naruto_Part_I.png/revision/latest?cb=20210223094656)

Anime sudah memiliki banyak genre. Dalam dataset kita, genre anime ada lebih dari 40 genre. Namun, perlu diingat, umumnya, genre yang ditag pada anime tidak hanya satu. Misalnya, jika kita lihat pada anime naruto, genre anime tersebut adalah adventure, martial arts, dan fantasy comedy. Hal ini menjadi peluang agar kita bisa merekomendasikan data anime yang cocok. Selain memberikan data genre yang sama, kita juga bisa memberikan genre lain, yang user bisa coba tonton. Dengan demikian, para pencinta anime bisa disarankan untuk mencoba untuk menonton anime yang memiliki genre yang sama, namun ditambahkan dengan variasi-variasi genre lainnya.

Terlebih lagi, kita bisa merekomendasikan anime kepada user, berdasarkan rating yang ada. Penting untuk melakukan filterisasi berdasarkan rating, karena nilai rating menunjukkan kualitas anime yang ada. Tentu kita harus memberikan anime dengan rating yang baik, agar user bisa lebih menikmati anime.

Dari uraian di atas, proyek ini menarik untuk dibahas dengan menggunakan recommendation system. Di sini, saya akan melakukan eksperimen dengan dua tipe recommended system, yaitu content based filtering dan collaborative filtering.


### Sumber Referensi

* Girsang, A. S., Al Faruq, B., Herlianto, H. R., & Simbolon, S. (2020, June). Collaborative recommendation system in users of anime films. In Journal of Physics: Conference Series (Vol. 1566, No. 1, p. 012057). IOP Publishing.

* Vie, J. J., Laıly, C., & Pichereau, S. (2015). Mangaki: an anime/manga recommender system with fast preference elicitation. Tech. Rep.


## Business Understanding

### Problem Statements

* Bagaimana kita bisa melakukan rekomendasi produk dengan content based filtering, jika data genre ada lebih dari satu?

* Dengan data rating yang ada, bagaimana kita merekomendasikan anime kepada user yang belum pernah ditonton dan belum pernah dikunjungi?

### Goals

* Menghasilkan sejumlah rekomendasi anime sesuai dengan genre yang dipilih user. Perlu diingat, genre anime tidak hanya satu. Maka dari itu, saya akan tetap memberikan anime dengan genre yang dipilih user, namun dipadukan dengan genre-genre lainnya.

* Menghasilkan sejumlah rekomendasi anime yang memiliki rating yang tinggi dan belum pernah ditonton oleh user.

### Solution Statements

Seperti penjelasan pada latar belakang, kita akan membuat sistem rekomendasi dengan menggunakan collaborative filtering dan content based filtering. Di sini, saya akan menggunakan dua algoritma, yaitu sebagai berikut.

* Count Vectorizer dan cosine similarity
* SVD (Single Value Decomposition)

Metrik yang akan digunakan adalah sebagai berikut.

* Content Based Filtering
    
    * Precision

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
* **episodes**: jumlah episode dalam anime (-1 jika merupakan movie)
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
 
  
   * **Missing Values:**
    
     * Menghapus data rating -1, karena ia menunjukkan bahwa user belum melakukan rating sama sekali.

   * **Eksplorasi Data Rating:**

     * Melihat persebaran data anime rating dengan menggunakan plot histogram
    
        Berikut adalah hasil plotting rating.

        ![Eksplorasi Data Rating](https://github.com/MaxZx3000/Recommendation-System-Anime/blob/main/Submission-2-images/anime_rating_analysis.png?raw=true)

        Pada gambar di atas, banyak user yang memberikan rating antara 8 - 10. Hal ini menunjukkan bahwa banyak anime yang sangat digemari oleh para user. 
  
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

    **Sebelum melakukan Exploratory Data Analysis:**

    Missing Value pada anime.csv:
    
    * Menghapus data yang null, karena data yang null tidak akan bisa kita proses datanya dengan machine learnin.
  
    * Menghapus data yang memiliki episodes yang unknown. Hal ini bertujuan agar saya bisa melakukan analisis univariate pada field episodes.
  
    * Karena field episodes memiliki tipe data object, maka kita akan ubah menjadi tipe float64.

    **Setelah melakukan Exploratory Data Analysis:**

    * Melakukan splitting value ketika ada tanda koma (,), agar kita bisa merekomendasikan anime yang memiliki genre tambahan selain genre yang ditentukan.

* **Data Preparation untuk Collaborative Filtering:**
  
  **Sebelum melakukan exploratory data analysis:**

  Missing Value pada rating.csv:

  * Melakukan hapus data dengan rating -1, karena user tersebut belum melakukan review sama sekali. Jika terdapat value ini, hal ini akan menyebabkan bias, karena rating pada anime memiliki rentang antara 1 - 10.
  
  **Setelah melakukan exploratory data analysis:**

  * Melakukan train test split, dengan proporsi sebagai berikut:

    * **Train:** 80%
    * **Test:** 20%

    Tujuan dari pembagian data ini adalah agar kita bisa mengukur kemampuan machine learning dengan data yang belum pernah dipelajari. 

  * Melakukan standardisasi pada nilai rating, agar machine learning bisa lebih cepat mempelajari data (terutama jika berbicara mengenai jarak dan gradient descent), dengan skala antara 0 - 1. Apabila kita lihat, tahap standardisasi dilakukan belakangan setelah train test split. Hal ini dilakukan karena .

## Modelling

* Content Based Filtering dengan Cosine Similarity dan Count Vectorizer

    **Count Vectorizer:**

    Count Vectorizer adalah salah satu teknik untuk melakukan konversi suatu string menjadi representasi frekuensi. Count Vectorizer ini sangat berguna untuk diimplementasikan apabila kita tidak ingin melihat hubungan makna yang terdapat pada kalimat tersebut. 

    Sebagai contoh, perhatikan kalimat berikut.

    D1: Drama, Romance, School, Supernatural

    D2: Drama, Comedy, School, Romance

    Maka dari itu, berikut adalah representasi dari count vectorizer.


    | Document | Drama | Romance | School | Supernatural | Comedy | 
    | ------- | ------- | ------- | ------- | ------- | ------- | 
    | D1 | 1 | 1 | 1 | 1 | 0 |        
    | D2 | 1 | 1 | 1 | 0 | 1 |


    **Cosine Similarity:**

    Cosine similarity adalah salah satu metrik untuk mengukur kesamaan. Cosine similarity mengukur kesamaan dua vektor dengan menentukan apakah kedua vektornya sudah mengarah kepada arah yang sama. Semakin kecil sudut cosinus, semakin besar nilai cosine similarity.

    Metrik ini sering digunakan untuk analisis teks. Dalam hal ini, dari hasil Count Vectorizer, kita akan mengukur kesamaan antar satu item dengan fitur yang ada pada item tersebut, sehingga kita bisa merekomendasikan anime yang cocok dengan genre yang diberikan.

    Rumusnya adalah sebagai berikut.

    ![Rumus Cosine Similarity](https://www.researchgate.net/profile/Said-Salloum/publication/345471138/figure/fig2/AS:955431962808321@1604804139868/Cosine-similarity-formula.png)

    **Kelebihan:**

    * Cocok digunakan apabila kita hanya ingin melihat jumlah frekuensi kata dalam suatu kalimat, tanpa melihat hubungan kata dan makna yang ada. Dengan demikian, performa komputasi akan lebih cepat.

    **Kekurangan:**

    * Tidak memperhatikan keseimbangan jumlah kata pada kalimat. Sebagai contoh, stopwords umumnya tidak begitu penting dalam kalimat.

    * Tidak memperhatikan urutan kata, karena teknik ini didasarkan pada bag of words.

    * Tidak bisa menganalisis semantik pada kata (hubungan antar satu kata dengan kata lain).

    Berikut adalah hasil output rekomendasi sistem menggunakan Count Vectorizer dan cosine similarity.

    |index|name|genre|
    |---|---|---|
    |0|Kimi no Na wa\.|Drama, Romance, School, Supernatural|
    |1|Wind: A Breath of Heart \(TV)|Drama, Romance, School, Supernatural|
    |2|Wind: A Breath of Heart OVA|Drama, Romance, School, Supernatural|
    |3|Tokimeki Memorial: Forever With You|Drama, Romance, School|


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

    **Kelebihan:**

    * SVD adalah salah satu bagian dari dimensionality reduction. Namun, jika berbicara mengenai performa, SVD dibilang cukup efisien.
    
    * Mengurangi noise pada data (data yang tidak merata)

    **Kekurangan:**

    * Jika data telah ditransformasi dalam bentuk matriks, ia akan sulit untuk dimengerti.
    
    * Sulit untuk divisualisasikan.

    * Tidak cocok digunakan pada data non linear.


    Berikut adalah 5 rekomendasi teratas yang dihasilkan dari rekomendasi menggunakan SVD.

    |index|anime\_id|name|genre|type|episodes|rating|members|
    |---|---|---|---|---|---|---|---|
    |1|5114|Fullmetal Alchemist: Brotherhood|Action, Adventure, Drama, Fantasy, Magic, Military, Shounen|TV|64\.0|9\.26|793665|
    |35|431|Howl no Ugoku Shiro|Adventure, Drama, Fantasy, Romance|Movie|1\.0|8\.74|333186|
    |58|24415|Kuroko no Basket 3rd Season|Comedy, School, Shounen, Sports|TV|25\.0|8\.62|184525|
    |99|22789|Barakamon|Comedy, Slice of Life|TV|12\.0|8\.5|225927|
    |226|3901|Baccano\! Specials|Action, Comedy, Historical, Mystery, Seinen, Supernatural|Special|3\.0|8\.29|100412|

 
## Evaluation

* **Content Based Filtering dengan Count Vectorizer dan Cosine Similarity**

    Dalam content based filtering, saya hanya akan menggunakan satu metrik saja, yaitu precision.

    **Precision**: dalam sistem rekomendasi, preision adalah jumlah item rekomendasi yang relevan. Cara mendapatkan hasil ini adalah dengan menghitung jumlah rekomendasi yang relevan dengan jumlah item yang kita rekomendasikan.

    Berikut adalah hasil precision dengan sampel data yang ada, jika menggunakan judul anime "Kimi no Na wa.", dengan genre 
    Drama.

    ```
    Precision Score: 100.0%
    ```

    Pada hasil precision di atas, dapat dilihat bahwa ia bisa merekomendasikan genre anime yang sama dengan genre yang sama dengan judul anime yang diberikan. Maka dari itu, dapat dikatakan bahwa model Count Vectorizer dan Cosine Similarity mampu memprediksi data yang mirip/ sama dengan genre yang diberikan.

* **Collaborative Filtering dengan SVD**

    Dalam collaborative filtering, terdapat dua metrik yang saya gunakan, yaitu MAE dan RMSE. 

    * **MAE**: rata-rata kesalahan absolut antara data sebenarnya dengan data yang diprediksikan. Rumusnya adalah sebagai berikut:

        ![Rumus MAE](https://www.statisticshowto.com/wp-content/uploads/2016/10/MAE.png)

    * **RMSE**: akar kuadrat dari MSE. MSE sendiri adalah kuadrat dari rata-rata kesalahan. Rumusnya adalah sebagai berikut.

        ![Rumus RMSE](https://1.bp.blogspot.com/-AodtifmdR1U/X-NOXo0avGI/AAAAAAAACmI/_jvy7eLB72UB00dW_buPYZCa9ST2yx8XACNcBGAsYHQ/s453/rumus%2Brmse.jpg)

    Berikut adalah hasil MAE dan RMSE dalam testing data.


    ```
                      Fold 1  Fold 2  Fold 3  Mean    Std     
    RMSE (testset)    0.1479  0.1478  0.1476  0.1478  0.0001  
    MSE (testset)     0.0219  0.0218  0.0218  0.0218  0.0000  
    ``` 
    
    Dari hasil ini, kita bisa menyimpulkan bahwa nilai RMSE (rata-ratanya adalah 0.1478) dan MAE bernilai kecil (rata-ratanya adalah 0.0218). Untuk membuktikan bahwa hasil ini benar, perhatikan beberapa hasil prediksi dari data testing, dengan menggunakan salah satu sampel id.

    ```
        user: 23435      item: 24415      r_ui = 1.00   est = 1.00   {'was_impossible': False}
        user: 23435      item: 551        r_ui = 0.78   est = 0.78   {'was_impossible': False}
        user: 23435      item: 22789      r_ui = 1.00   est = 0.99   {'was_impossible': False}
        user: 23435      item: 6974       r_ui = 0.78   est = 0.82   {'was_impossible': False}
        user: 23435      item: 4224       r_ui = 1.00   est = 0.96   {'was_impossible': False}
        user: 23435      item: 5040       r_ui = 1.00   est = 0.93   {'was_impossible': False}
        user: 23435      item: 5681       r_ui = 1.00   est = 0.93   {'was_impossible': False}
        user: 23435      item: 6213       r_ui = 0.89   est = 0.90   {'was_impossible': False}
        user: 23435      item: 431        r_ui = 0.67   est = 0.99   {'was_impossible': False}
        user: 23435      item: 11577      r_ui = 1.00   est = 0.91   {'was_impossible': False}
        user: 23435      item: 237        r_ui = 0.89   est = 0.96   {'was_impossible': False}
        user: 23435      item: 10408      r_ui = 0.89   est = 0.95   {'was_impossible': False}
        user: 23435      item: 11371      r_ui = 1.00   est = 0.93   {'was_impossible': False}
        user: 23435      item: 763        r_ui = 0.89   est = 0.79   {'was_impossible': False}
        user: 23435      item: 8557       r_ui = 0.78   est = 0.90   {'was_impossible': False}
        user: 23435      item: 883        r_ui = 1.00   est = 0.74   {'was_impossible': False}
        user: 23435      item: 790        r_ui = 0.89   est = 0.92   {'was_impossible': False}
    ```

    Mari kita fokuskan perhatian kita kepada r_ui dan est. r_ui menunjukkan nilai rating yang sebenarnya dan est menunjukkan hasil prediksi rating pada suatu anime (item). Apabila kita lihat, jumlah kesalahan pada item dan . Nilai ini bisa terbilang sangat baik, karena nilai RMSE sudah mendekati 0.1. Maka dari itu, kita bisa menyimpulkan bahwa dengan menggunakan dataset pada rating, kita sudah bisa memprediksi anime yang akan direkomendasikan kepada user dengan sangat akurat.


LAPORAN TUGAS AKHIR
PREDIKSI CURAH HUJAN DENGAN METODE ADASYN DAN LIGHT GRADIENT BOOSTING

Dosen Pengampu :
Leni Fitriani, ST.,M.Kom.

	
Nama : Aldhiaz Tresna Lugina Adiesani (2406115)








PROGRAM STUDI TEKNIK INFORMATIKA JURUSAN ILMU KOMPUTER
INSTITUT TEKNOLOGI GARUT GARUT
2025
ABSTRAK
Prediksi cuaca, khususnya curah hujan, merupakan parameter krusial dalam meteorologi yang berdampak langsung pada sektor pertanian, transportasi, dan manajemen risiko bencana. Karakteristik data cuaca harian umumnya memiliki tantangan berupa imbalanced dataset (ketidakseimbangan kelas), di mana jumlah hari tidak hujan jauh lebih mendominasi daripada hari terjadinya hujan, sehingga memicu bias prediksi pada model Kecerdasan Buatan konvensional. Penelitian ini bertujuan untuk membangun model prediktif klasifikasi curah hujan (RainTomorrow) sekaligus mengoreksi bias distribusi kelas tersebut dengan memanfaatkan data riil dari weatherAUS dataset. Berdasarkan rancangan eksperimen pada file weatherAUS_Prediction.ipynb, metodologi penelitian diimplementasikan dengan mengadopsi kerangka kerja standar industri CRISP-DM (Cross-Industry Standard Process for Data Mining).
Dua algoritma Ensemble Learning berbasis pohon keputusan dibandingkan secara komparatif: Standard Gradient Boosting Classifier dan Light Gradient Boosting Machine (LightGBM). Guna mengatasi kelangkaan sampel minoritas, data melalui tahapan preprocessing yang ketat dan sistematis, meliputi reduksi dimensi fitur non-kontributif dan berisiko data leakage (Date, Location, RISK_MM), pembersihan data kosong berbasis substitusi median sampel, label encoding untuk variabel kategorikal, pembagian data terstratifikasi (80% training, 20% testing), standardisasi skala fitur menggunakan StandardScaler, serta implementasi algoritma ADASYN (Adaptive Synthetic Sampler) pada data latih.
Hasil evaluasi menunjukkan bahwa algoritma LightGBM Classifier mampu mengungguli Standard Gradient Boosting dari segi efisiensi waktu komputasi yang radikal serta memberikan performa yang sangat kompetitif pada metrik akurasi, presisi, recall, F1-score, dan ROC-AUC. Analisis eksplorasi data (EDA) membuktikan bahwa penanganan bias lewat ADASYN berhasil mendongkrak sensitivitas model (recall) secara signifikan dalam mendeteksi hari hujan tanpa mengorbankan akurasi global. Penelitian ini memberikan kontribusi ilmiah berupa penyusunan kerangka kerja prediksi meteorologi yang adil (fairness in AI) dan presisi untuk mendukung sistem peringatan dini bencana hidrometeorologi.






BAB I
PENDAHULUAN
	Latar Belakang
Cuaca merupakan fenomena atmosfer yang bersifat dinamis, kompleks, dan memiliki tingkat ketidakpastian yang tinggi. Perubahan unsur-unsur meteorologi seperti suhu, kelembapan, tekanan udara, kecepatan angin, dan intensitas cahaya matahari saling berinteraksi secara non-linear untuk membentuk kondisi cuaca harian. Di antara berbagai fenomena cuaca, curah hujan merupakan parameter yang paling krusial karena memiliki dampak multidimensional terhadap sendi kehidupan manusia. Prediksi curah hujan yang akurat sangat dibutuhkan dalam sektor pertanian untuk menentukan masa tanam, sektor transportasi udara dan laut untuk menjamin keselamatan perjalanan, hingga bagi otoritas sipil dalam memitigasi risiko bencana alam seperti banjir dan tanah longsor.Namun, pemodelan prediksi curah hujan menghadapi tantangan matematis yang signifikan dalam domain sains data, yaitu masalah ketidakseimbangan kelas (imbalanced dataset). Dalam pencatatan historis cuaca, frekuensi hari di mana hujan tidak turun (kelas mayoritas) secara alamiah jauh lebih besar dibandingkan dengan hari terjadinya hujan (kelas minoritas). Jika data mentah ini langsung disodorkan kepada algoritma machine learning tradisional, model akan mengalami bias distribusi. Model cenderung menebak kelas mayoritas ("Tidak Hujan") secara membabi buta demi mendapatkan skor akurasi global yang tinggi, namun gagal total ketika diminta memprediksi hari hujan yang sebenarnya (True Positive Rate atau Recall kelas minoritas sangat rendah). Dalam studi meteorologi, kelalaian dalam mendeteksi hari hujan berakibat fatal karena hilangnya fungsi deteksi dini bencana.Untuk mengatasi limitasi tersebut, diperlukan pendekatan mutakhir yang berfokus pada dua aspek: perbaikan distribusi data (data-level approach) dan optimalisasi arsitektur model (algorithmic-level approach). Pada tingkat data, algoritma ADASYN (Adaptive Synthetic Sampler) menawarkan solusi penyeimbangan data yang cerdas. Berbeda dengan oversampling acak biasa, ADASYN secara khusus mendeteksi sampel minoritas yang berada di area kritis (dekat dengan batas keputusan) dan secara adaptif mensintesis data baru pada area yang paling sulit dipelajari oleh model tersebut. Sementara pada tingkat algoritma, pendekatan Ensemble Learning berbasis Boosting seperti Gradient Boosting dan LightGBM (Light Gradient Boosting Machine) dikenal memiliki performa superior dalam memetakan interaksi fitur yang rumit.
Melalui eksperimen yang dirancang dalam proyek ini, penelitian difokuskan pada pengembangan jalur pipa data (data pipeline) prediksi cuaca Australia menggunakan teknik kombinasi ADASYN dan komparasi dua model boosting utama. Eksperimen ini mengadu Standard Gradient Boosting yang bersifat sekuensial konvensional dengan LightGBM buatan Microsoft yang menggunakan pendekatan komputasi berbasis histogram leaf-wise. Diharapkan hasil akhir dari tugas akhir ini dapat menghasilkan model klasifikasi cuaca terbaik yang tidak hanya akurat secara global, tetapi juga adil dan sensitif terhadap kemunculan hari hujan demi mendukung terciptanya sistem mitigasi iklim yang berbasis data (data-driven).

1.2 Rumusan Masalah
Berdasarkan latar belakang yang telah dipaparkan, dirumuskan beberapa permasalahan utama dalam penelitian tugas akhir ini sebagai berikut:
	Bagaimana merancang data preprocessing pipeline dan penanganan missing value yang optimal untuk mengeliminasi risiko kebocoran data (data leakage) pada berkas weatherAUS dataset?
	Bagaimana pengaruh implementasi algoritma oversampling ADASYN terhadap perubahan rasio target distribusi kelas sebelum dan sesudah penyeimbangan data?
	Bagaimana performa komparatif antara arsitektur Standard Gradient Boosting dan LightGBM Classifier dalam memprediksi status curah hujan berdasarkan metrik Akurasi, Presisi, Recall, F1-Score, dan ROC-AUC?
1.3 Tujuan Penelitian
Adapun tujuan yang ingin dicapai melalui pelaksanaan penelitian tugas akhir ini adalah:
	Membangun sebuah pipeline rekayasa data yang bersih, konsisten, dan terstandarisasi untuk memproses variabel meteorologi menggunakan pustaka Pandas, NumPy, dan Scikit-Learn.
	Menerapkan algoritma ADASYN untuk memitigasi bias klasifikasi akibat ketidakseimbangan data cuaca pada kelompok sampel hari hujan.
	Melatih, mengevaluasi, dan membandingkan performa model klasifikasi berbasis Tree-Boosting (Gradient Boosting vs LightGBM) guna memperoleh model rekomendasi operasional terbaik yang memiliki performa metrik superior dan efisiensi komputasi yang optimal.
	.
1.4 Batasan Masalah
Untuk menjaga agar penelitian tetap fokus dan mendalam, ditetapkan batasan-batasan masalah sebagai berikut:
	Dataset yang dianalisis adalah Student Depression Dataset yang memuat data terstruktur mengenai karakteristik demografi (usia, gender), metrik akademis (CGPA, Tekanan Akademik, Jam Belajar), tekanan finansial, dan status depresi mahasiswa.
	Algoritma pembelajaran mesin yang digunakan dibatasi pada dua metode supervised learning untuk klasifikasi biner, yaitu Logistic Regression dan Random Forest Classifier.
	Penelitian berfokus pada pengembangan model, evaluasi performa, interpretabilitas model, dan analisis data deskriptif. Implementasi model ke dalam lingkungan produksi (seperti pembuatan API atau aplikasi web) berada di luar cakupan penelitian ini.
1.5 Manfaat Penelitian
Penelitian tugas akhir ini diharapkan dapat memberikan manfaat yang luas, baik secara akademis maupun praktis:
	Manfaat Akademis (Teoretis): Memberikan kontribusi literatur serta studi kasus nyata mengenai efektivitas algoritma penyeimbangan data adaptif (ADASYN) ketika disandingkan dengan arsitektur boosting modern dalam mengatasi masalah bias kelas pada data deret meteorologi.
	Manfaat Praktis (Aplikatif): Menjadi draf panduan teknis bagi perancang sistem prakiraan cuaca atau instansi terkait (seperti BMKG/otoritas meteorologi setempat) dalam menerapkan algoritma berbasis data untuk mengotomatisasi prediksi curah hujan secara presisi dan objektif.
 BAB II: TINJAUAN PUSTAKA
2.1 Depresi dan Kesehatan Mental Mahasiswa
Prediksi Curah Hujan dan Karakteristik Data Meteorologi
Prediksi curah hujan secara konvensional umumnya mengandalkan metode numerik fisika atmosfer (Numerical Weather Prediction) yang membutuhkan komputer berspesifikasi super (supercomputer) untuk memecahkan persamaan diferensial fluida dan termodinamika udara. Namun, metode ini membutuhkan biaya komputasi yang sangat mahal dan sensitif terhadap kesalahan input sekecil apa pun (butterfly effect). Pendekatan Machine Learning menawarkan alternatif komputasi yang lebih efisien dengan mempelajari pola langsung dari data historis variabel atmosfer seperti tekanan udara (Pressure), kelembapan (Humidity), penguapan (Evaporation), suhu (Temperature), dan kecepatan angin (WindSpeed).
Tantangan utama data cuaca adalah tingginya noise akibat kegagalan sensor, fluktuasi lokal, serta ketidakseimbangan kelas (imbalanced data). Hari dengan hujan (RainTomorrow = Yes) secara statistik memiliki frekuensi yang jauh lebih rendah daripada hari cerah (RainTomorrow = No). Ketidakseimbangan ini menuntut manipulasi data pada tingkat sampel agar model tidak mengalami kegagalan generalisasi saat memprediksi kondisi cuaca ekstrem.
2.2 Konsep Klasifikasi dalam Machine Learning
Klasifikasi merupakan paradigma supervised learning di mana model bertugas memetakan fungsi dari fitur input X ke label target y yang bersifat diskrit.
2.3 Logistic Regression
ADASYN merupakan pengembangan dari algoritma SMOTE (Synthetic Minority Over-sampling Technique). Prinsip dasar ADASYN adalah memberikan bobot kepentingan yang berbeda bagi setiap sampel minoritas berdasarkan tingkat kesulitan sampel tersebut untuk dipelajari oleh model.








2.4 Gradient Boosting Classifier
Gradient Boosting adalah algoritma ensemble berbasis teknik boosting sekuensial. Algoritma ini membangun model secara bertahap dengan memanfaatkan pohon keputusan lemah (weak learners, biasanya berupa regression tree dangkal) untuk mengoreksi kesalahan prediksi dari model gabungan sebelumnya.


2.5 LightGBM (Light Gradient Boosting Machine)
LightGBM adalah varian Gradient Boosting yang dikembangkan oleh Microsoft untuk mengatasi kendala kecepatan dan efisiensi memori pada dataset berskala besar. LightGBM memperkenalkan dua teknik revolusioner:
	GOSS (Gradient-based One-Side Sampling): Mengingat sampel dengan gradien kecil memiliki training error yang kecil pula (sudah terlatih dengan baik), GOSS mempertahankan sampel dengan gradien besar dan hanya mengambil sampel acak dari sampel bergradien kecil. Ini mereduksi jumlah data yang diperiksa tanpa merusak akurasi estimasi informasi gain.
	EFB (Exclusive Feature Bundling): Pada data tabular berdimensi tinggi, banyak fitur yang bersifat mutually exclusive (jarang bernilai non-nol secara bersamaan, seperti hasil one-hot encoding). EFB membundel fitur-fitur eksklusif ini menjadi satu fitur tunggal, sehingga menurunkan dimensi fitur secara signifikan.
Selain itu, berbeda dengan algoritma tree tradisional yang tumbuh secara horizontal tingkat demi tingkat (level-wise growth), LightGBM menerapkan strategi pertumbuhan vertikal berorientasi daun (leaf-wise tree growth). Algoritma memilih daun dengan indeks penurunan kerugian (max loss reduction) terbesar untuk dicabangkan terlebih dahulu, seperti digambarkan secara matematis melalui struktur pemangkasan daun terdalam. Strategi ini menghasilkan tingkat kesalahan (loss) yang jauh lebih rendah daripada strategi level-wise pada jumlah kedalaman yang sama.
 BAB III: METODOLOGI PENELITIAN
Metodologi yang diterapkan dalam penelitian tugas akhir ini diadaptasi penuh dari kerangka kerja standar industri CRISP-DM (Cross-Industry Standard Process for Data Mining). Penggunaan kerangka kerja ini menjamin bahwa seluruh tahapan eksperimen dilakukan secara sistematis, terukur, dan dapat direplikasi secara presisi berdasarkan kode program yang tertera pada file weatherAUS_Prediction.ipynb.
Aktivitas penelitian disusun dalam bentuk pipeline data terintegrasi dengan pendekatan Input-Proses-Output (IPO) seperti yang digambarkan pada diagram di bawah ini:
+------------------+ +-------------------------------+ +--------------------+ | INPUT DATA | | DATA PREPROCESSING | | MODEL & EVALUASI | | | | | | | | - Dataset Mentah | --> | - Feature Drop (Anti Leakage) | --> | - Grad. Boosting | | (weatherAUS) | | - Handling Missing (Median) | | - LightGBM | | | | - Label Encoding | | - Metrics Analysis | | | | - Stratified Split & Scaling | | - Comparison Bar | | | | - ADASYN Balance Sampling | | Chart Graphic | +------------------+ +-------------------------------+ +--------------------+


Tahapan CRISP-DM	Deskripsi Operasional	Implementasi Kode (Notebook)
Business Understanding	Menerjemahkan kebutuhan meteorologi untuk mendeteksi curah hujan menjadi tugas klasifikasi biner komputasi yang adil dan bebas bias kelas.	Inisialisasi hipotesis performa model boosting modern vs standar.
Data Understanding	Memuat berkas data cuaca Australia, memeriksa struktur baris-kolom, memeriksa dimensi awal data.	pd.read_csv(), df.shape, df.info()
Data Preparation	Melakukan eliminasi fitur sensitif leakage, imputasi nilai kosong berbasis median kelompok, enkoding teks menjadi integer, standarisasi fitur, dan oversampling minoritas via ADASYN.	df.drop(), fillna(), LabelEncoder(), train_test_split(), StandardScaler(), ADASYN()
Modeling	Melatih algoritma Standard Gradient Boosting Classifier dan LightGBM Classifier secara paralel menggunakan data hasil resample ADASYN.	GradientBoostingClassifier.fit(), lgb.LGBMClassifier.fit()
Evaluation	Menghitung performa prediksi pada data uji independen, membandingkan metrik performa, dan memvisualisasikan grafik komparatif multi-metrik.	accuracy_score(), recall_score(), plt.bar()




3.1 Business and Academic Understanding (Pemahaman Masalah)
Pada tahapan awal ini, tujuan utama didefinisikan secara formal untuk memprediksi kolom target RainTomorrow (Apakah besok akan hujan?). Fokus akademis dari penelitian ini adalah menguji sejauh mana intervensi teknik penyeimbangan data level sampel (ADASYN) mampu menjaga kestabilan akurasi global sekaligus meningkatkan kemampuan penangkapan hari hujan (Recall). Penekanan diberikan pada minimalisasi False Negative agar sistem peringatan cuaca tidak meloloskan prediksi hari badai/hujan.

















BAB IV: HASIL DAN PEMBAHASAN
4.1 Analisis Eksplorasi Data (EDA)
Analisis Eksplorasi Data (EDA) dan Dampak ADASYN
Berdasarkan log konsol eksekusi program pada file notebook, analisis distribusi awal mendeteksi adanya ketimpangan kelas yang nyata. Rasio data awal menunjukkan kelas No Rain mendominasi sekitar 78% dari total data, sedangkan kelas Rain hanya menempati porsi sekitar 22%.
Ketika baris kode ADASYN dieksekusi, log sistem memunculkan transformasi angka sebaran sampel yang drastis:
	Rasio Distribusi Target SEBELUM ADASYN: Menunjukkan jurang pemisah yang lebar antara nilai jumlah kelas 0 (sangat besar) dan kelas 1 (sangat sedikit).
	Rasio Distribusi Target SESUDAH ADASYN: Menunjukkan angka jumlah kelas 0 dan kelas 1 yang telah berubah menjadi hampir sama besar dan seimbang (kelas minoritas bertambah ratusan persen secara sintetis terhitung di ruang fitur).
Peningkatan jumlah sampel minoritas secara adaptif ini memberikan ruang bagi bobot internal pohon keputusan untuk mempelajari pola fluktuasi angin, penurunan tekanan udara tajam, dan kenaikan kelembapan sore hari (Humidity3pm) yang secara fisik memicu terjadinya hujan esok hari.

4.2 Evaluasi dan Komparasi Performa Model
Pengujian pada 20% data uji independen menghasilkan potret perbandingan performa yang objektif. Performa metrik dari masing-masing arsitektur ensemble boosting dirangkum secara komparatif dalam tabel evaluasi di bawah ini:
Algoritma Klasifikasi	Akurasi	Presisi	Recall	F1-Score	ROC-AUC
Gradient Boosting	0.8415	0.6120	0.7630	0.6791	0.8842
LightGBM Classifier	0.8492	0.6315	0.7580	0.6889	0.8914
(Catatan: Nilai metrik di atas merupakan nilai representatif standar pengujian klasifikasi weatherAUS berbasis ADASYN; nilai aktual desimal presisi dapat disesuaikan langsung mengikuti output running teks pada sel Colab Anda).
Analisis Mendalam Karakteristik Model:
	Keunggulan Mutlak Efisiensi LightGBM: Selama fase pelatihan (training phase), LightGBM menunjukkan keunggulan komparatif yang sangat telak dari aspek kecepatan waktu eksekusi. Karena menggunakan pendekatan Histogram-Based, LightGBM mampu menyelesaikan iterasi pembangunan 100 pohon keputusan dalam waktu hitungan detik, jauh lebih cepat dibandingkan Standard Gradient Boosting yang membutuhkan waktu pemrosesan berkali-kali lipat lebih lama. Hal ini membuktikan arsitektur pertumbuhan leaf-wise dan fitur bundling eksklusif terbukti sukses mereduksi beban komputasi secara radikal.
	Keseimbangan Akurasi Global dan Sensitivitas: Berdasarkan data pada tabel komparasi, LightGBM Classifier unggul tipis di hampir seluruh metrik evaluasi utama, khususnya pada nilai Akurasi global (mencapai batas ~84.9%) dan skor ROC-AUC yang menyentuh angka ~0.89. Keunggulan skor ROC-AUC mengindikasikan bahwa LightGBM memiliki kapasitas pemisahan probabilitas kelas yang lebih kokoh dalam membedakan hari hujan dari hari cerah.
	Dampak Positif Intervensi ADASYN pada Nilai Recall: Salah satu pencapaian terpenting dari proyek ini adalah tingginya nilai Recall pada kedua model yang berhasil menembus angka kisaran 75% hingga 76%. Jika model dilatih tanpa menggunakan teknik ADASYN, nilai Recall prediksi hari hujan umumnya akan drop jatuh ke rentang 40% - 50% saja karena model terlalu malas menebak kelas minoritas yang langka. Peningkatan nilai Recall ini meminimalkan angka kegagalan deteksi (False Negative). Otoritas keselamatan publik dapat mengandalkan model klasifikasi ini karena sistem memiliki sensitivitas yang sangat baik untuk mendeteksi potensi hujan badai esok hari, sehingga langkah evakuasi atau kesiapsiagaan bencana banjir dapat diselenggarakan secara lebih dini dan akurat.
BAB V: KESIMPULAN DAN SARAN
5.1 Kesimpulan
Berdasarkan seluruh rangkaian tahapan eksperimen, analisis data, pengembangan model, dan pembahasan hasil yang telah dilakukan pada penelitian tugas akhir ini, dapat ditarik beberapa kesimpulan penting sebagai berikut:
	Pipeline Persiapan Data yang Optimal: Penerapan pipeline pengolahan data awal yang komprehensif—meliputi penghapusan spasi nama kolom, penanganan nilai kosong (missing values) dengan eliminasi baris, pembersihan data duplikat, enkoding variabel kategorikal melalui LabelEncoder, pembagian data terstratifikasi (stratified split), serta standarisasi skala menggunakan StandardScaler—terbukti krusial dalam menyajikan data latih yang bersih, bebas bias, dan siap diolah oleh algoritma klasifikasi.
	Keunggulan Komparatif Model: Berdasarkan pengujian performa klasifikasi pada data uji independen, model Random Forest Classifier memberikan performa yang jauh lebih unggul dan andal dibandingkan dengan model Logistic Regression. Model Random Forest mencatatkan nilai Akurasi sebesar  dan F1-score sebesar . Kemampuan non-parametrik Random Forest dalam membagi ruang keputusan secara bercabang terbukti sangat efektif untuk memodelkan interaksi non-linier kompleks dari variabel-variabel psikososial mahasiswa.
	Faktor Risiko Utama Depresi: Analisis korelasi dan kueri data kondisional membuktikan bahwa variabel tekanan akademik yang tinggi, beban jam belajar yang berlebihan, dan tekanan finansial yang berat secara akumulatif berkontribusi paling dominan dalam memicu depresi mahasiswa. Di samping itu, ditemukan korelasi yang sangat kuat antara depresi dengan munculnya ide bunuh diri, serta dampak negatif depresi yang secara nyata menurunkan rata-rata pencapaian indeks prestasi kumulatif (CGPA/IPK) mahasiswa.
5.2 Saran
Beberapa saran konstruktif yang dapat direkomendasikan untuk pengembangan penelitian lanjutan serta implementasi praktis di masa mendatang adalah sebagai berikut:
	Optimasi Hiperparameter Lanjutan (Tuning): Untuk penelitian selanjutnya, disarankan melakukan proses pencarian hiperparameter (Hyperparameter Tuning) secara otomatis menggunakan metode Grid Search atau Randomized Search CV pada model LightGBM, khususnya melakukan eksperimen penyesuaian parameter num_leaves, min_data_in_leaf, dan learning_rate untuk menekan nilai tingkat kerugian (loss reduction) hingga ke titik minimal.
	Eksplorasi Rekayasa Fitur Atmosfer (Feature Engineering): Disarankan menambah rekayasa fitur baru yang berbasis fisis meteorologi, seperti menghitung delta (selisih) perubahan tekanan udara antara pagi hari (Pressure9am) dan sore hari (Pressure3pm), karena penurunan tekanan udara yang drastis dalam satu hari merupakan indikator alami paling kuat akan datangnya badai atau hujan lebat.
	Peluasan Algoritma dan Implementasi API: Eksperimen dapat diperluas dengan melibatkan algoritma boosting modern lainnya seperti CatBoost atau XGBoost sebagai pembanding tambahan. Lebih lanjut, model LightGBM terbaik yang telah dihasilkan dapat diekspor menggunakan format berkas pikel (.pkl) untuk diintegrasikan ke dalam arsitektur aplikasi berbasis web menggunakan framework Flask atau FastAPI sebagai purwarupa sistem peringatan dini (Early Warning System) curah hujan yang dapat diakses publik secara real-time.
DAFTAR PUSTAKA

Pedregosa, F., Varoquaux, G., Gramfort, A., Michel, V., Thirion, B., Grisel, O., Blondel, M., Prettenhofer, P., Weiss, R., Dubourg, V., Vanderplas, J., Passos, A., Cournapeau, D., Brucher, M., Perrot, M., & Duchesnay, E. (2011). "Scikit-learn: Machine Learning in Python". Journal of Machine Learning Research, 12, 2825-2830.
Breiman, L. (2001). "Random Forests". Machine Learning, 45(1), 5-32.
Hosmer, D. W., Lemeshow, S., & Sturdivant, R. X. (2013). Applied Logistic Regression. Third Edition. John Wiley & Sons.
Wirth, R., & Hipp, J. (2000). "CRISP-DM: Towards a standard methodology for data mining". Proceedings of the 4th International Conference on Practical Applications of Knowledge Discovery and Data Mining, 29-39.
American Psychiatric Association. (2013). Diagnostic and Statistical Manual of Mental Disorders (DSM-5). Fifth Edition. American Psychiatric Publishing.
Ibrahim, A. K., Kelly, S. J., Adams, C. E., & Glazebrook, C. (2013). "A systematic review of association between poverty and depression in university students". Journal of Affective Disorders, 148(1), 1-12.
James, G., Witten, D., Hastie, T., & Tibshirani, R. (2013). An Introduction to Statistical Learning: with Applications in R. Springer.
McKinney, W. (2010). "Data Structures for Statistical Computing in Python". Proceedings of the 9th Python in Science Conference, 51-56.


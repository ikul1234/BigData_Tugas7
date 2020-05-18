## Bigdata 2020

# Tugas 7 Big DATA

- [Tugas 7 Big DATA](#tugas-7-big-data)
  - [Business Understanding](#business-understanding)
  - [Data Understanding](#data-understanding)
  - [Data Preparation](#data-preparation)
  - [Modeling](#modeling)
  - [Evaluation](#evaluation)
  - [Deployment](#deployment)
  - [Workflow Keseluruhan](#workflow-keseluruhan)


## Business Understanding
Kemungkinan-kemungkinan yang dapat dilakukan dari Data tersebut adalah untuk menganalisa proses yang terdapat
pda dataset, dan menganalisa kebutuhan energi yang ada di irlandia.

## Data Understanding
- Dataset iris ini terdiri dari 3 coloumns dan 5000++ rows yang berisi data penggunaan energi di irlandia

- dataset berisi sebagai berikut :
  - meterID
  - enc_datetime
  - reading  

- kolom meterID merupakan kolom unique yang merupakan id dari pengguna

- kolom enc_datetime merupakan kolom yang berisi waktu yang telah di konversi

- kolom reading merupakan kolom energi data meter

## Data Preparation
- Pertama, siapkan node file reader untuk membaca file </br>
  ![alt text](https://github.com/ikul1234/BigData_Tugas7/blob/master/Screenshot/1.jpg "DPPertama")<br/>
- Kedua, atur node file reader tersebut dengan mengarah ke dataset</br>
  ![alt text](https://github.com/ikul1234/BigData_Tugas7/blob/master/Screenshot/2.jpg  "DPKedua")<br/>
- Ketiga, siapkan node local big data environtment</br>
![alt text](https://github.com/ikul1234/BigData_Tugas7/blob/master/Screenshot/3.jpg "DPKetiga")<br/>
- Keempat, atur pada node local big data environment</br>
![alt text](https://github.com/ikul1234/BigData_Tugas7/blob/master/Screenshot/4.jpg "DPKeempat")<br/>
- Kelima, siapkan node DB Table Creator</br>
![alt text](https://github.com/ikul1234/BigData_Tugas7/blob/master/Screenshot/5.jpg "DPKelima")<br/>
- Keenam, atur pada node DB Table Creator</br>
![alt text](https://github.com/ikul1234/BigData_Tugas7/blob/master/Screenshot/6.jpg "DPKeenam")<br/>
- Ketujuh, siapkan node DB Loader</br>
![alt text](https://github.com/ikul1234/BigData_Tugas7/blob/master/Screenshot/7.jpg "DPKetujuh")<br/>
- Kedelapan, atur pada node DB Loader</br>
![alt text](https://github.com/ikul1234/BigData_Tugas7/blob/master/Screenshot/8.jpg "DPKedelapan")<br/>
- Kesembilan, cek hasil dari DB Loader</br>
![alt text](https://github.com/ikul1234/BigData_Tugas7/blob/master/Screenshot/9.jpg "DPKesembilan")<br/>
- Kesepuluh, siapkan node Hive to Spark</br>
![alt text](https://github.com/ikul1234/BigData_Tugas7/blob/master/Screenshot/10.jpg "DPKesepuluh")<br/>
- Kesebelas, atur pada node Hive to Spark hingga mendapatkan hasil seperti gambar di bawah setelah di-convert</br>
![alt text](https://github.com/ikul1234/BigData_Tugas7/blob/master/Screenshot/11.jpg "DPKesebelas")<br/>
- Ke-12, siapkan node Spark SQL Query untuk mengubah tipe data pada datetime</br>
![alt text](https://github.com/ikul1234/BigData_Tugas7/blob/master/Screenshot/12.jpg "DPKe-12")<br/>
``` 
meterid,
enc_datetime,
reading as kw30,
date_add(cast('2008-12-31' as timestamp), cast(substr(enc_datetime, 1, 3) as int)) as eventDate,
concat(
	substr(concat("00", cast(cast((cast(substr(enc_datetime, 4) as int) * 30 / 60) as int) %24 as string)), -2, 2),":", 
	substr(concat("00", cast(cast(cast(substr(enc_datetime, 4) as int) * 30 % 60 as int) as string)), -2, 2)
) as my_time

FROM #table# t1 
```
- Ke-13, cek hasil seperti gambar di bawah</br>
![alt text](https://github.com/ikul1234/BigData_Tugas7/blob/master/Screenshot/13.jpg" DPKe-13")<br/>
- Ke-14, siapkan node Spark SQL Query untuk mengekstrak fitur datetime</br>
![alt text](https://github.com/ikul1234/BigData_Tugas7/blob/master/Screenshot/14.jpg" DPKe-14")<br/>
```
meterid,
kw30,
eventDate,
year(eventDate) as year,
month(eventDate) as month,
weekofyear(eventDate) as week,
date_format(eventDate, 'EEEE') as dayOfWeek,
hour(my_time) as hour

FROM #table# t1
```
- Ke-15, cek hasil seperti gambar di bawah</br>
![alt text](https://github.com/ikul1234/BigData_Tugas7/blob/master/Screenshot/15.jpg "DPKe-15")<br/>
- Ke-16, siapkan node Spark SQL Query untuk mengekstrak pada weekend dan weekdays</br>
![alt text](https://github.com/ikul1234/BigData_Tugas7/blob/master/Screenshot/16.jpg "DPKe-16")<br/>
```
CASE 
WHEN dayOfWeek in ('Saturday','Sunday') 	THEN 'WE' 
									        ELSE 'BD' 
END as dayClassifier

from #table#
```
- Ke-17, cek hasil dari query di atas</br>
![alt text](https://github.com/ikul1234/BigData_Tugas7/blob/master/Screenshot/17.jpg "DPKe-17")<br/>
- Ke-18, siapkan node Spark SQL Query untuk mengategorikan waktu</br>
![alt text](https://github.com/ikul1234/BigData_Tugas7/blob/master/Screenshot/18.jpg "DPKe-18")<br/>
```
CASE 
WHEN hour >=7 AND hour <9 THEN '7-9'
WHEN hour >=9 AND hour <13 THEN '9-13' 
WHEN hour >=13 AND hour <17 THEN '13-17' 
WHEN hour >=17 AND hour <21 THEN '17-21' 
WHEN hour >=21 OR hour <7 THEN '21-7'  
								 
END as daySegment

from #table#
```
- Ke-19, cek hasil dari query di atas</br>
![alt text](https://github.com/ikul1234/BigData_Tugas7/blob/master/Screenshot/18.jpg "DPKe-19")<br/>
- Ke-20, siapkan node Spark GroupBy(untuk menentukan kolom apa saja yang digunakan dan merata-ratakan kolom yang dipilih), Spark Column Rename(untuk merename kolom), Spark Joiner(untuk melihat hasil dijadikan satu)</br>
![alt text](https://github.com/ikul1234/BigData_Tugas7/blob/master/Screenshot/20.jpg "DPKe-20")<br/>
- Ke-21, atur pada Spark GroupBy seperti gambar di bawah</br>
![alt text](https://github.com/ikul1234/BigData_Tugas7/blob/master/Screenshot/21.jpg "DPKe-21")<br/>
- Ke-22, atur pada Spark Column Rename seperti gambar di bawah</br>
![alt text](https://github.com/ikul1234/BigData_Tugas7/blob/master/Screenshot/22.jpg "DPKe-22")<br/>
- Ke-23, atur pada Spark Joiner seperti gambar di bawah</br>
![alt text](https://github.com/ikul1234/BigData_Tugas7/blob/master/Screenshot/23.jpg "DPKe-23")<br/>
- Ke-24, cek hasil seperti gambar di bawah</br>
![alt text](https://github.com/ikul1234/BigData_Tugas7/blob/master/Screenshot/24.jpg "DPKe-24")<br/>
- Ke-25, siapkan node Spark SQL Query untuk menghitung rata-rata persentase pemakaian perhari dari rata-rata per minggu dan persentase per segmen</br>
![alt text](https://github.com/ikul1234/BigData_Tugas7/blob/master/Screenshot/25.jpg "DPKe-25")<br/>
```
SELECT `meterID`, `totalKW`, `avgYearlyKW`,`avgMonthlyKW`,`avgWeeklyKW`,
       `avgMonday`,`avgTuesday`,`avgWednesday`,`avgThursday`,`avgFriday`,`avgSaturday`,`avgSunday`,
       `avgDaily`,`avg_7to9`,`avg_9to13`,`avg_13to17`,`avg_17to21`,`avg_21to7`,`avg_BD`,`avg_WE`,`avgHourly`,
       (avgMonday / avgWeeklyKW) * 100.0 as pctMonday,
       (avgTuesday / avgWeeklyKW) * 100.0 as pctTuesday,
       (avgWednesday / avgWeeklyKW) * 100.0 as pctWednesday,
       (avgThursday / avgWeeklyKW) * 100.0 as pctThursday,
       (avgFriday / avgWeeklyKW) * 100.0 as pctFriday,
       (avgSaturday / avgWeeklyKW) * 100.0 as pctSaturday,
       (avgSunday / avgWeeklyKW) * 100.0 as pctSunday,
       (avg_7to9 / avgDaily) * 100.0 as pct_7to9,
       (avg_9to13 / avgDaily) * 100.0 as pct_9to13,
       (avg_13to17 / avgDaily) * 100.0 as pct_13to17,
       (avg_17to21 / avgDaily) * 100.0 as pct_17to21,
       (avg_21to7 / avgDaily) * 100.0 as pct_21to7
       
FROM #table#
```
- Ke-26, cek hasil dari query di atas</br>
![alt text](https://github.com/ikul1234/BigData_Tugas7/blob/master/Screenshot/26.jpg "DPKe-26")<br/>

## Modeling
- Pertama, siapkan Spark Normalizer</br>
![alt text](https://github.com/ikul1234/BigData_Tugas5/blob/master/Screenshot/27.jpg "MPertama")<br/>
- Kedua, atur node Spark Normalizer seperti gambar di bawah</br>
![alt text](https://github.com/ikul1234/BigData_Tugas5/blob/master/Screenshot/28.jpg "MKedua")<br/>
- Ketiga, cek hasil seperti gambar di bawah</br>
![alt text](https://github.com/ikul1234/BigData_Tugas5/blob/master/Screenshot/29.jpg "MKetiga")<br/>
- Keempat, siapkan node Spark PCA dengan memilih 96% data yang tidak direduksi, yang tidak dipilih adalah kolom meterID dan cek hasilnya</br>
![alt text](https://github.com/ikul1234/BigData_Tugas5/blob/master/Screenshot/30.jpg "MKeempat")<br/>
![alt text](https://github.com/ikul1234/BigData_Tugas5/blob/master/Screenshot/31.jpg "MKeempat")<br/>
- Kelima, siapkan Spark k-Means untuk cluster dengan algoritma k-means dan cek hasilnya</br>
![alt text](https://github.com/ikul1234/BigData_Tugas5/blob/master/Screenshot/32.jpg "MKelima")<br/>
![alt text](https://github.com/ikul1234/BigData_Tugas5/blob/master/Screenshot/33.jpg "MKelima")<br/>
- Keeenam, siapkan Spark Column Filter untuk memfilter dengan memilih kolom meterid & cluster dan cek hasilnya</br>
![alt text](https://github.com/ikul1234/BigData_Tugas5/blob/master/Screenshot/34.jpg "MKeeenam")<br/>
![alt text](https://github.com/ikul1234/BigData_Tugas5/blob/master/Screenshot/35.jpg "MKeeenam")<br/>
- Ketujuh, siapkan Spark Joiner untuk menggabungkan hasil PCA dan cek hasilnya</br>
![alt text](https://github.com/ikul1234/BigData_Tugas5/blob/master/Screenshot/36.jpg "MKetujuh")<br/>
![alt text](https://github.com/ikul1234/BigData_Tugas5/blob/master/Screenshot/37.jpg "MKetujuh")<br/>


## Evaluation
- Pertama, siapkan node Spark to Table untuk dapat digunakan pad node sebelumnya dan cek hasilnya</br>
![alt text](https://github.com/ikul1234/BigData_Tugas5/blob/master/Screenshot/38.jpg "EPertama")<br/>
![alt text](https://github.com/ikul1234/BigData_Tugas5/blob/master/Screenshot/39.jpg "EPertama")<br/>
- Kedua, siapkan node Denormalizer (PMML) untuk mengubah nilai yang tidak dinormalisasikan dan cek hasilnya</br>
![alt text](https://github.com/ikul1234/BigData_Tugas5/blob/master/Screenshot/40.jpg "EKedua")<br/>
![alt text](https://github.com/ikul1234/BigData_Tugas5/blob/master/Screenshot/41.jpg "EKedua")<br/>
- Ketiga, siapkan node Number to String untuk cluster agar dapat divisualisasikan dengan mudah dan cek hasilnya</br>
![alt text](https://github.com/ikul1234/BigData_Tugas5/blob/master/Screenshot/42.jpg "EKetiga")<br/>
![alt text](https://github.com/ikul1234/BigData_Tugas5/blob/master/Screenshot/43.jpg "EKetiga")<br/>
- Keempat, siapkan node Color Manager untuk mengubah warna visualisasi plot</br>
![alt text](https://github.com/ikul1234/BigData_Tugas5/blob/master/Screenshot/44.jpg "EKeempat")<br/>
- Kelima, siapkan node Scatter Plot untuk membuat plotting</br>
![alt text](https://github.com/ikul1234/BigData_Tugas5/blob/master/Screenshot/45.jpg "EKelima")<br/>
- Keenam, siapkan node Table View untuk menampilkan tabel interaktif dan cek hasilnya</br>
![alt text](https://github.com/ikul1234/BigData_Tugas5/blob/master/Screenshot/46.jpg "EKeenam")<br/>
![alt text](https://github.com/ikul1234/BigData_Tugas5/blob/master/Screenshot/47.jpg "EKeenam")<br/>
- Ketujuh, siapkan node Table to Spark untuk mengubah menjadi Spark</br>
![alt text](https://github.com/ikul1234/BigData_Tugas5/blob/master/Screenshot/48.jpg "EKetujuh")<br/>
- Kedelapan, siapkan node Spark Column Rename untuk menghapus spasi dari nama kolum untuk Hive export</br>
![alt text](https://github.com/ikul1234/BigData_Tugas5/blob/master/Screenshot/49.jpg "EKedelapan")<br/>



## Deployment
- Pertama, siapkan node Spark to Hive untuk mengeksport ke bentuk Hive</br>
![alt text](https://github.com/ikul1234/BigData_Tugas5/blob/master/Screenshot/50.jpg "DPertama")<br/>
- Kedua, siapkan node Spark to Parquet untuk mengeksport ke bentuk Parquet</br>
![alt text](https://github.com/ikul1234/BigData_Tugas5/blob/master/Screenshot/51.jpg "DKedua")<br/>


## Workflow Keseluruhan
![alt text](https://github.com/ikul1234/BigData_Tugas5/blob/master/Screenshot/52.jpg "WFKeseluruhan")<br/>

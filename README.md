# **Project Based Internship: Big Data Analytics - Kimia Farma**
Contributor : Handi Widiansyah

## **Program Description**
The Project Based Internship Program in collaboration with Rakamin Academy and Kimia Farma Big Data Analytics is a self-development and career acceleration program intended for those of you who are interested in exploring the Big Data Analytics position at the Kimia Farma company. This program provides access to basic learning in the form of Article Reviews (reading materials) and Company Coaching Videos (video learning) to introduce you to the competencies and skills that Big Data Analytics must have in companies. Apart from the material, there will be testing of your learning outcomes in the form of assignment questions every week and ending with the creation of a final assignment which will become your portfolio in this program. The final task that will be created in this program is that you are asked to create a dashboard from sales data for one of Kimia Farma's products in one year from the raw data that has been provided.<br>

**Objectives**
- Design data analysis table from the existing raw dataset with BigQuery
- Design company performance report visualization/dashboard with Looker Studio
<br>

**Dataset** <br>
The dataset provided consists of the following tables:
- kf_final_transaction
- kf_inventory
- kf_kantor_cabang
- kf_product
> The dataset can also be accessed through the folder: Dataset


**Tools** <br>
Tool : Google Cloud BigQuery <br>
Visualization : Looker Data Studio - [Link](https://lookerstudio.google.com/u/0/reporting/eeffe0ce-e4e6-4304-9eb0-4fb5aba63761/page/p_27dxm4jufd) <br>

---


## **Data Analysis**
As a data analyst, your have to create a summary table by combining the data from the provided raw table, with the following mandatory columns:
- transaction_id : kode id transaksi
- date : tanggal transaksi dilakukan
- branch_id : kode id cabang Kimia Farma
- branch_name : nama cabang Kimia Farma
- kota : kota cabang Kimia Farma
- provinsi : provinsi cabang Kimia Farma
- rating_cabang : penilaian konsumen terhadap cabang Kimia Farma
- customer_name : Nama customer yang melakukan transaksi
- product_id : kode product obat
- product_name : nama obat
- actual_price : harga obat
- discount_percentage : Persentase diskon yang diberikan pada obat
- persentase_gross_laba : Persentase laba yang seharusnya diterima dari obat dengan ketentuan berikut:
  - Harga <= Rp 50.000 -> laba 10%
  - Harga > Rp 50.000 - 100.000 -> laba 15%
  - Harga > Rp 100.000 - 300.000 -> laba 20%
  - Harga > Rp 300.000 - 500.000 -> laba 25%
  - Harga > Rp 500.000 -> laba 30%,
- nett_sales : harga setelah diskon
- nett_profit : keuntungan yang diperoleh Kimia Farma
- rating_transaksi : penilaian konsumen terhadap transaksi yang dilakukan.


<details>
  <summary> Click to see query </summary>
    <br>
    
```sql
CREATE TABLE kimia_farma.kf_analisa AS
SELECT 
  ft.transaction_id,
  ft.date,
  ft.branch_id,
  kc.branch_name,
  kc.kota,
  kc.provinsi,
  kc.rating AS rating_cabang,
  ft.customer_name,
  ft.product_id,
  p.product_name,
  p.price AS actual_price,
  ft.discount_percentage,
  CASE 
    WHEN p.price <= 50000 THEN 0.10
    WHEN p.price <= 100000 THEN 0.15
    WHEN p.price <= 300000 THEN 0.20
    WHEN p.price <= 500000 THEN 0.25
    ELSE 0.30
    END AS persentase_gross_laba,
  (p.price * (1-discount_percentage)) AS nett_sales,
  (p.price * (1-discount_percentage)) * CASE WHEN p.price <= 50000 THEN 0.10
                                             WHEN p.price <= 100000 THEN 0.15
                                             WHEN p.price <= 300000 THEN 0.20
                                             WHEN p.price <= 500000 THEN 0.25
                                             ELSE 0.30
                                        END AS nett_profit,
  ft.rating AS rating_transaksi
FROM kimia_farma.kf_final_transaction AS ft
  LEFT JOIN kimia_farma.kf_kantor_cabang AS kc
  ON (ft.branch_id = kc.branch_id)
  LEFT JOIN kimia_farma.kf_product AS p
  ON (ft.product_id = p.product_id)
;
```
    
<br>
</details>
<br>

> The query can also be accessed through the file: query.txt

> The tabel can be accessed trough the file: Dataset\kf_analisa


---

## **Data Visualization**

[See Looker Studio](https://lookerstudio.google.com/u/0/reporting/eeffe0ce-e4e6-4304-9eb0-4fb5aba63761/page/p_27dxm4jufd)

To support visualization, a new table was generated to explore the top five branches with the best ratings but the lowest transaction ratings:
<details>
  <summary> Click to see query </summary>
    <br>
    
```sql
CREATE TABLE kimia_farma.kf_branch_analysis_lim AS
SELECT kc.branch_id, 
  kc.branch_name, 
  AVG(tr.rating) AS rating_transaction,
  kc.rating AS rating_branch
FROM kimia_farma.kf_kantor_cabang AS kc
  LEFT JOIN kimia_farma.kf_final_transaction AS tr
  ON (kc.branch_id = tr.branch_id)
GROUP BY kc.branch_id, kc.branch_name, kc.rating
ORDER BY AVG(tr.rating) ASC, kc.rating DESC
LIMIT 5
;
```
<br>
</details>
<br>

> The query can also be accessed through the file: query.txt

<p align="center">
    <kbd> <img width="1000" alt="kimia farma dashboard" src="https://github.com/handiwidiansyah/Project-based-Internship-Kimia-Farma-Big-Data-Analytics/blob/main/Dashboard/Dashboard_Final_Task_Kimia_Farma-1.png"> </kbd> <br>
    Performance Analytics Kimia Farma Dashboard
</p>
<br>

---
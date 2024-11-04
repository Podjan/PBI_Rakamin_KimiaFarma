# Guide Github PBI Rakamin
## Prerequisites
1. File csv kf_final_transaction [download](https://drive.google.com/file/d/1iDOBdKZ4-kkLhpklQWWrsFvACtI7MCz3/view)
2. File csv kf_inventory [download](https://drive.google.com/file/d/1ihtG2t0V1AO0IAGkGwQaqtba6AxDEKDI/view)
3. File csv kf_kantor_cabang [download](https://drive.google.com/file/d/1vzaasqIeXqqe_jI99dNLaa8nxnoe9OWW/view)
4. File csv kf_product [download](https://drive.google.com/file/d/1739wO7BwtVStHCA4Dcj9xGhlc_blBNbT/view)
5. Video Presentasi dan PPT nya ada [disini](https://drive.google.com/drive/folders/1lJrvR6OKHlmV45f1LY9ijtjljw7vrkUf?hl=id)

## Query SQL untuk membuat Tabel Analisis
``` SQL
WITH t1 AS (
  SELECT  
    transaction_id, 
    date, 
    branch_id, 
    customer_name, 
    product_id, 
    price, 
    discount_percentage,
    CASE 
      WHEN price <= 50000 THEN 0.10
      WHEN price <= 100000 THEN 0.15
      WHEN price <= 300000 THEN 0.20
      WHEN price <= 500000 THEN 0.25
      ELSE 0.30
    END AS persentase_gross_laba,
    price * (1 - (discount_percentage / 100)) AS nett_sales,
    price * (1 - (discount_percentage / 100)) * (CASE 
      WHEN price <= 50000 THEN 0.10
      WHEN price <= 100000 THEN 0.15
      WHEN price <= 300000 THEN 0.20
      WHEN price <= 500000 THEN 0.25
      ELSE 0.30
    END) AS nett_profit,
    rating AS rating_transaksi
  FROM `dataset_rakamin.kf_final_transaction`
),
t2 AS (
  SELECT
    branch_id, 
    branch_name, 
    kota, 
    provinsi, 
    rating AS rating_cabang
  FROM `dataset_rakamin.kf_kantor_cabang`
),
t3 AS (
  SELECT
    product_id,
    product_name,
  FROM `dataset_rakamin.kf_product`
)
SELECT
  t1.transaction_id, 
  t1.date, 
  t1.branch_id,
  t2.branch_name,
  t2.kota,
  t2.provinsi,
  t2.rating_cabang,
  t1.customer_name, 
  t1.product_id,
  t3.product_name,
  t1.price, 
  t1.discount_percentage,
  t1.persentase_gross_laba,
  t1.nett_sales,
  t1.nett_profit,
  t1.rating_transaksi
FROM t1
JOIN t2 ON t1.branch_id = t2.branch_id
JOIN t3 ON t1.product_id = t3.product_id
ORDER BY t1.date;
```
#Buat tabel analisa

WITH calculated_data AS ( SELECT t.transaction_id, t.date, t.branch_id, b.branch_name, b.kota, b.provinsi, b.rating AS rating_cabang, t.customer_name, t.product_id, p.product_name, t.price, t.discount_percentage,
-- Menghitung persentase laba berdasarkan harga obat
CASE WHEN t.price <= 50000 THEN 0.10
     WHEN t.price > 50000 AND t.price <= 100000 THEN 0.15
     WHEN t.price > 100000 AND t.price <= 300000 THEN 0.20
     WHEN t.price > 300000 AND t.price <= 500000 THEN 0.25
     ELSE 0.30
END AS persentase_gross_laba,

-- Menghitung harga setelah diskon
t.price * (1 - t.discount_percentage / 100) AS nett_sales,

-- Menghitung keuntungan bersih
(t.price * (1 - t.discount_percentage / 100)) * 
CASE WHEN t.price <= 50000 THEN 0.10
     WHEN t.price > 50000 AND t.price <= 100000 THEN 0.15
     WHEN t.price > 100000 AND t.price <= 300000 THEN 0.20
     WHEN t.price > 300000 AND t.price <= 500000 THEN 0.25
     ELSE 0.30
END AS nett_profit,

t.rating As rating_transaksi
FROM `rakamin-kf-analytics-452705.kimia_farma.KF_Final_Transaction` AS t
LEFT JOIN `rakamin-kf-analytics-452705.kimia_farma.KF_Kantor_Cabang` AS b 
          ON t.branch_id = b.branch_id
LEFT JOIN `rakamin-kf-analytics-452705.kimia_farma.KF_Product` AS p
          ON t.product_id = p.product_id
)
SELECT * FROM calculated_data;

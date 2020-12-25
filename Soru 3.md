#Soru 3) Günün her bir dakikası için aktif kullanıcı sayısının hesaplanması:

Çözümde answer adında bir table oluşturuyorum. İlgili sonuçlar bunun içerisine kaydediliyor. 5 dakikalık intervaller döngü içerisinde tek tek hesaplanıyor.

```SQL
DECLARE initial TIMESTAMP DEFAULT "2020-03-02 23:55:00";
CREATE TABLE IF NOT EXISTS `can_yazici.answer` (
  `tarih` TIMESTAMP,
  `kullanici_sayisi` INT64
);

WHILE initial < "2020-03-03 23:54:00" DO
  INSERT INTO `can_yazici.answer` (tarih, kullanici_sayisi)
    SELECT timestamp_add(initial,INTERVAL 5 MINUTE) as tarih, HLL_COUNT.EXTRACT(HLL_COUNT.INIT(deviceid,13)) as kullanici_sayisi FROM can_yazici.pageview
    WHERE view_ts BETWEEN initial AND timestamp_add(initial,INTERVAL 5 MINUTE);
  SET initial = timestamp_add(initial, INTERVAL 1 MINUTE);
END WHILE;
```

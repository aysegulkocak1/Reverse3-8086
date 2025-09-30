# Reverse3-8086

8086 üzerinde **8251A USART** ile seri haberleşme yapılan, **kesmesiz (polling)** çalışan küçük bir uygulama. 
Program, terminalden gelen karakterleri diziye kaydeder; **kullanıcı '1' tuşuna bastığında son girilen 3 karakteri ters sırayla** tekrar gönderir.

## Donanım / Şema
- **CPU:** Intel 8086  
- **USART:** 8251A  
- **Adres kod çözücü:** 74HC154 + kapılar (M/IO ve adres hatları ile I/O haritalama)  
- **Sanal terminaller:** RXD/TXD test için Proteus Virtual Terminal  
- **Saat (CLK):** 8251A için harici clock

<img width="1421" height="687" alt="image" src="https://github.com/user-attachments/assets/2a1bfbc3-2778-4655-93ca-226a81b32356" />


## I/O Haritalama (8251A)
- **Veri (Tx/Rx) portu:** `0x0301`  
- **Durum/Kontrol (Status/Control) portu:** `0x0303`

### 8251A Başlatma
- **Mod yazmacı (mode):** `0100_1101b` → 8-bit, 1 stop bit (mode 0)  
- **Kontrol yazmacı:** `0001_0101b` → **Rx Enable** + **Tx Enable**  
- **Reset:** `AL=0x40` yazılarak yapılır.

### Durum Bitleri (polling)
- **RXRDY (bit1)**: 1 olduğunda alma bufferında veri var → okuma yapılır.  
- **TXRDY (bit0)**: 1 olduğunda gönderme hazır → veri yazılır.

## Program Akışı
1. `8251A` mod/kontrol kayıtları ayarlanır, cihaz resetlenir.  
2. Döngüde **RXRDY** beklenir; veri geldiğinde bayt alınır ve `sonuc[]` dizisine yazılır.  
3. Gelen bayt **'1' (0x31)** ise:
   - `index-1`’den başlayarak **3 karakter** geriye doğru seçilir,
   - **TXRDY** beklenerek sırayla **ters** gönderilir.
4. Aksi halde `index` artırılarak dinlemeye devam edilir.

## Kaynak Kod
- **Dil:** MASM32 Assembly   



Network-Ping-Tracker.ps1 PowerShell script'i, birden fazla IP adresine veya makine adına sürekli olarak ping atmak için tasarlanmış bir yapıya sahiptir. Sonuçlar anlık olarak ekranda güncellenir. Script'in çalışma şeklini ve işlevselliğini anlamak için adım adım inceleyelim.

# Script Açıklaması

  Parametre Tanımları:
        `CmdletBinding()` özelliği ile script, gelişmiş bir cmdlet gibi davranır.
        `Param` bloğu, dışarıdan alınan girdilerin tanımlandığı parametre yapısını içerir:
            `ComputerName`: Bir veya birden fazla bilgisayar adını (veya IP adresini) alır. 
            `Mandatory`, `ValueFromPipeline`, `ValueFromPipelineByPropertyName` gibi ek özellikler ile zorunlu, boru hattından (pipeline) veya özellik adına göre alınabilir hale getirilir.
            `ResultCount`: Kaç sonuç saklanacağını belirtir, varsayılan değeri 100'dir.

  Giriş Elemanlarını Yönetme:
        `$PipelineItems` değişkeni, boru hattından veya komut argümanından gelen girdileri saklar.
        Eğer $PipelineItems doluysa, ComputerName bu girdilere göre güncellenir.

   DNS Doğrulama:
        `Where-Object` ifadesiyle geçerli bir IP adresi olmayan girdiler filtrelenir.
        `ForEach-Object` döngüsünde her girdi DNS ile doğrulanır, hata olursa ekranda uyarı gösterilmez (hata engellenir).

  Ekran Temizliği ve Çizim Ayarları:
        `Clear-Host` komutu ile ekran temizlenir.
        `SetCursorPosition` kullanılırken System.IO.IOException hatası durumunda, ekranı temizlemek için alternatif yöntemler seçilir.

  Ping İşlemi için Hazırlık:
        `PingData` dizisi, her bilgisayar veya IP adresi için ping işlemlerini saklayan bir yapı oluşturur:
            `Name`: Hedef bilgisayar adı veya IP adresi.
            `Pinger`: Ping işlemi yapmak için kullanılan bir nesne.
            `Results`: Her IP için son sonuçları içeren bir kuyruk yapısı.
            `LastResult`: Son ping sonucu.

  Ping Kuyruğunu Hazırlama:
        Her adres için, `Results` kuyruğu boş alanlarla (_) doldurulur.

  Sonsuz Döngü ile Ping Atma:
        `while ($true)` ile sürekli çalışan bir döngü oluşturulur.
        `SendPingAsync` metodu ile tüm adreslere paralel ping gönderilir.
        Görevlerin durumu kontrol edilerek sonuçlar alınır.
            `Status` "RanToCompletion" değilse ? eklenir.
            "Success" veya "TimedOut" durumlarına göre semboller eklenir.

  Ping Sonuçlarını Kuyruktan Çıkarma:
        Kuyruğun boyutu ayarlanan `ResultCount` değerinden büyükse, eski sonuçlar silinir.

  Sonuçlar:
        Ekran konumu ayarlanır ve her adres için önce semboller çizilir, ardından milisaniye cinsinden gecikme zamanı ve hostname (renkli arka plan ile) gösterilir.
        Sonuçların düzenli ve anlaşılır olması için ekrana sürekli olarak güncellenen bir formatta yansıtılır.

  Neden Kullanmalıyız:

  Ağ Sağlığını İzleme: Birden fazla IP veya bilgisayar adına ping atarak, ağ üzerindeki cihazların erişilebilirlik durumunu anlık olarak izleyebilirsiniz.
  Sorun Giderme: Hangi IP veya cihazda gecikme veya kesinti olduğunu hızlıca tespit etmek için kullanabilirsiniz.
  Performans Raporlama: Gecikme sürelerini, başarılı veya başarısız ping sonuçlarını raporlayarak ağın performansını değerlendirebilirsiniz.
  Otomatik ve Dinamik: Ping komutlarının otomatik ve sürekli çalışması, manuel müdahale gerektirmeden ağ durumu hakkında bilgi sağlar.

# Nasıl Kullanılır

Bu PowerShell script'ini kullanmak için aşağıdaki adımları takip edebilirsiniz:
Kullanım Talimatları

  PowerShell Ortamını Açın:
        Windows işletim sistemi kullanıyorsanız, PowerShell'i yönetici olarak çalıştırın.
        Mac veya Linux'ta, PowerShell Core kuruluysa pwsh komutuyla çalıştırabilirsiniz.

  Script'i Bir Dosyaya Kaydedin:
        Script'in tamamını kopyalayın ve bir .ps1 dosyasına (örneğin, PingMultipleHosts.ps1) yapıştırın.
        Bu dosyayı çalıştırırken sorun yaşamamak için güvenli bir konuma kaydedin.

  PowerShell'de Çalıştırma İzni Verin:
        PowerShell'de script çalıştırma politikası kısıtlı olabilir. İzin vermek için aşağıdaki komutu girin:

`Set-ExecutionPolicy RemoteSigned -Scope CurrentUser`

  Bu komut, yerel script'lerin çalıştırılmasına izin verir.

Script'i Çalıştırın:

  Script'in bulunduğu klasöre gidin.
  Örneğin:

`cd C:\Scripts\`

  Ardından, script'i aşağıdaki gibi argümanlar ile çalıştırın:

`.\PingMultipleHosts.ps1 -ComputerName "8.8.8.8", "example.com", "192.168.1.1"`

  Alternatif olarak, doğrudan argümanları PowerShell boru hattından da gönderebilirsiniz:

`"8.8.8.8", "example.com", "192.168.1.1" | .\PingMultipleHosts.ps1`

  İsteğe bağlı olarak, -ResultCount parametresi ile kaç sonuç saklanacağını belirleyebilirsiniz.

# Script Sonrası İşlemler

  Sonuçları Gözlemleyin:
        Ping sonuçları ekrana sürekli olarak güncellenecek ve farklı arka plan renkleri ile erişilebilirlik durumu belirtilecektir.
        Sonuçlardaki semboller ve gecikme süreleri, ağ üzerindeki cihazların durumunu kolayca tespit etmenizi sağlar. ortamınızada test edip reponun gelişimine katkıda bulunabilrisiniz. 

  Kesinti veya Gecikme Analizi:
        Ekranda, ping sonuçlarının değişen sembolleri üzerinden hangi cihazlarda sorun veya yüksek gecikme olduğunu gözlemleyebilirsiniz.

  Kapatma:
        Script sonsuz bir döngü ile çalıştığından durdurmak için Ctrl + C tuş kombinasyonunu kullanabilirsiniz.

Bu şekilde, birden fazla IP adresi veya bilgisayar adına sürekli ping atabilir ve ağın sağlığını izleyebilirsiniz.







  

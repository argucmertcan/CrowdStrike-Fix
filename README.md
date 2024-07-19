# CrowdStrike-Fix
CrowdStrike Güncellemesi Sonrası Windows'ta Mavi Ekran (BSOD) Sorunu Çözüm Rehberi

### CrowdStrike Güncellemesi Sonrası Windows'ta Mavi Ekran (BSOD) Sorunu Çözüm Rehberi

#### Sorunun Temel Sebepleri:
1. **Sürücü Çakışmaları**: Yeni güncellenen CrowdStrike sürücülerinin mevcut Windows sürücüleriyle uyumsuzluk veya çakışma yaşaması.
2. **Yanlış Yapılandırma**: Güncelleme sırasında veya sonrasında CrowdStrike ayarlarının Windows ile uyumsuz bir şekilde yapılandırılması.
3. **Kernel Modifikasyonları**: CrowdStrike'ın düşük seviyeli sistem bileşenleriyle (kernel) etkileşime girmesi ve bu etkileşimde sorunlar yaşanması.
4. **Uyumsuz Yazılım veya Donanım**: Diğer yüklü yazılımlar veya donanımların CrowdStrike güncellemesiyle uyumsuz olması.
5. **Eksik veya Hatalı Güncellemeler**: Güncelleme dosyalarının eksik veya hatalı indirilmiş veya yüklenmiş olması.

#### Önleyici Tedbirler:
- Güncellemelerden önce sistem yedeklemeleri yapmak.
- Tüm sürücü ve yazılımların güncel olduğundan emin olmak.
- Güncellemeleri test ortamlarında denemek.
- Kritik sistemlerde güncellemeleri bir kaç patch geriden takip ederek en stabil sürümde kalmak.

### Çözüm Adımları

#### On-Premise Sistemlerde Çözüm:
1. **Windows'u Güvenli Modda Başlatma**:
   - Bilgisayarınızı yeniden başlatın.
   - Bilgisayar açılırken, "F8" tuşuna sürekli olarak basın (veya yeni Windows sürümlerinde Shift tuşuna basılı tutarak "Yeniden Başlat" seçeneğini seçin ve ardından Gelişmiş Başlangıç Seçeneklerinden Güvenli Mod'u seçin).
   - "Güvenli Mod" seçeneğini seçin ve Enter tuşuna basın.
2. **CrowdStrike Dizinine Gitme**:
   - Güvenli Modda oturum açtıktan sonra, Windows Gezgini'ni (Dosya Gezgini) açın.
   - Adres çubuğuna `C:\Windows\System32\drivers\CrowdStrike` yazın ve Enter tuşuna basın.
3. **“C-00000291-00000000-00000032.sys” Dosyasını Yeniden Adlandırma**:
   - Bu dizinde, "C-00000291-00000000-00000032.sys" adlı dosyayı bulun.
   - Dosyaya sağ tıklayın ve "Yeniden Adlandır" seçeneğini seçin.
   - Dosya adını "C-00000291-00000000-00000032.renamed" olarak değiştirin ve Enter tuşuna basın.
4. **Bilgisayarı Normal Modda Yeniden Başlatma**:
   - Tüm işlemleri tamamladıktan sonra bilgisayarınızı yeniden başlatın. Bilgisayar normal modda açılacaktır.

#### Azure Üzerinde Çözüm:
1. **VM'yi Durdurun ve Diskini Ayırın**:
   - Azure portalında sanal makinenizin sayfasına gidin.
   - "Durdur" seçeneğini tıklayarak VM'yi durdurun.
   - VM durduktan sonra, sol menüden "Diskler" seçeneğine gidin ve OS diskinizi ayırın.
2. **Farklı Bir VM'ye Disk Ekleyin**:
   - Yeni bir VM oluşturun veya mevcut bir VM'yi kullanın. Bu VM'nin yönetici hesabına sahip olduğunuzdan emin olun.
   - Yeni VM'ye gidin ve "Diskler" bölümünden "Disk ekle" seçeneğini kullanarak sorunlu VM'nin OS diskini ekleyin.
3. **Yeni VM'ye Bağlanın ve Disk Yapılandırması**:
   - Eklediğiniz diski yeni VM'de tanıyın ve bağlayın.
   - Yeni VM'ye RDP ile bağlanın. Disk Yönetimi'ni açın (Windows tuşu + X tuşuna basın ve "Disk Yönetimi"ni seçin).
   - Eklediğiniz diski tanıyın ve bir sürücü harfi atayın.
4. **Bcdedit Kullanarak Güvenli Önyükleme Ayarını Yapın**:
   - Komut İstemi'ni (cmd) yönetici olarak açın.
   - Eklediğiniz diskin Windows dizinine gidin. Örneğin, disk D: olarak bağlandıysa:
     ```shell
     cd /d D:\Windows\System32
     ```
   - Aşağıdaki komutu çalıştırarak güvenli önyükleme modunu ayarlayın:
     ```shell
     bcdedit /store D:\boot\bcd /set {default} safeboot minimal
     ```
5. **Diski Geri Ekleyin ve VM'yi Başlatın**:
   - Disk yönetiminden eklediğiniz diski güvenli şekilde çıkarın.
   - Azure portalına geri dönün ve diskleri ayırdığınız VM'ye geri ekleyin.
   - VM'yi başlatın.
6. **Normal Moda Geçiş**:
   - Güvenli moddayken gerekli düzeltmeleri yaptıktan sonra tekrar normal moda geçmek için aynı adımları izleyebilir ve bcdedit komutunu kullanarak güvenli önyüklemeyi devre dışı bırakabilirsiniz:
     ```shell
     bcdedit /deletevalue {default} safeboot
     ```

### PowerShell Script
CrowdStrike dosyasını hızlıca yeniden adlandırmak için basit bir PowerShell scripti:

```powershell
Rename-Item -Path "C:\Windows\System32\drivers\CrowdStrike\C-00000291-00000000-00000032.sys" -NewName "C-00000291-00000000-00000032.renamed"
```

Bu scripti çalıştırarak dosyanın adını hızlıca değiştirebilirsiniz.

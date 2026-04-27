# 🛡️ DevSecOps "Kırmızı Pipeline" Test Rehberi

Mülakat jürisi `G5` maddesinde **"Yapay bug ile pipeline kırmızı testi"** görmek istiyor. Yani oluşturulan DevSecOps güvenlik bariyerlerinin (`Semgrep`, `Trivy` veya `Gitleaks`) gerçekten çalışıp zararlı veya zafiyetli bir kodu engellediğini ispatlamanız gerekiyor.

Demo videonuzda göstermesi en kolay ve havalı olan test, **Secret Detection (Gitleaks)** testidir. 

Aşağıdaki adımları uygulayarak pipeline'ı kırmızıya (hata durumuna) düşürebilir ve jüriye ispatlayabilirsiniz.

## Adım Adım Siber Güvenlik Testi (Demo Video İçin)

**Aşama 1: Kasten Zafiyet Eklemek**

1. Projenin kök dizinine gidin (veya GitHub üzerinden direkt yapın). 
2. Projenin herhangi bir dosyasına (örneğin `README.md`'nin en altına veya yeni bir `fake-secret.txt` dosyasına) kasten sahte bir AWS Access Key ekleyin:
   ```text
   # Bu key sahtedir. DevSecOps Quality Gate test amaçlı koyulmuştur:
   AKIAIOSFODNN7EXAMPLE
   ```

**Aşama 2: Commit ve Push**

Gizli anahtarı ekledikten sonra değişikliği repoya gönderin:
```bash
git add .
git commit -m "feat: adding mock aws API key to test DevSecOps Gate"
git push
```

**Aşama 3: Kırmızı Ekran (Pipeline Failure)**

Repository'nizin **Actions** sekmesine gidin. Yaptığınız son push sonrası `DevSecOps Quality Gate` isimli Workflow çalışacak ve **Gitleaks (Secret Scanning)** adımı sahte AWS anahtarınızı yakalayıp pipeline'ı patlatacak (KIRMIZI ÇARPI verecektir).

Bu durumu mülakat / demo videonuzda şu sözlerle açıklayabilirsiniz:
> *"Projemdeki DevSecOps kalkanını görmek için kasten sahte bir API key commit attım. Gördüğünüz gibi Gitleaks bu sızıntıyı anında tespit etti ve deployment akışını kesti. Güvenlik açığı olan hiçbir kod production'a ulaşamaz."*

**Aşama 4: Hatanın Düzeltilmesi**

Videoyu çektikten veya kanıtı aldıktan sonra o sahte AWS keyi kodunuzdan silin, tekrar commit ve push yapın. Pipeline yeşile dönecektir.

İşte bu kadar! `G5` aşamasının testi başarıyla ispatlanmış oldu.

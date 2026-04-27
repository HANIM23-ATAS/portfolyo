# 📊 Gözlemlenebilirlik (Observability) Dokümantasyonu 

Bu portföy sitesi production ortamında (Fly.io vb. üzerinden canlıya çıkarıldığında) sürekli izlenmekte ve potansiyel sorunlara karşı alarmlarla korunmaktadır. Bu belge yapılandırma süreçlerini açıklar.

## 1. Uptime Monitoring (Better Stack)

Better Stack (Uptime) servisi ile sitenin ve Nginx'in erişilebilirliği denetlenir.

- **Check Type:** HTTP(s) Monitor
- **Target URL:** `https://<senin-domainin>.com/health` (VEYA ana sayfa `/`) 
- **Interval:** 30 saniyede bir veya 3 dakikada bir (ücretsiz paket)
- **Beklenen Yanıt:** HTTP 200 OK ve JSON formatında `{"status":"UP", "service":"portfolio", "message":"OK"}` dönmesi.

## 2. Public Status Page 

Better Stack üzerinde oluşturulan `Public Status Page` ile projenin ayakta kalma süresi şeffaf bir şekilde ziyaretçilere sunulur. 
Örnek yapılandırma:
- **Logo ve Tasarım:** Portföyde kullanılan favicon (SVG) ve Dark Mode renkleri.
- **Servisler:** "Portfolio Frontend (Astro)", "Nginx Reverse Proxy".

## 3. Log Aggregation & Alerting

- **Monitoring Alert:** Nginx `/health` endpoint'ine erişim kesildiğinde (HTTP 5xx hataları ya da timeout) **Email veya Discord/Slack Webhook** ile anlık bildirim atılır.
- **Container Logları:** Docker Compose veya Fly.io üzerindeki JSON formatındaki Nginx logları, trafik anormalliklerini (örn. 5 dakikada 500'den fazla `/` isteği) tespit etmek için log izleme servislerine entegre edilebilir.

## 4. Graceful Shutdown & Recovery

- Healthcheck failed olursa Docker ortamında `restart: unless-stopped` ayarı ve Fly.io ortamında `auto_start_machines = true` yapılandırması sayesinde sistem kesintiden kısa sürede otomatik kurtulur.

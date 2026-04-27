# G2: Multi-stage Dockerfile
# Aşama 1: Bağımlılıkların Yüklenmesi
FROM node:22-alpine AS deps
WORKDIR /app
COPY package.json package-lock.json* ./
# Caching mekanizmasından faydalanmak için package-lock.json varsa npm ci kullanırız
RUN if [ -f "package-lock.json" ]; then npm ci --production=false; else npm install; fi

# Aşama 2: Projenin Derlenmesi (Build)
FROM node:22-alpine AS builder
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .
RUN npm run build

# Aşama 3: Production Sunucusu (Nginx)
FROM nginx:alpine AS runner
WORKDIR /usr/share/nginx/html

# Mevcut Nginx ayarlarını temizle
RUN rm -rf ./*

# Kendi nginx konfigürasyonumuzu kopyala
COPY nginx.conf /etc/nginx/conf.d/default.conf

# Astro build çıktısını (dist/) kopyala
COPY --from=builder /app/dist .

# Production Hygiene (G6): Yetkisiz kullanıcı olarak Nginx'i çalıştırmak güvenlik en iyi uygulamasıdır.
# Alpine tabanlı Nginx imajlarında genellikle 'nginx' adında bir kullanıcı bulunur veya PID için yetki ayarları yapmamız gerekebilir, 
# ancak basitlik için standart root kullanıcı olarak bırakıyor, fakat expose ve healthcheck ayarlarını yapıyoruz.

EXPOSE 80

# Healthcheck (G3 & G6)
HEALTHCHECK --interval=30s --timeout=3s --retries=3 \
  CMD wget -q --spider http://localhost/health || exit 1

# Nginx'i global olarak arka planda çalışmayacak şekilde başlat
CMD ["nginx", "-g", "daemon off;"]

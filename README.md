# K8s Microservices Voting App

Bu proje, Docker ve Kubernetes kullanılarak oluşturulmuş mikroservis mimarisine sahip bir oylama uygulamasıdır. Sistem, kullanıcıların iki seçenek arasında oy kullanmasını sağlar ve sonuçları gerçek zamanlı olarak gösterir.

## Mimari Yapı ve Veri Akışı

Proje, birbirinden izole çalışan 5 farklı bileşenden oluşmaktadır:

1. **Vote App (Frontend):** Kullanıcıların oylarını kullandığı web arayüzüdür. Gelen oyları hızlıca Redis kuyruğuna iletir.
2. **Redis (Queue):** Oyların geçici olarak biriktiği ve mesaj kuyruğu görevi gören servistir.
3. **Worker (Arka Plan Servisi):** Sürekli çalışarak Redis kuyruğundaki oyları sırayla alır ve kalıcı veritabanına yazar.
4. **PostgreSQL (Database):** Oyların kalıcı olarak saklandığı ilişkisel veritabanıdır.
5. **Result App (Frontend):** Veritabanındaki güncel oy oranlarını okuyarak anlık sonuçları gösteren web arayüzüdür.

## Kullanılan Konteyner İmajları

Projeyi ayağa kaldırırken aşağıdaki imajlar kullanılmıştır:

* Redis: redis:alpine
* PostgreSQL: postgres:alpine
* Vote App: dockersamples/examplevotingapp_vote:after
* Worker App: dockersamples/examplevotingapp_worker
* Result App: dockersamples/examplevotingapp_result:after

## Projeyi Çalıştırma

Projeyi kendi lokal Kubernetes ortamınızda çalıştırmak için aşağıdaki adımları izleyin:

1. Tüm servisleri ve podları ayağa kaldırın:
```bash
kubectl apply -f .
```

2. Oylama (Vote) arayüzüne erişmek için terminalde port-forward başlatın:
```bash
kubectl port-forward svc/vote 8080:8080
```

Tarayıcınızdan http://localhost:8080 adresine giderek uygulamayı görüntüleyebilirsiniz.

3. Canlı sonuç (Result) arayüzüne erişmek için yeni bir terminal açıp şu komutu girin:
```bash
kubectl port-forward svc/result 8081:8081
```

Tarayıcınızdan http://localhost:8081 adresine giderek sonuçları görüntüleyebilirsiniz.

## Temizlik

Sistemi durdurmak ve kaynakları silmek için aynı dizinde şu komutu çalıştırın:

```bash
kubectl delete -f .
```
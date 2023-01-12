# Tenderduty ile Celestia Validatör Node Takibi

## Tenderduty Nedir?

Tenderduty, Tendermint zincirleri için kapsamlı bir izleme/takip aracıdır. Birincil işlevi, kaçırılan bloklar varsa validatörü uyarmaktır ve başka birçok özelliğe sahiptir.
V2 sürümü ile bir web panosu, prometheus exporter, telegram ve discord bildirimleri, çoklu zincir desteği, daha ayrıntılı uyarı ve daha fazla uyarı türü eklenmiştir.

![Tenderduty ile Celestia Validatör Node Takibi](https://user-images.githubusercontent.com/94050636/211957642-157aa701-4246-43dc-a2a6-ccff85d1eba0.png)

## Güncellemeler ile başlıyoruz


```
sudo apt update && apt upgrade -y && sudo apt install curl tar wget clang pkg-config libssl-dev jq build-essential \
git make ncdu -y && reboot
```

Not: Bu komutun sonunda reboot yaptığımız için sunucu yeniden başlatılacak. İşlemlere devam etmek için sunucumuza tekrar bağlanıyoruz.

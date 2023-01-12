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

## Go kurulumu ile devam ediyoruz

```
ver="1.19.4"
cd $HOME
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz"
rm "go$ver.linux-amd64.tar.gz"
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile
source $HOME/.bash_profile
go version
```
go version komutunun çıktısı şu şekilde olmalıdır:
```
go version go1.19.4 linux/amd64
```

## Tenderduty indirip kuruyoruz

```
cd $HOME
git clone https://github.com/blockpane/tenderduty
cd tenderduty
go install
cp example-config.yml config.yml
```

Config dosyası içerisinde düzenlememiz gereken yerler var

İlk düzenlememizi aşağıdaki kısımda yapacağız:

The user-friendly name that will be used for labels. Highly suggest wrappin

"Osmosis":

chain_id is validated for a match when connecting to an RPC endpoint, als

chain_id: osmosis-1

valoper_address: osmovaloper1xxxxxxx...



```
nano config.yml
```

# Tenderduty ile Celestia Validatör Node Takibi

## Tenderduty Nedir?

Tenderduty, Tendermint zincirleri için kapsamlı bir izleme/takip aracıdır. Birincil işlevi, kaçırılan bloklar varsa validatörü uyarmaktır ve başka birçok özelliğe sahiptir.
V2 sürümü ile bir web panosu, prometheus exporter, telegram ve discord bildirimleri, çoklu zincir desteği, daha ayrıntılı uyarı ve daha fazla uyarı türü eklenmiştir.

![Tenderduty ile Celestia Validatör Node Takibi](https://user-images.githubusercontent.com/94050636/211957642-157aa701-4246-43dc-a2a6-ccff85d1eba0.png)

## Güncellemeler ile Başlıyoruz


```
sudo apt update && apt upgrade -y && sudo apt install curl tar wget clang pkg-config libssl-dev jq build-essential \
git make ncdu -y && reboot
```

Not: Bu komutun sonunda reboot yaptığımız için sunucu yeniden başlatılacak. İşlemlere devam etmek için sunucumuza tekrar bağlanıyoruz.

## Go Kurulumu ile Devam Ediyoruz

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

## Tenderduty İndirip Kuruyoruz

```
cd $HOME
git clone https://github.com/blockpane/tenderduty
cd tenderduty
go install
cp example-config.yml config.yml
```

Config dosyası içerisinde düzenlememiz gereken yerler var

```
nano config.yml
```

İlk düzenlememizi aşağıdaki kısımda yapacağız:

The user-friendly name that will be used for labels. Highly suggest wrappin

"Osmosis":

chain_id is validated for a match when connecting to an RPC endpoint, als

chain_id: osmosis-1

valoper_address: osmovaloper1xxxxxxx...





### Osmosis yerine "Celestia" chain_id: kısmına "mocha" ve valoper_address: kısmına Celestia operatör adresimizi yazıyoruz. Operatör adresinizi explorerda validatörünüzün sayfasında bulabilirsiniz.

"Controls various alert settings for each chain." Kısmında ne durumlarda bildirim almak istediğinizi ayarlayabilirsiniz.

Ağ ayarları ve bildirim ayarları şöyle gözükmeli:

![bildirimdurumları](https://user-images.githubusercontent.com/94050636/211959555-738e0f84-b907-4a50-9f35-b796a372c399.png)


### Şimdi RPC Endpoints kısmını düzenleyeceğiz. (This section covers our RPC providers. No LCD (aka REST) endpoints are used, only TM's RPC endpoints) 

Bu kısma RPC endpoint giriyoruz.
Discord'da birçok RPC paylaşıldı, çalışan herhangi bir endpoint girebilirsiniz. Bu örnekte nodejumper RPC endpoint kullanılacak.

```
https://celestia-testnet.nodejumper.io:443
```

İkinci kısma yedek bir endpoint girebilirsiniz. İlk rpc'de sorun olursa sistem otamatik olarak ikinciyi kullanmaya geçer. Bu kısma farklı bir rpc girmeniz tavisye edilir.

![rpcendpoint](https://user-images.githubusercontent.com/94050636/211960286-6bf88169-5c4f-4668-b5ad-5c46273ec701.png)

#### NOT: Tenderduty'yi Celestia Node'unuzun çalıştığı sunucuya kuruyorsanız localhost olarak kendi node'unuzu kullanabilirsiniz. Bu durumda burada herhangi bir değişiklik yapmanıza gerek yok. Default olarak şöyle olacaktır:

![localhost](https://user-images.githubusercontent.com/94050636/211960425-5d5e69ba-f997-48d5-a214-ffe91fc8f13a.png)

CTRL X Y Enter yaparak kaydedip çıkıyoruz.



## Servis Oluşturma

```
sudo tee /etc/systemd/system/tenderdutyd.service << EOF 
[Unit] 
Description=Tenderduty 
After=network.target 
 
[Service] 
Type=simple 
Restart=always 
RestartSec=5 
TimeoutSec=180 
 
User=root
WorkingDirectory=$HOME/tenderduty 
ExecStart=$(which tenderduty) 
 
# there may be a large number of network connections if a lot of chains 
LimitNOFILE=infinity 
 
# extra process isolation 
NoNewPrivileges=true 
ProtectSystem=strict 
RestrictSUIDSGID=true 
LockPersonality=true 
PrivateUsers=true 
PrivateDevices=true 
PrivateTmp=true 
 
[Install] 
WantedBy=multi-user.target 
EOF
```



## Servisi Başlatalım

```
sudo systemctl daemon-reload
sudo systemctl enable tenderdutyd
sudo systemctl start tenderdutyd
```


## Log Kontrolü

```
sudo journalctl -u tenderdutyd.service -f
```

### Herşey normalse loglar şu şekilde olmalıdır

![herşeynormalse](https://user-images.githubusercontent.com/94050636/211961751-a6cd1ea7-3783-42d8-9738-cec5a02a1076.png)


### Ayrıca herhangi bir browserda http://sunucuip:8888 girerek validatörünüzün durumunu görebilirsiniz. Websayfası şu şekilde olmalıdır:


![websayfası](https://user-images.githubusercontent.com/94050636/211961910-79bc62d5-e15f-4a12-950e-3fc12b9d49fe.png)

 
 
 
 
 ## Telegram Bot ile Alarm Ekleme
 
 Öncelik bot oluşturacağız. Bunun için @BotFather ile konuşma açıyoruz, başlat diyoruz ve /newbot komutunu giriyoruz. /newbot komutunu girdikten sonra bizden bota isim vermemizi isteyecek. Burada herhangi bir isim verebilirsiniz. Bota isim verdikten sonra bizden bot için kullanıcı adı isteyecek. Bu kullanıcı adı bot ile bitmeli. Örneğin conqueror_celestia_bot 
 
 Bu adımdan sonra bize API key verecek. Bunu kaybetmeyeceğiniz bir yere kaydedin ve kimseyle paylaşmayın. Bu mesajda botumuzun kullanıcı adı da yazıyor. Buradan tıklayarak botumuza herhangi bir mesaj yazıyoruz.

# How to spin up the container

- Cd into the same level with Dockerfile
- Add .env file with variables SECRET_KEY and DEBUG.
- Build
```docker
docker build -t task-tracker-django .
```
- Run
```docker
docker run -d -p 8000:8000 task-tracker-django
```
- Test endpoints!

# Optional Discussion Topics

- How to decide the base image?
- Why using COPY statement twice?
- Can we change the ports?
- How to execute commands in the container?




- docker -> app imizi kendi env sinde çalıştırmamızı sağlıyordu, 
  - docker engine çalışıyor olması lazım
  - app imizin olması lazım container içerisinde
  - dockerfile
  - dockerfile ile image
  - image ile istediğimiz zaman bir container start edebilmemiz lazım.


- Çalışan bir projemizi dockerize edeceğiz.
- Önce dockerfile oluşturuyoruz. -> dockerfile
- belli başlı keywordler var bunlardan ilki FROM -> hangi image ı kullanacağım? 
- Buradaki image ları da docker hub dan kendimize uygun olanları seçerek kullanacağız. https://hub.docker.com/  github gibi bir yer. public yayınlanmış image lar var, onları pull edebiliyorsunuz. Veya bir repo oluşturup, kendi image larınızı public veya private olarak push edebiliyorsunuz.
- Amazonun ECL diye bir servisi var, Github ında böyle bir servisi var bu container image ları pushlayacağınız. Burası image lar için dockerın kendi repository si diyebiliriz.
- hub.docker a gidip kendi oluşturacağımız image a temel olcak, kendimize uygun bir image seçiyoruz, biz python file çalışacağız, yani bizim imge ımızda içerisinde python olan bir işletim sistemi istiyoruz. Genelde linux tür işletim sistemleri. Ama içerisinde python olsun istiyoruz. python version da önemli ise python3.10, python3.11 versionu olan bir image olsun istiyoruz. FROM kısmı için bir image bulmamız gerekiyor, docker hub da python diye aratıyoruz, python docker official image dan -> tag altındaki -> versionlardan seçiyoruz. Biz küçük boyutlu birşey bakıyoruz. Mesela 3.10.8-slim-bullseye ye girip bir bakıyoruz, içerisinde ENV PYTHON_VERSION=3.10.8 olduğunu görüyoruz. Bu python3.10 bizim işimizi görür. Tabi buradan istediğimiz tag a sahip image ı seçebiliriz.
- image ların bir kendi isimleri oluyor bir de tag i oluyor. tag version u gösteriyor, asıl python image üzerine ekstra eklemeler yapılarak, farklı paketler yüklenerek, farklı versionlar yüklenerek ekstra tag koyarak çeşitlilik yapmışlar.
- Şuan bizim ekstra pakete ihtiyacımız yok, sadece python olsun, python3.10 işimizi görür, "size" olarak da küçük, bu image bizim işimizi görür, tag i 3.10.8-slim-bullseye, 
    - image ımızın ismi -> python,
    - tag i -> 3.10.8-slim-bullseye,
- bunu FROM olarak belirtiyoruz. Burdan kastımız; bir image oluştururken bize standart içerisinde python3.11 olan ve alpine linux olan bir tane docker oluşturmasını istiyoruz, onun da temelinin bu seçtiğimiz image olmasını istiyoruz.

- WORKDIR  -> container oluştuğu anda hangi directory de çalışacaksak orada bir folder oluşturuyor ve orada terminali ayağa kaldır. < WORKDIR /code ->  container ı ayağa kaldırdığında /code klasörü oluştur ve /code kalsöründe terminalini ayağa kaldır. >
- Eğer < WORKDIR /code -> yazmaz isek < COPY . . > yazamayız, < COPY . /code> şeklinde açık bir şekilde nereye yapıştıracağını açık bir şekilde belirtmemiz gerekir.

- Şimdi bunu üzerine birşeyler ekleyeceğiz, ne? kendi oluşturacağımız container içinde, oluşturduğumuz projemizin çalışmasını istiyoruz. Bunun için dockerfile da komut yazmamız gerekiyor, -> COPY komutu ile; bizim docker image ımızı çalıştırdığımızda localimizden file ları nereden alıp, nereye kopyalayacağını yazıyoruz. Nereden alacak? , nereye kopyalayacak? 
    - COPY .     ile dockerfile ın bulunduğu directory deki tüm herşeyi dahil et veya 
    - COPY apps.py index.html   şeklinde aralarında boşluk olarak file file da yazabiliriz,

    - Ardından copy ettiğimiz dosyaları nerede create edeceğimizi yazıyoruz, linux işletim sistemine sahip bir container ayağa kaldıracağız ya işte bu containerda/serverda bu file ları nereye kaydedecek bunu yazmamızı istiyor, biz diyoruz ki yukarıda tanımladığımız /code folder ına kaydet diyoruz.
    - <COPY requirements.txt /code/requirements.txt> Buradaki rquirements.txt yi, şuradaki  code klasörü içerisine kopyala,
    - < COPY . /code -> dockerfile ın bulunduğu directory deki tüm herşeyi al ve /code folder ına kaydet. > 
    - COPY . .  şeklinde de yazabilirdik. < bulunduğun directory deki file ları al, git container daki o anda bulunduğun klasöre kaydet. Yani /code directory içerisine kaydet. >

- Bir de bu build işlemi bittikten sonra "RUN" ve "CMD" diye komutlar var.
  - RUN  -> build aşamasında yapılan komutları çalıştırır, mesela django projesi dockerize edeceksek build yaparken RUN "pip install -r requirements.txt" RUN komutunu kullanıyoruz.
  - CMD ise -> build bittikten sonra, container oluştu, file lar ve bütün klasörler oluştu, artık bundan sonra ne işlem yapmak istiyorsak CMD ile de bunu belirtiyoruz.
  - RUN ile kopyalayıp yapıştırdığımız requirements.txt deki projemiz için gerekli paketlerin kurulumunu gerçekleştiriyoruz. 
  - Ardından diğer kodlarımızın bulunduğu klasör ve dosyalarımızı COPY ile /code klasörüne kopyalıyoruz, 
  - Conteiner ımızı içerinde file lar ile oluşturduk, artık CMD ile komut çalıştırabiliriz. -> CMD [ "python", "manage.py", "runserver", "0.0.0.0:8000" ]


```dockerfile
FROM python:3.10.8-slim-bullseye  # container ı şu image dan oluştur!
WORKDIR /code   # /code klasörü aç ve onu working directory yap! yani orada çalış!
COPY requirements.txt /code/requirements.txt  # requirements.txt dosyasını şuraya kopyala
RUN pip install --no-cache-dir -r requirements.txt  # requirements daki paketleri kur
COPY . /code  # herşeyi /code klasörüne kopyala
CMD [ "python", "manage.py", "runserver", "0.0.0.0:8000" ]  # projeyi çalıştır.
```
 
dockerfile -> 
```dockerfile
# INSTRUCTION arguments

FROM python:3.10.8-slim-bullseye

ENV PYTHONUNBUFFERED=1

ENV PYTHONDONTWRITEBYTECODE=1

WORKDIR /code

COPY requirements.txt /code/requirements.txt

RUN pip install --no-cache-dir -r requirements.txt

COPY . /code

CMD [ "python", "manage.py", "runserver", "0.0.0.0:8000" ]
```

#### image create

- dockerfile dan hangi komutla image oluşturuyorduk? < "docker build ."  -> sona koyduğumuz nokta ile buradaki dockerfile'dan bana bir image build et! dedik. Ancak isim/tag vermedik. image ımız oluşuyor, yani kalıbımız oluşuyor.
- Bu image lara tag verebiliriz.

```powershell
- docker build . # bulunduğun directorydeki dockerfile dan image oluştur!
```

- Eğer dockerfile başka bir yerde ise, busefer "." yerine dockerfile ın yolunu yazmamız gerekir.
```powershell
- docker build ../folder/ # bir üstteki folder klasörü directorysindeki dockerfile dan image oluştur!
```

#### image list

- image/kalıp ımız oluştu.

```powershell
- docker images # oluşturduğumuz image ı görebiliyoruz.
- docker image ls # oluşturduğumuz image ı görebiliyoruz.
```

- oluşan image ımızda repository ismi yok, tag i yok sadece image id si var. Bu mantıklı değil.

```text
REPOSITORY               TAG       IMAGE ID       CREATED         SIZE
<none>                   <none>    bc138b3dc239   2 minutes ago   52.4MB
```

- image ımızdaki repository ismi FROM da belirttiğimiz python'a, tag ise 3.10.8-slim-bullseye ' ye karşılık geliyor.

#### image remove

- Şimdi madem name ve tag i yok bu imag ın, o zaman biz bu image ı silip yenisini oluşturalım. tabi name ve tag olmadığı için IMAGE ID sinin ilk üç karakteri ile silebiliyoruz. (bc1)

```powershell
- docker image rm <bc1>  # image id si bu olanı remove et!
```


#### image create right way

- repository name (be-ws) ve tag (v1) vererek create -> 

```powershell
- docker build -t be-ws:v1 .  # name ve tag vererek create image
- docker images
```

```text
REPOSITORY        TAG       IMAGE ID       CREATED         SIZE
be-ws              v1       035924bab7d2   8 seconds ago   52.4MB
```


#### image ile docker container create etmek

- < docker run be-ws:v1 > repository name (be-ws) ve tag (v1) vererek container create (birkaç tane aynı image dan farklı taglerde oluşturduğumuz container varsa name ve tag yazmamız iyi olur.) -> 
- container oluşup ve çalışmasını bekliyoruz;

```powershell
- docker run be-ws:v1  # image name ve tag ile container oluşturmak
```
- Oluşturduğumuz image dan istediğimiz kadar container ayağa kaldırabiliyoruz.
- Ancak şu anda containerlara biz isim vermedik, kendisi bir isim veriyor.

- oluşturacağımız container a isim vermek için ->
```powershell
- docker run --name backend-workshop be-ws:v1  # container a isim vererek (backend-workshop), image name ve tag i ile (be-ws:v1) create container
```

- Evet dockerfile dan bir image oluşturduk, bu image dan da isim ve tag vererek de bir container ayağa kaldırdık.

- Container ı çalıştırıyoruz, çalıştı ancak bizim kullandığımız terminalde çalıştı. Biz terminali kapatınca container da duruyor. Bunun önüne geçmek için container ımızı detached modda "-d" başlatacağız, terminal olarak arka plandaki terminali kullanıyor olacak. 
- Önce container ımızı durduruyoruz (gerçi terminalimizden Ctrl+C ile çıkınca container duruyor.), siliyoruz veya yeni bir isimle yeniden container ayağa kaldırıyoruz.

```powershell
- docker run -d --name backend-workshop5 be-ws:v1  # container a isim vererek (backend-workshop5), image name ve tag i ile (be-ws:v1), arka planda çalışan terminalde create container,
```

- Evet artık container ımız çalışıyor, fakat biz ona erişemiyoruz. Lokalimizden de port a ulaşmak istiyoruz, localimizdeki localhost 8000 portu ile container daki çalışan 8000 portuna ulaşmak istiyoruz,


- < docker run -p 8000:8000 be-ws:v1>  -> be-ws:v1 image ından bir container ayağa kaldır, p port ile locaimizdeki 8000 portu ile container daki 8000 portunu birbirine eşle, yani containerda çalışan servera biz localhost 8000 den de ulaşabilelim.
```powershell
- docker run -d -p 8000:8000 --name backend-workshop6 be-ws:v1
```

- Evet artık belirttiğim image dan, terminali arka planda çalışan, belirttiğim port numarasından erişebildiğim, bir isim vererek ayağa kaldırdığım bir containerımız oldu.
- 8000 portundan (Localhost:8000 den) erişiyorum ancak, "no such table: apiTodo_todo" şeklinde migrate hatası veriyor. Yani migrate yapılmadığı için tablolar db de oluşturulmamış.
- Peki bunu nasıl çözeceğiz? ->
 

- Container çalıştırdıktan sonra, çalışırken, docker desktop -> container -> actions kısmnından arka planda çalışan terminali açıp o termialde 
```sh
python manage.py migrate
```
komutunu çalıştırıp db de tablolarımızı oluşturabileceğimiz gibi,





#### container içerisine run ederken bağlanmak için ->

- Oluşturdunuz container bir server, linux server ı ve bu linux server ın terminaline bağlanıp içerisinde işlemler yapılabilir.

- container a bağlanıp içerisindeki file lara bakabiliriz. Bunun için interaktif modda çalıştırmak gerekiyor.

- linux lerde bash terminale oluyor, < docker -it < container-name> > böyle çalıştırılırsa default olarak bash terminaldir.

-bash terminalden başka açacaksanız ki (alpine a özel birşey, alpine larda normal shell var, ubuntu kullansaydınız buna gerek yoktu.) kodun sonuna sh yazıyorsunuz  < docker -it backend-workshop sh > 


```powershell
- docker exec -it backend-workshop sh  # shell çalışır. exit ile çıkılır terminalden
- python manage.py migrate
- python manage.py createsuperuser
- .....
- ....
- exit  # terminalden çıkılır.
```



- çalışan container ımızın içerisinde kod çalıştırabiliriz. (Mesela "python manage.py migrate")

```powershell
- docker exec backend-workshop7 python manage.py migrate
```




- Ayrıca container create ederken de kullanılabiliyor.
- be-ws:v1 imagından container ı oluştur, çalıştır;
- ?????? Port olmadığı için ulaşılamıyor.
- be-ws:v1 imagından container ı oluştur, bana bir terminal aç
```powershell
- docker run -it be-ws:v1
```

- be-ws:v1 imagından container ı oluştur, bana bir shell terminal aç
```powershell
- docker run -it be-ws:v1 sh
```



#### çalışan containerları görmek için ->

```powershell
- docker container ls # List local container.
- docker ps  # çalışan containerları görmek için
- docker ps -a  # çalışan/çalışmayan tüm containerları görmek için
- docker start|stop <container_name> # Start/Stop container.
- docker rm <container_name> # Delete stopped container.
- docker rm -f <container_name> # Delete çalışan container.
- docker container prune # delete all stopped container
```


#### image remove with rmi 

- image name ve tag name ile docker rmi ile image silebiliyoruz, ancak container oluşturulmuşsa silmez. 
- Biz yine de silmek istersek, force -f kullanmamız gerekiyor. 
- Bu şekilde silersek sildiğimiz image dan create ettiğimiz container/containerlar durduruluyor.

```powershell
- docker rmi be-ws:v1  # image ı bu (be-ws:v1) olanı remove et! container oluşturulmuşsa silmez!
- docker rmi -f be-ws:v1  # container oluşturulmuşsa bile image remove et! Bu image dan oluşturulan container da durduruluyor.
```


#### container delete etmek

```powershell
- docker rm <container_name>|<container_id> # Delete stopped container.
- docker container prune # delete all stopped container
- docker rm -f <container_name> # Delete çalışan container.
```

### docker hub da repository create edip oluşturduğumuz image ı paylaşmak

- docker hub da backend-test isminde bir repository create et,
- dockerfile dan bir image oluştur -> 
```powershell
- docker build -t backend-test:v1 .
```

- docker images ile backend-test isminde ve v1 Tag i ile oluştuğunu gördük.

- repository adresi ile push ediyoruz;
```powershell
- docker push umitdeveloper/backend-test
```

- hata verdi, localde < An image does not exist locally with the tag: umitdeveloper/be-ws:v1> bu tag e sahip bir image yok dedi. localdeki image ımızın name ve tag ini repository deki gibi değiştirmemizi istedi push etmeden önce, değiştireceğiz.
- name ve tag değişikliği ->
  < docker tag be-ws:v1 umitdeveloper/be-ws >     bunu buna çevir
```powershell
- docker tag backend-test:v1 umitdeveloper/backend-test
```
 

- istersek tag de verebiliriz < docker tag be-ws:v1 umitdeveloper/be-ws:v1 > bunu buna çevir, v1 tag i ver!
```powershell
- docker tag backend-test:v1 umitdeveloper/backend-test:v1
- docker push umitdeveloper/backend-test:v1
```


- < docker images> ile kontrol ettiğimizde aynı image id den farklı bir image (repository->umitdeveloper/backend-test   Tag->v1) oluşturduğunu gördük. 



- repository adresi ile push ediyoruz;
```powershell
- docker push umitdeveloper/backend-test:v1
```
- terminalde pushlandığını gördük, dockerhub da image ın oluştuğunu gördük.


- başkasının reposundaki image ı pull ediyoruz.
- dockerhub da henryfrstr diye aratınca henryfrstr/hello-world çıkıyor, Tags başlığının altındaki v1 image ını kopyalayıp, 
- < docker pull henryfrstr/hello-world:v1 > çalıştırıp pull ediyoruz.
- < docker images> ile görüyoruz,
- < docker run henryfrstr/hello-world:v1> ile container oluşturduk ve çalıştı.



# DOCKERHUB:
```powershell
- docker login # Login auto (get user-info from docker-desktop)
- docker login -u <user_name> # Login manual
- docker tag <image_name> <user_name>/<image_name>[:tag] # Connect repo and set tag
- docker push <user_name>/<image_name>[:tag] # PUSH
- docker pull <user_name>/<image_name>[:tag] # PULL
- docker search <image_name> # Search in DockerHub
```

## Deployment-project-ke-cloud-services (AWS)

### langkah-langkah deploy project ke cloud services :

* buat instance baru
* pilih os ex. : ubuntu v.20.04 lts
* konfigurasi file.pem
* setting port di instance id -> security group
* inbound rules -> edit inbound rules
* tambahkan port http dan mysql -> save
* jalankan instance
* klik instance id -> pilih connect
* update os ex. ubuntu

```
sudo apt update
```

* install docker
```
sudo apt install docker.io
```

* change mode (ganti hak akses untuk menjalankan docker)
```
sudo chmod 777 /var/run/docker.sock
```
* menambahkan ssh ke os yang terdapat di instance
* ssh-keygen -> enter" sampai generate file id_rsa.pub
* buka file dengan masuk ke direktori .ssh (pakai cd)
* menambahkan ssh key di github
* ssh & gpg key -> new ssh -> ssh diisi dari id_rsa.pub
* cloning git menggunakan ssh :
```
git clone git@github.com...
```

* [optional] mengganti nama project
```
mv [namaproject] [namabaru ex: app]
```

* buat file Dockerfile di vs code, branch master / main. [nama program] bisa disesuaikan
```
FROM golang:1.16-alpine

WORKDIR /app

COPY go.mod go.sum ./

RUN go mod download

COPY . .

RUN go build -o [nama program]

CMD ./[nama program]
```

* setting database -> RDS -> pilih yg free tier
* tambahkan greetings.yml di github action
* tambahkan cicd file .yml di github workflow. [nama project] dan [nama images] bisa disesuaikan
```
name: Deploy to EC2
on: 
  push:
    branches:
      - main
jobs:

  build:
    name: Build
    runs-on: ubuntu-latest
    steps:
    - name: Deploy to EC2 using SSH
      uses: appleboy/ssh-action@master
      with:
        host: ${{ secrets.HOST }}
        username: ${{ secrets.USERNAME }}
        key: ${{ secrets.KEY }}
        port: ${{ secrets.PORT }}
        script: |
          cd /home/ubuntu/app
          git pull origin main
          docker stop [nama project]
          docker rm [nama project]
          docker build -t [nama images]:latest .
          docker run -d -p 80:8080 --name [nama project] [nama images]-app:latest
```

* masuk setting pada github -> secret
* tambahkan new repository secret :
* HOST value ip public
* KEY value file .pem
* PORT value 22 (port ssh)
* USERNAME ubuntu (sesuaikan dengan username instance di aws)

* push CI/CD ke branch main

# MyClinic

MyClinic は、個人開業医のために作成された、オープンソースの、電子カルテシステムです。患者登録、診療録運用、処方箋発行、領収書発行、レセプト請求明細書作成など、医院運営に必要な業務が一通りできます。ライセンスは MIT です。

## 構成

[サーバー](https://github.com/hangilc/myclinic-scala)と[クライアント](https://github.com/hangilc/myclinic-ts)に分離された構成になっています。サーバーはウェブサーバーで、MySQL データベースにデータを保存し、HTTP リクエストに応答します。クライアントはウェブアプリケーションです。

サーバーは Scala で実装されており、データベース接続に Doobie、ウェブサーバーのために http4s フレームワークをそれぞれ使用しています。クライアントは Vite 及び Svelte フレームワークを使用し、TypeScript で実装されています。

## Docker を用いてのデモ

docker compose を使って MyClinic のデモを使用することができます。

以下の内容で docker-compose.yml を作成し、`docker compose up -d` を実行し、（MySQL が実行されるまで数秒待って）`http://127.0.0.1:8080/vite/reception/` にアクセスすると、「受付」サイトが開きます。そこで、患者の新規登録ができます。そのあと、`http://127.0.0.1:8080/vite/practice/` にアクセスし、診療を開始できます。


```
version: '3'
services:
  mysql:
    image: "hangilc/myclinic-mysql-demo:1.0.0"
    ports:
      - "23306:3306"
  files:
    image: "hangilc/myclinic-files-demo:1.0.0"
  vite:
    image: "hangilc/myclinic-vite:1.0.0"
  server:
    image: "hangilc/myclinic-scala:1.0.0"
    ports:
      - "8080:8080"
    environment:
      - MYCLINIC_DATA_DIR=/myclinic-files
      - MYCLINIC_DATA=/myclinic-files
      - MYCLINIC_PORTAL_TMP_DIR=/myclinic-files/portal-tmp
      - MYCLINIC_VITE_DIR=/vite
      - MYCLINIC_CONFIG_DIR=/myclinic-files/config
      - MYCLINIC_DB_HOST=mysql
      - MYCLINIC_DB_PORT=3306
      - MYCLINIC_DB_USER=myuser
      - MYCLINIC_DB_PASS=mypass
      - MYCLINIC_PAPER_SCAN_DIR=/myclinic-files/paper-scan
      - MYCLINIC_SHOHOUSEN_DIR=/myclinic-files/shohousen
      - MYCLINIC_CONFIG=/myclinic-files/config
      - MYCLINIC_FONT_DIR=/myclinic-files/fonts
      - MYCLINIC_SHUJII_DIR=/myclinic-files/shujii
      - MYCLINIC_PHARMACY_LIST=/myclinic-files/pharmacies.txt
      - MYCLINIC_REFER_DIR=/myclinic-files/refer
      - MYCLINIC_RCPT_DIR=/myclinic-files/rcpt
    volumes_from:
      - files
      - vite
    depends_on: 
      - mysql
```





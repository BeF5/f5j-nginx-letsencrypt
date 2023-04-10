NGINX Plus の Let's Encrypt の利用
####

0. (事前準備) NGINX Plusのインストール
====

1. NGINX Plusのインストール
----

| 以下のページの内容を参考にNGINX Plus及びモジュールをインストールします
| `NGINX Plus Lab 1. NGINX Plusのインストール (15min) <https://f5j-nginx-plus-lab1.readthedocs.io/en/latest/class1/module2/module2.html#nginx-plus-15min>`__


1. NGINX 事前設定
====

NGINXインストール時に配置される初期設定の ``server_name`` を証明書の発行を行うFQDNに変更します

.. code-block:: cmdin

  # ファイルをバックアップします
  sudo mv /etc/nginx/conf.d/default.conf  /etc/nginx/conf.d/default.conf-

  # vi コマンドでファイルを変更します
  sudo vi /etc/nginx/conf.d/default.conf
  # このサンプルでは以下のように変更しています
  #  Old: server_name  localhost;
  #  New: server_name app.nginx-demo.site;

変更した内容を確認します

.. code-block:: cmdin

  sudo diff -u /etc/nginx/conf.d/default.conf-  /etc/nginx/conf.d/default.conf

.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:

  --- /etc/nginx/conf.d/default.conf-     2023-04-10 07:21:20.825364455 +0000
  +++ /etc/nginx/conf.d/default.conf      2023-04-10 07:21:37.768941777 +0000
  @@ -1,6 +1,7 @@
   server {
       listen       80 default_server;
  -    server_name  localhost;
  +    #server_name  localhost;
  +    server_name app.nginx-demo.site;
  
       #access_log  /var/log/nginx/host.access.log  main;

NGINXに設定を反映します

.. code-block:: cmdin

  sudo nginx -s reload

2. CertBot の実行
====

Ubuntu 上で動作する nginx に対するCertbotの手順は `certbot instructions <https://certbot.eff.org/instructions?ws=nginx&os=ubuntufocal&tab=standard>`__ を参照してください

.. code-block:: cmdin

  sudo snap install certbot --classic

.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:

  certbot 2.5.0 from Certbot Project (certbot-eff✓) installed

NGINXのオプションを指定し、certbotを実行します

.. code-block:: cmdin

  sudo certbot --nginx

.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:
  :emphasize-lines: 1,13-14

  Saving debug log to /var/log/letsencrypt/letsencrypt.log
  Enter email address (used for urgent renewal and security notices)
   (Enter 'c' to cancel): xxx@xx.com << 有効なメールアドレスを入力
  
  - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
  Please read the Terms of Service at
  https://letsencrypt.org/documents/LE-SA-v1.3-September-21-2022.pdf. You must
  agree in order to register with the ACME server. Do you agree?
  - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
  (Y)es/(N)o: y << yを入力 
  
  - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
  Would you be willing, once your first certificate is successfully issued, to
  share your email address with the Electronic Frontier Foundation, a founding
  partner of the Let's Encrypt project and the non-profit organization that
  develops Certbot? We'd like to send you email about our work encrypting the web,
  EFF news, campaigns, and ways to support digital freedom.
  - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
  (Y)es/(N)o: y << yを入力 
  Account registered.
  
  Which names would you like to activate HTTPS for?
  We recommend selecting either all domains, or all domains in a VirtualHost/server block.
  - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
  1: app.nginx-demo.site
  - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
  Select the appropriate numbers separated by commas and/or spaces, or leave input
  blank to select all options shown (Enter 'c' to cancel): 1 << 出力結果から適切な番号を選択
  Requesting a certificate for app.nginx-demo.site
  
  Successfully received certificate.
  Certificate is saved at: /etc/letsencrypt/live/app.nginx-demo.site/fullchain.pem
  Key is saved at:         /etc/letsencrypt/live/app.nginx-demo.site/privkey.pem
  This certificate expires on 2023-07-09.
  These files will be updated when the certificate renews.
  Certbot has set up a scheduled task to automatically renew this certificate in the background.
  
  Deploying certificate
  Successfully deployed certificate for app.nginx-demo.site to /etc/nginx/conf.d/default.conf
  Congratulations! You have successfully enabled HTTPS on https://app.nginx-demo.site
  
  - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
  If you like Certbot, please consider supporting our work by:
   * Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
   * Donating to EFF:                    https://eff.org/donate-le
  - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

- 3行目: 有効なメールアドレスを入力します
- 10行目: サービスの利用規約です。 ``y`` を入力してください
- 19行目: EFFへのメール登録の確認。登録を希望する場合 ``y`` を入力してください
- 25行目: 証明書の対象となるFQDNが表示されます。こちらの情報はNGINXの設定ファイルより自動的に取得されています
- 28行目: 25行目のリストの中で今回対象とするFQDNを選択します。この例では ``1`` を選択します
- 31行目: 証明書の生成が成功したことが出力されています。以降に証明書の配置されたPATHなどが表示されています

3. Certbotの確認
====

生成した証明書の情報を確認します

.. code-block:: cmdin

  sudo certbot certificates

.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:
  :emphasize-lines: 5,8,9,10-11

  Saving debug log to /var/log/letsencrypt/letsencrypt.log
  
  - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
  Found the following certs:
    Certificate Name: app.nginx-demo.site
      Serial Number: 4d9e5b7a072be42940a7562c79ed176adc6
      Key Type: ECDSA
      Domains: app.nginx-demo.site
      Expiry Date: 2023-07-09 06:23:51+00:00 (VALID: 89 days)
      Certificate Path: /etc/letsencrypt/live/app.nginx-demo.site/fullchain.pem
      Private Key Path: /etc/letsencrypt/live/app.nginx-demo.site/privkey.pem
  - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

- 5行目、8行目で対象となるFQDNが表示されています
- 9行目で証明書の有効期限が ``89日`` であると示されています
- 10、11行目で証明書のPathが示されています

証明書の更新処理が正しく実行されるかDry Runを実行します

.. code-block:: cmdin

  sudo certbot renew --dry-run

.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:

  Saving debug log to /var/log/letsencrypt/letsencrypt.log
  
  - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
  Processing /etc/letsencrypt/renewal/app.nginx-demo.site.conf
  - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
  Account registered.
  Simulating renewal of an existing certificate for app.nginx-demo.site
  
  - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
  Congratulations, all simulated renewals succeeded:
    /etc/letsencrypt/live/app.nginx-demo.site/fullchain.pem (success)
  - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

正しく更新できる状態であることが確認できました

証明書の更新チェックの処理がいつに設定されているか確認します

.. code-block:: cmdin

  systemctl list-timers | grep -e NEXT -e certbot

.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:

  NEXT                        LEFT               LAST                        PASSED       UNIT                           ACTIVATES
  Mon 2023-04-10 09:40:00 UTC 2h 8min left       n/a                         n/a          snap.certbot.renew.timer       snap.certbot.renew.service

過去に証明書の更新チェックは行われていないため、 ``LAST`` 、 ``PASSED`` が ``n/a`` となっています
``NEXT`` に表示された時刻が更新チェックがなされる時刻となります

(Tips) この ``NEXTの時間を経過した後``、 ``systemctl status`` を確認すると、以下のように所定の時刻で certbot.renew が実行されたことを確認できます

.. code-block:: cmdin

  sudo systemctl status snap.certbot.renew

.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:

  ○ snap.certbot.renew.service - Service for snap application certbot.renew
       Loaded: loaded (/etc/systemd/system/snap.certbot.renew.service; static)
       Active: inactive (dead) since Mon 2023-04-10 09:40:11 UTC; 1h 13min ago
  TriggeredBy: ● snap.certbot.renew.timer
      Process: 4504 ExecStart=/usr/bin/snap run --timer=00:00~24:00/2 certbot.renew (code=exited, status=0/SUCCESS)
     Main PID: 4504 (code=exited, status=0/SUCCESS)
          CPU: 765ms
  
  Apr 10 09:40:10 ip-10-0-12-80 systemd[1]: Starting Service for snap application certbot.renew...
  Apr 10 09:40:11 ip-10-0-12-80 systemd[1]: snap.certbot.renew.service: Deactivated successfully.
  Apr 10 09:40:11 ip-10-0-12-80 systemd[1]: Finished Service for snap application certbot.renew.


NGINXの設定内容を確認します

.. code-block:: cmdin

  sudo diff -u /etc/nginx/conf.d/default.conf-  /etc/nginx/conf.d/default.conf

.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:
  :emphasize-lines: 7-8, 16-35

  --- /etc/nginx/conf.d/default.conf-     2023-04-10 07:21:20.825364455 +0000
  +++ /etc/nginx/conf.d/default.conf      2023-04-10 07:23:55.437516803 +0000
  @@ -1,6 +1,6 @@
   server {
  -    listen       80 default_server;
  -    server_name  localhost;
  +    #server_name  localhost;
  +    server_name app.nginx-demo.site;
  
       #access_log  /var/log/nginx/host.access.log  main;
  
  @@ -56,4 +56,23 @@
       #location = /dashboard.html {
       #    root /usr/share/nginx/html;
       #}
  +
  +    listen 443 ssl; # managed by Certbot
  +    ssl_certificate /etc/letsencrypt/live/app.nginx-demo.site/fullchain.pem; # managed by Certbot
  +    ssl_certificate_key /etc/letsencrypt/live/app.nginx-demo.site/privkey.pem; # managed by Certbot
  +    include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
  +    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot
  +
   }
  +server {
  +    if ($host = app.nginx-demo.site) {
  +        return 301 https://$host$request_uri;
  +    } # managed by Certbot
  +
  +
  +    listen       80 default_server;
  +    server_name app.nginx-demo.site;
  +    return 404; # managed by Certbot
  +
  +
  +}

TLS/SSLの設定が追加されています
これらの設定追加により、HTTP(TCP/80)にアクセスした場合には、HTTPS(TCP/443)にリダイレクトされる設定となっています。

4. 接続確認
====

HTTP(TCP/80) への接続を確認します

.. code-block:: cmdin

  curl -v http://app.nginx-demo.site

.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:
  :emphasize-lines: 7-8, 16-35

  *   Trying 13.231.152.79:80...
  * Connected to app.nginx-demo.site (13.231.152.79) port 80 (#0)
  > GET / HTTP/1.1
  > Host: app.nginx-demo.site
  > User-Agent: curl/7.81.0
  > Accept: */*
  >
  * Mark bundle as not supporting multiuse
  < HTTP/1.1 301 Moved Permanently
  < Server: nginx/1.23.2
  < Date: Mon, 10 Apr 2023 07:24:39 GMT
  < Content-Type: text/html
  < Content-Length: 169
  < Connection: keep-alive
  < Location: https://app.nginx-demo.site/
  <
  <html>
  <head><title>301 Moved Permanently</title></head>
  <body>
  <center><h1>301 Moved Permanently</h1></center>
  <hr><center>nginx/1.23.2</center>
  </body>
  </html>
  * Connection #0 to host app.nginx-demo.site left intact
  
  
  
HTTPS(TCP/443) への接続を確認します
  
.. code-block:: cmdin

  curl -v https://app.nginx-demo.site

.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:
  :emphasize-lines: 7-8, 16-35

  *   Trying 13.231.152.79:443...
  * Connected to app.nginx-demo.site (13.231.152.79) port 443 (#0)
  * ALPN, offering h2
  * ALPN, offering http/1.1
  *  CAfile: /etc/ssl/certs/ca-certificates.crt
  *  CApath: /etc/ssl/certs
  * TLSv1.0 (OUT), TLS header, Certificate Status (22):
  * TLSv1.3 (OUT), TLS handshake, Client hello (1):
  * TLSv1.2 (IN), TLS header, Certificate Status (22):
  * TLSv1.3 (IN), TLS handshake, Server hello (2):
  * TLSv1.2 (IN), TLS header, Finished (20):
  * TLSv1.2 (IN), TLS header, Supplemental data (23):
  * TLSv1.3 (IN), TLS handshake, Encrypted Extensions (8):
  * TLSv1.2 (IN), TLS header, Supplemental data (23):
  * TLSv1.3 (IN), TLS handshake, Certificate (11):
  * TLSv1.2 (IN), TLS header, Supplemental data (23):
  * TLSv1.3 (IN), TLS handshake, CERT verify (15):
  * TLSv1.2 (IN), TLS header, Supplemental data (23):
  * TLSv1.3 (IN), TLS handshake, Finished (20):
  * TLSv1.2 (OUT), TLS header, Finished (20):
  * TLSv1.3 (OUT), TLS change cipher, Change cipher spec (1):
  * TLSv1.2 (OUT), TLS header, Supplemental data (23):
  * TLSv1.3 (OUT), TLS handshake, Finished (20):
  * SSL connection using TLSv1.3 / TLS_AES_256_GCM_SHA384
  * ALPN, server accepted to use http/1.1
  * Server certificate:
  *  subject: CN=app.nginx-demo.site
  *  start date: Apr 10 06:23:52 2023 GMT
  *  expire date: Jul  9 06:23:51 2023 GMT
  *  subjectAltName: host "app.nginx-demo.site" matched cert's "app.nginx-demo.site"
  *  issuer: C=US; O=Let's Encrypt; CN=R3
  *  SSL certificate verify ok.
  * TLSv1.2 (OUT), TLS header, Supplemental data (23):
  > GET / HTTP/1.1
  > Host: app.nginx-demo.site
  > User-Agent: curl/7.81.0
  > Accept: */*
  >
  * TLSv1.2 (IN), TLS header, Supplemental data (23):
  * TLSv1.3 (IN), TLS handshake, Newsession Ticket (4):
  * TLSv1.2 (IN), TLS header, Supplemental data (23):
  * TLSv1.3 (IN), TLS handshake, Newsession Ticket (4):
  * old SSL session ID is stale, removing
  * TLSv1.2 (IN), TLS header, Supplemental data (23):
  * Mark bundle as not supporting multiuse
  < HTTP/1.1 200 OK
  < Server: nginx/1.23.2
  < Date: Mon, 10 Apr 2023 07:35:45 GMT
  < Content-Type: text/html
  < Content-Length: 615
  < Last-Modified: Tue, 07 Jun 2022 08:00:00 GMT
  < Connection: keep-alive
  < ETag: "629f0580-267"
  < Accept-Ranges: bytes
  <
  <!DOCTYPE html>
  <html>
  <head>
  <title>Welcome to nginx!</title>
  <style>
  html { color-scheme: light dark; }
  body { width: 35em; margin: 0 auto;
  font-family: Tahoma, Verdana, Arial, sans-serif; }
  </style>
  </head>
  <body>
  <h1>Welcome to nginx!</h1>
  <p>If you see this page, the nginx web server is successfully installed and
  working. Further configuration is required.</p>
  
  <p>For online documentation and support please refer to
  <a href="http://nginx.org/">nginx.org</a>.<br/>
  Commercial support is available at
  <a href="http://nginx.com/">nginx.com</a>.</p>
  
  <p><em>Thank you for using nginx.</em></p>
  </body>
  </html>
  * Connection #0 to host app.nginx-demo.site left intact

証明書の情報を確認します

.. code-block:: cmdin

  echo | openssl s_client -showcerts -connect app.nginx-demo.site:443  2>/dev/null | openssl x509 -inform pem -noout -text

.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:
  :emphasize-lines: 7-8, 16-35

  Certificate:
      Data:
          Version: 3 (0x2)
          Serial Number:
              ** 省略 **
          Signature Algorithm: sha256WithRSAEncryption
          Issuer: C = US, O = Let's Encrypt, CN = R3
          Validity
              Not Before: Apr 10 06:23:52 2023 GMT
              Not After : Jul  9 06:23:51 2023 GMT
          Subject: CN = app.nginx-demo.site
          Subject Public Key Info:
              Public Key Algorithm: id-ecPublicKey
                  Public-Key: (256 bit)
                  pub:
                    ** 省略 **
                  ASN1 OID: prime256v1
                  NIST CURVE: P-256
          X509v3 extensions:
              X509v3 Key Usage: critical
                  Digital Signature
              X509v3 Extended Key Usage:
                  TLS Web Server Authentication, TLS Web Client Authentication
              X509v3 Basic Constraints: critical
                  CA:FALSE
              X509v3 Subject Key Identifier:
                  ** 省略 **
              X509v3 Authority Key Identifier:
                  ** 省略 **
              Authority Information Access:
                  OCSP - URI:http://r3.o.lencr.org
                  CA Issuers - URI:http://r3.i.lencr.org/
              X509v3 Subject Alternative Name:
                  DNS:app.nginx-demo.site
              X509v3 Certificate Policies:
                  Policy: 2.23.140.1.2.1
                  Policy: 1.3.6.1.4.1.44947.1.1.1
                    CPS: http://cps.letsencrypt.org
              CT Precertificate SCTs:
                  Signed Certificate Timestamp:
                      Version   : v1 (0x0)
                      Log ID    : ** 省略 **
                      Timestamp : Apr 10 07:23:52.680 2023 GMT
                      Extensions: none
                      Signature : ecdsa-with-SHA256
                                  ** 省略 **
                  Signed Certificate Timestamp:
                      Version   : v1 (0x0)
                      Log ID    : ** 省略 **
                      Timestamp : Apr 10 07:23:52.699 2023 GMT
                      Extensions: none
                      Signature : ecdsa-with-SHA256
                                  ** 省略 **
      Signature Algorithm: sha256WithRSAEncryption
      Signature Value:
        ** 省略 **

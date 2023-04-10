環境
####

実施環境
====

-  利用するコマンド： snap
-  NGINX Trialライセンスの取得、ラボ実施ユーザのHome Directoryへ配置

動作環境
====

この手順を実施するためには以下を前提としています
- 対象のホストに対し、GlobalIPアドレスを宛先としたHTTP(TCP/80)の接続が可能であること
- 対象のホストに接続する際に利用するGlobalIPアドレスに紐づくFQDNが正しくDNSで名前解決できること


動作環境構成図
----

イラスト右側のNGINX Plusで各種操作を実施することを想定した手順を示します

   .. image:: ./media/nginx-letsencrypt-lab.png
      :width: 400







FROM perl:5.26

LABEL author="omokawa"

# プロキシ設定(必要な場合)
# ENV http_proxy http://omokawa.yasuaki.s4%40tex-sol.com:Omokawa123@172.16.16.3:15080
# ENV https_proxy http://omokawa.yasuaki.s4%40tex-sol.com:Omokawa123@172.16.16.3:15080
# ENV no_proxy 127.0.0.1,localhost,192.168.1.1

# 必要なパッケージをインストール
RUN set -x && \
  apt-get update && \
  apt-get install -y vim

# アプリ用ユーザ作成
RUN useradd -m -s /bin/bash appuser

# ユーザ切り替え
USER appuser

# ディレクトリ移動
WORKDIR /home/appuser

# cpanminとlocal::libでモジュール管理を行う。
# 今回は切り替えたユーザのホームディレクトリ配下にモジュールを管理する。
RUN set -x && \
  : curl -L cpanmin.us -o cpanm && \
  : chmod +x cpanm && \
  : ./cpanm -l ~/extlib local::lib && \
  : perl -I ~/extlib/lib/perl5 -Mlocal::lib=~/extlib | tee -a ~/.bash_profile && \
  : "RUN exec $SHELL -l # sourceコマンドとかやらずにシェルを再起動できるコマンド。-lオプションは、シェルをログインシェルとして起動するためのもの。dockerではうごかなかったため、下記のコマンドで代用" && \
  : . ~/.bash_profile && \
  : "Mojoliciousインストールと確認(初回のみ)" && \
  : ./cpanm -n Mojolicious && \
  : perl -MMojolicious -e 'print "$Mojolicious::VERSION\n"' && \
  : "Carton(webアプリ依存モジュールのチェック機構)インストールと確認" && \
  : ./cpanm Carton &&\
  cpanm Carton


# アプリ起動(今回は、/webapp/srcにマウントしたホスト側のファイルを起動する)
# ~/.bash_profileを読み込むのは、どうもRUN命令で設定したのはビルドシーケンスで適用
# されるもので、CMD命令はコンテナが立ち上げられた後で実行されるものらしい。というわけで
# USER命令が有効になっているので現在のユーザはappuserであるものの、.bash_profile
# はもう一度明示的に読み込む必要がある。
WORKDIR /webapp/src
CMD ["id", "-nu"]
# CMD ["which", "carton"]
# CMD ["carton", "exec", "--", "morbo", "bin/API.pl"]
# CMD source ~/.bash_profile && carton exec -- morbo bin/API.pl
# CMD [".", "~/.bash_profile", "&&", "carton", "exec", "--", "morbo", "bin/API.pl"]
# CMD date


#RUN useradd -m -s /bin/bash appuser && cpanm Carton
#RUN mkdir -p /home/appuser/webapp
#WORKDIR /home/appuser/webapp
#RUN chown appuser:appuser /home/appuser/webapp
#USER appuser
#COPY --chown=appuser:appuser /tmp/shared/cpanfile /home/appuser/webapp
#RUN carton install
#COPY --chown=appuser:appuser webapp .
#CMD carton exec -- ./myapp.pl daemon -l http://0.0.0.0:$PORT

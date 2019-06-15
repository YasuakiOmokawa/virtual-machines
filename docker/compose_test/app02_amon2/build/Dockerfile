FROM perl:5.26

LABEL author="omokawa"

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

# cpanminとlocal::libでモジュール管理を行う。今回は切り替えたユーザのホームディレクトリ配下にモジュールを管理する
RUN set -x && \
  curl -L cpanmin.us -o cpanm && \
  chmod +x cpanm && \
  ./cpanm -l ~/extlib local::lib && \
  perl -I ~/extlib/lib/perl5 -Mlocal::lib=~/extlib | tee -a ~/.bash_profile && \
  : "RUN exec $SHELL -l # sourceコマンドとかやらずにシェルを再起動できるコマンド。-lオプションは、シェルをログインシェルとして起動するためのもの。dockerではうごかなかったため、下記のコマンドで代用" && \
  . ~/.bash_profile && \
  : "Mojoliciousインストールと確認" && \
  ./cpanm -n Mojolicious && \
  perl -MMojolicious -e 'print "$Mojolicious::VERSION\n"' && \
  : "モジュール作成用にMinillaをインストール" && \
  cpanm Minilla

#RUN useradd -m -s /bin/bash appuser && cpanm Carton

#RUN mkdir -p /home/appuser/webapp
#WORKDIR /home/appuser/webapp
#RUN chown appuser:appuser /home/appuser/webapp
#USER appuser

#COPY --chown=appuser:appuser /tmp/shared/cpanfile /home/appuser/webapp
#RUN carton install

#COPY --chown=appuser:appuser webapp .

#CMD carton exec -- ./myapp.pl daemon -l http://0.0.0.0:$PORT

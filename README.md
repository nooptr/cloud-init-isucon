# cloud-init-isucon

## Overview

[cloud-init](https://cloud-init.io/)に対応した環境で[ISUCON](http://isucon.net/)の過去問を構築するためのcloud-config集です。
Apple Silicon(aarch64)にも対応しているため、[Multipass](https://multipass.run/)などと組み合わせればM1 Mac上で過去問環境の構築が可能です。

汎用的に作ってるので、cloud-initに対応した環境、例えば[AWS](https://docs.aws.amazon.com/ja_jp/AWSEC2/latest/UserGuide/user-data.html#user-data-cloud-init)、[Azure](https://docs.microsoft.com/ja-jp/azure/virtual-machines/linux/using-cloud-init)、[Google Cloud](https://blog.woohoosvcs.com/2019/11/cloud-init-on-google-compute-engine/)、[Oracle Cloud](https://docs.oracle.com/ja-jp/iaas/Content/Compute/References/images.htm#Oracle__linux-cloud-init)、[さくらのクラウド](https://manual.sakura.ad.jp/cloud/server/cloud-init.html)、[Multipass](https://multipass.run/)、[VMware](https://kb.vmware.com/s/article/59557?lang=ja)など、クラウドからローカルまで[幅広く使える](https://cloudinit.readthedocs.io/en/latest/topics/availability.html)はずです。

### cloud-config

* 公式
  * [ISUCON10予選](https://github.com/nooptr/cloud-init-isucon/tree/main/isucon10q)
  * [ISUCON11予選](https://github.com/nooptr/cloud-init-isucon/tree/main/isucon11q)
  * [ISUCON12予選](https://github.com/nooptr/cloud-init-isucon/tree/main/isucon12q)
* 非公式
  * [Pixiv社内ISUCON2016](https://github.com/nooptr/cloud-init-isucon/tree/main/private-isu)
  * [ISUCON11事前講習](https://github.com/nooptr/cloud-init-isucon/tree/main/isucon11-prior)
  * [Kayac社内ISUCON2022](https://github.com/nooptr/cloud-init-kayac-isucon-2022)
  * [NRI-ISUCON2022](https://github.com/nooptr/cloud-init-isucon/tree/main/nri-isucon2022)

サーバ内の構築にはAnsibleを使っています。Ansibleのplaybookのみ必要な場合は[nooptr/ansible-isucon](https://github.com/nooptr/ansible-isucon)をどうぞ。

## Usage

### さくらのクラウドでの利用方法

詳細な手順は[さくらのナレッジ](https://knowledge.sakura.ad.jp/31520/)をご確認ください。

### AWSでの利用方法

AWSはユーザーデータにcloud-initを渡すことができます。
詳細は[公式ドキュメント](https://docs.aws.amazon.com/ja_jp/AWSEC2/latest/UserGuide/user-data.html#user-data-cloud-init)をご確認ください。

### Multipassでの利用方法

* [Multipass](https://multipass.run/)実行環境を用意します
  * macOSユーザーは[Homebrew](https://brew.sh/)を使ってインストール可能です
  ```sh
  brew install multipass
  ```
  * Windowsユーザーは[アプリ インストーラー](https://apps.microsoft.com/store/detail/app-installer/9NBLGGH4NNS1)を使ってインストール可能です
  ```powershell
  winget.exe install Multipass
  ```
* このリポジトリ内のcfgファイルを手元に用意します
* cloud-initを使って起動します(isucon11qの例)
  ```sh
  multipass launch --name isucon12q --cpus 2 --disk 20G --mem 4G --timeout 60 --cloud-init isucon12q.cfg 22.04
  ```
  * cpus, disk, memoryは必要に応じて増減させてください
  * 末尾の `22.04` はUbuntuのバージョンです
  * cloud-initは時間がかかるため以下のようなメッセージが表示される場合がありますが、バックグラウンドで構築は継続しています
    ```
    launch failed: The following errors occurred:
    timed out waiting for initialization to complete
    ```
* 進捗は `/var/log/cloud-init-output.log` で確認できます
  ```sh
  multipass exec isucon12q -- tail -f /var/log/cloud-init-output.log
  ```
* `multipass shell` でシェルが使えます
  ```sh
  multipass shell isucon12q
  ```
* 環境の停止再開は stop/start です
  ```sh
  multipass stop isucon12q
  multipass start isucon12q
  ```
* 環境の削除は `multipass delete --purge` です
  ```sh
  multipass delete --purge isucon12q
  ```

## FAQ

### isuconユーザもしくはあるべきファイルが存在しない

Multipassやさくらのクラウドでの構築の場合、サーバ起動後も環境構築が続いている可能性があります。
`/var/log/cloud-init-output.log` で進捗を確認してください。

```sh
tail -f /var/log/cloud-init-output.log
```

### 構築の途中でエラーが発生した

ネットワークの状況やスリープモードなどの影響で構築中にエラーが発生した場合は以下のコマンドで再試行ができます。

```sh
sudo /var/lib/cloud/instance/scripts/runcmd
```

再試行や環境を一から作り直しても解決しない場合はissueで報告してみてください。

## Others

* [nooptr/ansible-isucon](https://github.com/nooptr/ansible-isucon)
* [nooptr/aws-isucon](https://github.com/nooptr/aws-isucon)
* [nooptr/docker-isucon](https://github.com/nooptr/docker-isucon)
* [nooptr/oci-arm-isucon](https://github.com/nooptr/oci-arm-isucon)
* [nooptr/vagrant-isucon](https://github.com/nooptr/vagrant-isucon)
* [nooptr/wsl-isucon](https://github.com/nooptr/wsl-isucon)

## License

MIT

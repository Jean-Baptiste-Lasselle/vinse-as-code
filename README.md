# vinse-as-code
mystery

* Installation de la paire clé publique clé privée
* configuration de la clé SSH d'utilisateur Github / GitLab
* insatllation des opérations spécifiques  : 
  * `vinse-start $URI_REPO_GITHUBLAB $NOM_DE_BRANCHE $CHEMIN_REPERTOIRE_LOCAL` (pour commencer à travailler sur un repo, sur une nouvelle branche,dans le réertoire local `$CHEMIN_REPERTOIRE_LOCAL` ). `$CHEMIN_REPERTOIRE_LOCAL` ne doit pas exister, ou être vide.
  * `vinse-resume $NOM_REPERTORIE_LOCAL $NOM_DE_BRANCHE $CHEMIN_REPERTOIRE_LOCAL` (pour commencer à travailler sur une branche, à partir d'un répertoire local au PC, il doit y avoir un repo git dans ce répertoire)
  * `vinse-send-merge-request` (pour commencer à travailler sur un repo)
  * `vinse-merge` (pour commencer à travailler sur un repo)
  * `vinse-stop` (pour terminer, avec une option discard, ou alor sun dernier commit & push) à travailler sur un repo)
  * gestion de ses paires de clés publiques privées, intégée à hashicorp vault.


# Clouydie ow

### Installation
 
```bash
# TODO: https://gist.github.com/anamorph/aaf8434d3bbad92059b3
sudo apt-get install -y python python-pip
sudo pip install awscli
# en raison de l'issue https://github.com/aws/aws-cli/issues/3579 , il 
# faut mettre à jour la CLI `aws` avec :
pip install awscli==1.16.14

###
# !!!! ATTENTION aux update / upgrade AWSCLI : 
# 
# -- Bon, j'ai essayé une version au-dessus de [1.16.14], mais 
#    on a une régression de la CLI AWS : 
#  > Avec la version [pip install awscli==1.16.14], j'ai un
#  succès.
#  > Par contre, avec la version [pip install awscli==1.16.225] 
#    j'ai une régression avec le retour de l'erreur avec
#    les commandes [aws s3 sync] cf. [./dossier_cloud_jbl]
# 

###
# puis il faut configurer la AWS CLI 
echo "Now we'll configure the AWS CLI"
read -p "Press any key to configure the AWS CLI" AWSCLICONF

aws configure
# the [aws configure] command will create the conf files : 
ls -allh ~/.aws/
cat ~/.aws/config
cat ~/.aws/credentials
aws version

```

* Installation Windows 10 : passer par l'installation de python, pip, et installer avec un pip install, cf. https://docs.aws.amazon.com/cli/latest/userguide/install-windows.html#awscli-install-windows-pip

```MSDOS
set PYHTON_INSTALLATION_HOME=C:\Users\%username%\.cloudieow\python3
mkdir %PYHTON_INSTALLATION_HOME%
REM cd %PYHTON_INSTALLATION_HOME%
choco install python3 --params "/InstallDir:%PYHTON_INSTALLATION_HOME%"
dir %PYHTON_INSTALLATION_HOME%
py -m pip install awscli==1.16.14
choco uninstall awscli
choco install awscli --version=1.16.14
echo "Fermez la fenêtre de shell MSDOS, et ouvrez en une nouvelle"
```

### Configuration

* fichier de configuration authentification `~/.aws/credentials` (devra êtree géré par HashiCorp Vault Secret Manager / et un truc genre `Keepass2`) : 
```ini
[default]
aws_access_key_id = accessKey1
aws_secret_access_key = verySecretKey1
```
* fichier de configuration d'exécution `~/.aws/config`
```ini
[default]
region = us-east-1
```
* Création du stockage cloud de vinse : 
  * debian stretch GNU / Linux : 
```bash
export NOM_DOSSIER_CLOUD=./vinse-in-ze-cloud
export NOM_HOTE_RESEAU_OSS_S3RVER=192.168.1.22
export NO_PORT_RESEAU_OSS_S3RVER=8003
export BUCKET_TO_UPLOAD_TO=cloudvinse
export URL_APPEL_API_ENDPOINT=http://$NOM_HOTE_RESEAU_OSS_S3RVER:$NO_PORT_RESEAU_OSS_S3RVER


aws s3 ls --endpoint-url=$URL_APPEL_API_ENDPOINT
echo "creation deu 'bucket' [$BUCKET_TO_UPLOAD_TO] "
aws --endpoint-url=$URL_APPEL_API_ENDPOINT s3 mb s3://$BUCKET_TO_UPLOAD_TO || exit 1
```
  * Windows 10+ : 
```bash
set NOM_DOSSIER_CLOUD=C:\Users\shamanvince\Desktop\vinsecloud
set NOM_HOTE_RESEAU_OSS_S3RVER=192.168.1.22
set NO_PORT_RESEAU_OSS_S3RVER=8003
set BUCKET_TO_UPLOAD_TO=cloudvinse
set URL_APPEL_API_ENDPOINT=http://%NOM_HOTE_RESEAU_OSS_S3RVER%:%NO_PORT_RESEAU_OSS_S3RVER%


aws s3 ls --endpoint-url=%URL_APPEL_API_ENDPOINT%
echo "creation du 'bucket' [%BUCKET_TO_UPLOAD_TO%] "
aws --endpoint-url=%URL_APPEL_API_ENDPOINT% s3 mb s3://%BUCKET_TO_UPLOAD_TO% || exit 1

```

# Utilisation


```bash
export NOM_DOSSIER_CLOUD=./vinse-in-ze-cloud
export NOM_HOTE_RESEAU_OSS_S3RVER=192.168.1.22
export NO_PORT_RESEAU_OSS_S3RVER=8003
export BUCKET_TO_UPLOAD_TO=cloudvinse
export URL_APPEL_API_ENDPOINT=http://$NOM_HOTE_RESEAU_OSS_S3RVER:$NO_PORT_RESEAU_OSS_S3RVER

# 
# -- preparing local directory synced to 
#    my private object storage service : 
# 
mkdir ./$NOM_DOSSIER_CLOUD


###
# 
# -- syncing
# 

# -- adding a new file in synced dir

cp *l*.* | grep -v 'g' ./$NOM_DOSSIER_CLOUD

# -- syncing to cloud

aws --endpoint-url=$URL_APPEL_API_ENDPOINT s3 sync "$NOM_DOSSIER_CLOUD/" "s3://$BUCKET_TO_UPLOAD_TO"

# -- now we need to check content of bucket $BUCKET_TO_UPLOAD_TO

aws --endpoint-url=$URL_APPEL_API_ENDPOINT s3 ls "s3://$BUCKET_TO_UPLOAD_TO"



# -- adding a new file in synced dir AGAIN

cp *l*.* | grep -v 'g' ./$NOM_DOSSIER_CLOUD

# -- syncing to cloud

aws --endpoint-url=$URL_APPEL_API_ENDPOINT s3 sync "$NOM_DOSSIER_CLOUD/" "s3://$BUCKET_TO_UPLOAD_TO"

# -- now we need to check content of bucket $BUCKET_TO_UPLOAD_TO

aws --endpoint-url=$URL_APPEL_API_ENDPOINT s3 ls "s3://$BUCKET_TO_UPLOAD_TO"



```

  * Windows 10+ : 
```bash
set NOM_DOSSIER_CLOUD=C:\Users\shamanvince\Desktop\vinsecloud
set NOM_HOTE_RESEAU_OSS_S3RVER=192.168.1.22
set NO_PORT_RESEAU_OSS_S3RVER=8003
set BUCKET_TO_UPLOAD_TO=cloudvinse
set URL_APPEL_API_ENDPOINT=http://$NOM_HOTE_RESEAU_OSS_S3RVER:$NO_PORT_RESEAU_OSS_S3RVER



# 
# -- preparing local directory synced to 
#    my private object storage service : 
# 
mkdir ./%NOM_DOSSIER_CLOUD%


###
# 
# -- syncing
# 

# -- adding a new file in synced dir

cp *l*.* | grep -v 'g' ./%NOM_DOSSIER_CLOUD%

# -- syncing to cloud

aws --endpoint-url=%URL_APPEL_API_ENDPOINT% s3 sync "%NOM_DOSSIER_CLOUD%/" "s3://%BUCKET_TO_UPLOAD_TO%"

# -- now we need to check content of bucket $BUCKET_TO_UPLOAD_TO

aws --endpoint-url=%URL_APPEL_API_ENDPOINT% s3 ls "s3://%BUCKET_TO_UPLOAD_TO%"



# -- adding a new file in synced dir AGAIN

cp *l*.* | grep -v 'g' ./%NOM_DOSSIER_CLOUD%

# -- syncing to cloud

aws --endpoint-url=%URL_APPEL_API_ENDPOINT% s3 sync "%NOM_DOSSIER_CLOUD%/" "s3://%BUCKET_TO_UPLOAD_TO%"

# -- now we need to check content of bucket $BUCKET_TO_UPLOAD_TO

aws --endpoint-url=%URL_APPEL_API_ENDPOINT% s3 ls "s3://%BUCKET_TO_UPLOAD_TO%"


```


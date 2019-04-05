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

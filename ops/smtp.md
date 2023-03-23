# Tongela serveur na yo moko ya kotinda ba mail SMTP

## maloba ya ebandeli

SMTP ekoki kosomba mbala moko ba services epai ya batekisi ya cloud, lokola:

* [Amazon SES SMTP ezali](https://docs.aws.amazon.com/ses/latest/dg/send-email-smtp.html)
* [Ali lipata email push](https://www.alibabacloud.com/help/directmail/latest/three-mail-sending-methods)

Okoki pe kotonga serveur ya mail na yo moko - kotinda sans limite, talo ya moke ya mobimba.

Na nse, tozali kolakisa litambe na litambe ndenge ya kotonga serveur ya mail na biso moko.

## Pona ya serveur

Serveur SMTP oyo ezo héberger yango moko esengaka IP public na ba ports 25, 456, na 587 oyo efungwami.

Ba clouds publics oyo basalelaka mingi ekangaki ba ports wana par défaut, mpe ekoki kozala possible ya kofungola yango na kobimisaka commande ya mosala, kasi ezali très troubles après tout.

Na recommandé kosomba na hôte oyo ezali na ba ports oyo efungwami mpe e soutenir ko setting up ba kombo ya domaine inverse.

Awa, nazali kopesa toli [ya Contabo](https://contabo.com) .

Contabo ezali fournisseur ya hébergement oyo azali na Munich, Allemagne, esalemaki na 2003 na ba prix très concurrents.

Soki oponi Euro lokola mbongo ya kosomba, ntalo ekozala moins cher (serveur oyo ezali na mémoire ya 8GB mpe 4 CPUs ezali na talo ya soki 529 yuans na mbula, mpe mbongo ya installation ya liboso ezali ofele mpo na mbula moko).

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/UoAQkwY.webp)

Tango ozali kosala commande, remark `prefer AMD` , mpe serveur oyo ezali na CPU AMD ekozala na performance ya malamu koleka.

Na oyo elandi, nakozua VPS ya Contabo lokola exemple pona kolakisa ndenge nini okoki kotonga serveur ya mail na yo moko.

## Configuration ya système ya Ubuntu

Système d'exploitation awa ezali Ubuntu 22.04

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/smpIu1F.webp)

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/m7Mwjwr.webp)

Soki serveur na ssh elakisaka `Welcome to TinyCore 13!` (ndenge emonisami na elilingi oyo ezali awa na nse), elakisi ete système yango ekɔtisami naino te. Svp déconnecter ssh pe zela mua minutes po o kota lisusu.

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/-qKACz9.webp)

Tango `Welcome to Ubuntu 22.04.1 LTS` ekobima, initialisation esili, mpe okoki kokoba na ba étapes oyo elandi.

### [Na bolingi] Kobanda esika ya botomboli

Etape oyo ezali optionnel.

Mpo na kozala malamu, natyaki installation mpe configuration ya système ya logiciel ubuntu na [github.com/wactax/ops.os/tree/main/ubuntu](https://github.com/wactax/ops.os/tree/main/ubuntu) .

Tambwisa commande oyo elandi mpo na ko installer na clique moko.

```
bash <(curl -s https://raw.githubusercontent.com/wactax/ops.os/main/ubuntu/boot.sh)
```

Ba usagers ya Chinois, svp bosalela commande oyo elandi na esika na yango, pe langue, fuseau horaire, etc.

```
CN=1 bash <(curl -s https://ghproxy.com/https://raw.githubusercontent.com/wactax/ops.os/main/ubuntu/boot.sh)
```

### Contabo efungolaka IPV6

Activer IPV6 po SMTP ekoki pe kotinda ba emails na ba adresses IPV6.

kobongisa `/etc/sysctl.conf`

Bobongola to bakisa milɔngɔ oyo elandi

```
net.ipv6.conf.all.disable_ipv6 = 0
net.ipv6.conf.default.disable_ipv6 = 0
net.ipv6.conf.lo.disable_ipv6 = 0
```

Bolanda na [mateya ya contabo: Kobakisa boyokani IPv6 na serveur na yo](https://contabo.com/blog/adding-ipv6-connectivity-to-your-server/)

Edit `/etc/netplan/01-netcfg.yaml` , bakisa mua ba lignes ndenge elakisami na figure oyo ezali awa na se (Fichier ya configuration par défaut Contabo VPS eza déjà na ba lignes oyo, décommentaire kaka).

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/5MEi41I.webp)

Na sima `netplan apply` pona kosala que configuration modifiée esala effet.

Sima ya configuration elongi, okoki kosalela `curl 6.ipw.cn` pona kotala adresse ipv6 ya réseau na yo ya libanda.

## Clone ya ebombelo ya configuration ops

```
git clone https://github.com/wactax/ops.soft.git
```

## Bosala certificat ya SSL ya ofele mpo na kombo ya domaine na yo

Kotinda mail esengaka certificat SSL mpo na encryption mpe ko signer.

Tosalelaka [acme.sh](https://github.com/acmesh-official/acme.sh) mpo na kobimisa ba certificats.

acme.sh ezali esaleli ya kotia maboko na certificat automatique ya source ouverte, .

Kota na entrepôt ya configuration ops.soft, tambuisa `./ssl.sh` , mpe dossier `conf` ekosalama na **répertoire ya likolo** .

Luka mopesi ya DNS na yo uta na [acme.sh dnsapi](https://github.com/acmesh-official/acme.sh/wiki/dnsapi) , bongisa `conf/conf.sh` .

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/Qjq1C1i.webp)

Na sima tambuisa `./ssl.sh 123.com` mpo na kobimisa ba certificats `123.com` mpe `*.123.com` mpo na kombo ya domaine na yo.

Kosala ya liboso eko installer automatiquement [acme.sh](https://github.com/acmesh-official/acme.sh) mpe ekobakisa mosala oyo ebongisami mpo na renouvellement automatique. Okoki komona `crontab -l` , ezali na ligne ya boye boye.

```
52 0 * * * "/mnt/www/.acme.sh"/acme.sh --cron --home "/mnt/www/.acme.sh" > /dev/null
```

Nzela mpo na certificat oyo esalemi ezali eloko lokola `/mnt/www/.acme.sh/123.com_ecc。`

Renouvellement ya certificat ekobenga script `conf/reload/123.com.sh` , editer script oyo, okoki kobakisa ba commandes lokola `nginx -s reload` pona ko refresher cache ya certificat ya ba applications oyo etali yango.

## Tongela serveur SMTP na chasquid

[chasquid](https://github.com/albertito/chasquid) ezali serveur SMTP ya source ouverte oyo ekomami na monoko ya Go.

Lokola esika ya ba programmes ya kala ya serveur ya mail lokola Postfix na Sendmail, chasquid ezali pete pe pete pona kosalela, pe ezali pe pete pona développement secondaire.

Run `./chasquid/init.sh 123.com` ekozala installé automatiquement na clic moko (remplacer 123.com na kombo ya domaine na yo ya kotinda).

## Configurer DKIM ya Signature ya Email

DKIM esalelamaka mpo na kotinda ba signature ya email mpo na kopekisa ete mikanda etalelama lokola spam.

Sima ya commande kosala malamu, bakosenga yo otia enregistrement ya DKIM (ndenge elakisami awa na se).

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/LJWGsmI.webp)

Bakisa kaka enregistrement ya TXT na DNS na yo (ndenge elakisami awa na se).

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/0szKWqV.webp)

## Tala état ya service & ba journals

 `systemctl status chasquid` Tala ezalela ya mosala.

Etat ya fonctionnement normal ezali ndenge elakisami na figure oyo ezali awa na se

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/CAwyY4E.webp)

 `grep chasquid /var/log/syslog` to `journalctl -xeu chasquid` ekoki kotala mokanda ya mabunga.

## Bozongisi sima configuration ya kombo ya domaine

Nkombo ya domaine inverse ezali mpo na kopesa nzela na adresse IP e résoudre na kombo ya domaine oyo ekokani.

Kobongisa kombo ya domaine inverse ekoki kopekisa ba emails ezala identifié lokola spam.

Tango mail ekozwama, serveur oyo eyambi ekosala analyse ya kombo ya domaine inverse na adresse IP ya serveur oyo etindi mpo na ko confirmer soki serveur oyo azali kotinda azali na kombo ya domaine inverse oyo ezali valide.

Soki serveur oyo azali kotinda azali na kombo ya domaine inverse te to soki kombo ya domaine inverse ekokani te na adresse IP ya serveur oyo azali kotinda, serveur oyo azali koyamba akoki koyeba email lokola spam to koboya yango.

Kota na [https://my.contabo.com/rdns](https://my.contabo.com/rdns) mpe configurer ndenge elakisami awa na se

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/IIPdBk_.webp)

Sima ya ko setting kombo ya domaine inverse, kobosana te ko configurer résolution ya forward ya kombo ya domaine ipv4 na ipv6 na serveur.

## Bobongisi kombo ya hôte ya chasquid.conf

Bobongola `conf/chasquid/chasquid.conf` na motuya ya kombo ya domaine ya sima.

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/6Fw4SQi.webp)

Na sima sala `systemctl restart chasquid` pona ko restart service.

## Sauvegarde conf na ebombelo ya git

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/Fier9uv.webp)

Par exemple, na sauvegarder dossier conf na processus na ngai moko ya github ndenge elandi

Salá liboso entrepôt privé

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/ZSCT1t1.webp)

Kota na répertoire conf pe tinda na entrepôt

```
git init
git add .
git commit -m "init"
git branch -M main
git remote add origin git@github.com:wac-tax-key/conf.git
git push -u origin main
```

## Bakisa motindi

kopota mbango

```
chasquid-util user-add i@wac.tax
```

Ekoki kobakisa motindi

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/khHjLof.webp)

### Verifier soki mot de passe etiamaki malamu

```
chasquid-util authenticate i@wac.tax --password=xxxxxxx
```

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/e92JHXq.webp)

Sima ya kobakisa mosaleli, `chasquid/domains/wac.tax/users` ekozala mise à jour, kobosana te kotinda yango na entrepôt.

## DNS bakisa enregistrement SPF

SPF ( Sender Policy Framework ) ezali technologie ya vérification ya email oyo esalelamaka pona kopekisa fraude ya email.

Ezali ko vérifier identité ya motindi ya mail na ko vérifier soki adresse IP ya motindi ekokani na ba enregistrements DNS ya kombo ya domaine oyo elobi que ezali, kopekisa ba escrocs batinda ba email ya lokuta.

Kobakisa ba enregistrements SPF ekoki kopekisa ba emails ezala identifié lokola spam na ndenge ekoki.

Soki serveur ya kombo ya domaine na yo esimbaka type SPF te, bakisa kaka enregistrement ya type TXT.

Ndakisa, SPF ya `wac.tax` ezali boye

`v=spf1 a mx include:_spf.wac.tax include:_spf.google.com ~all`

SPF mpo na `_spf.wac.tax`

`v=spf1 a:smtp.wac.tax ~all`

Notez que naza na `include:_spf.google.com` awa, yango ezali po nako configurer `i@wac.tax` lokola adresse ya envoi na boîte postale ya Google sima.

## Bobongisi ya DNS DMARC

DMARC ezali mokuse ya (Authentification, Rapport & Conformance ya message basé na domaine).

Esalemaka pona kokanga ba rebonds ya SPF (peut-être ewutaka na ba erreurs ya configuration, to mutu mususu azali kosala lokola yo pona kotinda ba spam).

Bakisa enregistrement ya TXT `_dmarc` , .

Makambo oyo ezali na kati ezali boye

```
v=DMARC1; p=quarantine; fo=1; ruf=mailto:ruf@wac.tax; rua=mailto:rua@wac.tax
```

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/k44P7O3.webp)

Ndimbola ya paramètre moko na moko ezali boye

### p (Politiki) .

Ezali kolakisa ndenge ya kosimba ba email oyo elongi te SPF (Sender Policy Framework) to DKIM (DomainKeys Identified Mail) vérification. Paramètre p ekoki kozala na moko ya ba valeurs misato:

* moko te: Action moko te esalemi, kaka résultat ya vérification nde ezo zongisama na motindi na nzela ya mécanisme ya rapport ya email.
* Quarantine: Tia mail oyo eleki vérification te na dossier ya spam, kasi ekoboya mail directement te.
* koboya: Boya directement ba email oyo elongi te vérification.

### fo (Ba options ya échec) .

Ezali kolakisa motango ya sango oyo ezongisami na mécanisme ya rapport. Ekoki kozala na moko ya ba valeurs oyo elandi:

* 0: Lakisa ba résultats ya validation pona ba messages nionso
* 1: Kaka kopesa lapolo ya ba messages oyo elongi te vérification
* d: Koyebisa kaka ba échecs ya vérification ya kombo ya domaine
* s: kaka kopesa sango ya ba échecs ya vérification ya SPF
* l: Koyebisa kaka ba échecs ya vérification ya DKIM

### rua & ruf

* rua (Kopesa lapolo ya URI mpo na balapolo ya kosangisa): Addresse ya email mpo na kozwa balapolo oyo esangisi
* ruf (Kopesa lapolo ya URI mpo na balapolo ya Forensic): adresi ya email mpo na kozwa balapolo ya sikisiki

## Bakisa ba enregistrements MX pona kotinda ba emails na Google Mail

Lokola nakokaki te kozwa boîte postale ya ofele ya entreprise oyo esimbaka ba adresses universelles (Catch-All, ekoki kozwa ba emails nionso oyo etindami na kombo ya domaine oyo, sans restrictions na ba préfixes), nasalelaki chasquid mpo na kotinda ba emails nionso na boîte postale na ngai ya Gmail.

**Soki ozali na boîte postale na yo moko ya mombongo oyo efutami, svp ko modifier MX te mpe sauter étape oyo.**

Bobongisi `conf/chasquid/domains/wac.tax/aliases` , botia boîte postale ya kotinda

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/OBDl2gw.webp)

`*` elakisi ba email nionso, `i` ezali préfixe ya adresse email ya mosaleli oyo azali kotinda oyo esalemi likolo. Mpo na kotinda mikanda, mosaleli mokomoko asengeli kobakisa molɔngɔ.

Na sima bakisa enregistrement MX (na pointer directement adresse ya kombo ya domaine inverse awa, ndenge elakisami na ligne ya liboso na figure oyo ezali awa na se).

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/7__KrU8.webp)

Sima ya configuration esili, okoki kosalela ba adresses email misusu pona kotinda ba email na `i@wac.tax` pe `any123@wac.tax` pona kotala soki okoki kozua ba email na Gmail.

Soki te, tala journal ya chasquid ( `grep chasquid /var/log/syslog` ).

## Tinda email na i@wac.tax na Google Mail

Nsima ya Google Mail kozwa mail, na lolenge ya bomoto nazalaki na elikya ya koyanola na `i@wac.tax` na esika ya i.wac.tax@gmail.com.

Kota na [https://mail.google.com/mail/u/1/#settings/accounts](https://mail.google.com/mail/u/1/#settings/accounts) mpe finá "Bakisa adresse email mosusu".

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/PAvyE3C.webp)

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/_OgLsPT.webp)

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/XIUf6Dc.webp)

Na nsima, kotia code ya vérification oyo ezwaki na email oyo batindaki.

Na nsuka, ekoki kozala lokola adresse ya motindi ya liboso (elongo na option ya koyanola na adresse moko).

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/a95dO60.webp)

Na ndenge wana, tosilisi kosala serveur ya mail SMTP mpe na tango moko tosaleli Google Mail mpo na kotinda mpe kozwa ba email.

## Tinda email ya test mpo na kotala soki configuration elongi

Bokota na `ops/chasquid`

Tambwisa `direnv allow` ko installer ba dépendances (direnv e installé na processus ya initialisation ya clé moko ya kala pe crochet ebakisami na shell)

na nsima kima mbangu

```
user=i@wac.tax pass=xxxx to=iuser.link@gmail.com ./sendmail.coffee
```

Ndimbola ya ba paramètres ezali boye

* mosaleli: Nkombo ya mosaleli ya SMTP
* koleka: Mot ya nzela ya SMTP
* na: mozwi

Okoki kotinda email ya test.

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/ae1iWyM.webp)

Ezali malamu kosalela Gmail mpo na kozwa ba email ya komeka mpo na kotala soki ba configurations elongi.

### Encryption standard ya TLS

Ndenge emonisami na elilingi oyo ezali awa na nse, ezali na mwa eloko oyo ya kokanga, oyo elakisi ete mokanda ya SSL efungolami malamu.

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/SrdbAwh.webp)

Na sima finá na "Lakisa Email Original".

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/qQQsdxg.webp)

### DKIM

Ndenge emonisami na elilingi oyo ezali awa na nse, lokasa ya mail ya ebandeli ya Gmail elakisaka DKIM, elingi koloba ete configuration ya DKIM elongi.

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/an6aXK6.webp)

Tala Received na motó ya likambo ya email ya ebandeli, mpe okoki komona ete adresse ya motindi ezali IPV6, elingi koloba ete IPV6 mpe e configuré malamu.

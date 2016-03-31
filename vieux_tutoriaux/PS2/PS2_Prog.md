<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=iso-8859-1">
<title>Préparations des outils pour programmer la PS2(C)</title>
<meta author="LXS">
<meta description="Comment programmer la Playstation 2 ?">
</head>
<body>

<center>
<h1>Développer sur Playstation 2<br/></h1>
<tt>Par <a href="mailto:linuxshell@wanadoo.fr">Linuxshell</a></tt>
<br/><br/>
<table border=0 cellspacing=0 align="center" width=50%>
<td align="center">
<p>Pendant bien longtemps beaucoup de personnes pensaient que le développement sur cette console n'était possible qu'avec la solution payant qu'était le Kit de Développement PS2 Linux.</p>
<p>Bien que peu honéreux pour un logiciel de la sorte, il en était tout de même payant. Il fallu alors se diriger vers une solution gratuite, et dans ce domaine l'utilsation de GCC est absolument parfaite.</p>
<p>Comparé au kit de développment officiel, il va falloir être plus expérimenté pour maîtriser ne serait-ce que la préparation des outils, mais l'avantage d'une telle solution repose essentiellement sur la maintenabilité et l'évolutivité des outils GNU.</p>
</td>
</table>
</center>
<br/><br/><br/>
<table align=center bgcolor="gold" border=0 cellspacing=0>
<td>
<center><b>[Disclaimer On]</b></center><br/>
Bien que nous essayons d'assurer la fiabilité de l'information figurant sur cette page,<br/>
l'auteur (et l'hébergeur) ne peut être tenu pour responsable de quelque perte, dommage<br/>
ou désagrément provoqué par le fait d'une erreur, d'une inexactitude ou d'une omission<br/>
figurant sur ces pages.<br/>
<p>Remerciement: Thorsten Titze, emulation.fr et fr.comp.emulateurs.</p>
<b><center>[Disclaimer Off]</center></b><br/>
</td>
</table>
<br/><br/><br/>
<h3><u><font color="#1111ee">Table des matières</u></font></h3>
<ol type="i">
    	<li>Introduction;</li>
    	<li>Construction des outils pour le processeur Emotion Engine(EE - R5900);</li>
    	<li>Construction des outils pour le processeur IOP (R3000);</li> 
		<li>Construire l'assembleur VU;</li>
		<li>Compiler PSX2LIB;</li>
		<li>Tester les librairies et le compilateur;</li>
		<li>Ecrire, compilation, puis test d'un premier programme;</li>
		<li>Création d'un CD;</li>
    </ol>
<p><li>Prologue: Discussion sur la légalité.</p>

<h4>Introduction</h4>
<p>Tout d'abord afin de pouvoir programmer sur PS2 il va falloir construire les outils pour les deux processeurs de la console, soit l'Emotion Engine et l'Input/Ouput Processor. Pour cela, comme à l'accoutumée, il va faloir installer l'environnement de développement Cygwin. Nous pourrons ainsi compiler dans un environnement "Unix-like" sous WIndows, GCC et BINUTILS.</p>
<p>Pour cela téléchargez <a href="http://www.cygwin.com/setup.exe">Setup.Exe</a> et sélectionnez ce que vous voulez(les plus feignant ou les nouveaux, prennez tout! :))</p>
<p>Installez tout, mais prennez lez sources, car nous devrons recompiler quelques packages.</p>
<p>Téléchargez également ces 2 archives, car les plateformes cibles des outils ne sont pas disponibles par défaut pour les processerus EE et IOP.<br/>
<a href="http://ps2dev.sourceforge.net/newsite/downloads/ee/gnu-ee-binutils-gcc-1.1.tar.gz">Package 1</a><br/>
<a href="ftp://ftp.cs.tu-berlin.de/pub/gnu/gettext/gettext-0.10.35.tar.gz">Package 2</a><p/>
<br/>
<p><b>Construction des outils pour le processeur Emotion Engine(EE - R5900);</b></p>
<p>Après l'installation de Cygwin, nous devons construire le cross-compiler pour le processeur EE de la PS2.<br/> 
Préparons la structure des répertoires. Démarrez le shell de Cygwin et tapez:<br/>
<b>$ cd /<br/>
$ mkdir GCC<br/>
$ cd GCC<br/>
$ mkdir build-gcc<br/></b>
<br/>
Copiez les sources dans le répertoire GCC, puis tapez pour les .tar.gz:<br/>
<b>$ gunzip nom_du_fichier.tar.gz<br/></b>
et pour les .tar.bz2:<br/>
<b>$ bunzip2 nom_du_fichier.tar.bz2<br/></b>
<br/>
Suivis ensuite de:<br/>
<b>$ tar xf nom_du_fichier.tar</b> <br/>
<br/>
Vous devriez obtenir 3 dossiers, build-gcc, gnu-ee-binutils-gcc et gettext-0.10.35.<br/>
<br/>
Avant de compiler EE-GCC, nous devons compiler gettext, librairie essentielle à la compilation.<br/>
Pour cela, tapons:<br/>
<b>$ cd /gcc/gettext-0.10.35<br/></b>
A noter que les nouvelles versions ne sont pas toujours les plus propices au développement car certaines ne compileront pas sur notre environnement.<br/>
<br/>
Configurons à présent notre makefile à l'aide de ./configure:<br/>
<b>$ ./configure --prefix=/usr --with-included-gettext<br/></b>
puis compilons:<br/>
<b>$ make<br/></b>
<br/>
Vérifions notre compilation et installons les librairies et entêtes afin que le compilateur les trouve:<br/>
<b>$ aliaspath='/usr/share/locale:/usr/local/share/locale'<br/>
$ make check<br/>
$ make install-strip <br/></b>
<br/>
A présent libintl.a est dans le sous-réperoire intl, car il est requis pour la compilation de GCC.<br/>
</p> 
<p>
Afin d'être parfaitement en accord avec le fonctionnement de la PS2, éditez /gcc/gnu-ee-binutils-gcc/ld/emulparams/elf32l5900.sh:<br/>
<br/>
Ligne 5:<br/>
<b>Changez: TEXT_START_ADDR=0xa0020000<br/>
en: TEXT_START_ADDR=0x00100000 <br/></b>
<br/>
Dans /gcc/gnu-ee-binutils-gcc/ld/<i>ldmain.c</i>, ligne 22:<br/>
<b>Changez: #include "emul-ops.h"<br/>
en: // #include "emul-ops.h"<br/></b>
<br/>
Dans /gcc/gnu-ee-binutils-gcc/gas/<i>listing.c</i> ligne 381:<br/>
<b>Change: if (__isascii (c) && ! iscntrl (c))<br/>
en: if (isascii (c) && ! iscntrl (c)) <br/></b>
<br/>
Initialisons les variables d'environnment:<br/>
<br/>
<b>$ target=mips64r5900-sce-elf<br/>
$ prefix=/usr/local/ps2/$target<br/>
$ binaries=$prefix/bin<br/>
$ PATH=$binaries:$PATH<br/></b>
<br/>
Commençons la compilation:
<b>$ cd /gcc/build-gcc</b><br/>
<br/>
Puis copions notre fichier libintl.a déjà compilé:<br/> 
<b>$ mkdir intl<br/>
$ cp ../gettext-0.10.35/intl/libintl.a intl/</b><br/>
<br/>
Configurons le makefile afin qu'il soit correctement initialisé sur le processeur de destination et les chemins:<br/>
<b>$ ../gnu-ee-binutils-gcc/configure --target=$target --prefix=$prefix </b><br/>
<br/>
Et c'est parti pour la compilation!<br/>
<b>$ make all install</b><br/>
Nous avons désormais GCC cross-compilé pour le processeur Emotion Engine de la PS2 présent dans usr/local/ps2/mips64r5900-sce-elf/bin.<br/>
</p>
<p>NOTE: pour quelques outils il est recommandé de créer quelques liens symboliques(spécialement pour les makefiles qui attent les outils EE comme ee-gg ou ee-ar).<br/>
Ainsi allons dans le répertoire "target" (dans ce tutorial /usr/local/ps2/mips64r5900-sce-elf) et tapons:
<b>$ ln -s mips64r5900-sce-elf-gcc ee-gcc <br/>
$ ln -s mips64r5900-sce-elf-ar ee-ar <br/>
$ ln -s mips64r5900-sce-elf-ld ee-ld <br/></b>
<br/>
Au cas où ce ne seraient pas assez vous pourriez lier tous les autres outils pour r5900 de même ! :)<br/>
</p>  
<p>En cas de problèmes, il s'agit probablement de la variable sys_errlist(déclarée 2 fois), pour cela éditez /usr/include/sys/errno.h:<br/>
Cherchez la variable et commentez afin d'obtenir ceci:<br/>
<b>/* Please don't use these variables directly.<br/>
Use strerror instead. *</br/>
// extern __IMPORT _CONST char * _CONST _sys_errlist[];<br/>
extern __IMPORT int _sys_nerr;<br/>
#ifdef __CYGWIN__<br/>
// extern __IMPORT const char * const sys_errlist[];</b><br/>
</p>
<br/>
<p><b>Construction des outils pour le processeur IOP (R3000);</b></p>
Ici la compilation pour l'Input/Output Processor est quasiment la même que pour la chaîne d'outils du EE. Nous ne nous occuperons pas du fichier<br/>
libintl.a puisqu'il est déjà là où il devrait être. Si vous avez déjà construit la chîne pour l'EE, vous commencez à être habitué! :)<br/>
<br/>
Attention! Changez bien le target surtout! Sinon vous gâcheriez votre première compilation...Et croyez moi rie n'est plus rageant! :)<br/>
<b>$ target=mipsel-scei-elfl<br/>
$ prefix=/usr/local/ps2/$target<br/>
$ binaries=$prefix/bin<br/>
$ PATH=$binaries:$PATH</b><br/>
<br/>
Allons dans le répertoire de GCC:<br/>
<b>$ cd /gcc/build-gcc<br/><br/></b>
Si vous venez de compiler pour l'EE, faites un petit nettoyage:<br/>
<b>$ make clean<br/></b>
Configurons le makefile afin qu'il soit correctement initialisé sur le processeur de destination et les chemins:<br/>
<b>$ ../gnu-ee-binutils-gcc/configure --target=$target --prefix=$prefix </b><br/>
<br/>
Et encore et toujours:<br/>
<b>$ make all install</b><br/>
C'est au tour du compilateur GCC pour l'Input/Output Processor d'être disponible dans /usr/local/ps2/mipsel-scei-elfl/bin.<br/>
<br/>
Pour la suite c'est comme pour l'EE(cf explications :))<br/>
<b>$ ln -s mipsel-scei-elfl-gcc iop-gcc <br/>
$ ln -s mipsel-scei-elfl-ar iop-ar <br/>
$ ln -s mipsel-scei-elfl-ld iop-ld <br/></b>
<br/>
Et vous pouvez encore lier tous les outils au pire :D<br/>

<p><b>Construire l'assembleur VU;</b></p>
Maintenant que les deux processeurs sont utilisables par GCC, il nous manque toujours le support de l'assembleur, utilisé par le VU.<br/>
Le VU est le Vector Units. Nous devons faire une petite modification dans un fichier de configuration pour activer la compilation les sources pour le DVP.<br/>
<br/>
Ouvrez <i>config.sub</i> dans le répertoire /gcc/gnu-ee-binutils-gcc:<br/>
<br/>
Recherchez les lignes:<br/>
<br/>
<b># Recognize the basic CPU types without company name.<br/>
# Some are omitted here because they have special[...]<br/>
tahoe | i860 | ia64 | m32r | m68k | m68000[...]<br/></b>
et remplacez par:<br/>
<b># Recognize the basic CPU types without company name.<br/>
# Some are omitted here because they have special[...]<br/>
tahoe <i>| dvp </i>| i860 | ia64 | m32r | m68k | m68000[...]<br/></b>
A noter qu'il faut ajouter la partie en italique.<br/>
<br/>
Changeons maintenant les variables comme tout à l'heure:<br/>
<b>$ target=dvp-elf<br/>
$ prefix=/usr/local/ps2/$target<br/>
$ binaries=$prefix/bin<br/>
$ PATH=$binaries:$PATH<br/></b>
<br/>
<b>$ cd /gcc/build-gcc<br/></b>
<br/>
Maintenant pour chaque répertoire, dont voici le listing:<br/>
 - libiberty<br/>
 - bfd<br/>
 - opcodes<br/>
 - binutils<br/>
 - ld<br/>
 - gas<br/>
<br/>
tapez après être entré dedans à l'aide de la commande "cd dossier_en_question":<br/>
linkons le répertoire de libintl.a: <b>$ ln -s ../intl<br/></b>
puis configurons le avec: <b>$ ../../gnu-ee-binutils-gcc/configure --target=$target --prefix=$prefix</b><br/>
<br/>
Puis créeons tout:<br/>
<b>$ make all install</b><br/>
<br/>
Il faut que nous fassions cela pour chacun des dossiers sus-cités.<br/>
A noter que rien ne vous empêche de faire un script qui automatiserait tout cela.<br/>
<p>Maintenant nous avons un assembleur fonctionnel pour VU0/VU1 présent dans /usr/local/ps2/dvp-elf/bin.</p>
<br/>
<br/>
<p><b>Compiler PSX2LIB;</b>
Maintenant que nous pouvons tout faire avec nos compilateurs et assembleur, il est temps de passé à ce pour quoi nous avons mis<br/>
temps de temps à configurer: la programmation! Et pour cela nous avons besoin de PSX2LIB<br/>
<br/>
La première chose à faire est de downloader les packages :)<br/>
<a href="http://linuxshell.free.fr/tutoriaux/PS2/psx2lib-1.0b-20020405.tar.bz2">Téléchargez-les!</a><br/>
<br/>
Les destinations devraient déjà être présente, mais au cas où ajoutez ces lignes à votre <i>.bash.rc</i> ou <i>.profile</i> dans votre /home/utilisateur:<br/>
<b>target=mipsel-scei-elfl<br/>
prefix=/usr/local/ps2/$target<br/>
binaries=$prefix/bin<br/>
PATH=$binaries:$PATH</b><br/>
<br/>
Copier psx2lib-1.0b-2002xxxx.tar.bz2 dans un répertoire de votre choix (comme /home/utilisateur/ps2dev);<br/>
Désarchivez comme ceci:<br>
<b>$ tar -jxf psx2lib-1.0b-2002xxxx.tar.bz2<br/></b>
<br/>
Puis allez dans le répertoire ainsi crée.<br/>
<br/>
Changeons le path de la racine de PSX2LIB. Editez <i>environ-ee.sh</i>(également à faire avec environ-iop.sh) :
<b>export PSX2LIB_BASE="~/ps2dev/psx2lib-1.0b" </b><br/>
<br/>
Maintenant lançons notre script:<br>
<b>$ . ./environ-ee.sh<br></b>
Le "." devant est essentiel puisqu'il permettra au script de se répéeter automatiquement à chaque lancement du shell.<br>
<br>
Compilons désormais la partie de la librairie pour le EE:<br>
<b>$ make<br></b>
Exécutons maintenant l'environnement IOP:<br>
<b>$ . ./environ-ee.sh<br></b>
Compilons désormais la partie de la librairie pour l'IOP:<br>
<b>$ make<br></b>
<br>
Nous avons désormais une libraire de développement GNU compilée pour le processeur Emotion Engine de la PS2.<br>
<br>
Dès que vous voulez utiliser la librairie dans votre shell pour la compilation EE vous devez appeller le script<i> environ-ee.sh</i><br>
et environ-iop.sh pour l'IOP!<br>
</p><br/>
<br/>
<p><b>Tester les librairies et le compilateur;</b>
<br>Pas encore :)

</p><br/>
<br/>
<p><b>Ecrire, compilation, puis test d'un premier programme;</b>
<br>Pas encore :)


</p><br/>
<br/>
<p><b>Création d'un CD;</b>
<br>Pas encore :)
</p>
</body>
</html>

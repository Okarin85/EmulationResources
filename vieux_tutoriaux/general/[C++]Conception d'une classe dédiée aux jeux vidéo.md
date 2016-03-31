<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.0 Transitional//EN">
<HTML>
<HEAD>
	<META HTTP-EQUIV="CONTENT-TYPE" CONTENT="text/html; charset=windows-1252">
	<META name="Author" content="Franck 'LXS' Verrot rooATdev-fr.com">
	<TITLE>Conception d'une classe dédiée aux jeux vidéo</TITLE>
</HEAD>
<BODY>
<p><center><h2>Conception d'une classe dédiée aux jeux vidéo</h2></center></p>
<P>
<i><b>Introduction:</b><br/>
Bon nombre de programmeurs, débutants ou confirmés, voulant se lancer dans la programmation de jeux vidéo se demandent quel(s) langage(s) utiliser pour construire leur première création. Et les réponses typiques présentes sur les forums sont à la fois les plus concrètes et les plus floues. En effet, les conseils systèmatiques de l'utilisation des langages C++ et/ou Java tirent généralement leurs fondements sur les expériences de programmeurs les plus aguéris; sans pour autant se fonder sur des choses réellement concrètes telles que l'encapsulation des données, les héritages multiples, la protection des membres et surtout la modélisation parfaite d'un monde concret. C'est à ces manques d'explications que cet article tentera de répondre, en essayant de fournir les exemples les plus complets pour que l'apprentissage ne se limite pas à la théorie car l'application des concepts abstraits, la pratique, permet de maîtriser plus aisément la théorie.</i>
</P>
<p>&nbsp;</p>
<P><h3>1.0 Les bases</h3>
Pour assimiler cet article ainsi que les notions qu'il embarque, vous devrez connaître les concepts des classes. Alors voici un petit rappel sur ceux-ci.<br/>
Une classe est regroupement de données, variables et fonctions s'appliquant à ce type, car les classes sont bel et bien des types. A l'intérieur de ces classes plusieurs propriété sont disponibles pour les données membres:

<ul>
<li><i>private</i>:donnée réservée aux fonctions membres  de la même classe, ainsi qu'aux fonctions déclarées comme <i>amies</i>(mot clef <b>friend</b>). Le fait de protéger d'une telle manière les données s'appelle <b>l'encapsulation</b>, une sorte de barrière qui cloisonnera les accès aux données membres.</li>
<li><i>protected</i>: de la même manière que <i>private</i>, sauf que les accès sont permis depuis les fonctions membres et friend des classes dérivées, donc basées sur celle-ci.</li>
<li><i>public</i>: accès total à la donnée/fonction membre, ainsi la donnée peut être manipulée depuis n'importe.</li>
</ul>

Une classe étant un type, sa déclaration se faire de la même manière. Imaginons une classe "CVoiture" (le "C" précédent le nom de la classe est une mnémonique permettant de se rappeler facilement que c'est une classe), alors un objet CVoiture se déclare: <i>CVoiture voiture;</i><br/>
<br/>
Une sorte de classe CVoiture pourrait être:<br/>
<pre>
// CVoiture.h
class CVoiture
{
private:
	int compteur;
protected:
	int essence;
	int huile;
public:
	int portes[4]; // 0 si fermée et 1 si ouverte
	// Constructeur
	CVoiture();
	// Desctructeur
	~CVoiture();


	void roule();
	void manipuler_porte(int porte);
};		

// CVoiture.cpp
CVoiture::CVoiture()
{
this->compteur= 0;
this->essence = 30; // le plein d'une twingo :)
}

void CVoiture::roule()
{
if(this->essence <= 0)
	return -1;
this->compteur++;
this->essence--;
}

void CVoiture::manipuler_porte(int porte)
{
this->portes[porte] ^= 1;
}

// main.cpp
#include "CVOiture.h"

int main(void)
{
	CVoiture *voiture = new CVoiture();
	while(-1 != voiture->roule())
	cout&lt;&lt;"Vroum vroum!"&lt;&lt;endl;
	return 0;
}
</pre>
<br/>
Comme vous pouvez le constater, on essaye de reproduire le plus fidèlement possible ce qu'il se passe dans la *vraie* vie, avec un compteur que l'on ne peut pas manipuler, de l'huile et de l'essence que vous seul pouvez manipuler, puis des portes que tout le monde peut manipuler. La voiture peut se conduire par tous, et la fonction roule() permet de simuler (avec un niveau d'abstraction toute fois assez élevé ;)) la voiture qui roule...
</P>
<P><h3>2.0 Optimisations des classes</h3>
En effet, ici nous venons de créer une classe assez légère ne comportant pas réellement de difficulté à mettre en place, mais lorsque nous voulons programmer un jeu, il faudra faire face à de multiples sprites et images, ainsi que des formes(carrés, triangles, cercles etc...) à dessiner, créer, donc manipuler.<br/>
On va donc faire appel ici aux fonctions purement virtuelles et à l'héritage multiple, qui va réellement nous changer la vie. En effet, nous allons pouvoir créer un objet global qui définira les points communs des autres objets à créer(coordonnées par exemple), tout en spécifiant que des fonctions doivent être définies plus tard pour préciser les spécificités du futur objet.<br/>
On utilise alors le mot clef <b>virtual</b> ainsi que "=0" à la fin de la déclaration de la fonction virtuelle pure, de ce fait notre classe change de nom, et s'appelle classe abstraite. Le "=0" ne signifie pas que la fonction est nulle mais uniquement que son implémentation se fera plus tard, dasn une de ses dérivées. Ainsi en théorie une fonction virtuelle pure n'exigera aucune déclaration dans aucune classe, pas même dans la classe dans laquelle elle est déclarée. On peut toutefois la définir mais aucun intéret vu que l'on souhaite bel et bien la définir de multiples fois.<br/> 
<b>"Mais alors qu'est-ce qu'une classe abstraite?"</b> me direz-vous. Et bien c'est une classe qui ne peut être instanciée directement et qui nécessitera alors une dérivation. Et c'est tout à faire ce que nous voulons faire: une classe de base qui définit des paramètres communs aux objets, sans pour autant les brider quand à leurs possibilités futures.<br/>
L'héritage signifie que la classe finale(celle qui résulte de la dérivation) est une "sorte" de la classe de base, qui inclut des spécificités. Ainsi on pourrait envisager une dérivation de la classe CVOiture en une classe CTwingo, déclarée telle que:
<pre>
class Cvoiture { ... };
class CTwingo: public CVoiture { ... };
</pre>
car une Twingo est une "sorte" de voiture(si si, et j'en ai une alors vous moquez pas). On peut donc dériver à volonté , pour toutes les voitures existantes. On porurait rajouter pour chaque dérivée une donnée membre "constructeur" par exemple...<br/>
L'héritage multiple quand à lui est utilisé pour spécifier un objet qui regroupe deux classes de base, on verra plus tard comment utiliser ce concept.
</p>

<p><h3>3.0 Première implémentation</h3>

Aller on reprend notre code en changeant quelques choses, attention toutefois, de nombreux concepts sont utilisés et s'entremêlent, si vous ne comprennez pas tout ce n'est pas grave, lisez l'intégralité puis relisez par la suite ;)

<pre>
=========snip=========snip=========snip=========snip=========snip======
// classes.h
/************************
 *						*
 * Entete des classes	*
 *						*
 ************************/

#include <iostream> // E/S
using namespace std; // pour éviter les std::cout etc...

//////////////////////////////////////////
// Classe CObjet
//////////////////////////////////////////
class CObjet
{
protected:
		int x,y;
public:
		virtual void identifies_toi() = 0;
		virtual void dessines_toi() = 0;
		void coordonnees() { cout&lt;&lt;"x="&lt;&lt;this->x&lt;&lt;" et y="&lt;&lt;this->y&lt;&lt;"!"&lt;&lt;endl; }
		virtual void places_toi(int x, int y) = 0;
};
//////////////////////////////////////////
//////////////////////////////////////////



//////////////////////////////////////////
// Classe CCarre
//////////////////////////////////////////
class CCarre : public CObjet
{
	// héritage de x et y pour la classe CCarre
	// nul besoin redéfinir ces coordonées
	private:
		int cote;
	public:
		CCarre();
		void identifies_toi();
		void dessines_toi();
		void places_toi(int x, int y);
};
//////////////////////////////////////////
//////////////////////////////////////////



//////////////////////////////////////////
// Classe CCercle
//////////////////////////////////////////
class CCercle : public CObjet
{
	// héritage de x et y pour la classe CCercle
	// nul besoin redéfinir ces coordonées
	private:
		int rayon;
	public:
		CCercle();
		void identifies_toi();
		void dessines_toi();
		void places_toi(int x, int y);
};
//////////////////////////////////////////
//////////////////////////////////////////



//////////////////////////////////////////
// Classe CTriangle
//////////////////////////////////////////
class CTriangle : public CObjet
{
	// héritage de x et y pour la classe CTriangle
	// nul besoin redéfinir ces coordonées
	private:
		int base;
		int hauteur;
	public:
		CTriangle(); // 
		void identifies_toi();
		void dessines_toi();
		void places_toi(int x, int y);
};
//////////////////////////////////////////
//////////////////////////////////////////

=========snip=========snip=========snip=========snip=========snip======



=========snip=========snip=========snip=========snip=========snip======
// classes.cpp

/************************
 *						*
 * Source des classes	*
 *						*
 ************************/

#include "classes.h"
//////////////////////////////////////////
// Classe CCarre
//////////////////////////////////////////
CCarre::CCarre()
{
	this->x = 0;
	this->y = 0;
	this->cote = 0;
}

void CCarre::identifies_toi()
{
	cout&lt;&lt;"Je suis un carré!"&lt;&lt;endl;
}

void CCarre::dessines_toi()
{
	
}

void CCarre::places_toi(int x, int y)
{
	this->x = x;
	this->y = y;
}
//////////////////////////////////////////
//////////////////////////////////////////



//////////////////////////////////////////
// Classe CCercle
//////////////////////////////////////////
CCercle::CCercle()
{
	this->x = 0;
	this->y = 0;
	this->rayon = 2;
}

void CCercle::identifies_toi()
{
	cout&lt;&lt;"Je suis un cercle!"&lt;&lt;endl;
}

void CCercle::dessines_toi()
{
	
}

void CCercle::places_toi(int x, int y)
{
	this->x = x;
	this->y = y;
}
//////////////////////////////////////////
//////////////////////////////////////////



//////////////////////////////////////////
// Classe CTriangle
//////////////////////////////////////////
CTriangle::CTriangle()
{
	this->x = 0;
	this->y = 0;
	this->base = 2;
	this->hauteur = 2;
}

void CTriangle::identifies_toi()
{
	cout&lt;&lt;"Je suis un triangle!"&lt;&lt;endl;
}

void CTriangle::dessines_toi()
{
	
}

void CTriangle::places_toi(int x, int y)
{
	this->x = x;
	this->y = y;
}
//////////////////////////////////////////
//////////////////////////////////////////
=========snip=========snip=========snip=========snip=========snip======



=========snip=========snip=========snip=========snip=========snip======
// main.h
/****************************************************************
*
*
* Entête principal avec définitions des principales classes
*
******************************************************************/

// Includes

#include "classes.h" // nos classes
=========snip=========snip=========snip=========snip=========snip======


=========snip=========snip=========snip=========snip=========snip======
// main.cpp

/************************
 *			*
 * Source principale	*
 *			*
 ************************/

#include "main.h"


// Point d'entrée du programme ne prennant aucun argument
int main(void)
{

	// Création d'une liste d'objets
	CObjet *objets[3] = { new CCarre(), new CCercle(), new CTriangle()};

	// Traitement de tous les objets de la même manière
	// Merci les fonctions virtuelles
	for(int i =0; i < 3; i++)
	{
		objets[i]->identifies_toi(); // je suis ...
		objets[i]->places_toi(1,2); // déplacement de l'objet
		objets[i]->coordonnees(); // affichage des coordonées
		objets[i]->dessines_toi(); // routine pour l'affichage graphique
	}

	// fin du programme
	return 0;
}
=========snip=========snip=========snip=========snip=========snip======
</pre>

Comme on peut le voir dans classes.h, objet comporte plusieurs fonctions virtuelles pures, mais même avec une seule, cette classe aurait été abstraite. On remarquer également que les données membres x et y sont protégées, car on estime que seules les fonctions autorisées (membres ou friend) ne doivent pouvoir le faire.<br/>
Ensuite 3 fonctions virtuelles pures viennent se greffer à la classe. Ces fonctions dépendent de l'implémentation que l'on aura faite dans la ou les classes dérivées, en effet, on carré n'étant pas un cercle, je vois mal comment on pourrait dessiner les deux de la même manière...<br/>
La fonction coordonnees() affiche les coordonées des objets, et cela indépendamment de l'implémentation de l'objet car les coordonnées sont définies comme <b>private</b>.<br/>
Ensuite dans classes.cpp nous pouvons voir l'implémentation des fonctions, déclarées dans la classe de base comme virtuelles pures, de chaque objet. Une simple sortie sur la console permet d'identifier les objets car ils se nommeront d'eux-mêmes, mais les fonctions de dessins prennant en compte la librairie graphique à utiliser celles-ci ne sont pas implémentées. Rien ne sert de vouloir afficher quelquechose si le concept basique n'est pas assimilé.
</p>

<p><h3>4.0 Utilisation de cette première implémentation</h3>
L'utilisation de ces concepts se fait dans main.cpp. On observe en début de programme une création d'un tableau de CObjets, 3 exactement, ayant chacun un type différent.<br/>
Ici objets[0] est un carré, objets[1] un cercle et objets[2] un triangle. Nous aurions très bien pu utiliser une liste chaînée pour faire encore mieux, la gestion de sprite par exemple pourrait en avoir besoin plus tard, mais libre à vous ensuite d'optimiser ce code.<br/>
La boucle principale du programme consiste à faire une série d'opération sur les objets, indépendamment de leur implémentation vu qu'on appelle les fonctions membres comme si l'on ne faisait face qu'à une seule implémentation.<br>
<br/>
objets[0]->identifies_toi() n'est pas la même que objets[1]->identifies_toi() pourtant elles sont appelées de manière identique.<br/>
L'affichage des coordonées étant le même pour toutes les classes dérivées de CObjet, on appelle bien CObjet::coordonnees().<br/>
</p>

<p><h3>4.0 Seconde implémentation</h3>
Maintenant que l'on sait dériver une classe de base et se servir des fonctions virtuelles pures, regardons de plus près l'héritage multiple, mécanisme réellement performant de ce langage.<br/>
Imaginons maintenant ceci:<br/>
<pre>
// Simple classe sur les philosophes
class CPhilosophes
{
public:
	int pensees;
	virtual void penser() = 0;
};
</pre>
On sait que Socrate est un philosophe qui n'a jamais rien écrit, et que son plus fidèle élève est Platon, c'est lui qui a transcrit toutes ses pensées.<br/>
<br/>
Ce qui nous donne:<br/>
<pre>
class CSocrate : CPhilosophes
{
public:
	void penser();	// pas besoin de la mettre virtuelle
	void parler();	// Socrate parlait ;)
};

class CPlaton : CPhilosophes
{
public:
	void penser(); // pas besoin de la mettre virtuelle
	void ecouter();	// Platon écoutait
};
</pre>

Jusque là rien de nouveau dans ces déclarations, mais si nous voulons instancier une classe sur le livre "L'apologie de Socrate" par Platon, nous devrons alors concevoir une classe qui est dérivée de CSocrate et de CPlaton.<br/>
<br/>
Ce qui nous donne:<br/>
<pre>
class CApologie : public CPlaton, public CSocrate
{
public:
	void penser(); // ne fera rien ou 
		       //appellera successivement 		       // CPlaton::penser() puis 
		       //CSocrate::penser(); au choix :)
	void transcrire();	//  Rapport de ce qui c'est dit 	
};
</pre>
Une simple instanciation de CApologie *apologie permet d'obtenir:<br/>
<pre>
apo->penser();	-> CApologie::penser();
apo->parler();	-> CSocrate::parler();
apo->ecouter();	-> CPlaton::ecouter();
</pre>
On récupère ainsi les fonctions membres publiques de chacune des classes de bases, ce qui s'avère fort utile.
</p>
<p>Mais que faire si l'on veut n'avoir qu'une seul objet partagé entre toutes les classes dérivées? Car prennons l'exemple d'un cas concret qu'est l'utilisation d'un fichier commun par deux objets, tous deux dérivés de la même classe de base, comment pourrions-nous manipuler ce fichier, sans pour autant instancier deux fois le même objet de la classe de base?
</p>
<p><h3>5.0 Classes de bases virtuelles</h3>
Le mot clef <b>virtual</b> peut servir à déclarer le type d'accès de la classe de base lors de la dérivation, en effet au lieu d'avoir:
<pre>
class C { ... };
class C1 : public C { ... };
</pre>
nous pourrions très bien avoir
<pre>
class C { ... };
class C1 : protected C { ... };
</pre>
donc par la même occasion
<pre>
class C { ... };
class C1 : public virtual C { ... }; // A noter que <b>virtual</b> peut se placer avant ou après <b>public</b>
</pre>
et pour aller au bout de la démarche
<pre>
class C { ... };
class C1 : protected virtual C { ... };
</pre>

sauf que pour la dérivation d'une classe de base, le mot clef <b>virtual</b> n'a pas la même signification. Il assure simplement une création unique d'un objet <i>C</i> dans C1 et toutes les autres classes dérivées de C. C'est donc grâce à ce mécanisme que nous allons tester cette instanciation unique.<br/><br/>
<pre>
=========snip=========snip=========snip=========snip=========snip======
// classes.h
#include "main.h"

class CLog
{
public:
	string fichier;
	virtual void ecrire() = 0;
	void affiche_fichier(){	cout&lt;&lt;this->fichier.c_str()&lt;&lt;endl;}
};

class CDaemonWeb : public virtual CLog
{
public:
	void ecrire();
	void choisir_fichier();
};

class CDaemonFtp : public virtual CLog
{
public:
	void ecrire();
	void choisir_fichier();
};

class CDaemon: public CDaemonWeb, public CDaemonFtp
{
public:
	void ecrire();
	void choisir_fichier();
};
=========snip=========snip=========snip=========snip=========snip======



=========snip=========snip=========snip=========snip=========snip======
// classes.cpp
#include "classes.h"

void CDaemonWeb::ecrire()
{
	this->fichier = "daemon web";
}

void CDaemonWeb::choisir_fichier()
{
	this->fichier = "daemon web";
}

void CDaemonFtp::ecrire()
{
	this->fichier = "daemon ftp";
}

void CDaemonFtp::choisir_fichier()
{
	this->fichier = "daemon ftp";
}


void CDaemon::ecrire()
{
	
}

void CDaemon::choisir_fichier()
{

}
=========snip=========snip=========snip=========snip=========snip======
</pre>
Voilà pour les principaux entêtes et définitions de classe. Et ci dessous le point d'entrée de l'exécutable qui pourra tester les accès et manipulations de l'objet partagé etc..

<pre>
=========snip=========snip=========snip=========snip=========snip======
//main.h
#include &lt;iostream>
using namespace std;
=========snip=========snip=========snip=========snip=========snip======



=========snip=========snip=========snip=========snip=========snip======
//main.cpp
#include "main.h"
#include "classes.h"

int main(void)
{
CDaemon *dede = new CDaemon();

dede->CDaemonWeb::fichier = "namespace CDaemon Web";
dede->CDaemonWeb::affiche_fichier(); // affiche "fichier choisi";

dede->CDaemonFtp::fichier = "namespace CDaemon Ftp";
dede->CDaemonFtp::affiche_fichier(); // affiche "fichier choisi";

dede->CDaemonWeb::fichier = "namespace heterogene CDaemonWeb et affichage CDaemonFtp";
dede->CDaemonFtp::affiche_fichier(); // affiche "fichier choisi";

dede->CDaemonFtp::fichier = "namespace heterogene CDaemonFtp et affichage CDaemonWeb";
dede->CDaemonWeb::affiche_fichier(); // affiche "fichier choisi";

dede->fichier = "abstraction";
dede->affiche_fichier(); // affiche "fichier choisi";

dede->CDaemonFtp::choisir_fichier();
dede->CDaemonWeb::affiche_fichier(); // affiche "daemon ftp";

//Etc etc...

return 0;
}
=========snip=========snip=========snip=========snip=========snip======
</pre>

Donc ici on teste de plusieurs façons, non exhaustives, les accès à la chaîne de caractères fichier. On ne s'attardera pas sur toutes, car il a un bon nombre de déclinaisons et de possibilités, de plus un simple dede->fichier = "quelquechose" suffit ou étendre la fonction choisir_fichier() à prendre un argument permet un contrôle total de l'objet...<br/>
On pourrait se demander à présent ce qu'il se passerait si l'héritage multiple a dériver deux fois la même classe(pour l'instant ce que l'on a fait), mais cette fois-ci une fois virtuellement et une autre fois normalement.<br/>
D'un point de vue complètement théorique c'est tout à fait possible, les classes de base dérivées d'un type de base virtuelle se partageront un sous objets unique, et chaque classe de base dérivée d'un type de base non virtuelle auront chacune leur objet...<br/>
<br/>D'un point de vue pratique et logique, on se demande bien comment on peut se rapprocher de la réalité en faisant un système si obscure, et qui de plus, est peu maintenable dans le futur, car le code devient réellement injustifié.
</p>
<p><h3>6.0 Conclusion</h3>
Comme nous venons de le voir à travers cette première approche, une classe parfaite n'existe pas, et pour simuler la réalité à travers une classe parfaite, il faudra dériver les classes de bases à l'infini...</p>

<p><b>Epilogue</b>
Voici une petite étude sur la programmation orientée objet, souvent dénommé POO (ou OOP en anglais), qui peut vous mettre sur la voie lorsque vous voudrez tirer partie de ce que sait faire le C++.<br/>
Toutefois il ne faut pas penser faire plus de miracles en C++/Java qu'en C si vous ne le maîtrisez pas assez, car nombre de jeux extraordinaires sont faits en C...<br/>
Disposer de l'outil ne signifie pas que l'on sait le manier...
</p>
<p><tt>07/2003 par Franck 'LXS' Verrot [rootAtdev-fr.com]</tt>
</p>

</BODY>
</HTML>

William Dubosq
Baptiste Jurand
4ETI

 # Compte-rendu TP2 Administration système

## Exercice 1 :
### Variables d'environnement

1. Les commandes de l'utilisateur sont stockées suivant le chemin d'accès suivant :
	> */usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin*

2. Sans argument dans la commande *cd*, c'est la valeur de la variable shell *HOME* qui est utilisée.

3. *LANG* permet de savoir la langue utilisée par l'utilisateur sur sa session et sur les logiciels.

	*PWD* donne le chemin d'accès jusqu'au répertoire.

	*OLDPWD* donne l'ancien chemin d'accès jusqu'au répertoire précédent le dernier changment de répertoire.

	*SHELL* renvoie le type de *shell* utilisé.

	*_* donne le dernier argument de l'exécution précédente.

4. On peut afficher la variable *MY_VAR* et son contenu avec les commande *echo*.

5. *bash* génère un nouvel invite de commande. La variable locale ne s'y trouve pas.

6. Quand on crée la variable avec *export*, elle devient une variable d'environnement. Les *bash* issus du *bash* d'origine héritent donc aussi de la variable. On peut donc utiliser *echo* sur elle dans ces *bash*.

7. Nous avons utilisé le code suivant :

	> *export NOMS="dubosq jurand"*

	> *printenv NOMS*

8. Nous avons utilisé le code suivant :

	> *echo "Bonjour à vous deux $NOMS"*

9. La commande *Unset* supprime la variable, elle ne supprime pas simplement sa valeur.

10. Nous avons utilisé le code suivant :

	> *echo '$HOME =' $(pwd)*


## Programmation Bash

## Exercice 2 :
### Contrôle de mot de passe

Nous avons utilisé le code suivant :

	#!/bin/bash

	PASSWORD=motdepasse
	read -s -p 'Saisissez votre mot de passe : ' mdp
	if [ $mdp = $PASSWORD]; then
		echo "correct"
	else
		echo "incorrect"
	fi

Le mot de passe est stocké dans la variable `PASSWORD`. `Read` permet d'afficher `read -p` et permet à l'utilisateur d'entrer son mot de passe. Ce qui est tapé est stocké dans `mdp` et est comparé à la valeur de `PASSWORD`.

## Exercice 3 :
### Expressions rationnelles

Nous avons utilisé le code suivant :

	#!/bin/bash
	function verif {
		re='^[+-]?[0-9]+([.][0-9]+)?$'
		if ! [[ $1 =~ $re ]] ; then
			return 1
		else
			return 0
		fi
	}

	verif $1
	variable = $?
	
	if [ $variable == 0 ] ; then
		echo "réel"
	else
		echo "ERROR : non-réel"
	fi

On utilise la variable `variable` dans laquelle on stocke la valeur de la fonction `verif()`. Si l'utilisateur entre un nombre réel, la fonction `verif()` renvoie 0, on envoie alors le message *'réel'*. Si le nombre n'est pas réel, `variable` vaut 1, on envoie alors un message d'erreur.

## Exercice 4 :
### Contrôle d'utilisateur

Nous avons utilisé le code suivant :

	#!/bin/bash

	if [ $# = 0 ]; then
		echo $0 "nom_utilisateur"
	else
		if [-z "$(cut -d: -f1 /etc/passwd | grep -x $1 )" ] ; then
			echo "utilisateur inexistant"
		else
			echo "utilisateur existant"
		fi
	fi

On compare la valeur de `$?` avec 1. En cas d'égalité, l'utilisateur est inexistant. Sinon il s'agit d'un utilisateur légitime.

## Exercice 5 :
### Factorielle

Nous avons utilisé le code suivant :

	#!/bin/bash
	
	function factorielle {
		nombre=$1
		if [ $nombre -eq 0 ]; then
			echo 1
		else
			echo $(( $nombre * factorielle $(( $nombre - 1 )) ))
		fi
	}
	echo "Résultat : $(factorielle $1)"

Nous avons ici utilisé la récursivité afin de trouver la valeur de la factorielle d'un nombre. Tant que ce nombre moins 1 de vaut pas zéro on rapplique la fonction `factorielle` au nombre inférieur.

## Exercice 6 :
### Le juste prix

Nous avons utilisé le code suivant :

	#!/bin/bash
	
	nombre=$(( $RANDOM % 1000 + 1 ))
	read -p 'TROUVEZ LE PRIX' essai
	#echo "$nombre"
	
	comparer() {
		if [ $essai-lt $nombre]; then
			read -p 'TROP' essai
			comparer(essai)
		elif [ $essai -gt $nombre]; then
			read -p 'PAS ASSEZ' essai
			comparer(essai)
		else
			echo "TROUVÉ!"
		fi
	}
	comparer(essai)

Avec `random`, on génère un nombre aléatoire `nombre`. L'utilisateur rentre des nombres et tant que ceux ci ne sont pas égaux à `nombre`, on rappelle la fonction en donnant au passage un indice (`TROP` ou `PAS ASSEZ` en fonction de ce qu'à rentré l'utilisateur).

## Exercice 7 :
### Statistiques

### *Q1*:

Nous avons utilisé le code suivant :

	#!/bin/bash

	for parametre in "$@"; do
		if [ $parametre -lt -100 ] || [ $parametre -gt 100 ] ; then
			echo "Tapez 3 nombres entre -100 et 100"
			exit
		fi
	done
	
	MOYENNE=$(( $(( $1 + $2 + $3 )) / 3 ))
	echo $MOYENNE
	
	MINIMUM=$1
	for parametre in "$@"; do
		if [ $parametre -lt $MINIMUM ] ; then
			MINIMUM=$parametre
		fi
	done
	echo $MINIMUM
	
	MAXIMUM=$1
	for parametre in "$@"; do
		if [ $param -gt $MAXIMUM ] ; then
			MAXIMUM=$parametre
		fi
	done
	echo $MAXIMUM

L'utilisateur rentre 3 nombres. La moyenne est facilement calculée. Pour ce qui est du minimum et du maximum, on part du principe que le premier nombre est le minimum (respectivement maximum) et on compare les paramètres suivants. Si l'un est plus petit (plus grand), alors c'est le nouveau minimum (maximum).

### *Q2*:

Nous avons utilisé le code suivant :

	#!/bin/bash

	for parametre in "$@"; do
		if [ $parametre -lt -100 ] || [ $parametre -gt 100 ] ; then
			echo "Tapez 3 nombres entre -100 et 100"
			exit
		fi
	done
	
	sum=0
	for parametre in "$@"; do
		sum=$((sum+$parametre))
	done
	echo $sum
	
	MOYENNE=$(( $(( $sum)) / $# ))
	echo $MOYENNE
	
	MINIMUM=$1
	for parametre in "$@"; do
		if [ $parametre -lt $MINIMUM ] ; then
			MINIMUM=$parametre
		fi
	done
	echo $MINIMUM
	
	MAXIMUM=$1
	for parametre in "$@"; do
		if [ $parametre -gt $MAXIMUM ] ; then
			MAXIMUM=$parametre
		fi
	done
	echo $MAXIMUM

Le code est ici adapté pour prendre en compte un plus grand nombre de paramètres. Le changements sont minimes, ils concernent surtout la moyenne car la somme est divisée par le nombre de paramètres.


### *Q3*:
/

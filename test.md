# [nextsim]_2022-04-15_wr.txt
#nextsim/wr

Weekly Report du 2022-04-15.

Table of content:
[[/1. Premiers pas avec nextsim (le code et l’environnement docker).]]
[[/2. Premiers pas avec le code de Sukun pour perturber le forçage et faire tourner des sinus d’ensemble]]
[[/3. SASIP]]
[[/4. Organisation de travail]]


---
### 1. Premiers pas avec nextsim (le code et l’environnement docker).
	* Point de départ: les 2 tutos vidéo d’Anton et Einar fait pour SASIP en juin 2021,
	* Mes notes  techniques détaillées ici: https://github.com/stephanieleroux/NEXTSIM/blob/main/2022-04-15_first_steps_with_nextsim.md
	* Bilan des avancées/problèmes: 
		* je me suis familiarisée avec Docker et la manière de construire le container pour y lancer nextsim.
		* j’ai pu faire tourner l’exemple simple (config idealizée) du Tuto d’Anton 
		* je n’ai pas réussi à utiliser les outils pynextsim en python avec l’image docker dispo actuellement: sur les conseils d’Anton  j’ai donc posté une issue GitHub à ce propos car il se peut que l’update très récente du package geodataset ne soit plus compatible avec l’image docker actuelle. ::*[En cours]*::.
		* je n’ai pas réussi à faire tourner l’exemple de config réaliste proposée par Einar dans le tuto SASIP (cf lien de notes techniques ci dessus). J’ai l’impression que l’image docker actuelle  et/ou le code de la branche develop actuelle n’est peut-être plus compatible avec  l’exemple de config d’Einar. J’ai demandé à Aurélie de voir de son côté si elle reproduisait l’erreur que j’obtiens. ::*[En cours]*::.
		* Plus généralement, je m’interroge sur la manière des gens NERSC de travailler avec nextsim. Où font-ils tourner leurs simulations?  Utilisent-ils docker eux même, ou bien est-ce que docker est suggérés pour les “extérieurs” seulement? Mes essais de cette semaine me donnent l’impression que: travailler dans un docker container est super pratique …seulement si l’image docker est à jour et compatible avec les mis à jours du code régulières, sinon on se retrouve vite coincés.  Je pense qu’il faut que j’apprenne vite à compiler le code (et l’environnement) moi même. Où faire ça,  c’est une autre question. Cal1? Gricad? Laptop?  Reflexion ::*[En cours]*::  	 
		 

		
---
### 2. Premiers pas avec le code de Sukun pour perturber le forçage et faire tourner des simus d’ensemble
	* Point de départ: tuto de Sukun:
	* Mes notes techniques détaillées ici: https://github.com/stephanieleroux/NEXTSIM/blob/main/2022-04-15_first_steps_with_Sukuns_code.md
	*  Bilan des avancées/problèmes: 
		* J’ai réussi à compiler et executer son code pour produire des fichiers de perturbation (à appliquer ensuite aux forçage lors d’un run d’ensemble).
		* Echange mail et visio avec Sukun pour quelques premières questions. Sukun m’a dit qu’il travaillait, en ce moment, à la demande d’Anton, à externaliser l’application des perturbations au forçage (hors nextsim) de manière à fabrique des forçages perturbés directement. Si il y arrive d’ici fin arrive, cela me faciliterait grandement la tâche.  ::*[En cours]*::
		* Me reste à comprendre et essayer de reproduire un run d’ensemble. Pour essayer il me faudrait partir d’une config réaliste exemple que j’arrive à faire tourner.  ::*[En cours]*:: (en lien avec  [[/1. Premiers pas avec nextsim (le code et l’environnement docker).]])
		* 
	
---
### 3. SASIP
	* Suivi des discussions du WP1
	* Test (successful) du VerySimpleModel sur mon laptop Mac: https://nextsim-dg.readthedocs.io/en/latest/installation.html . Voir avec Aurélie comment faire remonter les remarques pour améliorer cet exemple et ce tuto  ::*[En cours]*::.
	* Commencé à lire des tutos pour les bases du c++.  ::*[En cours]*::
	* Visu: Echange avec Jean-Baptiste Barré: son dernier exemple de vidéo sur l’antarctique pourrait peut-être être utile pour l’arctique aussi (cf slack). J’essayerai son tuto quand il sera disponible (Monder testait en premier ce jeudi 14/04).  ::*[En cours]*::
	* Echange avec Christine à propos de la TVA (cf message slack du 15/04).

---
### 4. Organisation de travail
	* Rappel: d’avril à décembre 2022 je suis à 70% SASIP et 30% IMHOTEP.
	* En moyenne je propose de m’organiser en travaillant le lundi-mardi-mercredi sur SASIP, le vendredi sur IMHOTEP, et le jeudi soit SASIP soit IMHOTEP selon les besoins du moment. Selon les besoins, une semaine entière pourrait aussi être consacrée uniquement à l’un ou l’autre des projets ponctuellement.
	* Pour info: pour datlas je comptabilise mes heures par projet.
	* Partage des calendriers timbra avec Pierre, où je rentre mes congés.
	* Possibilité de partager aussi mon calendrier gmail-boulot avec Pierre comme je le fais déjà avec Aurélie.
	* Fixe-t-on un moment hebdomadaire pour faire le point avec Pierre? Ou est-ce qu’on dit simplement qu’ajoute ça au point hebdomadaire Aurélie-Pierre?
	* Je vais aussi essayer de laisser une trace écrite (sorte de compte rendu) hebdomadaire de mes activités “SEA ICE” ici: 
	

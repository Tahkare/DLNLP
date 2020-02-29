# DLNLP
Le notebook "ftt" n'est pas commenté mais contient les modifications pour utiliser le fast embedding. Or, du à la taille du fichier qui contient le modèle pré-entrainé, il n'était pas possible de le mettre sur github

Pour exÃ©cuter notre code, il suffit de faire tourner le notebook.

Notre dataset est stockÃ© dans la classe Dataset de notre framework.
Cette classe peut prendre plusieurs arguments :
	- name (dÃ©faut = "") : 
		Le nom donnÃ© au dataset pour identifier les modÃ¨les sauvegardÃ©s
	- train_set, dev_set, test_set (dÃ©faut = None) :
		Les sets peuvent Ãªtre passÃ©s directement au format DataLoader
		Si ces arguments sont vides, il faut utiliser les 6 arguments suivants
	- train_data, dev_data, test_data (dÃ©faut = None) :
		Si les arguments au-dessus ne sont pas utilisÃ©s, on peut passer les donnÃ©es sous forme d'une liste.
		Chaque exemple doit Ãªtre passÃ© dans une liste mÃªme s'il ne s'agit que d'une seule valeur
		(ex : [[2],[1],[4]] ou [[1.2,1.7],[0.3,0.9],[2.1,0.5]])
	- train_labels, dev_labels, test_labels (dÃ©faut = None) :
		Si les arguments xxxxx_set ne sont pas utilisÃ©s, on passe les labels avec ces arguments ci.
		De mÃªme, le ou les labels de chaque exemple doivent Ãªtre dans une liste
		(ex : [[0],[1],[8]] ou [[0,0,1],[0,1,1],[1,0,1]])
	- data_type et label_type (dÃ©faut = 'float' et 'long') :
		Une string parmi 'int', 'float', 'long' ou 'double'.
		Indique le type de tensor qui sera utilisÃ© pour stocker les donnÃ©es et les labels
	- batch_size (dÃ©faut = 64) :
		La taille qui sera utilisÃ© pour chaque batch


Notre framework contient une deuxiÃ¨me classe Model qui va stocker toutes les informations nÃ©cessaires
Ã  faire tourner notre modÃ¨le.
Cette classe prend pour arguments :
	- name :
		Le nom donnÃ© au modÃ¨le pour identifier les modÃ¨les sauvegardÃ©s
	- model :
		Le modÃ¨le Pytorch qui sera utilisÃ©
	- loss :
		La fonction de perte utilisÃ©e, nous reviendrons dessus aprÃ¨s
	- optimizer :
		L'optimiseur utilisÃ©
	- dataset :
		Le dataset utilisÃ© sous forme de la classe ci-dessus
	- metric :
		La metric utilisÃ©e, nous reviendrons dessus aprÃ¨s
Dataset possÃ¨de trois fonctions utiles :
	- train avec comme arguments :
		- epochs (dÃ©faut = 10) : 
			le nombre d'Ã©poques d'entrainement
		- evaluate (dÃ©faut = True) : 
			un boolÃ©en qui indique si le modÃ¨le doit Ãªtre testÃ© sur le set de dev aprÃ¨s chaque Ã©poque
		- verbose (dÃ©faut = 1) : 
			Si 0, rien n'est affichÃ©. 
			Si 1, on affiche la loss Ã  la fin de chaque Ã©poque et le score si l'Ã©valuation est Ã  vrai
			Si 2, on affiche la loss aprÃ¨s chaque batch
		Cette fonction va entrainer le modÃ¨le et sauvegarder la meilleure et la derniÃ¨re itÃ©ration.
	- score avec comme arguments :
		- verbose (dÃ©faut = 1) :
			Si 0, rien n'est affichÃ©.
			Si 1, le score est affichÃ©
		Cette fonction va Ã©valuer le modÃ¨le sur le set de test
	- restore_best :
		Cette fonction va juste charger la meilleure itÃ©ration sauvegardÃ©e dans le modÃ¨le.
	
	
Pour la fonction de perte, on utilise une classe qui doit Ã©tendre la classe Loss dÃ©finie dans losses.py
et implÃ©menter la fonction compute avec pour arguments :
	- in_data :
		Les donnÃ©es qui ont Ã©tÃ© passÃ©es en entrÃ©e au modÃ¨le (peut servir pour certains modÃ¨les comme les VAE)
	- out_data :
		Les sorties qui ont Ã©tÃ© renvoyÃ©es par le modÃ¨le.
	- labels :
		Les labels qui correspondent aux donnÃ©es passÃ©es au modÃ¨le.
Cette fonction doit renvoyer la perte.


Pour la mÃ©trique, on utilise une classe qui doit Ã©tendre la classe Metric dÃ©finie dans metrics.py
et implÃ©menter trois fonctions :
	- step avec comme arguments :
		- in_data :
		Les donnÃ©es qui ont Ã©tÃ© passÃ©es en entrÃ©e au modÃ¨le (peut servir pour certains modÃ¨les comme les VAE)
		- out_data :
			Les sorties qui ont Ã©tÃ© renvoyÃ©es par le modÃ¨le.
		- labels :
			Les labels qui correspondent aux donnÃ©es passÃ©es au modÃ¨le.
		Cette fonction va mettre Ã  jour les variables utilisÃ©es pour calculer la mÃ©trique avec le batch fourni.
		(ex : le nombre total d'exemples vus et le nombre total d'exemples bien classÃ©s)
	- score :
		Cette fonction va renvoyer le score de la mÃ©trique en fonction des variables de la classe.
		(ex : le nombre total d'exemples bien classÃ©s divisÃ© par le nombre total d'exemples vus)
	- reset :
		Cette fonction remet les variables Ã  0 et est appelÃ©e par le constructeur

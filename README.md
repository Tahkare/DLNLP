# DLNLP
Le notebook "fte" n'est pas commenté mais contient les modifications pour utiliser le fast embedding. Or, du à la taille du fichier qui contient le modèle pré-entrainé, il n'était pas possible de le mettre sur github

Pour exécuter notre code, il suffit de faire tourner le notebook.

Notre dataset est stocké dans la classe Dataset de notre framework.
Cette classe peut prendre plusieurs arguments :
	- name (défaut = "") : 
		Le nom donné © au dataset pour identifier les modèles sauvegardés
	- train_set, dev_set, test_set (défaut = None) :
		Les sets peuvent être passés directement au format DataLoader
		Si ces arguments sont vides, il faut utiliser les 6 arguments suivants
	- train_data, dev_data, test_data (défaut = None) :
		Si les arguments au-dessus ne sont pas utilisés, on peut passer les données sous forme d'une liste.
		Chaque exemple doit être passés dans une liste même s'il ne s'agit que d'une seule valeur
		(ex : [[2],[1],[4]] ou [[1.2,1.7],[0.3,0.9],[2.1,0.5]])
	- train_labels, dev_labels, test_labels (défaut = None) :
		Si les arguments xxxxx_set ne sont pas utilisés, on passe les labels avec ces arguments ci.
		De même, le ou les labels de chaque exemple doivent être dans une liste
		(ex : [[0],[1],[8]] ou [[0,0,1],[0,1,1],[1,0,1]])
	- data_type et label_type (défaut = 'float' et 'long') :
		Une string parmi 'int', 'float', 'long' ou 'double'.
		Indique le type de tensor qui sera utilisé pour stocker les données et les labels
	- batch_size (défaut = 64) :
		La taille qui sera utilisé pour chaque batch


Notre framework contient une deuxième classe Model qui va stocker toutes les informations nécessaires
Ã  faire tourner notre modèle.
Cette classe prend pour arguments :
	- name :
		Le nom donné au modèle pour identifier les modèles sauvegardés
	- model :
		Le modèle Pytorch qui sera utilisé
	- loss :
		La fonction de perte utilisée, nous reviendrons dessus après
	- optimizer :
		L'optimiseur utilisé
	- dataset :
		Le dataset utilisé sous forme de la classe ci-dessus
	- metric :
		La metric utilisée, nous reviendrons dessus après
Dataset possède trois fonctions utiles :
	- train avec comme arguments :
		- epochs (défaut = 10) : 
			le nombre d'époques d'entrainement
		- evaluate (défaut = True) : 
			un booléen qui indique si le modèle doit être testé sur le set de dev après chaque époque
		- verbose (défaut = 1) : 
			Si 0, rien n'est affiché. 
			Si 1, on affiche la loss Ã  la fin de chaque époque et le score si l'évaluation est Ã  vrai
			Si 2, on affiche la loss après chaque batch
		Cette fonction va entrainer le modèle et sauvegarder la meilleure et la dernière itération.
	- score avec comme arguments :
		- verbose (défaut = 1) :
			Si 0, rien n'est affiché.
			Si 1, le score est affiché.
		Cette fonction va évaluer le modèle sur le set de test
	- restore_best :
		Cette fonction va juste charger la meilleure itération sauvegardée dans le modèle.
	
	
Pour la fonction de perte, on utilise une classe qui doit étendre la classe Loss définie dans losses.py
et implémenter la fonction compute avec pour arguments :
	- in_data :
		Les données qui ont été passées en entrée au modèle (peut servir pour certains modèles comme les VAE)
	- out_data :
		Les sorties qui ont été renvoyées par le modèle.
	- labels :
		Les labels qui correspondent aux données passées au modèle.
Cette fonction doit renvoyer la perte.


Pour la métrique, on utilise une classe qui doit étendre la classe Metric définie dans metrics.py
et implémenter trois fonctions :
	- step avec comme arguments :
		- in_data :
		Les données qui ont été passées en entrée au modèle (peut servir pour certains modèles comme les VAE)
		- out_data :
			Les sorties qui ont été renvoyées par le modèle.
		- labels :
			Les labels qui correspondent aux données passées au modèle.
		Cette fonction va mettre Ã  jour les variables utilisées pour calculer la métrique avec le batch fourni.
		(ex : le nombre total d'exemples vus et le nombre total d'exemples bien classés)
	- score :
		Cette fonction va renvoyer le score de la métrique en fonction des variables de la classe.
		(ex : le nombre total d'exemples bien classés divisé par le nombre total d'exemples vus)
	- reset :
		Cette fonction remet les variables Ã  0 et est appelée par le constructeur

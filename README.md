# stackexchange-api-tests
Test technique parti 1

# StackExchange API – Cas de test fonctionnels (ISTQB)

## Objectif
Ce document contient tous les cas de test fonctionnels pour l’API :  
https://api.stackexchange.com/2.3/questions

Les tests suivent les bonnes pratiques ISTQB : partition d’équivalence, valeurs limites, tests d’erreur, robustesse, etc.

---

## TC01 – Appel nominal sans paramètres
**Objectif :** Vérifier la réponse par défaut  
**Préconditions :** API accessible  
**Données :** GET /questions  
**Étapes :**  
1. Envoyer la requête GET  
**Résultats attendus :**  
- Code 200  
- JSON valide  
- `items` présent et non vide  

---

## TC02 – pagesize valide
**Objectif :** Tester une valeur valide  
**Données :** pagesize=10  
**Étapes :** GET /questions?pagesize=10  
**Résultats attendus :**  
- Code 200  
- ≤ 10 résultats  

---

## TC03 – pagesize négatif
**Objectif :** Tester valeur invalide  
**Données :** pagesize=-1  
**Étapes :** GET /questions?pagesize=-1  
**Résultats attendus :**  
- Code 400 ou message d’erreur  

---

## TC04 – pagesize limite
**Objectif :** Tester valeur maximale  
**Données :** pagesize=100  
**Étapes :** GET /questions?pagesize=100  
**Résultats attendus :**  
- Code 200  
- ≤ 100 résultats  

---

## TC05 – pagesize dépassement
**Objectif :** Tester dépassement limite  
**Données :** pagesize=101  
**Étapes :** GET /questions?pagesize=101  
**Résultats attendus :**  
- Erreur 400 ou valeur limitée à 100  

---

## TC06 – Filtrage par tag
**Objectif :** Vérifier filtre par tag  
**Données :** tagged=python  
**Étapes :** GET /questions?tagged=python  
**Résultats attendus :**  
- Code 200  
- Tous les résultats contiennent "python"  

---

## TC07 – Tag vide
**Objectif :** Tester cas limite  
**Données :** tagged=  
**Étapes :** GET /questions?tagged=  
**Résultats attendus :**  
- Erreur 400 ou comportement par défaut  

---

## TC08 – Pagination
**Objectif :** Vérifier continuité des pages  
**Données :** page=1, page=2  
**Étapes :**  
1. GET page=1  
2. GET page=2  
**Résultats attendus :**  
- Pas de doublons entre les pages  
- `has_more` cohérent  

---

## TC09 – Tri et ordre
**Objectif :** Vérifier tri  
**Données :** sort=activity, order=desc  
**Étapes :** GET avec paramètres  
**Résultats attendus :**  
- Liste triée du plus récent au plus ancien  

---

## TC10 – Sort invalide
**Objectif :** Tester valeur invalide  
**Données :** sort=invalid  
**Étapes :** GET avec sort invalide  
**Résultats attendus :**  
- Code 400, message d’erreur clair  

---

## TC11 – Mauvaise méthode HTTP
**Objectif :** Vérifier REST  
**Étapes :** Envoyer POST /questions  
**Résultats attendus :**  
- Code 405 Method Not Allowed  

---

## TC12 – Validation structure JSON
**Objectif :** Vérifier JSON  
**Étapes :** GET /questions  
**Résultats attendus :**  
- JSON valide  
- Champs présents : `items`, `has_more`  
- Chaque item contient `title`, `creation_date`  

---

## TC13 – Injection caractères spéciaux
**Objectif :** Tester robustesse  
**Données :** tagged=<script>alert(1)</script>  
**Étapes :** GET avec données  
**Résultats attendus :**  
- Pas de crash  
- Données échappées correctement  

---

## TC14 – UTF-8
**Objectif :** Tester encodage  
**Données :** tagged=économie  
**Étapes :** GET avec UTF-8  
**Résultats attendus :**  
- Réponse correcte, pas d’erreur d’encodage  

---

## TC15 – Rate limiting
**Objectif :** Vérifier quota API  
**Étapes :** Envoyer 50+ requêtes rapides  
**Résultats attendus :**  
- Limitation ou blocage (429)  
- `quota_remaining` correct  
- API stable

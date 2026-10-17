# TP 1 : Programmation dynamique par réflexion dans une architecture Microservices

Lab MLIAEdu — cours *Architecture Microservices : Conception, Déploiement et Orchestration*
(`/student/courses/7/labs/31`).

## Structure

```
src/main/java/
├── dao/
│   ├── IDao.java            # Contrat de persistance
│   └── DaoImpl.java         # Implémentation concrète
├── metier/
│   ├── IMetier.java         # Contrat métier
│   └── MetierImpl.java      # Implémentation + setter d'injection
└── presentation/
    └── Presentation2.java   # Moteur de chargement dynamique
config.txt                   # Classes à charger (fully qualified)
```

## Principe

`Presentation2` lit `config.txt`, charge les classes avec `Class.forName()`, les instancie
via `Constructor.newInstance()` et injecte le DAO dans le métier par `Method.invoke()`
sur `setDao(...)`. Aucune instanciation directe (`new`) n'apparaît dans le code appelant :
le contrat (`IDao`, `IMetier`) est découplé de l'implémentation.

## Configuration

`config.txt` (racine du projet) :

```
dao.DaoImpl
metier.MetierImpl
```

## Exécution

```bash
mvn clean compile exec:java
```

## Résultat attendu

```
[CONTAINER] DAO instancié : dao.DaoImpl
[CONTAINER] Métier instancié : metier.MetierImpl
[DAO] Récupération de la valeur depuis la source de données...
[METIER] Calcul effectué. Résultat : 200.0
Résultats = 200.0
```

## Points d'attention

- Les classes chargées doivent posséder un **constructeur par défaut public**
  (`DaoImpl`, `MetierImpl`), sinon `InstantiationException`.
- Les noms dans `config.txt` doivent être **fully qualified** (package inclus).
- `injectDependency` retrouve le type du paramètre du setter via
  `dependency.getClass().getInterfaces()[0]` : la cible doit donc implémenter
  l'interface attendue (`IDao`).
- Erreurs courantes : `ClassNotFoundException`, `IllegalAccessException`,
  `NullPointerException` si `config.txt` est absent ou mal lu.

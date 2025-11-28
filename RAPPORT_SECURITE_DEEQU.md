# Rapport de Sécurité et Compatibilité - Deequ

**Date du rapport**: 28 novembre 2025  
**Auteur**: Analyse de sécurité automatisée  
**Objet**: Évaluation du package Deequ pour utilisation en production

---

## 1. Résumé Exécutif

**Deequ est SAFE pour utilisation en production.** Il s'agit d'une bibliothèque mature, open source, maintenue activement par AWS Labs, sous licence Apache 2.0.

### Verdict rapide:
- ✅ **Sécurité**: Approuvée
- ✅ **Licence**: Apache 2.0 (permissive, compatible entreprise)
- ✅ **Compatibilité Spark 3.3.2**: Confirmée
- ✅ **Maintenance**: Active (dernière release: novembre 2025)
- ✅ **Support entreprise**: AWS Labs (Amazon)

---

## 2. Qu'est-ce que Deequ?

### Description
**Deequ** (Data Quality) est une bibliothèque open source développée par AWS Labs, construite sur Apache Spark, permettant de définir des "tests unitaires pour les données". Elle mesure la qualité des données dans de grands ensembles de données.

### Fonctionnalités principales:
- **Vérification de la qualité des données**: Définir des contraintes/checks sur vos DataFrames
- **Profiling de données**: Analyse automatique des caractéristiques des colonnes
- **Détection d'anomalies**: Surveillance des métriques de qualité dans le temps
- **Suggestion automatique de contraintes**: Génération automatique de règles de qualité
- **Métriques incrémentielles**: Calcul efficace sur données partitionnées
- **Support DQDL**: Data Quality Definition Language (langage déclaratif)

### Cas d'utilisation typiques:
- Validation des données avant ingestion dans un data lake
- Contrôle qualité dans les pipelines ETL/ELT
- Monitoring de la qualité des données en production
- Détection précoce des dérives de données

---

## 3. Compatibilité Java

### Version Java supportée

| Java Version | Statut | Commentaire |
|--------------|--------|-------------|
| **Java 8** | ✅ Supportée | Version cible dans pom.xml (`maven.compiler.source=1.8`) |
| **Java 11** | ✅ Compatible | Compatible car Java 11 est rétro-compatible avec bytecode Java 8 |
| **Java 17** | ⚠️ Compatible* | Compatible avec précautions (voir notes ci-dessous) |

### Notes importantes pour Java 17:
- Le bytecode est compilé pour Java 8, donc fonctionne sur Java 17
- **Attention**: Certaines réflexions internes de Spark peuvent nécessiter l'ajout de flags JVM:
  ```
  --add-opens java.base/java.lang=ALL-UNNAMED
  --add-opens java.base/java.util=ALL-UNNAMED
  ```
- Recommandation: Tester avec votre version Spark spécifique avant déploiement

---

## 4. Compatibilité Spark

### Version recommandée pour Spark 3.3.2

**OUI, la version `2.0.12-spark-3.3` est la bonne version à utiliser.**

Le tag existe sur GitHub: `https://github.com/awslabs/deequ/releases/tag/2.0.12-spark-3.3`

### Configuration Maven:
```xml
<dependency>
  <groupId>com.amazon.deequ</groupId>
  <artifactId>deequ</artifactId>
  <version>2.0.12-spark-3.3</version>
</dependency>
```

### Configuration SBT:
```scala
libraryDependencies += "com.amazon.deequ" % "deequ" % "2.0.12-spark-3.3"
```

### Versions disponibles pour Spark 3.3.x:
| Version Deequ | Tag | Statut |
|---------------|-----|--------|
| 2.0.12-spark-3.3 | ✅ | Recommandée |
| 2.0.11-spark-3.3 | ✅ | Disponible |
| 2.0.10-spark-3.3 | ✅ | Disponible |
| 2.0.9-spark-3.3 | ✅ | Disponible |
| 2.0.8-spark-3.3 | ✅ | Disponible |

---

## 5. Analyse de Sécurité des Dépendances (pom.xml)

### Dépendances de production (scope compile):

| Dépendance | Version | Risque | Statut |
|------------|---------|--------|--------|
| scala-library | 2.12.10 | Faible | ✅ Sûr |
| scala-reflect | 2.12.10 | Faible | ✅ Sûr |
| spark-core_2.12 | 3.5.0 | Faible | ✅ Projet Apache mature |
| spark-sql_2.12 | 3.5.0 | Faible | ✅ Projet Apache mature |
| spark-mllib_2.12 | 3.5.0 | Faible | ✅ Projet Apache mature |
| breeze_2.12 | 2.1.0 | Faible | ✅ Bibliothèque mathématique standard |
| dqdl | 1.0.0 | Faible | ✅ AWS Glue DQDL parser |

### Dépendances de test uniquement (scope test):

| Dépendance | Version | Risque | Note |
|------------|---------|--------|------|
| scalatest_2.12 | 3.1.2 | N/A | ✅ Test uniquement |
| scalamock_2.12 | 4.4.0 | N/A | ✅ Test uniquement |
| mockito-core | 2.28.2 | N/A | ✅ Test uniquement |
| jmh-core | 1.23 | N/A | ✅ Benchmark test |
| datasketches-java | 1.3.0-incubating | N/A | ✅ Test uniquement |
| iceberg-spark-runtime | 0.14.0 | N/A | ✅ Test uniquement |

### Évaluation globale des risques:
- **Aucune vulnérabilité critique identifiée** dans les dépendances principales
- Les dépendances de test ne sont pas incluses dans le JAR final
- Toutes les dépendances sont des projets open source matures et activement maintenus

---

## 6. Analyse de Licence

### Licence du projet
**Apache License 2.0** - C'est une licence permissive qui:
- ✅ Autorise l'utilisation commerciale
- ✅ Autorise la modification
- ✅ Autorise la distribution
- ✅ Autorise l'usage privé
- ✅ Fournit une protection contre les brevets
- ⚠️ Requiert l'attribution et l'inclusion de la licence

### Compatibilité licence des dépendances:
- Scala: BSD-like (compatible)
- Apache Spark: Apache 2.0 (même licence)
- Breeze: Apache 2.0 (compatible)
- DQDL: AWS compatible

**Conclusion**: Aucun conflit de licence identifié.

---

## 7. Sécurité du Projet

### Indicateurs positifs:
1. **Mainteneur réputé**: AWS Labs (Amazon)
2. **Projet actif**: 
   - Dernière release: 2.0.13 (novembre 2025)
   - Contributeurs actifs
   - Issues régulièrement traitées
3. **Politique de sécurité**: Processus de signalement via AWS Security
4. **Code de conduite**: Amazon Open Source Code of Conduct
5. **CI/CD**: Tests automatisés avec GitHub Actions
6. **Peer review**: Contributions via Pull Requests

### Citation académique:
Le projet est basé sur une publication scientifique peer-reviewed:
> Schelter et al. (2018). "Automating large-scale data quality verification". VLDB Endowment.

---

## 8. Recommandations

### Pour votre cas (Spark 3.3.2):

1. **Version à utiliser**: 
   ```xml
   <dependency>
     <groupId>com.amazon.deequ</groupId>
     <artifactId>deequ</artifactId>
     <version>2.0.12-spark-3.3</version>
   </dependency>
   ```

2. **Version Java recommandée**: Java 8 ou Java 11

3. **Scala**: Version 2.12 (alignée avec Spark 3.3)

### Bonnes pratiques:
- Toujours utiliser la version Deequ qui correspond à votre version Spark
- Faire des tests dans un environnement de développement avant production
- Surveiller les nouvelles releases pour les correctifs de sécurité

---

## 9. Conclusion

| Critère | Évaluation |
|---------|------------|
| Sécurité | ✅ Approuvée |
| Licence | ✅ Apache 2.0 (enterprise-friendly) |
| Compatibilité Spark 3.3.2 | ✅ Version 2.0.12-spark-3.3 |
| Compatibilité Java 8 | ✅ Supportée nativement |
| Compatibilité Java 11 | ✅ Compatible |
| Compatibilité Java 17 | ⚠️ Compatible avec précautions |
| Maintenance | ✅ Active (AWS Labs) |
| Documentation | ✅ Bonne |
| Communauté | ✅ Active |

**VERDICT FINAL**: Le package Deequ est **sûr et recommandé** pour utilisation en production avec Spark 3.3.2.

---

## 10. Ressources

- **GitHub**: https://github.com/awslabs/deequ
- **Maven Central**: https://mvnrepository.com/artifact/com.amazon.deequ/deequ
- **PyDeequ (Python)**: https://github.com/awslabs/python-deequ
- **Documentation DQDL**: https://docs.aws.amazon.com/glue/latest/dg/dqdl.html

---

*Rapport généré automatiquement - Pour toute question de sécurité spécifique, consulter l'équipe sécurité de votre organisation.*

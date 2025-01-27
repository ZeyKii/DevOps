# WIK-DPS-TP02

### Fonctionnement :

Ce TP a pour objectif de travailler avec Docker pour créer des images optimisées d'une API déjà développée (**WIK-DPS-TP01**). Deux approches sont mises en œuvre :  
1. **Image monolithique** : une seule étape pour construire et exécuter l'API.  
2. **Image multi-stage** : séparation des étapes de build et d'exécution pour une image finale allégée.

L'objectif principal est de respecter les bonnes pratiques Docker tout en minimisant la taille des images et le temps de build. Un utilisateur spécifique est également utilisé pour l'exécution du serveur afin de renforcer la sécurité.

### Objectifs pédagogiques :

- Comprendre les bonnes pratiques d'organisation des Dockerfiles.
- Réaliser des optimisations pour réduire le poids des images et accélérer les builds.
- Découvrir les outils de scan de vulnérabilités (Docker Scan, Trivy, Clair).
- Expérimenter la création d'images sécurisées pour un environnement de production.

### Résultats attendus :

1. Une image Docker monolithique fonctionnelle pour l'API.
2. Une image Docker multi-stage, plus légère et adaptée à la production.
3. Un rapport des vulnérabilités détectées par un outil de scan.

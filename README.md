TP 6 – Optimistic Locking avec @Version (JPA / Hibernate)
📌 Description du projet

Ce projet démontre le mécanisme de verrouillage optimiste (Optimistic Locking) avec JPA et Hibernate, en simulant un conflit de modification concurrente sur une entité Reservation.

L’objectif est de comprendre comment l’annotation @Version permet d’éviter les pertes de données lorsqu’une même ligne est modifiée simultanément par plusieurs transactions.

🎯 Objectifs pédagogiques

Comprendre le concept de verrouillage optimiste

Implémenter l’annotation @Version

Simuler un conflit concurrent avec plusieurs threads

Gérer l’exception OptimisticLockException

Mettre en place une stratégie de retry automatique

🛠️ Technologies utilisées

Java 8+

Maven

JPA 2.2

Hibernate 5.6.5

Base de données embarquée H2 Database

API JPA (javax.persistence)

SLF4J (logs)


🗄️ Modèle de données
Entités principales
👤 Utilisateur

id

nom

prenom

email

🏢 Salle

id

nom

capacite

description

📅 Reservation

id

dateDebut

dateFin

motif

utilisateur (ManyToOne)

salle (ManyToOne)

version (@Version)

@Version
private Long version;
🔐 Fonctionnement du Verrouillage Optimiste

Lorsqu’une entité possède un champ annoté @Version :

Hibernate ajoute une colonne version dans la table.

À chaque mise à jour :

La version est vérifiée.

Si la version en base ≠ version en mémoire → exception.

Si aucune autre transaction n’a modifié la ligne :

La version est automatiquement incrémentée.

Exemple SQL généré :

update reservations 
set ..., version=? 
where id=? and version=?

Si aucune ligne n’est modifiée → OptimisticLockException.

⚙️ Configuration
persistence.xml

Base en mémoire H2

hibernate.hbm2ddl.auto = create-drop

Affichage des requêtes SQL activé

▶️ Exécution du projet
Via Maven :
mvn clean compile exec:java -Dexec.mainClass="com.example.ConcurrentReservationSimulator"
Ou via l’IDE :

Exécuter la classe :

ConcurrentReservationSimulator
🧪 Simulation 1 – Conflit sans Retry

Deux threads modifient simultanément la même réservation :

Thread 1 modifie le motif

Thread 2 modifie les dates

Résultat attendu :

Un thread réussit

L’autre déclenche OptimisticLockException

Console attendue :

Thread 1 : Réservation récupérée, version = 0
Thread 2 : Réservation récupérée, version = 0
Thread 2 : Réservation mise à jour avec succès !
Thread 1 : Conflit de verrouillage optimiste détecté !
🔁 Simulation 2 – Conflit avec Retry

La classe OptimisticLockingRetryHandler :

Tente plusieurs mises à jour

Relit l’entité après un conflit

Réessaie jusqu’à maxRetries

Stratégie utilisée :

Maximum 3 tentatives

Backoff progressif (Thread.sleep)

Résultat attendu :

Les deux modifications sont appliquées

Aucune perte de données

📊 Comparaison des stratégies
Sans @Version	Avec @Version
Perte silencieuse de données	Détection de conflit
Dernière écriture gagne	Exception levée
Pas de sécurité	Cohérence garantie
🧠 Concepts Clés
Verrouillage pessimiste

Bloque la ligne en base

Utilise SELECT FOR UPDATE

Impacte les performances

Verrouillage optimiste

Ne bloque pas

Vérifie à la fin

Meilleure scalabilité

Ce TP utilise le verrouillage optimiste, recommandé pour les applications web à forte concurrence.

🚀 Points importants à retenir

@Version est indispensable pour activer l’optimistic locking

L’exception OptimisticLockException doit être gérée

Toujours relire l’entité avant un retry

Le retry doit être limité pour éviter une boucle infinie

📌 Améliorations possibles

Ajouter une interface REST (Spring Boot)

Ajouter des tests unitaires

Implémenter le verrouillage pessimiste pour comparaison

Ajouter des logs plus détaillés

👩‍💻 Auteur

Projet réalisé dans le cadre du TP 6 – JPA/Hibernate
Démonstration pédagogique du mécanisme @Version.

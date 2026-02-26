📌 TP 6 – Simulation d’un Conflit Concurrent et Optimistic Locking avec @Version
🧠 Introduction

Dans les applications multi-utilisateurs, plusieurs utilisateurs peuvent modifier les mêmes données en même temps.
Sans mécanisme de contrôle, cela peut provoquer une perte de données.

Ce projet démontre comment utiliser le verrouillage optimiste (Optimistic Locking) avec JPA / Hibernate afin de détecter et gérer ces conflits.


🎯 Objectifs du TP

Comprendre le principe du verrouillage optimiste

Utiliser l’annotation @Version

Simuler un conflit concurrent avec plusieurs threads

Capturer et gérer OptimisticLockException

Implémenter une stratégie de retry automatique



🛠️ Technologies utilisées

Java 8+

Maven

JPA 2.2

Hibernate 5.6

Base de données embarquée : H2 Database

SLF4J (logs)



📁 Organisation du projet
src/main/java/com/example
│
├── model
│   ├── Utilisateur.java
│   ├── Salle.java
│   └── Reservation.java  ← contient @Version
│
├── service
│   ├── ReservationService.java
│   └── ReservationServiceImpl.java
│
├── ConcurrentReservationSimulator.java
└── OptimisticLockingRetryHandler.java



🗄️ Modèle de données
🔹 Utilisateur

id

nom

prenom

email

🔹 Salle

id

nom

capacite

description

🔹 Reservation

id

dateDebut

dateFin

motif

utilisateur (ManyToOne)

salle (ManyToOne)

version (@Version)




🔐 Fonctionnement du Verrouillage Optimiste

Hibernate ajoute une colonne version en base.

Lors d’un UPDATE, la version est vérifiée.

Si la version a changé entre-temps :
➜ OptimisticLockException

Sinon :
➜ La version est incrémentée automatiquement.



🧪 Simulation des Conflits
🔴 1️⃣ Simulation sans Retry

Deux threads modifient la même réservation :

Thread 1 → modifie le motif

Thread 2 → modifie les dates

Résultat attendu :

Un thread réussit

L’autre déclenche OptimisticLockException



🟢 2️⃣ Simulation avec Retry

Utilisation de :

OptimisticLockingRetryHandler

Fonctionnement :

Tentatives multiples (max 3)

Relit l’entité après conflit

Réessaie automatiquement

Résultat :

Les deux modifications sont appliquées

Aucune perte de données


📸  Captures d’Écran
<img width="1920" height="1080" alt="Screenshot (249)" src="https://github.com/user-attachments/assets/3682e9c9-94f1-4264-9b3f-3e48490a3d94" />
<img width="1920" height="1080" alt="Screenshot (250)" src="https://github.com/user-attachments/assets/6ff24ce1-8c2f-4026-a4fe-f3a642729e8b" />
<img width="1920" height="1080" alt="Screenshot (251)" src="https://github.com/user-attachments/assets/cf58071d-5238-4cd4-b20d-4663b6d3067d" />
<img width="1920" height="1080" alt="Screenshot (252)" src="https://github.com/user-attachments/assets/592762cf-8a9d-44e3-b835-43e755027cbc" />
<img width="1920" height="1080" alt="Screenshot (253)" src="https://github.com/user-attachments/assets/e3dbca20-6923-4579-953a-0aadce72ec47" />
<img width="1920" height="1080" alt="Screenshot (254)" src="https://github.com/user-attachments/assets/d375d270-4ab1-4767-8f39-4984068e1331" />
<img width="1920" height="1080" alt="Screenshot (255)" src="https://github.com/user-attachments/assets/711a0c8b-a038-4731-ba68-78f6a6d3fb45" />
<img width="1920" height="1080" alt="Screenshot (256)" src="https://github.com/user-attachments/assets/966f6950-4f7d-4094-8740-b83f8a9bf3b4" />
<img width="1920" height="1080" alt="Screenshot (257)" src="https://github.com/user-attachments/assets/7b7e4369-801a-49b4-8e78-af6714db7b21" />
<img width="1920" height="1080" alt="Screenshot (258)" src="https://github.com/user-attachments/assets/98ba13cc-e42c-4960-9090-7288cca11dfe" />
<img width="1920" height="1080" alt="Screenshot (259)" src="https://github.com/user-attachments/assets/c5d87b39-3e1b-4a7a-bc6b-0cff63998548" />
<img width="1920" height="1080" alt="Screenshot (260)" src="https://github.com/user-attachments/assets/e4d419e0-5acb-44fc-9cde-da090d8ce88c" />


🎓 Conclusion

Ce TP montre que :

Le verrouillage optimiste est adapté aux applications web.

@Version protège contre les mises à jour concurrentes.

Une stratégie de retry améliore la robustesse.

Ce mécanisme garantit l’intégrité des données sans bloquer les transactions.

# LocalExpress – correction J01

→ Remplir au fur et à mesure le CDCF

## Maquette

> Maquette ou _zoning_ ?!?  
> → <https://becraft.fr/zoning-wireframe-prototype-comprendre-les-etapes-cles-de-la-conception-dinterface-utilisateur-ui/>

- **zoning** → `./assets/ui/zoning`
- **maquette** → `./assets/ui`

## _User stories_

[correction](./user-stories/LocalExpress_stories.md)

## _Use case_

![correction](./diagrams/use-case/LocalExpress_use-case.png)

> **NOTE** regarde dans ce dossier : il y a des alternatives…

→ extension VSCode ? [UMLet](https://marketplace.visualstudio.com/items?itemName=TheUMLetTeam.umlet)

## ERD

![diagramme](./diagrams/erd/LocalExpress_erd.png)

- Client / Commande
  - un **CLIENT** peut passer PLUSIEURS **COMMANDES**
  - une **COMMANDE** est passée par UN SEUL **CLIENT**

  → association _one-to-many_

- Commande / Ligne de commande
  - une **COMMANDE** contient UNE OU PLUSIEURS **LIGNES**
  - une **LIGNE** appartient à UNE SEULE **COMMANDE**

  → association _one-to-many_

- Ligne de commande / Produit
  - une **LIGNE** concerne UN SEUL **PRODUIT**
  - un **PRODUIT** peut apparaître dans PLUSIEURS **LIGNES**

  → association _one-to-many_

- Produit / Catégorie
  - un **PRODUIT** a UNE SEULE **CATÉGORIE**
  - une **CATÉGORIE** peut avoir PLUSIEURS **PRODUITS**

  → association _one-to-many_

> l'entité `Ligne de commande` est une table de liaison dûe à l'association
> `COMMANDE / PRODUIT` :
>
> - une commande peut contenir PLUSIEURS produits
> - un produit peut apparaître dans PLUSIEURS commandes
>
> → association _many-to-many_ → table de liaison `Ligne de commande`

## Diagrammes

### Séquence : mise au panier

```plantuml
@startuml
actor Client
participant "Panier" as panier

Client -> panier : Ajouter un produit dans mon panier
panier -> panier : Vérifier si le produit est déjà dans le panier
panier -> panier : Si OUI, augmenter la quantité
panier -> panier : Si NON, ajouter le produit au panier
panier -> Client : Confirmer l'ajout du produit au panier
@enduml
```

![sequence diagram](./diagrams/LocalExpress_sequence.png)

### Activité : client

```plantuml
@startuml
start
:Commencer;
:Voir les produits disponibles;

while (Achats en cours ?) is (oui)
  fork
    :Voir le détail d'un produit;
  fork again
    :Ajouter un produit au panier;
  fork again
    :Modifier le panier;
  end fork
endwhile (achats terminés)
  :Voir le montant total de mon panier;
  :Passer commande;
  :Saisir mes informations personnelles;
  :Valider la commande;

end
@enduml
```

![customer's activity diagram](./diagrams/LocalExpress_activity_customer.png)

## Kanban

| Backlog          | Sprint                      | En cours              | Terminé                  |
| ---------------- | --------------------------- | --------------------- | ------------------------ |
| Back-office      | Workflow GitHub Actions     | Maquettes             | UML : Cas d'utilisation  |
| Tests end to end | UML : Déploiement           | Initialiser le projet | UML : Entité-association |
|                  | Page d'accueil              |                       | UML : Séquence           |
|                  | Page principale             |                       | UML : Activité           |
|                  | Modale détail d'un produit  |                       |                          |
|                  | Panier                      |                       |                          |
|                  | Finalisation de la commande |                       |                          |
|                  | Page de confirmation        |                       |                          |
|                  | Gestion des erreurs         |                       |                          |
|                  | Tests unitaires             |                       |                          |

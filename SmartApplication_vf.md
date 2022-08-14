# Smart Application (vf)

*Cahier de charges initial  
Ce document est préalable aux spécifications qui seront exposées dans le prototype "Curiosity" (Thu Vo Minh), dont la v1 devrait être terminée fin janvier 2023.*

## Objectifs
1. Proposer une ontologie de base transversale à toutes les applications et segments d'applications, compréhensible de manière univoque tant par les développeurs que par les analystes business et l'équipe Produit
2. Etablir le fonctionnement général d'un ***système*** applicatif structuré autour d'objets standardisés, dotés chacun de son propre cycle de vie paramétrable.
3. Renforcer la prise en main (compréhension et manipulation) de ce système applicatif par les analystes business et l'équipe Produit, voire pour certaines parties par des utilisateurs spécifiques (comptabilité, gestion, etc.)
4. Doter le système applicatif d'outils poussés de simulation, de tests et d'audit fonctionnel.
5. Le tout dans le but ultime de disposer d'un système applicatif souple, hautement évolutif, qui peut être étendu rapidement, capable pour les années à venir d'intégrer les divers et multiples besoins, connus ou non, qui rendent compte des enjeux définis pour partie par les lois et règlements et pour partie par la stratégie de développement établie par l'Administration déléguée et par le CA de Smart.

## Description fonctionnelle
L'Application doit gérer des objets business décrivant des opérations économiques, de la saisie initiale des données en aval aux traitement déclaratifs en amont. Divers utilisateurs devront intervenir sur ces objets en différents moments de leur cycle de vie. Le cycle de vie d'une instance d'objet est déterminé par des règles de gestion susceptibles d'être configurées pour divers usages (diverses situations) et devant évoluer parfois rapidement en fonction des lois, règlements et autres décisions de gestion porpres à Smart. A priori, toutes les données business sont immutables et historisées. Enfin, chaque événement entraînant une modification de l'état du système est identifié et enregistré, dans son contexte.

### Les composants du système
#### Le contexte
Un utilisateur agit dans un contexte.  
Ce contexte est spécifié au moins par :

1. **Context_User** : l'utilisateur lui-même : quel est son état ? (notamment pour la validation des droits)
2. **Context_BusinessEntity** : éventuellement, l'entité business au sein de laquelle l'utilisateur agit : notamment pour connaître la déclinaison de service applicable. Et récupérer le cas échant la fraction de l'état du système la concerant.
3. **Context_LegalEntity** : éventuellement, l'entité légale au sein de laquele *l'entité business ou l'utilisateur* agit. Pour récupérer notamment la fraction de l'état du système la concernant.
4. **Context_AppFlo**w : l'application (ou partie d'application) émettrice des données, d'une commande (action).Ce qui permet d'adpater la réponse du système selon les scénarii de saisies (commandes, actions) proposés à l'utilisateur dans un même contexte [user, business entity, legal entity], sur un même objet.
5. **Context_BusinessObject** : l'objet business actif : son type, son état a minima.

Quand nous parlerons de contexte, celui-ci devrait donc être chaque fois spécifié par un tel tuple.

#### Règles de gestion et validation
* **La validation "well-formed"** : les données recueillies sont-elles, dans le contexte, complètes et correctement saisies ? 
* **la validation des droits** : l'utilisateur à l'origine de la saisie des données ou d'une commande sur des données existantes dispose-t-il des droits ad-hoc dans le contexte ?
* **la validation de gestion** : les données ou la commande sont-elle en l'état acceptable dans le contexte selon les règles de gestion (business logic) implémentées (paramétrées) dans le système ?  
Dans cette classe de validation, on retrouvera au moins
    * **une validation temporelle** : l'opération demandée l'est-elle dans la période de temps acceptable dans le contexte ?
    * **une validation quantitiative** : le cas échéant, l'ensemble du set de données est il quantitativement cohérent et acceptable selon les règles de gestion ?
    * **une validation systémique** : eu égard à l'état du système résultant de toutes les autres opérations enregistrées dans le contexte, l'opération en cours est-elle acceptable ?

La sortie attendue d'un règle (d'un jeu de règles, celles-ci pouvant s'enchaîner) est un booléen (true, false). Elle est accompagnée d'un message à l'utilisateur. Et dans le cas de False, peut être accompagnée de propostions de modifications ou de demandes d'informations supplémentaires.

#### Le cycle de vie (LifeCycle)
Le cycle de vie d'un objet est décrit dans une table d'états et transitions, dotées des jeux de règles ad hoc.

La description complète d'un cycle de vie comprend, pour chaque ligne de transitions (FROM ... TO) possible (active dans le système):

En réception :

* le contexte
* la commande (l'action)
* l'événement généré par la commande
* les données de l'objet (*type de l'objet => sélection de la table*)
* son état

En traitement :

* les appels aux règles ad hoc à la ligne de transition, règles qui seront également formalisées et pour certaines paramétrables
* la sortie des règles (true, false + data)

En sortie :

* Si traitement = true, le nouvel état à affecter à l'objet
* l'événement généré
* le traitement des data de sortie de règles, notamment et le cas échéant
    * l'envoi des messages vers l'interface utilisateur
    * l'envoi d'une commande à une autre table transition concernant un autre objet
    * les modifications des droits, des états d'autres objets, du comportement de l'interface utilisateur
    * etc.
    
A noter : un événement généré à la sortie peut entraîner, dans le contexte modifié, l'activation d'une autre ligne de transition de la table.

### Les droits (ébauche)
Des commandes (actions) et des données peuvent être accessibles 
* en étant exposées dans une interface utilisateur de l'application Smart online
* en étant exposée dans des web services ou des API mis à disposition des utilisateurs.

Dans les deux cas, l'on peut considérer que quelque chose peut être
* exposé unité par unité ou en lot
* vu ou non
* modifié ou non
* créé ou non
* supprimé ou non

En outre :

* chaque jeu de données, faisant forcément partie d'un document structuré (cfr norme UBL), les droits peuvent être rapporté à un objet ou à un noeud (un élément) de l'objet. Et donc, dans le contexte d'une application en ligne, à un champ (éditable ou non) ou un bloc de texte spécifique
* chaque action, chaque commande, est par ailleurs identifable sur une interface, ou par des paramètres envoyés à une API ou un Web Service.

Dès lors, l'ensemble des droits peut être aisément (mais avec une grande verbosité, certes) décrit sous la forme d'une liste (objet, noeuds, actions) dont chaque item est doté de 5 attributs booléens (visible ou non + modifiable ou non + créable ou non + supprimable ou non + par lot ou non).  
  
A priori, cela suffit au business et dans cette ébauche à spécifier les droits sépcifiques d'un utilistauer dans un contexte donné.

L'on peut prédéfinir des jeux de droits labellisés "rôles".

### Les objets
Chaque fois que cela est possible (et cela l'est quasi toujours), un objet sera spécifié en tant que document ABIE ou Common Aggregate Component dans la norme UBL.
#### Les utilisateurs (users)
|           |                                 |
|-----------|--------------------------       |
| UBL       | Person, PartyLegalEntity, Party |
| LifeCycle | "User_LifeCycle"                |
| Rules     | "User_Rules"                    |
| Event     | "User_Events"                   |

Un utilisateur est défini comme étant une personne ayant accès à tout ou partie d'une application Smart et suceptible d'interagir avec lui, en étant connecté et authentifié par le moyen d'une paire login/password unique.
Une personne, en tant qu'user est une personne physique, à l'exception des "user_system", catégorie réservée aux automates pouvant agir en tant qu'utilisateur (exemple : l'utilisateur connecté agissant via l'API "Deliveroo", ou encore l'application Smart elle-même quand elle modifie l'état du système).

Une personne morale (dotée d'une personnamité juridique) peut à l'occasion devoir agit par l'intermédiaire d'une personne physique non identifiée en tant que telle dans le système. cette personne anonyme sera créée en tant que "Person" (UBL) et reliée à la PartyLegalEntity dont elle relève. A charge pour la PartyLegalEntity de confier elle-même à l'un de ses agents l'usage de cette "personne anonyme".

1. Si l'on souhaite une ouverture forte du système online, un utilisateur peut s'enregistrer uniquement en fournissant login et password, et éventuellement un moyen de récupération du mot de passe. Il dispose d'un droit d'accès limité aux fonctions de l'application online.
2. Un utilisateur qualifié doit fournir les données d'identification de base d'une personne : nom complet, adresse complète, courriel. Après vérification, des fonctions supplémentaires sont accessibles à un utilisateur qualifié.
3. Un utilisateur authentique devra fournir un numéro de compte en banque personnel et, selon le contexte, un ou plusieurs numéros d'identification nationale (au moins un pour être considéré comme "authentique" : TVA, NISS, BCE, Eid, par exemple pour la Belgique), voire l'enregistrement authentique d'une signature ou un document quelconque signé (par exemple le "contrat d'utilisation".
4. Un utilisateur peut décéder (faillite pour une PartyLegalEntity), ou vouloir mettre fin à ses relations avec Smart. Smart peut aussi vouloir suspendre intégralement les droits d'accès à un utilisateur. Il sera "neutralisé" ou suspendu.

*Cycle de vie élémentaire de l'objet USER*

| FROM        | TO                                            |
|-------------|-----------------------------------------------|
| (start)     | enregistré                                    |
| enregistré  | qualifié, neutralisé, suspendu                |
| qualifié    | enregistré, neutralisé, suspendu, authentifié |
| authentifié | qualifié, enregistré, neutralisé, suspendu    |
| suspendu    | enregistré, qualifié, authentifié, neutralisé |
| neutralisé  | (end)                                         |

Chaque état est associé à un ensemble de droits de base spécifiques sur le contexte applicatif auquel il souhaite accéder.

##### Use Cases
Application :   
Sections[sous-sections] : mon profil, Smart Infos [news, base de connaissance], Vie coopérative [événements, mes participations au capital], devenir sociétaire, Formations [consulter, s'inscrire], Business [personnel, entités], Offres d'emploi [consulter, répondre], supervision

**Une personne quelconque souhaite s'enregistrer "pour voir":**

```json
{
    "Person":{  
        "ID":"45500000",
        "IdentityDocumentReference":[{  
            "ID":"AC000001",  
            "DocumentType":"AccessContract",  
            "ValidityPeriod":{"StartDate":"2022-08-15"},
            "XPath":"/accessContrat/identifiers",
            "Attachment":{
                "EmbeddedDocument":"<accessContract><identifiers><login>loginxxx</login><password>passyyy</password></identifers></accesContract>"
            }
        }]
    }
}
```
*Accès :  mon profil, Smart Infos [news, base de connaissance],devenir sociétaire, Formations [consulter], Business (version sandbox) [personnel, entités], Offres d'emploi [consulter]*

**Une personne est qualifiée:**
```json
{
    "Person":{  
        "ID":"455000002", 
        "FirstName":"Roger",
        "FamilyName":"Burton",
        "ResidenceAddress":{
            "ID": "ad000002",
            "StreetName":"rue du Test",
            "BuildingNumber":"1",
            "CityName":"Namur",
            "PostalZone":"4000",
            "Country":{
                "Name":"Belgique"
            }
        },
        "Contact":{
            "ID":"cntct000001",
            "ElectronicMail":"bur@gmail.com"
        },
        "IdentityDocumentReference":[{  
            "ID":"AC000001",  
            "DocumentType":"AccessContract",  
            "ValidityPeriod":{"StartDate":"2022-08-15"},
            "XPath":"/accessContrat/identifiers",
            "Attachment":{
                "EmbeddedDocument":"<accessContract><identifiers><login>loginxxx</login><password>passyyy</password></identifers></accesContract>"
            }
        }]
    }
}
```
*Accès :  mon profil, Smart Infos [news, base de connaissance],devenir sociétaire, Formations [consulter, s'inscrire], Business (version sandbox) [personnel, entités], Offres d'emploi [consulter, répondre]*

**Une personne est authentique:**

```json
{
    "Person":{  
        "ID":"455000003", 
        "FirstName":"Frédéric",
        "FamilyName":"Gregoir",
        "ResidenceAddress":{
            "ID": "ad000003",
            "StreetName":"rue du Test",
            "BuildingNumber":"55",
            "CityName":"Bruxelles",
            "PostalZone":"1050",
            "Country":{
                "Name":"Belgique"
            }
        },
        "Contact":{
            "ID":"cntct000002",
            "ElectronicMail":"gre@gmail.com"
        },
        "IdentityDocumentReference":[{  
            "ID":"AC000002",  
            "DocumentType":"xml",  
            "DocumentDescription":"AccessContract",  
            "ValidityPeriod":{"StartDate":"2022-08-10"},
            "XPath":"/accessContrat/identifiers",
            "Attachment":{
                "EmbeddedDocument":"<accessContract><identifiers><login>loginxxx2</login><password>passyyy2</password></identifers></accesContract>"
            }
        },{  
            "ID":"AC000001",  
            "UUID":"eid987654321",  
            "DocumentType":"Eid",  
            "DocumentDescription":"Carte d'identité",  
            "ValidityPeriod":{"StartDate":"2020-01-1","EndDate":"2025-06-30"},
        }],
        "FinancialAccount":{
            "ID":"BE31 1234 4567 8900",
            "AccountFormatCode":"IBAN",
            "Country":{
                "Name":"Belgique"
            }
        }
    }
}
```
*Accès :  mon profil, Smart Infos [news, base de connaissance],Vie coopérative [événements, mes participations au capital], Formations [consulter, s'inscrire], Business [personnel, entités si CURU signé, sinon sandbox], Offres d'emploi [consulter, répondre]*

**Une entité légale doit être enregistrée:**

```json
{
    "Party":{
        "PartyIdentification":{
            "ID":"SCH001"
        },
        "Person":[{  
            "ID":"455000005", 
            "RoleCode":"anonymousContact",
            "Contact":{
                "ID":"cntct000002",
                "ElectronicMail":"info@schleiper.com"
            },
            "IdentityDocumentReference":[{  
                "ID":"AC000001",  
                "DocumentType":"xml",  
                "DocumentDescription":"AccessContract",  
                "ValidityPeriod":{"StartDate":"2022-08-15"},
                "XPath":"/accessContrat/identifiers",
                "Attachment":{
                    "EmbeddedDocument":"<accessContract><identifiers><login>loginxxx</login><password>passyyy</password></identifers></accesContract>"
                }
            }]
        }],
        "PartyLegalEntity":{
            "RegistrationName":"Schleiper",
            "CompanyID":"BCExxx", 
            "CorporateRegistrationScheme":{
                "Name":"BCE",
                "JurisdictionRegionAddress":{
                    "Country":{
                        "Name":"Belgique"
                    }
                }
            }
        }
    }
}
```
*Accès (compte "anonyme":  mon profil, Smart Infos [news, base de connaissance],Vie coopérative [événements, mes participations au capital] ou devenir sociétaire (selon), Formations [consulter, s'inscrire], Business [~~personnel,~~ entités PartyLegalEntity]*

**Une Activité doit être enregistrée (au nom de l'user 455000002):**

```json
{
    "Party":{
        "PartyIdentification":{
            "ID":"489000001",
            "@schemeName":"Smart_UP"
        },
        "PartyName":{
            "Name":"nobody's perfect"
        },
        "IndustryClassificationCode":[{
            "@listName":"NACEBEL",
            "Value":"256000"
        }],
        "Person":[{  
            "ID":"455000002", 
            "RoleCode":"Titulaire",
            "IdentityDocumentReference":[{  
                "ID":"CURU000001",  
                "DocumentDescription":"CURUContract",  
                "ValidityPeriod":{"StartDate":"2022-08-25"},
            }]
        }]
    }
}
```
*Accès :  mon profil, Smart Infos [news, base de connaissance],Vie coopérative [événements, mes participations au capital], Formations [consulter, s'inscrire], Business [personnel, entités selon déclinaison de services], Offres d'emploi [consulter, répondre]*

et l'instance de l'objet "Party" dédiée à Productions associées asbl (et/ou Formateurs Associés, etc.) ajoutera un item à son noeud "AgentParty"

```json
{
    "Party":{
        
        "AgentParty":[{
            "PartyIdentification":{
                "ID":"489000001",
                "@schemeName":"Smart_UP"
            }        
        }]
    }
}
```
Il apparaît donc que pour la gestion des utilisateurs, il conviendrait d'utiliser deux composants UBL, pas plus : Person et Party, qui seraient enregistrés en tant que tel.

La norme UBL a prévu un mécanisme pour historiser les documents, et qui peut être utilisé pour historiser également ces deux composants : il est implémenté pour le document ABIE Order : OrderChange. Il se contente de reprendre quasiment la structure du document Order moyennant 2-3 modifications, dont  un noeud supplémentaire pour indiquer la position d'ordre dans la séquence des changements. A priori, à lalecture de la norme, il semble qu'un document xxxChange reprend l'ensemble des données du document xxx ou xxxChange précédent, aux adaptations près évidemment (modifications, suppressions, ajouts).
On peut l'utiliser ici. D'autant que les changements sur ces objets sont à rotation lente (chnagement d'adresse, par exemple).

Fonctionnement :
* à la création et à chaque modification d'une instance "Person", le système vérifie l'état dans lequel il peut migrer ou doit migrer ;
* la création des entités légales Smart doit être préalable à la création des entités "Activités"
* un département Smart (la DSI, par exemple), est traitée comme une entité "Activité", et elles sont distinguées par le @schemeName (SmartDPRTMNT, par exemple)
* le contrat (explicite ou implicite, peut importe) déterminant l'octroi d'un accès (login/password), tout comme les autres contrats de portée générale (CURU, Contrat Utilisation du site CFA ... par exemple) sont repris dans les IdentityDocumentReference, étant entendu que c'est toujours une personne physique qui le signe, dans un rôle donné.

Ceci doit être affiné en fonction de besoins encore non identifiés dans cette ébauche.

Enfin, rien n'empêche (règles de personnalisation selon UBL) de piocher dans les composants de plus bas niveaux pour construire deux composants "Person" (pour les personnes physiques) et "Party" (pour les personnes morales et les organisations reconnues comme telle part Smart) *powered by Smart*.

**A noter:** *les organisations reconnues comme telle part Smart peuvent être ses départements et directions, les "Activités" (3en1 ou Activités proprement dites), et enfin les associations de titulaires en cas de titulariat collectif sur une Activité, une association d'Activités, Smart elle-même comme "groupe belge", ...*









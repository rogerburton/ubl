# Smart Application (vf)
*Cahier de charges initial  
Ce document est préalable aux spécifications qui seront exposées dans le prototype "Curiosity" (Thu Vo Minh), dont la v1 devrait être terminée fin janvier 2023.*

**Table of contents**
1. [Objectifs](#objectifs)
2. [Description fonctionnelle](#descfonct)
3. [Les composants du système](#components)
    1. [Le contexte](#context)
    2. [Les déclinaisons de service](#bundle)
    3. [Le cycle de vie](#lifecycle)
        1. [Règles de gestion et validation](#rules)
    4. [Les droits](#rights)
    5. [Les objets](#objects)
4. [L'interface online](#uionline)

<!-- 
    etc.
    x. [xxx]()
-->

## <a name="objectifs"></a>Objectifs
1. Proposer une ontologie de base transversale à toutes les applications et segments d'applications, compréhensible de manière univoque tant par les développeurs que par les analystes business et l'équipe Produit
2. Etablir le fonctionnement général d'un ***système*** applicatif structuré autour d'objets standardisés, dotés chacun de son propre cycle de vie paramétrable.
3. Renforcer la prise en main (compréhension et manipulation) de ce système applicatif par les analystes business et l'équipe Produit, voire pour certaines parties par des utilisateurs spécifiques (comptabilité, gestion, etc.)
4. Doter le système applicatif d'outils poussés de simulation, de tests et d'audit fonctionnel.
5. Le tout dans le but ultime de disposer d'un système applicatif souple, hautement évolutif, qui peut être étendu rapidement, capable pour les années à venir d'intégrer les divers et multiples besoins, connus ou non, qui rendent compte des enjeux définis pour partie par les lois et règlements et pour partie par la stratégie de développement établie par l'Administration déléguée et par le CA de Smart.

## <a name="descfonct"></a>Description fonctionnelle
L'Application doit gérer des objets business décrivant des opérations économiques, de la saisie initiale des données en aval aux traitement déclaratifs en amont. Divers utilisateurs devront intervenir sur ces objets en différents moments de leur cycle de vie. Le cycle de vie d'une instance d'objet est déterminé par des règles de gestion susceptibles d'être configurées pour divers usages (diverses situations) et devant évoluer parfois rapidement en fonction des lois, règlements et autres décisions de gestion porpres à Smart. A priori, toutes les données business sont immutables et historisées. Enfin, chaque événement entraînant une modification de l'état du système est identifié et enregistré, dans son contexte.

### <a name="components"></a>Les composants du système
#### <a name="context"></a>Le contexte
Un utilisateur agit dans un contexte.  
Ce contexte est spécifié au moins par :

1. **Context_User** : l'utilisateur lui-même : quel est son état ? (notamment pour la validation des droits)
2. **Context_BusinessEntity** : éventuellement, l'entité business au sein de laquelle l'utilisateur agit : notamment pour connaître la déclinaison de service applicable. Et récupérer le cas échant la fraction de l'état du système la concerant.
3. **Context_LegalEntity** : éventuellement, l'entité légale au sein de laquele *l'entité business ou l'utilisateur* agit. Pour récupérer notamment la fraction de l'état du système la concernant.
4. **Context_AppFlo**w : l'application (ou partie d'application) émettrice des données, d'une commande (action).Ce qui permet d'adpater la réponse du système selon les scénarii de saisies (commandes, actions) proposés à l'utilisateur dans un même contexte [user, business entity, legal entity], sur un même objet.
5. **Context_BusinessObject** : l'objet business actif : son type, son état a minima.

Quand nous parlerons de contexte, celui-ci devrait donc être chaque fois spécifié par un tel tuple.
Intriduire dans la notion de context l'ID de l'utilisateur ou celui d'une entité, et faire du contexte un critère de sélection d'un jeu de règles, c'est permettre de personnaliser, pendant un temps donné le cas échéant, les droits d'accès et de traitement.

#### <a name="bundle"></a>Les déclinaisons de service
Cette notion est au coeur de la stratégie de développement.
Peu importe ici l'usage que l'on en fera, il s'agit avant tout de distinguer des "configurations de services".
* 0 pour tout écran, traitement, scenario de saisie, opération  concernant strictement la "structure" de façon globale ;
* 1 pour l'équivalent de la configuration actuelle "3en1" (une Activité bridée) ;
* 2 pour l'équivalent de la configuration actuelle "Activité" ;
* 3 pour une configuration dédiée à des Activité "++" (inscrite dans le long terme  : crédit, apport personnel, CDI, etc.)
* 4 pour la gestion d'entités légales agissant comme des Activités dans notre éco-système (à créer)
* 5 pour des Activités gérées par Smart (similaire à la déclinaison 3) en son nom et pour son compte ("Proxy", par exemple, ou "expérience activités", ou encore "DSI")
* 6 pour des Tiers externes non gérés par Smart (clients, fournisseurs, etc.)
* -1 pour un contexte où la notion de déclinaison de services n'est pas pertinente (sans usage actuel)

#### <a name="lifecycle"></a>Le cycle de vie (LifeCycle)
Le cycle de vie d'un objet est décrit dans une table d'états et transitions, dotées des jeux de règles ad hoc.

La description complète d'un cycle de vie comprend, pour chaque ligne de transitions (FROM ... TO) possible (active dans le système):

En réception:

* le contexte
* la commande (l'action): par convention dans ce document, on identifiera une commande système par un préfixe sys_ et une commande lancée à partir d'une interface online par un préfixe htmlbutton_. L'action est labellisée par un verbe conjugué à l'actif et au présent
* l'événement généré par la commande : il est la bellisé par un verbe au participe passé. La notion d'événement est essentielle : c'est l'émission d'un événement qui est écouté par le système et qui déclenche de possibles changements d'états des objets.
* les données de l'objet (*type de l'objet => sélection de la table*)
* et son état

En traitement:

* les appels aux règles ad hoc à la ligne de transition, règles qui seront également formalisées et pour certaines paramétrables
* la sortie des règles (true, false + data)

En sortie:

* Si traitement = true, le nouvel état à affecter à l'objet
* l'événement généré
* le traitement des data de sortie de règles, notamment et le cas échéant
    * l'envoi des messages vers l'interface utilisateur
    * l'envoi d'une commande à une autre table transition concernant un autre objet
    * les modifications des droits, des états d'autres objets, du comportement de l'interface utilisateur
    * etc.
    
A noter : un événement généré à la sortie peut entraîner, dans le contexte modifié, l'activation d'une autre ligne de transition de la table.

##### <a name="rules"></a>Règles de gestion et validation
Les règles sont essentiellement au service des changements d'état des objets.

* **La validation "well-formed"** : les données recueillies sont-elles, dans le contexte, complètes et correctement saisies ? 
* **la validation des droits** : l'utilisateur à l'origine de la saisie des données ou d'une commande sur des données existantes dispose-t-il des droits ad-hoc dans le contexte ?
* **la validation de gestion** : les données ou la commande sont-elle en l'état acceptable dans le contexte selon les règles de gestion (business logic) implémentées (paramétrées) dans le système ?  
Dans cette classe de validation, on retrouvera au moins
    * **une validation temporelle** : l'opération demandée l'est-elle dans la période de temps acceptable dans le contexte ?
    * **une validation quantitative** (portée : l'instance en cours) : le cas échéant, l'ensemble du set de données est il quantitativement cohérent et acceptable selon les règles de gestion ?
    * **une validation par l'état du système** (portée : la fraction de l'état du système dédiée à l'utilisateur ou l'entité concerné) : eu égard à l'état du système résultant de toutes les autres opérations enregistrées dans le contexte, l'opération en cours est-elle acceptable ?

La sortie attendue d'un règle (d'un jeu de règles, celles-ci pouvant s'enchaîner) est un booléen (true, false). Elle est génère des logs à chaque appel.

#### <a name="rights"></a>Les droits (ébauche)
Des commandes (actions) et des données peuvent être accessibles 
* en étant exposées dans une interface utilisateur de l'application Smart online
* en étant exposées dans des web services ou des API mis à disposition des utilisateurs.

Dans les deux cas, l'on peut considérer que quelque chose peut être
* exposé/traité unité par unité ou en lot
* vu ou non
* modifié ou non
* créé ou non
* supprimé ou non

En outre :

* chaque jeu de données, faisant forcément partie d'un document structuré (cfr norme UBL), les droits peuvent être rapporté à un objet ou à un noeud (un élément) de l'objet. Et donc, dans le contexte d'une application en ligne, à un champ (éditable ou non) ou un noeud HTML spécifique;
* chaque action, chaque commande, est par ailleurs identifable sur une interface, ou par des paramètres envoyés à une API ou un Web Service.

Dès lors, l'ensemble des droits peut être aisément (certes avec une grande verbosité) décrit sous la forme d'une liste (objet, noeuds, actions) dont chaque item est doté de 5 attributs booléens (visible ou non + modifiable ou non + créable ou non + supprimable ou non + par lot ou non).  
  
A priori, cela suffit au business et dans cette ébauche à spécifier les droits sépcifiques d'un utilisateur dans un contexte donné.

L'on peut prédéfinir des jeux de droits labellisés "rôles".

#### <a name="objects"></a>Les objets
La norme UBL sera utilisée **systématiquement** (documents ABIE, Common Aggregate Components). Cette norme est mise à profit dans tous les aspects de l'application. Elle fournit 
* une sémantique normée et systématique,
* des schémas de données,
* des règles de personnalisation si besoin est, 
* et une méthode d'identification du chemin de chaque donnée (XPath).

En matière de personnalisation, on utilisera le composant UBLExtensions qui comprend le noeud <a href="http://www.datypic.com/sc/ubl23/e-ext_UBLExtension.html" target="_blank">UBLExtension</a> (tous les composants de type CAC ont en standard un noeud UBLExtensions à disposition) pour ajouter là où c'est nécessaire 
* des paires clé/valeur dans un composant AdditionalProperty à créer :
```xml
<cac:AdditionalProperty>
    <cbc:ID listID="" listName="" listAgencyID="" listAgencyName="" listVersionID=""></cbc:ID>
    <cbc:Name></cbc:Name>
    <cbc:Value></cbc:Value>
    <cbc:ValueQualifier></cbc:ValueQualifier>
</cac:AdditionalProperty>
```
*  le composant <a href="http://www.datypic.com/sc/ubl23/t-cac_PeriodType.html" target="_blank">ValidityPeriod</a>
* l'élément <a href="https://www.truugo.com/ubl/2.3/cbc_sequencenumberidtype/" target="_blank">SequenceNumberID</a>, pour les composants de cardinalité 0/1 ... n. Notamment quand de multiples items d'un même composant sont ordonnés dans le temps (historisation). Ce sera par exemple le cas pour un "Person" comportant plusieurs adresses enregistrant les changements d'adresse à partir d'une adresse initiale.

Là où on a besoin d'une validation "pour un usage d'échange de données normées avec le monde extérieur" (Invoice et CreditNote), on utilisera un validateur Peppol. Sinon, un validateur UBL 2.3 suffira, nonobstant les éléments personnalisés.

La notion de langue est implémentée dans la norme.

### <a name="uionline"></a>L'interface online
Afin d'avancer dans ce cahier de charges, nous présentons ici un schéma d'interface ***à titre d'exemple***. Toute réflexion sur l'interface online de l'application se devra de prendre en compte les adaptations à apporter à l'ensemble du cahier de charges quand celui-ci prend pour exemple le schéma (élémentaire et réduit au strict nécessaire pour son usage d'exemple) ici proposé.

| Ecran (scenario de saisie)(AppFlow) | composants | Commentaires |
|-|-|-|
| public | | |
| public_connexion | htmlButton_seconnecter | |
| public_connexion | htmlButton_senregistrer, _signer | |
| public_formations | | |
| public_infos | | |
| private | | |
| private_nav | htmlButton_profile, _devenirsocietaire, _viecoop, _infos, _formations, _business, _offresemplois | |
| private_section_profile | htmlButton_enregistrer | |
| private_section_devenirsocietaire | htmlButton_souscrire, _payer, _signer | |
| private_section_viecoop | subsection_evenements, _mespartsociales_htmlButton_souscrire, _payer | |
| private_section_infos | htmlButton_envoyermaquestion | ... dont la base de connaissance |
| private_section_formations | subsection_sinscrire.htmlButton_enregistrer | |
| private_section_business | (subsection_personnel_profile, _operations, _entities_profile, _operations) => [htmlButton_creer, _supprimer, _modifier, _signer, _soumettre] | la sous-section personnel reprend principalement ce qui concerne les revenus personnels, et la sous-section entities, tout ce qui concerne les entités dont l'utilisateur s'occupe d'une manière ou d'une autre |
| private_section_offresemplois | htmlButton_soumettre | |
| private_section_advisor | ... (+ htmlButton_validate dans le contexte) |  en plus de cette section dédiée, le bouton "validate" apparaîtra dans les autres sections là où c'est nécessaire |
| private_section_supervisor | ... (+ htmlButton_validate dans le contexte) | " |
| private_section_configurator | ... (+ htmlButton_validate dans le contexte) | " |

Ce schéma nous permettra de décrire sommairement le comportement de l'interface à chaque action. On le complètera au fur et à mesure des besoins rencontrés dans le cahier des charges.

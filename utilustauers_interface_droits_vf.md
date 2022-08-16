# Interfaces, utilisateurs, droits

Les utilisateurs disposent de droits relatifs
* à leur activité dans l'éco-système à titre personnel : l'information utile à cet égard est enregistrée dans  USER (ubl: Person) et conditionné par l'état actuel (enregistré, qualifié, authentifié, suspendu, neutralisé) de l'instance les concernant :
* à leur activité dans l'éco-système en tant que qu'agent d'une ou plusieurs entités LegalPerson ou Organization : **l'information utile à cet égard est enregistrée dans  LegalPerson et Organization (ubl: Party/Person/RoleCode et Party/PartyAuthorization)** et conditionné par l'état actuel (enregistré, qualifié, authentifié, suspendu, neutralisé) de l'instance de chaque LegalPerson et/ou Organization dans lesquels ils sont nommés (Party/Person[]/ID = User.ID pour l'utilisateur connecté).

**C'est ainsi que l'on identifie un conseiller, un superviseur : un item "Person" avec le RoleCode ad hoc dans l'instance d'une Organization dédiée à Smart. Le noeud UBLExtensions de cet item Person comprendra un élément ValidityPeriod délimitant dans le temps l'effectivité des droits de cette Person, dans ce RoleCode, pour cette Organization. Tout comme l'on identifie un utilisateur "Titulaire".**

* **Un utilisateur dans l'état "enregistré"** ne peut agir dans aucune LegalPerson ni Organization. Il ne peut donc agir qu'à titre personnel.

| Ecran (scenario de saisie)(AppFlow) | Droits |
|-|-|
| private_nav | htmlButton_profile, _devenirsocietaire, _viecoop, _infos, _formations, _business, _offresemplois |
| private_section_profile | htmlButton_enregistrer |
| private_section_devenirsocietaire | htmlButton_souscrire, _payer, _signer |
| private_section_viecoop | subsection_evenements, ~~_mespartsociales_htmlButton_souscrire, _payer~~ |
| private_section_infos | ~~htmlButton_envoyermaquestion~~ | 
| private_section_formations | ~~subsection_sinscrire.htmlButton_enregistrer~~ |
| private_section_business | (subsection_personnel_profile, _operations, _entities_profile, _operations) => [htmlButton_creer, _supprimer, _modifier, _signer, _soumettre] VERSION SANDBOX| 
| private_section_offresemplois | ~~htmlButton_soumettre~~ |

* **Un utilisateur dans l'état "qualifié"** ne peut agir dans aucune LegalPerson ni Organization. Il ne peut donc agir qu'à titre personnel.

| Ecran (scenario de saisie)(AppFlow) | Droits |
|-|-|
| private_nav | htmlButton_profile, _devenirsocietaire, _viecoop, _infos, _formations, _business, _offresemplois |
| private_section_profile | htmlButton_enregistrer |
| private_section_devenirsocietaire | htmlButton_souscrire, _payer, _signer |
| private_section_viecoop | subsection_evenements, ~~_mespartsociales_htmlButton_souscrire, _payer~~ |
| private_section_infos | htmlButton_envoyermaquestion | 
| private_section_formations | subsection_sinscrire.htmlButton_enregistrer |
| private_section_business | (subsection_personnel_profile, _operations, _entities_profile, _operations) => [htmlButton_creer, _supprimer, _modifier, _signer, _soumettre] VERSION SANDBOX| 
| private_section_offresemplois | htmlButton_soumettre |

* **Un utilisateur dans l'état "authentifié"** qui n'est pas "mutualisé" et sans avoir signé CURU ne peut agir dans aucune LegalPerson ni Organization. Il ne peut donc agir qu'à titre personnel.
    
1. S'il n'est pas sociétaire :  
  
| Ecran (scenario de saisie)(AppFlow) | Droits |
|-|-|
| private_nav | htmlButton_profile, _devenirsocietaire, _viecoop, _infos, _formations, _business, _offresemplois |
| private_section_profile | htmlButton_enregistrer |
| private_section_devenirsocietaire | htmlButton_souscrire, _payer, _signer |
| private_section_viecoop | subsection_evenements, ~~_mespartsociales_htmlButton_souscrire, _payer~~ |
| private_section_infos | htmlButton_envoyermaquestion | 
| private_section_formations | subsection_sinscrire.htmlButton_enregistrer |
| private_section_business | (subsection_personnel_profile, _operations, _entities_profile, _operations) => [htmlButton_creer, _supprimer, _modifier, _signer, _soumettre] Version sandbox pour la subsection "entities", version opérationnelle pour la subsection "personnel"| 
| private_section_offresemplois | htmlButton_soumettre |

2.  S'il est sociétaire :

| Ecran (scenario de saisie)(AppFlow) | Droits |
|-|-|
| private_nav | htmlButton_profile, ~~_devenirsocietaire~~, _viecoop, _infos, _formations, _business, _offresemplois |
| private_section_profile | htmlButton_enregistrer |
| ~~private_section_devenirsocietaire~~ | ~~htmlButton_souscrire, _payer, _signer~~ |
| private_section_viecoop | subsection_evenements, _mespartsociales_htmlButton_souscrire, _payer |
| private_section_infos | htmlButton_envoyermaquestion | 
| private_section_formations | subsection_sinscrire.htmlButton_enregistrer |
| private_section_business | (subsection_personnel_profile, _operations, _entities_profile, _operations) => [htmlButton_creer, _supprimer, _modifier, _signer, _soumettre] Version sandbox pour la subsection "entities", version opérationnelle pour la subsection "personnel"| 
| private_section_offresemplois | htmlButton_soumettre |

3. S'il est personnel mutualisé et si un RoleCode le lui permet, il a accès à la private_section_business > subsection "entities" en version opérationnelle.

4. S'il a signé le CURU et crée au moins une "Organization", il a accès à private_section_business > subsection "entities" en version opérationnelle.

5. Les titulaires d'une Organization peuvent donner mandat à des utilisateurs "lambda" (simplement dans l'état "enregistré") pour accèder, en plus des droits propre à cet état, à private_section_business > subsection "entities" en version opérationnelle.




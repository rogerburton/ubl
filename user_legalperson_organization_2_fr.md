# User, LegalPerson, Organization : cahier de charge
## Fonctions générales (classes de règles)
Paramètres : (data, context, scope, use)
* f_isUnique : vérifie si une donnée est unique dans le périmètre défini, et pour quel usage
* f_exists : vérifie si une donnée existe
* f_isWellFormed : vérifie si la donnée est bien formée et valide
* f_rigths : vérifie les droits
* f_signature : vérifie une signature
* f_inValidityPeriod : vérifie si la requête est dans les temps imparti
* f_isConsistent : vérifie si le jeu de donnée est consistant (règles de gestion inter-données dans le périmètre défini)
* f_requestTo : intervention humaine ou mécanique (advisor, supervisor, sys, ...) spécifique

L'usage de ces fonctions est spécifié dans chaque table d'états et transitions. Elles servent surtout dans cette ébauche à "classer" les règles.

## User
**UBL : Person** (cac) <a href="http://www.datypic.com/sc/ubl23/e-cac_Person.html" target="_blank">http://www.datypic.com/sc/ubl23/e-cac_Person.html</a>  
(instances enregistrées en tant que documents à part entière).

**Données** importantes:
* Label : **UserID** : /Person/ID : la référence Smart, unique (portée globale)
* Label : **UserLogin** : /Person/IdentityDocumentReference FOR /DocumentDescription ="Application Disclaimer"  => /Attachment/ApplicationDisclaimerContract/identifiers/login
* Label : **UserPassword** : /Person/IdentityDocumentReference FOR /DocumentDescription ="Application Disclaimer"  => /Attachment/ApplicationDisclaimerContract/identifiers/password
* Label : **DisclaimerIssueDate** : /Person/IdentityDocumentReference FOR /DocumentDescription ="Application Disclaimer"  => /IssueDate
* Label : **DisclaimerSignature** : /Person/IdentityDocumentReference FOR /DocumentDescription ="Application Disclaimer"  => /ResultOfVerification/ValidationResultCode
* Label : **FinAccount** : /Person/FinancialAccount/ID
* Label : **NISSNumber** : /Person/IdentityDocumentReference FOR /DocumentType ="NISS"  => /ID
* Label : **EIDNumber** : /Person/IdentityDocumentReference FOR /DocumentType ="Eid"  => /ID
* Label : **NISSVerification** : /Person/IdentityDocumentReference FOR /DocumentType ="NISS"  => /ResultOfVerification/ValidationResultCode
* Label : **CURUVerification** : /Party FOR /PartyIdentification/ID/@schemeID="OSmart" AND /PartyAuthorization/PurposeCode/@listName="Smart" AND /Person/RoleCode="Titulaire" AND /Person/IdentityDocumentReference FOR /DocumentType ="CURU" => /Person/IdentityDocumentReference/ResultOfVerification/ValidationResultCode
* Label : **CURUIssueDate** : /Party FOR /PartyIdentification/ID/@schemeID="OSmart" AND /PartyAuthorization/PurposeCode/@listName="Smart" AND /Person/RoleCode="Titulaire" AND /Person/IdentityDocumentReference FOR /DocumentType ="CURU" => /Person/IdentityDocumentReference/IssueDate
* Label : **ListOfUP** : /Party FOR /PartyIdentification/ID/@schemeID="OSmart" AND /PartyAuthorization/PurposeCode/@listName="Smart" AND /Person/RoleCode="Titulaire" AND /Person/IdentityDocumentReference FOR /DocumentType ="CURU" AND /Person/IdentityDocumentReference/ResultOfVerification/ValidationResultCode="signé" AND /Person/ID="..." => /Party/PartyIdentification/ID

...

*Les UBL Person sont enregistrés en tant que document xml sous le type "user".*

**Etats possibles** : "enregistré", "qualifié", "authentifié", "suspendu", "neutralisé"

**Evénements** auxquels l'objet "User" peut répondre : "user_Create_Account_Requested", "user_Account_Created", "user_Account_PartialQualified", "user_Account_Qualified", "user_Account_Updated"

#### LifeCycle

|Context:AppFlow|Event|FROM|Rules|Result|TO|output_Event|Comment|
|-|-|-|-|-|-|-|-|
|public_signup|user_Create_Account_Requested|(start)|f_isUnique(data:[login,password], scope:"global") && f_isWellFormed(data:[login, password]) |True|enregistré|user_Account_Created(newContext:sys)|-|
|"|"|"|"|False|(end)|-|-|
|sys|user_Account_Created|enregistré|f_isWellFormed(data:[FirstName, FamilyName, ResidenceAddress, BirthDate]) && f_exists(data:[ResidenceAddress, ElectronicMail],scope:"global")|True|enregistré|user_Account_PartialQualified(newContext:sys)|-|
|"|"|"|"|False|enregistré|(none)|-|
|sys|user_Account_PartialQualified|enregistré|f_isWellFormed(data:[FinAccount]) && f_exists(data:[FinAccount],scope:"global")|True|qualifié|user_Account_Qualified(newContext:sys)|-|
|"|"|"|"|False|enregistré|(none)|-|
|sys|user_Account_Qualified|qualifié|f_isWellFormed(data:[EIDNumber]) && f_exists(data:[EIDNumber],scope:"global")|True|authentifié|user_Account_Authenticated(newContext:sys)|-|
|"|"|"|"|False|qualifié|(none)|-|
|private_supervisor|account_suspended|enregistré, qualifié, authentifié|f_rights(userID)|True|suspendu|user_Account_suspended|-|
|"|"|"|"|False|(état d'entrée)|(none)|-|
|private_supervisor|account_neutralized|enregistré, qualifié, authentifié|f_rights(userID)|True|neutralisé|user_Account_neutralized|-|
|"|"|"|"|False|(état d'entrée)|(none)|-|
|private_supervisor|account_reactivated|neutralisé, suspendu|f_rights(userID)|True|enregistré|user_Account_Created(newContext:sys)|-|
|"|"|"|"|False|(état d'entrée)|(none)|-|

## LegalPerson
**UBL : Party** (cac) <a href="http://www.datypic.com/sc/ubl23/e-cac_Person.html" target="_blank">http://www.datypic.com/sc/ubl23/e-cac_Person.html</a>  
(instances enregistrées en tant que documents à part entière).

**Données** importantes:

...

*Les UBL Party sont enregistrés en tant que document xml sous le type "legalperson".*

**Etats possibles** : "enregistré", "qualifié", "authentifié", "suspendu", "neutralisé"

**Evénements** auxquels l'objet "LegalPerson" peut répondre : "legalperson_Create_Account_Requested", "legalperson_Account_Created", "legalperson_Account_PartialQualified", "legalperson_Account_Qualified", "legalperson_Account_Updated"

#### LifeCycle

|Context:AppFlow|Event|FROM|Rules|Result|TO|output_Event|Comment|
|-|-|-|-|-|-|-|-|
|public_signup|legalperson_Create_Account_Requested|(start)|f_isUnique(data:[BCE OU TVA], scope:"global") && f_exists(data:[login, password]) |True|enregistré|legalperson_Account_Created(newContext:sys)|-|
|"|"|"|"|False|(end)|-|-|
|sys|legalperson_Account_Created|enregistré|f_exists(data:[FinAccount],scope:"global")|True|enregistré|legalperson_Account_PartialQualified(newContext:sys)|-|
|"|"|"|"|False|enregistré|(none)|-|
|sys|legalperson_Account_PartialQualified|enregistré|f_exists(data:[Contact],scope:"global")|True|qualifié|legalperson_Account_Qualified(newContext:sys)|-|
|"|"|"|"|False|enregistré|(none)|-|
|private_supervisor|account_suspended|enregistré, qualifié|f_rights(legalpersonID)|True|suspendu|legalperson_Account_suspended|-|
|"|"|"|"|False|(état d'entrée)|(none)|-|
|private_supervisor|account_neutralized|enregistré, qualifié|f_rights(legalpersonID)|True|neutralisé|legalperson_Account_neutralized|-|
|"|"|"|"|False|(état d'entrée)|(none)|-|
|private_supervisor|account_reactivated|neutralisé, suspendu|f_rights(legalpersonID)|True|enregistré|legalperson_Account_Created(newContext:sys)|-|
|"|"|"|"|False|(état d'entrée)|(none)|-|

## Organization
**UBL : Party** (cac) <a href="http://www.datypic.com/sc/ubl23/e-cac_Person.html" target="_blank">http://www.datypic.com/sc/ubl23/e-cac_Person.html</a>  
(instances enregistrées en tant que documents à part entière).

**Données** importantes:

...

*Les UBL Party sont enregistrés en tant que document xml sous le type "organization".*

**Etats possibles** : "enregistré", "qualifié", "authentifié", "suspendu", "neutralisé"

**Evénements** auxquels l'objet "organization" peut répondre : "organization_Create_Account_Requested", "organization_Account_Created", "organization_Account_PartialQualified", "organization_Account_Qualified", "organization_Account_Updated"

#### LifeCycle

|Context:AppFlow|Event|FROM|Rules|Result|TO|output_Event|Comment|
|-|-|-|-|-|-|-|-|
|public_signup|organization_Create_Account_Requested|(start)|f_isUnique(data:[Name], scope:"global") && f_rights(data:[userID]) |True|enregistré|organization_Account_Created(newContext:sys)|-|
|"|"|"|"|False|(end)|-|-|
|sys|organization_Account_Created|enregistré|f_isWellFormed(data:[IndustryClassificationCode],scope:"global")|True|qualifié|organization_Account_Qualified(newContext:sys)|-|
|"|"|"|"|False|enregistré|organization_Account_Qualified(newContext:sys)|-|
|sys|organization_Account_Qualified|qualifié|f_signature(CURU)|True|authentifié|(none)|-|
|"|"|"|"|False|qualifié|(none)|-|
|private_supervisor|account_suspended|enregistré, qualifié|f_rights(organizationID)|True|suspendu|organization_Account_suspended|-|
|"|"|"|"|False|(état d'entrée)|(none)|-|
|private_supervisor|account_neutralized|enregistré, qualifié|f_rights(organizationID)|True|neutralisé|organization_Account_neutralized|-|
|"|"|"|"|False|(état d'entrée)|(none)|-|
|private_supervisor|account_reactivated|neutralisé, suspendu|f_rights(organizationID)|True|enregistré|organization_Account_Created(newContext:sys)|-|
|"|"|"|"|False|(état d'entrée)|(none)|-|


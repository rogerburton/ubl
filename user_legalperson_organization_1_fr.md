# Smart Application (vf) : les utilisateurs, les entités

## Discussion

Un utilisateur est défini comme étant une personne ayant accès à tout ou partie d'une application Smart et susceptible d'interagir avec lui, en étant connecté et authentifié par le moyen d'une paire login/password unique.
Un utilisateur est une personne physique, à l'exception des "user_system", catégorie réservée aux automates pouvant agir en tant qu'utilisateur (exemple : l'utilisateur connecté agissant via l'API "Deliveroo", ou encore l'application Smart elle-même quand elle modifie l'état du système).

Une personne morale (dotée d'une personnalité juridique) peut à l'occasion devoir agit par l'intermédiaire d'une personne physique non identifiée (generic user) en tant que telle dans le système. cette personne anonyme sera créée en tant que "Person" (UBL) et reliée à la PartyLegalEntity dont elle relève. A charge pour la PartyLegalEntity de confier elle-même à l'un de ses agents l'usage de cette "personne anonyme".

Une organisation est un regroupement de personnes (morales et/ou physiques) ou un département (sens générique) d'une personne morale ou d'une organisation. Pour être enregistré comme telle dans le système, elles doivent être d'une manière ou d'une autre reconnues par Smart.

## UBL
|Objets Smart|UBL|Etats|
|-|-|-|
|User|<a href="http://www.datypic.com/sc/ubl23/t-cac_PersonType.html" target="_blank">Person</a>|enregistré, qualifié, authentifié, neutralisé, suspendu|
|LegalPerson|<a href="http://www.datypic.com/sc/ubl23/t-cac_PartyType.html" target="_blank">Party</a>|enregistré, qualifié, authentifié, neutralisé, suspendu|
|Organization|<a href="http://www.datypic.com/sc/ubl23/t-cac_PartyType.html" target="_blank">Party</a>|enregistré, qualifié, authentifié, neutralisé, suspendu|

Nous prenons le parti, qui doit être éprouvé, que le même jeu d'états peut servir aux trois objets.

L'on distingue les LegalPersons des Organizations en utilisant les noeuds UBL du composant <a href="http://www.datypic.com/sc/ubl23/e-cac_PartyLegalEntity.html" target="_blank">PartyLegalEntity</a> du composant ***Party***: CompanyLegalFormCode (cbc [0..1]) et CorporateRegistrationScheme (cac [0..1]).

Elles ont une durée de vie qui peut être spécifiée par RegistrationDate (cbc [0..1]), RegistrationExpirationDate (cbc [0..1]) et CompanyLiquidationStatusCode (cbc [0..1]).

Le cas échéant, la déclinaison de services applicable à Party (éventuellement, pour une période déterminée où à partir d'un moment déterminé) est spécifiée dans un composant UBLExtension.

### Exemples d'instances

#### Enregistrement initial (signup))

```xml
<cac:Person>
    <cbc:ID schemeID="" schemeName="" schemeAgencyID="" schemeAgencyName="" schemeVersionID="">455000001</cbc:ID>
    <cac:IdentityDocumentReference>
        <cbc:ID schemeID="" schemeName="" schemeAgencyID="" schemeAgencyName="" schemeVersionID="">numéro du contrat</cbc:ID>
        <cbc:DocumentTypeCode listID="" listName="" listAgencyID="" listAgencyName="" listVersionID="">smartdoc</cbc:DocumentType>
        <cbc:DocumentType>Contract</cbc:DocumentType>
        <cbc:DocumentDescription>Application Disclaimer</cbc:DocumentType>
        <cbc:DocumentStatusCode listID="" listName="" listAgencyID="" listAgencyName="" listVersionID="">Signé</cbc:DocumentType>
        <cac:Attachment>
            <cbc:EmbeddedDocument><![CDATA[<?xml version="1.0" encoding="UTF-8"?><ApplicationDisclaimerContract><contract> <![CDATA[le texte du contrat]]></contract><identifiers><login>mylogin</login><password>mypassword</password></identifers></accesContract>]]</cbc:EmbeddedDocument>
        </cac:Attachment>
        <cac:ValidityPeriod>
            <cbc:StartDate>2022-12-21</cbc:StartDate>
            <cbc:EndDate>2022-12-21</cbc:EndDate>
        </cac:ValidityPeriod>
        <cbc:IssueDate></cbc:IssueDate>
        <cbc:IssueTime></cbc:IssueTime>
        <cac:IssuerParty>
            <cac:PartyIdentification>
                <cbc:ID schemeID="" schemeName="" schemeAgencyID="" schemeAgencyName="" schemeVersionID="">SMA000</cbc:ID>
            </cac:PartyIdentification>
        </cac:IssuerParty>
        <cac:ResultOfVerification>
            <cbc:ValidatorID schemeID="" schemeName="" schemeAgencyID="" schemeAgencyName="" schemeVersionID=""></cbc:ValidatorID>
            <cbc:ValidationResultCode></cbc:ValidationResultCode>
            <cbc:ValidationDate></cbc:ValidationDate>
            <cbc:ValidationTime></cbc:ValidationTime>
            <cbc:ValidateTool></cbc:ValidateTool>
            <cbc:ValidateToolVersion></cbc:ValidateToolVersion>
        </cac:ResultOfVerification>
    </cac:IdentityDocumentReference>
</cac:Person>

```
**Etat** : User["ID"="45500000"]_state="**enregistré**"

A noter : On utilise le noeud "IdentityDocumentReference" (cac [0..n]) pour tous les contrats qui affecte une identité spécifique à un utilisateur.

#### Un utilisateur qualifié
**Le même schéma que le précédent, auquel on ajoute** un noeud Person.ResidenceAddress et les trois noeuds FirstName, FamilyName et BirthDate

```xml
<cac:Person>
    <cbc:BirthDate>1957-06-27</cbc:BirthDate>
    <cbc:FamilyName>Burton</cbc:FamilyName>
    <cbc:FirstName>Roger</cbc:FirstName>
    <cac:ResidenceAddress>
        <cbc:ID>ad000002</cbc:ID>
        <cbc:StreetName>rue du Test</cbc:StreetName>
        <cbc:BuildingNumber>1</cbc:BuildingNumber>
        <cbc:CityName>Namur</cbc:CityName>
        <cbc:PostalZone>4000</cbc:PostalZone>
        <cac:Country>
            <cbc:Name>Belgique</cbc:Name>
        </cac:Country>
    </cac:ResidenceAddress>
</cac:Person>
```
**Etat** : User["ID"="45500000"]_state="**qualifié**"

#### Un utilisateur authentique

**Le même schéma que le précédent, auquel on ajoute** un noeud Person.FinancialAccount et un item dans le noeud Person.IdentityDocumentReference
```xml
<cac:Person>
    <cac:FinancialAccount>
        <cbc:ID schemeID="" schemeName="" schemeAgencyID="" schemeAgencyName="" schemeVersionID="">BE33 1234 1234 1234</cbc:ID>
        <cbc:AccountFormat>Iban</cbc:AccountFormat>
        <cbc:Currency>Euro</cbc:Currency>
        <cac:Country>
            <cbc:Name>Belgique</cbc:Name>
        </cac:Country>
    </cac:FinancialAccount>
</cac:Person>
```

```xml
<cac:Person>
    <cac:IdentityDocumentReference>
        <cbc:ID schemeID="" schemeName="" schemeAgencyID="" schemeAgencyName="" schemeVersionID="">Eid123456789</cbc:ID>
        <cbc:DocumentTypeCode listID="" listName="" listAgencyID="" listAgencyName="" listVersionID="">Eid</cbc:DocumentType>
        <cbc:DocumentType>Eid</cbc:DocumentType>
        <cbc:DocumentDescription>Carte d'identité belge</cbc:DocumentType>
        <cbc:DocumentStatusCode listID="" listName="" listAgencyID="" listAgencyName="" listVersionID="">vérifié</cbc:DocumentType>
        <cac:ValidityPeriod>
            <cbc:StartDate>2022-12-21</cbc:StartDate>
            <cbc:EndDate>2022-12-21</cbc:EndDate>
        </cac:ValidityPeriod>
        <cbc:IssueDate></cbc:IssueDate>
        <cbc:IssueTime></cbc:IssueTime>
        <cac:IssuerParty>
            <cac:PartyIdentification>
                <cbc:ID schemeID="" schemeName="" schemeAgencyID="" schemeAgencyName="" schemeVersionID="">SMA000</cbc:ID>
            </cac:PartyIdentification>
        </cac:IssuerParty>
        <cac:ResultOfVerification>
            <cbc:ValidatorID schemeID="" schemeName="" schemeAgencyID="" schemeAgencyName="" schemeVersionID=""></cbc:ValidatorID>
            <cbc:ValidationResultCode></cbc:ValidationResultCode>
            <cbc:ValidationDate></cbc:ValidationDate>
            <cbc:ValidationTime></cbc:ValidationTime>
            <cbc:ValidateTool></cbc:ValidateTool>
            <cbc:ValidateToolVersion></cbc:ValidateToolVersion>
        </cac:ResultOfVerification>
    </cac:IdentityDocumentReference>
</cac:Person>
```

**Etat** : User["ID"="45500000"]_state="**authentifié**"

#### Une entité légale

```xml
<cac:Party>
   <cac:PartyIdentification>
      <cbc:ID schemeID="LSmart" schemeName="" schemeAgencyID="Smart" schemeAgencyName="" schemeVersionID="">SCH001</cbc:ID>
   </cac:PartyIdentification>
   <cac:PartyLegalEntity>
      <cbc:CompanyID schemeID="BCE" schemeName="" schemeAgencyID="" schemeAgencyName="Belgique" schemeVersionID="">BCExxx</cbc:CompanyID>
      <cbc:RegistrationName>Schleiper</cbc:RegistrationName>
   </cac:PartyLegalEntity>
   <cac:Person>
      <cac:Contact>
         <cbc:ElectronicMail>info@schleiper.com</cbc:ElectronicMail>
         <cbc:ID>generic000002</cbc:ID>
      </cac:Contact>
      <cbc:ID>455000005</cbc:ID>
      <cbc:RoleCode>genericContact</cbc:RoleCode>
   </cac:Person>
</cac:Party>
```
**Etat** : LegalPerson["Party.PartyIdentification.ID"="SCH001"]_state="**enregistré**"

#### Une organisation (de type "Unité de production Smart")

```xml
<cac:Party>
   <cac:PartyIdentification>
      <cbc:ID schemeID="OSmart" schemeName="" schemeAgencyID="Smart" schemeAgencyName="" schemeVersionID="">489000000</cbc:ID>
   </cac:PartyIdentification>
   <cac:PartyName>
      <cbc:Name>489000000</cbc:Name>
   </cac:PartyName>
   <cbc:IndustryClassificationCode listID="" listName="NACEBEL" listAgencyID="" listAgencyName="" listVersionID="">nace1</cbc:IndustryClassificationCode>
   <cbc:IndustryClassificationCode listID="" listName="NACEBEL" listAgencyID="" listAgencyName="" listVersionID="">nace2</cbc:IndustryClassificationCode>
   <cac:PartyAuthorization>
       <cbc:PurposeCode listID="" listName="Smart" listAgencyID="" listAgencyName="" listVersionID="">2</cbc:PupopseCode>
       <cac:ValidityPeriod>
          <cbc:StartDate>2022-12-21</cbc:StartDate>
          <cbc:EndDate>2022-12-21</cbc:EndDate>
       </cac:ValidityPeriod>
   </cac:PartyAuthorization>
   <cac:Person>
      <cac:Contact>
         <cbc:ElectronicMail>bur@gmail.com</cbc:ElectronicMail>
         <cbc:ID>contact000001</cbc:ID>
      </cac:Contact>
      <cbc:ID>455000005</cbc:ID>
      <cbc:RoleCode>Titulaire</cbc:RoleCode>
      <cac:IdentityDocumentReference>
        <cbc:ID schemeID="" schemeName="" schemeAgencyID="" schemeAgencyName="" schemeVersionID="">numéro du contrat</cbc:ID>
        <cbc:DocumentTypeCode listID="" listName="" listAgencyID="" listAgencyName="" listVersionID="">smartdoc</cbc:DocumentType>
        <cbc:DocumentType>Contract</cbc:DocumentType>
        <cbc:DocumentDescription>CURU</cbc:DocumentType>
        <cbc:DocumentStatusCode listID="" listName="" listAgencyID="" listAgencyName="" listVersionID="">Signé</cbc:DocumentType>
        <cac:Attachment>
            <cbc:EmbeddedDocument><![CDATA[<?xml version="1.0" encoding="UTF-8"?><ApplicationDisclaimerContract><contract> <![CDATA[le texte du contrat]]></contract></accesContract>]]</cbc:EmbeddedDocument>
        </cac:Attachment>
        <cac:ValidityPeriod>
            <cbc:StartDate>2022-12-21</cbc:StartDate>
            <cbc:EndDate>2099-12-31</cbc:EndDate>
        </cac:ValidityPeriod>
        <cbc:IssueDate></cbc:IssueDate>
        <cbc:IssueTime></cbc:IssueTime>
        <cac:IssuerParty>
            <cac:PartyIdentification>
                <cbc:ID schemeID="" schemeName="" schemeAgencyID="" schemeAgencyName="" schemeVersionID="">SMA000</cbc:ID>
            </cac:PartyIdentification>
        </cac:IssuerParty>
        <cac:ResultOfVerification>
            <cbc:ValidatorID schemeID="" schemeName="" schemeAgencyID="" schemeAgencyName="" schemeVersionID=""></cbc:ValidatorID>
            <cbc:ValidationResultCode></cbc:ValidationResultCode>
            <cbc:ValidationDate></cbc:ValidationDate>
            <cbc:ValidationTime></cbc:ValidationTime>
            <cbc:ValidateTool></cbc:ValidateTool>
            <cbc:ValidateToolVersion></cbc:ValidateToolVersion>
        </cac:ResultOfVerification>
     </cac:IdentityDocumentReference>
   </cac:Person>
</cac:Party>
```
et l'instance de l'objet "Party" dédiée à Productions associées asbl (et/ou Formateurs Associés, etc.) ajoutera un item à son noeud "AgentParty"

```xml
<cac:Party>
    <cac:AgentParty>
        <cac:PartyIdentification>
            <cbc:ID schemeID="OSmart" schemeName="" schemeAgencyID="" schemeAgencyName="Smart" schemeVersionID="">489000001</cbc:ID>
        </cac:PartyIdentification>
    </cac:AgentParty>
    <cac:AgentParty>
        <cac:PartyIdentification>
            <cbc:ID schemeID="OSmart" schemeName="" schemeAgencyID="" schemeAgencyName="Smart" schemeVersionID="">489000002</cbc:ID>
        </cac:PartyIdentification>
    </cac:AgentParty>
</cac:Party>
```
### Notes
Il apparaît donc que pour la gestion des utilisateurs, des entités légales et des organisations, on peut se contenter d'utiliser deux composants UBL, pas plus : Person et Party, qui seraient enregistrés en tant que tel.  

L'utilisation des attributs permet une gestion fine des "catégories" dont on pourrait avoir besoin.

Enfin, il est à noter que les informations relevant d'une même "identité" peuvent être disséminées : néanmoins une gestion rigoureuse du type d'information qui doit se retrouver ici plutôt que là permettra d'éviter toute difficulté relative au catalogage de l'information.
Cette gestion doit être spécifiée rigoureusement.

**A traiter : l'historisation des informations**
*La norme UBL a prévu un mécanisme pour historiser les documents, et qui peut être utilisé pour historiser également ces trois objets (Person, LegalPerson, Organization, dans les deux UBL Person et Party) : il est implémenté pour le document ABIE Order : OrderChange. Il se contente de reprendre quasiment la structure du document Order moyennant 2-3 modifications, dont  un noeud supplémentaire pour indiquer la position d'ordre dans la séquence des changements. A priori, à la lecture de la norme, il semble qu'un document xxxChange reprend l'ensemble des données du document xxx ou xxxChange précédent, aux adaptations près évidemment (modifications, suppressions, ajouts).
On peut l'utiliser ici. D'autant que les changements sur ces objets sont à rotation lente (changement d'adresse, par exemple).*




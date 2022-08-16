# Discussion sur la paramétrisation d'une "finite state machine", d'un "rules engine", et sur la formalisation des règles, états et transitions

Ce à quoi nous tenons : amener les business analysts et l'équipe Produit à formaliser ses spécifications dans des lexiques et syntaxes déterminés,dans une formalisation forte. Eu égard à la nature de nos informations et objets à traiter et à l'importance, pour eux, des règles et des états et transitions, nous tenons à formaliser et standardiser l'expression de ceux-ci. Contrainte forte.

La standard UBL devrait en outre apporter, côté données, une standardisation transversale au front, au back, au business.

Cette formalisation/standardisation, outre l'avantage propre à toute norme et à toute formalisation (rigueur, univocité, universalité, communauté de compréhension, etc.), a un objectif didactique (pédagogique) et méthodologique, qui manque cruellement aujourd'hui à Smart.

La notion de paramétrisation rentre dans cet objectif : l'équipe Produit, les business analystes, etc. s'approprieront d'autant mieux ces standards et cette formalisation qu'ils auront l'impression de "commander" du bout des doigts le comportements du système.

**Néanmoins, en termes d'implémentation, *peu importe* que le système fonctionne ou non réellement à coup de paramètres directement lus dans des tables nourries par des tiers aux développeurs.**

Ce qui **seul** importe **au plus haut point** en termes d'implémentation informatique, c'est :
* **La certitude de l'univocité** : la certitude que chacun doit avoir qu'il n'y a pas une once possible d'interprétation sauvage des spécifications formalisées/standardisées émanant du business par les développeurs.
* **La rapidité de développement** lorsque des "paramètres" et des objets (règles, états et transitions, comportement de l'interface, droits, use case, etc.) sont modifiés, supprimés, ajoutés à l'initiative du business.

Nous devons construire une "architecture applicative" et des modèles de spécifications qui permette de répondre à ces deux besoins. Nous ne pouvons plus nous permettre de prendre des semaines, des mois et parfois plus encore pour développer la capacité du système à intégrer une nouvelle règle, un nouveau composant, un nouveau comportement, un nouvel état dans le cycle de vie d'un objet ...

Or cette rapidité de développement peut laisser croire (et particulièrement à un codeur fonctionnarisé ou médiocre) que l'on vise à diminuer le "besoin" de codage de l'entreprise, à atténuer le besoin de recourir à des développeurs, etc. Et ceux-ci peuvent donc être tenté de ne pas "jouer le jeu" d'une architecture souple, de prise en main rapide, versatile, etc. D'où l'intérêt de ne pas laisser totalement le choix aux futurs développeurs du "comment implémenter", et de les contraindre à structurer leur travail autour non seulement des concepts de "finite state machine " et de "rules engine", mais de leur implémentation réelle, même si en réalité nous n'avons besoin que de version assez simple d'une machine à états finis et d'un moteur de règles.

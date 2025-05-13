# KeyTrap : Vulnérabilité critique de DNSSEC

## Qu'est-ce que c'est ?

KeyTrap est une vulnérabilité critique de DNSSEC (DNS Security Extensions) qui permet, à l'aide d'un paquet DNS, de paralyser toutes les implémentations DNS courantes et les fournisseurs publics de DNS.

Cette technique s'appuie sur un défaut de conception fondamental dans le standard DNSSEC : le standard exige que les résolveurs testent toutes les clés cryptographiques disponibles contre toutes les signatures DNSSEC disponibles. Un attaquant peut exploiter cette exigence en créant un domaine malveillant avec de nombreuses clés utilisant le même key-tag et de nombreuses signatures référençant ce key-tag.

## Contexte historique

Cette vulnérabilité est **présente dans DNSSEC depuis plus de 20 ans**, mais n'a été découverte qu'en 2024 par une équipe de chercheurs allemands issus de :

- L'institut de recherche ATHENE
- L'université Goethe de Francfort
- Le Fraunhofer SIT
- L'université technique de Darmstadt

## Mécanisme d'attaque

Le résolveur, suivant fidèlement le standard, tentera de valider TOUTES les clés contre TOUTES les signatures, entraînant un effort de validation quadratique. Cette complexité algorithmique permet à l'attaquant d'épuiser les ressources CPU du résolveur avec un nombre suffisant de clés et signatures, réalisant ainsi un déni de service (DoS).

L'exploitation de cette vulnérabilité peut retarder les réponses DNS de **56 secondes à 16 heures** selon l'implémentation visée.

## Impact et portée

Sans correction, cette faille pourrait avoir de graves implications pour :

- Les implémentations de validation DNSSEC
- Les fournisseurs publics de DNS tels que Google et Cloudflare
- Toutes les applications utilisant Internet (navigateurs web, email, messagerie instantanée)

Selon un rapport d'Akamai : "_environ 35 % des utilisateurs d'Internet basés aux États-Unis et 30 % des utilisateurs d'Internet dans le monde utilisent des résolveurs DNS qui utilisent la validation DNSSEC et sont donc vulnérables à KeyTrap._"

D'après les chercheurs : "_Avec KeyTrap, un attaquant pourrait complètement désactiver de grandes parties de l'Internet mondial_".

## Systèmes affectés

Tous les résolveurs DNS implémentant le standard DNSSEC sont vulnérables, notamment :

- Bind9
- Unbound (NLNetLabs)
- dnsmasq
- PowerDNS Recursor
- DNS Server de Windows Server
- Google Public Resolver
- Cloudflare 1.1.1.1

Le tableau ci-dessous répertorie les différents résolveurs DNS impactés qui se trouve dans le rapport technique d'ATHENE :

![DNSSEC - KeyTrap - Tableau](https://www.it-connect.fr/wp-content-itc/uploads/2024/02/DNSSEC-KeyTrap-Tableau.png)

_Source : [Rapport technique ATHENE](https://www.athene-center.de/fileadmin/content/PDF/Keytrap_2401.pdf)_

## Solutions déployées

Depuis novembre 2023, des mesures d'atténuation ont été déployées par :

- **Google** et **Cloudflare** : Patches et optimisations
- **Akamai** : Mesures de protection
- **Microsoft** : Correction intégrée dans la mise à jour cumulative du Patch Tuesday de février 2024

Ces patches limitent le nombre de validations qu'un résolveur accepte d'effectuer et implémentent diverses optimisations pour mitiger l'attaque.
## Recommandations de sécurisation

La vulnérabilité KeyTrap souligne l'importance d'une approche multicouche pour la sécurisation DNS :

### Mesures techniques immédiates

- **Mise à jour des logiciels DNS** : Appliquer immédiatement les patches disponibles
- **Configuration des résolveurs** : Activer la validation DNSSEC avec des limites de validation appropriées
- **Monitoring** : Surveiller les temps de réponse DNS pour détecter d'éventuelles attaques

### Architecture DNS sécurisée

- **Séparation des zones** : Maintenir une séparation stricte entre zones publiques et internes
- **Redondance** : Implémenter des serveurs DNS secondaires pour la continuité de service
- **Restriction d'accès** : Configurer des ACL pour limiter les requêtes récursives

### Bonnes pratiques DNSSEC

- **Déploiement recommandé** : Malgré KeyTrap, DNSSEC reste essentiel pour l'authentification DNS
- **Gestion des clés** : Maintenir une rotation appropriée des clés KSK et ZSK
- **Validation rigoureuse** : Configurer les résolveurs pour valider les signatures DNSSEC
## Conclusion

KeyTrap représente une découverte majeure en cybersécurité, révélant une faille de conception présente dans DNSSEC depuis plus de deux décennies. Cette vulnérabilité démontre l'importance cruciale de la recherche académique en sécurité informatique pour identifier et corriger des défauts critiques dans l'infrastructure Internet.

Bien que KeyTrap ait un potentiel destructeur considérable - capable de paralyser de vastes portions d'Internet avec un simple paquet DNS - la réaction rapide de la communauté technique illustre l'efficacité de la collaboration entre chercheurs et industrie. Les patches déployés par tous les grands fournisseurs de DNS témoignent de la maturité de l'écosystème de sécurité Internet.

Cette découverte souligne également que même des protocoles bien établis et largement déployés comme DNSSEC peuvent receler des vulnérabilités non détectées. Elle rappelle l'importance de maintenir une vigilance constante et de continuer la recherche en sécurité pour protéger l'infrastructure critique d'Internet.

## Bibliographie

- [https://www.athene-center.de/en/keytrap](https://www.athene-center.de/en/keytrap)
- [https://www.athene-center.de/fileadmin/content/PDF/Keytrap_2401.pdf](https://www.athene-center.de/fileadmin/content/PDF/Keytrap_2401.pdf) (Rapport technique complet)
- [https://www.it-connect.fr/dnssec-un-seul-paquet-peut-perturber-lacces-a-internet-avec-lattaque-keytrap/](https://www.it-connect.fr/dnssec-un-seul-paquet-peut-perturber-lacces-a-internet-avec-lattaque-keytrap/)

## CVE

**CVE-2023-50387**

## RFC

### RFC liés à DNSSEC :

**RFC principaux (Core DNSSEC) :**

- **RFC 4033** - DNS Security Introduction and Requirements
- **RFC 4034** - Resource Records for the DNS Security Extensions
- **RFC 4035** - Protocol Modifications for the DNS Security Extensions

**RFC de mise à jour et clarifications :**

- **RFC 6840** - Clarifications and Implementation Notes for DNS Security (DNSSEC)
- **RFC 9364** - DNS Security Extensions (DNSSEC) - Document de synthèse (2023)

**RFC pour NSEC3 (amélioration de DNSSEC) :**

- **RFC 5155** - DNS Security (DNSSEC) Hashed Authenticated Denial of Existence
- **RFC 9276** - Guidance for NSEC3 Parameter Settings

**RFC opérationnels :**

- **RFC 6781** - DNSSEC Operational Practices, Version 2

## Utilisation de l'IA

J'ai utilisé l'IA (Claude) essentiellement pour corriger et améliorer les tournures de phrase de ce résumé. Je me suis aussi permis de l'utiliser pour retrouver les RFC qui sont utilisés ; j'ai bien évidemment relu tout ce qu'il me générait. Je l'ai aussi utilisé pour faire le pont entre ce que j'ai lu sur d'autres notes que j'ai trouvées sur KeyTrap.

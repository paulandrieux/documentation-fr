
# Créer un nouveau critère de personnalisation de contenu _(criteria)_

## Utilisation de l'objet DataSource

Le système de critères de Victoire repose sur l'objet [*DataSource*](https://github.com/Victoire/victoire/blob/master/Bundle/CriteriaBundle/DataSource/RequestDataSource.php) dont le rôle est de fournir :

- des méthodes qui retournent la valeur qui sert de filtre.
- des paramètres de formulaire qui seront utilisés dans la modale des widgets

## Etude de cas

Victoire inclut de base 2 types de fitres :

- un filtre sur la locale (ou language courant) : `_locale`
- un filtre sur le protocole : `scheme` http | https

Nous allons étudier le filtre sur la locale à titre d'exemple.

```php
#Victoire\Bundle\CriteriaBundle\DataSource

[...]

class RequestDataSource
{
    /**
     * @var RequestStack
     */
    private $requestStack;
    private $availableLocales;

    /**
     * RequestCriteria constructor.
     *
     * @param RequestStack $requestStack
     * @param              $availableLocales
     */
    public function __construct(RequestStack $requestStack, $availableLocales)
    {
        $this->requestStack = $requestStack;
        $this->availableLocales = $availableLocales;
    }

    public function getLocale()
    {
        return $this->requestStack->getCurrentRequest()->getLocale();
    }

[...]

    public function getLocaleFormParams()
    {
        return [
            'type'    => ChoiceType::class,
            'options' => [
                'choices' => $this->availableLocales,
            ],
        ];
    }

[...]

}
```

- La méthode "getLocale" retourne la locale de la requête courante
- La méthode "getLocaleFormParams" retourne le tableau de paramètres nécessaires pour générer un élément de formulaire de type Choice avec les locales activées comme choix.

## Créer un nouveau critère de personnalisation de contenu

### 1. Création de l'objet "DataSource"

Créer un nouvel obket "DataSource" qui implémente chacune des 2 méthodes :

- "getXxx" : définit la valeur Xxx servant de filtre
- "getXxxFormParams" : définit le formulaire intégrant les différentes variables possibles de la valeur Xxx

### 2. Déclaration de l'objet

Pour qu'il soit reconnu par Victoire comme un nouveau *critère*, il faut le déclarer en tant que service avec le tag suivant :

    tags:
        - { name: victoire_criteria, group: yyy, method: getXxx, alias: xxx }


Le tag propose d'associer un `group` au critère.
Ce groupe est utilisé pour afficher les différents critères par section. Vous êtes libres d'utiliser un groupe déjà existant ou d'en créer un nouveau.

Ceci est un robot branchant les alertes du site [liberation.fr](http://www.liberation.fr/slack) dans un channel [Slack](https://slack.com).

## Intégration

Avant tout, il faut intrégrer [Hubot](https://my.slack.com/services/new/hubot) à notre team Slack.
Une fois l'intégration configurée, on note son `API Token`.


## Lancer le robot

Pour lancer le robot (par exemple pour le tester avant de le déployer sur [Heroku](https://heroku.com)), il faut installer [Node.js®](https://nodejs.org/).
Et on démarre le robot avec notre `API Token`.

```bash
$> HUBOT_SLACK_TOKEN=<API Token> ./bin/hubot --adapter slack
...
```

(On peut toujours laisser le robot vivre comme ça, c'est très bien tant que personne n'éteint cet ordinateur).

## Déployer le robot sur Heroku

On peut aussi, par exemple, déployer le robot sur [Heroku](https://heroku.com).
Toutes les actions qui suivent utilisent [heroku toolbelt](https://toolbelt.heroku.com/), mais sont également accessibles depuis notre [dashboard](https://dashboard.heroku.com/).

On commence par créer une nouvelle application, puis on lui assigne les addons [rediscloud](https://elements.heroku.com/addons/rediscloud) et [scheduler](https://elements.heroku.com/addons/scheduler).

```bash
$> heroku create <foo bar>
...
$> heroku addons:create rediscloud:30
...
$> heroku addons:create scheduler:standard
...
```

On configure ensuite quelques variables (dont l'`API Token` qu'on a noté plus haut).

```bash
$> heroku config:add HUBOT_SLACK_TOKEN=<API Token>
...
$> heroku config:add HEROKU_URL=$(heroku apps:info -s | grep web-url | cut -d= -f2)
...
$> heroku config:add HUBOT_HEROKU_KEEPALIVE_URL=$(heroku apps:info -s | grep web-url | cut -d= -f2)
...
```

Enfin, on déploit le robot.

```bash
$> git push heroku master
...
```

Le script `hubot-heroku-keepalive` s'occupera de garder notre robot en vie tout au long de la journée. La seule chose qu'il reste à faire est de configurer le [scheduler](https://elements.heroku.com/addons/scheduler) pour qu'il réveille le robot tous les matins.
Rendez-vous sur la page de configuration de l'addon. On clique sur `Add new job`, et on configure pour que le job `curl ${HUBOT_HEROKU_KEEPALIVE_URL}heroku/keepalive` s'exécute tous les jours à 6h du matin.

Indexation et recherche et affichage des résultats
==================================================

modules utilisés:

- speakingurl
- url-inspector
- objection ou knex si objection n'est pas utile

configurations
==============

- une config knex
- liste de domaines à indexer
- intervalle de temps en secondes minimum entre deux indexations de pages,
- intervalle de temps en secondes maximum entre deux indexations de pages


indexation
==========

1) crawl le site en utilisant url-inspector,
qui te renvoie:
- title
- des trucs plus ou moins utiles à côté (thumbnail)
- le texte du corps de la page, mais sans les parties inutiles sémantiquement (par ex.
le footer de la page, ou le header, ou un tag nav, etc)
- la liste des liens absolus dom('a[href]')

2) pour crawler, on configure le moteur d'indexation pour qu'il se limite
à une liste de domaines

3) une API HTTP pour enregistrer et requêter le modèle /api/indexation
title: string
meta: jsonb
body: text (avec index gin trgm)
url: string
mtime: stamp with tz
links: jsonb

4) example d'indexation avec recherche

`CREATE EXTENSION pg_trgm`
`CREATE INDEX index_gin_content_body ON content USING gin(body gin_trgm_ops)`
ça autorise une recherche par trigramme, "déclenchée" par ces requêtes:
query(
"SELECT * FROM content WHERE body ILIKE ('%' || ? || '%') ORDER BY similarity(body, ?) DESC LIMIT 10"
, txt, txt);


5) la fonction de récursion est temporisée et ne cherche pas à tout indexer
le plus vite possible (configurable)
On parcoure links - si le domaine est dans la liste des domaines autorisés,
on récurse sur le link


recherche
=========

Expose une API publique pour chercher qui donne des résultats sous forme json
exploitables directement pour affichage:

/api/search?text=toto&maxresults=10 (les paramètres d'objection)

Un résultat:

{	
	url: "http://...",
	title: "titre de la page",
	thumbnail: "éventuellement un thumbnail de la page",
	text: "pommes",
	before: "La tarte aux ",
	after: " de ma grand-mère",
	similarity: 0.9885,
	mtime: ...
}


affichage
=========

À la google, éventuellement avec le thumbnail ?

pour choisir sur quoi on veut chercher, on pourra ensuite tirer parti des
informations exportées dans les attributes schema.org:

input: toto

[x] références
[x] images


options
=======

avec url-inspector il est envisageable d'indexer les fichiers aussi,
genre les exif des images, vidéos, audio, etc.


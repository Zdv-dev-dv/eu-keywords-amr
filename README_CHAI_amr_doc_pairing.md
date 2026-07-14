# CHAI — Appariement AMR *fairness* → document (all manifestos)

Notebook : `CHAI_amr_doc_pairing.ipynb`

## But
Pour un tirage aléatoire de phrases du corpus **fairness-MapAIE**, apparier de façon
**vérifiée** chaque graphe AMR de *fairness* :
1. à la phrase dont il est issu (`sample_id`), et
2. au document / type d'acteurice correspondant dans `AI_Ethics_Manifestos.xlsx`.

## Chaîne d'appariement
```
phrase --(sample_id)--> AMR --(::id + ::doc_id)--> doc_id
doc_id = n° de ligne (1-indexé) dans list_urls.txt
       --(URL, clé EXACTE)--> ligne du xlsx --> Institution / Sector / actor_type
```

## Deux garde-fous (motivés par des erreurs réelles constatées)
1. **Anti-décalage AMR ↔ phrase.** Jointure sur `sample_id` (jamais sur la position dans
   le fichier) + assertion `doc_id(AMR) == doc_id(échantillon)`. Un ancien `ego_summary`
   avait un `doc_id` décalé d'un cran ; ce contrôle l'aurait bloqué.
2. **Appariement doc→manifeste par URL, pas par titre.** L'ancien fuzzy-match de titre
   pouvait se tromper (ex. un doc étiqueté « Samsung » alors que le .txt est le CEPEJ).
   L'URL est une clé exacte. Le titre-fuzzy est gardé en diagnostic seulement.

## Arborescence attendue (local)
```
.
├── CHAI_amr_doc_pairing.ipynb
├── AI_Ethics_Manifestos.xlsx
├── fairness_MapAIE.csv            # sinon reconstruit depuis mapaie/txt
└── mapaie/
    ├── txt/<doc_id>.txt
    └── list_urls.txt
```

## Utilisation
- **Frais tirage + parse local :** mettre `RUN_PARSER = True` (nécessite `amrlib` +
  un modèle, p.ex. `parse_xfm_bart_base`). Le notebook tire N_SAMPLE phrases, les parse,
  puis apparie.
- **Réutiliser des AMR déjà parsés :** laisser `RUN_PARSER = False` ; le notebook recharge
  l'échantillon apparié (`fairness_sample_100.csv`) et le fichier penman existant.

> L'échantillon et le fichier AMR forment un **couple**. On ne retire un nouvel échantillon
> que lorsqu'on va le parser ; sinon on recharge celui qui accompagne les penmans.

## Sorties
- `fairness_sample_100.csv` — l'échantillon (sample_id, doc_id, sentence)
- `fairness_amr_doc_paired.csv` — **livrable** : une ligne par phrase, avec
  `institution, title, sector_raw, actor_type, url, amr_penman`

## Dépendances
`pandas`, `numpy`, `openpyxl`, `penman` ; `amrlib` (+ un modèle) seulement si `RUN_PARSER=True`.

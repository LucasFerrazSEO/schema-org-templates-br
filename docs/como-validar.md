# Como validar um modelo antes de publicar

A validação tem quatro camadas, e cada uma pega um tipo de erro que as
outras deixam passar. Rode as quatro, nesta ordem.

## 1. Sintaxe JSON

```bash
python -m json.tool templates/clinica-medica.jsonld
```

Se o arquivo tiver vírgula sobrando, aspas trocadas ou chave sem fechar, o
comando mostra a linha do erro. Sem erro, ele imprime o JSON formatado.

## 2. Integridade do grafo com o jsonld-graph-check

O [jsonld-graph-check](https://github.com/LucasFerrazSEO/jsonld-graph-check)
(do autor) confere `@id` ausente ou repetido, referência que aponta para
nó inexistente, nó isolado e se o `FAQPage` aponta para nós `Question`
que existem no grafo.

A ferramenta lê arquivos `.html` (procura os blocos
`<script type="application/ld+json">`) e `.json`. Um arquivo `.jsonld`
é tratado como HTML e não é lido, então use um destes caminhos:

```bash
# opção 1: copiar o modelo com extensão .json
cp templates/clinica-medica.jsonld /tmp/clinica-medica.json
python jsonld_graph_check.py /tmp/clinica-medica.json

# opção 2: testar a página publicada, salva como HTML
python jsonld_graph_check.py pagina-inicial.html
```

Saída esperada para um modelo íntegro:

```
  ok  /tmp/clinica-medica.json: 7 nó(s), 7 referência(s)

=== jsonld-graph-check: 1 grafo(s) em 1 arquivo(s) ===
ERROS 0 | AVISOS 0
```

## 3. Vocabulário no Schema Markup Validator

O [Schema Markup Validator](https://validator.schema.org/), mantido pelo
schema.org, aceita URL ou trecho de código e confere se os tipos e as
propriedades existem no vocabulário. É aqui que aparece uma propriedade
com nome errado ou usada em um tipo que não a aceita.

## 4. Elegibilidade no Teste de pesquisa aprimorada do Google

O [Teste de pesquisa aprimorada](https://search.google.com/test/rich-results)
aceita URL pública ou código e mostra quais recursos do Google a marcação
pode ativar, com erros e avisos por recurso. Duas observações:

- O Google [afirma](https://developers.google.com/search/docs/appearance/structured-data/sd-policies)
  que não garante que o dado estruturado apareça nos resultados, mesmo
  quando o teste passa.
- O resultado aprimorado de FAQ deixou de aparecer na Pesquisa Google em
  2026, segundo o [registro de mudanças do Google Search Central](https://developers.google.com/search/updates).
  O teste não vai mostrar FAQ como recurso elegível, e isso não é erro do
  modelo.

## Depois de publicar

Teste a URL publicada, não só o código colado. Plugin de SEO, tema ou
outro script pode injetar um segundo bloco JSON-LD na mesma página, com
outro `@id` para o mesmo negócio. Nesse caso, remova a duplicidade para que
a página tenha um grafo só.

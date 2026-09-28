# Erros comuns em JSON-LD de negócio brasileiro

Cada erro abaixo aparece com frequência em sites de prestadores de
serviço. Nenhum deles impede o JSON de ser válido, e é por isso que passam
despercebidos: o arquivo abre sem erro de sintaxe, mas descreve a entidade
de um jeito incompleto ou contraditório.

## 1. `@id` relativo

**Errado:** `"@id": "#organization"`

**Certo:** `"@id": "https://seudominio.com.br/#organization"`

Um `@id` relativo depende da URL da página em que está. Na página inicial
ele vira `https://seudominio.com.br/#organization`, na página de contato
vira `https://seudominio.com.br/contato/#organization`, e o mesmo negócio
passa a ter dois identificadores. Use a URL completa, sempre a mesma em
todas as páginas.

## 2. `addressRegion` por extenso

**Errado:** `"addressRegion": "Minas Gerais"`

**Certo:** `"addressRegion": "MG"`

A sigla da UF é o formato do endereçamento postal brasileiro e o que
aparece nos exemplos da documentação do Google. Por extenso, o nome do
estado vai para `areaServed`, no nó `State`, onde descreve a área
atendida e não o endereço.

## 3. Telefone sem `+55`

**Errado:** `"telephone": "(31) 0000-0000"`

**Certo:** `"telephone": "+553100000000"`

A documentação de empresa local do Google pede o código do país e o
código de área no telefone. Sem o `+55`, o número fica ambíguo para quem
lê o dado fora do Brasil. O formato E.164 junta `+55`, o DDD e o número, sem espaço nem
parêntese. O número do dado estruturado precisa ser o mesmo do site e do
perfil do Google.

## 4. `sameAs` para perfil inexistente

**Errado:** deixar no `sameAs` o marcador do modelo, um perfil desativado
ou o perfil de outra empresa com nome parecido.

**Certo:** listar só perfis que existem, estão ativos e pertencem ao
negócio.

O `sameAs` diz "esta entidade é a mesma deste perfil". Um link quebrado ou
de outra empresa contradiz a descrição, e a contradição enfraquece a
identificação. Se o negócio não tem LinkedIn, apague a linha do LinkedIn.

## 5. `FAQPage` sem conteúdo visível correspondente

**Errado:** um `FAQPage` com perguntas que não aparecem no HTML da página,
ou com respostas diferentes das que o visitante lê.

**Certo:** as mesmas perguntas e respostas, palavra por palavra, visíveis
na página (pode ser em bloco expansível).

As [diretrizes gerais de dados estruturados do Google](https://developers.google.com/search/docs/appearance/structured-data/sd-policies)
(atualizadas em julho de 2026) pedem que a marcação não descreva conteúdo
invisível para quem lê a página. Vale a mesma lógica para qualquer sistema
que confronta o JSON-LD com o texto. Sem FAQ visível, apague o
`FAQPage`, os nós `Question` e o `hasPart` da página.

## 6. Nós soltos no `@graph`

**Errado:** `WebSite`, `WebPage`, `Organization` e `Person` no mesmo
`@graph` sem nenhuma referência entre eles.

**Certo:** cada nó apontando para outro por `{"@id": ...}`: a página em
`isPartOf` do site, o site em `publisher` do negócio, o fundador em
`worksFor` do negócio.

Nós soltos aparecem nos validadores como entidades separadas, sem relação
entre si. O [jsonld-graph-check](https://github.com/LucasFerrazSEO/jsonld-graph-check)
(do autor) aponta nó isolado e referência que leva a nó inexistente.

## 7. Tipo obsoleto ou genérico demais

**Errado:** `Attorney` para escritório de advocacia, `ProfessionalService`
para agência, `LocalBusiness` para clínica médica.

**Certo:** `LegalService`, `LocalBusiness` com `knowsAbout`,
`MedicalClinic`.

`Attorney` e `ProfessionalService` ainda existem no schema.org, mas as
páginas dos dois informam que estão obsoletos. No outro sentido,
`LocalBusiness` para uma clínica joga fora a especificidade que existe.
A tabela do [README](../README.pt-BR.md#modelos) traz o tipo de cada
segmento.

## 8. Dado que não está na página

**Errado:** avaliação, preço, horário ou registro profissional no JSON-LD
que não aparecem no site.

**Certo:** marcar só o que o visitante consegue conferir.

O dado estruturado descreve a página. O que ele afirma e a página não
mostra vira uma afirmação sem prova.

## 9. `dateModified` que muda sem o conteúdo mudar

**Errado:** portal ou blog que atualiza `dateModified` em todas as
páginas a cada deploy, ou todo dia, para parecer recente.

**Certo:** `dateModified` muda só quando o texto muda de verdade, e a
data aparece também na página.

Data de atualização falsa é um sinal que não se sustenta quando alguém
compara as versões, e não muda o que o conteúdo oferece.

## 10. Autor como texto ou com cargo no nome

**Errado:** `"author": "Redação"` ou `"name": "Maria Exemplo, repórter"`.

**Certo:** `author` apontando para um nó `Person` com `name` só com o
nome, `url` da página de autor e `sameAs` para perfis que existem. Texto
sem autor identificado usa a `Organization` como autor.

A [documentação de Article do Google](https://developers.google.com/search/docs/appearance/structured-data/article)
pede o nome do autor sem cargo nem título, e uma entrada `author` por
autor.

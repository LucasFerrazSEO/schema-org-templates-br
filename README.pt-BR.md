[English](README.md) · **Português (Brasil)**

# schema-org-templates-br

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE) ![JSON-LD](https://img.shields.io/badge/format-JSON--LD-blue.svg)

`schema-org-templates-br` é um conjunto gratuito e de código aberto de
modelos JSON-LD para negócios brasileiros de serviço: clínica médica,
clínica odontológica, clínica de estética, advocacia, contabilidade,
imobiliária, construtora, agência de marketing, arquiteto e profissional
liberal. Cada modelo é um `@graph` completo e coerente, com `@id`
totalmente qualificado, endereço brasileiro, telefone no formato E.164,
horário de funcionamento, área atendida, fundador e um `FAQPage` mínimo.
Todos os dados são fictícios e foram escritos do zero.

Os modelos, o guia de campos e o guia de erros estão em português do
Brasil, porque o público são empresas brasileiras e quem faz o site delas.
Este README existe em inglês e em português.

## Sumário

- [Contexto](#contexto)
- [O que o repositório contém](#o-que-o-repositório-contém)
- [Modelos](#modelos)
- [Escolha dos tipos](#escolha-dos-tipos)
- [Publishers: portais de notícia e blogs de conteúdo](#publishers-portais-de-notícia-e-blogs-de-conteúdo)
- [Como usar](#como-usar)
- [Como validar](#como-validar)
- [Perguntas frequentes](#perguntas-frequentes)
- [Limitações](#limitações)
- [Método e origem](#método-e-origem)
- [Projetos relacionados do autor](#projetos-relacionados-do-autor)
- [Como contribuir](#como-contribuir)
- [Autor](#autor)
- [Licença](#licença)

## Contexto

A maior parte dos exemplos de JSON-LD publicados na internet foi escrita
para uma empresa genérica dos Estados Unidos: estado por extenso, telefone
sem código de país, nós soltos sem ligação entre si. A clínica ou o
escritório brasileiro que copia um desses exemplos acaba com
`addressRegion` escrito "Minas Gerais" em vez de "MG", telefone sem `+55`,
`sameAs` apontando para perfil que não existe e um `FAQPage` que repete
perguntas que ninguém vê na página. Estes modelos partem do caso
brasileiro e mantêm cada nó ligado aos outros por `@id`, para que o dado
estruturado descreva uma entidade só, coerente.

## O que o repositório contém

```
templates/   12 modelos JSON-LD, um por segmento (.jsonld)
docs/        guias em português do Brasil
  campos-por-segmento.md   campos obrigatórios e recomendados por segmento
  erros-comuns.md          erros comuns e como corrigir
  como-validar.md          como validar antes de publicar
```

Todo modelo de negócio tem a mesma espinha:

1. A entidade principal da página primeiro no `@graph` (o negócio, ou a
   pessoa na página de perfil).
2. `WebPage` (ou `ProfilePage`) com `isPartOf` apontando para o `WebSite`
   e `hasPart` apontando para o `FAQPage`.
3. `WebSite` com `publisher` apontando para o negócio.
4. `Person` do fundador, com `@id` próprio e `worksFor` apontando de volta
   para o negócio.
5. `FAQPage` com `isPartOf` apontando para a página e `mainEntity`
   apontando para nós `Question` que existem no mesmo grafo.
6. `OfferCatalog` com os serviços, cada um com `provider` apontando para
   o negócio.

## Modelos

| Segmento | Tipo schema.org | Arquivo |
|---|---|---|
| Clínica médica | `MedicalClinic` | [templates/clinica-medica.jsonld](templates/clinica-medica.jsonld) |
| Clínica odontológica | `Dentist` | [templates/clinica-odontologica.jsonld](templates/clinica-odontologica.jsonld) |
| Clínica de estética | `HealthAndBeautyBusiness` | [templates/clinica-estetica.jsonld](templates/clinica-estetica.jsonld) |
| Advocacia | `LegalService` | [templates/advocacia.jsonld](templates/advocacia.jsonld) |
| Contabilidade | `AccountingService` | [templates/contabilidade.jsonld](templates/contabilidade.jsonld) |
| Imobiliária | `RealEstateAgent` | [templates/imobiliaria.jsonld](templates/imobiliaria.jsonld) |
| Construtora | `GeneralContractor` | [templates/construtora.jsonld](templates/construtora.jsonld) |
| Agência ou consultoria de marketing | `LocalBusiness` + `knowsAbout` | [templates/agencia-marketing.jsonld](templates/agencia-marketing.jsonld) |
| Arquiteto | `LocalBusiness` + `knowsAbout` | [templates/arquiteto.jsonld](templates/arquiteto.jsonld) |
| Profissional liberal (autor, especialista) | `Person` + `ProfilePage` | [templates/profissional-liberal.jsonld](templates/profissional-liberal.jsonld) |
| Portal de notícias (publisher com redação) | `NewsMediaOrganization` + `NewsArticle` | [templates/portal-de-noticias.jsonld](templates/portal-de-noticias.jsonld) |
| Blog de conteúdo (publisher sem redação) | `Organization` + `BlogPosting` | [templates/blog-de-conteudo.jsonld](templates/blog-de-conteudo.jsonld) |

## Escolha dos tipos

Cada tipo foi conferido no schema.org (versão de desenvolvimento, setembro
de 2026). A [documentação de empresa local do Google](https://developers.google.com/search/docs/appearance/structured-data/local-business)
pede o subtipo mais específico de `LocalBusiness` disponível, então a
regra aqui é: o tipo mais específico que existe e não está obsoleto.

- **Advocacia: `LegalService`, não `Attorney`.** [`Attorney`](https://schema.org/Attorney)
  ainda existe no schema.org, mas a página dele informa que o tipo está
  obsoleto porque `LegalService` é mais abrangente e menos ambíguo.
- **Agência de marketing e arquiteto: `LocalBusiness`, não
  `ProfessionalService`.** [`ProfessionalService`](https://schema.org/ProfessionalService)
  também ainda existe, mas o schema.org o marca como obsoleto por
  confusão com `Service`. Não há tipo específico para agência de marketing
  nem para escritório de arquitetura, então os modelos usam o pai
  `LocalBusiness` e descrevem a especialidade com `knowsAbout` (aceito em
  `Organization` e em `Person`) e com os serviços em `hasOfferCatalog`.
- **Clínica de estética: `HealthAndBeautyBusiness`, não `BeautySalon`.**
  `BeautySalon` é subtipo de `HealthAndBeautyBusiness` e serve para salão
  de beleza. A clínica de estética brasileira costuma oferecer
  procedimentos faciais e corporais que vão além do salão, então o tipo
  pai a descreve com menos risco de confundir. Se os procedimentos forem
  feitos por médico, `MedicalClinic` pode ser a escolha mais precisa.
- **Clínica médica e odontológica** usam `medicalSpecialty` com valores da
  enumeração `MedicalSpecialty` do schema.org (`PrimaryCare` e
  `Dentistry` nos modelos).

## Publishers: portais de notícia e blogs de conteúdo

Dois modelos atendem sites que publicam conteúdo e ganham com ele, por
anúncio de display (AdSense, Ad Manager ou AdX), tráfego de busca,
tráfego de busca com IA ou arbitragem de tráfego pago. Eles descrevem uma
página de artigo, não a página de um negócio, então o grafo muda:

1. O artigo (`NewsArticle` ou `BlogPosting`) primeiro, com `author`
   apontando para uma `Person` e `publisher` apontando para a organização.
2. `WebPage` com `mainEntity` apontando para o artigo e `breadcrumb`
   apontando para um `BreadcrumbList`.
3. `Person` do autor, com a página de autor em `url`, `knowsAbout` e
   `sameAs`.
4. O publisher: `NewsMediaOrganization` para portal com redação,
   `Organization` para blog de conteúdo. Os dois com `logo` e
   `publishingPrinciples`.
5. `WebSite` com `publisher` apontando para a organização.

**Qual tipo escolher.** Use [`NewsMediaOrganization`](https://schema.org/NewsMediaOrganization)
só quando o site tem redação e publica as políticas editoriais. O tipo
tem propriedades próprias para isso: `ethicsPolicy`, `correctionsPolicy`,
`verificationFactCheckingPolicy`, `masthead`, `ownershipFundingInfo`,
`actionableFeedbackPolicy` e `unnamedSourcesPolicy`, entre outras. Cada
uma aponta para uma página que existe no site. Blog sem redação usa
`Organization` com [`publishingPrinciples`](https://schema.org/publishingPrinciples)
apontando para a política editorial.

**O que o Google recomenda para artigos.** A [documentação de Article do Google](https://developers.google.com/search/docs/appearance/structured-data/article)
(atualizada em setembro de 2026) não tem propriedade obrigatória. As
recomendadas são `headline`, `image` (os modelos usam três proporções,
1x1, 4x3 e 16x9), `datePublished`, `dateModified` em ISO 8601 com fuso
horário, e `author` como `Person` ou `Organization` com `name` e `url`.
Cada autor entra em uma entrada `author` própria.

**O que o schema não faz pela monetização.** Nenhuma propriedade do
schema.org declara AdSense, Ad Manager ou AdX, e dado estruturado não
interfere na aprovação nem na receita de anúncio. Em conteúdo patrocinado,
o artigo pode levar [`sponsor`](https://schema.org/sponsor) apontando para
a organização patrocinadora, e os links pagos no texto usam
`rel="sponsored"`, como pede a [documentação de atributos de link do Google](https://developers.google.com/search/docs/crawling-indexing/qualify-outbound-links).
Para quem compra tráfego (arbitragem), a marcação é a mesma: o que importa
é que `dateModified` mude só quando o conteúdo muda de verdade e que as
páginas não sejam produzidas em massa só para ranquear. As
[políticas de spam do Google](https://developers.google.com/search/docs/essentials/spam-policies)
chamam isso de abuso de conteúdo em escala.

**Para a busca com IA.** Autor com página de autor real, `sameAs` para
perfis que existem e política editorial publicada facilitam que um
sistema de busca identifique quem escreveu o texto e quem publica. Isso
ajuda a desambiguar. Não garante citação.

## Como usar

1. Copie o modelo do seu segmento.
2. Troque `https://example.com.br/` pelo seu domínio em todos os lugares,
   mantendo os fragmentos (`#organization`, `#website`, `#webpage`,
   `#fundador`, `#faq`).
3. Troque cada valor fictício pelo dado real: nome, endereço, CEP,
   coordenadas, telefone, horário, serviços e fundador.
4. Em `sameAs`, deixe só os perfis que existem e pertencem ao negócio.
   Apague todo marcador que você não substituir. Um `sameAs` apontando para
   perfil inexistente enfraquece a identificação da entidade.
5. Reescreva o `FAQPage` com as perguntas e respostas que estão visíveis
   na página, palavra por palavra. Se a página não tem FAQ visível, apague
   o `FAQPage`, os nós `Question` e o `hasPart` da página.
6. Publique dentro de `<script type="application/ld+json">` no HTML da
   página.

## Como validar

Detalhes em [docs/como-validar.md](docs/como-validar.md). Em resumo:

1. Sintaxe JSON: `python -m json.tool modelo.jsonld`.
2. Integridade do grafo com o [jsonld-graph-check](https://github.com/LucasFerrazSEO/jsonld-graph-check)
   (do autor). A ferramenta lê `.html` e `.json`, então renomeie uma cópia
   do modelo para `.json` ou teste a página HTML publicada.
3. Vocabulário no [Schema Markup Validator](https://validator.schema.org/).
4. Elegibilidade no Google pelo [Teste de pesquisa aprimorada](https://search.google.com/test/rich-results).

Os doze modelos passam no `json.tool` e no `jsonld-graph-check` com zero
erro e zero aviso.

## Perguntas frequentes

**Os modelos podem ser usados comercialmente?**
Sim. Estão sob licença MIT, então você pode usá-los em sites de clientes,
sem cadastro e sem limite de uso.

**Estes modelos garantem pesquisa aprimorada ou citação por IA?**
Não, e nenhuma marcação garante isso. O Google afirma que não garante que
o dado estruturado apareça nos resultados, mesmo quando está correto. Os
modelos descrevem o negócio com precisão. O que cada buscador ou sistema
de IA faz com essa descrição está fora do controle de qualquer pessoa.

**Por que manter um `FAQPage` se o Google não mostra mais o resultado de FAQ?**
O [registro de mudanças do Google Search Central](https://developers.google.com/search/updates)
registra a descontinuação do resultado aprimorado de FAQ em maio de 2026
e, em junho de 2026, a retirada da documentação porque o recurso não
aparece mais nos resultados. O `FAQPage` fica nos modelos como descrição
de um conteúdo visível na página, ligado ao resto do grafo. Se a página
não tem FAQ visível, apague.

**Posso incluir CNPJ ou número de registro profissional?**
Os modelos não trazem CNPJ, CPF nem número de registro, porque os dados
são fictícios. Os nós `hasCredential` nomeiam o conselho profissional
(CRM, CRO, OAB, CRC, CRECI, CREA, CAU) sem número. Inclua dado real só se
ele estiver na página.

## Limitações

Os modelos cobrem a página inicial de um negócio com um endereço e a
página de perfil de um profissional. Negócio com várias unidades precisa
de um nó `LocalBusiness` por unidade, cada um com `@id` e endereço
próprios. Os modelos não trazem `aggregateRating` nem `review`, porque
avaliação precisa vir de avaliações reais exibidas na página. A escolha de
tipos reflete o schema.org conferido em setembro de 2026, e o vocabulário
muda com o tempo.

## Método e origem

A estrutura do grafo segue o padrão interno usado nos dados estruturados
do [lucasferrazseo.com](https://lucasferrazseo.com): `@id` totalmente
qualificado, entidade principal primeiro, todo nó ligado por referência e
`FAQPage` só quando há conteúdo visível correspondente. Tipos e
propriedades foram conferidos um a um no schema.org, e cada modelo foi
validado com `json.tool` e `jsonld-graph-check` antes da publicação.

## Projetos relacionados do autor

- [structured-data-json-ld](https://github.com/LucasFerrazSEO/structured-data-json-ld):
  o repositório anterior, com um exemplo genérico de `LocalBusiness`.
- [jsonld-graph-check](https://github.com/LucasFerrazSEO/jsonld-graph-check):
  validador de linha de comando da integridade de um `@graph` JSON-LD.
- [Gerador de schema on-line](https://lucasferrazseo.com/ferramentas/gerador-de-schema-para-seo-e-geo/):
  monta o JSON-LD a partir de um formulário, no navegador.

## Como contribuir

Relatos de erro, correções e pedidos de novos segmentos são bem-vindos pelas [Issues do GitHub](https://github.com/LucasFerrazSEO/schema-org-templates-br/issues).

## Autor

[Lucas Ferraz](https://lucasferraz.com) é especialista em SEO, criação de sites e Generative Engine Optimization e fundador da [Lucas Ferraz SEO](https://lucasferrazseo.com).

## Licença

MIT. Veja o arquivo [LICENSE](LICENSE).

**English** · [Português (Brasil)](README.pt-BR.md)

# schema-org-templates-br

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE) ![JSON-LD](https://img.shields.io/badge/format-JSON--LD-blue.svg)

`schema-org-templates-br` is a free, open source set of JSON-LD templates
for Brazilian service businesses: medical and dental clinics, aesthetics
clinics, law firms, accounting firms, real estate agencies, general
contractors, marketing agencies, architects and independent professionals.
Each template is a complete, internally consistent `@graph` with fully
qualified `@id` values, a Brazilian postal address, an E.164 phone number,
opening hours, service area, founder and a minimal `FAQPage`. All data is
fictitious and written from scratch.

The templates, the field guide and the error guide are written in Brazilian
Portuguese, because the audience is Brazilian businesses and the people
who build their sites. This README exists in English and Portuguese.

## Contents

- [Background](#background)
- [What is included](#what-is-included)
- [Templates](#templates)
- [Type choices](#type-choices)
- [Publishers: news portals and content blogs](#publishers-news-portals-and-content-blogs)
- [How to use](#how-to-use)
- [How to validate](#how-to-validate)
- [FAQ](#faq)
- [Limitations](#limitations)
- [Methodology](#methodology)
- [Related projects by the author](#related-projects-by-the-author)
- [Contributing](#contributing)
- [Author](#author)
- [License](#license)

## Background

Most JSON-LD examples published online are written for a generic US
business: state names spelled out, phone numbers without a country code,
loose nodes with no link between them. A Brazilian clinic or law firm that
copies one of those examples ends up with `addressRegion` written as
"Minas Gerais" instead of "MG", a phone number without `+55`, `sameAs`
pointing to profiles that do not exist and a `FAQPage` that repeats
questions nobody can see on the page. These templates start from the
Brazilian case and keep every node tied to the others by `@id`, so the
structured data describes one coherent entity.

## What is included

```
templates/   12 JSON-LD templates, one per segment (.jsonld)
docs/        guides in Brazilian Portuguese
  campos-por-segmento.md   required and recommended fields per segment
  erros-comuns.md          common mistakes and how to fix them
  como-validar.md          how to validate before publishing
```

Every business template has the same backbone:

1. The main entity of the page first in the `@graph` (the business, or the
   person on the profile page).
2. `WebPage` (or `ProfilePage`) with `isPartOf` pointing to the `WebSite`
   and `hasPart` pointing to the `FAQPage`.
3. `WebSite` with `publisher` pointing to the business.
4. `Person` for the founder, with its own `@id` and `worksFor` pointing
   back to the business.
5. `FAQPage` with `isPartOf` pointing to the page and `mainEntity`
   pointing to `Question` nodes that exist in the same graph.
6. `OfferCatalog` with the services, each one with `provider` pointing to
   the business.

## Templates

| Segment | schema.org type | File |
|---|---|---|
| Medical clinic | `MedicalClinic` | [templates/clinica-medica.jsonld](templates/clinica-medica.jsonld) |
| Dental clinic | `Dentist` | [templates/clinica-odontologica.jsonld](templates/clinica-odontologica.jsonld) |
| Aesthetics clinic | `HealthAndBeautyBusiness` | [templates/clinica-estetica.jsonld](templates/clinica-estetica.jsonld) |
| Law firm | `LegalService` | [templates/advocacia.jsonld](templates/advocacia.jsonld) |
| Accounting firm | `AccountingService` | [templates/contabilidade.jsonld](templates/contabilidade.jsonld) |
| Real estate agency | `RealEstateAgent` | [templates/imobiliaria.jsonld](templates/imobiliaria.jsonld) |
| General contractor | `GeneralContractor` | [templates/construtora.jsonld](templates/construtora.jsonld) |
| Marketing agency or consultancy | `LocalBusiness` + `knowsAbout` | [templates/agencia-marketing.jsonld](templates/agencia-marketing.jsonld) |
| Architect | `LocalBusiness` + `knowsAbout` | [templates/arquiteto.jsonld](templates/arquiteto.jsonld) |
| Independent professional (author, specialist) | `Person` + `ProfilePage` | [templates/profissional-liberal.jsonld](templates/profissional-liberal.jsonld) |
| News portal (publisher with a newsroom) | `NewsMediaOrganization` + `NewsArticle` | [templates/portal-de-noticias.jsonld](templates/portal-de-noticias.jsonld) |
| Content blog (publisher without a newsroom) | `Organization` + `BlogPosting` | [templates/blog-de-conteudo.jsonld](templates/blog-de-conteudo.jsonld) |

## Type choices

Every type was checked on schema.org (development version, September
2026). Google's [local business documentation](https://developers.google.com/search/docs/appearance/structured-data/local-business)
asks for the most specific `LocalBusiness` subtype available, so the rule here is: the most specific
type that exists and is not deprecated.

- **Law firm: `LegalService`, not `Attorney`.** [`Attorney`](https://schema.org/Attorney)
  still exists on schema.org, but its page says it is deprecated because `LegalService` is
  more inclusive and less ambiguous.
- **Marketing agency and architect: `LocalBusiness`, not
  `ProfessionalService`.** [`ProfessionalService`](https://schema.org/ProfessionalService)
  also still exists, but
  schema.org marks it as deprecated due to confusion with `Service`. There
  is no specific type for a marketing agency or an architecture firm, so
  the templates use the parent `LocalBusiness` and describe the specialty
  with `knowsAbout` (accepted on `Organization` and `Person`) and with the
  services in `hasOfferCatalog`.
- **Aesthetics clinic: `HealthAndBeautyBusiness`, not `BeautySalon`.**
  `BeautySalon` is a subtype of `HealthAndBeautyBusiness` and fits a beauty
  salon. A Brazilian "clínica de estética" usually offers facial and body
  procedures that go beyond a salon, so the parent type describes it with
  less risk of misleading. If the procedures are performed by a physician,
  `MedicalClinic` may be the more accurate choice.
- **Medical and dental clinics** use `medicalSpecialty` with values from
  the schema.org `MedicalSpecialty` enumeration (`PrimaryCare` and
  `Dentistry` in the templates).

## Publishers: news portals and content blogs

Two templates cover sites that publish content and earn from it, through
display ads (AdSense, Ad Manager or AdX), search traffic, AI search
traffic or paid traffic arbitrage. They describe an article page, not a
business page, so the graph is different:

1. The article (`NewsArticle` or `BlogPosting`) first, with `author`
   pointing to a `Person` and `publisher` pointing to the organization.
2. `WebPage` with `mainEntity` pointing to the article and `breadcrumb`
   pointing to a `BreadcrumbList`.
3. `Person` for the author, with an author page in `url`, `knowsAbout`
   and `sameAs`.
4. The publisher: `NewsMediaOrganization` for a portal with a newsroom,
   `Organization` for a content blog. Both with `logo` and
   `publishingPrinciples`.
5. `WebSite` with `publisher` pointing to the organization.

**Which type to choose.** Use [`NewsMediaOrganization`](https://schema.org/NewsMediaOrganization)
only when the site has a newsroom and publishes its editorial policies.
The type has its own properties for that: `ethicsPolicy`,
`correctionsPolicy`, `verificationFactCheckingPolicy`, `masthead`,
`ownershipFundingInfo`, `actionableFeedbackPolicy` and
`unnamedSourcesPolicy`, among others. Each one points to a page that
exists on the site. A blog without a newsroom uses `Organization` with
[`publishingPrinciples`](https://schema.org/publishingPrinciples) pointing
to its editorial policy.

**What Google recommends for articles.** Google's [Article documentation](https://developers.google.com/search/docs/appearance/structured-data/article)
(updated September 2026) lists no required properties. The recommended
ones are `headline`, `image` (the templates use three aspect ratios,
1x1, 4x3 and 16x9), `datePublished`, `dateModified` in ISO 8601 with
time zone, and `author` as a `Person` or `Organization` with `name` and
`url`. Each author goes in its own `author` entry.

**What schema does not do for monetization.** No schema.org property
declares AdSense, Ad Manager or AdX, and structured data does not affect
ad approval or ad revenue. For sponsored content, the article can carry
[`sponsor`](https://schema.org/sponsor) pointing to the sponsor
organization, and paid links in the text use `rel="sponsored"`, as
Google's [link attributes documentation](https://developers.google.com/search/docs/crawling-indexing/qualify-outbound-links)
asks. For sites that buy traffic (arbitrage), the markup is the same:
what matters is that `dateModified` changes only when the content really
changes, and that pages are not produced in bulk only to rank. Google's
[spam policies](https://developers.google.com/search/docs/essentials/spam-policies)
call that scaled content abuse.

**For AI search.** An author with a real author page, `sameAs` to
existing profiles and a published editorial policy make it easier for a
search system to identify who wrote the text and who publishes it. That
helps disambiguation. It does not guarantee a citation.

## How to use

1. Copy the template of your segment.
2. Replace `https://example.com.br/` with your domain everywhere, keeping
   the fragments (`#organization`, `#website`, `#webpage`, `#fundador`,
   `#faq`).
3. Replace every fictitious value with real data: name, address, postal
   code, coordinates, phone number, hours, services and founder.
4. In `sameAs`, keep only profiles that exist and belong to the business.
   Remove every placeholder you do not replace. A `sameAs` pointing to a
   profile that does not exist weakens the identification of the entity.
5. Rewrite the `FAQPage` with the questions and answers that are visible
   on the page, word for word. If the page has no visible FAQ, remove the
   `FAQPage`, the `Question` nodes and the `hasPart` of the page.
6. Publish inside `<script type="application/ld+json">` in the page HTML.

## How to validate

Details in [docs/como-validar.md](docs/como-validar.md). In short:

1. JSON syntax: `python -m json.tool template.jsonld`.
2. Graph integrity with [jsonld-graph-check](https://github.com/LucasFerrazSEO/jsonld-graph-check)
   (by the author). The tool reads `.html` and `.json`, so rename a copy
   of the template to `.json` or test the published HTML page.
3. Vocabulary with the [Schema Markup Validator](https://validator.schema.org/).
4. Google eligibility with the [Rich Results Test](https://search.google.com/test/rich-results).

All twelve templates pass `json.tool` and `jsonld-graph-check` with zero
errors and zero warnings.

## FAQ

**Are the templates free for commercial use?**
Yes. They are released under the MIT license, so you can use them on
client sites, with no sign-up and no usage limit.

**Will these templates give my site rich results or AI citations?**
No, and no markup can guarantee that. Google states that it does not
guarantee structured data will show up in search results, even when it is
correct. The templates describe the business accurately. What each search
engine or AI system does with that description is outside anyone's control.

**Why keep a `FAQPage` if Google no longer shows FAQ rich results?**
The [Google Search Central changelog](https://developers.google.com/search/updates)
records the deprecation of the FAQ rich result in May 2026 and, in June
2026, the removal of its documentation because the feature is no longer
shown in Search results. The `FAQPage` stays in the templates as a
description of content that is visible on the page, linked to the rest of
the graph. If the page has no visible FAQ, remove it.

**Can I add CNPJ or professional registration numbers?**
The templates do not include CNPJ, CPF or registration numbers, because
the data is fictitious. The `hasCredential` nodes name the professional
council (CRM, CRO, OAB, CRC, CRECI, CREA, CAU) without a number. Add real
data only if it is shown on the page.

## Limitations

The templates cover the home page of a single-location business and a
profile page of one professional. Businesses with several branches need
one `LocalBusiness` node per branch, each with its own `@id` and address.
Templates do not include `aggregateRating` or `review`, because ratings
must come from real reviews shown on the page. The type choices reflect
schema.org as checked in September 2026, and vocabulary changes over time.

## Methodology

The graph structure follows the internal pattern used in the structured
data of [lucasferrazseo.com](https://lucasferrazseo.com): fully qualified
`@id`, main entity first, every node linked by reference and a `FAQPage`
only when there is visible content to match. Types and properties were
checked one by one on schema.org, and every template was validated with
`json.tool` and `jsonld-graph-check` before publication.

## Related projects by the author

- [structured-data-json-ld](https://github.com/LucasFerrazSEO/structured-data-json-ld):
  the earlier repository, with a generic `LocalBusiness` example.
- [jsonld-graph-check](https://github.com/LucasFerrazSEO/jsonld-graph-check):
  command-line validator for the integrity of a JSON-LD `@graph`.
- [Online schema generator](https://lucasferrazseo.com/ferramentas/gerador-de-schema-para-seo-e-geo/)
  (in Portuguese): builds JSON-LD from a form, in the browser.

## Contributing

Bug reports, corrections and requests for new segments are welcome through [GitHub Issues](https://github.com/LucasFerrazSEO/schema-org-templates-br/issues).

## Author

[Lucas Ferraz](https://lucasferraz.com) is an SEO, website development and Generative Engine Optimization specialist and the founder of [Lucas Ferraz SEO](https://lucasferrazseo.com).

## License

MIT. See [LICENSE](LICENSE).

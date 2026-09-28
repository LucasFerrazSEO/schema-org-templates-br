# Campos obrigatórios e recomendados por segmento

Este guia separa o que cada modelo precisa ter para descrever o negócio
sem lacuna do que vale acrescentar quando o dado existe e aparece na
página. "Obrigatório" aqui segue duas fontes: o que a
[documentação de empresa local do Google](https://developers.google.com/search/docs/appearance/structured-data/local-business)
(atualizada em setembro de 2026) exige (`name` e `address`) e o que o grafo
precisa para ficar coerente (`@id`, `url` e as ligações entre os nós).
"Recomendado" reúne o que o Google recomenda e o que ajuda a identificar a
entidade sem ambiguidade.

## Campos comuns a todos os negócios

| Campo | Situação | Observação |
|---|---|---|
| `@id` | obrigatório | URL completa com fragmento, por exemplo `https://seudominio.com.br/#organization`. |
| `@type` | obrigatório | O subtipo mais específico que existe e não está obsoleto (tabela abaixo). |
| `name` | obrigatório | O nome que aparece na fachada, no site e no perfil do Google, igual nos três. |
| `address` (`PostalAddress`) | obrigatório | `streetAddress`, `addressLocality`, `addressRegion` com a sigla da UF, `postalCode` no formato `00000-000` e `addressCountry` `"BR"`. |
| `url` | obrigatório | A página inicial do site. |
| `telephone` | recomendado | Formato E.164, com `+55` e DDD, por exemplo `+553100000000`. |
| `geo` (`GeoCoordinates`) | recomendado | Latitude e longitude do endereço, com pelo menos 5 casas decimais, como pede a documentação do Google. |
| `openingHoursSpecification` | recomendado | Um bloco por faixa de dias com o mesmo horário, `opens` e `closes` em `HH:MM`. |
| `areaServed` | recomendado | `City` e `State` por nome. Use só a área que o negócio atende de verdade. |
| `sameAs` | recomendado | Só perfis que existem e pertencem ao negócio (perfil do Google, Instagram, LinkedIn). |
| `founder` | recomendado | Referência `{"@id": ...}` para o nó `Person`, que tem `@id` próprio. |
| `hasOfferCatalog` | recomendado | `OfferCatalog` com os serviços, cada um com `provider` apontando para o negócio. |
| `logo` e `image` | recomendado | URLs absolutas de imagens que existem no site. |
| `description` | recomendado | Uma frase factual sobre o que o negócio faz e onde. |

## Campos por segmento

| Segmento | Tipo | Campos específicos recomendados |
|---|---|---|
| Clínica médica | `MedicalClinic` | `medicalSpecialty` com valor da enumeração `MedicalSpecialty` (por exemplo `https://schema.org/PrimaryCare`); `hasCredential` do médico responsável nomeando o CRM. |
| Clínica odontológica | `Dentist` | `medicalSpecialty` `https://schema.org/Dentistry`; `hasCredential` do responsável técnico nomeando o CRO. |
| Clínica de estética | `HealthAndBeautyBusiness` | Serviços bem descritos no `hasOfferCatalog`. Se os procedimentos forem médicos, avaliar `MedicalClinic`. |
| Advocacia | `LegalService` | `knowsAbout` com as áreas do direito; `hasCredential` do advogado nomeando a OAB. `Attorney` está obsoleto no schema.org. |
| Contabilidade | `AccountingService` | Serviços no `hasOfferCatalog`; `hasCredential` do contador nomeando o CRC. |
| Imobiliária | `RealEstateAgent` | Serviços de venda e locação no `hasOfferCatalog`; `hasCredential` do corretor nomeando o CRECI. |
| Construtora | `GeneralContractor` | Tipos de obra no `hasOfferCatalog`; `hasCredential` do engenheiro nomeando o CREA. |
| Agência ou consultoria de marketing | `LocalBusiness` | `knowsAbout` no negócio e no fundador; serviços no `hasOfferCatalog`. `ProfessionalService` está obsoleto no schema.org. |
| Arquiteto | `LocalBusiness` | `knowsAbout` com as especialidades; `hasCredential` nomeando o CAU. |
| Profissional liberal | `Person` + `ProfilePage` | `jobTitle`, `knowsAbout`, `sameAs`, `mainEntityOfPage`; na `ProfilePage`, `mainEntity`, `dateCreated` e `dateModified`. |

## Nós de página

| Nó | Campos | Ligações |
|---|---|---|
| `WebSite` | `@id`, `url`, `name`, `inLanguage` `"pt-BR"` | `publisher` aponta para o negócio (ou para a pessoa, no perfil). |
| `WebPage` | `@id`, `url`, `name`, `inLanguage` | `isPartOf` aponta para o `WebSite`; `about` aponta para o negócio; `hasPart` aponta para o `FAQPage`. |
| `ProfilePage` | `@id`, `url`, `name`, `dateCreated`, `dateModified` | `mainEntity` aponta para a `Person`; `isPartOf` aponta para o `WebSite`. |
| `FAQPage` | `@id` | `isPartOf` aponta para a página; `mainEntity` lista os nós `Question`. |
| `Question` | `@id`, `name`, `acceptedAnswer` (`Answer` com `text`) | Pergunta e resposta iguais às visíveis na página. |

## O que os modelos deixam de fora de propósito

`aggregateRating` e `review` só entram com avaliações reais exibidas na
página. `priceRange` só entra se o preço estiver publicado. CNPJ, CPF e
número de registro profissional não aparecem nos modelos porque os dados
são fictícios; inclua o dado real só se ele estiver visível na página.

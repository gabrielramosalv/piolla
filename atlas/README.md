# Piolla Atlas

## Visão geral

O **Piolla Atlas** é o serviço de catálogo e referência de medicamentos do ecossistema Piolla. Seu objetivo é consolidar informações provenientes de diferentes fontes externas e disponibilizá-las por meio de uma representação única, estruturada e rastreável.

O nome **Atlas** parte da ideia de um atlas tradicional: uma coleção organizada de informações que permite localizar, relacionar e compreender elementos de um determinado universo. No Piolla, esse universo é formado por medicamentos, princípios ativos, produtos medicinais, apresentações, classificações, regulações e suas respectivas origens.

O Atlas não é apenas um espelho de uma base externa. Ele funciona como uma camada de **consolidação e referência**, capaz de combinar informações complementares de múltiplas fontes sem transformar nenhuma delas, isoladamente, no modelo canônico da aplicação.

## Necessidade

Informações sobre medicamentos estão distribuídas entre diferentes organizações, datasets, APIs e documentos. Uma fonte pode possuir dados de identificação e registro sanitário, outra pode detalhar apresentações comerciais e GTINs, enquanto outra pode fornecer classificações internacionais ou informações regulatórias.

Além disso, duas ou mais fontes podem contribuir para a descrição de uma mesma entidade:

```text
Fonte A
├── identificação do produto
└── registro sanitário

Fonte B
├── apresentação comercial
├── GTIN
└── classificação terapêutica

Fonte C
└── classificação internacional
```

O Atlas existe para transformar essas informações heterogêneas em um catálogo consistente e consultável.

## Responsabilidades

O Atlas é responsável por:

- importar dados de fontes externas;
- preservar o conteúdo bruto utilizado em cada importação;
- interpretar e normalizar os dados recebidos;
- validar os dados importados;
- resolver correspondências entre registros de diferentes fontes;
- consolidar informações sobre uma mesma entidade;
- manter referências e proveniência dos dados;
- registrar processos e execuções de ETL;
- atualizar continuamente o catálogo;
- disponibilizar o catálogo consolidado por meio de uma API.

## Escopo

O Atlas representa principalmente **informações estruturais e de referência sobre medicamentos**.

Entre os principais conceitos do catálogo estão:

```text
Medication
ActiveIngredient
MedicinalProduct
MedicationPackage
Organization / Manufacturer
PharmaceuticalForm
AdministrationRoute
MedicationClassification
MedicationClassificationSystem
MedicationRegulation
RegulatoryAuthority
RegulatoryClassification
```

Também fazem parte do contexto de rastreabilidade:

```text
SourceOrganization
DataSource
DataProvenance
ETLProcess
ETLExecution
RawArtifact
```

## Fora de escopo

O Atlas não é responsável por interpretar clinicamente um tratamento individual.

Análises como interação entre medicamentos, carga sedativa acumulada, contraindicações contextualizadas, riscos de uma combinação ou impacto na rotina do paciente pertencem a serviços especializados. O Atlas fornece a **base de referência estruturada** sobre a qual esses serviços podem trabalhar.

# Dinâmica de funcionamento

O funcionamento do Atlas pode ser dividido em três grandes etapas:

```text
Aquisição
   ↓
Consolidação
   ↓
Consulta
```

## 1. Aquisição

O pipeline obtém informações de fontes externas suportadas, como:

```text
ANVISA
CMED
WHO
openFDA
outras fontes
```

Essas fontes podem ser disponibilizadas por diferentes mecanismos:

```text
CSV
JSON
API HTTP
dataset
documento
```

Antes de qualquer transformação, o conteúdo recebido é preservado como um **RawArtifact**.

```text
Fonte externa
      ↓
   Download
      ↓
 RawArtifact
```

O artefato bruto representa o conteúdo da fonte da forma mais próxima possível daquela em que foi obtido. Isso permite auditoria, reprocessamento, investigação de erros, comparação entre versões e evolução dos parsers sem depender da disponibilidade futura da versão original da fonte.

## 2. ETL e consolidação

Depois da aquisição, o conteúdo passa pelo pipeline:

```text
EXTRACT
   ↓
RAW
   ↓
TRANSFORM
   ↓
VALIDATE
   ↓
RESOLVE
   ↓
LOAD
```

### Extract

Obtém o conteúdo da fonte e registra o artefato bruto utilizado.

### Transform

Interpreta estruturas específicas da fonte e converte seus valores para uma representação compreensível pelo sistema.

Exemplos:

```text
abreviações de apresentação
nomes de substâncias
classificações
identificadores
formas farmacêuticas
vias de administração
```

### Validate

Aplica regras de consistência sobre os dados interpretados.

### Resolve

Determina quando registros de fontes diferentes representam a mesma entidade.

```text
        Entidade consolidada
               ↑
        ┌──────┼──────┐
      fonte A fonte B fonte C
```

Uma nova origem não deve gerar automaticamente uma nova entidade.

### Load

Insere ou atualiza os dados consolidados no banco do Atlas, juntamente com metadados de proveniência e de execução do pipeline.

# Proveniência

A proveniência é um conceito central do Atlas. O sistema não deve apenas armazenar um valor; deve ser capaz de informar de onde aquele dado veio.

Uma mesma entidade pode possuir contribuições de múltiplas fontes:

```text
MedicinalProduct
│
├── DataProvenance
│   ├── DataSource: ANVISA
│   └── Role: IDENTIFICATION
│
├── DataProvenance
│   ├── DataSource: CMED
│   └── Role: PRESENTATION
│
└── DataProvenance
    ├── DataSource: CMED
    └── Role: CLASSIFICATION
```

A proveniência deve permitir responder perguntas como:

```text
De onde veio este registro?
Qual fonte externa o identificou?
Qual referência externa corresponde a ele?
Quando essa informação foi obtida?
Qual execução de ETL participou da sua materialização?
Qual papel aquela fonte teve na formação da entidade?
```

## SourceOrganization e DataSource

O Atlas diferencia a organização responsável pela informação da fonte concreta utilizada.

```text
SourceOrganization
└── CMED

DataSource
└── TA_PRECO_MEDICAMENTO
```

```text
SourceOrganization
└── ANVISA

DataSource
└── DADOS_ABERTOS_MEDICAMENTOS
```

Assim, uma organização pode disponibilizar diversas fontes.

# Execuções de ETL

Um `ETLProcess` representa um processo de tratamento conhecido pelo sistema.

```text
CMED Medication Import
version: 2.1
```

Uma `ETLExecution` representa uma execução concreta desse processo:

```text
ETLExecution
├── startedAt
├── finishedAt
├── status
└── etlProcess
```

Isso diferencia o processo definido de cada execução realizada. A proveniência pode relacionar registros consolidados à execução que participou da sua produção.

# Artefatos brutos

Cada execução pode produzir ou consumir um ou mais artefatos brutos:

```text
RawArtifact
├── DataSource
├── location
├── fileName
├── contentType
├── checksum
├── size
└── retrievedAt
```

O conteúdo pode ser armazenado inicialmente em filesystem:

```text
data/
└── raw/
    └── cmed/
        └── 2026/
            └── 08/
                └── 16/
                    └── <execution-id>/
                        └── TA_PRECO_MEDICAMENTO.csv
```

O banco mantém seus metadados e localização. A implementação pode evoluir posteriormente para object storage sem alterar o conceito.

# Atualização contínua

O Atlas deve funcionar como um catálogo continuamente sincronizado. Uma importação não deve ser tratada simplesmente como apagar todos os dados e importá-los novamente.

Sempre que possível, o pipeline deve identificar:

```text
NEW
UPDATED
UNCHANGED
INACTIVE / MISSING
```

A ausência de um registro em uma nova versão da fonte não implica necessariamente exclusão. Pode representar inativação, alteração da publicação, indisponibilidade temporária, mudança de apresentação ou erro da fonte.

# API

A API é a principal interface de consulta do catálogo.

```text
Consumidor
    ↓
Atlas API
    ↓
Cache
    ↓
Atlas Database
```

A API pode expor recursos relacionados a:

```text
medications
active ingredients
medicinal products
packages
organizations
pharmaceutical forms
administration routes
classifications
regulations
data sources
provenance
```

O contrato público deve representar o modelo consolidado do Atlas, e não reproduzir diretamente o formato de uma fonte externa.

# Cache

O Redis é utilizado para armazenar resultados de consultas reutilizáveis:

```text
Request
   ↓
Redis
 ├── HIT  → Response
 └── MISS
       ↓
     Database
       ↓
     Redis
       ↓
    Response
```

Quando uma importação altera dados relevantes, o pipeline invalida as entradas de cache afetadas.

# Fluxo completo

```text
Fontes externas
ANVISA / CMED / WHO / openFDA / outras
                ↓
          Data Pipeline
                ↓
     ┌──────────┴──────────┐
     ↓                     ↓
Raw Artifact Storage   Atlas Database
                           ↓
                       Atlas API
                           ↓
                         Redis
                           ↓
                      Consumidores
```

# Princípios

## Fonte não é modelo de domínio

Nenhuma fonte externa define isoladamente o modelo do Atlas.

```text
Formato CMED   != modelo Atlas
Formato ANVISA != modelo Atlas
Formato WHO    != modelo Atlas
```

Os componentes de importação conhecem as estruturas externas; o catálogo trabalha com seus próprios conceitos.

## Preservar antes de transformar

O dado bruto deve ser preservado antes de qualquer transformação.

```text
RAW
 ↓
transformação
 ↓
modelo consolidado
```

## Proveniência é parte do dado

Uma informação consolidada deve possuir rastreabilidade suficiente para explicar sua origem.

## Uma entidade pode possuir múltiplas fontes

Não existe necessariamente:

```text
MedicinalProduct.source
```

Existe:

```text
MedicinalProduct
    ↓
múltiplas DataProvenances
```

## ETL deve ser reexecutável

Sempre que possível, os processos devem ser:

- idempotentes;
- auditáveis;
- reproduzíveis;
- versionáveis;
- capazes de reprocessar artefatos históricos.

# Arquitetura

A arquitetura de alto nível é composta por:

- **Atlas API** — interface REST de consulta ao catálogo;
- **Data Pipeline** — worker responsável por aquisição, transformação, validação, resolução e carga;
- **Atlas Database** — banco relacional com o catálogo consolidado, proveniência e metadados de ETL;
- **Raw Artifact Storage** — armazenamento dos conteúdos brutos obtidos das fontes;
- **Redis** — cache de consultas da API.

O diagrama C4 de containers está em:

```text
docs/architecture/atlas-container-c4.puml
```

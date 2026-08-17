# Dicionário de Dados — CMED `TA_PRECO_MEDICAMENTO.csv`

**Órgão:** Câmara de Regulação do Mercado de Medicamentos — CMED / Anvisa  
**Finalidade deste documento:** descrever de forma neutra e completa a estrutura, o significado e as principais regras de interpretação das colunas do arquivo público de preços de medicamentos da CMED.  
**Versão do documento:** 1.1  
**Data de referência:** 16/08/2026

---

# 1. Sobre o arquivo

O arquivo `TA_PRECO_MEDICAMENTO.csv` é uma lista pública mantida pela CMED contendo informações cadastrais, regulatórias, tributárias e econômicas de apresentações de medicamentos.

Ele reúne, entre outras informações:

- princípio(s) ativo(s);
- empresa/laboratório;
- identificadores da apresentação;
- registro sanitário;
- códigos EAN/GTIN;
- nome do produto;
- apresentação;
- classe terapêutica;
- tipo de produto;
- regime de preço;
- preços fábrica;
- preços máximos ao consumidor;
- restrições de comercialização;
- enquadramentos tributários;
- tarja.

A unidade prática de registro da planilha é a **apresentação do medicamento**, e não apenas o nome genérico do produto.

---

# 2. Regras gerais de leitura do CSV

## 2.1 Separador

O arquivo utiliza:

```text
;
```

como delimitador de campos.

É recomendável utilizar um parser CSV compatível com campos entre aspas, pois valores textuais podem conter caracteres especiais e separadores internos.

Não é recomendado interpretar o arquivo por meio de um simples:

```text
split(";")
```

---

## 2.2 Encoding

O arquivo deve ser lido utilizando encoding compatível com caracteres acentuados em português.

Caso haja caracteres corrompidos, verificar primeiro:

- UTF-8;
- Windows-1252 / ISO-8859-1.

A versão efetivamente publicada deve ser usada como referência.

---

## 2.3 Separador decimal

Os valores monetários seguem o padrão brasileiro:

```text
122,09
203,36
2557,42
```

Portanto:

```text
,
```

é usado como separador decimal.

---

## 2.4 Campos numéricos que devem ser tratados como texto

Mesmo sendo compostos apenas por algarismos, os seguintes campos devem ser interpretados como identificadores textuais:

```text
CNPJ
CÓDIGO GGREM
REGISTRO
EAN 1
EAN 2
EAN 3
```

Isso evita problemas como:

- perda de zeros à esquerda;
- conversão para notação científica;
- arredondamento;
- perda de precisão.

---

## 2.5 Valores ausentes

A ausência de informação pode aparecer como:

```text
campo vazio
-
```

Dependendo da coluna.

`-` não deve ser interpretado como zero.

---

# 3. Estrutura das colunas

## 3.1 `SUBSTÂNCIA`

**Tipo:** texto

Identifica a(s) substância(s) relacionada(s) à apresentação do medicamento.

Exemplos:

```text
HEMIFUMARATO DE QUETIAPINA
ACETATO DE CASPOFUNGINA
DIENOGESTE
PITAVASTATINA CÁLCICA
```

Um produto pode conter mais de uma substância.

Exemplo conceitual:

```text
SUBSTÂNCIA A;SUBSTÂNCIA B
```

A denominação pode representar:

- princípio ativo;
- sal;
- éster;
- derivado;
- outra forma química utilizada no registro.

Portanto, a denominação apresentada nessa coluna não deve ser automaticamente interpretada como nome internacional INN.

---

## 3.2 `CNPJ`

**Tipo:** texto

Número de inscrição da empresa no Cadastro Nacional da Pessoa Jurídica.

Exemplo:

```text
61.190.096/0001-92
```

O formato normalmente utiliza:

```text
XX.XXX.XXX/XXXX-XX
```

É um identificador brasileiro e deve ser preservado como texto.

---

## 3.3 `LABORATÓRIO`

**Tipo:** texto

Razão social da empresa/laboratório associada ao produto/apresentação na base da CMED.

Exemplo:

```text
EUROFARMA LABORATÓRIOS S.A.
```

O campo representa a entidade empresarial registrada na base e não deve ser interpretado automaticamente como a localização física da unidade fabril.

---

## 3.4 `CÓDIGO GGREM`

**Tipo:** texto

Código utilizado pela CMED para identificar uma apresentação de medicamento.

Exemplo:

```text
508015060107606
```

Características importantes:

- é gerado no contexto da CMED;
- identifica uma apresentação específica;
- não equivale ao número de registro sanitário;
- não equivale ao EAN/GTIN.

---

## 3.5 `REGISTRO`

**Tipo:** texto

Número completo do registro sanitário da apresentação do medicamento na Anvisa.

Exemplo:

```text
1004311080020
```

No contexto do SAMMED/CMED, o número completo inclui os dígitos relativos à apresentação.

Deve ser lido como texto.

---

## 3.6 `EAN 1`

**Tipo:** texto

Primeiro código EAN/GTIN associado à apresentação.

Exemplo:

```text
7891317134303
```

Usualmente corresponde a um GTIN-13.

---

## 3.7 `EAN 2`

**Tipo:** texto

Segundo código EAN/GTIN que pode estar associado à mesma apresentação.

Pode aparecer como:

```text
-
```

quando não houver valor informado.

---

## 3.8 `EAN 3`

**Tipo:** texto

Terceiro código EAN/GTIN que pode estar associado à mesma apresentação.

Pode aparecer como:

```text
-
```

quando não houver valor informado.

---

# 4. Identificadores: diferenças importantes

Os campos abaixo possuem finalidades diferentes:

```text
CÓDIGO GGREM
REGISTRO
EAN
```

### CÓDIGO GGREM

Identificador da apresentação dentro do sistema da CMED.

### REGISTRO

Identificador sanitário da apresentação perante a Anvisa.

### EAN/GTIN

Identificador comercial utilizado em código de barras.

Os três valores não devem ser considerados sinônimos.

---

# 5. `PRODUTO`

**Tipo:** texto

Nome do medicamento/produto registrado.

Exemplos:

```text
QUET XR
HEMIFUMARATO DE QUETIAPINA
NOVALGINA
```

Em medicamentos de marca, normalmente contém o nome comercial.

Em genéricos, frequentemente corresponde à denominação do princípio ativo.

---

# 6. `APRESENTAÇÃO`

**Tipo:** texto estruturado

É um dos campos mais densos da planilha.

Pode conter, em uma única string:

- dosagem/concentração;
- forma farmacêutica;
- tipo de liberação;
- via de administração;
- embalagem primária;
- embalagem secundária;
- material de embalagem;
- quantidade.

Exemplo:

```text
50 MG COM REV LIB PROL CT BL PLAS TRANS AL X 30
```

Outro:

```text
70 MG PO LIOF SOL INFUS IV CT FA VD TRANS
```

A coluna utiliza abreviações padronizadas ou históricas empregadas pela Anvisa/CMED.

---

# 7. Abreviações frequentes de forma farmacêutica

| Abreviação | Significado |
|---|---|
| `COM` | Comprimido |
| `COM REV` | Comprimido revestido |
| `COM LIB PROL` | Comprimido de liberação prolongada |
| `COM REV LIB PROL` | Comprimido revestido de liberação prolongada |
| `CAP` | Cápsula |
| `CAP DURA` | Cápsula dura |
| `CAP MOLE` | Cápsula mole |
| `PO` | Pó |
| `PO LIOF` | Pó liofilizado |
| `PO LIOF SOL INJ` | Pó liofilizado para solução injetável |
| `PO SOL INFUS` | Pó para solução para infusão |
| `SOL` | Solução |
| `SOL INJ` | Solução injetável |
| `SOL INFUS` | Solução para infusão |
| `SOL GOT` | Solução gotas |
| `SUS` | Suspensão |
| `SUS GOT` | Suspensão gotas |
| `GEL` | Gel |
| `POM` | Pomada |

A lista não é exaustiva.

Para interpretação rigorosa, deve-se utilizar o Vocabulário Controlado de Formas Farmacêuticas da Anvisa.

---

# 8. Abreviações frequentes de via de administração

| Abreviação | Significado |
|---|---|
| `OR` | Oral |
| `IV` | Intravenosa |
| `IM` | Intramuscular |
| `SC` | Subcutânea |
| `ID` | Intradérmica |
| `IT` | Intratecal |
| `OFT` | Oftálmica |
| `NAS` | Nasal |
| `OTO` | Otológica |
| `RET` | Retal |
| `VAG` | Vaginal |
| `SUBL` | Sublingual |
| `TRANSD` | Transdérmica |
| `INAL` | Inalatória |
| `INAL OR` | Inalatória por via oral |
| `INAL NAS` | Inalatória por via nasal |

A lista não é exaustiva.

---

# 9. Abreviações frequentes de embalagem

| Abreviação | Significado |
|---|---|
| `CT` | Cartucho |
| `CX` | Caixa |
| `BL` | Blíster |
| `AMP` | Ampola |
| `FA` | Frasco-ampola |
| `FR` | Frasco |
| `BG` | Bisnaga |
| `ENV` | Envelope |
| `STR` | Strip |
| `SER PREENC` | Seringa preenchida |
| `AL` | Alumínio |
| `PLAS` | Plástico |
| `VD` | Vidro |
| `AMB` | Âmbar |
| `OPC` | Opaco |
| `TRANS` | Transparente |
| `TRANSL` | Translúcido |

A composição de embalagem pode conter múltiplos materiais.

---

# 10. Exemplos de interpretação de `APRESENTAÇÃO`

## Exemplo 1

```text
50 MG COM REV LIB PROL CT BL PLAS TRANS AL X 30
```

Pode ser lido aproximadamente como:

```text
50 MG
→ dosagem/concentração

COM REV LIB PROL
→ comprimido revestido de liberação prolongada

CT
→ cartucho

BL
→ blíster

PLAS TRANS AL
→ materiais/características da embalagem

X 30
→ 30 unidades
```

---

## Exemplo 2

```text
70 MG PO LIOF SOL INFUS IV CT FA VD TRANS
```

Pode ser interpretado como:

```text
70 MG
→ dosagem/concentração

PO LIOF
→ pó liofilizado

SOL INFUS
→ destinado ao preparo de solução para infusão

IV
→ intravenoso

CT
→ cartucho

FA
→ frasco-ampola

VD TRANS
→ vidro transparente
```

---

# 11. `CLASSE TERAPÊUTICA`

**Tipo:** texto estruturado

Exemplo:

```text
N5A1 - ANTIPSICÓTICOS ATÍPICOS
```

A coluna utiliza classificação terapêutica baseada na **EPhMRA — European Pharmaceutical Market Research Association**.

Na documentação do SAMMED, a CMED utiliza a classificação em nível IV.

Estrutura típica:

```text
CÓDIGO - DESCRIÇÃO
```

Exemplos:

```text
N5A1 - ANTIPSICÓTICOS ATÍPICOS
J2A - AGENTES SISTÊMICOS PARA INFECÇÕES FÚNGICAS
G3D - PROGESTÓGENOS EXCLUINDO G3A, G3F
C10A1 - ESTATINAS, INIBIDORES DA REDUTASE HMG-CoA
```

---

## 11.1 EPhMRA não é ATC

A classificação EPhMRA não deve ser confundida com a classificação:

```text
ATC
```

da Organização Mundial da Saúde.

Os códigos podem possuir aparência semelhante, mas são sistemas distintos.

---

# 12. `TIPO DE PRODUTO (STATUS DO PRODUTO)`

**Tipo:** texto categórico

Representa a categoria regulatória/comercial do produto.

Exemplos encontrados incluem:

```text
Genérico
Similar
Novo
Biológico
Específico
Radiofármaco
```

A lista pode variar ao longo do tempo.

O valor deve ser interpretado exatamente conforme publicado na versão da planilha utilizada.

---

# 13. `REGIME DE PREÇO`

**Tipo:** texto categórico

Indica o regime econômico aplicável ao produto.

Valores encontrados incluem:

```text
Regulado
Liberado
```

### Regulado

Produto sujeito às regras de regulação de preços da CMED.

### Liberado

Produto enquadrado em regime específico de liberação de preço conforme regras da CMED.

O significado jurídico e econômico deve ser interpretado conforme a regulamentação vigente.

---

# 14. PF — Preço Fábrica

O **Preço Fábrica (PF)** representa o teto de preço pelo qual fabricantes ou distribuidores podem comercializar o medicamento nas condições estabelecidas pela CMED.

Os valores variam de acordo com cenários tributários.

---

## 14.1 `PF Sem Impostos`

**Tipo:** decimal

Preço fábrica sem os tributos considerados pela metodologia da CMED.

Não deve ser automaticamente interpretado como equivalente a `PF 0%`.

---

## 14.2 `PF 0%`

**Tipo:** decimal

Preço Fábrica considerando alíquota de ICMS de 0%.

---

## 14.3 `PF 12%`

Preço Fábrica considerando ICMS de:

```text
12%
```

---

## 14.4 `PF 17%`

Preço Fábrica considerando ICMS de:

```text
17%
```

---

## 14.5 `PF 17% ALC`

Preço Fábrica para:

```text
ICMS 17%
```

em condição de:

```text
ALC
```

Área de Livre Comércio.

---

## 14.6 `PF 17,5%`

Preço Fábrica considerando ICMS de:

```text
17,5%
```

---

## 14.7 `PF 17,5% ALC`

Preço Fábrica considerando:

```text
ICMS 17,5%
```

em Área de Livre Comércio.

---

## 14.8 `PF 18%`

Preço Fábrica considerando ICMS de:

```text
18%
```

---

## 14.9 `PF 18% ALC`

Preço Fábrica considerando:

```text
ICMS 18%
```

em Área de Livre Comércio.

---

## 14.10 `PF 20%`

Preço Fábrica considerando ICMS de:

```text
20%
```

---

# 15. PMC — Preço Máximo ao Consumidor

O **Preço Máximo ao Consumidor (PMC)** representa o preço máximo permitido para venda ao consumidor em farmácias e drogarias, observadas as regras da CMED e a tributação aplicável.

---

## 15.1 `PMC 0%`

Preço Máximo ao Consumidor considerando:

```text
ICMS 0%
```

---

## 15.2 `PMC 12%`

PMC considerando:

```text
ICMS 12%
```

---

## 15.3 `PMC 17%`

PMC considerando:

```text
ICMS 17%
```

---

## 15.4 `PMC 17% ALC`

PMC considerando:

```text
ICMS 17%
```

em Área de Livre Comércio.

---

## 15.5 `PMC 17,5%`

PMC considerando:

```text
ICMS 17,5%
```

---

## 15.6 `PMC 17,5% ALC`

PMC considerando:

```text
ICMS 17,5%
```

em Área de Livre Comércio.

---

## 15.7 `PMC 18%`

PMC considerando:

```text
ICMS 18%
```

---

## 15.8 `PMC 18% ALC`

PMC considerando:

```text
ICMS 18%
```

em Área de Livre Comércio.

---

## 15.9 `PMC 20%`

PMC considerando:

```text
ICMS 20%
```

---

# 16. `RESTRIÇÃO HOSPITALAR`

**Tipo:** booleano textual

Valores:

```text
Sim
Não
```

Indica se a apresentação possui restrição relacionada ao uso/comercialização hospitalar.

Produtos de uso restrito hospitalar podem não possuir PMC destinado à comercialização varejista comum.

---

# 17. `CAP`

**Tipo:** booleano textual

Valores:

```text
Sim
Não
```

CAP significa:

```text
Coeficiente de Adequação de Preços
```

É um mecanismo de desconto obrigatório aplicável em determinadas vendas de medicamentos ao poder público.

A coluna indica o enquadramento/aplicabilidade.

Ela não representa diretamente o percentual vigente do CAP.

---

# 18. `CONFAZ 87`

**Tipo:** booleano textual

Valores:

```text
Sim
Não
```

Indica enquadramento relacionado ao:

```text
Convênio ICMS 87/2002
```

do CONFAZ.

Esse convênio disciplina hipóteses de isenção de ICMS para determinados fármacos e medicamentos destinados a órgãos da Administração Pública.

---

# 19. `ICMS 0%`

**Tipo:** booleano textual

Valores:

```text
Sim
Não
```

Indica enquadramento da apresentação em situação de ICMS zero/isenção conforme as regras aplicáveis à lista.

Não deve ser confundido com:

```text
CONFAZ 87
```

apesar de haver relação tributária entre os conceitos.

---

# 20. `ANÁLISE RECURSAL`

**Tipo:** texto

Campo relacionado à situação administrativa de análise de preço perante a CMED.

Pode estar associado a:

- pedido de reconsideração;
- recurso;
- análise ainda em andamento.

Pode aparecer vazio quando não aplicável.

---

# 21. `LISTA DE CONCESSÃO DE CRÉDITO TRIBUTÁRIO (PIS/COFINS)`

**Tipo:** texto categórico

Valores encontrados:

```text
Positiva
Negativa
Neutra
```

Representa a classificação tributária utilizada no regime de PIS/Pasep e COFINS aplicável ao medicamento.

---

## 21.1 Lista Positiva

Medicamentos enquadrados no regime tributário correspondente à Lista Positiva.

---

## 21.2 Lista Negativa

Medicamentos enquadrados no regime correspondente à Lista Negativa.

---

## 21.3 Lista Neutra

Medicamentos não enquadrados nas condições das listas Positiva ou Negativa, conforme a regulamentação tributária aplicável.

---

# 22. `COMERCIALIZAÇÃO 2020`

**Tipo:** booleano textual

Valores:

```text
Sim
Não
```

Indica se houve comercialização do produto no ano de:

```text
2020
```

O ano faz parte da própria semântica do campo.

Portanto:

```text
COMERCIALIZAÇÃO 2020 = Sim
```

não significa necessariamente que o produto continua sendo comercializado atualmente.

É um campo histórico.

---

# 23. `TARJA`

**Tipo:** texto categórico

Informa a classificação de tarja/condição de venda apresentada pela CMED.

Valores encontrados podem incluir:

```text
Tarja Vermelha
Tarja Preta
Tarja Sem Tarja
```

Além de variações textuais como:

```text
Tarja Vermelha(*)
Tarja Vermelha sob restrição
```

Espaços duplicados e observações entre parênteses também podem ocorrer.

---

## 23.1 Tarja Vermelha

Indica medicamento sujeito a prescrição médica segundo as regras aplicáveis.

Dependendo do medicamento, podem existir exigências adicionais de dispensação e retenção de receita.

---

## 23.2 Tarja Preta

Indica medicamento sujeito a controle especial mais restritivo e regras específicas de prescrição/dispensação.

---

## 23.3 Sem Tarja

Indica ausência de tarja vermelha ou preta na condição de venda apresentada pela base.

Isso não deve ser confundido com a faixa amarela usada para identificar medicamentos genéricos.

---

# 24. Ordem completa das colunas

A estrutura observada no arquivo é:

```text
1.  SUBSTÂNCIA
2.  CNPJ
3.  LABORATÓRIO
4.  CÓDIGO GGREM
5.  REGISTRO
6.  EAN 1
7.  EAN 2
8.  EAN 3
9.  PRODUTO
10. APRESENTAÇÃO
11. CLASSE TERAPÊUTICA
12. TIPO DE PRODUTO (STATUS DO PRODUTO)
13. REGIME DE PREÇO
14. PF Sem Impostos
15. PF 0%
16. PF 12%
17. PF 17%
18. PF 17% ALC
19. PF 17,5%
20. PF 17,5% ALC
21. PF 18%
22. PF 18% ALC
23. PF 20%
24. PMC 0%
25. PMC 12%
26. PMC 17%
27. PMC 17% ALC
28. PMC 17,5%
29. PMC 17,5% ALC
30. PMC 18%
31. PMC 18% ALC
32. PMC 20%
33. RESTRIÇÃO HOSPITALAR
34. CAP
35. CONFAZ 87
36. ICMS 0%
37. ANÁLISE RECURSAL
38. LISTA DE CONCESSÃO DE CRÉDITO TRIBUTÁRIO (PIS/COFINS)
39. COMERCIALIZAÇÃO 2020
40. TARJA
```

---

# 25. Resumo dos tipos recomendados para leitura

| Campo | Tipo recomendado |
|---|---|
| SUBSTÂNCIA | String |
| CNPJ | String |
| LABORATÓRIO | String |
| CÓDIGO GGREM | String |
| REGISTRO | String |
| EAN 1 | String |
| EAN 2 | String |
| EAN 3 | String |
| PRODUTO | String |
| APRESENTAÇÃO | String |
| CLASSE TERAPÊUTICA | String |
| TIPO DE PRODUTO | String |
| REGIME DE PREÇO | String |
| PF ... | Decimal |
| PMC ... | Decimal |
| RESTRIÇÃO HOSPITALAR | Boolean |
| CAP | Boolean |
| CONFAZ 87 | Boolean |
| ICMS 0% | Boolean |
| ANÁLISE RECURSAL | String / nullable |
| LISTA PIS/COFINS | String / enum |
| COMERCIALIZAÇÃO 2020 | Boolean |
| TARJA | String / enum normalizado |

---

# 26. Cuidados importantes na interpretação

## 26.1 Não tratar `SUBSTÂNCIA` como um nome internacional canônico

O valor pode conter:

```text
sal
éster
derivado
forma química específica
```

---

## 26.2 Não tratar `PRODUTO` e `SUBSTÂNCIA` como equivalentes

Eles podem possuir o mesmo texto em medicamentos genéricos, mas representam conceitos distintos na planilha.

---

## 26.3 Não tratar `CÓDIGO GGREM`, `REGISTRO` e `EAN` como a mesma chave

Cada um possui finalidade própria.

---

## 26.4 Não interpretar `CLASSE TERAPÊUTICA` como ATC

A coluna utiliza EPhMRA.

---

## 26.5 Não interpretar `TARJA` como classe terapêutica

Tarja representa uma dimensão regulatória/condição de venda.

---

## 26.6 Não usar `COMERCIALIZAÇÃO 2020` como indicador de disponibilidade atual

A coluna refere-se exclusivamente ao ano explicitado.

---

## 26.7 Não tratar ausência de PMC automaticamente como erro

Algumas apresentações, especialmente hospitalares ou submetidas a regras específicas, podem não possuir PMC aplicável.

---

## 26.8 Não associar diretamente uma alíquota PF/PMC a um estado

Os cabeçalhos representam cenários de alíquota tributária.

A aplicabilidade territorial depende da legislação tributária vigente.

---

# 27. Referências

## CMED — preços de medicamentos

`https://www.gov.br/anvisa/pt-br/assuntos/medicamentos/cmed/precos`

## Arquivo CSV

`https://www.gov.br/anvisa/pt-br/assuntos/medicamentos/cmed/precos/arquivos/ta_preco_medicamento.csv`

## Manual do Sistema SAMMED

`https://www.gov.br/anvisa/pt-br/assuntos/medicamentos/cmed/legislacao/arquivos/arquivos/6065json-file-1/@@download/file/6065json-file-1.pdf`

## Dicionário de Dados — Consumidor

`https://dados.anvisa.gov.br/dados/Dicion%C3%A1rio%20Consumidor.docx`

## Vocabulário Controlado de Formas Farmacêuticas, Vias de Administração e Embalagens

`https://www.gov.br/anvisa/pt-br/centraisdeconteudo/publicacoes/medicamentos/publicacoes-sobre-medicamentos/vocabulario-controlado.pdf/@@download/file`

## CONFAZ — Convênio ICMS 87/2002

`https://www.confaz.fazenda.gov.br/legislacao/convenios/2002/CV087_02`

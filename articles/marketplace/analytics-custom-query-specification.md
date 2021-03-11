---
title: Especificação de consulta personalizada
description: Aprenda a usar consultas personalizadas para extrair dados programáticamente de tabelas de análise para as suas ofertas no mercado comercial da Microsoft.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 4be063342a6c46d73c86f2d9dff1da5395328389
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102584024"
---
# <a name="custom-query-specification"></a>Especificação de consulta personalizada

Os parceiros podem usar esta especificação de consulta para formular facilmente consultas personalizadas para extrair dados de tabelas de análise. As consultas podem ser usadas para selecionar apenas as colunas e métricas desejadas que correspondam a um determinado critério. No centro da especificação linguística está a definição de conjunto de dados na qual uma consulta personalizada pode ser escrita.

## <a name="datasets"></a>Conjuntos de dados

Da mesma forma que algumas consultas são executadas contra uma base de dados que tem tabelas e colunas, uma consulta personalizada funciona em Datasets que têm colunas e métricas. A lista completa dos conjuntos de dados disponíveis para formular uma consulta pode ser obtida utilizando a API dos conjuntos de dados.

Aqui está um exemplo de um conjunto de dados mostrado como um JSON.

```json
{
     "datasetName": "ISVUsage",
     "selectableColumns": [
       "MarketplaceSubscriptionId",
       "OfferName",
            "CustomerId",
     "MonthStartDate",
     "SKU"
    ],
    "availableMetrics": [
     "NormalizedUsage", 
     "RawUsage"
        "EstimatedExtendedChargeCC"
    ],
    "availableDateRanges": [
     "LAST_MONTH",
     "LAST_3_MONTHS",
     "LAST_6_MONTHS",
     "LIFETIME"
    ]
}
```

### <a name="parts-of-a-dataset"></a>Partes de um conjunto de dados

- Um nome de conjunto de dados é como um nome de tabela de base de dados. Por exemplo, ISVUsage. Um conjunto de dados tem uma lista de colunas que podem ser selecionadas, como MarketplaceSubscriptionId.
- Um conjunto de dados também tem métricas, que são como funções de agregação numa base de dados. Por exemplo, Normalização.
- Existem períodos de tempo fixos sobre os quais os dados podem ser exportados.

### <a name="formulating-a-query-on-a-dataset"></a>Formular uma consulta num conjunto de dados

Estas são algumas consultas de amostra que mostram como extrair vários tipos de dados.

| Consulta | Description |
| ------------ | ------------- |
| **SELECIONE** MarketplaceSubscriptionId,CustomerId **FROM** ISVUsage **TIMESPAN LAST_MONTH** | Esta consulta terá cada único `MarketplaceSubscriptionId` e correspondente no último `CustomerId` mês. |
| **SELECIONE** MarketplaceSubscriptionId, EstimatedExtendedChargecc **from** ISVUsage **ORDER BY** EstimatedExtendedChargeCC **LIMIT** 10 | Esta consulta obterá as 10 melhores subscrições em ordem decrescente do número de licenças vendidas em cada subscrição. |
| **SELECIONE** CustomerId, NormalizedUsage, RawUsage **FROM** ISVUsage **ORDER BY** NormalizedUsage **WHERE** NormalizedUsage > 100000 **ORDER BY** NormalizedUsage **TIMESPAN** LAST_6_MONTHS | Esta consulta receberá a Normalização de Usage e RawUsage de todos os Clientes que tenham NormalizedUsage superior a 100.000. |
| **SELECIONE** MarketplaceSubscriptionId, MonthStartDate, Normalização **da** ISVUsage **WHERE** CustomerId IN ('2a31c234-1f4e-4c60-909e-76d234f93161', '80780748-3f9a-11eb-b378-0242ac13002') | Esta consulta obterá a `MarketplaceSubscriptionId` receita gerada por cada mês pelos dois `CustomerId` valores: `2a31c234-1f4e-4c60-909e-76d234f93161` e `80780748-3f9a-11eb-b378-0242ac130002` . |
|||

## <a name="query-specification"></a>Especificação de consulta

Esta secção descreve a definição e estrutura da consulta.

### <a name="grammar-reference"></a>Referência gramatical

Esta tabela descreve os símbolos usados nas consultas.

| Símbolo | Significado |
| ------------ | ------------- |
| ? | Opcional |
| * | Zero ou mais |
| + | Um ou mais |
| &#124; | Ou/Uma das listas |
| | |

### <a name="query-definition"></a>Definição de consulta

A declaração de consulta tem as seguintes cláusulas: SelectClause, FromClause, WhereClause?, OrderClause?, LimitClause?, e TimeSpan?.

- **SelectClause**: **SELECT** ColumOrMetricName (, ColumOrMetricName)*
    - **Nome ColumOrMetricName**: Colunas e Métricas definidas no Conjunto de Dados
- **FromClause**: **From** DatasetName
    - **DatasetName**: Nome do conjunto de dados definido no Conjunto de Dados
- **WhereClause**: **WHERE** FilterCondition **(E** FilterCondition)*
    - **FiltragemCondição**: Valor do operador columormétrico
        - **Operador**: = | > | < | >= | <= | != | Como | NÃO COMO | IN | NÃO EM
        - **Valor**: Número | | De CordasLiteral | MultiNumberList MultiStringList 
            - **Número:**- ? [0-9]+ (. [0-9] [0-9]*)?
            - **StringLiteral**: ' [a-zA-Z0-9_]*'
            - **MultiNumberList**: (Número(Número)*)
            - **MultiStringList**: (StringLiteral (,StringLiteral)*)
- **OrdemClause**: **ORDEM POR** OrdemCondição (,Ordem Decondição)*
    - **OrdemCondição**: ColumOrMetricName **(ASC**  |  **DESC)***
    - **LimitClause**: **LIMIT** [0-9]+
    - **TimeSpan**: **TIMEPAN** (HOJE | ONTEM | LAST_7_DAYS | LAST_14_DAYS | LAST_30_DAYS | LAST_90_DAYS | LAST_180_DAYS | LAST_365_DAYS | LAST_MONTH | LAST_3_MONTHS | LAST_6_MONTHS | LAST_1_YEAR | VIDA ÚTIL)

### <a name="query-structure"></a>Estrutura de consulta

Uma consulta de relatório é composta por várias partes:

- SELECIONAR
- FROM
- WHERE
- ORDENAR POR
- LIMITE
- TIMEPAN

Cada parte é descrita abaixo.

#### <a name="select"></a>SELECIONAR

Esta parte da consulta especifica as colunas que serão exportadas. As colunas que podem ser selecionadas são os campos `selectableColumns` listados e `availableMetrics` secções de um conjunto de dados. As linhas exportadas finais contêm sempre valores distintos nas colunas selecionadas. Por exemplo, não haverá filas duplicadas no ficheiro exportado. As métricas serão calculadas para cada combinação distinta das colunas selecionadas.

**Exemplo:**
- **SELECIONE,** `OfferName``NormalizedUsage`

#### <a name="from"></a>FROM

Esta parte da consulta indica o conjunto de dados a partir do qual os dados devem ser exportados. O nome do conjunto de dados aqui dado precisa de ser um nome de conjunto de dados válido devolvido pela API dos conjuntos de dados.

**Exemplo:**
- De `ISVUsage`
- De `ISVOrder`

#### <a name="where"></a>WHERE

Esta parte da consulta é utilizada para especificar as condições do filtro no conjunto de dados. Apenas as linhas correspondentes a todas as condições enumeradas nesta cláusula estarão presentes no ficheiro final exportado. A condição do filtro pode estar em qualquer uma das colunas listadas `selectableColumns` em e `availableMetrics` . Os valores especificados na condição do filtro podem ser uma lista de números ou uma lista de cordas apenas quando o operador estiver `IN` ou `NOT IN` . Os valores podem sempre ser dados como uma corda literal e serão convertidos para os tipos nativos de colunas. As várias condições do filtro devem ser separadas com uma `AND` operação.

**Exemplo:**

- MarketplaceSubscriptionId = '868368da-957d-4959-8992-3c12dc7e6260'
- Nome do cliente **COMO** '%Contosso%'
- CustomerId **NOT IN** (1000, 1001, 1002)
- OrdemQuantity=100
- OrdemQuantity='100'
    - MarketplaceSubscriptionD='7b487ac0-ce12-b732-dcd6-91a1e4e74a50' AND CustomerId=' 0f8b7fa0-eb83-a183-1225-ca153ef807aa'

#### <a name="order-by"></a>ORDENAR POR

Esta parte da consulta especifica os critérios de encomenda das linhas exportadas. As colunas sobre as quais a encomenda pode ser definida têm de ser do `selectableColumns` conjunto `availableMetrics` de dados e do conjunto de dados. Se não houver uma direção de encomenda especificada, será incumprida `DESC` na coluna. A encomenda pode ser definida em várias colunas separando os critérios com uma vírgula.

**Exemplo:**

- **ENCOMENDAR POR** Normalizado Usage **ASC**, EstimativaExtendedCharge(CC) **DESC**
- **ENCOMENDAR POR** Nome de cliente **ASC**, Normalização Desatada

#### <a name="limit"></a>LIMITE

Esta parte da consulta especifica o número de linhas que serão exportadas. O número que especifica tem de ser um número não-número positivo.

#### <a name="timespan"></a>TIMEPAN

Esta parte da consulta especifica a duração para a qual os dados devem ser exportados. Os valores possíveis devem ser do `availableDateRanges` campo na definição do conjunto de dados.

### <a name="case-sensitivity-in-query-specification"></a>Sensibilidade do caso na especificação de consulta

A especificação é completamente insensível. Palavras-chave predefinidas, nomes de colunas e valores podem ser especificados utilizando maiúsão ou minúscula.

## <a name="see-also"></a>Ver também

- [Lista de consultas de sistema](analytics-system-queries.md)

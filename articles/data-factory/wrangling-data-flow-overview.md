---
title: Fluxos de dados Wrangling em Azure Data Factory
description: Uma visão geral dos fluxos de dados do Wrangling no Azure Data Factory
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: e36cc044e6a4160d16f15b93d8a88d946f476c89
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74287105"
---
# <a name="what-are-wrangling-data-flows"></a>O que são fluxos de dados Wrangling?

As organizações precisam fazer a preparação de dados e Wrangling para uma análise precisa de dados complexos que continuam crescendo todos os dias. A preparação de dados é necessária para que as organizações possam usar os dados em vários processos de negócios e reduzir o tempo de implantação.

Os fluxos de dados do Wrangling no Azure Data Factory permitem que você faça uma preparação de dados sem código em escala de nuvem iterativamente. Os fluxos de dados do Wrangling integram-se ao [Power Query online](https://docs.microsoft.com/power-query/) e tornam Power Query funções M disponíveis para usuários data Factory.

O fluxo de dados Wrangling converte M gerado pelo editor de mashup do Power Query online no código do Spark para execução em escala de nuvem.

Os fluxos de dados do Wrangling são especialmente úteis para engenheiros de dados ou "integradores de dados do cidadão".

## <a name="use-cases"></a>Casos de utilização

### <a name="fast-interactive-data-exploration-and-preparation"></a>Exploração e preparação rápida de dados interativos

Vários engenheiros de dados e integradores de dados do cidadão podem explorar e preparar interativamente conjuntos de dados em escala de nuvem. Com o aumento do volume, variedade e velocidade dos dados em data lagos, os usuários precisam de uma maneira eficaz de explorar e preparar conjuntos de dados. Por exemplo, talvez seja necessário criar um conjunto de dados que ' tenha todas as informações demográficas do cliente para novos clientes desde 2017 '. Você não está mapeando para um destino conhecido. Você está explorando, Wrangling e preparando conjuntos de valores para atender a um requisito antes de publicá-lo no Lake. Os fluxos de dados Wrangling geralmente são usados para cenários de análise menos formal. Os conjuntos de informações de uso podem ser usados para fazer transformações e operações de aprendizado de máquina downstream.

### <a name="code-free-agile-data-preparation"></a>Preparação de dados ágil sem código

Os integradores de dados do cidadão gastam mais de 60% do tempo procurando e preparando dados. Eles estão procurando fazer isso de forma gratuita de código para melhorar a produtividade operacional. Permitir que os integradores de dados do cidadão enriquecem, formam e publiquem dados usando ferramentas conhecidas como Power Query online de forma escalonável aumentam drasticamente sua produtividade. O fluxo de dados do Wrangling no Azure Data Factory permite que o conhecido editor de mashup do Power Query online permita que os integradores de dados do cidadão corrijam erros rapidamente, padronize dados e produzam dados de alta qualidade para dar suporte às decisões de negócios.

### <a name="data-validation"></a>Validação de dados

Examine visualmente seus dados de forma livre de código para remover exceções, anomalias e ti de acordo com uma forma para análise rápida.

## <a name="supported-sources"></a>Fontes com suporte

| Conector | Formato de dados | Tipo de autenticação |
| -- | -- | --|
| [Armazenamento de Blobs do Azure](connector-azure-blob-storage.md) | CSV | Chave da Conta |
| [Armazenamento do Azure Data Lake Ger1](connector-azure-data-lake-store.md) | CSV | Principal de Serviço |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | CSV | Chave de conta, entidade de serviço |
| [Base de Dados SQL do Azure](connector-azure-sql-database.md) | - | Autenticação do SQL |
| [Análise de Synapse do Azure](connector-azure-sql-data-warehouse.md) | - | Autenticação do SQL |

## <a name="the-mashup-editor"></a>O editor de mashup

Quando você cria um fluxo de dados do Wrangling, todos os DataSets de origem tornam-se consultas de conjunto e são colocados na pasta **ADFResource** Por padrão, o userquery apontará para a primeira consulta de DataSet. Todas as transformações devem ser feitas no userquery, pois as alterações nas consultas de conjunto de consulta não são suportadas, nem serão persistidas. No momento, não há suporte para a renomeação, adição e exclusão de consultas.

![Wrangling](media/wrangling-data-flow/editor.png)

Atualmente, não há suporte para todas as funções Power Query M para Wrangling de dados, apesar de estarem disponíveis durante a criação. Ao criar os fluxos de dados do Wrangling, a seguinte mensagem de erro será exibida se não houver suporte para uma função:

`The wrangling data flow is invalid. Expression.Error: The transformation logic isn't supported. Please try a simpler expression`

Para obter mais informações sobre as transformações com suporte, consulte [funções de fluxo de dados do Wrangling](wrangling-data-flow-functions.md).

Atualmente, o fluxo de dados Wrangling só dá suporte à gravação em um coletor.

## <a name="next-steps"></a>Passos seguintes

Saiba como [criar um fluxo de dados do Wrangling](wrangling-data-flow-tutorial.md).
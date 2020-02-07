---
title: Fluxos de dados em Azure Data Factory
description: Uma visão geral dos fluxos de dados em Azure Data Factory
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 8f91ed926c733b211443805722b6817b1ce005b6
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2020
ms.locfileid: "77048350"
---
# <a name="what-are-wrangling-data-flows"></a>O que são fluxos de dados?

As organizações precisam de fazer a preparação de dados e a luta para uma análise precisa de dados complexos que continuam a crescer todos os dias. É necessária a preparação de dados para que as organizações possam utilizar os dados em vários processos de negócio e reduzir o tempo para valorizar.

Os fluxos de dados em Azure Data Factory permitem-lhe fazer a preparação de dados sem código sem código sem escala em escala de nuvem iterativamente. Os fluxos de dados de contorcer-se integram-se com a [Power Query Online](https://docs.microsoft.com/power-query/) e disponibiliza funções de Power Query M para utilizadores de fábricas de dados.

O fluxo de dados de sangling traduz M gerado pelo Power Query Online Mashup Editor em código de faísca para execução em escala de nuvem.

Os fluxos de dados de contorcer em conflito são especialmente úteis para engenheiros de dados ou "integradores de dados dos cidadãos".

## <a name="use-cases"></a>Casos de utilização

### <a name="fast-interactive-data-exploration-and-preparation"></a>Exploração e preparação de dados interativos rápidos

Vários engenheiros de dados e integradores de dados de cidadãos podem explorar e preparar conjuntos de dados em escala de nuvem. Com o aumento do volume, variedade e velocidade dos dados nos lagos de dados, os utilizadores precisam de uma forma eficaz de explorar e preparar conjuntos de dados. Por exemplo, poderá ter de criar um conjunto de dados que "tenha toda a informação demográfica do cliente para novos clientes desde 2017". Você não está mapeando para um destino conhecido. Estás a explorar, a discutir e a preparar conjuntos de dados para satisfazer um requisito antes de o publicares no lago. Os fluxos de dados de estrangulamento são frequentemente usados para cenários de análise menos formais. Os conjuntos de dados preparados podem ser usados para fazer transformações e operações de aprendizagem automática a jusante.

### <a name="code-free-agile-data-preparation"></a>Preparação de dados ágeis sem código

Os integradores de dados dos cidadãos passam mais de 60% do seu tempo à procura e a preparar dados. Querem fazê-lo de forma livre de códigos para melhorar a produtividade operacional. Permitir aos integradores de dados dos cidadãos enriquecer, moldar e publicar dados usando ferramentas conhecidas como Power Query Online de uma forma escalável melhora drasticamente a sua produtividade. O fluxo de dados em Azure Data Factory permite ao conhecido editor de mashup Power Query Online permitir que os integradores de dados dos cidadãos corrijam erros rapidamente, padronizem dados e produzam dados de alta qualidade para apoiar decisões empresariais.

### <a name="data-validation"></a>Validação de dados

Digitalmente, digitalize os seus dados de forma livre de códigos para remover quaisquer anomalias, anomalias e adaptá-la a uma forma para análiserápida rápida.

## <a name="supported-sources"></a>Fontes com suporte

| Conector | Formato de dados | Tipo de autenticação |
| -- | -- | --|
| [Armazenamento de Blobs do Azure](connector-azure-blob-storage.md) | CSV | Chave da Conta |
| [Armazenamento do Azure Data Lake Ger1](connector-azure-data-lake-store.md) | CSV | Principal de Serviço |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) (Armazenamento do Azure Data Lake Gen2) | CSV | Chave de Conta, Diretor de Serviço |
| [Base de Dados SQL do Azure](connector-azure-sql-database.md) | - | Autenticação do SQL |
| [Análise synapse azure](connector-azure-sql-data-warehouse.md) | - | Autenticação do SQL |

## <a name="the-mashup-editor"></a>O editor de mashup

Quando cria um fluxo de dados de sangling, todos os conjuntos de dados de origem tornam-se consultas de conjunto de dados e são colocados na pasta **ADFResource.** Por predefinição, o UserQuery apontará para a consulta do conjunto de dados. Todas as transformações devem ser feitas no UserQuery, uma vez que as alterações às consultas de conjunto de dados não são suportadas nem serão perspercadas. Renomear, adicionar e apagar consultas não é atualmente suportado.

![Distúrbios](media/wrangling-data-flow/editor.png)

Atualmente, nem todas as funções power query M são suportadas para a discussão de dados, apesar de estarem disponíveis durante a autoria. Ao construir os fluxos de dados de sangling, será solicitado com a seguinte mensagem de erro se uma função não for suportada:

`The wrangling data flow is invalid. Expression.Error: The transformation logic isn't supported. Please try a simpler expression`

Para obter mais informações sobre transformações suportadas, consulte [as funções de fluxo](wrangling-data-flow-functions.md)de dados .

Atualmente, o fluxo de dados de suposição apenas suporta a escrita a uma pia.

## <a name="next-steps"></a>Passos seguintes

Aprenda a criar um fluxo de [dados em discussão.](wrangling-data-flow-tutorial.md)
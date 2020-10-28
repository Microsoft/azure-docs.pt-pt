---
title: Fluxos de dados na Fábrica de Dados do Azure
description: Uma visão geral dos fluxos de dados de distúrbios na Azure Data Factory
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: fe3fd25753344c5ccfd21310ae01fca9962e3971
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92636073"
---
# <a name="what-are-wrangling-data-flows"></a>O que estão a mexer nos fluxos de dados?

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]


As organizações precisam de fazer a preparação de dados e a luta para uma análise precisa de dados complexos que continuam a crescer todos os dias. A preparação de dados é necessária para que as organizações possam usar os dados em vários processos de negócio e reduzir o tempo de valor.

Os fluxos de dados de escoaragem na Azure Data Factory permitem-lhe fazer a preparação de dados sem código em iterativas à escala de nuvem. Os fluxos de dados de esticão integram-se com [a Power Query Online](/power-query/) e disponibilizam funções de Power Query M para os utilizadores da fábrica de dados.

O fluxo de dados de wrangling traduz M gerado pelo Power Query Online Mashup Editor em código de faísca para a execução em escala de nuvem.

Os fluxos de dados de estrangulamento são especialmente úteis para engenheiros de dados ou "integradores de dados de cidadãos".

> [!NOTE]
> O fluxo de dados de estrangulamento está atualmente disponível na visualização pública

## <a name="use-cases"></a>Casos de utilização

### <a name="fast-interactive-data-exploration-and-preparation"></a>Rápida exploração e preparação de dados interativos

Vários engenheiros de dados e integradores de dados de cidadãos podem explorar e preparar conjuntos de dados interativamente à escala de nuvem. Com o aumento do volume, variedade e velocidade dos dados em lagos de dados, os utilizadores precisam de uma forma eficaz de explorar e preparar conjuntos de dados. Por exemplo, poderá ser necessário criar um conjunto de dados que "tenha todas as informações demográficas dos clientes para novos clientes desde 2017". Não estás a mapear um alvo conhecido. Estás a explorar, a discutir e a preparar conjuntos de dados para cumprir um requisito antes de o publicares no lago. Os fluxos de dados de estrangulamento são frequentemente usados para cenários de análise menos formais. Os conjuntos de dados preparados podem ser utilizados para fazer transformações e operações de aprendizagem automática a jusante.

### <a name="code-free-agile-data-preparation"></a>Preparação de dados ágeis sem código

Os integradores de dados do cidadão passam mais de 60% do seu tempo à procura e preparação de dados. Querem fazê-lo de forma livre de códigos para melhorar a produtividade operacional. Permitir que os integradores de dados dos cidadãos enriqueçam, moldem e publiquem dados utilizando ferramentas conhecidas como a Power Query Online de uma forma escalável melhora drasticamente a sua produtividade. O fluxo de dados na Azure Data Factory permite ao familiar editor de mashup online power query online permitir que os integradores de dados dos cidadãos corrijam erros rapidamente, padronizem dados e produzam dados de alta qualidade para apoiar decisões empresariais.

### <a name="data-validation"></a>Validação de dados

Digitalize visualmente os seus dados de forma livre de códigos para remover quaisquer anomalias, anomalias e conformá-lo com uma forma de análise rápida.

## <a name="supported-sources"></a>Fontes apoiadas

| Conector | Formato de dados | Tipo de autenticação |
| -- | -- | --|
| [Armazenamento de Blobs do Azure](connector-azure-blob-storage.md) | CSV | Chave de conta |
| [Armazenamento do Azure Data Lake Ger1](connector-azure-data-lake-store.md) | CSV | Principal de Serviço |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) (Armazenamento do Azure Data Lake Gen2) | CSV | Chave de Conta, Diretor de Serviço |
| [Base de Dados SQL do Azure](connector-azure-sql-database.md) | - | Autenticação do SQL |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) | - | Autenticação do SQL |

## <a name="the-mashup-editor"></a>O editor de mashup

Quando cria um fluxo de dados de problemas, todos os conjuntos de dados de origem tornam-se consultas de conjunto de dados e são colocados na pasta **ADFResource.** Por predefinição, o UserQuery apontará para a primeira consulta de conjunto de dados. Todas as transformações devem ser feitas no UserQuery, uma vez que as alterações às consultas de dataset não são suportadas nem serão persistidos. Renomear, adicionar e eliminar consultas não é atualmente suportado.

![Brigas](media/wrangling-data-flow/editor.png)

Atualmente nem todas as funções power query M são suportadas para a luta de dados, apesar de estarem disponíveis durante a autoria. Ao construir os fluxos de dados de luta, será solicitado com a seguinte mensagem de erro se uma função não for suportada:

`The wrangling data flow is invalid. Expression.Error: The transformation logic isn't supported. Please try a simpler expression`

Para obter mais informações sobre as transformações apoiadas, consulte [as funções de fluxo de dados](wrangling-data-flow-functions.md).

Atualmente, o fluxo de dados de luta só suporta escrever para um lavatório.

## <a name="next-steps"></a>Passos seguintes

Saiba como [criar um fluxo de dados de estrangulamento.](wrangling-data-flow-tutorial.md)
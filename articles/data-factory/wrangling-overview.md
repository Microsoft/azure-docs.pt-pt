---
title: Conflito de dados na Azure Data Factory
description: Uma visão geral da discussão de dados na Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/19/2021
ms.openlocfilehash: f922e7a2755a6e26a0d9f93f2668753e2f4dad5a
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98738174"
---
# <a name="what-is-data-wrangling"></a>O que é que os dados estão a discutir?

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

As organizações precisam de explorar os seus dados de negócio críticos para a preparação de dados e a luta, a fim de fornecer uma análise precisa de dados complexos que continuam a crescer todos os dias. A preparação de dados é necessária para que as organizações possam usar os dados em vários processos de negócio e reduzir o tempo de valor.

A Data Factory capacita-o com a preparação de dados sem código em escala de nuvem iterativamente utilizando a Power Query. A Data Factory integra-se com [a Power Query Online](/power-query/) e disponibiliza funções de Power Query M como uma atividade de pipeline.

Data Factory traduz M gerado pelo Power Query Online Mashup Editor em código de faísca para a execução em escala de nuvem, traduzindo M em Azure Data Factory Data Flows. A discussão de dados com a Consulta de Energia e os fluxos de dados são especialmente úteis para engenheiros de dados ou "integradores de dados de cidadãos".

> [!NOTE]
> A atividade de Power Query na Azure Data Factory está atualmente disponível em pré-visualização pública

## <a name="use-cases"></a>Casos de utilização

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4MFkW]

### <a name="fast-interactive-data-exploration-and-preparation"></a>Rápida exploração e preparação de dados interativos

Vários engenheiros de dados e integradores de dados de cidadãos podem explorar e preparar conjuntos de dados interativamente à escala de nuvem. Com o aumento do volume, variedade e velocidade dos dados em lagos de dados, os utilizadores precisam de uma forma eficaz de explorar e preparar conjuntos de dados. Por exemplo, poderá ser necessário criar um conjunto de dados que "tenha todas as informações demográficas dos clientes para novos clientes desde 2017". Não estás a mapear um alvo conhecido. Estás a explorar, a discutir e a preparar conjuntos de dados para cumprir um requisito antes de o publicares no lago. As discussões são frequentemente usadas para cenários de análise menos formais. Os conjuntos de dados preparados podem ser utilizados para fazer transformações e operações de aprendizagem automática a jusante.

### <a name="code-free-agile-data-preparation"></a>Preparação de dados ágeis sem código

Os integradores de dados do cidadão passam mais de 60% do seu tempo à procura e preparação de dados. Querem fazê-lo de forma livre de códigos para melhorar a produtividade operacional. Permitir que os integradores de dados dos cidadãos enriqueçam, moldem e publiquem dados utilizando ferramentas conhecidas como a Power Query Online de uma forma escalável melhora drasticamente a sua produtividade. A discussão na Azure Data Factory permite ao conhecido editor de mashup online power query online permitir que os integradores de dados dos cidadãos corrijam erros rapidamente, padronizem dados e produzam dados de alta qualidade para apoiar decisões empresariais.

### <a name="data-validation-and-exploration"></a>Validação e exploração de dados

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

Quando cria uma atividade de consulta de energia, todos os conjuntos de dados de origem tornam-se consultas de conjunto de dados e são colocados na pasta **ADFResource.** Por predefinição, o UserQuery apontará para a primeira consulta de conjunto de dados. Todas as transformações devem ser feitas no UserQuery, uma vez que as alterações às consultas de dataset não são suportadas nem serão persistidos. Renomear, adicionar e eliminar consultas não é atualmente suportado.

![Brigas](media/wrangling-data-flow/editor.png)

Atualmente nem todas as funções power query M são suportadas para a luta de dados, apesar de estarem disponíveis durante a autoria. Ao construir as suas atividades de Consulta de Energia, será solicitado com a seguinte mensagem de erro se uma função não for suportada:

`The wrangling data flow is invalid. Expression.Error: The transformation logic isn't supported. Please try a simpler expression`

Para obter mais informações sobre as transformações apoiadas, consulte [as funções de conflito de dados](wrangling-functions.md).

## <a name="next-steps"></a>Próximos passos

Saiba como [criar um mash-up de power query](wrangling-tutorial.md)de dados .

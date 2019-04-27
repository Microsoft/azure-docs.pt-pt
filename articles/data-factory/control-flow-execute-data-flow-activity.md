---
title: Executar a atividade de fluxo de dados no Azure Data Factory | Documentos da Microsoft
description: A atividade de fluxo de dados do execute executa fluxos de dados.
services: data-factory
documentationcenter: ''
author: kromerm
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: makromer
ms.openlocfilehash: 856f4bd9c2b04ff10ed598c5e641955e1de99398
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60557605"
---
# <a name="execute-data-flow-activity-in-azure-data-factory"></a>Executar a atividade de fluxo de dados no Azure Data Factory
Utilize a atividade de fluxo de dados de execução para executar o fluxo de dados do ADF nas execuções do pipeline debug (área de segurança) e nas execuções de pipeline acionada.

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

## <a name="syntax"></a>Sintaxe

```json
{
    "name": "MyDataFlowActivity",
    "type": "ExecuteDataFlow",
    "typeProperties": {
      "dataflow": {
         "referenceName": "dataflow1",
         "type": "DataFlowReference"
      },
        "compute": {
          "computeType": "General",
          "coreCount": 8,
      }
}

```

## <a name="type-properties"></a>Propriedades do tipo

* ```dataflow``` é o nome da entidade de fluxo de dados que deseja executar
* ```compute``` Descreve o ambiente de execução do Spark
* ```coreCount``` é o número de núcleos para atribuir a esta execução de atividade do seu fluxo de dados

![Executar fluxo de dados](media/data-flow/activity-data-flow.png "executar fluxo de dados")

### <a name="run-on"></a>Executar em

Escolha o ambiente de computação para esta execução de seu fluxo de dados. A predefinição é o Runtime de integração do padrão de resolução automática do Azure. Esta opção irá executar o fluxo de dados no ambiente do Spark na mesma região que a fábrica de dados. O tipo de computação será um cluster de tarefa, o que significa que o ambiente de computação demorará alguns minutos para inicialização.

### <a name="debugging-pipelines-with-data-flows"></a>Depurar pipelines com os fluxos de dados

![Botão de depuração](media/data-flow/debugbutton.png "botão de depuração")

Utilize os dados de fluxo de depuração para utilizar um cluster warmed para testar os fluxos de dados interativamente numa depuração de pipeline ser executada. Utilize a opção de depuração Pipleine para testar os fluxos de dados dentro de um pipeline.

### <a name="compute-type"></a>Tipo de computação

Pode escolher para fins gerais, otimizadas para computação ou com otimização de memória, consoante os requisitos do seu fluxo de dados.

### <a name="core-count"></a>Contagem de núcleos

Escolha quantos núcleos que pretende atribuir ao trabalho. Para tarefas mais pequenas, menos núcleos funcionará melhor.

### <a name="staging-area"></a>Área de transição

Se sair está afundando até os dados no armazém de dados do Azure, tem de escolher uma localização de transição para a sua carga de batch do Polybase.

## <a name="parameterized-datasets"></a>Conjuntos de dados parametrizados

Se estiver a utilizar conjuntos de dados parametrizados, certifique-se de que defina os valores de parâmetro.

![Executar os parâmetros de fluxo de dados](media/data-flow/params.png "parâmetros")

### <a name="debugging-parameterized-data-flows"></a>Depuração parametrizado fluxos de dados

Só é possível depurar fluxos de dados com conjuntos de dados parametrizados do Pipeline Debug executado com a atividade de fluxo de dados de execução. Atualmente, sessões de depuração interativa no fluxo de dados do ADF não funcionam com conjuntos de dados parametrizados. Execuções de pipeline e execuções de depuração funcionará com parâmetros.

## <a name="next-steps"></a>Passos Seguintes
Consulte outras atividades de fluxo de controle suportadas pelo Data Factory: 

- [Atividade Se Condição](control-flow-if-condition-activity.md)
- [Atividade Executar Pipeline](control-flow-execute-pipeline-activity.md)
- [Para cada atividade](control-flow-for-each-activity.md)
- [Atividade Obter Metadados](control-flow-get-metadata-activity.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)
- [Atividade Web](control-flow-web-activity.md)
- [Atividade Until](control-flow-until-activity.md)

---
title: Executar a atividade de fluxo de dados no Azure Data Factory | Documentos da Microsoft
description: Como executar os dados fluem de dentro de um pipeline de fábrica de dados.
services: data-factory
documentationcenter: ''
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: makromer
ms.openlocfilehash: 24b27c16573a35b1d8749d7ff381fbef970f4bd0
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/29/2019
ms.locfileid: "67471665"
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

### <a name="debugging-pipelines-with-data-flows"></a>Depurar pipelines com os fluxos de dados

![Botão de depuração](media/data-flow/debugbutton.png "botão de depuração")

Utilize os dados de fluxo de depuração para utilizar um cluster warmed para testar os fluxos de dados interativamente numa depuração de pipeline ser executada. Utilize a opção de Pipeline de depuração para testar os fluxos de dados dentro de um pipeline.

### <a name="run-on"></a>Executar em

Este é um campo obrigatório que define quais Runtime de integração a utilizar para a execução de atividade de fluxo de dados. Por predefinição, o Data Factory irá utilizar o runtime de integração do Azure de resolução automática de predefinição. No entanto, pode criar seu próprio Runtimes de integração do Azure que definir regiões específicas, tipo e valor de TTL contagens de núcleos de computação para a execução de atividade de fluxo de dados.

A definição de execuções de fluxo de dados predefinida é de 8 núcleos de computação geral com um valor de TTL de 60 minutos.

Escolha o ambiente de computação para esta execução de seu fluxo de dados. A predefinição é o Runtime de integração do padrão de resolução automática do Azure. Esta opção irá executar o fluxo de dados no ambiente do Spark na mesma região que a fábrica de dados. O tipo de computação será um cluster de tarefa, o que significa que o ambiente de computação demorará alguns minutos para inicialização.

Tem controlo sobre o ambiente de execução do Spark para suas atividades de fluxo de dados. Na [runtime de integração do Azure](concepts-integration-runtime.md) se as definições para definir o tipo de computação (fins gerais, otimizada de memória e com otimização de computação), o número de núcleos do trabalho e time-to-live de acordo com o motor de execução com a computação de fluxo de dados requisitos. Além disso, definir o valor de TTL permitirá manter um cluster de acesso pouco frequente que já está disponível para execuções de tarefas.

![Runtime de integração do Azure](media/data-flow/ir-new.png "Runtime de integração do Azure")

> [!NOTE]
> A seleção de Runtime de integração na atividade de fluxo de dados só se aplica a *acionadas execuções* do seu pipeline. Depurar o seu pipeline com os dados fluem com depuração será executado no cluster do Spark de predefinição de 8 núcleos.

### <a name="staging-area"></a>Área de transição

Se sair está afundando até os dados no armazém de dados do Azure, tem de escolher uma localização de transição para a sua carga de batch do Polybase. A definição de teste apenas é aplicável a cargas de trabalho do armazém de dados do Azure.

## <a name="parameterized-datasets"></a>Conjuntos de dados parametrizados

Se estiver a utilizar conjuntos de dados parametrizados, certifique-se de que defina os valores de parâmetro.

![Executar os parâmetros de fluxo de dados](media/data-flow/params.png "parâmetros")

## <a name="parameterized-data-flows"></a>Fluxos de dados parametrizado

Se tiver parâmetros dentro do seu fluxo de dados, defina os valores dinâmicos dos seus parâmetros de fluxo de dados aqui na secção parâmetros da atividade de executar o fluxo de dados. Pode utilizar a linguagem de expressão de Pipeline do ADF (apenas para tipos de parâmetro de cadeia de caracteres) ou a linguagem de expressão de fluxo de dados para definir os valores de parâmetros com expressões dinâmicos ou literais valores estáticos.

![Executar o exemplo de parâmetro de fluxo de dados](media/data-flow/parameter-example.png "exemplo de parâmetro")

### <a name="debugging-data-flows-with-parameters"></a>Fluxos de dados de depuração com parâmetros

Neste momento, pode depurar apenas fluxos de dados com parâmetros do Pipeline Debug executado com a atividade de fluxo de dados de execução. Sessões de depuração interativa no fluxo de dados do ADF estará disponível brevemente. Execuções de pipeline e execuções de depuração, no entanto, irão funcionar com parâmetros.

É uma boa prática criar o seu fluxo de dados com conteúdo estático para que tenha a propagação de coluna de metadados completo disponível no momento da conceção para trouble-shooting. Em seguida, substitua o conjunto de dados estático com um conjunto de dados parametrizado dinâmico quando operacionalizar o seu pipeline de fluxo de dados.

## <a name="next-steps"></a>Passos Seguintes
Consulte outras atividades de fluxo de controle suportadas pelo Data Factory: 

- [Atividade Se Condição](control-flow-if-condition-activity.md)
- [Atividade Executar Pipeline](control-flow-execute-pipeline-activity.md)
- [Para cada atividade](control-flow-for-each-activity.md)
- [Atividade Obter Metadados](control-flow-get-metadata-activity.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)
- [Atividade Web](control-flow-web-activity.md)
- [Atividade Until](control-flow-until-activity.md)

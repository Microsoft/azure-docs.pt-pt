---
title: Consumir Azure Machine Learning eventos
titleSuffix: Azure Machine Learning
description: Neste artigo, saiba como usar a grade de eventos do Azure para assinar, reagir e cancelar a assinatura de eventos gerados pelo Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: andyxu
author: gogowings
ms.date: 11/04/2019
ms.openlocfilehash: 2fe2d07b29b8799712d59cdf21aeb3ce989ca3b2
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158473"
---
# <a name="consume-azure-machine-learning-events-preview"></a>Consumir eventos de Azure Machine Learning (visualização)

O Azure Machine Learning gerencia todo o ciclo de vida do processo de aprendizado de máquina, incluindo treinamento de modelo, implantação de modelo e monitoramento. Azure Machine Learning eventos permitem que os aplicativos reajam a eventos durante o ciclo de vida do Machine Learning, como a conclusão de execuções de treinamento, o registro e a implantação de modelos e a detecção de descompasso de dados, usando o moderno sem servidor arquiteturas. 

Esses eventos são publicados por meio da [grade de eventos do Azure](https://azure.microsoft.com/services/event-grid/). Usando portal do Azure, PowerShell ou CLI do Azure, os clientes podem assinar eventos facilmente [especificando um ou mais tipos de eventos e condições de filtragem](/azure/event-grid/event-filtering). Os clientes também têm a opção de criar uma ampla gama de manipuladores de eventos, como Azure Functions, aplicativos lógicos do Azure ou WebHooks genéricos. Azure Machine Learning, juntamente com a grade de eventos do Azure, fornece uma plataforma de entrega de eventos altamente disponível, confiável e tolerante a falhas para criar aplicativos orientados a eventos.

Para obter informações sobre como usar Azure Machine Learning com a grade de eventos, consulte [criar fluxos de trabalho de Machine Learning controlado por eventos (versão prévia)](how-to-use-event-grid.md).

## <a name="the-event-model"></a>O modelo de evento 

A grade de eventos do Azure lê eventos de fontes, como Azure Machine Learning e outros serviços do Azure. Esses eventos são enviados para manipuladores de eventos, como hubs de eventos do Azure, Azure Functions, aplicativos lógicos e outros. O diagrama a seguir mostra como a grade de eventos conecta fontes e manipuladores, mas não é uma lista abrangente de integrações com suporte.

![Modelo funcional da grade de eventos do Azure](./media/concept-event-grid-integration/azure-event-grid-functional-model.png)

Para obter mais informações sobre fontes de eventos e manipuladores de eventos, consulte [o que é a grade de eventos?](/azure/event-grid/overview).

## <a name="azure-machine-learning-event-types"></a>Azure Machine Learning tipos de evento

Azure Machine Learning fornece eventos nos vários pontos do ciclo de vida do Machine Learning: 

| Tipo de evento | Descrição |
| ---------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | Gerado quando uma execução do experimento do Machine Learning é concluída |
| `Microsoft.MachineLearningServices.ModelRegistered` | Gerado quando um modelo de aprendizado de máquina é registrado no espaço de trabalho |
| `Microsoft.MachineLearningServices.ModelDeployed` | Gerado quando uma implantação do serviço de inferência com um ou mais modelos é concluída |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | Gerado quando um trabalho de detecção de descompasso de dados para dois conjuntos de dados é concluído |

## <a name="subscribe-to-machine-learning-events"></a>Assinar eventos de Machine Learning

As assinaturas para eventos de Azure Machine Learning são protegidas pelo RBAC (controle de acesso baseado em função). Somente o [colaborador ou o proprietário](how-to-assign-roles.md#default-roles) de um espaço de trabalho pode criar, atualizar e excluir assinaturas de evento.

As assinaturas de evento podem ser filtradas com base em uma variedade de condições. Os filtros podem ser aplicados a assinaturas de evento durante a [criação](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) da assinatura de evento ou [em um momento posterior](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest). 

### <a name="filter-by-event-type"></a>Filtrar por tipo de evento
Uma assinatura de evento pode especificar um ou mais tipos de evento Azure Machine Learning.

### <a name="filter-by-event-subject"></a>Filtrar por assunto do evento
A grade de eventos do Azure dá suporte a filtros de assunto com base em __começa com__ e __termina com__ as correspondências, para que os eventos com um assunto correspondente sejam entregues ao Assinante. Diferentes eventos de aprendizado de máquina têm um formato de assunto diferente.

| Tipo de evento | Formato da entidade | Assunto de exemplo |
| ---------- | ----------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` |
| `Microsoft.MachineLearningServices.ModelRegistered` | `models/{modelName}:{modelVersion}` | `models/sklearn_regression_model:3` |
| `Microsoft.MachineLearningServices.ModelDeployed` | `endpoints/{serviceId}` | `endpoints/my_sklearn_aks` |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | `datadrift/{data.DataDriftId}/run/{data.RunId}` | `datadrift/4e694bf5-712e-4e40-b06a-d2a2755212d4/run/my_driftrun1_1550564444_fbbcdc0f` |

### <a name="advanced-filtering"></a>Filtragem avançada

A grade de eventos do Azure também dá suporte à filtragem avançada com base no esquema de evento publicado. Azure Machine Learning detalhes do esquema de eventos podem ser encontrados no [esquema de evento da grade de eventos do Azure para Azure Machine Learning](../../event-grid/event-schema-machine-learning.md).

Alguns filtros avançados de exemplo que você pode executar incluem:

* Para `Microsoft.MachineLearningServices.ModelRegistered` evento, para filtrar o valor da marca do modelo:

    ```
    --advanced-filter data.ModelTags.key1 StringIn ('value1')
    ```

Para saber mais sobre como aplicar filtros, consulte [filtrar eventos para a grade de eventos](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

## <a name="consume-machine-learning-events"></a>Consumir Machine Learning eventos

Os aplicativos que manipulam Machine Learning eventos devem seguir algumas práticas recomendadas:

> [!div class="checklist"]
> * Como várias assinaturas podem ser configuradas para rotear eventos para o mesmo manipulador de eventos, é importante não pressupor que os eventos sejam de uma fonte específica, mas para verificar o tópico da mensagem a fim de garantir que ele venha do espaço de trabalho do Machine Learning que você espera.
> * Da mesma forma, verifique se o eventType é um que você está preparado para processar e não presuma que todos os eventos recebidos serão os tipos esperados.
> * Como as mensagens podem chegar fora de ordem e depois de algum atraso, use os campos ETag para entender se suas informações sobre objetos ainda estão atualizadas.  Além disso, use os campos do Sequencer para entender a ordem dos eventos em qualquer objeto específico.
> * Ignore os campos que você não entende. Essa prática ajudará a mantê-lo resiliente a novos recursos que podem ser adicionados no futuro.
> * As operações de Azure Machine Learning com falha ou canceladas não dispararão um evento. Por exemplo, se uma implantação de modelo falhar, o Microsoft. MachineLearningServices. ModelDeployed não será disparado. Considere esse modo de falha ao projetar seus aplicativos. Você sempre pode usar Azure Machine Learning SDK, CLI ou portal para verificar o status de uma operação e entender os motivos de falha detalhados.

A grade de eventos do Azure permite que os clientes criem manipuladores de mensagens desacopladas, que podem ser disparados por Azure Machine Learning eventos. Alguns exemplos notáveis de manipuladores de mensagens são:
* Funções do Azure
* Azure Logic Apps
* Azure Event Hubs
* Pipeline de Azure Data Factory
* WebHooks genéricos, que podem ser hospedados na plataforma do Azure ou em outro lugar

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a grade de eventos e dê uma tentativa de Azure Machine Learning eventos:

- [Sobre o Event Grid](../../event-grid/overview.md)
- [Esquema de evento da grade de eventos do Azure para Azure Machine Learning](../../event-grid/event-schema-machine-learning.md)
- [Criar fluxos de trabalho orientados a eventos com Azure Machine Learning](how-to-use-event-grid.md)

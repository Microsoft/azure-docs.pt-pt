---
title: Consumir eventos de aprendizagem automática azure
titleSuffix: Azure Machine Learning
description: Neste artigo, aprenda a usar a Grelha de Eventos Azure para subscrever, reagir e cancelar a subscrição de eventos gerados pela Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: andyxu
author: gogowings
ms.date: 11/04/2019
ms.openlocfilehash: d8a975487c68a21b2c8b6fa2f07d86c312243f12
ms.sourcegitcommit: d322d0a9d9479dbd473eae239c43707ac2c77a77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2020
ms.locfileid: "79139050"
---
# <a name="consume-azure-machine-learning-events-preview"></a>Consumir eventos de aprendizagem automática azure (pré-visualização)

O Azure Machine Learning gere todo o ciclo de vida do processo de aprendizagem automática, incluindo formação de modelos, implantação de modelos e monitorização. Os eventos de Aprendizagem Automática Azure permitem que as aplicações reajam a eventos durante o ciclo de vida da aprendizagem automática, tais como a conclusão de ensaios, o registo e implementação de modelos, e a deteção de deriva de dados, utilizando o servidor moderno arquiteturas. 

Estes eventos são publicados através da [Azure Event Grid.](https://azure.microsoft.com/services/event-grid/) Utilizando o portal Azure, Powershell ou Azure CLI, os clientes podem facilmente subscrever eventos [especificando um ou mais tipos de eventos e condições de filtragem](/azure/event-grid/event-filtering). Os clientes também têm escolha para construir uma vasta gama de manipuladores de eventos, tais como Funções Azure, Aplicações Lógicas Azure ou webhooks genéricos. O Azure Machine Learning, juntamente com a Azure Event Grid, oferece uma plataforma de entrega de eventos de alto nível disponível, fiável e tolerante a falhas para que possa construir aplicações orientadas para eventos.

Para obter informações sobre a utilização de Machine Learning Azure com grelha de eventos, consulte [Create Event Driven Machine Learning Workflows (Preview)](how-to-use-event-grid.md).

## <a name="the-event-model"></a>O modelo do evento 

A Azure Event Grid lê eventos de fontes, como o Azure Machine Learning e outros serviços Azure. Estes eventos são então enviados para manipuladores de eventos como Azure Event Hubs, Funções Azure, Aplicações Lógicas, entre outros. O diagrama seguinte mostra como a Grelha de Eventos liga fontes e manipuladores, mas não é uma lista completa de integrações apoiadas.

![Modelo funcional da Grelha de Eventos Azure](./media/concept-event-grid-integration/azure-event-grid-functional-model.png)

Para obter mais informações sobre fontes de eventos e manipuladores de eventos, consulte [o que é a Grelha de Eventos?](/azure/event-grid/overview)

## <a name="azure-machine-learning-event-types"></a>Tipos de eventos de aprendizagem automática azure

O Azure Machine Learning proporciona eventos nos vários pontos do ciclo de vida da aprendizagem automática: 

| Tipo de evento | Descrição |
| ---------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | Criado quando uma experiência de aprendizagem automática é concluída |
| `Microsoft.MachineLearningServices.ModelRegistered` | Criado quando um modelo de aprendizagem automática é registado no espaço de trabalho |
| `Microsoft.MachineLearningServices.ModelDeployed` | Levantado quando uma implantação de serviço de inferência com um ou mais modelos é concluída |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | Levantado quando um trabalho de deteção de deriva de dados para dois conjuntos de dados é concluído |
| `Microsoft.MachineLearningServices.RunStatusChanged` | Aumentado quando um estado de execução mudou, atualmente só aumentado quando um estado de execução é "falhado" |

## <a name="subscribe-to-machine-learning-events"></a>Subscreva eventos de Aprendizagem Automática

As subscrições para eventos de Machine Learning Azure estão protegidas por controlo de acesso baseado em funções (RBAC). Apenas [o colaborador ou proprietário](how-to-assign-roles.md#default-roles) de um espaço de trabalho pode criar, atualizar e excluir subscrições de eventos.

As subscrições de eventos podem ser filtradas com base numa variedade de condições. Os filtros podem ser aplicados a subscrições de eventos durante a [criação](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) da subscrição do evento ou [posteriormente.](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) 

### <a name="filter-by-event-type"></a>Filtro por tipo de evento
Uma subscrição de evento pode especificar um ou mais tipos de eventos de Aprendizagem automática de Azure.

### <a name="filter-by-event-subject"></a>Filtrar por assunto de evento
A Azure Event Grid suporta filtros de matérias com base em __partidas e__ __termina com__ jogos, para que os eventos com um assunto correspondente sejam entregues ao assinante. Diferentes eventos de aprendizagem automática têm formato de assunto diferente.

| Tipo de evento | Formato de assunto | Cobaia |
| ---------- | ----------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` |
| `Microsoft.MachineLearningServices.ModelRegistered` | `models/{modelName}:{modelVersion}` | `models/sklearn_regression_model:3` |
| `Microsoft.MachineLearningServices.ModelDeployed` | `endpoints/{serviceId}` | `endpoints/my_sklearn_aks` |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | `datadrift/{data.DataDriftId}/run/{data.RunId}` | `datadrift/4e694bf5-712e-4e40-b06a-d2a2755212d4/run/my_driftrun1_1550564444_fbbcdc0f` |
| `Microsoft.MachineLearningServices.RunStatusChanged` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` | 

### <a name="advanced-filtering"></a>Filtragem avançada

A Azure Event Grid também suporta uma filtragem avançada com base no esquema do evento publicado. Os detalhes do evento Azure Machine Learning podem ser encontrados no esquema do [evento Azure Event Grid para o Azure Machine Learning.](../event-grid/event-schema-machine-learning.md)

Algumas filtrações avançadas de amostra saem:

* Para `Microsoft.MachineLearningServices.ModelRegistered` evento, para filtrar o valor de etiqueta do modelo:

    ```
    --advanced-filter data.ModelTags.key1 StringIn ('value1')
    ```

Para saber mais sobre como aplicar filtros, consulte [os eventos de filtragem para a Grelha de Eventos](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

## <a name="consume-machine-learning-events"></a>Consumir eventos de aprendizagem automática

As aplicações que lidam com eventos de Machine Learning devem seguir algumas práticas recomendadas:

> [!div class="checklist"]
> * Como várias subscrições podem ser configuradas para direcionar eventos para o mesmo manipulador de eventos, é importante não assumir que os eventos são de uma determinada fonte, mas para verificar o tópico da mensagem para garantir que ele vem do espaço de trabalho de aprendizagem automática que você está esperando.
> * Da mesma forma, verifique se o eventoType é um que está preparado para processar, e não assuma que todos os eventos que receber serão os tipos que espera.
> * Como as mensagens podem chegar fora de ordem e depois de algum atraso, use os campos de etag para entender se a sua informação sobre objetos ainda está atualizada.  Além disso, utilize os campos de sequenciadores para entender a ordem dos acontecimentos em qualquer objeto em particular.
> * Ignore campos que não entende. Esta prática ajudará a mantê-lo resiliente a novas funcionalidades que poderão ser adicionadas no futuro.
> * As operações de Aprendizagem automática azure falhadas ou canceladas não desencadearão um evento. Por exemplo, se uma implementação de um modelo falhar na Microsoft.MachineLearningServices.ModelDeployed não será acionado. Considere tal modo de falha ao projetar as suas aplicações. Pode sempre utilizar O SDK, CLI ou portal de Aprendizagem automática Azure para verificar o estado de uma operação e compreender as razões de falha detalhadas.

A Azure Event Grid permite que os clientes construam manipuladores de mensagens dissociados, que podem ser desencadeados por eventos de Machine Learning Azure. Alguns exemplos notáveis de manipuladores de mensagens são:
* Funções do Azure
* Azure Logic Apps
* Azure Event Hubs
* Gasoduto azure data factory
* Webhooks genéricos, que podem ser hospedados na plataforma Azure ou em qualquer outro lugar

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a Grelha de Eventos e dê aos eventos de Aprendizagem automática Azure uma tentativa:

- [Sobre o Event Grid](../event-grid/overview.md)
- [Esquema de evento de azure event para azure machine learning](../event-grid/event-schema-machine-learning.md)
- [Criar fluxos de trabalho orientados para eventos com a Azure Machine Learning](how-to-use-event-grid.md)

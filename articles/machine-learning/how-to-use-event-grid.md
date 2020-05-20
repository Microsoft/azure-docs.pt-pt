---
title: Desencadear eventos em fluxos de trabalho ml
titleSuffix: Azure Machine Learning
description: Configurar aplicações, processos ou fluxos de trabalho de aprendizagem automática ci/CD em Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: shipatel
author: shivp950
ms.reviewer: larryfr
ms.date: 03/11/2020
ms.openlocfilehash: 79bea443ea576ba0c17c1a8150c305d7cd2320ff
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83655793"
---
# <a name="trigger-applications-processes-or-cicd-workflows-based-on-azure-machine-learning-events-preview"></a>Aplicações, processos ou fluxos de trabalho CI/CD baseados em eventos de Aprendizagem Automática Azure (Pré-visualização)

Neste artigo, aprende-se a configurar aplicações, processos ou fluxos de trabalho ci/CD baseados em eventos de Aprendizagem automática do Azure, tais como e-mails de notificação de falha ou pipeline ML, quando certas condições são detetadas pela [Azure Event Grid.](https://docs.microsoft.com/azure/event-grid/) 

O Azure Machine Learning gere todo o ciclo de vida do processo de aprendizagem automática, incluindo formação de modelos, implantação de modelos e monitorização. Você pode usar a Grelha de Eventos para reagir a eventos de Aprendizagem automática Azure, como a conclusão de corridas de formação, o registo e implementação de modelos, e a deteção de deriva de dados, utilizando arquiteturas modernas sem servidores. Em seguida, pode subscrever e consumir eventos como o estado de execução alterado, a conclusão da execução, o registo do modelo, a implementação do modelo e a deteção de deriva de dados dentro de um espaço de trabalho.

Quando utilizar a Grelha de Eventos para ações orientadas por eventos:
* Envie e-mails sobre falha de execução e conclusão de execução
* Utilize uma função Azure depois de um modelo ser registado
* Eventos de streaming de Azure Machine Learning para vários pontos finais
* Desencadear um oleoduto ML quando a deriva for detetada

> [!NOTE] 
> Atualmente, os eventos runStatusChange só disparam quando o estado de execução é **falhado**

## <a name="prerequisites"></a>Pré-requisitos
Para utilizar a Rede de Eventos, necessita de acesso ao espaço de trabalho Azure Machine Learning para o que irá criar eventos.

## <a name="the-event-model--types"></a>Os tipos de & do modelo de evento

A Azure Event Grid lê eventos de fontes, como o Azure Machine Learning e outros serviços Azure. Estes eventos são então enviados para manipuladores de eventos como Azure Event Hubs, Funções Azure, Aplicações Lógicas, entre outros. O diagrama seguinte mostra como a Grelha de Eventos liga fontes e manipuladores, mas não é uma lista completa de integrações apoiadas.

![Modelo funcional da Grelha de Eventos Azure](./media/concept-event-grid-integration/azure-event-grid-functional-model.png)

Para obter mais informações sobre fontes de eventos e manipuladores de eventos, consulte [o que é a Grelha de Eventos?](/azure/event-grid/overview)

### <a name="event-types-for-azure-machine-learning"></a>Tipos de eventos para Aprendizagem automática Azure

O Azure Machine Learning proporciona eventos nos vários pontos do ciclo de vida da aprendizagem automática: 

| Tipo de evento | Descrição |
| ---------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | Criado quando uma experiência de aprendizagem automática é concluída |
| `Microsoft.MachineLearningServices.ModelRegistered` | Criado quando um modelo de aprendizagem automática é registado no espaço de trabalho |
| `Microsoft.MachineLearningServices.ModelDeployed` | Levantado quando uma implantação de serviço de inferência com um ou mais modelos é concluída |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | Levantado quando um trabalho de deteção de deriva de dados para dois conjuntos de dados é concluído |
| `Microsoft.MachineLearningServices.RunStatusChanged` | Aumentado quando um estado de execução mudou, atualmente só aumentado quando um estado de execução é "falhado" |

### <a name="filter--subscribe-to-events"></a>Filtrar & subscrever eventos

Estes eventos são publicados através da Azure Event Grid. Utilizando o portal Azure, PowerShell ou Azure CLI, os clientes podem facilmente subscrever eventos [especificando um ou mais tipos de eventos e condições de filtragem.](/azure/event-grid/event-filtering) 

Ao configurar os seus eventos, pode aplicar filtros apenas para acionar dados específicos do evento. No exemplo abaixo, para o estado de execução alterado eventos, pode filtrar por tipos de execução. O evento só dispara quando os critérios são cumpridos. Consulte o esquema da grelha de [eventos azure machine learning](/azure/event-grid/event-schema-machine-learning) para saber sobre os dados do evento que pode filtrar. 

As subscrições para eventos de Machine Learning Azure estão protegidas por controlo de acesso baseado em funções (RBAC). Apenas [o colaborador ou proprietário](how-to-assign-roles.md#default-roles) de um espaço de trabalho pode criar, atualizar e excluir subscrições de eventos.  Os filtros podem ser aplicados a subscrições de eventos durante a [criação](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) da subscrição do evento ou posteriormente. 


1. Vá ao portal Azure, selecione uma nova subscrição ou uma existente. 

1. Selecione o separador de filtros e desloque-se para filtros avançados. Para obter a **Chave** e **o Valor,** forneça os tipos de propriedade que pretende filtrar. Aqui você pode ver que o evento só será acionado quando o tipo de execução é um pipeline run ou pipeline step run.  

    :::image type="content" source="media/how-to-use-event-grid/select-event-filters.png" alt-text="eventos de filtro":::


+ **Filtrar por tipo de evento:** Uma subscrição de evento pode especificar um ou mais tipos de eventos de Aprendizagem automática de Azure.

+ **Filtrar por assunto do evento:** A Azure Event Grid suporta filtros de matérias com base em __partidas e__ __termina com__ jogos, para que os eventos com um assunto correspondente sejam entregues ao assinante. Diferentes eventos de aprendizagem automática têm formato de assunto diferente.

  | Tipo de evento | Formato de assunto | Cobaia |
  | ---------- | ----------- | ----------- |
  | `Microsoft.MachineLearningServices.RunCompleted` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` |
  | `Microsoft.MachineLearningServices.ModelRegistered` | `models/{modelName}:{modelVersion}` | `models/sklearn_regression_model:3` |
  | `Microsoft.MachineLearningServices.ModelDeployed` | `endpoints/{serviceId}` | `endpoints/my_sklearn_aks` |
  | `Microsoft.MachineLearningServices.DatasetDriftDetected` | `datadrift/{data.DataDriftId}/run/{data.RunId}` | `datadrift/4e694bf5-712e-4e40-b06a-d2a2755212d4/run/my_driftrun1_1550564444_fbbcdc0f` |
  | `Microsoft.MachineLearningServices.RunStatusChanged` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` | 

+ **Filtragem avançada**: Azure Event Grid também suporta filtragem avançada com base em esquemade evento publicado. Os detalhes do evento Azure Machine Learning podem ser encontrados no esquema do [evento Azure Event Grid para o Azure Machine Learning.](../event-grid/event-schema-machine-learning.md)  Algumas filtrações avançadas de amostra saem:

  Para `Microsoft.MachineLearningServices.ModelRegistered` o evento, para filtrar o valor de etiqueta do modelo:

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

## <a name="set-up-in-azure-portal"></a>Configuração no portal Azure

1. Abra o [portal Azure](https://portal.azure.com) e vá ao seu espaço de trabalho azure machine learning.

1. A partir da barra esquerda, selecione __Eventos__ e, em seguida, selecione Assinaturas de **Eventos**. 

    ![select-events-in-workspace.png](./media/how-to-use-event-grid/select-event.png)

1. Selecione o tipo de evento para consumir. Por exemplo, a seguinte imagem selecionou __Modelo registado,__ __Modelo implementado,__ __Executar concluído__e __deriva dataset detetada:__

    ![add-event-type](./media/how-to-use-event-grid/add-event-type-updated.png)

1. Selecione o ponto final para publicar o evento para. Na seguinte imagem, o hub do __Evento__ é o ponto final selecionado:

    ![select-event-handler](./media/how-to-use-event-grid/select-event-handler.png)

Depois de confirmar a sua seleção, clique em __Criar__. Após a configuração, estes eventos serão empurrados para o seu ponto final.


### <a name="set-up-with-the-cli"></a>Configurar com o CLI

Pode instalar o mais recente [Azure CLI,](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)ou utilizar a Shell Azure Cloud que é fornecida como parte da sua subscrição Azure.

Para instalar a extensão da Grelha de Eventos, utilize o seguinte comando do CLI:

```azurecli-interactive
az add extension --name eventgrid
```

O exemplo que se segue demonstra como selecionar uma subscrição azure e cria e uma nova subscrição de eventos para O Machine Learning Do Azure:

```azurecli-interactive
# Select the Azure subscription that contains the workspace
az account set --subscription "<name or ID of the subscription>"

# Subscribe to the machine learning workspace.
az eventgrid event-subscription create \
  --name {eventGridFilterName} \
  --source-resource-id "/subscriptions/{subId}/resourceGroups/{rgName}/ \providers/Microsoft.MachineLearningServices/workspaces/{wsName}" \
  --endpoint {event handler endpoint} \
  --included-event-types Microsoft.MachineLearningServices.ModelRegistered \
  --subject-begins-with "models/mymodelname"
```

## <a name="examples"></a>Exemplos

### <a name="example-send-email-alerts"></a>Exemplo: Enviar alertas de e-mail

Utilize [aplicativos azure logic](https://docs.microsoft.com/azure/logic-apps/) para configurar e-mails para todos os seus eventos. Personalize com condições e especifique destinatários para permitir a colaboração e a consciencialização entre as equipas que trabalham em conjunto.

1. No portal Azure, vá ao seu espaço de trabalho Azure Machine Learning e selecione o separador de eventos a partir da barra esquerda. A partir daqui, selecione __aplicativos Lógicos.__ 

    ![select-logic-ap](./media/how-to-use-event-grid/select-logic-ap.png)

1. Inscreva-se na Aplicação Lógica UI e selecione o serviço de Machine Learning como o tipo de tópico. 

    ![tipo tópico selecionado](./media/how-to-use-event-grid/select-topic-type.png)

1. Selecione para que evento s deve ser notificado. Por exemplo, a seguinte imagem __RunCompleted__.

    ![select-event-runcomplete](./media/how-to-use-event-grid/select-event-runcomplete.png)

1. Pode utilizar o método de filtragem na secção acima ou adicionar filtros apenas para acionar a aplicação lógica num subconjunto de tipos de eventos. Na seguinte imagem, é utilizado um __filtro prefixo__ de __/datadriftID/runs/__

    ![eventos de filtro](./media/how-to-use-event-grid/filtering-events.png)

1. Em seguida, adicione um passo para consumir este evento e procure por e-mail. Existem várias contas de correio diferentes que pode usar para receber eventos. Também pode configurar as condições sobre quando enviar um alerta de e-mail.

    ![selecionar e-mail-ação](./media/how-to-use-event-grid/select-email-action.png)

1. Selecione __Enviar um e-mail__ e preencher os parâmetros. No assunto, pode incluir o Tipo de __Evento__ e __Tópico__ para ajudar a filtrar eventos. Também pode incluir um link para a página do espaço de trabalho para execuções no corpo da mensagem. 

    ![configurar-e-mail-corpo](./media/how-to-use-event-grid/configure-email-body.png)

1. Para salvar esta ação, selecione **Save As** no canto esquerdo da página. A partir da barra certa que aparece, confirme a criação desta ação.

    ![confirmar-lógica-app-criar](./media/how-to-use-event-grid/confirm-logic-app-create.png)


### <a name="example-data-drift-triggers-retraining"></a>Exemplo: Deriva de dados desencadeia reconversão

Os modelos ficam velhos ao longo do tempo, e não permanecem úteis no contexto em que está a funcionar. Uma maneira de saber se está na hora de retreinar o modelo é detetar a deriva de dados. 

Este exemplo mostra como usar a grelha de eventos com uma App Lógica Azure para desencadear a reconversão. O exemplo desencadeia um oleoduto Azure Data Factory quando ocorre deriva de dados entre a formação de um modelo e o serviço de conjuntos de dados.

Antes de começar, execute as seguintes ações:

* Criar um monitor de conjunto de dados para detetar a [deriva de dados]( https://aka.ms/datadrift) num espaço de trabalho
* Crie um [oleoduto azure data factory](https://docs.microsoft.com/azure/data-factory/)publicado.

Neste exemplo, um simples pipeline data factory é usado para copiar ficheiros numa loja de blob e executar um pipeline de Machine Learning publicado. Para mais informações sobre este cenário, consulte como configurar um passo de [Machine Learning na Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-machine-learning-service)

![adf-mlpipeline-estágio](./media/how-to-use-event-grid/adf-mlpipeline-stage.png)

1. Comece por criar a aplicação lógica. Vá ao [portal Azure,](https://portal.azure.com)procure Por Apps Lógicas e selecione criar.

    ![app lógica de pesquisa](./media/how-to-use-event-grid/search-for-logic-app.png)

1. Preencha as informações solicitadas. Para simplificar a experiência, utilize o mesmo grupo de subscrição e recursos que o seu Pipeline azure Data Factory e o espaço de trabalho Azure Machine Learning.

    ![configuração-lógica-app-for-adf](./media/how-to-use-event-grid/set-up-logic-app-for-adf.png)

1. Depois de ter criado a aplicação lógica, selecione Quando ocorre um evento de __recurso da Rede de Eventos__. 

    ![select-event-grid-trigger](./media/how-to-use-event-grid/select-event-grid-trigger.png)

1. Faça login e preencha os detalhes do evento. Dete teo Nome de __Recurso__ para o nome do espaço de trabalho. Desloque o __Tipo de Evento__ para __DatasetDrift Detetado__.

    ![login-and-add-event](./media/how-to-use-event-grid/login-and-add-event.png)

1. Adicione um novo passo e procure a __Azure Data Factory.__ Selecione __Criar uma execução__de gasoduto . 

    ![criar-adfpipeline-run](./media/how-to-use-event-grid/create-adfpipeline-run.png)

1. Faça login e especifique o oleoduto azure data factory publicado para funcionar.

    ![especificar-adf-pipeline](./media/how-to-use-event-grid/specify-adf-pipeline.png)

1. Guarde e crie a aplicação lógica utilizando o botão **de salvamento** na parte superior esquerda da página. Para ver a sua aplicação, vá ao seu espaço de trabalho no [portal Azure](https://portal.azure.com) e clique em **Eventos**.

    ![show-logic-app-webhook](./media/how-to-use-event-grid/show-logic-app-webhook.png)

Agora, o gasoduto da fábrica de dados é acionado quando ocorre deriva. Veja detalhes sobre a corrida à deriva de dados e o pipeline de machine learning no [novo portal do espaço de trabalho](https://ml.azure.com). 

![espaço de visão no trabalho](./media/how-to-use-event-grid/view-in-workspace.png)

### <a name="example-deploy-a-model-based-on-tags"></a>Exemplo: Implementar um modelo baseado em etiquetas

Um objeto modelo de Aprendizagem automática Azure contém parâmetros que pode girar implementações em nome, versão, etiqueta e propriedade do modelo. O evento de registo do modelo pode desencadear um ponto final e pode utilizar uma Função Azure para implementar um modelo baseado no valor desses parâmetros.

Por exemplo, consulte o [https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid](https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid) repositório e siga os passos no ficheiro **readme.**

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a Grelha de Eventos e dê aos eventos de Aprendizagem automática Azure uma tentativa:

- [Sobre o Event Grid](../event-grid/overview.md)

- [Esquema do evento para aprendizagem automática azure](../event-grid/event-schema-machine-learning.md)


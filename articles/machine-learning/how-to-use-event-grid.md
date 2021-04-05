---
title: Desencadear eventos em fluxos de trabalho ML (pré-visualização)
titleSuffix: Azure Machine Learning
description: Configurar aplicações, processos ou fluxos de trabalho de aprendizagem automática de máquinas de CI/CD em Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: shipatel
author: shivp950
ms.reviewer: larryfr
ms.date: 05/11/2020
ms.openlocfilehash: 9dc7c0a476f57048c7caefb6001918d314c991ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102209701"
---
# <a name="trigger-applications-processes-or-cicd-workflows-based-on-azure-machine-learning-events-preview"></a>Ativar aplicações, processos ou fluxos de trabalho ci/CD baseados em eventos de Aprendizagem automática Azure (pré-visualização)

Neste artigo, aprende a configurar aplicações, processos ou fluxos de trabalho de CI/CD baseados em eventos de Aprendizagem automática Azure, tais como e-mails de notificação de falhas ou gasoduto ML, quando determinadas condições são detetadas pela [Azure Event Grid](../event-grid/index.yml).

A Azure Machine Learning gere todo o ciclo de vida do processo de aprendizagem automática, incluindo formação de modelos, implementação de modelos e monitorização. Você pode usar a Grade de Eventos para reagir a eventos de Azure Machine Learning, como a conclusão de corridas de treino, o registo e implementação de modelos, e a deteção de deriva de dados, utilizando arquiteturas modernas sem servidor. Em seguida, pode subscrever e consumir eventos como alterar o estado de execução, executar a conclusão, o registo do modelo, a implementação do modelo e a deteção de deriva de dados dentro de um espaço de trabalho.

Quando utilizar a Grade de Eventos para ações orientadas para eventos:
* Enviar e-mails em falha de execução e executar conclusão
* Utilize uma função Azure depois de registado um modelo
* Eventos de streaming de Azure Machine Learning para vários pontos finais
* Desencadeie um gasoduto ML quando a deriva for detetada

## <a name="prerequisites"></a>Pré-requisitos
Para utilizar a Grade de Eventos, precisa de acesso do colaborador ou proprietário ao espaço de trabalho Azure Machine Learning para o que irá criar eventos.

## <a name="the-event-model--types"></a>O modelo de evento & tipos

A Azure Event Grid lê eventos de fontes, como Azure Machine Learning e outros serviços Azure. Estes eventos são então enviados para manipuladores de eventos como Azure Event Hubs, Azure Functions, Logic Apps, entre outros. O diagrama seguinte mostra como a Grade de Eventos conecta fontes e manipuladores, mas não é uma lista completa de integrações apoiadas.

![Modelo funcional da Grelha de Eventos Azure](./media/concept-event-grid-integration/azure-event-grid-functional-model.png)

Para obter mais informações sobre fontes de eventos e manipuladores de eventos, consulte [o que é a Grade de Eventos?](../event-grid/overview.md)

### <a name="event-types-for-azure-machine-learning"></a>Tipos de eventos para Azure Machine Learning

A Azure Machine Learning proporciona eventos nos vários pontos do ciclo de vida da aprendizagem automática: 

| Tipo de evento | Descrição |
| ---------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | Criado quando uma experiência de aprendizagem automática é concluída |
| `Microsoft.MachineLearningServices.ModelRegistered` | Criado quando um modelo de aprendizagem automática é registado no espaço de trabalho |
| `Microsoft.MachineLearningServices.ModelDeployed` | Levantado quando uma implantação do serviço de inferência com um ou mais modelos é concluída |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | Levantado quando um trabalho de deteção de deriva de dados para dois conjuntos de dados é concluído |
| `Microsoft.MachineLearningServices.RunStatusChanged` | Levantado quando um estado de execução mudou, atualmente apenas aumentado quando um estado de execução é 'falhado' |

### <a name="filter--subscribe-to-events"></a>Filter & subscrever eventos

Estes eventos são publicados através da Azure Event Grid. Utilizando o portal Azure, PowerShell ou Azure CLI, os clientes podem subscrever facilmente eventos [especificando um ou mais tipos de eventos e condições de filtragem](../event-grid/event-filtering.md). 

Ao configurar os seus eventos, pode aplicar filtros apenas para acionar dados específicos do evento. No exemplo abaixo, para eventos alterados de estado de execução, pode filtrar por tipos de execução. O evento só dispara quando os critérios são cumpridos. Consulte o esquema de grelha de [eventos Azure Machine Learning](../event-grid/event-schema-machine-learning.md) para saber mais sobre os dados do evento que pode filtrar. 

As subscrições de eventos de aprendizagem automática Azure estão protegidas pelo controlo de acesso baseado em funções Azure (Azure RBAC). Apenas [o colaborador ou proprietário](how-to-assign-roles.md#default-roles) de um espaço de trabalho pode criar, atualizar e eliminar subscrições de eventos.  Os filtros podem ser aplicados a subscrições de eventos durante a [criação](/cli/azure/eventgrid/event-subscription) da subscrição do evento ou posteriormente. 


1. Vá ao portal Azure, selecione uma nova subscrição ou uma existente. 

1. Selecione o separador filtros e desloque-se para os filtros Avançados. Para a **Chave** e **Valor,** forneça os tipos de propriedade por que deseja filtrar. Aqui pode ver que o evento só dispara quando o tipo de corrida é uma corrida de gasoduto ou um passo de gasoduto.  

    :::image type="content" source="media/how-to-use-event-grid/select-event-filters.png" alt-text="eventos de filtro":::


+ **Filtro por tipo de evento:** Uma subscrição de eventos pode especificar um ou mais tipos de eventos Azure Machine Learning.

+ **Filtrar por objeto do evento:** A Azure Event Grid suporta filtros de assunto baseados em __começa__ e __termina com fósforos,__ de modo que os eventos com um sujeito correspondente são entregues ao assinante. Diferentes eventos de aprendizagem automática têm formato de assunto diferente.

  | Tipo de evento | Formato de assunto | Sujeito de amostra |
  | ---------- | ----------- | ----------- |
  | `Microsoft.MachineLearningServices.RunCompleted` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` |
  | `Microsoft.MachineLearningServices.ModelRegistered` | `models/{modelName}:{modelVersion}` | `models/sklearn_regression_model:3` |
  | `Microsoft.MachineLearningServices.ModelDeployed` | `endpoints/{serviceId}` | `endpoints/my_sklearn_aks` |
  | `Microsoft.MachineLearningServices.DatasetDriftDetected` | `datadrift/{data.DataDriftId}/run/{data.RunId}` | `datadrift/4e694bf5-712e-4e40-b06a-d2a2755212d4/run/my_driftrun1_1550564444_fbbcdc0f` |
  | `Microsoft.MachineLearningServices.RunStatusChanged` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` | 

+ **Filtragem avançada**: A Azure Event Grid também suporta filtragem avançada com base em esquemas de eventos publicados. Os detalhes do esquema do esquema do evento Azure Machine Learning podem ser encontrados no [esquema de eventos Azure Event Grid para Azure Machine Learning](../event-grid/event-schema-machine-learning.md).  Algumas amostras de filtragem avançadas que pode realizar incluem:

  Para `Microsoft.MachineLearningServices.ModelRegistered` o evento, filtrar o valor da etiqueta do modelo:

  ```
  --advanced-filter data.ModelTags.key1 StringIn ('value1')
  ```

  Para saber mais sobre como aplicar filtros, consulte [eventos de filtro para grelha de eventos.](../event-grid/how-to-filter-events.md)

## <a name="consume-machine-learning-events"></a>Consumir eventos de aprendizagem automática

As aplicações que lidam com eventos de Machine Learning devem seguir algumas práticas recomendadas:

> [!div class="checklist"]
> * Como várias subscrições podem ser configuradas para encaminhar eventos para o mesmo manipulador de eventos, é importante não assumir que os eventos são de uma determinada fonte, mas para verificar o tópico da mensagem para garantir que ele vem do espaço de trabalho de machine learning que você está esperando.
> * Da mesma forma, verifique se o eventType é aquele que está preparado para processar, e não assuma que todos os eventos que receber serão os tipos que espera.
> * Como as mensagens podem chegar fora de ordem e depois de algum atraso, use os campos etag para entender se as suas informações sobre objetos ainda estão atualizadas.  Além disso, use os campos de sequenciadores para entender a ordem dos eventos em qualquer objeto em particular.
> * Ignore campos que não entende. Esta prática irá ajudá-lo a manter-se resiliente a novas funcionalidades que poderão ser adicionadas no futuro.
> * As operações falhadas ou canceladas de Aprendizagem automática do Azure não desencadearão um evento. Por exemplo, se uma implementação de modelo falhar microsoft.MachineLearningServices.ModelDeployed não será acionado. Considere este modo de falha ao conceber as suas aplicações. Pode sempre utilizar o Azure Machine Learning SDK, CLI ou portal para verificar o estado de uma operação e compreender as razões de falha detalhadas.

A Azure Event Grid permite que os clientes construam manipuladores de mensagens desacoplados, que podem ser desencadeados por eventos de Aprendizagem automática Azure. Alguns exemplos notáveis de manipuladores de mensagens são:
* Funções do Azure
* Azure Logic Apps
* Azure Event Hubs
* Gasoduto da Fábrica de Dados Azure
* Webhooks genéricos, que podem ser hospedados na plataforma Azure ou em qualquer outro lugar

## <a name="set-up-in-azure-portal"></a>Instale-se no portal Azure

1. Abra o [portal Azure](https://portal.azure.com) e vá ao seu espaço de trabalho Azure Machine Learning.

1. A partir da barra esquerda, selecione __Eventos__ e, em seguida, selecione **Subscrições de Eventos**. 

    ![select-events-in-workspace.png](./media/how-to-use-event-grid/select-event.png)

1. Selecione o tipo de evento para consumir. Por exemplo, a imagem que se segue selecionou __o Modelo registado,__ __o Modelo implementado,__ __o Run concluído__ e a deriva do Conjunto __de Dados detetada:__

    ![tipo de evento adicionar](./media/how-to-use-event-grid/add-event-type-updated.png)

1. Selecione o ponto final para publicar o evento para. Na seguinte imagem, o __centro de eventos__ é o ponto final selecionado:

    ![A screenshot mostra o painel de subscrição de eventos Create com Select Event Hub aberto.](./media/how-to-use-event-grid/select-event-handler.png)

Uma vez confirmada a sua seleção, clique em __Criar__. Após a configuração, estes eventos serão empurrados para o seu ponto final.


### <a name="set-up-with-the-cli"></a>Configurar com o CLI

Pode instalar o mais recente [Azure CLI,](/cli/azure/install-azure-cli)ou utilizar o Azure Cloud Shell que é fornecido como parte da sua subscrição Azure.

Para instalar a extensão 'Grade de Eventos', utilize o seguinte comando a partir do CLI:

```azurecli-interactive
az add extension --name eventgrid
```

O exemplo a seguir demonstra como selecionar uma subscrição do Azure e cria uma nova subscrição de eventos para a Azure Machine Learning:

```azurecli-interactive
# Select the Azure subscription that contains the workspace
az account set --subscription "<name or ID of the subscription>"

# Subscribe to the machine learning workspace. This example uses EventHub as a destination. 
az eventgrid event-subscription create --name {eventGridFilterName} \
  --source-resource-id /subscriptions/{subId}/resourceGroups/{RG}/providers/Microsoft.MachineLearningServices/workspaces/{wsName} \
  --endpoint-type eventhub \
  --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.EventHub/namespaces/n1/eventhubs/EH1 \
  --included-event-types Microsoft.MachineLearningServices.ModelRegistered \
  --subject-begins-with "models/mymodelname"
```

## <a name="examples"></a>Exemplos

### <a name="example-send-email-alerts"></a>Exemplo: Enviar alertas de e-mail

Use [aplicativos Azure Logic](../logic-apps/index.yml) para configurar e-mails para todos os seus eventos. Personalize com condições e especifique os destinatários para permitir a colaboração e a consciencialização entre as equipas que trabalham em conjunto.

1. No portal Azure, vá ao seu espaço de trabalho Azure Machine Learning e selecione o separador eventos a partir da barra esquerda. A partir daqui, selecione __aplicativos Logic__. 

    ![A screenshot mostra uma página de eventos de espaço de trabalho machine learning com aplicações lógicas.](./media/how-to-use-event-grid/select-logic-ap.png)

1. Inscreva-se na UI da Aplicação Lógica e selecione o serviço de Machine Learning como o tipo de tópico. 

    ![A screenshot mostra o Quando ocorre uma caixa de diálogo de recurso com machine learning selecionada como tipo de recurso.](./media/how-to-use-event-grid/select-topic-type.png)

1. Selecione para que eventos a notificar. Por exemplo, a seguinte imagem __RunCompleted__.

    ![A screenshot mostra o Quando ocorre uma caixa de diálogo de recurso com um tipo de evento selecionado.](./media/how-to-use-event-grid/select-event-runcomplete.png)

1. Pode utilizar o método de filtragem na secção acima ou adicionar filtros apenas para ativar a aplicação lógica num subconjunto de tipos de eventos. Na imagem seguinte, é utilizado um __filtro prefixo__ de __/datadriftID/runs/__

    ![filtrar eventos](./media/how-to-use-event-grid/filtering-events.png)

1. Em seguida, adicione um passo para consumir este evento e procure por e-mail. Existem várias contas de correio diferentes que pode usar para receber eventos. Também pode configurar condições para quando enviar um alerta de e-mail.

    ![A screenshot mostra a caixa de diálogo de ação Escolha com o e-mail introduzido na linha de pesquisa.](./media/how-to-use-event-grid/select-email-action.png)

1. Selecione __Enviar um e-mail__ e preencher os parâmetros. No assunto, pode incluir o Tipo de __Evento__ e __Tópico__ para ajudar a filtrar eventos. Também pode incluir um link para a página do espaço de trabalho para execuções no corpo da mensagem. 

    ![Screenshot mostra a caixa de diálogo de e-mail enviar uma caixa de diálogo de e-mail com tópico e tipo de evento adicionado à linha de assunto da lista para a direita.](./media/how-to-use-event-grid/configure-email-body.png)

1. Para guardar esta ação, **selecione Save As** no canto esquerdo da página. Do bar direito que aparece, confirme a criação desta ação.

    ![A screenshot mostra os botões Save As e Create no Logic Apps Designer.](./media/how-to-use-event-grid/confirm-logic-app-create.png)


### <a name="example-data-drift-triggers-retraining"></a>Exemplo: Deriva de dados desencadeia reconversão

Os modelos ficam velhos ao longo do tempo, e não permanecem úteis no contexto em que está a decorrer. Uma maneira de dizer se está na hora de retreinar o modelo é detetar a deriva de dados. 

Este exemplo mostra como usar a grelha de eventos com uma App Azure Logic para desencadear a reconversão. O exemplo desencadeia um oleoduto Azure Data Factory quando ocorre uma deriva de dados entre a formação de um modelo e a servir conjuntos de dados.

Antes de começar, execute as seguintes ações:

* Crie um monitor de conjunto de dados para detetar a [deriva de dados](how-to-monitor-datasets.md) num espaço de trabalho
* Criar um [oleoduto Azure Data Factory](../data-factory/index.yml)publicado.

Neste exemplo, um simples oleoduto Data Factory é usado para copiar ficheiros numa loja de bolhas e executar um pipeline de Machine Learning publicado. Para obter mais informações sobre este cenário, veja como configurar um passo de [Machine Learning na Azure Data Factory](../data-factory/transform-data-machine-learning-service.md)

![A screenshot mostra o pipeline de formação em recursos de fábrica com dados de cópia1 alimentando M L Execute Pipeline1.](./media/how-to-use-event-grid/adf-mlpipeline-stage.png)

1. Comece com a criação da aplicação lógica. Vá ao [portal Azure,](https://portal.azure.com)procure por Aplicações Lógicas e selecione create.

    ![pesquisa lógica-app](./media/how-to-use-event-grid/search-for-logic-app.png)

1. Preencha a informação solicitada. Para simplificar a experiência, utilize o mesmo grupo de subscrição e recursos que o seu Pipeline de Fábrica de Dados Azure e o espaço de trabalho Azure Machine Learning.

    ![A screenshot mostra o painel de criação de aplicativos lógicos.](./media/how-to-use-event-grid/set-up-logic-app-for-adf.png)

1. Uma vez criado a aplicação lógica, selecione __Quando ocorrer um evento de recursos de Grade de Eventos__. 

    ![O Screenshot mostra o Design de Aplicações Lógicas com Início com opções comuns de gatilho, incluindo quando ocorre um evento de recursos de Grade de Eventos.](./media/how-to-use-event-grid/select-event-grid-trigger.png)

1. Faça login e preencha os detalhes do evento. Desaprote o __nome de recurso__ para o nome do espaço de trabalho. Defina o __tipo de evento__ para __datasetDriftDetected__.

    ![A screenshot mostra o quando ocorre um evento de recurso com um item tipo de evento selecionado.](./media/how-to-use-event-grid/login-and-add-event.png)

1. Adicione um novo passo e procure __a Azure Data Factory.__ __Selecione Criar uma corrida de gasodutos__. 

    ![A screenshot mostra o painel de ação Escolha um painel de ação com Criar uma corrida de gasoduto selecionada.](./media/how-to-use-event-grid/create-adfpipeline-run.png)

1. Faça login e especifique o pipeline Azure Data Factory publicado para executar.

    ![A screenshot mostra o painel de conduta Create com vários valores.](./media/how-to-use-event-grid/specify-adf-pipeline.png)

1. Guarde e crie a aplicação lógica utilizando o botão **de poupança** no topo esquerdo da página. Para ver a sua aplicação, vá ao seu espaço de trabalho no [portal Azure](https://portal.azure.com) e clique em **Eventos.**

    ![A Screenshot mostra eventos com a Aplicação Lógica em destaque.](./media/how-to-use-event-grid/show-logic-app-webhook.png)

Agora, o gasoduto da fábrica de dados é acionado quando ocorre a deriva. Veja detalhes sobre a sua deriva de dados e o pipeline de aprendizagem automática no [novo portal do espaço de trabalho.](https://ml.azure.com) 

![A screenshot mostra pontos finais do gasoduto.](./media/how-to-use-event-grid/view-in-workspace.png)

### <a name="example-deploy-a-model-based-on-tags"></a>Exemplo: Implementar um modelo baseado em etiquetas

Um objeto modelo Azure Machine Learning contém parâmetros que pode pivô implementações em tais como nome de modelo, versão, etiqueta e propriedade. O evento de registo do modelo pode desencadear um ponto final e pode utilizar uma Função Azure para implementar um modelo baseado no valor desses parâmetros.

Por exemplo, consulte o [https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid](https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid) repositório e siga os passos no ficheiro **readme.**

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a Grade de Eventos e experimente os eventos de Machine Learning da Azure:

- [Sobre o Event Grid](../event-grid/overview.md)

- [Esquema de evento para Azure Machine Learning](../event-grid/event-schema-machine-learning.md)

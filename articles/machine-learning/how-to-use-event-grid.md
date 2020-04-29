---
title: Criar fluxos de trabalho de aprendizagem automática orientados para eventos
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar a grelha de eventos com o Azure Machine Learning para permitir soluções orientadas para o evento.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: shipatel
author: shivp950
ms.reviewer: larryfr
ms.date: 03/11/2020
ms.openlocfilehash: 2a1440dcda27a487c89be4ac63e624a2bb6b393a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82111883"
---
# <a name="create-event-driven-machine-learning-workflows-preview"></a>Criar fluxos de trabalho de aprendizagem automática orientados para eventos (Pré-visualização)

[A Azure Event Grid](https://docs.microsoft.com/azure/event-grid/) apoia eventos de Machine Learning Azure. Pode subscrever e consumir eventos como o estado de execução alterado, a conclusão da execução, o registo do modelo, a implementação do modelo e a deteção de deriva de dados dentro de um espaço de trabalho.

Para obter mais informações sobre os tipos de eventos, consulte a [integração do Azure Machine Learning com a Event Grid](concept-event-grid-integration.md) e o esquema de grelha de [eventos azure machine learning.](/azure/event-grid/event-schema-machine-learning)

Utilize a Grelha de Eventos para permitir cenários comuns como:

* Envie e-mails sobre falha de execução e conclusão de execução
* Utilize uma função Azure depois de um modelo ser registado
* Eventos de streaming de Azure Machine Learning para vários pontos finais
* Desencadear um oleoduto ML quando a deriva for detetada

> [!NOTE] 
> Atualmente, os eventos runStatusChange só disparam quando o estado de execução é **falhado**
>

## <a name="prerequisites"></a>Pré-requisitos
* O acesso do colaborador ou proprietário ao espaço de trabalho Azure Machine Learning irá criar eventos para.

### <a name="configure-eventgrid-using-the-azure-portal"></a>Configure EventGrid utilizando o portal Azure

1. Abra o [portal Azure](https://portal.azure.com) e vá ao seu espaço de trabalho azure machine learning.

1. A partir da barra esquerda, selecione __Eventos__ e, em seguida, selecione Assinaturas de **Eventos**. 

    ![select-events-in-workspace.png](./media/how-to-use-event-grid/select-event.png)

1. Selecione o tipo de evento para consumir. Por exemplo, a seguinte imagem selecionou __Modelo registado,__ __Modelo implementado,__ __Executar concluído__e __deriva dataset detetada:__

    ![add-event-type](./media/how-to-use-event-grid/add-event-type-updated.png)

1. Selecione o ponto final para publicar o evento para. Na seguinte imagem, o hub do __Evento__ é o ponto final selecionado:

    ![select-event-handler](./media/how-to-use-event-grid/select-event-handler.png)

Depois de confirmar a sua seleção, clique em __Criar__. Após a configuração, estes eventos serão empurrados para o seu ponto final.


### <a name="configure-eventgrid-using-the-cli"></a>Configure EventGrid utilizando o CLI

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

## <a name="filter-events"></a>Eventos de filtro

Ao configurar os seus eventos, pode aplicar filtros apenas para acionar dados específicos do evento. No exemplo abaixo, para o estado de execução alterado eventos, pode filtrar por tipos de execução. O evento só dispara quando os critérios são cumpridos. Consulte o esquema da grelha de [eventos azure machine learning](/azure/event-grid/event-schema-machine-learning) para saber sobre os dados do evento que pode filtrar. 

1. Vá ao portal Azure, selecione uma nova subscrição ou uma existente. 

1. Selecione o separador de filtros e desloque-se para filtros avançados. Para obter a **Chave** e **o Valor,** forneça os tipos de propriedade que pretende filtrar. Aqui você pode ver que o evento só será acionado quando o tipo de execução é um pipeline run ou pipeline step run.  

    :::image type="content" source="media/how-to-use-event-grid/select-event-filters.png" alt-text="eventos de filtro":::

## <a name="sample-send-email-alerts"></a>Amostra: Enviar alertas de e-mail

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


## <a name="sample-trigger-retraining-when-data-drift-occurs"></a>Amostra: Reconversão do gatilho quando ocorrer deriva de dados

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

## <a name="sample-deploy-a-model-based-on-tags"></a>Amostra: Implementar um modelo com base em etiquetas

Um objeto modelo de Aprendizagem automática Azure contém parâmetros que pode girar implementações em nome, versão, etiqueta e propriedade do modelo. O evento de registo do modelo pode desencadear um ponto final e pode utilizar uma Função Azure para implementar um modelo baseado no valor desses parâmetros.

Por exemplo, consulte [https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid](https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid) o repositório e siga os passos no ficheiro **readme.**

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre os eventos disponíveis, consulte o [evento Azure Machine Learning schema](/azure/event-grid/event-schema-machine-learning)

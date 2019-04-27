---
title: Cenário sem servidor - criar um dashboard de informações de cliente com os serviços do Azure | Documentos da Microsoft
description: Gerir comentários dos clientes, dados de mídia social e muito mais com a criação de um dashboard de cliente com o Azure Logic Apps e as funções do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: jeffhollan
ms.author: jehollan
ms.reviewer: estfan, LADocs
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.topic: article
ms.date: 03/15/2018
ms.openlocfilehash: 638b29dd2a15d0467c41e20ecfed9f333b34c04d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60508030"
---
# <a name="create-streaming-customer-insights-dashboard-with-azure-logic-apps-and-azure-functions"></a>Criar o dashboard de conhecimentos dos clientes de transmissão em fluxo com o Azure Logic Apps e as funções do Azure

O Azure oferece [sem servidor](https://azure.microsoft.com/solutions/serverless/) ferramentas que ajudam a rapidamente criar e alojar aplicações na cloud, sem ter que pensar sobre a infraestrutura. Neste tutorial, pode criar um dashboard que aciona nos comentários dos clientes, analisa o feedback com o machine learning e publica informações a uma origem, como o Power BI ou o Azure Data Lake.

Para esta solução, de usar esses componentes-chave do Azure para aplicações sem servidor: [As funções do Azure](https://azure.microsoft.com/services/functions/) e [do Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/).
O Azure Logic Apps fornecem um mecanismo de fluxo de trabalho sem servidor na cloud, para que possa criar orquestrações em componentes sem servidor e ligue a mais de 200 serviços e APIs. As funções do Azure fornece computação sem servidor na cloud. Esta solução utiliza as funções do Azure para sinalizar tweets de cliente com base em palavras-chave predefinidas.

Neste cenário, vai criar uma aplicação lógica que aciona sobre como encontrar os comentários dos clientes. Alguns conectores que ajuda a responder aos comentários dos clientes incluem Outlook.com, Office 365, pesquisa Monkey, Twitter e um [pedido HTTP de um web form](https://blogs.msdn.microsoft.com/logicapps/2017/01/30/calling-a-logic-app-from-an-html-form/). O fluxo de trabalho que criar monitoriza uma hashtag no Twitter.

Pode [compilar a solução completa no Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) e [implantar a solução com o modelo Azure Resource Manager](../logic-apps/logic-apps-create-deploy-template.md). Para instruções em vídeo que mostra como criar esta solução [Assista a este vídeo do Channel 9](https://aka.ms/logicappsdemo). 

## <a name="trigger-on-customer-data"></a>Acionamento por dados dos clientes

1. O portal do Azure ou o Visual Studio, crie uma aplicação lógica em branco. 

   Se estiver familiarizado com aplicações lógicas, reveja os [início rápido do portal do Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md) ou o [início rápido para Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

2. No Estruturador da aplicação lógica, localize e adicione o acionador do Twitter com esta ação: **Quando um novo tweet é publicado**

3. Configure o acionador para escutar os tweets com base numa palavra-chave ou um hashtag.

   Em acionadores baseados em consulta, por exemplo, o acionador do Twitter, a propriedade de periodicidade determina a frequência com que a aplicação lógica verifica novos itens.

   ![Exemplo de Acionador do Twitter][1]

Esta aplicação lógica agora é acionado em todos os novos tweets. Em seguida, pode tirar e analisar os dados de tweet para que possa entender melhor os sentimentos expressados. 

## <a name="analyze-tweet-text"></a>Analisar o texto do tweet

Para detetar o sentimento por trás de algum texto, pode usar [serviços cognitivos do Azure](https://azure.microsoft.com/services/cognitive-services/).

1. No Estruturador da aplicação lógica, no acionador, escolha **novo passo**.

2. Encontrar o **análise de texto** conector.

3. Selecione o **detetar sentimento** ação.

4. Se lhe for pedido, forneça uma chave de serviços cognitivos válida para o serviço de análise de texto.

5. Sob **corpo do pedido**, selecione a **texto do Tweet** campo, que fornece o texto do tweet como entrada para análise.

Depois de obter dados de tweet e informações sobre o tweet, agora, pode utilizar vários outros conectores relevantes e respetivas ações:

* **Power BI – Adicione linhas ao conjunto de dados de transmissão em fluxo**: Ver os tweets recebidos no dashboard do Power BI.
* **O Azure Data Lake - acrescentar ficheiro**: Adicione dados de cliente a um conjunto de dados do Azure Data Lake para incluir nas tarefas de análise.
* **SQL - adicionar linhas**: Store dados numa base de dados para recuperação posterior.
* **Slack – enviar mensagem**: Notifique um canal Slack sobre comentários negativos que possam exigir ação.

Também pode criar e do Azure funcionar, de forma a que pode efetuar o processamento personalizado nos seus dados. 

## <a name="process-data-with-azure-functions"></a>Processar dados com as funções do Azure

Antes de criar uma função, crie uma aplicação de funções na sua subscrição do Azure. Além disso, para a sua aplicação lógica chamar diretamente uma função, a função tem de ter um HTTP acionar a ligação, por exemplo, utilize o **HttpTrigger** modelo. Saiba mais [como criar sua primeira aplicação de função e a função no portal do Azure](../azure-functions/functions-create-first-azure-function-azure-portal.md).

Para este cenário, utilize o texto do tweet como o corpo do pedido para a função do Azure. No código da função, defina a lógica que determina se o texto do tweet contém uma palavra-chave ou uma expressão. Manter a função como simples ou complexo quanto for necessário para o cenário.
No final da função, devolver uma resposta para a aplicação lógica com alguns dados, por exemplo, um simples valor booleano como `containsKeyword` ou um objeto complexo.

> [!TIP]
> Para aceder a uma resposta complexa de uma função numa aplicação lógica, utilize o **Parse JSON** ação.

Quando tiver terminado, guarde a função e, em seguida, adicione a função como uma ação na aplicação lógica que está a criar.

## <a name="add-azure-function-to-logic-app"></a>Adicionar função do Azure à aplicação lógica

1. No Estruturador da aplicação lógica, sob o **detetar sentimento** ação, escolha **novo passo**.

2. Encontrar o **as funções do Azure** conector e, em seguida, selecione a função que criou.

3. Sob **corpo do pedido**, selecione **texto do Tweet**.

![Passo de função do Azure configurado][2]

## <a name="run-and-monitor-your-logic-app"></a>Executar e monitorizar a sua aplicação lógica

Para rever todas as execuções atuais ou anteriores para a aplicação lógica, pode utilizar a avançada de depuração e capacidades do Azure Logic Apps fornece no portal do Azure, o Visual Studio, ou através de APIs REST do Azure e SDKs de monitorização.

Para testar facilmente a sua aplicação lógica, no Estruturador da aplicação lógica, escolha **executar acionador**. O acionador consulta tweets com base na agenda especificada, até que seja encontrado um tweet que atende a seus critérios. Durante o andamento da execução, o estruturador mostra uma visão em direto para essa execução.

Para a vista anterior históricos de execuções no Visual Studio ou o portal do Azure: 

* Abra o Cloud Explorer do Visual Studio. Encontre a sua aplicação lógica, abra o menu de atalho da aplicação. Selecione **histórico de execuções de Open**.

* No portal do Azure, encontre a aplicação lógica. No menu da sua aplicação lógica, escolha **descrição geral**. 

## <a name="create-automated-deployment-templates"></a>Criar modelos de implementação automatizada

Depois de criar uma solução de aplicação lógica, pode capturar e implementar a sua aplicação como uma [modelo Azure Resource Manager](../azure-resource-manager/resource-group-overview.md#template-deployment) para qualquer região do Azure do mundo. Pode utilizar esta capacidade de modificar os parâmetros para a criação de versões diferentes da sua aplicação e para integrar a sua solução em Pipelines do Azure. Também pode incluir as funções do Azure no seu modelo de implementação, para que possa gerir a solução completa com todas as dependências como um único modelo. Saiba mais [como criar modelos de implementação de aplicação de lógica](../logic-apps/logic-apps-create-deploy-template.md).

Para um modelo de implementação de exemplo com uma função do Azure, consulte a [repositório de modelos de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-function-app-create-dynamic).

## <a name="next-steps"></a>Passos Seguintes

* [Encontre outros exemplos e cenários para o Azure Logic Apps](logic-apps-examples-and-scenarios.md)

<!-- Image References -->
[1]: ./media/logic-apps-scenario-social-serverless/twitter.png
[2]: ./media/logic-apps-scenario-social-serverless/function.png

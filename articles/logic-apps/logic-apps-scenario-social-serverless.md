---
title: Criar painel de insights para o cliente
description: Gerir o feedback do cliente, dados das redes sociais e muito mais construindo um dashboard de clientes com Apps Azure Logic e Funções Azure
services: logic-apps
ms.suite: integration
author: jeffhollan
ms.author: jehollan
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 03/15/2018
ms.openlocfilehash: 22e0c7304f7a53a86bc5c6739a2061352d738d29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98784817"
---
# <a name="create-a-streaming-customer-insights-dashboard-with-azure-logic-apps-and-azure-functions"></a>Crie um dashboard de informação de clientes em streaming com apps Azure Logic e Funções Azure

O Azure oferece ferramentas [sem servidor](https://azure.microsoft.com/solutions/serverless/) que o ajudam a construir e hospedar rapidamente aplicações na nuvem, sem ter de pensar em infraestruturas. Neste tutorial, pode criar um dashboard que desencadeie o feedback do cliente, analise feedback com machine learning e publique insights para uma fonte, como Power BI ou Azure Data Lake.

Para esta solução, utilize estes componentes Azure chave para aplicações sem servidor: [Funções Azure](https://azure.microsoft.com/services/functions/) e [Apps Azure Logic](https://azure.microsoft.com/services/logic-apps/).
A Azure Logic Apps fornece um motor de fluxo de trabalho sem servidor na nuvem para que possa criar orquestrações através de componentes sem servidor e ligar-se a mais de 200 serviços e APIs. As Funções Azure fornecem computação sem servidor na nuvem. Esta solução utiliza funções Azure para sinalizar tweets de clientes com base em palavras-chave predefinidas.

Neste cenário, cria-se uma aplicação lógica que desencadeia a procura de feedback dos clientes. Alguns conectores que o ajudam a responder ao feedback do cliente incluem Outlook.com, Office 365, Survey Monkey, Twitter, e um [pedido HTTP de um formulário web.](/archive/blogs/logicapps/calling-a-logic-app-from-an-html-form) O fluxo de trabalho que cria monitoriza uma hashtag no Twitter.

Pode [construir toda a solução no Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) e [implementar a solução com o modelo Azure Resource Manager.](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md) Para um vídeo que mostra como criar esta solução, [veja este vídeo do Canal 9](https://aka.ms/logicappsdemo). 

## <a name="trigger-on-customer-data"></a>Gatilho nos dados do cliente

1. No portal Azure ou Visual Studio, crie uma aplicação lógica em branco. 

   Se é novo em aplicações lógicas, reveja o [quickstart para o portal Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md) ou o [quickstart para Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

2. In Logic App Designer, encontre e adicione o gatilho do Twitter que tem esta ação: **Quando um novo tweet é publicado**

3. Configurar o gatilho para ouvir tweets com base numa palavra-chave ou hashtag.

   Nos gatilhos baseados nas sondagens, como o gatilho do Twitter, a propriedade de recorrência determina com que frequência a aplicação lógica verifica novos itens.

   ![Exemplo do gatilho do Twitter][1]

Esta aplicação lógica agora dispara em todos os novos tweets. Em seguida, pode pegar e analisar os dados do tweet para que possa entender melhor os sentimentos expressos. 

## <a name="analyze-tweet-text"></a>Analisar texto do tweet

Para detetar o sentimento por trás de algum texto, pode utilizar [os Serviços Cognitivos Azure.](https://azure.microsoft.com/services/cognitive-services/)

1. Na Logic App Designer, sob o gatilho, escolha **novo passo.**

2. Encontre o conector **Text Analytics.**

3. Selecione a ação **Detetar sentimento.**

4. Se solicitado, forneça uma chave de Serviços Cognitivos válida para o serviço Text Analytics.

5. Em **''Request Body**' ( **Tweet Text),** que fornece o texto do tweet como entrada para análise.

Depois de obter os dados do tweet e insights sobre o tweet, pode agora utilizar vários outros conectores relevantes e suas ações:

* **Power BI - Adicione linhas ao conjunto de dados de streaming**: Ver tweets de entrada num painel Power BI.
* **Azure Data Lake - Ficheiro de apêndice**: Adicione os dados dos clientes a um conjunto de dados do Azure Data Lake para incluir em trabalhos de análise.
* **SQL - Adicionar linhas**: Armazenar dados numa base de dados para posterior recuperação.
* **Slack - Enviar mensagem**: Notifique um canal Slack sobre feedback negativo que possa exigir ação.

Também pode criar e uma Função Azure para que possa realizar o processamento personalizado nos seus dados. 

## <a name="process-data-with-azure-functions"></a>Processar dados com Funções Azure

Antes de criar uma função, crie uma aplicação de função na sua subscrição Azure. Além disso, para que a sua aplicação lógica chame diretamente uma função, a função deve ter uma ligação http trigger, por exemplo, use o modelo **HttpTrigger.** Saiba [como criar a sua primeira aplicação de função e funcionar no portal Azure](../azure-functions/functions-get-started.md).

Para este cenário, utilize o texto do tweet como o órgão de pedido para a sua Função Azure. No seu código de função, defina a lógica que determina se o texto do tweet contém uma palavra-chave ou uma frase. Mantenha a função tão simples ou complexa quanto necessário para o cenário.
No final da função, devolva uma resposta à aplicação lógica com alguns dados, por exemplo, um simples valor boolean, como `containsKeyword` ou um objeto complexo.

> [!TIP]
> Para aceder a uma resposta complexa a partir de uma função numa aplicação lógica, utilize a ação **Parse JSON.**

Quando terminar, guarde a função e adicione a função como uma ação na aplicação lógica que está a construir.

## <a name="add-azure-function-to-logic-app"></a>Adicione a função Azure à aplicação lógica

1. Na Logic App Designer, no âmbito da ação **Detect Sentiment,** escolha **novo passo.**

2. Encontre o conector **Azure Functions** e, em seguida, selecione a função que criou.

3. Em **''Request Body**' select **Tweet Text**.

![Passo de função Azure configurado][2]

## <a name="run-and-monitor-your-logic-app"></a>Executar e monitorizar a sua aplicação lógica

Para rever quaisquer execuções atuais ou anteriores para a sua aplicação lógica, pode utilizar as ricas capacidades de depuração e monitorização que a Azure Logic Apps fornece no portal Azure, Visual Studio, ou através das APIs e SDKs Azure REST.

Para testar facilmente a sua aplicação lógica, no Logic App Designer, escolha **Run Trigger**. As sondagens para tweets com base na sua agenda especificada até que seja encontrado um tweet que satisfaça os seus critérios. Enquanto a corrida progride, o designer mostra uma visão ao vivo para esta corrida.

Para ver histórias anteriores no Visual Studio ou no portal Azure: 

* Open Visual Studio Cloud Explorer. Encontre a sua aplicação lógica, abra o menu de atalho da aplicação. Selecione **O histórico de execução aberta**.

  > [!TIP]
  > Se não tiver este comando no Visual Studio 2019, verifique se tem as últimas atualizações para o Visual Studio.

* No portal Azure, encontre a sua aplicação lógica. No menu da sua aplicação lógica, escolha **visão geral.** 

## <a name="create-automated-deployment-templates"></a>Criar modelos de implementação automatizada

Depois de criar uma solução de aplicação lógica, pode capturar e implementar a sua app como [modelo de Gestor de Recursos Azure](../azure-resource-manager/templates/overview.md) para qualquer região do Azure no mundo. Pode utilizar esta capacidade tanto para modificar parâmetros para criar diferentes versões da sua aplicação como para integrar a sua solução em Azure Pipelines. Também pode incluir Funções Azure no seu modelo de implementação para que possa gerir toda a solução com todas as dependências como um único modelo. Saiba como automatizar a implementação de [aplicativos lógicos.](logic-apps-azure-resource-manager-templates-overview.md)

Para obter um modelo de implementação de exemplo com uma função Azure, verifique o [repositório do modelo de arranque rápido Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-function-app-create-dynamic).

## <a name="next-steps"></a>Passos seguintes

* [Encontre outros exemplos e cenários para Azure Logic Apps](logic-apps-examples-and-scenarios.md)

<!-- Image References -->
[1]: ./media/logic-apps-scenario-social-serverless/twitter.png
[2]: ./media/logic-apps-scenario-social-serverless/function.png
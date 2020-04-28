---
title: Criar painel de insights do cliente
description: Gerencie o feedback dos clientes, dados das redes sociais e muito mais através da construção de um dashboard de clientes com Aplicações lógicas Azure e Funções Azure
services: logic-apps
ms.suite: integration
author: jeffhollan
ms.author: jehollan
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 03/15/2018
ms.openlocfilehash: e300bf9c9aa0acf0bed6426eb73f690f9a38bd74
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75980430"
---
# <a name="create-a-streaming-customer-insights-dashboard-with-azure-logic-apps-and-azure-functions"></a>Crie um dashboard de informação de clientes em streaming com aplicações da Lógica Azure e funções azure

O Azure oferece ferramentas [sem servidorque](https://azure.microsoft.com/solutions/serverless/) o ajudam a construir e hospedar aplicações rapidamente na nuvem, sem ter de pensar em infraestruturas. Neste tutorial, pode criar um dashboard que desencadeie o feedback do cliente, analise o feedback com machine learning e publique insights para uma fonte, como power BI ou Azure Data Lake.

Para esta solução, utiliza estes componentes chave Azure para aplicações sem servidor: [Funções Azure](https://azure.microsoft.com/services/functions/) e [Aplicações Lógicas Azure](https://azure.microsoft.com/services/logic-apps/).
As Aplicações Lógicas Azure fornecem um motor de fluxo de trabalho sem servidores na nuvem para que possa criar orquestrações através de componentes sem servidor estoirar e ligar-se a serviços e APIs de mais de 200. As Funções Azure fornecem computação sem servidor estoque na nuvem. Esta solução utiliza funções Azure para sinalizar tweets de clientes com base em palavras-chave predefinidas.

Neste cenário, cria-se uma aplicação lógica que desencadeia a procura de feedback por parte dos clientes. Alguns conectores que o ajudam a responder ao feedback do cliente incluem Outlook.com, Office 365, Survey Monkey, Twitter e um [pedido http de um formulário web](https://blogs.msdn.microsoft.com/logicapps/2017/01/30/calling-a-logic-app-from-an-html-form/). O fluxo de trabalho que cria monitoriza uma hashtag no Twitter.

Você pode [construir toda a solução no Estúdio Visual](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) e implementar a [solução com o modelo De Gestor de Recursos Azure](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md). Para um vídeo que mostra como criar esta solução, [veja este vídeo do Canal 9](https://aka.ms/logicappsdemo). 

## <a name="trigger-on-customer-data"></a>Desencadear os dados do cliente

1. No portal Azure ou No Estúdio Visual, crie uma aplicação lógica em branco. 

   Se você é novo em aplicativos lógicos, reveja o [quickstart para o portal Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md) ou o [quickstart para Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

2. Na Logic App Designer, encontre e adicione o gatilho do Twitter que tem esta ação: **Quando um novo tweet é publicado**

3. Configurar o gatilho para ouvir tweets com base numa palavra-chave ou hashtag.

   Nos gatilhos baseados nas sondagens, como o gatilho do Twitter, a propriedade de recorrência determina com que frequência a aplicação lógica verifica novos itens.

   ![Exemplo do gatilho do Twitter][1]

Esta aplicação lógica agora dispara em todos os novos tweets. Em seguida, pode pegar e analisar os dados do tweet para que possa entender melhor os sentimentos expressos. 

## <a name="analyze-tweet-text"></a>Analisar texto de tweet

Para detetar o sentimento por trás de algum texto, pode utilizar [serviços cognitivos Azure](https://azure.microsoft.com/services/cognitive-services/).

1. Na Logic App Designer, sob o gatilho, escolha **novo passo**.

2. Encontre o conector **Text Analytics.**

3. Selecione a ação **Detect Sentiment.**

4. Se solicitado, forneça uma chave de Serviços Cognitivos válida para o serviço Text Analytics.

5. No **âmbito do Request Body,** selecione o campo de texto do **Tweet,** que fornece o texto do tweet como entrada para análise.

Depois de obter os dados do tweet e insights sobre o tweet, pode agora usar vários outros conectores relevantes e suas ações:

* **Power BI - Adicione linhas ao conjunto**de dados de streaming : Veja os tweets de entrada num dashboard Power BI.
* **Azure Data Lake - Ficheiro apêndice**: Adicione os dados dos clientes a um conjunto de dados do Lago de Dados Azure para incluir em trabalhos de análise.
* **SQL - Adicionar linhas**: Armazenar dados numa base de dados para posterior recuperação.
* **Folga - Enviar mensagem**: Notifique um canal Slack sobre feedback negativo que possa exigir ação.

Também pode criar e uma Função Azure para que possa realizar o processamento personalizado dos seus dados. 

## <a name="process-data-with-azure-functions"></a>Processar dados com funções azure

Antes de criar uma função, crie uma aplicação de função na subscrição do Azure. Além disso, para que a sua aplicação lógica ligue diretamente a uma função, a função deve ter um gatilho HTTP, por exemplo, usar o modelo **HttpTrigger.** Aprenda [a criar a sua primeira aplicação de funções e a função no portal Azure](../azure-functions/functions-create-first-azure-function-azure-portal.md).

Para este cenário, utilize o texto do tweet como o organismo de pedido para a sua Função Azure. No seu código de função, defina a lógica que determina se o texto do tweet contém uma palavra-chave ou frase. Mantenha a função tão simples ou complexa quanto necessária para o cenário.
No final da função, devolva uma resposta à aplicação lógica com alguns dados, por exemplo, um simples valor booleano, como `containsKeyword` ou um objeto complexo.

> [!TIP]
> Para aceder a uma resposta complexa de uma função numa aplicação lógica, utilize a ação **Parse JSON.**

Quando terminar, guarde a função e adicione a função como uma ação na aplicação lógica que está a construir.

## <a name="add-azure-function-to-logic-app"></a>Adicione a função Azure à aplicação lógica

1. Na Logic App Designer, no âmbito da ação **Detect Sentiment,** escolha **novo passo.**

2. Encontre o conector **funções Azure** e, em seguida, selecione a função que criou.

3. Sob **o Corpo de Pedido,** selecione Tweet **Text**.

![Passo de função Azure configurado][2]

## <a name="run-and-monitor-your-logic-app"></a>Executar e monitorizar a sua aplicação lógica

Para rever quaisquer execuções atuais ou anteriores para a sua aplicação lógica, pode utilizar as ricas capacidades de depuração e monitorização que as Aplicações Lógicas Azure fornecem no portal Azure, Visual Studio, ou através dos APIs e SDKs do Azure REST.

Para testar facilmente a sua aplicação lógica, no Logic App Designer, escolha **O Gatilho de Execução**. As sondagens para tweets com base na sua agenda especificada até que seja encontrado um tweet que cumpra os seus critérios. Enquanto a corrida progride, o designer mostra uma visão ao vivo para esta corrida.

Para ver histórias anteriores no Estúdio Visual ou no portal Azure: 

* Open Visual Studio Cloud Explorer. Encontre a sua aplicação lógica, abra o menu de atalho da aplicação. Selecione **histórico de execução aberta.**

  > [!TIP]
  > Se não tiver este comando no Visual Studio 2019, verifique se tem as últimas atualizações para o Visual Studio.

* No portal Azure, encontre a sua aplicação lógica. No menu da sua aplicação lógica, escolha **visão geral**. 

## <a name="create-automated-deployment-templates"></a>Criar modelos de implementação automatizada

Depois de criar uma solução lógica de aplicações, pode capturar e implementar a sua aplicação como modelo de Gestor de [Recursos Azure](../azure-resource-manager/templates/overview.md) para qualquer região do Azure no mundo. Pode utilizar esta capacidade tanto para modificar parâmetros para criar diferentes versões da sua aplicação como para integrar a sua solução em Pipelines Azure. Também pode incluir funções Azure no seu modelo de implementação para que possa gerir toda a solução com todas as dependências como um único modelo. Aprenda a automatizar a implementação de [aplicações lógicas.](logic-apps-azure-resource-manager-templates-overview.md)

Para um modelo de implantação com uma função Azure, verifique o [repositório](https://github.com/Azure/azure-quickstart-templates/tree/master/101-function-app-create-dynamic)do modelo de arranque rápido Azure .

## <a name="next-steps"></a>Passos seguintes

* [Encontre outros exemplos e cenários para apps lógicas do Azure](logic-apps-examples-and-scenarios.md)

<!-- Image References -->
[1]: ./media/logic-apps-scenario-social-serverless/twitter.png
[2]: ./media/logic-apps-scenario-social-serverless/function.png

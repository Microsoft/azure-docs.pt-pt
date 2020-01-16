---
title: Criar painel de informações do cliente
description: Gerencie comentários de clientes, dados de mídia social e muito mais criando um painel de clientes com aplicativos lógicos do Azure e Azure Functions
services: logic-apps
ms.suite: integration
author: jeffhollan
ms.author: jehollan
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 03/15/2018
ms.openlocfilehash: e300bf9c9aa0acf0bed6426eb73f690f9a38bd74
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75980430"
---
# <a name="create-a-streaming-customer-insights-dashboard-with-azure-logic-apps-and-azure-functions"></a>Criar um painel de informações do cliente de streaming com os aplicativos lógicos do Azure e Azure Functions

O Azure oferece ferramentas sem [servidor](https://azure.microsoft.com/solutions/serverless/) que ajudam você a criar e hospedar aplicativos na nuvem rapidamente, sem precisar pensar na infraestrutura. Neste tutorial, você pode criar um painel que é disparado nos comentários do cliente, analisa os comentários com o aprendizado de máquina e publica informações em uma fonte, como Power BI ou Azure Data Lake.

Para essa solução, você usa estes componentes principais do Azure para aplicativos sem servidor: [Azure Functions](https://azure.microsoft.com/services/functions/) e [aplicativos lógicos do Azure](https://azure.microsoft.com/services/logic-apps/).
O aplicativo lógico do Azure fornece um mecanismo de fluxo de trabalho sem servidor na nuvem para que você possa criar orquestrações em componentes sem servidor e se conectar a mais de 200 serviços e APIs. O Azure Functions fornece computação sem servidor na nuvem. Essa solução usa Azure Functions para sinalizar tweets de cliente com base em palavras-chave predefinidas.

Nesse cenário, você cria um aplicativo lógico que dispara ao encontrar comentários de clientes. Alguns conectores que ajudam você a responder aos comentários dos clientes incluem Outlook.com, Office 365, macaco de pesquisa, Twitter e uma [solicitação HTTP de um formulário da Web](https://blogs.msdn.microsoft.com/logicapps/2017/01/30/calling-a-logic-app-from-an-html-form/). O fluxo de trabalho que você cria monitora uma hashtag no Twitter.

Você pode [criar a solução inteira no Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) e [implantar a solução com Azure Resource Manager modelo](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md). Para ver uma explicação em vídeo que mostra como criar essa solução, [Assista a este vídeo do Channel 9](https://aka.ms/logicappsdemo). 

## <a name="trigger-on-customer-data"></a>Disparar nos dados do cliente

1. No portal do Azure ou no Visual Studio, crie um aplicativo lógico em branco. 

   Se você for novo em aplicativos lógicos, leia o guia de [início rápido para o portal do Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md) ou o guia de [início rápido para o Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

2. No designer de aplicativo lógico, localize e adicione o gatilho do Twitter que tem esta ação: **quando um novo tweet é Postado**

3. Configure o gatilho para escutar tweets com base em uma palavra-chave ou hashtag.

   Em gatilhos baseados em sondagem, como o gatilho do Twitter, a propriedade Recurrence determina com que frequência o aplicativo lógico verifica se há novos itens.

   ![Exemplo de gatilho do Twitter][1]

Este aplicativo lógico agora é acionado em todos os novos tweets. Você pode pegar e analisar os dados do tweet para que você possa entender melhor as opiniões expressas. 

## <a name="analyze-tweet-text"></a>Analisar texto do tweet

Para detectar a percepção por trás de algum texto, você pode usar os [Serviços cognitivas do Azure](https://azure.microsoft.com/services/cognitive-services/).

1. No designer de aplicativo lógico, no gatilho, escolha **nova etapa**.

2. Localize o conector de **análise de texto** .

3. Selecione a ação **detectar sentimentos** .

4. Se solicitado, forneça uma chave de serviços cognitivas válida para o serviço de Análise de Texto.

5. Em **corpo da solicitação**, selecione o campo de **texto tweet** , que fornece o texto do tweet como entrada para análise.

Depois de obter os dados e as informações do tweet sobre o tweet, agora você pode usar vários outros conectores relevantes e suas ações:

* **Power bi-adicionar linhas ao conjunto de registros de streaming**: Exibir tweets de entrada em um painel de Power bi.
* **Azure data Lake-acrescentar arquivo**: adiciona dados do cliente a um conjunto de Azure data Lake para incluir em trabalhos de análise.
* **SQL-adicionar linhas**: armazene dados em um banco de dado para recuperação posterior.
* **Margem de atraso – enviar mensagem**: Notifique um canal de margem de atraso sobre comentários negativos que podem exigir ação.

Você também pode criar e uma função do Azure para que possa executar o processamento personalizado em seus dados. 

## <a name="process-data-with-azure-functions"></a>Processar dados com Azure Functions

Antes de criar uma função, crie um aplicativo de funções em sua assinatura do Azure. Além disso, para seu aplicativo lógico chamar diretamente uma função, a função deve ter uma associação de gatilho HTTP, por exemplo, usar o modelo **HttpTrigger** . Saiba [como criar seu primeiro aplicativo de funções e função no portal do Azure](../azure-functions/functions-create-first-azure-function-azure-portal.md).

Para este cenário, use o texto do tweet como o corpo da solicitação para sua função do Azure. No seu código de função, defina a lógica que determina se o texto do tweet contém uma palavra-chave ou frase. Mantenha a função tão simples ou complexa quanto necessário para o cenário.
No final da função, retorne uma resposta para o aplicativo lógico com alguns dados, por exemplo, um valor booliano simples, como `containsKeyword` ou um objeto complexo.

> [!TIP]
> Para acessar uma resposta complexa de uma função em um aplicativo lógico, use a ação **analisar JSON** .

Quando terminar, salve a função e adicione a função como uma ação no aplicativo lógico que você está criando.

## <a name="add-azure-function-to-logic-app"></a>Adicionar Azure function ao aplicativo lógico

1. No designer de aplicativo lógico, na ação **detectar sentimentos** , escolha **nova etapa**.

2. Localize o conector de **Azure Functions** e, em seguida, selecione a função que você criou.

3. Em **corpo da solicitação**, selecione **texto do tweet**.

![Etapa da função do Azure configurada][2]

## <a name="run-and-monitor-your-logic-app"></a>Executar e monitorar seu aplicativo lógico

Para examinar as execuções atuais ou anteriores para seu aplicativo lógico, você pode usar os recursos avançados de depuração e monitoramento que os aplicativos lógicos do Azure fornecem no portal do Azure, no Visual Studio ou por meio de APIs REST do Azure e SDKs.

Para testar facilmente seu aplicativo lógico, no designer de aplicativo lógico, escolha **executar gatilho**. O gatilho sonda os tweets com base na agenda especificada até que um tweet que atenda aos seus critérios seja encontrado. Enquanto a execução progride, o designer mostra uma exibição dinâmica para essa execução.

Para exibir os históricos de execução anteriores no Visual Studio ou no portal do Azure: 

* Abra o Visual Studio Cloud Explorer. Localize seu aplicativo lógico, abra o menu de atalho do aplicativo. Selecione **abrir histórico de execução**.

  > [!TIP]
  > Se você não tiver esse comando no Visual Studio 2019, verifique se você tem as atualizações mais recentes para o Visual Studio.

* Na portal do Azure, localize seu aplicativo lógico. No menu do aplicativo lógico, escolha **visão geral**. 

## <a name="create-automated-deployment-templates"></a>Criar modelos de implementação automatizada

Depois de criar uma solução de aplicativo lógico, você pode capturar e implantar seu aplicativo como um [modelo de Azure Resource Manager](../azure-resource-manager/templates/overview.md) em qualquer região do Azure no mundo. Você pode usar essa capacidade para modificar parâmetros para criar versões diferentes do seu aplicativo e para integrar sua solução no Azure Pipelines. Você também pode incluir Azure Functions em seu modelo de implantação para que possa gerenciar toda a solução com todas as dependências como um único modelo. Saiba como [automatizar a implantação do aplicativo lógico](logic-apps-azure-resource-manager-templates-overview.md).

Para obter um exemplo de modelo de implantação com uma função do Azure, verifique o [repositório de modelos de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-function-app-create-dynamic).

## <a name="next-steps"></a>Passos seguintes

* [Encontre outros exemplos e cenários para aplicativos lógicos do Azure](logic-apps-examples-and-scenarios.md)

<!-- Image References -->
[1]: ./media/logic-apps-scenario-social-serverless/twitter.png
[2]: ./media/logic-apps-scenario-social-serverless/function.png

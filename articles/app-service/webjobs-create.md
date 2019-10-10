---
title: Executar tarefas em segundo plano com o serviço de Azure App de trabalho Web
description: Saiba como usar o webjobs para executar tarefas em segundo plano em aplicativos Web do serviço Azure App, aplicativos de API ou aplicativos móveis.
services: app-service
author: ggailey777
manager: jeconnoc
editor: jimbe
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.service: app-service
ms.topic: article
ms.date: 10/16/2018
ms.author: glenga
ms.reviewer: msangapu;david.ebbo;suwatch;pbatum;naren.soni
ms.custom: seodec18
ms.openlocfilehash: 53f808570a298c8e576b6df7b4654196ffc56813
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177498"
---
# <a name="run-background-tasks-with-webjobs-in-azure-app-service"></a>Executar tarefas em segundo plano com trabalhos Web no serviço Azure App

## <a name="overview"></a>Visão geral
O webjobs é um recurso do [serviço de Azure app](https://docs.microsoft.com/azure/app-service/) que permite executar um programa ou script no mesmo contexto que um aplicativo Web, aplicativo de API ou aplicativo móvel. Não há nenhum custo adicional para usar trabalhos Web.

> [!IMPORTANT]
> Os trabalhos Web ainda não têm suporte para o serviço de aplicativo no Linux.

Este artigo mostra como implantar trabalhos Web usando o [portal do Azure](https://portal.azure.com) para carregar um executável ou script. Para obter informações sobre como desenvolver e implantar trabalhos Web usando o Visual Studio, consulte [implantar trabalhos Web usando o Visual Studio](webjobs-dotnet-deploy-vs.md).

O SDK do Azure WebJobs pode ser usado com trabalhos Web para simplificar muitas tarefas de programação. Para obter mais informações, consulte [o que é o SDK de trabalhos](https://github.com/Azure/azure-webjobs-sdk/wiki)Web.

Azure Functions fornece outra maneira de executar programas e scripts. Para obter uma comparação entre trabalhos Web e funções, consulte [escolher entre fluxo, aplicativos lógicos, funções e trabalhos](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md)Web.

## <a name="webjob-types"></a>Tipos de WebJob

A tabela a seguir descreve as diferenças entre trabalhos Web *contínuos* e *disparados* .


|Contínuo  |Ativou  |
|---------|---------|
| Inicia imediatamente quando o WebJob é criado. Para impedir que o trabalho seja encerrado, o programa ou o script normalmente faz seu trabalho dentro de um loop infinito. Se o trabalho for encerrado, você poderá reiniciá-lo. | Inicia somente quando disparado manualmente ou em um agendamento. |
| É executado em todas as instâncias em que o aplicativo Web é executado. Opcionalmente, você pode restringir o WebJob a uma única instância. |É executado em uma única instância que o Azure seleciona para o balanceamento de carga.|
| Dá suporte à depuração remota. | Não dá suporte à depuração remota.|

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

## <a name="acceptablefiles"></a>Tipos de arquivo com suporte para scripts ou programas

Há suporte para os seguintes tipos de arquivo:

* . cmd,. bat,. exe (usando o Windows cmd)
* . ps1 (usando o PowerShell)
* . sh (usando bash)
* . php (usando PHP)
* . py (usando Python)
* . js (usando o Node. js)
* . jar (usando Java)

## <a name="CreateContinuous"></a>Criar um WebJob contínuo

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. Na [portal do Azure](https://portal.azure.com), vá para a página **serviço de aplicativo** do seu aplicativo Web do serviço de aplicativo, aplicativo de API ou aplicativo móvel.

2. Selecione **trabalhos**Web.

   ![Selecionar trabalhos Web](./media/web-sites-create-web-jobs/select-webjobs.png)

2. Na página **trabalhos** Web, selecione **Adicionar**.

    ![Página do WebJob](./media/web-sites-create-web-jobs/wjblade.png)

3. Use as configurações de **Adicionar WebJob** conforme especificado na tabela.

   ![Adicionar página do WebJob](./media/web-sites-create-web-jobs/addwjcontinuous.png)

   | Definição      | Valor da amostra   | Descrição  |
   | ------------ | ----------------- | ------------ |
   | **Nome** | myContinuousWebJob | Um nome que é exclusivo dentro de um aplicativo do serviço de aplicativo. Deve começar com uma letra ou um número e não pode conter caracteres especiais além de "-" e "_". |
   | **Upload de arquivo** | ConsoleApp. zip | Um arquivo *. zip* que contém o arquivo executável ou de script, bem como quaisquer arquivos de suporte necessários para executar o programa ou script. Os tipos de arquivo de script ou executáveis com suporte são listados na seção [tipos de arquivo com suporte](#acceptablefiles) . |
   | **Tipo** | Contínuo | Os [tipos de trabalho Web](#webjob-types) são descritos anteriormente neste artigo. |
   | **Dimensionar** | Várias instâncias | Disponível somente para trabalhos Web contínuos. Determina se o programa ou script é executado em todas as instâncias ou apenas uma instância. A opção de executar em várias instâncias não se aplica aos [tipos de preço](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)gratuito ou compartilhado. | 

4. Clique em **OK**.

   O novo WebJob aparece na página **trabalhos** Web.

   ![Lista de trabalhos Web](./media/web-sites-create-web-jobs/listallwebjobs.png)

2. Para parar ou reiniciar um WebJob contínuo, clique com o botão direito do mouse no WebJob na lista e clique em **parar** ou **Iniciar**.

    ![Parar um WebJob contínuo](./media/web-sites-create-web-jobs/continuousstop.png)

## <a name="CreateOnDemand"></a>Criar um trabalho Web disparado manualmente

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. Na [portal do Azure](https://portal.azure.com), vá para a página **serviço de aplicativo** do seu aplicativo Web do serviço de aplicativo, aplicativo de API ou aplicativo móvel.

2. Selecione **trabalhos**Web.

   ![Selecionar trabalhos Web](./media/web-sites-create-web-jobs/select-webjobs.png)

2. Na página **trabalhos** Web, selecione **Adicionar**.

    ![Página do WebJob](./media/web-sites-create-web-jobs/wjblade.png)

3. Use as configurações de **Adicionar WebJob** conforme especificado na tabela.

   ![Adicionar página do WebJob](./media/web-sites-create-web-jobs/addwjtriggered.png)

   | Definição      | Valor da amostra   | Descrição  |
   | ------------ | ----------------- | ------------ |
   | **Nome** | myTriggeredWebJob | Um nome que é exclusivo dentro de um aplicativo do serviço de aplicativo. Deve começar com uma letra ou um número e não pode conter caracteres especiais além de "-" e "_".|
   | **Upload de arquivo** | ConsoleApp. zip | Um arquivo *. zip* que contém o arquivo executável ou de script, bem como quaisquer arquivos de suporte necessários para executar o programa ou script. Os tipos de arquivo de script ou executáveis com suporte são listados na seção [tipos de arquivo com suporte](#acceptablefiles) . |
   | **Tipo** | Ativou | Os [tipos de trabalho Web](#webjob-types) são descritos anteriormente neste artigo. |
   | **Acionadores** | Manual | |

4. Clique em **OK**.

   O novo WebJob aparece na página **trabalhos** Web.

   ![Lista de trabalhos Web](./media/web-sites-create-web-jobs/listallwebjobs.png)

7. Para executar o WebJob, clique com o botão direito do mouse em seu nome na lista e clique em **executar**.
   
    ![Executar WebJob](./media/web-sites-create-web-jobs/runondemand.png)

## <a name="CreateScheduledCRON"></a>Criar um WebJob agendado

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. Na [portal do Azure](https://portal.azure.com), vá para a página **serviço de aplicativo** do seu aplicativo Web do serviço de aplicativo, aplicativo de API ou aplicativo móvel.

2. Selecione **trabalhos**Web.

   ![Selecionar trabalhos Web](./media/web-sites-create-web-jobs/select-webjobs.png)

2. Na página **trabalhos** Web, selecione **Adicionar**.

   ![Página do WebJob](./media/web-sites-create-web-jobs/wjblade.png)

3. Use as configurações de **Adicionar WebJob** conforme especificado na tabela.

   ![Adicionar página do WebJob](./media/web-sites-create-web-jobs/addwjscheduled.png)

   | Definição      | Valor da amostra   | Descrição  |
   | ------------ | ----------------- | ------------ |
   | **Nome** | myScheduledWebJob | Um nome que é exclusivo dentro de um aplicativo do serviço de aplicativo. Deve começar com uma letra ou um número e não pode conter caracteres especiais além de "-" e "_". |
   | **Upload de arquivo** | ConsoleApp. zip | Um arquivo *. zip* que contém o arquivo executável ou de script, bem como quaisquer arquivos de suporte necessários para executar o programa ou script. Os tipos de arquivo de script ou executáveis com suporte são listados na seção [tipos de arquivo com suporte](#acceptablefiles) . |
   | **Tipo** | Ativou | Os [tipos de trabalho Web](#webjob-types) são descritos anteriormente neste artigo. |
   | **Acionadores** | Agendado | Para que o agendamento funcione de forma confiável, habilite o recurso Always On. Always On está disponível apenas nos tipos de preço básico, Standard e Premium.|
   | **Expressão CRON** | 0 0/20 * * * * | As [expressões cron](#ncrontab-expressions) são descritas na seção a seguir. |

4. Clique em **OK**.

   O novo WebJob aparece na página **trabalhos** Web.

   ![Lista de trabalhos Web](./media/web-sites-create-web-jobs/listallwebjobs.png)

## <a name="ncrontab-expressions"></a>Expressões NCRONTAB

Você pode inserir uma [expressão NCRONTAB](../azure-functions/functions-bindings-timer.md#ncrontab-expressions) no portal ou incluir um arquivo `settings.job` na raiz do seu arquivo WebJob *. zip* , como no exemplo a seguir:

```json
{
    "schedule": "0 */15 * * * *"
}
```

Para saber mais, consulte [agendando um WebJob disparado](webjobs-dotnet-deploy-vs.md#scheduling-a-triggered-webjob).

## <a name="ViewJobHistory"></a>Exibir o histórico de trabalhos

1. Selecione o WebJob para o qual você deseja ver o histórico e, em seguida, selecione o botão **logs** .
   
   ![Botão logs](./media/web-sites-create-web-jobs/wjbladelogslink.png)

2. Na página **detalhes do WebJob** , selecione um horário para ver os detalhes de uma execução.
   
   ![Detalhes do WebJob](./media/web-sites-create-web-jobs/webjobdetails.png)

3. Na página **detalhes da execução do WebJob** , selecione **alternar saída** para ver o texto do conteúdo do log.
   
    ![Detalhes da execução do trabalho Web](./media/web-sites-create-web-jobs/webjobrundetails.png)

   Para ver o texto de saída em uma janela separada do navegador, selecione **baixar**. Para baixar o texto em si, clique com o botão direito do mouse em **baixar** e use as opções do navegador para salvar o conteúdo do arquivo.
   
5. Selecione o link de navegação web de **trabalhos** Web na parte superior da página para ir para uma lista de trabalhos Web.

    ![Breadcrumb do WebJob](./media/web-sites-create-web-jobs/breadcrumb.png)
   
    ![Lista de trabalhos Web no painel de histórico](./media/web-sites-create-web-jobs/webjobslist.png)
   
## <a name="NextSteps"></a> Passos seguintes

O SDK do Azure WebJobs pode ser usado com trabalhos Web para simplificar muitas tarefas de programação. Para obter mais informações, consulte [o que é o SDK de trabalhos](https://github.com/Azure/azure-webjobs-sdk/wiki)Web.

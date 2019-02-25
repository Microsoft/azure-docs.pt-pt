---
title: Executar tarefas em segundo plano com o WebJobs - serviço de aplicações do Azure
description: Saiba como utilizar o WebJobs para executar tarefas em segundo plano no serviço de aplicações do Azure web apps, aplicações API ou aplicações móveis.
services: app-service
documentationcenter: ''
author: ggailey777
manager: jeconnoc
editor: jimbe
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2018
ms.author: glenga;msangapu;david.ebbo;suwatch;pbatum;naren.soni;
ms.custom: seodec18
ms.openlocfilehash: 0f2053e978b7c890f4e175515ed54f69694950c6
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/24/2019
ms.locfileid: "56749924"
---
# <a name="run-background-tasks-with-webjobs-in-azure-app-service"></a>Executar tarefas em segundo plano com o WebJobs no serviço de aplicações do Azure

## <a name="overview"></a>Descrição geral
WebJobs é uma funcionalidade do [App Service do Azure](https://docs.microsoft.com/azure/app-service/) que permite-lhe executar um programa ou script no mesmo contexto como uma aplicação web, a aplicação API ou a aplicação móvel. Não existe nenhum custo adicional para utilizar o WebJobs.

> [!IMPORTANT]
> WebJobs ainda não é suportada para o serviço de aplicações no Linux.

Este artigo mostra como implementar WebJobs com o [portal do Azure](https://portal.azure.com) para carregar um executável ou script. Para obter informações sobre como desenvolver e implementar o WebJobs com o Visual Studio, consulte [implementar WebJobs com o Visual Studio](webjobs-dotnet-deploy-vs.md).

O SDK de WebJobs do Azure pode ser utilizado com o WebJobs para simplificar diversas tarefas de programação. Para obter mais informações, consulte [o que é o SDK de WebJobs](https://github.com/Azure/azure-webjobs-sdk/wiki).

As funções do Azure proporciona outra forma de executar programas e scripts. Para uma comparação entre funções e WebJobs, consulte [escolher entre Flow, Logic Apps, funções e WebJobs](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

## <a name="webjob-types"></a>Tipos de WebJob

A tabela seguinte descreve as diferenças entre *contínua* e *acionada* WebJobs.


|Contínuo  |Acionado  |
|---------|---------|
| Começa imediatamente quando o trabalho Web é criado. Para impedir que a tarefa terminar, o programa ou script normalmente faz seu trabalho dentro de um loop infinito. Se a tarefa terminar, pode reiniciá-lo. | É iniciado apenas quando acionados com base numa agenda ou manualmente. |
| É executado em todas as instâncias que a aplicação web é executado. Opcionalmente, pode restringir o WebJob para uma única instância. |É executado numa única instância para o Azure seleciona o balanceamento de carga.|
| Suporta a depuração remota. | Não suporta a depuração remota.|

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

## <a name="acceptablefiles"></a>Tipos de ficheiro suportados para scripts ou programas

São suportados os seguintes tipos de ficheiro:

* . cmd,. bat, .exe (usando o cmd do Windows)
* . ps1 (com o PowerShell)
* . SH (com o Bash)
* PHP (com PHP)
* . PY (com o Python)
* . js (com node. js)
* . JAR (com Java)

## <a name="CreateContinuous"></a> Criar um trabalho Web contínuo

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. Na [portal do Azure](https://portal.azure.com), aceda ao **serviço de aplicações** página da aplicação móvel, a aplicação API ou a aplicação do serviço de aplicações web.

2. Selecione **WebJobs**.

   ![Selecione o WebJobs](./media/web-sites-create-web-jobs/select-webjobs.png)

2. Na **WebJobs** página, selecione **Add**.

    ![Página de WebJob](./media/web-sites-create-web-jobs/wjblade.png)

3. Utilize o **adicionar WebJob** definições conforme especificado na tabela.

   ![Adicionar página de WebJob](./media/web-sites-create-web-jobs/addwjcontinuous.png)

   | Definição      | Valor da amostra   | Descrição  |
   | ------------ | ----------------- | ------------ |
   | **Nome** | myContinuousWebJob | Um nome que seja exclusivo dentro de uma aplicação de serviço de aplicações. Tem de começar com uma letra ou um número e não pode conter carateres especiais diferente de "-" e "_". |
   | **Carregamento de ficheiros** | ConsoleApp.zip | R *. zip* ficheiro que contém o ficheiro executável ou script, bem como quaisquer ficheiros de suporte necessários para executar o programa ou script. Os tipos de ficheiro executável ou script suportados estão listados na [tipos de ficheiro suportados](#acceptablefiles) secção. |
   | **Tipo** | Contínuo | O [tipos de WebJob](#webjob-types) descritos anteriormente neste artigo. |
   | **Dimensionamento** | De ocorrências múltiplas | Disponível apenas para trabalhos Web contínuos. Determina se o programa ou script é executado em todas as instâncias ou apenas uma instância. A opção para executar em várias instâncias não se aplica a gratuito ou partilhado [escalões de preço](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). | 

4. Clique em **OK**.

   O WebJob novo é apresentado no **WebJobs** página.

   ![Lista de WebJobs](./media/web-sites-create-web-jobs/listallwebjobs.png)

2. Para parar ou reiniciar um trabalho Web contínuo, com o botão direito do trabalho Web na lista e clique em **parar** ou **iniciar**.

    ![Parar um trabalho Web contínuo](./media/web-sites-create-web-jobs/continuousstop.png)

## <a name="CreateOnDemand"></a> Criar um WebJob manualmente acionado

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. Na [portal do Azure](https://portal.azure.com), aceda ao **serviço de aplicações** página da aplicação móvel, a aplicação API ou a aplicação do serviço de aplicações web.

2. Selecione **WebJobs**.

   ![Selecione o WebJobs](./media/web-sites-create-web-jobs/select-webjobs.png)

2. Na **WebJobs** página, selecione **Add**.

    ![Página de WebJob](./media/web-sites-create-web-jobs/wjblade.png)

3. Utilize o **adicionar WebJob** definições conforme especificado na tabela.

   ![Adicionar página de WebJob](./media/web-sites-create-web-jobs/addwjtriggered.png)

   | Definição      | Valor da amostra   | Descrição  |
   | ------------ | ----------------- | ------------ |
   | **Nome** | myTriggeredWebJob | Um nome que seja exclusivo dentro de uma aplicação de serviço de aplicações. Tem de começar com uma letra ou um número e não pode conter carateres especiais diferente de "-" e "_".|
   | **Carregamento de ficheiros** | ConsoleApp.zip | R *. zip* ficheiro que contém o ficheiro executável ou script, bem como quaisquer ficheiros de suporte necessários para executar o programa ou script. Os tipos de ficheiro executável ou script suportados estão listados na [tipos de ficheiro suportados](#acceptablefiles) secção. |
   | **Tipo** | Acionado | O [tipos de WebJob](#webjob-types) descritos anteriormente neste artigo. |
   | **acionadores** | Manual | |

4. Clique em **OK**.

   O WebJob novo é apresentado no **WebJobs** página.

   ![Lista de WebJobs](./media/web-sites-create-web-jobs/listallwebjobs.png)

7. Para executar o WebJob, clique no respetivo nome na lista e clique em **executar**.
   
    ![Executar WebJob](./media/web-sites-create-web-jobs/runondemand.png)

## <a name="CreateScheduledCRON"></a> Criar um WebJob agendado

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. Na [portal do Azure](https://portal.azure.com), aceda ao **serviço de aplicações** página da aplicação móvel, a aplicação API ou a aplicação do serviço de aplicações web.

2. Selecione **WebJobs**.

   ![Selecione o WebJobs](./media/web-sites-create-web-jobs/select-webjobs.png)

2. Na **WebJobs** página, selecione **Add**.

   ![Página de WebJob](./media/web-sites-create-web-jobs/wjblade.png)

3. Utilize o **adicionar WebJob** definições conforme especificado na tabela.

   ![Adicionar página de WebJob](./media/web-sites-create-web-jobs/addwjscheduled.png)

   | Definição      | Valor da amostra   | Descrição  |
   | ------------ | ----------------- | ------------ |
   | **Nome** | myScheduledWebJob | Um nome que seja exclusivo dentro de uma aplicação de serviço de aplicações. Tem de começar com uma letra ou um número e não pode conter carateres especiais diferente de "-" e "_". |
   | **Carregamento de ficheiros** | ConsoleApp.zip | R *. zip* ficheiro que contém o ficheiro executável ou script, bem como quaisquer ficheiros de suporte necessários para executar o programa ou script. Os tipos de ficheiro executável ou script suportados estão listados na [tipos de ficheiro suportados](#acceptablefiles) secção. |
   | **Tipo** | Acionado | O [tipos de WebJob](#webjob-types) descritos anteriormente neste artigo. |
   | **acionadores** | Agendadas | Para o agendamento para trabalhar de forma fiável, ative a funcionalidade Always On. Always On está disponível apenas no básico, Standard e Premium de escalões de preço.|
   | **Expressão CRON** | 0 0/20 * * * * | [Expressões CRON](#cron-expressions) são descritos na secção seguinte. |

4. Clique em **OK**.

   O WebJob novo é apresentado no **WebJobs** página.

   ![Lista de WebJobs](./media/web-sites-create-web-jobs/listallwebjobs.png)

## <a name="cron-expressions"></a>Expressões CRON

Pode introduzir um [expressão CRON](../azure-functions/functions-bindings-timer.md#cron-expressions) no portal ou incluir uma `settings.job` ficheiro na raiz do WebJob *. zip* ficheiro, como no exemplo seguinte:

```json
{
    "schedule": "0 */15 * * * *"
}
```

Para obter mais informações, consulte [agendamento um WebJob acionado](webjobs-dotnet-deploy-vs.md#scheduling-a-triggered-webjob).

## <a name="ViewJobHistory"></a> Ver o histórico de tarefas

1. Selecione o trabalho Web que pretende ver histórico para e, em seguida, selecione o **registos** botão.
   
   ![Botão de registos](./media/web-sites-create-web-jobs/wjbladelogslink.png)

2. Na **detalhes de WebJob** página, selecione uma hora para ver detalhes de uma execução.
   
   ![Detalhes do trabalho Web](./media/web-sites-create-web-jobs/webjobdetails.png)

3. Na **detalhes da execução de WebJob** página, selecione **saída de alternância** para ver o texto do conteúdo de registo.
   
    ![Detalhes da execução de tarefa de Web](./media/web-sites-create-web-jobs/webjobrundetails.png)

   Para ver o texto de saída numa janela do browser separado, selecione **transferir**. Para transferir o texto em si, com o botão direito **transferir** e utilize as opções do browser para guardar o conteúdo do ficheiro.
   
5. Selecione o **WebJobs** ligação de trilho na parte superior da página, para aceder a uma lista de WebJobs.

    ![Trilho do WebJob](./media/web-sites-create-web-jobs/breadcrumb.png)
   
    ![Lista de WebJobs no dashboard do histórico](./media/web-sites-create-web-jobs/webjobslist.png)
   
## <a name="NextSteps"></a> Passos seguintes

O SDK de WebJobs do Azure pode ser utilizado com o WebJobs para simplificar diversas tarefas de programação. Para obter mais informações, consulte [o que é o SDK de WebJobs](https://github.com/Azure/azure-webjobs-sdk/wiki).

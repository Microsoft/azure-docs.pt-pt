---
title: Executar tarefas de fundo com WebJobs
description: Aprenda a usar webJobs para executar tarefas de fundo no Serviço de Aplicações Azure. Escolha entre uma variedade de formatos de script e execute-os com expressões CRON.
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: glenga
ms.reviewer: msangapu;david.ebbo;suwatch;pbatum;naren.soni
ms.custom: seodec18
ms.openlocfilehash: 4c568c95a5dbc1799a765c95a2b224de53dfbe9f
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78374156"
---
# <a name="run-background-tasks-with-webjobs-in-azure-app-service"></a>Executar tarefas de fundo com WebJobs no Serviço de Aplicações Azure

Este artigo mostra como implementar webJobs usando o [portal Azure](https://portal.azure.com) para carregar um executável ou script. Para obter informações sobre como desenvolver e implementar WebJobs utilizando o Visual Studio, consulte [o Deploy WebJobs utilizando o Visual Studio](webjobs-dotnet-deploy-vs.md).

## <a name="overview"></a>Descrição geral
WebJobs é uma funcionalidade do [Azure App Service](index.yml) que lhe permite executar um programa ou script no mesmo contexto que uma aplicação web, app API ou aplicação móvel. Não há custos adicionais para usar webJobs.

> [!IMPORTANT]
> WebJobs ainda não é suportado para o Serviço de Aplicações no Linux.

O Azure WebJobs SDK pode ser usado com WebJobs para simplificar muitas tarefas de programação. Para mais informações, consulte [O Que é o WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

A Azure Functions fornece outra forma de executar programas e scripts. Para uma comparação entre WebJobs e Funções, consulte [Escolha entre Fluxo, Aplicações Lógicas, Funções e WebJobs](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

## <a name="webjob-types"></a>Tipos webJob

A tabela seguinte descreve as diferenças entre WebJobs *contínuos* e *desencadeados.*


|Contínuo  |Desencadeado  |
|---------|---------|
| Começa imediatamente quando o WebJob é criado. Para evitar que o trabalho termine, o programa ou o guião normalmente faz o seu trabalho dentro de um ciclo interminável. Se o trabalho acabar, pode reiniciá-lo. | Só começa quando acionado manualmente ou num horário. |
| Funciona em todas as instâncias em que a aplicação web funciona. Pode restringir opcionalmente o WebJob a uma única instância. |Funciona numa única instância que o Azure seleciona para equilibrar a carga.|
| Suporta depuração remota. | Não suporta depuração remota.|

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

## <a name="acceptablefiles"></a>Tipos de ficheiros suportados para scripts ou programas

Os seguintes tipos de ficheiros são suportados:

* .cmd, .bat, .exe (usando Windows cmd)
* .ps1 (usando powerShell)
* .sh (usando Bash)
* .php (utilizando PHP)
* .py (usando Python)
* .js (usando node.js)
* .jar (usando Java)

## <a name="CreateContinuous"></a>Criar um WebJob contínuo

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. No [portal Azure,](https://portal.azure.com)vá à página do **App Service** da sua aplicação web do App Service, app API ou aplicação móvel.

2. Selecione **WebJobs**.

   ![Selecione WebJobs](./media/web-sites-create-web-jobs/select-webjobs.png)

2. Na página **WebJobs,** selecione **Adicionar**.

    ![Página WebJob](./media/web-sites-create-web-jobs/wjblade.png)

3. Utilize as definições **add WebJob** conforme especificado na tabela.

   ![Adicionar página WebJob](./media/web-sites-create-web-jobs/addwjcontinuous.png)

   | Definição      | Valor da amostra   | Descrição  |
   | ------------ | ----------------- | ------------ |
   | **Nome** | myContinuousWebJob | Um nome único dentro de uma aplicação de Serviço de Aplicações. Deve começar com uma letra ou um número e não pode conter caracteres especiais que não "-" e "_". |
   | **Upload de ficheiros** | ConsoleApp.zip | Um ficheiro *.zip* que contenha o ficheiro executável ou script, bem como quaisquer ficheiros de suporte necessários para executar o programa ou script. Os tipos de ficheiros executáveis ou scripts suportados estão listados na secção de tipos de [ficheiros suportados.](#acceptablefiles) |
   | **Tipo** | Contínuo | Os [tipos WebJob](#webjob-types) são descritos anteriormente neste artigo. |
   | **Dimensionar** | Várias instâncias | Disponível apenas para WebJobs Contínuos. Determina se o programa ou script funciona em todas as instâncias ou apenas uma instância. A opção de correr em várias instâncias não se aplica aos níveis de preços Gratuitos ou [Partilhados](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). | 

4. Clique em **OK**.

   O novo WebJob aparece na página **WebJobs.**

   ![Lista de WebJobs](./media/web-sites-create-web-jobs/listallwebjobs.png)

2. Para parar ou reiniciar um WebJob contínuo, clique no WebJob na lista e clique em **Parar** ou **Iniciar**.

    ![Pare um WebJob contínuo](./media/web-sites-create-web-jobs/continuousstop.png)

## <a name="CreateOnDemand"></a>Criar um WebJob desencadeado manualmente

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. No [portal Azure,](https://portal.azure.com)vá à página do **App Service** da sua aplicação web do App Service, app API ou aplicação móvel.

2. Selecione **WebJobs**.

   ![Selecione WebJobs](./media/web-sites-create-web-jobs/select-webjobs.png)

2. Na página **WebJobs,** selecione **Adicionar**.

    ![Página WebJob](./media/web-sites-create-web-jobs/wjblade.png)

3. Utilize as definições **add WebJob** conforme especificado na tabela.

   ![Adicionar página WebJob](./media/web-sites-create-web-jobs/addwjtriggered.png)

   | Definição      | Valor da amostra   | Descrição  |
   | ------------ | ----------------- | ------------ |
   | **Nome** | myTriggeredWebJob | Um nome único dentro de uma aplicação de Serviço de Aplicações. Deve começar com uma letra ou um número e não pode conter caracteres especiais que não "-" e "_".|
   | **Upload de ficheiros** | ConsoleApp.zip | Um ficheiro *.zip* que contenha o ficheiro executável ou script, bem como quaisquer ficheiros de suporte necessários para executar o programa ou script. Os tipos de ficheiros executáveis ou scripts suportados estão listados na secção de tipos de [ficheiros suportados.](#acceptablefiles) |
   | **Tipo** | Desencadeado | Os [tipos WebJob](#webjob-types) são descritos anteriormente neste artigo. |
   | **Acionadores** | Manual | |

4. Clique em **OK**.

   O novo WebJob aparece na página **WebJobs.**

   ![Lista de WebJobs](./media/web-sites-create-web-jobs/listallwebjobs.png)

7. Para executar o WebJob, clique no seu nome na lista e clique em **Executar**.
   
    ![Executar WebJob](./media/web-sites-create-web-jobs/runondemand.png)

## <a name="CreateScheduledCRON"></a>Criar um WebJob agendado

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. No [portal Azure,](https://portal.azure.com)vá à página do **App Service** da sua aplicação web do App Service, app API ou aplicação móvel.

2. Selecione **WebJobs**.

   ![Selecione WebJobs](./media/web-sites-create-web-jobs/select-webjobs.png)

2. Na página **WebJobs,** selecione **Adicionar**.

   ![Página WebJob](./media/web-sites-create-web-jobs/wjblade.png)

3. Utilize as definições **add WebJob** conforme especificado na tabela.

   ![Adicionar página WebJob](./media/web-sites-create-web-jobs/addwjscheduled.png)

   | Definição      | Valor da amostra   | Descrição  |
   | ------------ | ----------------- | ------------ |
   | **Nome** | myScheduledWebJob | Um nome único dentro de uma aplicação de Serviço de Aplicações. Deve começar com uma letra ou um número e não pode conter caracteres especiais que não "-" e "_". |
   | **Upload de ficheiros** | ConsoleApp.zip | Um ficheiro *.zip* que contenha o ficheiro executável ou script, bem como quaisquer ficheiros de suporte necessários para executar o programa ou script. Os tipos de ficheiros executáveis ou scripts suportados estão listados na secção de tipos de [ficheiros suportados.](#acceptablefiles) |
   | **Tipo** | Desencadeado | Os [tipos WebJob](#webjob-types) são descritos anteriormente neste artigo. |
   | **Acionadores** | Agendado | Para que o agendamento funcione de forma fiável, ative a funcionalidade Always On. Always On está disponível apenas nos níveis de preços Básico, Standard e Premium.|
   | **Expressão CRON** | 0 0/20 * * * * | [As expressões cron](#ncrontab-expressions) são descritas na secção seguinte. |

4. Clique em **OK**.

   O novo WebJob aparece na página **WebJobs.**

   ![Lista de WebJobs](./media/web-sites-create-web-jobs/listallwebjobs.png)

## <a name="ncrontab-expressions"></a>Expressões NCRONTAB

Pode introduzir uma [expressão NCRONTAB](../azure-functions/functions-bindings-timer.md#ncrontab-expressions) no portal ou incluir um ficheiro `settings.job` na raiz do seu ficheiro WebJob *.zip,* como no seguinte exemplo:

```json
{
    "schedule": "0 */15 * * * *"
}
```

Para saber mais, consulte [Agendar um WebJob desencadeado](webjobs-dotnet-deploy-vs.md#scheduling-a-triggered-webjob).

[!INCLUDE [webjobs-cron-timezone-note](../../includes/webjobs-cron-timezone-note.md)]

## <a name="ViewJobHistory"></a>Ver o histórico do trabalho

1. Selecione o WebJob para o qual pretende ver o histórico e, em seguida, selecione o botão **Logs.**
   
   ![Botão de logs](./media/web-sites-create-web-jobs/wjbladelogslink.png)

2. Na página **WebJob Details,** selecione um momento para ver detalhes para uma execução.
   
   ![Detalhes do WebJob](./media/web-sites-create-web-jobs/webjobdetails.png)

3. Na página **WebJob Executar Detalhes,** selecione **Toggle Output** para ver o texto do conteúdo do registo.
   
    ![Detalhes de execução de trabalho web](./media/web-sites-create-web-jobs/webjobrundetails.png)

   Para ver o texto de saída numa janela separada do navegador, selecione **download**. Para descarregar o texto em si, clique no **descarregamento** e use as opções do seu navegador para guardar o conteúdo do ficheiro.
   
5. Selecione o link **webJobs** breadcrumb na parte superior da página para ir a uma lista de WebJobs.

    ![Migalha de pão WebJob](./media/web-sites-create-web-jobs/breadcrumb.png)
   
    ![Lista de WebJobs no dashboard de história](./media/web-sites-create-web-jobs/webjobslist.png)
   
## <a name="NextSteps"></a> Passos seguintes

O Azure WebJobs SDK pode ser usado com WebJobs para simplificar muitas tarefas de programação. Para mais informações, consulte [O Que é o WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

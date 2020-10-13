---
title: Executar tarefas de fundo com WebJobs
description: Aprenda a usar WebJobs para executar tarefas de fundo no Azure App Service. Escolha entre uma variedade de formatos de script e execute-os com expressões CRON.
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: glenga
ms.reviewer: msangapu;suwatch;pbatum;naren.soni
ms.custom: seodec18
ms.openlocfilehash: 235d82e54c79350f110ab0cda4f4b672e396c61d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91652011"
---
# <a name="run-background-tasks-with-webjobs-in-azure-app-service"></a>Executar tarefas de fundo com WebJobs no Azure App Service

Este artigo mostra como implementar WebJobs utilizando o [portal Azure](https://portal.azure.com) para carregar um executável ou script. Para obter informações sobre como desenvolver e implementar WebJobs utilizando o Visual Studio, consulte [Implementar WebJobs utilizando o Visual Studio](webjobs-dotnet-deploy-vs.md).

## <a name="overview"></a>Descrição geral
WebJobs é uma funcionalidade do [Azure App Service](index.yml) que lhe permite executar um programa ou script no mesmo caso que uma aplicação web, app API ou aplicativo móvel. Não há custo adicional para usar WebJobs.

> [!IMPORTANT]
> O WebJobs ainda não está suportado para o Serviço de Aplicações em Linux.

O Azure WebJobs SDK pode ser usado com WebJobs para simplificar muitas tarefas de programação. Para mais informações, consulte [o que é o WebJobs SDK.](https://github.com/Azure/azure-webjobs-sdk/wiki)

As Funções Azure fornecem outra forma de executar programas e scripts. Para uma comparação entre WebJobs e Funções, consulte [Escolha entre Fluxo, Aplicações Lógicas, Funções e WebJobs](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

## <a name="webjob-types"></a>Tipos webJob

A tabela seguinte descreve as diferenças entre WebJobs *contínuos* e *desencadeados.*


|Contínuo  |Desencadeado  |
|---------|---------|
| Começa imediatamente quando o WebJob é criado. Para evitar que o trabalho termine, o programa ou o script normalmente faz o seu trabalho dentro de um ciclo interminável. Se o trabalho acabar, pode reiniciá-lo. | Só começa quando acionado manualmente ou com um horário. |
| Executa em todos os casos que a aplicação web executa. Pode, opcionalmente, restringir o WebJob a um único caso. |Executa num único caso que o Azure seleciona para o equilíbrio de carga.|
| Suporta depurações remotas. | Não suporta depurar remotamente.|

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

## <a name="supported-file-types-for-scripts-or-programs"></a><a name="acceptablefiles"></a>Tipos de ficheiros suportados para scripts ou programas

São suportados os seguintes tipos de ficheiro:

* .cmd, .bat, .exe (usando o Windows cmd)
* .ps1 (usando PowerShell)
* .sh (usando Bash)
* .php (usando PHP)
* .py (usando Python)
* .js (utilizando Node.js)
* .jar (usando Java)

## <a name="create-a-continuous-webjob"></a><a name="CreateContinuous"></a> Criar um WebJob contínuo

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. No [portal Azure,](https://portal.azure.com)aceda à página de Serviço de **Aplicações** da sua aplicação web Do Serviço de Aplicações, app API ou aplicação móvel.

2. Selecione **WebJobs**.

   ![Selecione WebJobs](./media/web-sites-create-web-jobs/select-webjobs.png)

2. Na página **WebJobs,** selecione **Adicionar**.

    ![Página WebJob](./media/web-sites-create-web-jobs/wjblade.png)

3. Utilize as definições **Add WebJob** conforme especificado na tabela.

   ![Screenshot que mostra as definições do Add WebJob que precisa de configurar.](./media/web-sites-create-web-jobs/addwjcontinuous.png)

   | Definição      | Valor da amostra   | Descrição  |
   | ------------ | ----------------- | ------------ |
   | **Nome** | myContinuousWebJob | Um nome único dentro de uma aplicação do Serviço de Aplicações. Deve começar com uma letra ou um número e não pode conter caracteres especiais que não "-" e "_". |
   | **Upload de ficheiros** | ConsoleApp.zip | Um ficheiro *.zip* que contenha o seu ficheiro executável ou script, bem como quaisquer ficheiros de suporte necessários para executar o programa ou script. Os tipos suportados de ficheiros executáveis ou script estão listados na secção [de tipos de ficheiros suportados.](#acceptablefiles) |
   | **Tipo** | Contínuo | Os [tipos webJob](#webjob-types) são descritos anteriormente neste artigo. |
   | **Dimensionamento** | Várias instâncias | Disponível apenas para WebJobs contínuos. Determina se o programa ou o script são executados em todas as instâncias ou apenas num caso. A opção de correr em várias instâncias não se aplica aos [níveis de preços gratuitos](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)ou partilhados . | 

4. Clique em **OK**.

   O novo WebJob aparece na página **WebJobs.**

   ![Lista de WebJobs](./media/web-sites-create-web-jobs/listallwebjobs.png)

2. Para parar ou reiniciar um WebJob contínuo, clique com o direito no WebJob na lista e clique em **Stop** ou **Start**.

    ![Pare um WebJob contínuo](./media/web-sites-create-web-jobs/continuousstop.png)

## <a name="create-a-manually-triggered-webjob"></a><a name="CreateOnDemand"></a> Criar um WebJob ativado manualmente

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. No [portal Azure,](https://portal.azure.com)aceda à página de Serviço de **Aplicações** da sua aplicação web Do Serviço de Aplicações, app API ou aplicação móvel.

2. Selecione **WebJobs**.

   ![Selecione WebJobs](./media/web-sites-create-web-jobs/select-webjobs.png)

2. Na página **WebJobs,** selecione **Adicionar**.

    ![Página WebJob](./media/web-sites-create-web-jobs/wjblade.png)

3. Utilize as definições **Add WebJob** conforme especificado na tabela.

   ![Screenshot que mostra as definições que precisam de ser definidas para criar um WebJob ativado manualmente.](./media/web-sites-create-web-jobs/addwjtriggered.png)

   | Definição      | Valor da amostra   | Descrição  |
   | ------------ | ----------------- | ------------ |
   | **Nome** | myTriggeredWebJob | Um nome único dentro de uma aplicação do Serviço de Aplicações. Deve começar com uma letra ou um número e não pode conter caracteres especiais que não "-" e "_".|
   | **Upload de ficheiros** | ConsoleApp.zip | Um ficheiro *.zip* que contenha o seu ficheiro executável ou script, bem como quaisquer ficheiros de suporte necessários para executar o programa ou script. Os tipos suportados de ficheiros executáveis ou script estão listados na secção [de tipos de ficheiros suportados.](#acceptablefiles) |
   | **Tipo** | Desencadeado | Os [tipos webJob](#webjob-types) são descritos anteriormente neste artigo. |
   | **Acionadores** | Manual | |

4. Clique em **OK**.

   O novo WebJob aparece na página **WebJobs.**

   ![Lista de WebJobs](./media/web-sites-create-web-jobs/listallwebjobs.png)

7. Para executar o WebJob, clique com o botão direito no seu nome na lista e clique em **Executar**.
   
    ![Executar WebJob](./media/web-sites-create-web-jobs/runondemand.png)

## <a name="create-a-scheduled-webjob"></a><a name="CreateScheduledCRON"></a> Criar um WebJob programado

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. No [portal Azure,](https://portal.azure.com)aceda à página de Serviço de **Aplicações** da sua aplicação web Do Serviço de Aplicações, app API ou aplicação móvel.

2. Selecione **WebJobs**.

   ![Selecione WebJobs](./media/web-sites-create-web-jobs/select-webjobs.png)

2. Na página **WebJobs,** selecione **Adicionar**.

   ![Página WebJob](./media/web-sites-create-web-jobs/wjblade.png)

3. Utilize as definições **Add WebJob** conforme especificado na tabela.

   ![Adicionar página WebJob](./media/web-sites-create-web-jobs/addwjscheduled.png)

   | Definição      | Valor da amostra   | Descrição  |
   | ------------ | ----------------- | ------------ |
   | **Nome** | mySchededWebJob | Um nome único dentro de uma aplicação do Serviço de Aplicações. Deve começar com uma letra ou um número e não pode conter caracteres especiais que não "-" e "_". |
   | **Upload de ficheiros** | ConsoleApp.zip | Um ficheiro *.zip* que contenha o seu ficheiro executável ou script, bem como quaisquer ficheiros de suporte necessários para executar o programa ou script. Os tipos suportados de ficheiros executáveis ou script estão listados na secção [de tipos de ficheiros suportados.](#acceptablefiles) |
   | **Tipo** | Desencadeado | Os [tipos webJob](#webjob-types) são descritos anteriormente neste artigo. |
   | **Acionadores** | Agendado | Para que o agendamento funcione de forma fiável, ative a funcionalidade Always On. Always On está disponível apenas nos níveis de preços Básico, Standard e Premium.|
   | **Expressão CRON** | 0 0/20 * * * * | [As expressões cron](#ncrontab-expressions) são descritas na secção seguinte. |

4. Clique em **OK**.

   O novo WebJob aparece na página **WebJobs.**

   ![Lista de WebJobs](./media/web-sites-create-web-jobs/listallwebjobs.png)

## <a name="ncrontab-expressions"></a>Expressões NCRONTAB

Pode introduzir uma [expressão NCRONTAB](../azure-functions/functions-bindings-timer.md#ncrontab-expressions) no portal ou incluir um `settings.job` ficheiro na raiz do seu ficheiro WebJob.zip, como no seguinte exemplo: *.zip*

```json
{
    "schedule": "0 */15 * * * *"
}
```

Para saber mais, consulte [Agendar um WebJob desencadeado.](webjobs-dotnet-deploy-vs.md#scheduling-a-triggered-webjob)

[!INCLUDE [webjobs-cron-timezone-note](../../includes/webjobs-cron-timezone-note.md)]

## <a name="view-the-job-history"></a><a name="ViewJobHistory"></a> Ver a história do trabalho

1. Selecione o WebJob para o qual deseja ver o histórico e, em seguida, selecione o botão **'Registares'.**
   
   ![Botão de registos](./media/web-sites-create-web-jobs/wjbladelogslink.png)

2. Na página **WebJob Details,** selecione uma hora para ver detalhes para uma corrida.
   
   ![Detalhes do WebJob](./media/web-sites-create-web-jobs/webjobdetails.png)

3. Na página **'Detalhes',** selecione **Toggle Output** para ver o texto do conteúdo do registo.
   
    ![Detalhes de execução de trabalho na Web](./media/web-sites-create-web-jobs/webjobrundetails.png)

   Para ver o texto de saída numa janela separada do navegador, selecione **download**. Para descarregar o texto em si, clique no **download** e use as opções do seu navegador para guardar o conteúdo do ficheiro.
   
5. Selecione o link de migalhas de pão **WebJobs** no topo da página para ir a uma lista de WebJobs.

    ![Migalha de pão WebJob](./media/web-sites-create-web-jobs/breadcrumb.png)
   
    ![Lista de WebJobs no painel de instrumentos de história](./media/web-sites-create-web-jobs/webjobslist.png)
   
## <a name="next-steps"></a><a name="NextSteps"></a> Próximos passos

O Azure WebJobs SDK pode ser usado com WebJobs para simplificar muitas tarefas de programação. Para mais informações, consulte [o que é o WebJobs SDK.](https://github.com/Azure/azure-webjobs-sdk/wiki)

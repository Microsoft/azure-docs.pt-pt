---
title: Desenvolver e implantar trabalhos Web usando o Visual Studio – Azure
description: Saiba como desenvolver e implantar Azure WebJobs para Azure App serviço usando o Visual Studio.
author: ggailey777
manager: gwallace
ms.assetid: a3a9d320-1201-4ac8-9398-b4c9535ba755
ms.service: app-service
ms.topic: conceptual
ms.custom: vs-azure
ms.date: 02/18/2019
ms.author: glenga
ms.reviewer: david.ebbo;suwatch;pbatum;naren.soni
ms.openlocfilehash: ac458b01135be8628fbf939e310f8bda02b8d290
ms.sourcegitcommit: 9858ab651a520c26f0ed18215e650efbf1fc5de9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2019
ms.locfileid: "72303567"
---
# <a name="develop-and-deploy-webjobs-using-visual-studio---azure-app-service"></a>Desenvolver e implantar trabalhos Web usando o Visual Studio-serviço de Azure App

Este artigo explica como usar o Visual Studio para implantar um projeto de aplicativo de console em um aplicativo Web no [serviço de aplicativo](overview.md) como um [WebJob do Azure](https://go.microsoft.com/fwlink/?LinkId=390226). Para obter informações sobre como implantar trabalhos Web usando o [portal do Azure](https://portal.azure.com), consulte [executar tarefas em segundo plano com trabalhos](webjobs-create.md)Web.

Você pode publicar vários webjobs em um único aplicativo Web. Certifique-se de que cada WebJob em um aplicativo Web tenha um nome exclusivo.

A versão 3. x do [SDK do Azure WebJobs](webjobs-sdk-how-to.md) permite que você desenvolva trabalhos Web que são executados como aplicativos .NET Core ou .NET Framework aplicativos, enquanto a versão 2. x dá suporte apenas à .NET Framework. A maneira como você implanta um projeto de trabalhos Web é diferente para projetos do .NET Core versus .NET Framework.

## <a name="webjobs-as-net-core-console-apps"></a>Trabalhos Web como aplicativos de console do .NET Core

Ao usar a versão 3. x dos trabalhos Web, você pode criar e publicar trabalhos Web como aplicativos de console do .NET Core. Para obter as instruções passo a passo para criar e publicar um aplicativo de console do .NET Core no Azure como um WebJob, consulte Introdução [ao SDK do Azure WebJobs para processamento em segundo plano orientado a eventos](webjobs-sdk-get-started.md).

> [!NOTE]
> Os webjobs do .NET Core não podem ser vinculados a projetos da Web. Se você precisar implantar seu WebJob com um aplicativo Web, deverá [criar seu webjob como um aplicativo de console .NET Framework](#webjobs-as-net-framework-console-apps).  

### <a name="deploy-to-azure-app-service"></a>Implementar no Serviço de Aplicações do Azure

A publicação de um WebJob do .NET Core no serviço de aplicativo do Visual Studio usa as mesmas ferramentas que a publicação de um aplicativo ASP.NET Core.

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)] 

### <a name="webjob-types"></a>Tipos de WebJob

Por padrão, um WebJob publicado a partir de um projeto de console do .NET Core é executado somente quando disparado ou sob demanda. Você também pode atualizar o projeto para ser [executado em um agendamento](#scheduled-execution) ou executado continuamente.

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

#### <a name="scheduled-execution"></a>Execução agendada

Quando você publica um aplicativo de console do .NET Core no Azure, um novo arquivo *Settings. Job* é adicionado ao projeto. Use esse arquivo para definir um agendamento de execução para seu WebJob. Para obter mais informações, consulte [agendando um WebJob disparado](#scheduling-a-triggered-webjob).

#### <a name="continuous-execution"></a>Execução contínua

Você pode usar o Visual Studio para alterar o trabalho Web para ser executado continuamente quando o Always On estiver habilitado no Azure.

1. Se você ainda não tiver feito isso, [publique o projeto no Azure](#deploy-to-azure-app-service).

1. No **Explorador de Soluções**, clique com o botão direito do rato no projeto e selecione **Publicar**.

1. Na guia **publicar** , escolha **configurações**. 

1. Na caixa de diálogo **configurações de perfil** , escolha **contínuo** para o **tipo WebJob**e escolha **salvar**.

    ![Caixa de diálogo Configurações de publicação para um WebJob](./media/webjobs-dotnet-deploy-vs/publish-settings.png)

1. Selecione **publicar** para republicar o WebJob com as configurações atualizadas.

## <a name="webjobs-as-net-framework-console-apps"></a>Trabalhos Web como aplicativos de console .NET Framework  

Quando o Visual Studio implanta um projeto de aplicativo de console .NET Framework habilitado para trabalhos Web, ele copia os arquivos de tempo de execução para a pasta apropriada no aplicativo (*App_Data/Jobs/Continuous* para webjobs contínuos e *App_Data/Jobs/disparados* para Trabalhos Web agendados ou sob demanda).

Um projeto habilitado para trabalhos Web tem os seguintes itens adicionados a ele:

* O pacote NuGet [Microsoft. Web. webjobs. publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) .
* Um arquivo [webjob-Publish-Settings. JSON](#publishsettings) que contém as configurações de implantação e Agendador. 

![Diagrama mostrando o que é adicionado a um aplicativo de console para habilitar a implantação como um WebJob](./media/webjobs-dotnet-deploy-vs/convert.png)

Você pode adicionar esses itens a um projeto de aplicativo de console existente ou usar um modelo para criar um novo projeto de aplicativo de console habilitado para trabalhos Web. 

Você pode implantar um projeto como um WebJob por si próprio ou vinculá-lo a um projeto Web para que ele seja implantado automaticamente sempre que você implantar o projeto Web. Para vincular projetos do, o Visual Studio inclui o nome do projeto habilitado para trabalhos Web em um arquivo [webjobs-lista. JSON](#webjobslist) no projeto.

![Diagrama mostrando a vinculação do projeto WebJob ao projeto Web](./media/webjobs-dotnet-deploy-vs/link.png)

### <a name="prerequisites"></a>Pré-requisitos

Se você estiver usando o Visual Studio 2015, instale o [SDK do Azure para .net (visual studio 2015)](https://azure.microsoft.com/downloads/).

Se você estiver usando o Visual Studio 2017, instale a [carga de trabalho de desenvolvimento do Azure](https://docs.microsoft.com/visualstudio/install/install-visual-studio#step-4---choose-workloads).

### <a id="convert"></a>Habilitar a implantação de trabalhos Web para um projeto de aplicativo de console existente

Tem duas opções:

* [Habilite a implantação automática com um projeto Web](#convertlink).

  Configure um projeto de aplicativo de console existente para que ele seja implantado automaticamente como um WebJob quando você implantar um projeto Web. Use essa opção quando desejar executar o WebJob no mesmo aplicativo Web no qual você executa o aplicativo Web relacionado.

* [Habilite a implantação sem um projeto Web](#convertnolink).

  Configure um projeto de aplicativo de console existente para implantar como um WebJob por si só, sem nenhum link para um projeto Web. Use essa opção quando desejar executar um WebJob em um aplicativo Web por si só, sem nenhum aplicativo Web em execução no aplicativo Web. Talvez você queira fazer isso para poder dimensionar seus recursos do WebJob independentemente dos recursos do aplicativo Web.

#### <a id="convertlink"></a>Habilitar a implantação automática de webjobs com um projeto Web

1. Clique com o botão direito do mouse no projeto Web no **Gerenciador de soluções**e clique em **Adicionar** > **projeto existente como WebJob do Azure**.
   
    ![Projeto existente como WebJob do Azure](./media/webjobs-dotnet-deploy-vs/eawj.png)
   
    A caixa de diálogo [Adicionar trabalho Web do Azure](#configure) é exibida.
2. Na lista suspensa **nome do projeto** , selecione o projeto de aplicativo de console a ser adicionado como um WebJob.
   
    ![Selecionando projeto na caixa de diálogo Adicionar trabalho Web do Azure](./media/webjobs-dotnet-deploy-vs/aaw1.png)
3. Conclua a caixa de diálogo [Adicionar trabalho Web do Azure](#configure) e clique em **OK**. 

#### <a id="convertnolink"></a>Habilitar a implantação de webjobs sem um projeto Web
1. Clique com o botão direito do mouse no projeto de aplicativo de console no **Gerenciador de soluções**e clique em **publicar como Azure WebJob...** . 
   
    ![Publicar como WebJob do Azure](./media/webjobs-dotnet-deploy-vs/paw.png)
   
    A caixa de diálogo [Adicionar trabalho Web do Azure](#configure) é exibida, com o projeto selecionado na caixa **nome do projeto** .
2. Conclua a caixa de diálogo [Adicionar trabalho Web do Azure](#configure) e clique em **OK**.
   
   O assistente **publicar Web** é exibido.  Se você não quiser publicar imediatamente, feche o assistente. As configurações que você inseriu são salvas para quando você deseja [implantar o projeto](#deploy).

### <a id="create"></a>Criar um novo projeto habilitado para trabalhos Web
Para criar um novo projeto habilitado para trabalhos Web, você pode usar o modelo de projeto de aplicativo de console e habilitar a implantação de trabalhos Web, conforme explicado na [seção anterior](#convert). Como alternativa, você pode usar o modelo de novo projeto de trabalhos Web:

* [Usar o modelo de novo projeto de trabalhos Web para um WebJob independente](#createnolink)
  
    Crie um projeto e configure-o para implantar sozinho como um WebJob, sem link para um projeto Web. Use essa opção quando desejar executar um WebJob em um aplicativo Web por si só, sem nenhum aplicativo Web em execução no aplicativo Web. Talvez você queira fazer isso para poder dimensionar seus recursos do WebJob independentemente dos recursos do aplicativo Web.
* [Usar o modelo de novo projeto de trabalhos Web para um WebJob vinculado a um projeto](#createlink)
  
    Crie um projeto que esteja configurado para ser implantado automaticamente como um WebJob quando um projeto Web na mesma solução for implantado. Use essa opção quando desejar executar o WebJob no mesmo aplicativo Web no qual você executa o aplicativo Web relacionado.

> [!NOTE]
> O modelo New-Project de trabalhos Web instala automaticamente pacotes NuGet e inclui código em *Program.cs* para o [SDK de trabalhos](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs)Web. Se você não quiser usar o SDK de trabalhos Web, remova ou altere a instrução `host.RunAndBlock` em *Program.cs*.
> 
> 

#### <a id="createnolink"></a>Usar o modelo de novo projeto de trabalhos Web para um WebJob independente
1. Clique em **arquivo**@no__t-**1 novo projeto**e, na caixa de diálogo **novo projeto** , clique em **nuvem** > **Azure WebJob (.NET Framework)** .
   
    ![Caixa de diálogo novo projeto mostrando o modelo WebJob](./media/webjobs-dotnet-deploy-vs/np.png)
2. Siga as instruções mostradas anteriormente para [tornar o projeto de aplicativo de console um projeto de trabalhos Web independente](#convertnolink).

#### <a id="createlink"></a>Usar o modelo de novo projeto de trabalhos Web para um WebJob vinculado a um projeto
1. Clique com o botão direito do mouse no projeto Web no **Gerenciador de soluções**e clique em **Adicionar** > **novo projeto do Azure WebJob**.
   
    ![Nova entrada de menu do projeto do Azure WebJob](./media/webjobs-dotnet-deploy-vs/nawj.png)
   
    A caixa de diálogo [Adicionar trabalho Web do Azure](#configure) é exibida.
2. Conclua a caixa de diálogo [Adicionar trabalho Web do Azure](#configure) e clique em **OK**.

### <a id="configure"></a>A caixa de diálogo Adicionar trabalho Web do Azure
A caixa de diálogo **Adicionar trabalho Web do Azure** permite que você insira o nome do webjob e a configuração do modo de execução para seu WebJob. 

![Caixa de diálogo Adicionar trabalho Web do Azure](./media/webjobs-dotnet-deploy-vs/aaw2.png)

Os campos nessa caixa de diálogo correspondem aos campos na caixa de diálogo **Adicionar WebJob** da portal do Azure. Para obter mais informações, consulte [executar tarefas em segundo plano com trabalhos](webjobs-create.md)Web.

> [!NOTE]
> * Para obter informações sobre a implantação de linha de comando, consulte [habilitando a linha de comando ou a entrega contínua de Azure WebJobs](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/).
> * Se você implantar um WebJob e, em seguida, decidir que deseja alterar o tipo de trabalho Web e reimplantar, precisará excluir o arquivo *webjobs-Publish-Settings. JSON* . Isso fará com que o Visual Studio mostre as opções de publicação novamente, para que você possa alterar o tipo de WebJob.
> * Se você implantar um WebJob e posteriormente alterar o modo de execução de contínuo para não contínuo ou vice-versa, o Visual Studio criará um novo WebJob no Azure quando você reimplantar. Se você alterar outras configurações de agendamento, mas deixar o modo de execução igual ou alternar entre agendado e sob demanda, o Visual Studio atualizará o trabalho existente em vez de criar um novo.
> 
> 

### <a id="publishsettings"></a>webjob-Publish-Settings. JSON
Quando você configura um aplicativo de console para a implantação de trabalhos Web, o Visual Studio instala o pacote NuGet [Microsoft. Web. webjobs. publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) e armazena informações de agendamento em um arquivo *webjob-Publish-Settings. JSON* no projetoPasta de propriedades do projeto de trabalhos Web. Aqui está um exemplo desse arquivo:

        {
          "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
          "webJobName": "WebJob1",
          "startTime": "null",
          "endTime": "null",
          "jobRecurrenceFrequency": "null",
          "interval": null,
          "runMode": "Continuous"
        }

Você pode editar esse arquivo diretamente e o Visual Studio fornece o IntelliSense. O esquema de arquivo é armazenado em [https://schemastore.org](https://schemastore.org/schemas/json/webjob-publish-settings.json) e pode ser exibido lá.  

### <a id="webjobslist"></a>trabalhos Web-List. JSON
Quando você vincula um projeto habilitado para trabalhos Web a um projeto da Web, o Visual Studio armazena o nome do projeto webjobs em um arquivo *webjobs-lista. JSON* na pasta *Propriedades* do projeto Web. A lista pode conter vários projetos de trabalhos Web, conforme mostrado no exemplo a seguir:

        {
          "$schema": "http://schemastore.org/schemas/json/webjobs-list.json",
          "WebJobs": [
            {
              "filePath": "../ConsoleApplication1/ConsoleApplication1.csproj"
            },
            {
              "filePath": "../WebJob1/WebJob1.csproj"
            }
          ]
        }

Você pode editar esse arquivo diretamente e o Visual Studio fornece o IntelliSense. O esquema de arquivo é armazenado em [https://schemastore.org](https://schemastore.org/schemas/json/webjobs-list.json) e pode ser exibido lá.

### <a id="deploy"></a>Implantar um projeto de trabalhos Web
Um projeto do webjobs que você vinculou a um projeto Web é implantado automaticamente com o projeto Web. Para obter informações sobre a implantação de projeto Web, consulte os **guias de instruções** > **implantar aplicativo** no painel de navegação esquerdo.

Para implantar um projeto de trabalhos Web por si só, clique com o botão direito do mouse no projeto em **Gerenciador de soluções** e clique em **publicar como Azure WebJob...** . 

![Publicar como WebJob do Azure](./media/webjobs-dotnet-deploy-vs/paw.png)

Para um WebJob independente, o mesmo assistente de **publicação da Web** que é usado para projetos da Web é exibido, mas com menos configurações disponíveis para alteração.

## <a name="scheduling-a-triggered-webjob"></a>Agendando um WebJob disparado

O webjobs usa um arquivo *Settings. Job* para determinar quando um WebJob é executado. Use esse arquivo para definir um agendamento de execução para seu WebJob. O exemplo a seguir é executado a cada hora das 9h às 17:00:

```json
{
    "schedule": "0 0 9-17 * * *"
}
```

Esse arquivo deve estar localizado na raiz da pasta webjobs, ao longo do script do WebJob, como `wwwroot\app_data\jobs\triggered\{job name}` ou `wwwroot\app_data\jobs\continuous\{job name}`. Ao implantar um WebJob do Visual Studio, marque as propriedades do arquivo `settings.job` como **copiar se mais recente**. 

Quando você [cria um WebJob na portal do Azure](webjobs-create.md), o arquivo Settings. Job é criado para você.

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

### <a name="cron-expressions"></a>Expressões CRON

O webjobs usa as mesmas expressões CRON para agendamento como o gatilho de temporizador no Azure Functions. Para saber mais sobre o suporte a CRON, consulte o [artigo de referência de gatilho de temporizador](../azure-functions/functions-bindings-timer.md#ncrontab-expressions).

[!INCLUDE [webjobs-cron-timezone-note](../../includes/webjobs-cron-timezone-note.md)]

### <a name="settingjob-reference"></a>configuração. referência de trabalho

As configurações a seguir são suportadas pelos trabalhos Web:

| **Definição** | **Tipo**  | **Descrição** |
| ----------- | --------- | --------------- |
| `is_in_place` | Tudo | Permite que o trabalho seja executado no local sem ser copiado primeiro para uma pasta temporária. Para saber mais, consulte [diretório de trabalho de trabalhos](https://github.com/projectkudu/kudu/wiki/WebJobs#webjob-working-directory)Web. |
| `is_singleton` | Contínuo | Só execute os trabalhos Web em uma única instância quando expandido. Para saber mais, consulte [definir um trabalho contínuo como singleton](https://github.com/projectkudu/kudu/wiki/WebJobs-API#set-a-continuous-job-as-singleton). |
| `schedule` | Ativou | Execute o WebJob em uma agenda baseada em CRON. PARA saber mais, consulte o [artigo de referência do gatilho de temporizador](../azure-functions/functions-bindings-timer.md#ncrontab-expressions). |
| `stopping_wait_time`| Tudo | Permite o controle do comportamento de desligamento. Para saber mais, confira [desligamento normal](https://github.com/projectkudu/kudu/wiki/WebJobs#graceful-shutdown). |

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre o SDK de trabalhos Web](webjobs-sdk-how-to.md)

---
title: Desenvolver e implementar WebJobs usando VS
description: Aprenda a desenvolver WebJobs Azure em Estúdio Visual e implemente-os para o Azure App Service, incluindo a criação de uma tarefa agendada.
author: ggailey777
ms.assetid: a3a9d320-1201-4ac8-9398-b4c9535ba755
ms.topic: conceptual
ms.custom: vs-azure
ms.date: 02/18/2019
ms.author: glenga
ms.reviewer: david.ebbo;suwatch;pbatum;naren.soni
ms.openlocfilehash: feacd463a10bae66dc8fa88a99b9ea60f399e9ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74684175"
---
# <a name="develop-and-deploy-webjobs-using-visual-studio---azure-app-service"></a>Develop and deploy WebJobs using Visual Studio - Azure App Service (Programar e implementar WebJobs com o Visual Studio – Serviço de Aplicações do Azure)

Este artigo explica como usar o Visual Studio para implementar um projeto de Aplicação de Consolas para uma aplicação web no [App Service](overview.md) como um [WebJob Azure.](https://go.microsoft.com/fwlink/?LinkId=390226) Para obter informações sobre como implementar webJobs utilizando o [portal Azure,](https://portal.azure.com)consulte [as tarefas de Fundo de Execução com WebJobs](webjobs-create.md).

Pode publicar vários WebJobs numa única aplicação web. Certifique-se de que cada WebJob numa aplicação web tem um nome único.

A versão 3.x do [Azure WebJobs SDK](webjobs-sdk-how-to.md) permite-lhe desenvolver WebJobs que funcionam como aplicações .NET Core ou .NET Framework, enquanto a versão 2.x suporta apenas o .NET Framework. A forma como implementa um projeto WebJobs é diferente para projetos .NET Core versus .NET Framework.

## <a name="webjobs-as-net-core-console-apps"></a>WebJobs como .NET Core aplicativos de consola

Ao utilizar a versão 3.x do WebJobs, pode criar e publicar webJobs como aplicações de consola .NET Core. Para instruções passo a passo para criar e publicar uma aplicação de consola .NET Core para o Azure como webJob, consulte [Get started with the Azure WebJobs SDK para processamento de fundo orientado por eventos](webjobs-sdk-get-started.md).

> [!NOTE]
> .NET Core WebJobs não pode ser ligado a projetos web. Se precisar de implementar o seu WebJob com uma aplicação web, deve [criar o seu WebJob como uma aplicação de consola .NET Framework](#webjobs-as-net-framework-console-apps).  

### <a name="deploy-to-azure-app-service"></a>Implementar no Serviço de Aplicações do Azure

A publicação de um WebJob .NET Core para App Service do Visual Studio utiliza a mesma ferramenta que a publicação de uma aplicação ASP.NET Core.

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)] 

### <a name="webjob-types"></a>Tipos webJob

Por padrão, um WebJob publicado a partir de um projeto de consola .NET Core só funciona quando desencadeado ou a pedido. Também pode atualizar o projeto para [executar em horário](#scheduled-execution) ou executar continuamente.

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

#### <a name="scheduled-execution"></a>Execução programada

Quando publica uma aplicação de consola .NET Core para o Azure, é adicionado ao projeto um novo ficheiro de *definições.job.* Utilize este ficheiro para definir um calendário de execução para o seu WebJob. Para mais informações, consulte [Agendar um WebJob acionado](#scheduling-a-triggered-webjob).

#### <a name="continuous-execution"></a>Execução contínua

Pode utilizar o Visual Studio para alterar o WebJob para funcionar continuamente quando o Always On está ativado em Azure.

1. Se ainda não o fez, [publique o projeto para o Azure.](#deploy-to-azure-app-service)

1. No **Explorador de Soluções**, clique com o botão direito do rato no projeto e selecione **Publicar**.

1. No separador **Publicar,** escolha **Definições**. 

1. No diálogo **definições** de perfil, escolha **"Contínuo** para **o Tipo WebJob**", e escolha **Guardar**.

    ![Publique o diálogo de definições para um WebJob](./media/webjobs-dotnet-deploy-vs/publish-settings.png)

1. Selecione **Publicar** para republicar o WebJob com as definições atualizadas.

## <a name="webjobs-as-net-framework-console-apps"></a>WebJobs como .NET Aplicativos de consola-quadro  

Quando o Visual Studio implementa um projeto de aplicação de consolas de estrutura .NET ativado pela WebJobs, copia ficheiros de tempo de execução para a pasta apropriada na aplicação web *(App_Data/jobs/continuous* para WebJobs contínuos e *App_Data/jobs/triggerpara* WebJobs programado ou a pedido).

Um projeto ativado pela WebJobs tem os seguintes itens adicionados:

* O [pacote Microsoft.web.webjobs.Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) nuget.
* Um ficheiro [webjob-publish-settings.json](#publishsettings) que contém definições de implantação e programador. 

![Diagrama mostrando o que é adicionado a uma App de Consola para permitir a implementação como um WebJob](./media/webjobs-dotnet-deploy-vs/convert.png)

Pode adicionar estes itens a um projeto de aplicação de consola existente ou utilizar um modelo para criar um novo projeto de aplicação de consola santiva ativado pelo WebJobs. 

Pode implementar um projeto como WebJob por si só, ou ligá-lo a um projeto web para que ele implemente automaticamente sempre que implementa o projeto web. Para ligar projetos, o Visual Studio inclui o nome do projeto ativado pela WebJobs num ficheiro [webjobs-list.json](#webjobslist) no projeto web.

![Diagrama mostrando projeto WebJob ligando ao projeto web](./media/webjobs-dotnet-deploy-vs/link.png)

### <a name="prerequisites"></a>Pré-requisitos

Se estiver a utilizar o Visual Studio 2015, instale o [Azure SDK para .NET (Visual Studio 2015)](https://azure.microsoft.com/downloads/).

Se estiver a utilizar o Visual Studio 2017, instale a carga de trabalho de [desenvolvimento do Azure.](https://docs.microsoft.com/visualstudio/install/install-visual-studio#step-4---choose-workloads)

### <a name="enable-webjobs-deployment-for-an-existing-console-application-project"></a><a id="convert"></a>Ativar a implementação webJobs para um projeto de aplicação de consola existente

Tem duas opções:

* [Ativar a implementação automática com um projeto web](#convertlink).

  Configure um projeto de aplicação de consola existente para que ele implemente automaticamente como um WebJob quando implementa um projeto web. Utilize esta opção quando pretender executar o seu WebJob na mesma aplicação web na qual executa a aplicação web relacionada.

* [Ativar a implementação sem um projeto web](#convertnolink).

  Configure um projeto de aplicação de consola existente para implementar como um WebJob por si só, sem qualquer ligação a um projeto web. Utilize esta opção quando pretender executar um WebJob numa aplicação web por si só, sem que nenhuma aplicação web esteja a funcionar na aplicação web. Talvez queira fazê-lo para poder escalar os seus recursos WebJob independentemente dos seus recursos de aplicação web.

#### <a name="enable-automatic-webjobs-deployment-with-a-web-project"></a><a id="convertlink"></a>Ativar a implementação automática de WebJobs com um projeto web

1. Clique no projeto web no **Solution Explorer**e, em seguida, clique em **Adicionar** > **Projeto Existente como Azure WebJob**.
   
    ![Projeto existente como Azure WebJob](./media/webjobs-dotnet-deploy-vs/eawj.png)
   
    Aparece a caixa de diálogo [Add Azure WebJob.](#configure)
2. Na lista de lançamentos do **nome do Projeto,** selecione o projeto de Aplicação de Consola para adicionar como WebJob.
   
    ![Selecionar projeto no diálogo Add Azure WebJob](./media/webjobs-dotnet-deploy-vs/aaw1.png)
3. Complete o diálogo [Add Azure WebJob](#configure) e, em seguida, clique em **OK**. 

#### <a name="enable-webjobs-deployment-without-a-web-project"></a><a id="convertnolink"></a>Ativar a implementação webJobs sem um projeto web
1. Clique no projeto de aplicação da consola no **Solution Explorer**e clique em Publicar **como Azure WebJob...**. 
   
    ![Publicar como Azure WebJob](./media/webjobs-dotnet-deploy-vs/paw.png)
   
    Aparece a caixa de diálogo [Add Azure WebJob,](#configure) com o projeto selecionado na caixa de nome sinuosa **do Projeto.**
2. Complete a caixa de diálogo [Add Azure WebJob](#configure) e, em seguida, clique em **OK**.
   
   O assistente **da Web publicar** aparece.  Se não quiser publicar imediatamente, feche o assistente. As definições que introduziu são guardadas para quando pretende [implementar o projeto](#deploy).

### <a name="create-a-new-webjobs-enabled-project"></a><a id="create"></a>Criar um novo projeto ativado pela WebJobs
Para criar um novo projeto ativado pelo WebJobs, pode utilizar o modelo do projeto de aplicação de consolae e permitir a implementação do WebJobs, tal como explicado na [secção anterior.](#convert) Como alternativa, pode utilizar o modelo de novo projeto WebJobs:

* [Use o modelo de novo projeto WebJobs para um WebJob independente](#createnolink)
  
    Crie um projeto e configure-o para implementar por si mesmo como um WebJob, sem qualquer ligação a um projeto web. Utilize esta opção quando pretender executar um WebJob numa aplicação web por si só, sem que nenhuma aplicação web esteja a funcionar na aplicação web. Talvez queira fazê-lo para poder escalar os seus recursos WebJob independentemente dos seus recursos de aplicação web.
* [Use o modelo de novo projeto WebJobs para um WebJob ligado a um projeto web](#createlink)
  
    Crie um projeto que esteja configurado para implementar automaticamente como Um WebJob quando um projeto web na mesma solução é implementado. Utilize esta opção quando pretender executar o seu WebJob na mesma aplicação web na qual executa a aplicação web relacionada.

> [!NOTE]
> O modelo de novo projeto WebJobs instala automaticamente pacotes NuGet e inclui código em *Program.cs* para o [WebJobs SDK](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs). Se não quiser utilizar o WebJobs SDK, remova `host.RunAndBlock` ou altere a declaração em *Program.cs*.
> 
> 

#### <a name="use-the-webjobs-new-project-template-for-an-independent-webjob"></a><a id="createnolink"></a>Use o modelo de novo projeto WebJobs para um WebJob independente
1. Clique em **File** > **New Project**, e depois na caixa de diálogo New **Project** clique em **Cloud** > **Azure WebJob (.NET Framework)**.
   
    ![Novo diálogo do projeto mostrando o modelo WebJob](./media/webjobs-dotnet-deploy-vs/np.png)
2. Siga as instruções anteriormente mostradas para tornar o projeto de [Aplicação de Consola um projeto WebJobs independente](#convertnolink).

#### <a name="use-the-webjobs-new-project-template-for-a-webjob-linked-to-a-web-project"></a><a id="createlink"></a>Use o modelo de novo projeto WebJobs para um WebJob ligado a um projeto web
1. Clique no projeto web no **Solution Explorer**e, em seguida, clique em **Adicionar** > **Novo Projeto WebJob Azure**.
   
    ![Entrada no menu do novo projeto WebJob AzurJob](./media/webjobs-dotnet-deploy-vs/nawj.png)
   
    Aparece a caixa de diálogo [Add Azure WebJob.](#configure)
2. Complete a caixa de diálogo [Add Azure WebJob](#configure) e, em seguida, clique em **OK**.

### <a name="the-add-azure-webjob-dialog"></a><a id="configure"></a>O diálogo WebJob Add Azure
O diálogo **Add Azure WebJob** permite introduzir o nome WebJob e executar a definição de modo para o seu WebJob. 

![Adicionar diálogo WebJob Azure](./media/webjobs-dotnet-deploy-vs/aaw2.png)

Os campos deste diálogo correspondem aos campos do diálogo **Add WebJob** do portal Azure. Para mais informações, consulte [executar tarefas de Fundo com WebJobs](webjobs-create.md).

> [!NOTE]
> * Para obter informações sobre a implantação da linha de comando, consulte a linha de comando ativação ou a [entrega contínua de WebJobs Azure](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/).
> * Se implementar um WebJob e decidir que pretende alterar o tipo de WebJob e reimplementar, terá de eliminar o ficheiro *webjobs-publish-settings.json.* Isto fará com que o Visual Studio mostre as opções de publicação novamente, para que possa alterar o tipo de WebJob.
> * Se implementar um WebJob e, mais tarde, alterar o modo de execução de contínuo para não-contínuo ou vice-versa, o Visual Studio cria um novo WebJob em Azure quando reimplanta. Se alterar outras definições de agendamento, mas deixar o modo de execução o mesmo ou alternar entre o Scheduled e o On Demand, o Visual Studio atualiza o trabalho existente em vez de criar um novo.
> 
> 

### <a name="webjob-publish-settingsjson"></a><a id="publishsettings"></a>webjob-publish-settings.json
Quando configura uma aplicação de consola para implementação webJobs, o Visual Studio instala o pacote [Microsoft.Web.WebJobs.Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet e armazena informações de agendamento num ficheiro *webjob-publish-settings.json* na pasta do projeto *Propriedades* do projeto WebJobs. Aqui está um exemplo desse ficheiro:

        {
          "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
          "webJobName": "WebJob1",
          "startTime": "null",
          "endTime": "null",
          "jobRecurrenceFrequency": "null",
          "interval": null,
          "runMode": "Continuous"
        }

Pode editar este ficheiro diretamente, e o Visual Studio fornece o IntelliSense. O esquema do ficheiro é [https://schemastore.org](https://schemastore.org/schemas/json/webjob-publish-settings.json) armazenado e pode ser visto lá.  

### <a name="webjobs-listjson"></a><a id="webjobslist"></a>webjobs-list.json
Quando liga um projeto ativado pela WebJobs a um projeto web, o Visual Studio armazena o nome do projeto WebJobs num ficheiro *webjobs-list.json* na pasta *Propriedades* do projeto web. A lista pode conter vários projetos WebJobs, como mostra o seguinte exemplo:

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

Pode editar este ficheiro diretamente, e o Visual Studio fornece o IntelliSense. O esquema do ficheiro é [https://schemastore.org](https://schemastore.org/schemas/json/webjobs-list.json) armazenado e pode ser visto lá.

### <a name="deploy-a-webjobs-project"></a><a id="deploy"></a>Implementar um projeto WebJobs
Um projeto WebJobs que ligou a um projeto web implementa automaticamente com o projeto web. Para obter informações sobre a implementação do projeto web, consulte **como orientar** > **a aplicação Deploy** na navegação à esquerda.

Para implementar um projeto WebJobs por si só, clique no projeto no **Solution Explorer** e clique em Publicar **como Azure WebJob...**. 

![Publicar como Azure WebJob](./media/webjobs-dotnet-deploy-vs/paw.png)

Para um WebJob independente, aparece o mesmo assistente **Web publish** que é usado para projetos web, mas com menos configurações disponíveis para alterar.

## <a name="scheduling-a-triggered-webjob"></a>Agendar um WebJob acionado

WebJobs usa um ficheiro de *definições.job* para determinar quando um WebJob é executado. Utilize este ficheiro para definir um calendário de execução para o seu WebJob. O seguinte exemplo decorre de hora em hora das 9:00 às 17:00:

```json
{
    "schedule": "0 0 9-17 * * *"
}
```

Este ficheiro deve estar localizado na raiz da pasta WebJobs, juntamente `wwwroot\app_data\jobs\triggered\{job name}` com `wwwroot\app_data\jobs\continuous\{job name}`o script do webJob, como ou . Quando implementar um WebJob do Visual `settings.job` Studio, marque as propriedades dos seus ficheiros como **Copy se for mais recente**. 

Quando [cria um WebJob a partir do portal Azure,](webjobs-create.md)o ficheiro definições.job é criado para si.

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

### <a name="cron-expressions"></a>Expressões cron

WebJobs usa as mesmas expressões cron para agendamento que o gatilho do temporizador nas Funções Azure. Para saber mais sobre o suporte do CRON, consulte o artigo de referência do gatilho do [temporizador](../azure-functions/functions-bindings-timer.md#ncrontab-expressions).

[!INCLUDE [webjobs-cron-timezone-note](../../includes/webjobs-cron-timezone-note.md)]

### <a name="settingjob-reference"></a>definição.referência de trabalho

As seguintes definições são suportadas pelo WebJobs:

| **Definição** | **Tipo**  | **Descrição** |
| ----------- | --------- | --------------- |
| `is_in_place` | Todos | Permite que o trabalho seja executado no lugar sem ser copiado primeiro para uma pasta temporária. Para saber mais, consulte o diretório de [trabalho webJobs.](https://github.com/projectkudu/kudu/wiki/WebJobs#webjob-working-directory) |
| `is_singleton` | Contínuo | Só execute os WebJobs numa única instância quando for dimensionado. Para saber mais, consulte [Definir um trabalho contínuo como singleton](https://github.com/projectkudu/kudu/wiki/WebJobs-API#set-a-continuous-job-as-singleton). |
| `schedule` | Desencadeado | Execute o WebJob num horário baseado em CRON. Para saber mais, consulte o [temporizador de referência.](../azure-functions/functions-bindings-timer.md#ncrontab-expressions) |
| `stopping_wait_time`| Todos | Permite o controlo do comportamento de encerramento. Para saber mais, consulte a [paragem graciosa.](https://github.com/projectkudu/kudu/wiki/WebJobs#graceful-shutdown) |

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre o WebJobs SDK](webjobs-sdk-how-to.md)

---
title: Desenvolver e implementar WebJobs usando VS
description: Aprenda a desenvolver O Azure WebJobs em Visual Studio e implemente-os no Azure App Service, incluindo a criação de uma tarefa programada.
author: ggailey777
ms.assetid: a3a9d320-1201-4ac8-9398-b4c9535ba755
ms.topic: conceptual
ms.custom: vs-azure
ms.date: 02/18/2019
ms.author: glenga
ms.reviewer: david.ebbo;suwatch;pbatum;naren.soni
ms.openlocfilehash: 971ab39ddf4b383f92f0cd81e5258ad357e76e99
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85857068"
---
# <a name="develop-and-deploy-webjobs-using-visual-studio---azure-app-service"></a>Develop and deploy WebJobs using Visual Studio - Azure App Service (Programar e implementar WebJobs com o Visual Studio – Serviço de Aplicações do Azure)

Este artigo explica como usar o Visual Studio para implementar um projeto de Aplicação de Consolas para uma aplicação web no [Serviço de Aplicações](overview.md) como [um Azure WebJob](https://go.microsoft.com/fwlink/?LinkId=390226). Para obter informações sobre como implementar WebJobs utilizando o [portal Azure,](https://portal.azure.com)consulte [executar tarefas de fundo com WebJobs](webjobs-create.md).

Pode publicar vários WebJobs numa única aplicação web. Certifique-se de que cada WebJob numa aplicação web tem um nome único.

A versão 3.x do [Azure WebJobs SDK](webjobs-sdk-how-to.md) permite desenvolver WebJobs que funcionam como aplicações .NET Core ou .NET Framework, enquanto a versão 2.x suporta apenas o Quadro .NET. A forma como implementa um projeto WebJobs é diferente para projetos .NET Core versus .NET Framework.

## <a name="webjobs-as-net-core-console-apps"></a>WebJobs como .NET Core apps de consolas

Ao utilizar a versão 3.x dos WebJobs, pode criar e publicar WebJobs como aplicações de consola .NET Core. Para obter instruções passo a passo para criar e publicar uma aplicação de consola .NET Core para Azure como WebJob, consulte [Começar com o Azure WebJobs SDK para processamento de fundo orientado para eventos](webjobs-sdk-get-started.md).

> [!NOTE]
> .NET Core WebJobs não pode ser ligado a projetos web. Se precisar de implementar o seu WebJob com uma aplicação web, deverá [criar o seu WebJob como uma aplicação de consola .NET Framework](#webjobs-as-net-framework-console-apps).  

### <a name="deploy-to-azure-app-service"></a>Implementar no Serviço de Aplicações do Azure

A publicação de um Serviço WebJob .NET Core para App do Visual Studio utiliza a mesma ferramenta que a publicação de uma aplicação Core ASP.NET.

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)] 

### <a name="webjob-types"></a>Tipos webJob

Por padrão, um WebJob publicado a partir de um projeto de consola .NET Core funciona apenas quando desencadeado ou a pedido. Também pode atualizar o projeto para [executar em um horário](#scheduled-execution) ou executado continuamente.

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

#### <a name="scheduled-execution"></a>Execução programada

Quando publica uma aplicação de consola .NET Core para o Azure, um novo ficheiro *de definições.job* é adicionado ao projeto. Utilize este ficheiro para definir um calendário de execução para o seu WebJob. Para obter mais informações, consulte [Agendar um WebJob desencadeado.](#scheduling-a-triggered-webjob)

#### <a name="continuous-execution"></a>Execução contínua

Pode utilizar o Visual Studio para alterar o WebJob para funcionar continuamente quando o Always On estiver ativado no Azure.

1. Se ainda não o fez, [publique o projeto ao Azure.](#deploy-to-azure-app-service)

1. No **Explorador de Soluções**, clique com o botão direito do rato no projeto e selecione **Publicar**.

1. No separador **Publicar,** escolha **Definições**. 

1. No diálogo **Definições de Perfil,** escolha **Continua para** **o Tipo WebJob**e escolha **Guardar**.

    ![Publique o diálogo de Definições para um WebJob](./media/webjobs-dotnet-deploy-vs/publish-settings.png)

1. Selecione **Publicar** para republicar o WebJob com as definições atualizadas.

## <a name="webjobs-as-net-framework-console-apps"></a>WebJobs como .NET Framework apps de consolas  

Quando o Visual Studio implementa um projeto de aplicação de consolas framework .NET ativado pelo WebJobs, copia ficheiros de tempo de execução para a pasta apropriada na aplicação web *(App_Data/jobs/continuous* for continuous WebJobs e *App_Data/jobs/triggered* for scheduled or on demand WebJobs).

Um projeto ativado pela WebJobs tem os seguintes itens adicionados:

* O pacote [Microsoft.Web.WebJobs.Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet.
* Uma [webjob-publish-settings.jsno](#publishsettings) ficheiro que contém configurações de implementação e agendamento. 

![Diagrama mostrando o que é adicionado a uma App de Consola para permitir a implementação como WebJob](./media/webjobs-dotnet-deploy-vs/convert.png)

Pode adicionar estes itens a um projeto de aplicação de consola existente ou usar um modelo para criar um novo projeto de aplicação de consola ativado pelo WebJobs. 

Você pode implementar um projeto como um WebJob por si só, ou ligá-lo a um projeto web para que ele implemente automaticamente sempre que implementar o projeto web. Para ligar projetos, o Visual Studio inclui o nome do projeto ativado pela WebJobs numa [webjobs-list.jsem](#webjobslist) arquivo no projeto web.

![Diagrama mostrando projeto WebJob ligando-se ao projeto web](./media/webjobs-dotnet-deploy-vs/link.png)

### <a name="prerequisites"></a>Pré-requisitos

Se estiver a utilizar o Visual Studio 2015, instale o [Azure SDK para .NET (Visual Studio 2015)](https://azure.microsoft.com/downloads/).

Se estiver a utilizar o Visual Studio 2017, instale a carga de trabalho de [desenvolvimento do Azure](https://docs.microsoft.com/visualstudio/install/install-visual-studio#step-4---choose-workloads).

### <a name="enable-webjobs-deployment-for-an-existing-console-application-project"></a><a id="convert"></a>Ativar a implementação do WebJobs para um projeto de aplicação de consola existente

Tem duas opções:

* [Ativar a implementação automática com um projeto web.](#convertlink)

  Configure um projeto de aplicação de consola existente para que ele se implante automaticamente como WebJob quando implementa um projeto web. Utilize esta opção quando pretender executar o seu WebJob na mesma aplicação web em que executou a aplicação web relacionada.

* [Ativar a implementação sem um projeto web](#convertnolink).

  Configure um projeto de Aplicação de Consola existente para implementar como um WebJob por si só, sem ligação a um projeto web. Utilize esta opção quando pretender executar um WebJob numa aplicação web por si só, sem nenhuma aplicação web em execução na aplicação web. É possível que queira fazê-lo para poder escalar os seus recursos WebJob independentemente dos recursos da sua aplicação web.

#### <a name="enable-automatic-webjobs-deployment-with-a-web-project"></a><a id="convertlink"></a>Ativar a implementação automática do WebJobs com um projeto web

1. Clique com o botão direito no projeto web no **Solution Explorer**e, em seguida, clique em **Adicionar**Projeto Existente  >  **como Azure WebJob**.
   
    ![Projeto existente como Azure WebJob](./media/webjobs-dotnet-deploy-vs/eawj.png)
   
    Aparece a caixa de diálogo [Add Azure WebJob.](#configure)
2. Na lista de drop-down **do nome do Projeto,** selecione o projeto de Aplicação de Consola para adicionar como WebJob.
   
    ![Selecionando projeto no diálogo Add Azure WebJob](./media/webjobs-dotnet-deploy-vs/aaw1.png)
3. Complete o diálogo [Add Azure WebJob](#configure) e, em seguida, clique **em OK**. 

#### <a name="enable-webjobs-deployment-without-a-web-project"></a><a id="convertnolink"></a>Ativar a implementação do WebJobs sem um projeto web
1. Clique com o botão direito no projeto de Aplicação de Consola no **Solution Explorer**e, em seguida, clique em Publicar **como Azure WebJob...**. 
   
    ![Publicar como Azure WebJob](./media/webjobs-dotnet-deploy-vs/paw.png)
   
    Aparece a caixa de diálogo [Add Azure WebJob,](#configure) com o projeto selecionado na caixa **de nomes** do Projeto.
2. Complete a caixa de diálogo [Add Azure WebJob](#configure) e, em seguida, clique **em OK**.
   
   O **assistente da Web de publicação** aparece.  Se não quiser publicar imediatamente, feche o assistente. As definições que inseriu estão guardadas para quando [pretender implantar o projeto](#deploy).

### <a name="create-a-new-webjobs-enabled-project"></a><a id="create"></a>Criar um novo projeto ativado pela WebJobs
Para criar um novo projeto ativado pelo WebJobs, pode utilizar o modelo de projeto de aplicação de consola e ativar a implementação do WebJobs, conforme explicado na [secção anterior.](#convert) Como alternativa, pode utilizar o modelo de novo projeto WebJobs:

* [Use o modelo de novo projeto WebJobs para um WebJob independente](#createnolink)
  
    Crie um projeto e configuure-o para implementar por si só como um WebJob, sem qualquer ligação a um projeto web. Utilize esta opção quando pretender executar um WebJob numa aplicação web por si só, sem nenhuma aplicação web em execução na aplicação web. É possível que queira fazê-lo para poder escalar os seus recursos WebJob independentemente dos recursos da sua aplicação web.
* [Use o modelo de novo projeto WebJobs para um WebJob ligado a um projeto web](#createlink)
  
    Crie um projeto configurado para implementar automaticamente como WebJob quando um projeto web na mesma solução é implementado. Utilize esta opção quando pretender executar o seu WebJob na mesma aplicação web em que executou a aplicação web relacionada.

> [!NOTE]
> O modelo de novo projeto WebJobs instala automaticamente pacotes NuGet e inclui código em *Program.cs* para o [WebJobs SDK](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs). Se não quiser utilizar o WebJobs SDK, remova ou altere a `host.RunAndBlock` declaração em *Program.cs*.
> 
> 

#### <a name="use-the-webjobs-new-project-template-for-an-independent-webjob"></a><a id="createnolink"></a>Use o modelo de novo projeto WebJobs para um WebJob independente
1. Clique em **File**  >  **New Project**e, em seguida, na caixa de diálogo new **project** clique em **Cloud**  >  **Azure WebJob (.NET Framework)**.
   
    ![Novo diálogo do projeto mostrando o modelo WebJob](./media/webjobs-dotnet-deploy-vs/np.png)
2. Siga as instruções mostradas anteriormente para [tornar o projeto de Aplicação de Consola num projeto independente da WebJobs.](#convertnolink)

#### <a name="use-the-webjobs-new-project-template-for-a-webjob-linked-to-a-web-project"></a><a id="createlink"></a>Use o modelo de novo projeto WebJobs para um WebJob ligado a um projeto web
1. Clique com o botão direito no projeto web no **Solution Explorer**e, em seguida, clique em **Adicionar**Novo  >  **Projeto WebJob Azure**.
   
    ![Entrada do menu do Novo Azure WebJob Project](./media/webjobs-dotnet-deploy-vs/nawj.png)
   
    Aparece a caixa de diálogo [Add Azure WebJob.](#configure)
2. Complete a caixa de diálogo [Add Azure WebJob](#configure) e, em seguida, clique **em OK**.

### <a name="the-add-azure-webjob-dialog"></a><a id="configure"></a>O diálogo Add Azure WebJob
O diálogo **Add Azure WebJob** permite-lhe introduzir o nome WebJob e executar a definição do modo de funcionamento para o seu WebJob. 

![Adicione o diálogo Azure WebJob](./media/webjobs-dotnet-deploy-vs/aaw2.png)

Os campos deste diálogo correspondem aos campos do diálogo **Add WebJob** do portal Azure. Para obter mais informações, consulte [executar tarefas de fundo com WebJobs](webjobs-create.md).

> [!NOTE]
> * Para obter informações sobre a implementação da linha de comando, consulte [ativar a linha de comando ou a entrega contínua de Azure WebJobs](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/).
> * Se implementar um WebJob e decidir que pretende alterar o tipo de WebJob e recolocar, terá de eliminar a *webjobs-publish-settings.jsno* ficheiro. Isto fará com que o Visual Studio mostre novamente as opções de publicação, para que possa alterar o tipo de WebJob.
> * Se implementar um WebJob e posteriormente alterar o modo de funcionamento de contínuo para não contínuo ou vice-versa, o Visual Studio cria um novo WebJob em Azure quando se recoloca. Se alterar outras definições de agendamento, mas deixar o modo de funcionamento da mesma forma ou alternar entre Programado e On Demand, o Visual Studio atualiza o trabalho existente em vez de criar um novo.
> 
> 

### <a name="webjob-publish-settingsjson"></a><a id="publishsettings"></a>webjob-publish-settings.js
Ao configurar uma aplicação de consola para implementação de WebJobs, o Visual Studio instala o pacote [Microsoft.Web.WebJobs.Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet e armazena informações de agendamento num *webjob-publish-settings.jsem* ficheiro na pasta *propriedades* do projeto WebJobs. Aqui está um exemplo desse ficheiro:

```json
{
  "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
  "webJobName": "WebJob1",
  "startTime": "null",
  "endTime": "null",
  "jobRecurrenceFrequency": "null",
  "interval": null,
  "runMode": "Continuous"
}
```

Pode editar este ficheiro diretamente e o Visual Studio fornece o IntelliSense. O esquema de ficheiros é armazenado [https://schemastore.org](http://schemastore.org/schemas/json/webjob-publish-settings.json) e pode ser visto lá.  

### <a name="webjobs-listjson"></a><a id="webjobslist"></a>webjobs-list.js
Quando liga um projeto ativado pela WebJobs a um projeto web, o Visual Studio armazena o nome do projeto WebJobs numa *webjobs-list.jsem* ficheiro na pasta *Propriedades* do projeto web. A lista pode conter vários projetos WebJobs, como mostra o seguinte exemplo:

```json
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
```

Pode editar este ficheiro diretamente e o Visual Studio fornece o IntelliSense. O esquema de ficheiros é armazenado [https://schemastore.org](http://schemastore.org/schemas/json/webjobs-list.json) e pode ser visto lá.

### <a name="deploy-a-webjobs-project"></a><a id="deploy"></a>Implementar um projeto WebJobs
Um projeto WebJobs que você ligou a um projeto web implementa-se automaticamente com o projeto web. Para obter informações sobre a implementação do projeto web, consulte **como guiar**implementar  >  **a aplicação** na navegação à esquerda.

Para implementar um projeto WebJobs por si só, clique com o botão direito no **Solution Explorer** e clique em **Publicar como Azure WebJob...**. 

![Publicar como Azure WebJob](./media/webjobs-dotnet-deploy-vs/paw.png)

Para um WebJob independente, aparece o mesmo assistente web de **publicação** que é utilizado para projetos web, mas com menos configurações disponíveis para ser alterado.

## <a name="scheduling-a-triggered-webjob"></a>Agendar um WebJob acionado

O WebJobs utiliza um ficheiro *de definições.job* para determinar quando é executado um WebJob. Utilize este ficheiro para definir um calendário de execução para o seu WebJob. O exemplo a seguir passa de hora a hora das 9:00 às 17:00:

```json
{
    "schedule": "0 0 9-17 * * *"
}
```

Este ficheiro deve estar localizado na raiz da pasta WebJobs, juntamente com o script do WebJob, como `wwwroot\app_data\jobs\triggered\{job name}` ou `wwwroot\app_data\jobs\continuous\{job name}` . Quando implementar um WebJob a partir do Visual Studio, marque as propriedades do seu `settings.job` ficheiro como Copy se for mais **recente**. 

Quando [cria um WebJob a partir do portal Azure,](webjobs-create.md)o ficheiro de definições.job é criado para si.

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

### <a name="cron-expressions"></a>Expressões cron

O WebJobs utiliza as mesmas expressões CRON para agendar como o gatilho do temporizador em Funções Azure. Para saber mais sobre o suporte ao CRON, consulte o artigo de referência do gatilho do [temporizador](../azure-functions/functions-bindings-timer.md#ncrontab-expressions).

[!INCLUDE [webjobs-cron-timezone-note](../../includes/webjobs-cron-timezone-note.md)]

### <a name="settingsjob-reference"></a>definições.referência de trabalho

As seguintes definições são suportadas pela WebJobs:

| **Definição** | **Tipo**  | **Descrição** |
| ----------- | --------- | --------------- |
| `is_in_place` | Todos | Permite que o trabalho corra no lugar sem ser copiado pela primeira vez para uma pasta temporária. Para saber mais, consulte [o diretório de trabalho da WebJobs.](https://github.com/projectkudu/kudu/wiki/WebJobs#webjob-working-directory) |
| `is_singleton` | Contínuo | Só execute os WebJobs num único caso quando escalado. Para saber mais, consulte [Definir um trabalho contínuo como singleton](https://github.com/projectkudu/kudu/wiki/WebJobs-API#set-a-continuous-job-as-singleton). |
| `schedule` | Desencadeado | Execute o WebJob num horário baseado em CRON. Para saber mais, consulte o artigo de referência do gatilho do [temporizador](../azure-functions/functions-bindings-timer.md#ncrontab-expressions). |
| `stopping_wait_time`| Todos | Permite o controlo do comportamento de paragem. Para saber mais, veja [o encerramento graceful.](https://github.com/projectkudu/kudu/wiki/WebJobs#graceful-shutdown) |

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Saiba mais sobre o WebJobs SDK](webjobs-sdk-how-to.md)

---
title: Desenvolver e implementar WebJobs usando o Visual Studio
description: Aprenda a desenvolver O Azure WebJobs em Visual Studio e implemente-os no Azure App Service, incluindo a criação de uma tarefa programada.
author: ggailey777
ms.assetid: a3a9d320-1201-4ac8-9398-b4c9535ba755
ms.topic: conceptual
ms.custom: devx-track-csharp, vs-azure
ms.date: 07/30/2020
ms.author: glenga
ms.reviewer: david.ebbo;suwatch;pbatum;naren.soni
ms.openlocfilehash: de10903be86b52b3415b57a53be81e7fd1661f63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89226034"
---
# <a name="develop-and-deploy-webjobs-using-visual-studio"></a>Desenvolver e implementar WebJobs usando o Visual Studio

Este artigo explica como usar o Visual Studio para implementar um projeto de aplicação de consola para uma aplicação web no [Azure App Service](overview.md) como [um Azure WebJob](https://go.microsoft.com/fwlink/?LinkId=390226). Para obter informações sobre como implementar WebJobs utilizando o [portal Azure,](https://portal.azure.com)consulte [executar tarefas de fundo com WebJobs no Azure App Service](webjobs-create.md).

Pode optar por desenvolver um WebJob que funciona como uma [aplicação .NET Core](#webjobs-as-net-core-console-apps) ou uma [aplicação .NET Framework](#webjobs-as-net-framework-console-apps). A versão 3.x do [Azure WebJobs SDK](webjobs-sdk-how-to.md) permite desenvolver WebJobs que funcionam como aplicações .NET Core ou .NET Framework, enquanto a versão 2.x suporta apenas o Quadro .NET. A forma como implementa um projeto WebJobs é diferente para projetos .NET Core do que para projetos .NET Framework.

Pode publicar vários WebJobs numa única aplicação web, desde que cada WebJob numa aplicação web tenha um nome único.

## <a name="webjobs-as-net-core-console-apps"></a>WebJobs como .NET Core apps de consolas

Com a versão 3.x do Azure WebJobs SDK, pode criar e publicar WebJobs como aplicações de consola .NET Core. Para obter instruções passo a passo para criar e publicar uma aplicação de consola .NET Core para Azure como WebJob, consulte [Começar com o Azure WebJobs SDK para processamento de fundo orientado para eventos](webjobs-sdk-get-started.md).

> [!NOTE]
> .NET Core WebJobs não pode ser ligado a projetos web. Se precisar de implementar o seu WebJob com uma aplicação web, [crie os seus WebJobs como uma aplicação de consola .NET Framework](#webjobs-as-net-framework-console-apps).  

### <a name="deploy-to-azure-app-service"></a>Implementar no Serviço de Aplicações do Azure

A publicação de um Serviço de Aplicações Web Core .NET Core para Azure app da Visual Studio utiliza a mesma ferramenta que a publicação de uma aplicação Core ASP.NET.

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)] 

## <a name="webjobs-as-net-framework-console-apps"></a>WebJobs como .NET Framework apps de consolas  

Se utilizar o Visual Studio para implementar um projeto de aplicação de consolas .NET Framework ativado pelo WebJobs, copia ficheiros de tempo de execução para a pasta apropriada na aplicação web *(App_Data/jobs/continuous* for continuous WebJobs e *App_Data/jobs/triggered* for scheduled or on demand WebJobs).

O Visual Studio adiciona os seguintes itens a um projeto ativado pela WebJobs:

* O pacote [Microsoft.Web.WebJobs.Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet.
* Uma [webjob-publish-settings.jsno](#publishsettings) ficheiro que contém configurações de implementação e agendamento. 

![Diagrama mostrando o que é adicionado a uma aplicação de consola para permitir a implementação como WebJob](./media/webjobs-dotnet-deploy-vs/convert.png)

Pode adicionar estes itens a um projeto de aplicação de consola existente ou usar um modelo para criar um novo projeto de aplicação de consola ativado pelo WebJobs. 

Implementar um projeto como webJob por si só, ou ligá-lo a um projeto web para que ele implemente automaticamente sempre que implementar o projeto web. Para ligar projetos, o Visual Studio inclui o nome do projeto ativado pela WebJobs numa [webjobs-list.jsem](#webjobslist) arquivo no projeto web.

![Diagrama mostrando projeto WebJob ligando-se ao projeto web](./media/webjobs-dotnet-deploy-vs/link.png)

### <a name="prerequisites"></a>Pré-requisitos

Instale o Visual Studio 2017 ou o Visual Studio 2019 com a [carga de trabalho de desenvolvimento do Azure.](/visualstudio/install/install-visual-studio#step-4---choose-workloads)

### <a name="enable-webjobs-deployment-for-an-existing-console-app-project"></a><a id="convert"></a> Ativar a implementação do WebJobs para um projeto de aplicações de consola existente

Tem duas opções:

* [Ativar a implementação automática com um projeto web.](#convertlink)

  Configure um projeto de aplicação de consola existente para que ele se implemente automaticamente como WebJob quando implementa um projeto web. Utilize esta opção quando pretender executar o seu WebJob na mesma aplicação web em que executou a aplicação web relacionada.

* [Ativar a implementação sem um projeto web](#convertnolink).

  Configure um projeto de aplicação de consola existente para implementar como um WebJob por si só, sem uma ligação a um projeto web. Utilize esta opção quando pretender executar um WebJob numa aplicação web por si só, sem nenhuma aplicação web em execução na aplicação web. É melhor fazê-lo para escalar os seus recursos WebJob independentemente dos seus recursos de aplicação web.

#### <a name="enable-automatic-webjobs-deployment-with-a-web-project"></a><a id="convertlink"></a> Ativar a implementação automática do WebJobs com um projeto web

1. Clique com o botão direito no projeto web no **Solution Explorer** e, em seguida, selecione **Add**  >  **Existing Project como Azure WebJob**.
   
    ![Projeto existente como Azure WebJob](./media/webjobs-dotnet-deploy-vs/eawj.png)
   
    Aparece a caixa de diálogo [Add Azure WebJob.](#configure)
2. Na lista de drop-down **do nome do Projeto,** selecione o projeto de aplicação de consola para adicionar como WebJob.
   
    ![Selecionando projeto na caixa de diálogo Add Azure WebJob](./media/webjobs-dotnet-deploy-vs/aaw1.png)
3. Complete a caixa de diálogo [Add Azure WebJob](#configure) e, em seguida, selecione **OK**. 

#### <a name="enable-webjobs-deployment-without-a-web-project"></a><a id="convertnolink"></a> Ativar a implementação do WebJobs sem um projeto web
1. Clique com o botão direito no projeto de aplicação da consola no **Solution Explorer** e, em seguida, selecione Publicar **como Azure WebJob**. 
   
    ![Publicar como Azure WebJob](./media/webjobs-dotnet-deploy-vs/paw.png)
   
    Aparece a caixa de diálogo [Add Azure WebJob,](#configure) com o projeto selecionado na caixa **de nomes** do Projeto.
2. Complete a caixa de diálogo [Add Azure WebJob](#configure) e, em seguida, selecione **OK**.
   
   O **assistente da Web de publicação** aparece. Se não quiser publicar imediatamente, feche o assistente. As definições que inseriu estão guardadas para quando [pretender implantar o projeto](#deploy).

### <a name="create-a-new-webjobs-enabled-project"></a><a id="create"></a>Criar um novo projeto ativado pela WebJobs
Para criar um novo projeto ativado pelo WebJobs, utilize o modelo de projeto de aplicação de consola e permita a implementação do WebJobs, conforme explicado na [secção anterior](#convert). Como alternativa, pode utilizar o modelo de novo projeto WebJobs:

* [Use o modelo de novo projeto WebJobs para um WebJob independente](#createnolink)
  
    Crie um projeto e configuure-o para implementar por si só como um WebJob, sem qualquer ligação a um projeto web. Utilize esta opção quando pretender executar um WebJob numa aplicação web por si só, sem nenhuma aplicação web em execução na aplicação web. É melhor fazê-lo para escalar os seus recursos WebJob independentemente dos seus recursos de aplicação web.
* [Use o modelo de novo projeto WebJobs para um WebJob ligado a um projeto web](#createlink)
  
    Crie um projeto configurado para implementar automaticamente como WebJob quando implementar um projeto web na mesma solução. Utilize esta opção quando pretender executar o seu WebJob na mesma aplicação web em que executou a aplicação web relacionada.

> [!NOTE]
> O modelo de novo projeto WebJobs instala automaticamente pacotes NuGet e inclui código no *Programa.cs* para o [WebJobs SDK](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs). Se não quiser utilizar o WebJobs SDK, remova ou altere a `host.RunAndBlock` declaração no *Programa.cs*.
> 
> 

#### <a name="use-the-webjobs-new-project-template-for-an-independent-webjob"></a><a id="createnolink"></a> Use o modelo de novo projeto WebJobs para um WebJob independente
1. Selecione **o**  >  **novo**  >  **projeto de arquivo**. No **Creta uma nova** caixa de diálogo de projeto, procure e selecione **Azure WebJob (.NET Framework)** para C#.
   
2. Siga as instruções anteriores para [tornar o projeto de aplicação de consola um projeto independente webJobs](#convertnolink).

#### <a name="use-the-webjobs-new-project-template-for-a-webjob-linked-to-a-web-project"></a><a id="createlink"></a> Use o modelo de novo projeto WebJobs para um WebJob ligado a um projeto web
1. Clique com o botão direito no projeto web no **Solution Explorer** e, em seguida, selecione **Add**  >  **New Azure WebJob Project**.
   
    ![Entrada do menu do Novo Azure WebJob Project](./media/webjobs-dotnet-deploy-vs/nawj.png)
   
    Aparece a caixa de diálogo [Add Azure WebJob.](#configure)
2. Complete a caixa de diálogo [Add Azure WebJob](#configure) e, em seguida, selecione **OK**.


### <a name="webjob-publish-settingsjson-file"></a><a id="publishsettings"></a>webjob-publish-settings.jsno arquivo
Quando configura uma aplicação de consola para implementação do WebJobs, o Visual Studio instala o pacote [Microsoft.Web.WebJobs.Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet e armazena informações de agendamento num *webjob-publish-settings.jsem* ficheiro na pasta *propriedades* do projeto WebJobs. Aqui está um exemplo desse ficheiro:

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

### <a name="webjobs-listjson-file"></a><a id="webjobslist"></a>webjobs-list.jsno arquivo
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

Pode editar este ficheiro diretamente no Visual Studio, com o IntelliSense. O esquema de ficheiros é armazenado em [https://schemastore.org](http://schemastore.org/schemas/json/webjobs-list.json) .

### <a name="deploy-a-webjobs-project"></a><a id="deploy"></a>Implementar um projeto WebJobs
Um projeto WebJobs que você ligou a um projeto web implementa-se automaticamente com o projeto web. Para obter informações sobre a implementação do projeto web, consulte **como orientar** Implementar a  >  **aplicação** na navegação à esquerda.

Para implementar um projeto WebJobs por si só, clique com o botão direito no **Solution Explorer** e selecione **Publicar como Azure WebJob**. 

![Publicar como Azure WebJob](./media/webjobs-dotnet-deploy-vs/paw.png)

Para um WebJob independente, aparece o mesmo assistente web de **publicação** que é utilizado para projetos web, mas com menos configurações disponíveis para ser alterado.

### <a name="add-azure-webjob-dialog-box"></a><a id="configure"></a>Adicione caixa de diálogo Azure WebJob
A caixa de diálogo **Add Azure WebJob** permite-lhe introduzir o nome WebJob e a definição do modo de execução para o seu WebJob. 

![Adicione caixa de diálogo Azure WebJob](./media/webjobs-dotnet-deploy-vs/aaw2.png)

Alguns dos campos desta caixa de diálogo correspondem aos campos na caixa de diálogo **Add WebJob** do portal Azure. Para obter mais informações, consulte [executar tarefas de fundo com WebJobs no Azure App Service](webjobs-create.md).

Informações de implementação do WebJob:

* Para obter informações sobre a implementação da linha de comando, consulte [ativar a linha de comando ou a entrega contínua de Azure WebJobs](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/).

* Se implementar um WebJob e decidir que pretende alterar o tipo de WebJob e recolocar, elimine o *webjobs-publish-settings.jsno* ficheiro. Ao fazê-lo, o Visual Studio ressalvou as opções de publicação, para que possa alterar o tipo de WebJob.

* Se implementar um WebJob e posteriormente alterar o modo de funcionamento de contínuo para não contínuo ou vice-versa, o Visual Studio cria um novo WebJob em Azure quando se recoloca. Se alterar outras definições de agendamento, mas deixar o modo de funcionamento da mesma forma ou alternar entre Programado e On Demand, o Visual Studio atualiza o trabalho existente em vez de criar um novo.

## <a name="webjob-types"></a>Tipos webJob

O tipo de WebJob pode ser *acionado* ou *contínuo:*

- Desencadeado (padrão): Um WebJob desencadeado começa com base num evento de encadernação, num [horário,](#scheduling-a-triggered-webjob)ou quando o ativa manualmente (a pedido). Funciona num único caso em que a aplicação web funciona.

- Contínuo: Um WebJob [contínuo](#continuous-execution) começa imediatamente quando o WebJob é criado. Funciona em todas as instâncias dimensionadas de aplicações web por padrão, mas pode ser configurado para funcionar como uma única instância através *de configurações.job*.

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

### <a name="scheduling-a-triggered-webjob"></a>Agendar um WebJob acionado

Quando publica uma aplicação de consola para o Azure, o Visual Studio define o tipo de WebJob para **Trigger por** padrão e adiciona um novo ficheiro *de definições.job* ao projeto. Para os tipos webjob despoletados, pode utilizar este ficheiro para definir um calendário de execução para o seu WebJob.

Utilize o ficheiro *de definições.job* para definir um calendário de execução para o seu WebJob. O exemplo a seguir passa de hora a hora das 9:00 às 17:00:

```json
{
    "schedule": "0 0 9-17 * * *"
}
```

Este ficheiro está localizado na raiz da pasta WebJobs com o script do WebJob, como `wwwroot\app_data\jobs\triggered\{job name}` ou `wwwroot\app_data\jobs\continuous\{job name}` . Quando implementar um WebJob a partir do Visual Studio, marque as suas *propriedades de ficheiros de trabalho* em Visual Studio como Copy se for mais **recente**.

Se [criar um WebJob a partir do portal Azure,](webjobs-create.md)o ficheiro *de definições.job* é criado para si.

#### <a name="cron-expressions"></a>Expressões cron

O WebJobs utiliza as mesmas expressões CRON para agendar como o gatilho do temporizador em Funções Azure. Para saber mais sobre o suporte cron, consulte [o gatilho do Temporizador para Funções Azure](../azure-functions/functions-bindings-timer.md#ncrontab-expressions).

[!INCLUDE [webjobs-cron-timezone-note](../../includes/webjobs-cron-timezone-note.md)]

#### <a name="settingsjob-reference"></a>definições.referência de trabalho

As seguintes definições são suportadas pela WebJobs:

| **Definição** | **Tipo**  | **Descrição** |
| ----------- | --------- | --------------- |
| `is_in_place` | Todos | Permite que o WebJob corra no lugar sem primeiro ser copiado para uma pasta temporária. Para mais informações, consulte [o diretório de trabalho da WebJob.](https://github.com/projectkudu/kudu/wiki/WebJobs#webjob-working-directory) |
| `is_singleton` | Contínuo | Só execute o WebJob num único caso quando escalado. Para obter mais informações, consulte [Definir um trabalho contínuo como singleton](https://github.com/projectkudu/kudu/wiki/WebJobs-API#set-a-continuous-job-as-singleton). |
| `schedule` | Desencadeado | Execute o WebJob num horário baseado em CRON. Para obter mais informações, consulte as [expressões NCRONTAB](../azure-functions/functions-bindings-timer.md#ncrontab-expressions). |
| `stopping_wait_time`| Todos | Permite o controlo do comportamento de paragem. Para mais informações, consulte [o encerramento graceful](https://github.com/projectkudu/kudu/wiki/WebJobs#graceful-shutdown). |

### <a name="continuous-execution"></a>Execução contínua

Se ativar **Always on in** Azure, pode utilizar o Visual Studio para alterar o WebJob para funcionar continuamente:

1. Se ainda não o fez, [publique o projeto ao Azure.](#deploy-to-azure-app-service)

1. No **Explorador de Soluções**, clique com o botão direito do rato no projeto e selecione **Publicar**.

1. No separador **Publicar,** escolha **Editar.** 

1. Na caixa de diálogo **de definições** de perfis, escolha **Continua para** **WebJob Type** e, em seguida, escolha **Guardar**.

    ![Publique caixa de diálogo de Definições para um WebJob](./media/webjobs-dotnet-deploy-vs/publish-settings.png)

1. Selecione **Publicar** no separador **Publicar** para republicar o WebJob com as definições atualizadas.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre o WebJobs SDK](webjobs-sdk-how-to.md)
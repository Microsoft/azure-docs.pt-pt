---
title: Desenvolver e implementar o WebJobs com o Visual Studio - Azure
description: Saiba como desenvolver e implementar WebJobs do Azure para o serviço de aplicações do Azure com o Visual Studio.
services: app-service
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.assetid: a3a9d320-1201-4ac8-9398-b4c9535ba755
ms.service: app-service
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: vs-azure
ms.workload: azure-vs
ms.date: 02/18/2019
ms.author: glenga;david.ebbo;suwatch;pbatum;naren.soni
ms.openlocfilehash: ede7e2fe3a2ab4c0dfd4efaea5ec789924968194
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/24/2019
ms.locfileid: "56750162"
---
# <a name="develop-and-deploy-webjobs-using-visual-studio---azure-app-service"></a>Desenvolver e implementar o WebJobs com o Visual Studio - serviço de aplicações do Azure

Este artigo explica como utilizar o Visual Studio para implementar um projeto de aplicação de consola numa aplicação web no [serviço de aplicações](overview.md) como um [WebJob do Azure](https://go.microsoft.com/fwlink/?LinkId=390226). Para obter informações sobre como implementar WebJobs com o [portal do Azure](https://portal.azure.com), consulte [tarefas de execução em segundo plano com o WebJobs](webjobs-create.md).

Pode publicar WebJobs a várias para uma aplicação web individual. Certifique-se de que cada trabalho Web numa aplicação web tem um nome exclusivo.

Versão 3.x dos [SDK de WebJobs do Azure](webjobs-sdk-how-to.md) permite que se desenvolva WebJobs que são executados como aplicações de .NET Core, enquanto a versão 2.x suporta apenas o .NET Framework. Da forma que implementa um projeto do WebJobs é diferentes projetos de .NET Core em comparação com aqueles do .NET Framework.

## <a name="webjobs-as-net-core-console-apps"></a>WebJobs como aplicações de consola .NET Core

Quando utilizar a versão 3.x do WebJobs, pode criar e publicar WebJobs como aplicações de consola .NET Core. Para obter instruções passo a passo Criar e publicar uma aplicação de consola .NET Core para o Azure como um WebJob, consulte [introdução ao SDK de WebJobs do Azure para processamento condicionada por eventos em segundo plano](webjobs-sdk-get-started.md).

> [!NOTE]
> Não é possível ligar o .NET core WebJobs com projetos da web. Se precisar de implementar o seu trabalho Web com uma aplicação web, deve [criar seu WebJob como uma aplicação de consola do .NET Framework](#webjobs-as-net-framework-console-apps).  

### <a name="deploy-to-azure-app-service"></a>Implementar no serviço de aplicações do Azure

Publicar um WebJob em .NET Core no serviço de aplicações a partir do Visual Studio utiliza as ferramentas do mesmo como publicar uma aplicação ASP.NET Core.

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)] 

### <a name="webjob-types"></a>Tipos de WebJob

Por predefinição, um WebJob publicados a partir de um projeto de console é executado apenas quando acionados do .NET Core ou a pedido. Também pode atualizar o projeto para [executada numa agenda](#scheduled-execution) ou executar continuamente.

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

#### <a name="scheduled-execution"></a>Execução agendada

Quando publica uma aplicação de consola .NET Core para o Azure, uma nova *Job* ficheiro é adicionado ao projeto. Utilize este ficheiro para definir uma agenda de execução para o WebJob. Para obter mais informações, consulte [agendamento um WebJob acionado](#scheduling-a-triggered-webjob).

#### <a name="continuous-execution"></a>Execução contínua

Pode usar o Visual Studio para alterar o WebJob em execução continuamente quando Always On está ativado no Azure.

1. Se ainda não fez isso, [publicar o projeto para o Azure](#deploy-to-azure-app-service).

1. No **Explorador de Soluções**, clique com o botão direito do rato no projeto e selecione **Publicar**.

1. Na **Publish** separador, escolha **definições**. 

1. Na **definições de perfil** caixa de diálogo, escolha **contínua** para **tipo WebJob**e escolha **guardar**.

    ![Publicar a caixa de diálogo Definições para um WebJob](./media/webjobs-dotnet-deploy-vs/publish-settings.png)

1. Selecione **publicar** republicar o WebJob com as definições atualizadas.

## <a name="webjobs-as-net-framework-console-apps"></a>WebJobs como aplicações de consola do .NET Framework  

Quando o Visual Studio implementa um projeto de ativado o WebJobs aplicação de consola do .NET Framework, ele executa duas tarefas:

* Copia os ficheiros de tempo de execução para a pasta apropriada na aplicação web (*aplicação_dados/tarefas/contínua* para o WebJobs contínuos e *aplicação_dados/tarefas/acionada* para WebJobs agendados ou a pedido).
* Configura [agendador do Azure](https://docs.microsoft.com/azure/scheduler/) tarefas para WebJobs agendados para execução em horários específicos. (Isso não é necessária para o WebJobs contínuos.)

Um projeto de WebJobs capacidade tem os seguintes itens adicionados à mesma:

* O [Microsoft.Web.WebJobs.Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) pacote NuGet.
* R [Settings webjob publicar](#publishsettings) ficheiro que contém as definições de implementação e o scheduler. 

![Diagrama que mostra o que é adicionado a uma aplicação de consola para permitir uma implantação como um WebJob](./media/webjobs-dotnet-deploy-vs/convert.png)

Pode adicionar estes itens para um projeto de aplicação de consola existente ou utilizar um modelo para criar um novo projeto de aplicação de consola preparada para WebJobs. 

Pode implementar um projeto como um WebJob por si só ou ligá-lo num projeto web para que ele implementa automaticamente sempre que implementar o projeto web. Para ligar a projetos, o Visual Studio inclui o nome do projeto WebJobs habilitado numa [webjobs list.json](#webjobslist) arquivo no projeto da web.

![Diagrama que mostra a vinculação ao projeto de web de projetos de trabalho Web](./media/webjobs-dotnet-deploy-vs/link.png)

### <a name="prerequisites"></a>Pré-requisitos

Se estiver a utilizar o Visual Studio 2015, instale o [Azure SDK para .NET (Visual Studio 2015)](https://azure.microsoft.com/downloads/).

Se estiver a utilizar o Visual Studio 2017, instale o [carga de trabalho de desenvolvimento do Azure](https://docs.microsoft.com/visualstudio/install/install-visual-studio#step-4---select-workloads).

### <a id="convert"></a> Ativar a implementação do WebJobs para um projeto de aplicação de consola existente

Tem duas opções:

* [Ativar a implementação automática com um projeto web](#convertlink).

  Configure um projeto de aplicação de consola existente para que ele implementa automaticamente como um WebJob quando implementar um projeto web. Utilize esta opção quando pretender executar o WebJob na mesma aplicação web em que executar o aplicativo da web relacionados.

* [Ativar a implementação sem um projeto web](#convertnolink).

  Configure um projeto de aplicação de consola existente para implementar como um WebJob por si só, com nenhum link para um projeto web. Utilize esta opção quando pretender executar um WebJob num aplicativo web por si só, com nenhuma aplicação web em execução na aplicação web. Pode querer fazer isso para que seja possível dimensionar os recursos de WebJob independentemente dos recursos da aplicação web.

#### <a id="convertlink"></a> Ativar a implementação automática de WebJobs com um projeto da web

1. Com o botão direito no projeto web no **Explorador de soluções**e, em seguida, clique em **Add** > **projeto existente como Azure WebJob**.
   
    ![Projeto existente como WebJob do Azure](./media/webjobs-dotnet-deploy-vs/eawj.png)
   
    O [adicionar WebJob do Azure](#configure) é apresentada a caixa de diálogo.
2. Na **nome do projeto** na lista pendente, selecione a aplicação de consola do projeto para adicionar como um WebJob.
   
    ![Selecionar projeto na caixa de diálogo Adicionar WebJob do Azure](./media/webjobs-dotnet-deploy-vs/aaw1.png)
3. Concluir o [adicionar WebJob do Azure](#configure) caixa de diálogo e clique em **OK**. 

#### <a id="convertnolink"></a> Ativar a implementação de WebJobs sem um projeto da web
1. Com o botão direito no projeto de aplicação de consola no **Explorador de soluções**e, em seguida, clique em **publicar como WebJob do Azure...** . 
   
    ![Publicar como WebJob do Azure](./media/webjobs-dotnet-deploy-vs/paw.png)
   
    O [adicionar WebJob do Azure](#configure) caixa de diálogo é apresentada, com o projeto selecionado na **nome do projeto** caixa.
2. Concluir o [adicionar WebJob do Azure](#configure) caixa de diálogo e clique em **OK**.
   
   O **publicar Web** é apresentado o assistente.  Se não pretender publicar imediatamente, feche o assistente. As definições que introduzir são guardadas para quando deseja [implementar o projeto](#deploy).

### <a id="create"></a>Criar um novo projeto habilitados para o WebJobs
Para criar um novo projeto habilitados para o WebJobs, pode utilizar o modelo de projeto de aplicativo de Console e ativar a implementação do WebJobs, conforme explicado [secção anterior](#convert). Como alternativa, pode utilizar o modelo de novo projeto do WebJobs:

* [Utilizar o modelo de novo projeto do WebJobs para um WebJob independente](#createnolink)
  
    Crie um projeto e configurá-lo para implementar por si só como um WebJob, com nenhum link para um projeto web. Utilize esta opção quando pretender executar um WebJob num aplicativo web por si só, com nenhuma aplicação web em execução na aplicação web. Pode querer fazer isso para que seja possível dimensionar os recursos de WebJob independentemente dos recursos da aplicação web.
* [Utilizar o modelo de novo projeto do WebJobs para um WebJob ligado a um projeto da web](#createlink)
  
    Crie um projeto que está configurado para implementar automaticamente como um WebJob quando um projeto web na mesma solução é implementado. Utilize esta opção quando pretender executar o WebJob na mesma aplicação web em que executar o aplicativo da web relacionados.

> [!NOTE]
> O modelo de novo projeto do WebJobs automaticamente instala os pacotes de NuGet e inclui o código na *Program.cs* para o [SDK do WebJobs](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs). Se não quiser utilizar o SDK de WebJobs, remova ou altere a `host.RunAndBlock` instrução na *Program.cs*.
> 
> 

#### <a id="createnolink"></a> Utilizar o modelo de novo projeto do WebJobs para um WebJob independente
1. Clique em **arquivo** > **novo projeto**e, em seguida, no **novo projeto** clique da caixa de diálogo **Cloud**  >   **WebJob do Azure (.NET Framework)**.
   
    ![Caixa de diálogo do projeto nova que mostra o modelo de WebJob](./media/webjobs-dotnet-deploy-vs/np.png)
2. Siga as indicações mostradas anteriormente ao [tornar o aplicativo de Console, um projeto de WebJobs independente do projeto](#convertnolink).

#### <a id="createlink"></a> Utilizar o modelo de novo projeto do WebJobs para um WebJob ligado a um projeto da web
1. Com o botão direito no projeto web no **Explorador de soluções**e, em seguida, clique em **Add** > **novo projeto de WebJob do Azure**.
   
    ![Nova entrada de menu do projeto de WebJob do Azure](./media/webjobs-dotnet-deploy-vs/nawj.png)
   
    O [adicionar WebJob do Azure](#configure) é apresentada a caixa de diálogo.
2. Concluir o [adicionar WebJob do Azure](#configure) caixa de diálogo e clique em **OK**.

### <a id="configure"></a>A caixa de diálogo Adicionar WebJob do Azure
O **adicionar WebJob do Azure** caixa de diálogo permite-lhe introduzir o nome de WebJob e modo de configuração para o WebJob de execução. 

![Adicionar a caixa de diálogo do trabalho Web do Azure](./media/webjobs-dotnet-deploy-vs/aaw2.png)

Os campos nesta caixa de diálogo correspondem aos campos na **WebJob adicionar** caixa de diálogo do portal do Azure. Para obter mais informações, consulte [tarefas de execução em segundo plano com o WebJobs](webjobs-create.md).

> [!NOTE]
> * Para obter informações sobre a implementação da linha de comandos, consulte [ativação da linha de comandos ou a entrega contínua do WebJobs do Azure](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/).
> * Se implementar um WebJob e, em seguida, decidir que pretende alterar o tipo de WebJob e volte a implementar, terá de eliminar a *Settings webjobs publicar* ficheiro. Esta ação irá tornar o Visual Studio para mostrar as opções de publicação mais uma vez, para que possa alterar o tipo de trabalho Web.
> * Se implementar um WebJob e posteriormente alterar o modo de execução do contínua para não contínuos e vice-versa, o Visual Studio cria um WebJob de novo no Azure quando implementar novamente. Se alterar outras definições de agendamento, mas deixe modo de execução o mesmo ou alternar entre agendada e a pedido, Visual Studio atualiza a tarefa existente em vez de criar um novo.
> 
> 

### <a id="publishsettings"></a>webjob-publish-settings.json
Quando configurar uma aplicação de consola para a implementação de WebJobs, o Visual Studio instala o [Microsoft.Web.WebJobs.Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet pacote e arquivos de agendamento de informações num *Settings webjob publicar*  arquivo no projeto *propriedades* pasta do projeto WebJobs. Eis um exemplo desse ficheiro:

        {
          "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
          "webJobName": "WebJob1",
          "startTime": "null",
          "endTime": "null",
          "jobRecurrenceFrequency": "null",
          "interval": null,
          "runMode": "Continuous"
        }

Pode editar este ficheiro diretamente, e o Visual Studio fornece o IntelliSense. O esquema de ficheiro é armazenado em [ https://schemastore.org ](https://schemastore.org/schemas/json/webjob-publish-settings.json) e pode ser vista aqui.  

### <a id="webjobslist"></a>webjobs-list.json
Quando liga um projeto habilitados para o WebJobs para um projeto web, o Visual Studio armazena o nome do projeto de WebJobs uma *webjobs list.json* arquivo no projeto da web *propriedades* pasta. A lista pode conter vários projetos de WebJobs, conforme mostrado no exemplo a seguir:

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

Pode editar este ficheiro diretamente, e o Visual Studio fornece o IntelliSense. O esquema de ficheiro é armazenado em [ https://schemastore.org ](https://schemastore.org/schemas/json/webjobs-list.json) e pode ser vista aqui.

### <a id="deploy"></a>Implementar um projeto de WebJobs
Um projeto de WebJobs que a ligação a um projeto web implementa automaticamente com o projeto de web. Para obter informações sobre a implantação do projeto de web, consulte **guias de procedimentos** > **implementar aplicação** na navegação à esquerda.

Para implementar um projeto de WebJobs por si só, com o botão direito do rato no **Explorador de soluções** e clique em **publicar como WebJob do Azure...** . 

![Publicar como WebJob do Azure](./media/webjobs-dotnet-deploy-vs/paw.png)

Para um WebJob independente, o mesmo **publicar Web** assistente que é utilizado para projetos web é apresentada, mas com menos definições disponíveis para alterar.

## <a name="scheduling-a-triggered-webjob"></a>Agendamento de um WebJob acionado

WebJobs utiliza um *Job* ficheiro para determinar quando é executado um WebJob. Utilize este ficheiro para definir uma agenda de execução para o WebJob. O exemplo seguinte executa a cada hora das 9:00 para 5 PM:

```json
{
    "schedule": "0 0 9-17 * * *"
}
```

Este ficheiro tem de estar localizado na raiz da pasta WebJobs, ao longo de lado o script de seu trabalho de Web, como `wwwroot\app_data\jobs\triggered\{job name}` ou `wwwroot\app_data\jobs\continuous\{job name}`. Quando implementar um WebJob do Visual Studio, marque sua `settings.job` propriedades como do ficheiro **copiar se for mais recente**. 

Quando [criar um WebJob do portal do Azure](webjobs-create.md), o ficheiro Settings Job é criado por si.

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

### <a name="cron-expressions"></a>Expressões CRON

WebJobs utiliza as mesmas expressões CRON para o agendamento como o acionador de temporizador nas funções do Azure. Para saber mais sobre o suporte CRON, veja a [artigo de referência do acionador de temporizador](../azure-functions/functions-bindings-timer.md#cron-expressions).

### <a name="settingjob-reference"></a>referência de Setting.job

WebJobs suporta as seguintes definições:

| **Definição** | **Tipo**  | **Descrição** |
| ----------- | --------- | --------------- |
| `is_in_place` | Todos | Permite que a tarefa seja executada no local sem primeiro a ser copiado para uma pasta temporária. Para obter mais informações, consulte [diretório de trabalho de WebJobs](https://github.com/projectkudu/kudu/wiki/WebJobs#webjob-working-directory). |
| `is_singleton` | Contínuo | Execute apenas os trabalhos Web numa única instância ao aumentar horizontalmente. Para obter mais informações, consulte [definir uma tarefa contínua como singleton](https://github.com/projectkudu/kudu/wiki/WebJobs-API#set-a-continuous-job-as-singleton). |
| `schedule` | Acionado | Execute o WebJob num agendamento com base em CRON. Para obter mais informações, consulte a [artigo de referência do acionador de temporizador](../azure-functions/functions-bindings-timer.md#cron-expressions). |
| `stopping_wait_time`| Todos | Permite controlar o comportamento de encerramento. Para obter mais informações, consulte [desligamento não](https://github.com/projectkudu/kudu/wiki/WebJobs#graceful-shutdown). |

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre o SDK de WebJobs](webjobs-sdk-how-to.md)

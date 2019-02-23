---
title: Introdução ao SDK do WebJobs - Azure
description: Introdução ao SDK do WebJobs para processamento condicionada por eventos em segundo plano. Saiba como aceder a dados nos serviços de terceiros e de serviços do Azure.
services: app-service\web, storage
documentationcenter: .net
author: ggailey777
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/15/2019
ms.author: glenga
ms.openlocfilehash: 390d9dcda18e10c21db8165653faec7880863ba1
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56734477"
---
# <a name="get-started-with-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Introdução ao SDK de WebJobs do Azure para processamento condicionada por eventos em segundo plano

Este artigo mostra como criar um projeto do SDK de WebJobs do Azure, executá-lo localmente e implementá-la no App Service do Azure.

As instruções são para [Visual Studio 2017](https://www.visualstudio.com/vs/), mas as mesmas tarefas podem ser feitas com outras ferramentas, tal como [Visual Studio Code](https://code.visualstudio.com/).

## <a name="what-is-the-azure-webjobs-sdk"></a>O que é o Azure WebJobs SDK

O Azure WebJobs SDK é uma estrutura que simplifica a tarefa de escrever código de processamento em segundo plano que acessa dados nos serviços do Azure. O SDK possui uma sintaxe declarativa para especificar os eventos que devem acionar uma função, como uma nova mensagem adicionada a uma fila. Sintaxe declarativa semelhante controla ler e escrever dados assim que uma função foi acionada. Este sistema de acionadores e enlaces se encarrega da maior parte das tarefas de codificação baixo nível associadas ao acesso do Azure e serviços de terceiros.

### <a name="functions-triggers-and-bindings"></a>As funções, acionadores e enlaces

Um projeto do SDK do WebJobs define um ou mais *funções*. Uma função é um método que tem um atributo de Acionador na sua assinatura do método. Acionadores especificam condições para chamar uma função e enlaces especificam as ações a leitura e escrita. Por exemplo, o atributo de Acionador na função seguinte informa o tempo de execução para chamar a função sempre que é apresentada uma mensagem de fila no `items` fila. O `Blob` atributo indica o tempo de execução para utilizar a mensagem de fila para ler um blob no *workitems* contentor. O conteúdo da mensagem de fila &mdash; fornecidas a `queueTrigger` parâmetro &mdash; é o nome do blob.

```cs
public static void Run(
    [QueueTrigger("items")] string myQueueItem,
    [Blob("workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    TraceWriter log)
{
    log.Info($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

### <a name="versions-2x-and-3x"></a>As versões 2.x e 3.x

As instruções informar sobre como criar um projeto de 3.x de versão do SDK do WebJobs. A principal alteração introduzida pela versão 3.x é o uso do .NET Core em vez do .NET Framework. Diferenças entre a v2.x e v3.x são chamados Escalamento neste artigo.

### <a name="azure-functions"></a>Funções do Azure

[As funções do Azure](../azure-functions/functions-overview.md) fornece uma opção sem servidor para as suas funções. As funções baseia-se no SDK do WebJobs e é uma opção quando não precisar de utilizar o SDK de WebJobs diretamente. Azure funções 2.x utiliza o SDK do WebJobs 3.x. Para obter mais informações, consulte [comparação entre as funções do Azure e o SDK de WebJobs](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que tenha [uma conta do Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) e experiência com [apps no App Service do Azure](overview.md). Para concluir os passos neste artigo:

* [Instale o Visual Studio 2017](/visualstudio/install/) com o **desenvolvimento do Azure** carga de trabalho. Se já tiver o Visual Studio, mas não tem essa carga de trabalho, adicione a carga de trabalho selecionando **ferramentas > obter ferramentas e funcionalidades**.
* [Criar uma aplicação de serviço de aplicações](app-service-web-get-started-dotnet-framework.md). Se já tiver um que pode implementar um WebJob seja, pode usá-lo em vez de criar um novo.

## <a name="create-a-project"></a>Criar um projeto

1. No Visual Studio, selecione **ficheiro > novo > projeto**.

2. Selecione **ambiente de trabalho clássico do Windows > aplicação da consola (.NET Framework)**.

3. Nomeie o projeto *WebJobsSDKSample*e, em seguida, selecione **OK**.

   ![Caixa de diálogo Novo Projeto](./media/webjobs-sdk-get-started/new-project.png)

## <a name="webjobs-nuget-packages"></a>Pacotes NuGet do WebJobs

Os pacotes de NuGet que instalar o SDK de WebJobs diferem ligeiramente entre v2.x e v3.x.

### <a name="install-sdk-version-3x-packages"></a>Instalar pacotes do SDK versão 3.x

1. Instale as versões mais recentes 3.x estável os seguintes pacotes NuGet:

    * `Microsoft.Azure.WebJobs`
    * `Microsoft.Azure.WebJobs.Extensions`

    Aqui está o **Package Manager Console** comandos para a versão 3.0.3:

    ```powershell
    Install-Package Microsoft.Azure.WebJobs -version 3.0.2
    Install-Package Microsoft.Azure.WebJobs.Extensions -version 3.0.1
    ```

### <a name="install-the-sdk-version-2x-package"></a>Instalar o pacote do SDK versão 2.x

1. Instalar a versão 2.x estável mais recente do pacote NuGet `Microsoft.Azure.WebJobs`.

    Aqui está o **Package Manager Console** comando para a versão 2.2.0:

    ```powershell
    Install-Package Microsoft.Azure.WebJobs -version 2.2.0
    ```

## <a name="create-the-host"></a>Criar o anfitrião

O anfitrião é o contentor de tempo de execução para as funções: escuta acionadores e as chamadas de funções. O anfitrião que criar depende da versão do SDK.

### <a name="version-3x"></a>Versão 3.x

O seguinte cria um host que implementa [ `IHost` ](/dotnet/api/microsoft.extensions.hosting.ihost), que é o Host genérico em ASP.NET Core. 

1. Na *Program.cs*, adicione um `using` instrução:

    ```cs
    using Microsoft.Extensions.Hosting;
    ```

1. Substitua o método `Main` pelo código abaixo:

    ```cs
    static void Main(string[] args)
    {
        var builder = new HostBuilder();
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                });
        var host = builder.Build();
        using (host)
        {
            host.Run();
        }
    }
    ```

No ASP.NET Core, configurações de host são definidas por chamar métodos no [ `HostBuilder` ](/dotnet/api/microsoft.extensions.hosting.hostbuilder) instância. Para obter mais informações, consulte [Host genérico do .NET](/aspnet/core/fundamentals/host/generic-host). O `ConfigureWebJobs` método de extensão inicializa o anfitrião do WebJobs.

### <a name="version-2x"></a>Versão 2.x

O código a seguir cria os **JobHost** objeto.

1. Na *Program.cs*, adicione um `using` instrução:

   ```cs
   using Microsoft.Azure.WebJobs;
   ```

1. Substitua o método `Main` pelo código abaixo:

   ```cs
   static void Main()
   {
       var config = new JobHostConfiguration();
       var host = new JobHost(config);
       host.RunAndBlock();
   }
   ```

Configurações de Host são definidas `JobHostConfiguration` instância utilizada para criar o `JobHost`.

## <a name="enable-console-logging"></a>Ativar o registo da consola

A arquitetura de registo que foi desenvolvida para ASP.NET Core é recomendado para as versões 2.x e 3.x do SDK do WebJobs. Esta estrutura oferece um desempenho melhor e mais flexibilidade na mídia de armazenamento e na filtragem. Para obter mais informações, consulte [iniciar sessão no ASP.NET Core](/aspnet/core/fundamentals/logging).

Nesta secção, vai configurar o registo de consola que utiliza essa estrutura.

1. Instale a versão estável mais recente dos pacotes de NuGet seguintes:

   * `Microsoft.Extensions.Logging` -A arquitetura de registo.
   * `Microsoft.Extensions.Logging.Console` -A consola *fornecedor*. Um fornecedor envia registos para um destino específico, neste caso para a consola.

   Seguem-se a **Package Manager Console** comandos para a versão 2.2.0:

   ```powershell
   Install-Package Microsoft.Extensions.Logging -version 2.2.0
   ```

   ```powershell
   Install-Package Microsoft.Extensions.Logging.Console -version 2.2.0
   ```

1. Na *Program.cs*, adicione um `using` instrução:

   ```cs
   using Microsoft.Extensions.Logging;
   ```

1. As atualizações de código de Registro em log dependem de sua versão do SDK:

    **Versão 3.x**

    Chamar o [ `ConfigureLogging` ](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging) método no [ `HostBuilder` ](/dotnet/api/microsoft.extensions.hosting.hostbuilder). O [ `AddConsole` ](/dotnet/api/microsoft.extensions.logging.consoleloggerextensions.addconsole) método adiciona o registo da consola para a configuração.

    ```cs
    builder.ConfigureLogging((context, b) =>
    {
        b.AddConsole();
    });
    ```

    O `Main` método agora esta aparência:

    ```cs
    static void Main(string[] args)
    {
        var builder = new HostBuilder();
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                });
        builder.ConfigureLogging((context, b) =>
                {
                    b.AddConsole();
                });
        var host = builder.Build();
        using (host)
        {
            host.Run();
        }
    }
    ```

    **Versão 2.x**

    Na `Main` método, adicione o código para atualizar o `JobHostConfiguration` antes de criar o `JobHost`:

    ```cs
    config.DashboardConnectionString = "";
    var loggerFactory = new LoggerFactory();
    config.LoggerFactory = loggerFactory
        .AddConsole();
    ```

    O `Main` método agora esta aparência:

    ```cs
    var config = new JobHostConfiguration();
    config.DashboardConnectionString = "";
    var loggerFactory = new LoggerFactory();
    config.LoggerFactory = loggerFactory
        .AddConsole();
    var host = new JobHost(config);
    host.RunAndBlock();
    ```

    Estas atualizações faça o seguinte:

    * Desativa [registo de dashboard](https://github.com/Azure/azure-webjobs-sdk/wiki/Queues#logs). O dashboard é um ferramenta de monitoramento de legado e o registo de dashboard não é recomendado para cenários de produção de alto débito.
    * Adiciona o fornecedor de consola com o padrão [filtragem](webjobs-sdk-how-to.md#log-filtering).

Agora, pode adicionar uma função que é acionada por mensagens que chegam numa [fila de armazenamento do Azure](../azure-functions/functions-bindings-storage-queue.md).

## <a name="install-binding-extensions"></a>Instalar as extensões de enlace

A partir da versão 3.x, explicitamente tem de instalar as extensões para os atributos de ligação do SDK do WebJobs que usar em suas funções. As únicas exceções são [acionador de temporizador](../azure-functions/functions-bindings-timer.md) e [acionador HTTP](../azure-functions/functions-bindings-http-webhook.md), que não necessitam de uma extensão. Versão 2.x do SDK do WebJobs não utilizar extensões, eles são incluídos no SDK. Se estiver a utilizar versão 2.x, ignorar em frente para a secção seguinte.

1. Instale a versão estável mais recente dos [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) pacote NuGet, versão 3.x. 

    Aqui está o **Package Manager Console** comando para a versão 3.0.2:

    ```powershell
    Install-Package Microsoft.Azure.WebJobs.Extensions.Storage -Version 3.0.2
    ```

2. Na `ConfigureWebJobs` método de extensão, a chamada a `AddAzureStorage` método no [ `HostBuilder` ](/dotnet/api/microsoft.extensions.hosting.hostbuilder) instância ao inicializar a extensão de armazenamento. Neste momento, o `ConfigureWebJobs` método é semelhante ao seguinte exemplo:

    ```cs
    builder.ConfigureWebJobs(b =>
                    {
                        b.AddAzureStorageCoreServices();
                        b.AddAzureStorage();
                    });
    ```

## <a name="create-a-function"></a>Criar uma função

1. Com o botão direito no projeto, selecione **Add** > **Novo Item...** e dê o nome do novo C# arquivo de classe *Functions.cs*.

1. No Functions.cs, substitua o modelo gerado com o código a seguir:

   ```cs
   using Microsoft.Azure.WebJobs;
   using Microsoft.Extensions.Logging;

   namespace WebJobsSDKSample
   {
       public class Functions
       {
           public static void ProcessQueueMessage([QueueTrigger("queue")] string message, ILogger logger)
           {
               logger.LogInformation(message);
           }
       }
   }
   ```

   O `QueueTrigger` atributo indica o tempo de execução para chamar esta função quando uma nova mensagem for feita numa fila de armazenamento do Azure chamada `queue`. O conteúdo da mensagem de fila é fornecido para o código de método no `message` parâmetro. O corpo do método é onde processar os dados de Acionador. Neste exemplo, o código apenas regista a mensagem.

   O `message` parâmetro não tem de ser uma cadeia de caracteres. Também pode ligar a um objeto JSON, uma matriz de bytes, ou uma [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueuemessage) objeto. [Ver utilização de Acionador de fila](../azure-functions/functions-bindings-storage-queue.md#trigger---usage). Cada tipo de enlace (por exemplo, filas, blobs ou tabelas) tem um conjunto diferente de tipos de parâmetro que é possível ligar a.

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

O emulador de armazenamento do Azure que é executada localmente não tem todos os recursos que precisa do SDK do WebJobs. Portanto, nesta seção cria uma conta de armazenamento no Azure e configura o projeto para utilizá-lo.

1. Open **Explorador de servidores** no Visual studio e iniciar sessão no Azure. Com o botão direito a **Azure** nó e, em seguida, selecione **ligar a subscrição do Microsoft Azure**.

   ![Iniciar sessão no Azure](./media/webjobs-sdk-get-started/sign-in.png)

1. Sob o **Azure** nó **Explorador de servidores**, com o botão direito **armazenamento**e, em seguida, selecione **conta de armazenamento criar**.

   ![Criar o menu de conta de armazenamento](./media/webjobs-sdk-get-started/create-storage-account-menu.png)

1. Na **criar conta de armazenamento** caixa de diálogo, introduza um nome exclusivo para a conta de armazenamento.

1. Escolha a mesma **região** que criou a aplicação de serviço de aplicações no, ou uma região fechar para.

1. Selecione **Criar**.

   ![Criar conta de armazenamento](./media/webjobs-sdk-get-started/create-storage-account.png)

1. Sob o **armazenamento** nó **Explorador de servidores**, selecione a nova conta de armazenamento. Na **propriedades** janela, selecione as reticências (**...** ) na direita dos **cadeia de ligação** campo de valor.

   ![Botão de reticências de cadeia de ligação](./media/webjobs-sdk-get-started/conn-string-ellipsis.png)

1. Copie a cadeia de ligação e guardar esse valor em algum lugar que pode copiá-lo novamente prontamente.

   ![Copie a cadeia de ligação](./media/webjobs-sdk-get-started/copy-key.png)

## <a name="configure-storage-to-run-locally"></a>Configurar o armazenamento para executar localmente

O SDK do WebJobs procura a cadeia de ligação de armazenamento nas definições da aplicação no Azure. Ao executar localmente, ele procura por este valor no ficheiro de configuração local ou em variáveis de ambiente.

### <a name="appsettingsjson-sdk-version-3x"></a>appSettings (versão do SDK 3.x)

1. Criar uma *appSettings* do ficheiro ou adicionar um `AzureWebJobsStorage` campo, como no exemplo seguinte:

    ```json
    {
        "AzureWebJobsStorage": "{storage connection string}"
    }
    ```

1. Substitua *{cadeia de ligação de armazenamento}* com a cadeia de ligação que copiou anteriormente.

### <a name="appconfig-sdk-version-2x"></a>App. config (versão do SDK 2.x)

1. Adicione o seguinte XML para o *App. config* ficheiro, imediatamente após a abertura `<configuration>` marca.

   ```xml
   <connectionStrings>
     <add name="AzureWebJobsStorage" connectionString="{storage connection string}" />
   </connectionStrings>
   ```

1. Substitua *{cadeia de ligação de armazenamento}* com a cadeia de ligação que copiou anteriormente.

Mais tarde, adicionará a mesma definição de aplicação de cadeia de ligação na sua aplicação no App Service do Azure.

## <a name="test-locally"></a>Testar localmente

Nesta secção, criar e executar localmente o projeto e acione a função através da criação de uma mensagem de fila.

1. Prima Ctrl + F5 para executar o projeto.

   A consola mostra que o tempo de execução localizar a sua função e está aguardando para mensagens na fila dispará-lo. A seguinte saída é gerada pelo v3.x host:

   ```console
    info: Microsoft.Azure.WebJobs.Hosting.JobHostService[0]
          Starting JobHost
    info: Host.Startup[0]
          Found the following functions:
          WebJobsSDKSample.Functions.ProcessQueueMessage

    info: Host.Startup[0]
          Job host started
    Application started. Press Ctrl+C to shut down.
    Hosting environment: Development
    Content root path: C:\WebJobsSDKSample\WebJobsSDKSample\bin\Debug\netcoreapp2.1\
   ```

1. Feche a janela de consola.

1. Na **Explorador de servidores** no Visual Studio, expanda o nó da nova conta de armazenamento e, em seguida, clique com botão direito **filas**. 

1. Selecione **criar fila**.

1. Introduza *fila* como o nome para a fila e, em seguida, selecione **OK**.

   ![Criar fila](./media/webjobs-sdk-get-started/create-queue.png)

1. O nó para a nova fila com o botão direito e, em seguida, selecione **vista de fila**.

1. Selecione o **Adicionar mensagem** ícone.

   ![Criar fila](./media/webjobs-sdk-get-started/create-queue-message.png)

1. Na **Adicionar mensagem** caixa de diálogo, introduza *Olá, mundo!* como o **mensagem de texto**e, em seguida, selecione **OK**.

   ![Criar fila](./media/webjobs-sdk-get-started/hello-world-text.png)

1. Execute novamente o projeto.

   Como usou o `QueueTrigger` atributo o `ProcessQueueMessage` função, o tempo de execução do SDK de WeJobs escuta para mensagens na fila durante o arranque. Encontrar uma nova mensagem de fila na fila com o nome *fila* e chama a função.

   Devido a [fila consulta um término exponencial](../azure-functions/functions-bindings-storage-queue.md#trigger---polling-algorithm), ele poderá demorar até 2 minutos para que o tempo de execução localizar a mensagem e invocar a função. Este tempo de espera pode ser reduzido através da execução [modo de desenvolvimento](webjobs-sdk-how-to.md#host-development-settings).

  Para a versão 3.x, o resultado da consola é semelhante a isto:

   ```console
    info: Function.ProcessQueueMessage[0]
          Executing 'Functions.ProcessQueueMessage' (Reason='New queue message detected on 'queue'.', Id=2c319369-d381-43f3-aedf-ff538a4209b8)
    info: Function.ProcessQueueMessage[0]
          Trigger Details: MessageId: b00a86dc-298d-4cd2-811f-98ec39545539, DequeueCount: 1, InsertionTime: 1/18/2019 3:28:51 AM +00:00
    info: Function.ProcessQueueMessage.User[0]
          Hello World!
    info: Function.ProcessQueueMessage[0]
          Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=2c319369-d381-43f3-aedf-ff538a4209b8)
   ```

1. Feche a janela de consola.

## <a name="add-application-insights-logging"></a>Adicionar registos do Application Insights

Quando o projeto é executado no Azure, não é possível monitorizar a execução de função ao visualizar a saída da consola. Recomendamos que é a solução de monitorização [Application Insights](../azure-monitor/app/app-insights-overview.md). Para obter mais informações, consulte [as funções do Azure de Monitor](../azure-functions/functions-monitoring.md).

Nesta secção, efetue as seguintes tarefas para configurar o Application Insights registo antes de implementar para o Azure:

* Certifique-se de que tem uma aplicação de serviço de aplicações e uma instância do Application Insights para trabalhar com.
* Configure a aplicação de serviço de aplicações para utilizar a instância do Application Insights e a conta de armazenamento que criou anteriormente.
* Configure o projeto para o registo para o Application Insights.

### <a name="create-app-service-app-and-application-insights-instance"></a>Criar aplicação de serviço de aplicações e a instância do Application Insights

1. Se ainda não tiver uma aplicação de serviço de aplicações que pode utilizar, [criá-lo](app-service-web-get-started-dotnet-framework.md). Quando criar a sua aplicação, também pode criar um recurso do Application Insights ligado. Ao fazer isso, o `APPINSIGHTS_INSTRUMENTATIONKEY` está definido para si na sua aplicação.

1. Se ainda não tiver um recurso do Application Insights que pode utilizar, [criá-lo](../azure-monitor/app/create-new-resource.md ). Definir **tipo de aplicação** ao **gerais**e a ignorar as seções a seguir **copiar a chave de instrumentação**.

1. Se já tiver um recurso do Application Insights que pretende utilizar, [copiar a chave de instrumentação](../azure-monitor/app/create-new-resource.md#copy-the-instrumentation-key).

### <a name="configure-app-settings"></a>Configurar as definições da aplicação 

1. Na **Explorador de servidores** no Visual Studio, expanda o **serviço de aplicações** no nó **Azure**.

1. Expanda o grupo de recursos que a aplicação de serviço de aplicações está no e, em seguida, clique no seu serviço de aplicações.

1. Selecione **ver definições**.

1. Na **cadeias de ligação** caixa, adicione a seguinte entrada.

   |Name  |Cadeia de ligação  |Tipo de Base de Dados|
   |---------|---------|------|
   |AzureWebJobsStorage | {Armazenamento cadeia de ligação que copiou anteriormente}|Personalizado|

1. Se o **as definições da aplicação** não existe uma instrumentação do Application Insights da chave, adicione um que copiou anteriormente. (A chave de instrumentação pode já estar lá, dependendo de como criou a aplicação de serviço de aplicações.)

   |Name  |Value  |
   |---------|---------|
   |APPINSIGHTS_INSTRUMENTATIONKEY | {chave de instrumentação} |

1. Substitua *{chave de instrumentação}* com a chave de instrumentação do recurso do Application Insights que está a utilizar.

1. Selecione **Guardar**.

1. Adicione a ligação do Application Insights ao projeto, para que pode executá-lo localmente:

    **Versão 3.x**

    Na *appSettings* do ficheiro, adicione um `APPINSIGHTS_INSTRUMENTATIONKEY` campo, como no exemplo seguinte:

    ```json
    {
        "AzureWebJobsStorage": "{storage connection string}",
        "APPINSIGHTS_INSTRUMENTATIONKEY": "{instrumentation key}"
    }
    ```

    **Versão 2.x**

    Adicione o seguinte XML para o *App. config* ficheiro, imediatamente após a coleção de cadeias de caracteres de conexão.

    ```xml
    <appSettings>
        <add key="APPINSIGHTS_INSTRUMENTATIONKEY" value="{instrumentation key}" />
    </appSettings>
    ```

    Numa das versões, substitua *{chave de instrumentação}* com a chave de instrumentação do recurso do Application Insights que está a utilizar.

1. Guarde as alterações.

### <a name="add-application-insights-logging-provider"></a>Adicionar fornecedor de registo do Application Insights

Para tirar partido das [Application Insights](../azure-monitor/app/app-insights-overview.md) registo, atualizar o seu código de registo para fazer o seguinte:

* Adicionar um fornecedor de registo do Application Insights com o padrão [filtragem](webjobs-sdk-how-to.md#log-filtering); todas as informações e registos de nível mais alto vai para a consola e o Application Insights quando estiver a executar localmente.
* Colocar o `LoggerFactory` objeto num `using` bloco para garantir que a saída de registo for descarregada quando o anfitrião é fechado.

#### <a name="version-3x"></a>Versão 3.x

1. Instalar a versão 3.x estável mais recente do pacote NuGet para o fornecedor de registo do Application Insights: `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`.

   Aqui está o **Package Manager Console** comando para a versão 3.0.2:

   ```powershell
   Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -Version 3.0.2
   ```

1. Open *Program.cs* e substitua o código no `Main` método com o código a seguir:

    ```cs
    static void Main(string[] args)
    {
        var builder = new HostBuilder();
        builder.UseEnvironment(EnvironmentName.Development);
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                    b.AddAzureStorage();
                });
        builder.ConfigureLogging((context, b) =>
                {
                    b.AddConsole();

                    // If the key exists in settings, use it to enable Application Insights.
                    string instrumentationKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
                    if (!string.IsNullOrEmpty(instrumentationKey))
                    {
                        b.AddApplicationInsights(o => o.InstrumentationKey = instrumentationKey);
                    }
                });
        var host = builder.Build();
        using (host)
        {
            host.Run();
        }
    }
    ```

#### <a name="version-2x"></a>Versão 2.x

1. Instalar a versão 2.x estável mais recente do pacote NuGet para o fornecedor de registo do Application Insights: `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`.

   Aqui está o **Package Manager Console** comando para a versão 2.2.0:

   ```powershell
   Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
   ``` 

1. Instalar a versão de 4.x estável mais recente do pacote NuGet para o Gestor de configuração do .NET: `System.Configuration.ConfigurationManager`.

   Aqui está o **Package Manager Console** comando para a versão 4.4.1:

   ```powershell
   Install-Package System.Configuration.ConfigurationManager -version 4.4.1
   ``` 

1. Open *Program.cs* e adicione um `using` instrução para o Gestor de configuração:

   ```csharp
   using System.Configuration;
   ```

1. Substitua o código no `Main` método com o código a seguir:

   ```csharp
   using (var loggerFactory = new LoggerFactory())
   {
       var config = new JobHostConfiguration();
       var instrumentationKey =
           ConfigurationManager.AppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"];
       config.DashboardConnectionString = "";
       config.LoggerFactory = loggerFactory
           .AddApplicationInsights(instrumentationKey, null)
           .AddConsole();
       var host = new JobHost(config);
       host.RunAndBlock();
   }
   ```

## <a name="test-application-insights-logging"></a>Registo do Application Insights de teste

Nesta secção, irá executar localmente novamente para verificar que dados de registo vai agora para o Application Insights, bem como para a consola.

1. Uso **Explorador de servidores** no Visual Studio para criar uma mensagem de fila, da mesma forma que fez [anteriores](#trigger-the-function-in-azure), com a exceção introduza *Hello App Insights!* como o texto da mensagem.

1. Execute o projeto.

   O SDK do WebJobs processar a mensagem de fila e ver os registos na janela da consola.

1. Feche a janela de consola.

1. Abra o [portal do Azure](https://portal.azure.com/)e aceda ao seu recurso do Application Insights.

1. Selecione **pesquisa**.

   ![Selecione pesquisa](./media/webjobs-sdk-get-started/select-search.png)

1. Se não vir o *Hello App Insights!* mensagem, selecione **atualizar** periodicamente durante vários minutos. (Os registos não aparecem imediatamente porque demora algum tempo para o cliente do Application Insights liberar os registos que processa.)

   ![Registos no Application Insights](./media/webjobs-sdk-get-started/logs-in-ai.png)

1. Feche a janela de consola.

## <a name="deploy-as-a-webjob"></a>Implementar como um WebJob

Nesta secção, vai implementar o projeto como um WebJob. Implementar uma aplicação de serviço de aplicações que [criado anteriormente](#create-app-service-app-and-application-insights-instance). Para testar seu código enquanto executa no Azure, irá disparar uma invocação de função através da criação de uma mensagem de fila.

1. Na **Explorador de soluções**, clique com o botão direito no projeto e, em seguida, selecione **publicar como WebJob do Azure**.

1. Na **adicionar WebJob do Azure** caixa de diálogo, selecione **OK**.

   ![Adicionar trabalho Web do Azure](./media/webjobs-sdk-get-started/add-azure-webjob.png)

   Visual Studio instala automaticamente um pacote de NuGet para publicação de WebJob.

1. Na **perfil** passo do **publicar** assistente, selecione **serviço de aplicações do Microsoft Azure**.

   ![Caixa de diálogo Publicar](./media/webjobs-sdk-get-started/publish-dialog.png)

1. Na **serviço de aplicações** caixa de diálogo, selecione **seu grupo de recursos > seu serviço de aplicações**e, em seguida, selecione **OK**.

   ![Caixa de diálogo do serviço de aplicações](./media/webjobs-sdk-get-started/app-service-dialog.png)

1. Após é gerado o perfil de publicação, selecione **publicar**.

## <a name="trigger-the-function-in-azure"></a>Acione a função no Azure

1. Certifique-se de que não estiver a executar localmente (fechar a janela da consola, caso esteja ainda aberto). Caso contrário, a instância local poderá ser o primeiro a processar as mensagens de fila que cria.

1. Na **fila** página no Visual Studio, adicione uma mensagem à fila como antes.

1. Atualizar o **fila** página e a nova mensagem de erro desaparece porque foi processada pela função em execução no Azure.

   > [!TIP]
   > Quando estiver a testar no Azure, utilize [modo de desenvolvimento](webjobs-sdk-how-to.md#host-development-settings) Certifique-se de que uma função de Acionador de fila é invocada imediatamente e evitar atrasos devido a [fila de término exponencial de consulta](../azure-functions/functions-bindings-storage-queue.md#trigger---polling-algorithm).

### <a name="view-logs-in-application-insights"></a>Ver registos no Application Insights

1. Abra o [portal do Azure](https://portal.azure.com/)e aceda ao seu recurso do Application Insights.

1. Selecione **pesquisa**.

1. Se não vir o *Olá Azure!* mensagem, selecione **atualizar** periodicamente durante vários minutos.

   Verá os registos da função em execução num WebJob, incluindo o *Olá Azure!* texto que introduziu na secção anterior.

## <a name="add-an-input-binding"></a>Adicionar um enlace de entrada

Enlaces de entrada simplificam o código que lê os dados. Neste exemplo, a mensagem de fila será um nome de blob e usará o nome do blob para encontrar e ler um blob no armazenamento do Azure.

1. Na *Functions.cs*, substitua o `ProcessQueueMessage` método com o código a seguir:

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger("queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
   }
   ```

   Nesse código, `queueTrigger` é um [expressão de ligação](../azure-functions/functions-bindings-expressions-patterns.md), que significa que é resolvido para um valor diferente no tempo de execução.  No tempo de execução, ele tem o conteúdo da mensagem de fila.

1. Adicionar um `using`:

   ```cs
   using System.IO;
   ```

1. Crie um contentor de BLOBs na sua conta de armazenamento.

   a. Na **Explorador de servidores** no Visual Studio, expanda o nó para a sua conta de armazenamento, com o botão direito **Blobs**e, em seguida, selecione **criar contentor de BLOBs**.

   b. Na **criar contentor de BLOBs** caixa de diálogo, introduza *contentor* como o nome do contentor e, em seguida, clique **OK**.

1. Carregar o *Program.cs* ficheiro para o contentor de Blobs. (Este ficheiro é utilizado aqui como exemplo; poderia carregar qualquer ficheiro de texto e criar uma mensagem de fila com o nome do ficheiro.)

   a. Na **Explorador de servidores**, faça duplo clique no nó do contentor que criou.

   b. Na **contentor** janela, selecione a **carregar** botão.

   ![Botão de carregamento de BLOBs](./media/webjobs-sdk-get-started/blob-upload-button.png)

   c. Localize e selecione *Program.cs*e, em seguida, selecione **OK**.

1. Criar uma mensagem de fila na fila que criou anteriormente, com *Program.cs* como o texto da mensagem.

   ![Mensagem de fila Program.cs](./media/webjobs-sdk-get-started/queue-msg-program-cs.png)

1. Execute o projeto.

   A mensagem de fila aciona a função, que, em seguida, lê o blob e registra seu comprimento. O resultado da consola tem esta aparência:

   ```console
   Found the following functions:
   ConsoleApp1.Functions.ProcessQueueMessage
   Job host started
   Executing 'Functions.ProcessQueueMessage' (Reason='New queue message detected on 'queue'.', Id=5a2ac479-de13-4f41-aae9-1361f291ff88)
   Blob name:Program.cs
   Size: 532 bytes
   Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=5a2ac479-de13-4f41-aae9-1361f291ff88)
   ```

## <a name="add-an-output-binding"></a>Adicionar um enlace de saída

Enlaces de saída simplificam o código que escreve dados. Neste exemplo modifica aquele anterior com a criação de uma cópia do blob em vez de registo de seu tamanho. Enlaces de armazenamento de BLOBs estão incluídos no pacote de extensão do armazenamento do Azure, que foi instalados anteriormente.

1. Substitua o método `ProcessQueueMessage` pelo código abaixo:

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger("queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       [Blob("container/copy-{queueTrigger}", FileAccess.Write)] Stream outputBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
       myBlob.CopyTo(outputBlob);
   }
   ```

1. Criar outra mensagem de fila com *Program.cs* como o texto da mensagem.

1. Execute o projeto.

   A mensagem de fila aciona a função, que, em seguida, lê o blob, registra seu comprimento e cria um novo blob. O resultado da consola é o mesmo, mas quando vá para a janela do contentor de blob e selecione **Atualize**, verá um novo blob com o nome *cópia Program.cs.*

## <a name="next-steps"></a>Passos Seguintes

Este guia mostra como criar, executar e implementar um projeto do SDK do WebJobs.

Para mostrar tudo o que entra num projeto do SDK do WebJobs, as instruções tinham-o a criar um projeto a partir do zero. No entanto, quando cria seu próximo projeto, considere utilizar o **trabalho Web do Azure** modelo no **Cloud** categoria. Este modelo cria um projeto com pacotes de NuGet e o código de exemplo já configurado. O código de exemplo poderá ter de ser alterado para utilizar a nova arquitetura de registo.

> [!div class="nextstepaction"]
> [Saiba mais sobre o SDK de WebJobs](webjobs-sdk-how-to.md)

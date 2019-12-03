---
title: Introdução ao SDK de trabalhos Web
description: Introdução ao SDK de trabalhos Web para processamento em segundo plano orientado por eventos. Saiba como acessar dados em serviços do Azure e serviços de terceiros.
author: ggailey777
ms.devlang: dotnet
ms.topic: article
ms.date: 02/18/2019
ms.author: glenga
ms.openlocfilehash: 33ae3f9f928a55f50f4ecd0c6c98790a384e880b
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74684185"
---
# <a name="get-started-with-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Introdução ao SDK do Azure WebJobs para processamento em segundo plano controlado por eventos

Este artigo mostra como usar o Visual Studio 2019 para criar um projeto Azure WebJobs SDK, executá-lo localmente e, em seguida, implantá-lo no [serviço Azure app](overview.md). A versão 3. x do SDK de trabalhos Web dá suporte a aplicativos de console do .NET Core e do .NET Framework. Para saber mais sobre como trabalhar com o SDK de trabalhos Web, consulte [como usar o SDK do Azure WebJobs para processamento em segundo plano controlado por eventos](webjobs-sdk-how-to.md).

Este artigo mostra como implantar trabalhos Web como um aplicativo de console do .NET Core. Para implantar trabalhos Web como um aplicativo de console .NET Framework, consulte [webjobs como .NET Framework aplicativos de console](webjobs-dotnet-deploy-vs.md#webjobs-as-net-framework-console-apps). Se você estiver interessado no SDK de trabalhos Web versão 2. x, que só dá suporte a .NET Framework, consulte [desenvolver e implantar trabalhos Web usando o serviço de Azure app do Visual Studio](webjobs-dotnet-deploy-vs.md).

## <a name="prerequisites"></a>Pré-requisitos

* [Instale o Visual Studio 2019](/visualstudio/install/) com a carga de trabalho de **desenvolvimento do Azure** . Se você já tiver o Visual Studio, mas não tiver essa carga de trabalho, adicione a carga de trabalho selecionando **ferramentas > obter ferramentas e recursos**.

* Você deve ter [uma conta do Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) para publicar seu projeto do SDK de trabalhos Web no Azure.

## <a name="create-a-project"></a>Criar um projeto

1. No Visual Studio, selecione **criar um novo projeto**.

2. Selecione **aplicativo de console (.NET Core)** .

3. Nomeie o projeto *WebJobsSDKSample*e, em seguida, selecione **criar**.

   ![Caixa de diálogo Novo Projeto](./media/webjobs-sdk-get-started/new-project.png)

## <a name="webjobs-nuget-packages"></a>Pacotes NuGet de trabalhos Web

1. Instale a última versão estável 3. x do pacote NuGet `Microsoft.Azure.WebJobs.Extensions`, que inclui `Microsoft.Azure.WebJobs`.

     Este é o comando do **console do Gerenciador de pacotes** para a versão 3.0.2:

     ```powershell
     Install-Package Microsoft.Azure.WebJobs.Extensions -version 3.0.2
     ```

## <a name="create-the-host"></a>Criar o host

O host é o contêiner de tempo de execução para funções que escutam funções de gatilhos e chamadas. As etapas a seguir criam um host que implementa [`IHost`](/dotnet/api/microsoft.extensions.hosting.ihost), que é o host genérico no ASP.NET Core.

1. Em *Program.cs*, adicione uma instrução `using`:

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

No ASP.NET Core, as configurações de host são definidas por meio da chamada de métodos na instância de [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) . Para obter mais informações, consulte [host genérico .net](/aspnet/core/fundamentals/host/generic-host). O método de extensão `ConfigureWebJobs` Inicializa o host de trabalhos Web. No `ConfigureWebJobs`, você inicializa extensões de trabalhos Web específicas e define as propriedades dessas extensões.  

## <a name="enable-console-logging"></a>Habilitar o log do console

Nesta seção, você configura o log de console que usa a [estrutura de log de ASP.NET Core](/aspnet/core/fundamentals/logging).

1. Instale a versão estável mais recente do pacote `Microsoft.Extensions.Logging.Console` NuGet, que inclui `Microsoft.Extensions.Logging`.

   Aqui está o comando **Package Manager Console** para a versão 2.2.0:

   ```powershell
   Install-Package Microsoft.Extensions.Logging.Console -version 2.2.0
   ```

1. Em *Program.cs*, adicione uma instrução `using`:

   ```cs
   using Microsoft.Extensions.Logging;
   ```

1. Chame o método [`ConfigureLogging`](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging) em [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder). O método [`AddConsole`](/dotnet/api/microsoft.extensions.logging.consoleloggerextensions.addconsole) adiciona o log de console à configuração.

    ```cs
    builder.ConfigureLogging((context, b) =>
    {
        b.AddConsole();
    });
    ```

    O método `Main` agora tem esta aparência:

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

    Essa atualização faz o seguinte:

    * Desabilita o [log do painel](https://github.com/Azure/azure-webjobs-sdk/wiki/Queues#logs). O painel é uma ferramenta de monitoramento herdada e o log do Dashboard não é recomendado para cenários de produção de alta taxa de transferência.
    * Adiciona o provedor de console com [filtragem](webjobs-sdk-how-to.md#log-filtering)padrão.

Agora, você pode adicionar uma função que é disparada pelas mensagens que chegam em uma [fila de armazenamento do Azure](../azure-functions/functions-bindings-storage-queue.md).

## <a name="install-the-storage-binding-extension"></a>Instalar a extensão de enlace do Armazenamento

A partir da versão 3. x, você deve instalar explicitamente a extensão de associação de armazenamento exigida pelo SDK de trabalhos Web. Nas versões anteriores, as associações de armazenamento foram incluídas no SDK.

1. Instale a versão estável mais recente do pacote NuGet [Microsoft. Azure. webjobs. Extensions. Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) , versão 3. x. 

    Aqui está o comando **Package Manager Console** para a versão 3.0.4:

    ```powershell
    Install-Package Microsoft.Azure.WebJobs.Extensions.Storage -Version 3.0.4
    ```

2. No método de extensão `ConfigureWebJobs`, chame o método `AddAzureStorage` na instância de [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) para inicializar a extensão de armazenamento. Neste ponto, o método `ConfigureWebJobs` é semelhante ao exemplo a seguir:

    ```cs
    builder.ConfigureWebJobs(b =>
                    {
                        b.AddAzureStorageCoreServices();
                        b.AddAzureStorage();
                    });
    ```

## <a name="create-a-function"></a>Criar uma função

1. Clique com o botão direito do mouse no projeto, selecione **adicionar** > **novo item...** , escolha **classe**, nomeie o novo C# arquivo de classe *functions.cs*e selecione **Adicionar**.

1. No Functions.cs, substitua o modelo gerado pelo seguinte código:

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

   O atributo `QueueTrigger` informa o tempo de execução para chamar essa função quando uma nova mensagem é gravada em uma fila de armazenamento do Azure chamada `queue`. O conteúdo da mensagem da fila é fornecido para o código do método no parâmetro `message`. O corpo do método é o local em que você processa os dados do gatilho. Neste exemplo, o código apenas registra a mensagem.

   O parâmetro `message` não precisa ser uma cadeia de caracteres. Você também pode associar a um objeto JSON, a uma matriz de bytes ou a um objeto [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage) . [Consulte uso do gatilho de fila](../azure-functions/functions-bindings-storage-queue.md#trigger---usage). Cada tipo de associação (como filas, BLOBs ou tabelas) tem um conjunto diferente de tipos de parâmetros aos quais você pode associar.

## <a name="create-a-storage-account"></a>Create a storage account

O emulador de armazenamento do Azure que é executado localmente não tem todos os recursos que o SDK de trabalhos Web precisa. Portanto, nesta seção, você cria uma conta de armazenamento no Azure e configura o projeto para usá-la. Se você já tiver uma conta de armazenamento, pule para a etapa 6.

1. Abra **Gerenciador de servidores** no Visual Studio e entre no Azure. Clique com o botão direito do mouse no nó **Azure** e selecione **conectar-se a Microsoft Azure assinatura**.

   ![Iniciar sessão no Azure](./media/webjobs-sdk-get-started/sign-in.png)

1. No nó **Azure** no **Gerenciador de servidores**, clique com o botão direito do mouse em **armazenamento**e selecione **criar conta de armazenamento**.

   ![Menu Criar conta de armazenamento](./media/webjobs-sdk-get-started/create-storage-account-menu.png)

1. Na caixa de diálogo **criar conta de armazenamento** , insira um nome exclusivo para a conta de armazenamento.

1. Escolha a mesma **região** em que você criou o aplicativo do serviço de aplicativo ou uma região perto de você.

1. Selecione **Criar**.

   ![Criar conta de armazenamento](./media/webjobs-sdk-get-started/create-storage-account.png)

1. No nó **armazenamento** em **Gerenciador de servidores**, selecione a nova conta de armazenamento. Na janela **Propriedades** , selecione as reticências ( **...** ) à direita do campo valor da **cadeia de conexão** .

   ![Reticências da cadeia de conexão](./media/webjobs-sdk-get-started/conn-string-ellipsis.png)

1. Copie a cadeia de conexão e salve esse valor em algum lugar para que você possa copiá-lo novamente de imediato.

   ![Copiar cadeia de conexão](./media/webjobs-sdk-get-started/copy-key.png)

## <a name="configure-storage-to-run-locally"></a>Configurar o armazenamento para ser executado localmente

O SDK de trabalhos Web procura a cadeia de conexão de armazenamento nas configurações do aplicativo no Azure. Quando você executa localmente, ele procura esse valor no arquivo de configuração local ou em variáveis de ambiente.

1. Clique com o botão direito do mouse no projeto, selecione **adicionar** > **novo item...** , escolha o **arquivo de configuração JSON JavaScript**, nomeie o arquivo novo arquivo *appSettings. JSON* e selecione **Adicionar**. 

1. No novo arquivo, adicione um campo `AzureWebJobsStorage`, como no exemplo a seguir:

    ```json
    {
        "AzureWebJobsStorage": "{storage connection string}"
    }
    ```

1. Substitua *{cadeia de conexão de armazenamento}* pela cadeia de conexão que você copiou anteriormente.

1. Selecione o arquivo *appSettings. JSON* em Gerenciador de soluções e, na janela **Propriedades** , defina **copiar para diretório de saída** como **copiar se mais recente**.

Posteriormente, você adicionará a mesma configuração de aplicativo de cadeia de conexão em seu aplicativo no serviço Azure App.

## <a name="test-locally"></a>Testar localmente

Nesta seção, você criará e executará o projeto localmente e disparará a função criando uma mensagem da fila.

1. Pressione **Ctrl + F5** para executar o projeto.

   O console mostra que o tempo de execução encontrou sua função e está aguardando que as mensagens da fila o disparem. A saída a seguir é gerada pelo host v3. x:

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

1. Feche a janela do console.

1. Em **Gerenciador de servidores** no Visual Studio, expanda o nó da sua nova conta de armazenamento e clique com o botão direito do mouse em **filas**.

1. Selecione **criar fila**.

1. Insira *fila* como o nome da fila e, em seguida, selecione **OK**.

   ![Criar fila](./media/webjobs-sdk-get-started/create-queue.png)

1. Clique com o botão direito do mouse no nó da nova fila e selecione **Exibir fila**.

1. Selecione o ícone **Adicionar mensagem** .

   ![Criar fila](./media/webjobs-sdk-get-started/create-queue-message.png)

1. Na caixa de diálogo **Adicionar mensagem** , insira *Olá, mundo!* como o **texto da mensagem**e selecione **OK**. Agora há uma mensagem na fila.

   ![Criar fila](./media/webjobs-sdk-get-started/hello-world-text.png)

1. Execute novamente o projeto.

   Como você usou o atributo `QueueTrigger` na função `ProcessQueueMessage`, o tempo de execução do SDK do WeJobs escuta mensagens da fila quando ela é iniciada. Ele encontra uma nova mensagem de fila na fila chamada *Queue* e chama a função.

   Devido à [retirada exponencial da sondagem da fila](../azure-functions/functions-bindings-storage-queue.md#trigger---polling-algorithm), pode levar até 2 minutos para que o tempo de execução localize a mensagem e invoque a função. Esse tempo de espera pode ser reduzido com a execução no [modo de desenvolvimento](webjobs-sdk-how-to.md#host-development-settings).

   A saída do console tem a seguinte aparência:

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

1. Feche a janela do console. 

1. Volte para a janela da fila e atualize-a. A mensagem foi removida, pois foi processada pela função que está sendo executada localmente. 

## <a name="add-application-insights-logging"></a>Adicionar log de Application Insights

Quando o projeto é executado no Azure, você não pode monitorar a execução da função exibindo a saída do console. A solução de monitoramento que recomendamos é [Application insights](../azure-monitor/app/app-insights-overview.md). Para obter mais informações, consulte [monitorar Azure Functions](../azure-functions/functions-monitoring.md).

Nesta seção, você executa as seguintes tarefas para configurar o log de Application Insights antes de implantar no Azure:

* Verifique se você tem um aplicativo do serviço de aplicativo e uma instância de Application Insights com a qual trabalhar.
* Configure o aplicativo do serviço de aplicativo para usar a instância de Application Insights e a conta de armazenamento que você criou anteriormente.
* Configure o projeto para fazer logon no Application Insights.

### <a name="create-app-service-app-and-application-insights-instance"></a>Criar aplicativo do serviço de aplicativo e instância de Application Insights

1. Se você ainda não tiver um aplicativo do serviço de aplicativo que possa usar, [crie um](app-service-web-get-started-dotnet-framework.md). Ao criar seu aplicativo, você também pode criar um recurso de Application Insights conectado. Quando você faz isso, o `APPINSIGHTS_INSTRUMENTATIONKEY` é definido para você em seu aplicativo.

1. Se você ainda não tiver um recurso Application Insights que possa usar, [crie um](../azure-monitor/app/create-new-resource.md ). Defina o **tipo de aplicativo** como **geral**e ignore as seções a seguir para **copiar a chave de instrumentação**.

1. Se você já tiver um recurso Application Insights que deseja usar, [Copie a chave de instrumentação](../azure-monitor/app/create-new-resource.md#copy-the-instrumentation-key).

### <a name="configure-app-settings"></a>Configurar as definições da aplicação 

1. Em **Gerenciador de servidores** no Visual Studio, expanda o nó **serviço de aplicativo** no **Azure**.

1. Expanda o grupo de recursos no qual seu aplicativo do serviço de aplicativo está e clique com o botão direito do mouse no aplicativo do serviço de aplicativo.

1. Selecione **exibir configurações**.

1. Na caixa **cadeias de conexão** , adicione a seguinte entrada.

   |Nome  |Cadeia de conexão  |Tipo de banco de dados|
   |---------|---------|------|
   |AzureWebJobsStorage | {a cadeia de conexão de armazenamento que você copiou anteriormente}|Personalizados|

1. Se a caixa **configurações do aplicativo** não tiver uma chave de instrumentação Application insights, adicione aquela que você copiou anteriormente. (A chave de instrumentação pode já estar lá, dependendo de como você criou o aplicativo do serviço de aplicativo.)

   |Nome  |Valor  |
   |---------|---------|
   |APPINSIGHTS_INSTRUMENTATIONKEY | {chave de instrumentação} |

1. Substitua *{chave de instrumentação}* pela chave de instrumentação do Application insights recurso que você está usando.

1. Selecione **Guardar**.

1. Adicione a conexão de Application Insights ao projeto para que você possa executá-lo localmente. No arquivo *appSettings. JSON* , adicione um campo `APPINSIGHTS_INSTRUMENTATIONKEY`, como no exemplo a seguir:

    ```json
    {
        "AzureWebJobsStorage": "{storage connection string}",
        "APPINSIGHTS_INSTRUMENTATIONKEY": "{instrumentation key}"
    }
    ```

    Substitua *{chave de instrumentação}* pela chave de instrumentação do Application insights recurso que você está usando.

1. Guarde as alterações.

### <a name="add-application-insights-logging-provider"></a>Adicionar Application Insights provedor de log

Para aproveitar o log de [Application insights](../azure-monitor/app/app-insights-overview.md) , atualize seu código de registro para fazer o seguinte:

* Adicionar um provedor de log de Application Insights com [filtragem](webjobs-sdk-how-to.md#log-filtering)padrão; todas as informações e os logs de nível superior vão para o console e Application Insights quando você estiver executando localmente.
* Coloque o objeto [LoggerFactory](./webjobs-sdk-how-to.md#logging-and-monitoring) em um bloco de `using` para garantir que a saída do log seja liberada quando o host for encerrado.

1. Instale a última versão estável 3. x do pacote NuGet para o provedor de log de Application Insights: `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`.

   Este é o comando do **console do Gerenciador de pacotes** para a versão 3.0.2:

   ```powershell
   Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -Version 3.0.2
   ```

1. Abra *Program.cs* e substitua o código no método `Main` pelo seguinte código:

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

    Isso adiciona o provedor de Application Insights ao registro em log, usando a chave que você adicionou anteriormente às configurações do aplicativo.

## <a name="test-application-insights-logging"></a>Testar log de Application Insights

Nesta seção, você executará novamente localmente para verificar se os dados de log agora vão Application Insights e também para o console.

1. Use **Gerenciador de servidores** no Visual Studio para criar uma mensagem da fila, da mesma maneira que fazia [anteriormente](#trigger-the-function-in-azure), exceto inserir *Hello app insights!* como o texto da mensagem.

1. Execute o projeto.

   O SDK de trabalhos Web processa a mensagem da fila e você vê os logs na janela do console.

1. Feche a janela do console.

1. Abra o [portal do Azure](https://portal.azure.com/)e vá para o recurso de Application insights.

1. Selecione **Pesquisar**.

   ![Selecionar pesquisa](./media/webjobs-sdk-get-started/select-search.png)

1. Se você não vir o *Hello app insights!* , selecione **Atualizar** periodicamente por vários minutos. (Os logs não aparecem imediatamente porque leva algum tempo para que o cliente do Application Insights libere os logs que ele processa.)

   ![Logs em Application Insights](./media/webjobs-sdk-get-started/logs-in-ai.png)

1. Feche a janela do console.

## <a name="deploy-as-a-webjob"></a>Implantar no Azure

Durante a implantação, você cria uma instância do serviço de aplicativo para executar suas funções. Quando você publica um aplicativo de console do .NET Core no serviço de aplicativo no Azure, ele automaticamente é executado como um WebJob. Para saber mais sobre a publicação, consulte [desenvolver e implantar trabalhos Web usando o Visual Studio](webjobs-dotnet-deploy-vs.md).

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)]

## <a name="trigger-the-function-in-azure"></a>Disparar a função no Azure

1. Verifique se você não está executando localmente (feche a janela do console se ela ainda estiver aberta). Caso contrário, a instância local pode ser a primeira a processar as mensagens da fila que você criar.

1. Na página **fila** no Visual Studio, adicione uma mensagem à fila como antes.

1. Atualize a página da **fila** e a nova mensagem desaparece porque ela foi processada pela função em execução no Azure.

   > [!TIP]
   > Quando você está testando no Azure, use o [modo de desenvolvimento](webjobs-sdk-how-to.md#host-development-settings) para garantir que uma função de gatilho de fila seja invocada imediatamente e evite atrasos devido à [retirada exponencial da sondagem de fila](../azure-functions/functions-bindings-storage-queue.md#trigger---polling-algorithm).

### <a name="view-logs-in-application-insights"></a>Exibir logs no Application Insights

1. Abra o [portal do Azure](https://portal.azure.com/)e vá para o recurso de Application insights.

1. Selecione **Pesquisar**.

1. Se você não vir o *Hello Azure!* , selecione **Atualizar** periodicamente por vários minutos.

   Você vê os logs da função em execução em um WebJob, incluindo o *Olá Azure!* texto que você inseriu na seção anterior.

## <a name="add-an-input-binding"></a>Adicionar uma associação de entrada

As associações de entrada simplificam o código que lê os dados. Para este exemplo, a mensagem da fila será um nome de BLOB e você usará o nome do blob para localizar e ler um blob no armazenamento do Azure.

1. No *functions.cs*, substitua o método `ProcessQueueMessage` pelo código a seguir:

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger("queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
   }
   ```

   Nesse código, `queueTrigger` é uma [expressão de associação](../azure-functions/functions-bindings-expressions-patterns.md), o que significa que ele é resolvido para um valor diferente em tempo de execução.  Em tempo de execução, ele tem o conteúdo da mensagem da fila.

1. Adicionar um `using`:

   ```cs
   using System.IO;
   ```

1. Crie um contêiner de BLOB em sua conta de armazenamento.

   a. Em **Gerenciador de servidores** no Visual Studio, expanda o nó da sua conta de armazenamento, clique com o botão direito do mouse em **BLOBs**e selecione **criar contêiner de blob**.

   b. Na caixa de diálogo **criar contêiner de blob** , insira o *contêiner* como o nome do contêiner e clique em **OK**.

1. Carregue o arquivo *Program.cs* no contêiner de BLOB. (Este arquivo é usado aqui como exemplo; você pode carregar qualquer arquivo de texto e criar uma mensagem de fila com o nome do arquivo.)

   a. Em **Gerenciador de servidores**, clique duas vezes no nó do contêiner que você criou.

   b. Na janela **contêiner** , selecione o botão **carregar** .

   ![Botão de carregamento de BLOB](./media/webjobs-sdk-get-started/blob-upload-button.png)

   c. Localize e selecione *Program.cs*e, em seguida, selecione **OK**.

1. Crie uma mensagem de fila na fila que você criou anteriormente, com *Program.cs* como o texto da mensagem.

   ![Mensagem da fila Program.cs](./media/webjobs-sdk-get-started/queue-msg-program-cs.png)

1. Execute o projeto localmente.

   A mensagem da fila dispara a função, que, em seguida, lê o blob e registra seu comprimento. A saída do console tem a seguinte aparência:

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

As associações de saída simplificam o código que grava dados. Este exemplo modifica o anterior, escrevendo uma cópia do blob em vez de registrar seu tamanho. As associações de armazenamento de BLOBs são incluídas no pacote de extensão de armazenamento do Azure que instalamos anteriormente.

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

1. Crie outra mensagem de fila com *Program.cs* como o texto da mensagem.

1. Execute o projeto localmente.

   A mensagem da fila dispara a função, que, em seguida, lê o blob, registra seu comprimento e cria um novo BLOB. A saída do console é a mesma, mas quando você acessa a janela do contêiner de BLOB e seleciona **Atualizar**, você vê um novo blob chamado *Copy-Program.cs.*

## <a name="republish-the-updates-to-azure"></a>Republicar as atualizações no Azure

1. No **Explorador de Soluções**, clique com o botão direito do rato no projeto e selecione **Publicar**.

1. Na caixa de diálogo **publicar** , verifique se o perfil atual está selecionado e escolha **publicar**. Os resultados da publicação são detalhados na janela **saída** .
 
1. Verifique a função no Azure novamente carregando um arquivo para o contêiner de BLOB e adicionando uma mensagem à fila que é o nome do arquivo carregado. Você vê a mensagem ser removida da fila e uma cópia do arquivo criado no contêiner de BLOB. 

## <a name="next-steps"></a>Passos seguintes

Este artigo mostrou como criar, executar e implantar um projeto webjobs SDK 3. x.

> [!div class="nextstepaction"]
> [Saiba mais sobre o SDK de trabalhos Web](webjobs-sdk-how-to.md)

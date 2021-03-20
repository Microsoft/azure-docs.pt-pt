---
title: Começa com o WebJobs SDK
description: Introdução ao WebJobs SDK para processamento de fundo orientado por eventos. Saiba como aceder aos dados nos serviços Azure e nos serviços de terceiros.
author: ggailey777
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.topic: article
ms.date: 02/18/2019
ms.author: glenga
ms.openlocfilehash: 4a22602dd9638b981cfe3d8bae9b5cdaacbf90dc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91652045"
---
# <a name="get-started-with-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Get started with the Azure WebJobs SDK for event-driven background processing (Introdução ao SDK de WebJobs do Azure para processamento em segundo plano condicionado por eventos)

Este artigo mostra como usar o Visual Studio 2019 para criar um projeto Azure WebJobs SDK, executá-lo localmente e, em seguida, implantá-lo para [o Azure App Service](overview.md). A versão 3.x do WebJobs SDK suporta aplicações de consola .NET Core e .NET Framework. Para saber mais sobre o trabalho com o WebJobs SDK, consulte [Como utilizar o Azure WebJobs SDK para processamento de fundo orientado por eventos.](webjobs-sdk-how-to.md)

Este artigo mostra-lhe como implementar WebJobs como uma aplicação de consola .NET Core. Para implementar webJobs como uma aplicação de consola .NET Framework, consulte [webJobs como aplicações de consola .NET Framework](webjobs-dotnet-deploy-vs.md#webjobs-as-net-framework-console-apps). Se estiver interessado na versão 2.x da WebJobs SDK, que suporta apenas o .NET Framework, consulte [Desenvolver e implementar WebJobs utilizando o Visual Studio - Azure App Service](webjobs-dotnet-deploy-vs.md).

## <a name="prerequisites"></a>Pré-requisitos

* [Instale o Visual Studio 2019](/visualstudio/install/) com a carga de trabalho **de desenvolvimento do Azure.** Se já tem o Visual Studio mas não tem essa carga de trabalho, adicione a carga de trabalho selecionando **Ferramentas > Obter Ferramentas e Funcionalidades**.

* Você deve ter [uma conta Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) para publicar o seu projeto WebJobs SDK para a Azure.

## <a name="create-a-project"></a>Criar um projeto

1. Em Visual Studio, **selecione Criar um Novo Projeto.**

2. Selecione **App de consola (.NET Core)**.

3. Nomeie o projeto *WebJobsSDKSample* e, em seguida, **selecione Create**.

   ![Caixa de diálogo Novo Projeto](./media/webjobs-sdk-get-started/new-project.png)

## <a name="webjobs-nuget-packages"></a>Pacotes WebJobs NuGet

1. Instale a versão mais recente estável 3.x do [ `Microsoft.Azure.WebJobs.Extensions` pacote NuGet,](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions/)que inclui `Microsoft.Azure.WebJobs` .

     Aqui está o comando da consola Do Gestor de **Pacotes:**

     ```powershell
     Install-Package Microsoft.Azure.WebJobs.Extensions -version <3_X_VERSION>
     ```

    Neste comando, `<3_X_VERSION>` substitua-o por uma versão suportada do pacote. 

## <a name="create-the-host"></a>Criar o Anfitrião

O hospedeiro é o recipiente de tempo de execução para funções que ouve para as funções de gatilhos e chamadas. Os passos seguintes criam um anfitrião que implementa [`IHost`](/dotnet/api/microsoft.extensions.hosting.ihost) , que é o Anfitrião Genérico em ASP.NET Core.

1. No *Programa.cs,* adicione estas `using` declarações:

    ```cs
    using System.Threading.Tasks;
    using Microsoft.Extensions.Hosting;
    ```

1. Substitua o método `Main` pelo código abaixo:

    ```cs
    static async Task Main()
    {
        var builder = new HostBuilder();
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                });
        var host = builder.Build();
        using (host)
        {
            await host.RunAsync();
        }
    }
    ```

Em ASP.NET Core, as configurações do anfitrião são definidas por métodos de chamada no [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) caso. Para obter mais informações, consulte [.NET Generic Host](/aspnet/core/fundamentals/host/generic-host). O `ConfigureWebJobs` método de extensão inicializa o anfitrião WebJobs. Em `ConfigureWebJobs` , você inicializa extensões webJobs específicas e definir propriedades dessas extensões.  

## <a name="enable-console-logging"></a>Ativar o registo de consolas

Nesta secção, configura o registo de consolas que utiliza a [estrutura de registo do núcleo de ASP.NET](/aspnet/core/fundamentals/logging).

1. Instale a versão mais recente estável do [ `Microsoft.Extensions.Logging.Console` pacote NuGet,](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/)que inclui `Microsoft.Extensions.Logging` .

   Aqui está o comando da consola Do Gestor de **Pacotes:**

   ```powershell
   Install-Package Microsoft.Extensions.Logging.Console -version <3_X_VERSION>
   ```
   Neste comando, `<3_X_VERSION>` substitua-o por uma versão 3.x suportada do pacote.

1. No *Programa.cs,* adicione uma `using` declaração:

   ```cs
   using Microsoft.Extensions.Logging;
   ```

1. Ligue para o [`ConfigureLogging`](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging) [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) método. O [`AddConsole`](/dotnet/api/microsoft.extensions.logging.consoleloggerextensions.addconsole) método adiciona o registo da consola à configuração.

    ```cs
    builder.ConfigureLogging((context, b) =>
    {
        b.AddConsole();
    });
    ```

    O `Main` método agora é assim:

    ```cs
    static async Task Main()
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
            await host.RunAsync();
        }
    }
    ```

    Esta atualização faz o seguinte:

    * Desativa a registo do painel de [instrumentos](https://github.com/Azure/azure-webjobs-sdk/wiki/Queues#logs). O painel de instrumentos é uma ferramenta de monitorização antiga, e a exploração de painéis não é recomendada para cenários de produção de alta produção.
    * Adiciona o fornecedor de consola com [filtragem predefinida](webjobs-sdk-how-to.md#log-filtering).

Agora, pode adicionar uma função que é desencadeada por mensagens que chegam numa fila de Armazenamento Azure.

## <a name="install-the-storage-binding-extension"></a>Instalar a extensão de enlace do Armazenamento

A partir da versão 3.x, deve instalar explicitamente a extensão de ligação de armazenamento exigida pelo WebJobs SDK. Em versões anteriores, as ligações de armazenamento foram incluídas no SDK.

1. Instale a versão mais recente estável do pacote  [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) NuGet, versão 3.x. 

    Aqui está o comando da consola Do Gestor de **Pacotes:**

    ```powershell
    Install-Package Microsoft.Azure.WebJobs.Extensions.Storage -Version <3_X_VERSION>
    ```
    
    Neste comando, `<3_X_VERSION>` substitua-o por uma versão suportada do pacote. 

2. No método de `ConfigureWebJobs` extensão, ligue para o `AddAzureStorage` método da instância para [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) inicializar a extensão de armazenamento. Neste ponto, o `ConfigureWebJobs` método parece o seguinte exemplo:

    ```cs
    builder.ConfigureWebJobs(b =>
                    {
                        b.AddAzureStorageCoreServices();
                        b.AddAzureStorage();
                    });
    ```

## <a name="create-a-function"></a>Criar uma função

1. Clique com o botão direito no projeto, **selecione Add**  >  **New Item...**, escolha **Classe,** nomeie as novas *funções* de ficheiro classe C#.cs , e **selecione Adicionar**.

1. Em Funções.cs, substitua o modelo gerado pelo seguinte código:
    
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

   O `QueueTrigger` atributo diz ao tempo de execução para ligar para esta função quando uma nova mensagem é escrita numa fila de armazenamento Azure chamada `queue` . O conteúdo da mensagem de fila é fornecido ao código-método no `message` parâmetro. O corpo do método é onde se processa os dados do gatilho. Neste exemplo, o código apenas regista a mensagem.

   O `message` parâmetro não tem de ser uma corda. Também pode ligar-se a um objeto JSON, a uma matriz de byte ou a um objeto [CloudQueueMessage.](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage) [Consulte a utilização do gatilho da fila](../azure-functions/functions-bindings-storage-queue-trigger.md?tabs=csharp#usage). Cada tipo de encadernação (como filas, bolhas ou tabelas) tem um conjunto diferente de tipos de parâmetros a que se pode ligar.

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

O Emulador de Armazenamento Azure que funciona localmente não tem todas as funcionalidades de que o WebJobs SDK precisa. Assim, nesta secção você cria uma conta de armazenamento em Azure e configura o projeto para usá-lo. Se já tem uma conta de armazenamento, salte para o passo 6.

1. Abra **o Server Explorer** no estúdio Visual e inscreva-se no Azure. Clique com o botão direito no nó **Azure** e, em seguida, **selecione Connect to Microsoft Azure Subscription**.

   ![Iniciar sessão no Azure](./media/webjobs-sdk-get-started/sign-in.png)

1. Sob o nó **Azure** no **Server Explorer,** clique no **armazenamento** à direita e, em seguida, selecione **Criar a conta de Armazenamento**.

   ![Criar menu de conta de armazenamento](./media/webjobs-sdk-get-started/create-storage-account-menu.png)

1. Na caixa de diálogo **'Conta de Armazenamento' Criar,** insira um nome único para a conta de armazenamento.

1. Escolha a mesma **Região** em que criou a sua app App Service ou uma região próxima de si.

1. Selecione **Criar**.

   ![Criar conta de Armazenamento](./media/webjobs-sdk-get-started/create-storage-account.png)

1. Sob o nó **de armazenamento** no **Server Explorer,** selecione a nova conta de Armazenamento. Na janela **Propriedades,** selecione a elipse **(...)** à direita do campo de valor **da corda de ligação.**

   ![Elipse de cadeia de ligação](./media/webjobs-sdk-get-started/conn-string-ellipsis.png)

1. Copie a cadeia de ligação e guarde este valor em algum lugar onde possa copiá-la novamente.

   ![Cadeia de ligação de cópia](./media/webjobs-sdk-get-started/copy-key.png)

## <a name="configure-storage-to-run-locally"></a>Configure armazenamento para executar localmente

O WebJobs SDK procura a cadeia de ligação de armazenamento nas Definições de Aplicação em Azure. Quando corre localmente, procura este valor no ficheiro de configuração local ou em variáveis ambientais.

1. Clique com o botão direito no projeto, **selecione Add**  >  **New Item...**, escolha **o ficheiro de configuração JavaScript JSON,** nomeie o novo ficheiro *appsettings.jsno* ficheiro e selecione **Adicionar**. 

1. No novo ficheiro, adicione um `AzureWebJobsStorage` campo, como no exemplo seguinte:

    ```json
    {
        "AzureWebJobsStorage": "{storage connection string}"
    }
    ```

1. Substitua *{cadeia de ligação de armazenamento}* com a cadeia de ligação que copiou anteriormente.

1. Selecione o *appsettings.jsno* ficheiro no Solution Explorer e na janela **Propriedades,** desave a Cópia para o **Diretório de Saída** para Copiar se for mais **recente**.

Mais tarde, irá adicionar a mesma definição de aplicação de cadeia de ligação na sua aplicação no Azure App Service.

## <a name="test-locally"></a>Teste local

Nesta secção, você constrói e executa o projeto localmente e desencadeia a função criando uma mensagem de fila.

1. Pressione **ctrl+F5** para executar o projeto.

   A consola mostra que o tempo de funcionamento encontrou a sua função e está à espera que as mensagens de fila a desencadeiem. A seguinte saída é gerada pelo anfitrião v3.x:

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

1. Feche a janela da consola.

1. No **Server Explorer** no Visual Studio, expanda o nó para a sua nova conta de armazenamento e, em seguida, clique com o botão direito em **Filas**.

1. Selecione **Criar Fila**.

1. Introduza a *fila* como o nome da fila e, em seguida, selecione **OK**.

   ![Screenshot que mostra onde cria a fila e lhe dá o nome de "fila". ](./media/webjobs-sdk-get-started/create-queue.png)

1. Clique no nó à direita para a nova fila e, em seguida, selecione **Ver Fila**.

1. Selecione o ícone **'Adicionar mensagem'.**

   ![Screenshot que realça o ícone 'Adicionar Mensagem'.](./media/webjobs-sdk-get-started/create-queue-message.png)

1. No diálogo **'Adicionar Mensagem',** insira *Hello World!* como o **texto mensagem**, e, em seguida, selecione **OK**. Há agora uma mensagem na fila.

   ![Criar fila](./media/webjobs-sdk-get-started/hello-world-text.png)

1. Execute novamente o projeto.

   Como usou o `QueueTrigger` atributo na `ProcessQueueMessage` função, o tempo de execução weJobs SDK ouve mensagens de fila quando começa. Encontra uma nova mensagem de fila na fila chamada *fila* e chama a função.

   Devido ao [recuo exponencial das sondagens,](../azure-functions/functions-bindings-storage-queue-trigger.md?tabs=csharp#polling-algorithm)pode levar até 2 minutos para o tempo de funcionamento encontrar a mensagem e invocar a função. Este tempo de espera pode ser reduzido funcionando no [modo de desenvolvimento](webjobs-sdk-how-to.md#host-development-settings).

   A saída da consola é assim:

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

1. Feche a janela da consola. 

1. Volta para a janela da fila e refresca-a. A mensagem desapareceu, uma vez que foi processada pela sua função em funcionamento local. 

## <a name="add-application-insights-logging"></a>Adicionar Insights de Aplicação registação

Quando o projeto funciona em Azure, não é possível monitorizar a execução da função visualizando a saída da consola. A solução de monitorização que recomendamos é [Application Insights](../azure-monitor/app/app-insights-overview.md). Para obter mais informações, consulte [as Funções Do Monitor Azure](../azure-functions/functions-monitoring.md).

Nesta secção, faz as seguintes tarefas para configurar o registo de Insights de Aplicação antes de implementar para o Azure:

* Certifique-se de que tem uma aplicação de Serviço de Aplicações e uma instância de Insights de Aplicação para trabalhar.
* Configure a aplicação do Serviço de Aplicações para utilizar a instância De Insights de Aplicação e a conta de armazenamento que criou anteriormente.
* Configurar o projeto de registo de insights de aplicação.

### <a name="create-app-service-app-and-application-insights-instance"></a>Criar app de Serviço de Aplicações e Aplicações Insights

1. Se ainda não tiver uma aplicação de Serviço de Aplicações que possa utilizar, [crie uma](quickstart-dotnet-framework.md). Quando cria a sua aplicação, também pode criar um recurso de Insights de Aplicação conectado. Quando o fizeres, o `APPINSIGHTS_INSTRUMENTATIONKEY` está definido para ti na tua aplicação.

1. Se ainda não tiver um recurso Application Insights que possa utilizar, [crie um](../azure-monitor/app/create-new-resource.md ). Descoda **o tipo de aplicação** para **General**, e ignore as secções que seguem Copiar **a tecla de instrumentação**.

1. Se já tiver um recurso Application Insights que pretende utilizar, [copie a tecla de instrumentação](../azure-monitor/app/create-new-resource.md#copy-the-instrumentation-key).

### <a name="configure-app-settings"></a>Configurar as definições da aplicação 

1. No **Server Explorer** em Visual Studio, expanda o nó de Serviço de **Aplicações** sob **Azure**.

1. Expanda o grupo de recursos em que a sua aplicação de Serviço de Aplicações está e, em seguida, clique com o botão direito na sua aplicação de Serviço de Aplicações.

1. Selecione **Ver Definições**.

1. Na caixa **'Cordas de Ligação',** adicione a seguinte entrada.

   |Name  |cadeia de conexão  |Tipo de base de dados|
   |---------|---------|------|
   |AzureWebJobsStorage | {a cadeia de ligação de armazenamento que copiou anteriormente}|Personalizado|

1. Se a caixa **'Definições de Aplicação'** não tiver uma chave de instrumentação de Insights de Aplicação, adicione a que copiou anteriormente. (A chave de instrumentação pode já estar lá, dependendo da forma como criou a aplicação do Serviço de Aplicações.)

   |Name  |Valor  |
   |---------|---------|
   |APPINSIGHTS_INSTRUMENTATIONKEY | {chave de instrumentação} |

1. Substitua *{instrumentação tecla}* com a tecla de instrumentação do recurso Application Insights que está a utilizar.

1. Selecione **Guardar**.

1. Adicione a ligação Application Insights ao projeto para que possa executá-lo localmente. No *appsettings.jsem* arquivo, adicione um `APPINSIGHTS_INSTRUMENTATIONKEY` campo, como no seguinte exemplo:

    ```json
    {
        "AzureWebJobsStorage": "{storage connection string}",
        "APPINSIGHTS_INSTRUMENTATIONKEY": "{instrumentation key}"
    }
    ```

    Substitua *{instrumentação tecla}* com a tecla de instrumentação do recurso Application Insights que está a utilizar.

1. Guarde as alterações.

### <a name="add-application-insights-logging-provider"></a>Adicionar fornecedor de registo de Insights de Aplicação

Para tirar partido do registo de Insights de [Aplicação,](../azure-monitor/app/app-insights-overview.md) atualize o seu código de registo para fazer o seguinte:

* Adicione um fornecedor de registo de insights de aplicação com [filtragem](webjobs-sdk-how-to.md#log-filtering)predefinido . Ao correr localmente, todas as informações e registos de nível superior são escritos tanto para a consola como para o Application Insights.
* Coloque o objeto [LoggerFactory](./webjobs-sdk-how-to.md#logging-and-monitoring) num `using` bloco para garantir que a saída de registo é lavada quando o hospedeiro sai.

1. Instale a versão mais recente estável 3.x do [ `Microsoft.Azure.WebJobs.Logging.ApplicationInsights` pacote NuGet](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/).

   Aqui está o comando da consola Do Gestor de **Pacotes:**

   ```powershell
   Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -Version <3_X_VERSION>
   ```
    Neste comando, `<3_X_VERSION>` substitua-o por uma versão suportada do pacote.

1. *Programa Aberto.cs* e substitua o código no `Main` método pelo seguinte código:

    ```cs
    static async Task Main()
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
                        b.AddApplicationInsightsWebJobs(o => o.InstrumentationKey = instrumentationKey);
                    }
                });
        var host = builder.Build();
        using (host)
        {
            await host.RunAsync();
        }
    }
    ```

    Isto adiciona o fornecedor De Insights de Aplicação à sessão madeireira, utilizando a chave que adicionou anteriormente às definições da sua aplicação.

## <a name="test-application-insights-logging"></a>Registo de insights de aplicação de teste

Nesta secção, corre novamente localmente para verificar se os dados de registo vão agora para o Application Insights, bem como para a consola.

1. Use **o Server Explorer** no Visual Studio para criar uma mensagem de fila como fez [anteriormente](#test-locally), exceto insira Hello App *Insights!* como o texto da mensagem.

1. Executar o projeto.

   O WebJobs SDK processa a mensagem de fila e vê os registos na janela da consola.

1. Feche a janela da consola.

1. Vá ao [portal Azure](https://portal.azure.com/) para ver o seu recurso Application Insights. Procure e selecione **Insights de Aplicação**.

1. Escolha a sua instância De Insights de Aplicação.

1. Selecione **Pesquisar**.

   ![Selecione Pesquisar](./media/webjobs-sdk-get-started/select-search.png)

1. Se não vir o *Hello App Insights!* mensagem, **selecione Refresh** periodicamente por vários minutos. (Os registos não aparecem imediatamente, porque demora algum tempo para o cliente Application Insights descarregar os registos que processa.)

   ![Registos em Insights de Aplicações](./media/webjobs-sdk-get-started/logs-in-ai.png)

1. Feche a janela da consola.

## <a name="deploy-to-azure"></a><a name="deploy-as-a-webjob"></a>Implementar no Azure

Durante a implementação, cria uma instância de serviço de aplicações para executar as suas funções. Quando publica uma aplicação de consola .NET Core para o App Service em Azure, é automaticamente executado como WebJob. Para saber mais sobre a publicação, consulte [Desenvolver e implementar WebJobs utilizando o Visual Studio](webjobs-dotnet-deploy-vs.md).

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)]

## <a name="trigger-the-function-in-azure"></a>Desencadear a função em Azure

1. Certifique-se de que não está a funcionar localmente (feche a janela da consola se ainda estiver aberta). Caso contrário, a instância local pode ser a primeira a processar quaisquer mensagens de fila que crie.

1. Na página **queue** no Estúdio Visual, adicione uma mensagem à fila como antes.

1. Refresque a página **da Fila** e a nova mensagem desaparece porque foi processada pela função em execução em Azure.

   > [!TIP]
   > Quando estiver a testar em Azure, utilize [o modo de desenvolvimento](webjobs-sdk-how-to.md#host-development-settings) para garantir que uma função de gatilho de fila é invocada imediatamente e evite atrasos devido ao recuo [exponencial das sondagens](../azure-functions/functions-bindings-storage-queue-trigger.md?tabs=csharp#polling-algorithm)na fila .

### <a name="view-logs-in-application-insights"></a>Ver registos em Insights de Aplicação

1. Abra o [portal Azure](https://portal.azure.com/)e aceda ao seu recurso Application Insights.

1. Selecione **Pesquisar**.

1. Se não vir o *Hello Azure!* mensagem, **selecione Refresh** periodicamente por vários minutos.

   Você vê os registos da função em execução em um WebJob, incluindo o *Hello Azure!* texto que inseriu na secção anterior.

## <a name="add-an-input-binding"></a>Adicione uma ligação de entrada

As ligações de entrada simplificam o código que lê dados. Para este exemplo, a mensagem da fila será um nome blob e você usará o nome blob para encontrar e ler uma bolha no Azure Storage.

1. Em *Funções.cs,* substitua o `ProcessQueueMessage` método pelo seguinte código:

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger("queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
   }
   ```

   Neste código, `queueTrigger` é uma expressão [vinculativa,](../azure-functions/functions-bindings-expressions-patterns.md)o que significa que resolve um valor diferente no tempo de execução.  No tempo de execução, tem o conteúdo da mensagem de fila.

1. Adicione `using` um:

   ```cs
   using System.IO;
   ```

1. Crie um recipiente de bolhas na sua conta de armazenamento.

   a. No **Server Explorer** in Visual Studio, expanda o nó para a sua conta de armazenamento, clique com o botão direito **Blobs** e, em seguida, selecione **Create Blob Container**.

   b. No diálogo **do recipiente Create Blob,** *introduza* o recipiente como nome do recipiente e, em seguida, clique em **OK**.

1. Faça o upload do ficheiro *.cs Programa* para o recipiente blob. (Este ficheiro é usado aqui como exemplo; pode carregar qualquer ficheiro de texto e criar uma mensagem de fila com o nome do ficheiro.)

   a. No **Server Explorer,** clique duas vezes no nó para o recipiente que criou.

   b. Na janela **do Recipiente,** selecione o **botão Upload.**

   ![Botão de upload blob](./media/webjobs-sdk-get-started/blob-upload-button.png)

   c. Encontre e selecione *Programa.cs*, e, em seguida, selecione **OK**.

1. Crie uma mensagem de fila na fila que criou anteriormente, com *o Programa.cs* como o texto da mensagem.

   ![Programa de mensagens de fila.cs](./media/webjobs-sdk-get-started/queue-msg-program-cs.png)

1. Executar o projeto localmente.

   A mensagem de fila ativa a função, que depois lê a bolha e regista o seu comprimento. A saída da consola é assim:

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

As ligações de saída simplificam o código que escreve dados. Este exemplo modifica o anterior escrevendo uma cópia da bolha em vez de registar o seu tamanho. As ligações de armazenamento blob estão incluídas no pacote de extensão de armazenamento Azure que instalamos anteriormente.

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

1. Crie outra mensagem de fila com *o Programa.cs* como texto da mensagem.

1. Executar o projeto localmente.

   A mensagem de fila ativa a função, que depois lê a bolha, regista o seu comprimento e cria uma nova bolha. A saída da consola é a mesma, mas quando se vai à janela do recipiente blob e seleciona **Refresh**, vê-se um novo blob nomeado *programa de cópia.cs.*

## <a name="republish-the-updates-to-azure"></a>Reeditar as atualizações para a Azure

1. No **Explorador de Soluções**, clique com o botão direito do rato no projeto e selecione **Publicar**.

1. No diálogo **publicar,** certifique-se de que o perfil atual está selecionado e, em seguida, escolha **Publicar**. Os resultados da publicação são detalhados na janela **saída.**
 
1. Verifique a função em Azure, enviando novamente um ficheiro para o recipiente blob e adicionando uma mensagem à fila que é o nome do ficheiro carregado. Vê a mensagem ser removida da fila e uma cópia do ficheiro criado no recipiente blob. 

## <a name="next-steps"></a>Passos seguintes

Este artigo mostrou-lhe como criar, executar e implementar um projeto WebJobs SDK 3.x.

> [!div class="nextstepaction"]
> [Saiba mais sobre o WebJobs SDK](webjobs-sdk-how-to.md)
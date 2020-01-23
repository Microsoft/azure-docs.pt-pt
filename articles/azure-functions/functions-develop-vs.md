---
title: Desenvolver Azure Functions usando o Visual Studio
description: Saiba como desenvolver e testar Azure Functions usando as ferramentas do Azure Functions para o Visual Studio 2019.
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: 7aceae256c4fc4d18615e181bbcb7425e659955d
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76547375"
---
# <a name="develop-azure-functions-using-visual-studio"></a>Desenvolver Azure Functions usando o Visual Studio  

O Visual Studio permite que você desenvolva, teste C# e implante funções de biblioteca de classes no Azure. Se essa experiência for sua primeira com Azure Functions, você poderá aprender mais em [uma introdução ao Azure Functions](functions-overview.md).

O Visual Studio oferece os seguintes benefícios ao desenvolver suas funções: 

* Edite, crie e execute funções em seu computador de desenvolvimento local. 
* Publique seu projeto de Azure Functions diretamente no Azure e crie recursos do Azure conforme necessário. 
* Use C# atributos para declarar associações de função diretamente no C# código.
* Desenvolva e implante C# funções pré-compiladas. As funções pré-instaladas fornecem um desempenho de início frio melhor do que C# as funções baseadas em script. 
* Codifique suas funções no C# enquanto tem todos os benefícios do desenvolvimento do Visual Studio. 

Este artigo fornece detalhes sobre como usar o Visual Studio para desenvolver C# funções de biblioteca de classes e publicá-las no Azure. Antes de ler este artigo, conclua o guia de [início rápido do Functions para Visual Studio](functions-create-your-first-function-visual-studio.md). 

Salvo indicação em contrário, os procedimentos e exemplos mostrados são para o Visual Studio 2019. 

## <a name="prerequisites"></a>Pré-requisitos

Azure Functions ferramentas estão incluídas na carga de trabalho de desenvolvimento do Azure do Visual Studio, começando com o Visual Studio 2017. Certifique-se de incluir a carga de trabalho de **desenvolvimento do Azure** em sua instalação do Visual Studio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Outros recursos necessários, como uma conta de armazenamento do Azure, são criados em sua assinatura durante o processo de publicação.

> [!NOTE]
> No Visual Studio 2017, a carga de trabalho de desenvolvimento do Azure instala as ferramentas de Azure Functions como uma extensão separada. Ao atualizar o Visual Studio 2017, verifique também se você está usando a [versão mais recente](#check-your-tools-version) das ferramentas de Azure functions. As seções a seguir mostram como verificar e (se necessário) atualizar sua extensão de ferramentas de Azure Functions no Visual Studio 2017. 
>
> Ignore esta seção ao usar o Visual Studio 2019.

### <a name="check-your-tools-version"></a>Verifique a versão das ferramentas no Visual Studio 2017

1. Partir do **ferramentas** menu, escolha **extensões e atualizações**. Expanda as **ferramentas** de > **instaladas** e escolha **Azure Functions e ferramentas de trabalhos da Web**.

    ![Verificar a versão das ferramentas de funções](./media/functions-develop-vs/functions-vstools-check-functions-tools.png)

1. Anote a **versão**instalada. Você pode comparar essa versão com a versão mais recente listada [nas notas de versão](https://github.com/Azure/Azure-Functions/blob/master/VS-AzureTools-ReleaseNotes.md). 

1. Se tiver uma versão mais antiga, Atualize as suas ferramentas no Visual Studio, conforme mostrado na seção a seguir.

### <a name="update-your-tools-in-visual-studio-2017"></a>Atualize suas ferramentas no Visual Studio 2017

1. Na caixa de diálogo **extensões e atualizações** , expanda **atualizações** > **Visual Studio Marketplace**, escolha **Azure Functions e ferramentas de trabalhos da Web** e selecione **Atualizar**.

    ![Atualizar a versão das ferramentas do Functions](./media/functions-develop-vs/functions-vstools-update-functions-tools.png)   

1. Após a atualização das ferramentas é transferida, feche o Visual Studio ao acionar que as ferramentas de atualização através do instalador do VSIX.

1. No instalador, escolha **OK** para iniciar e, em seguida, **Modificar** para atualizar as ferramentas. 

1. Depois que a atualização for concluída, escolha **fechar** e reinicie o Visual Studio.

> [!NOTE]  
> No Visual Studio 2019 e posterior, a extensão de ferramentas de Azure Functions é atualizada como parte do Visual Studio.  

## <a name="create-an-azure-functions-project"></a>Criar um projeto das Funções do Azure

[!INCLUDE [Create a project using the Azure Functions](../../includes/functions-vstools-create.md)]

O modelo de projeto cria C# um projeto, instala o `Microsoft.NET.Sdk.Functions` pacote NuGet e define a estrutura de destino. O novo projeto tem os seguintes arquivos:

* **host. JSON**: permite configurar o host do functions. Essas configurações se aplicam quando são executadas localmente e no Azure. Para obter mais informações, consulte [referência de host. JSON](functions-host-json.md).

* **local. Settings. JSON**: mantém as configurações usadas ao executar funções localmente. Essas configurações não são usadas durante a execução no Azure. Para obter mais informações, consulte [Local Settings File](#local-settings-file).

    >[!IMPORTANT]
    >Como o arquivo local. Settings. JSON pode conter segredos, você deve excluí-lo do seu controle de origem do projeto. A configuração **copiar para diretório de saída** para esse arquivo deve sempre ser **cópia se for mais recente**. 

Para obter mais informações, consulte [funções de projeto de biblioteca de classes](functions-dotnet-class-library.md#functions-class-library-project).

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

As configurações em local. Settings. JSON não são carregadas automaticamente quando você publica o projeto. Para certificar-se de que essas configurações também existam em seu aplicativo de funções no Azure, você deve carregá-las depois de publicar o projeto. Para saber mais, confira [configurações do aplicativo de funções](#function-app-settings).

Os valores em **connectionStrings** nunca são publicados.

Os valores das configurações do aplicativo de funções também podem ser lidos em seu código como variáveis de ambiente. Para obter mais informações, consulte [variáveis de ambiente](functions-dotnet-class-library.md#environment-variables).

## <a name="configure-the-project-for-local-development"></a>Configurar o projeto para desenvolvimento local

O tempo de execução do Functions usa uma conta de armazenamento do Azure internamente. Para todos os tipos de gatilho diferentes de HTTP e WebHooks, você deve definir a chave **Values. AzureWebJobsStorage** para uma cadeia de conexão de conta de armazenamento do Azure válida. Seu aplicativo de funções também pode usar o [emulador de armazenamento do Azure](../storage/common/storage-use-emulator.md) para a configuração de conexão **AzureWebJobsStorage** que é exigida pelo projeto. Para usar o emulador, defina o valor de **AzureWebJobsStorage** como `UseDevelopmentStorage=true`. Altere essa configuração para uma cadeia de conexão de conta de armazenamento real antes da implantação.

Para definir a cadeia de conexão da conta de armazenamento:

1. No Visual Studio, abra o **Cloud Explorer**, expanda **conta de armazenamento** > **sua conta de armazenamento**e, na guia **Propriedades** , copie o valor da **cadeia de conexão primária** .

2. Em seu projeto, abra o arquivo local. Settings. JSON e defina o valor da chave **AzureWebJobsStorage** para a cadeia de conexão que você copiou.

3. Repita a etapa anterior para adicionar chaves exclusivas à matriz de **valores** para quaisquer outras conexões exigidas por suas funções. 

## <a name="add-a-function-to-your-project"></a>Adicionar uma função ao seu projeto

Em C# funções de biblioteca de classes, as associações usadas pela função são definidas pela aplicação de atributos no código. Quando você cria seus gatilhos de função a partir dos modelos fornecidos, os atributos de gatilho são aplicados para você. 

1. No **Explorador de Soluções**, clique com o botão direito do rato no nó do projeto e selecione **Adicionar** > **Novo item**. Selecione **Azure function**, digite um **nome** para a classe e clique em **Adicionar**.

2. Escolha seu gatilho, defina as propriedades de associação e clique em **criar**. O exemplo a seguir mostra as configurações ao criar uma função disparada pelo armazenamento de filas. 

    ![Criar uma função disparada por fila](./media/functions-develop-vs/functions-vstools-create-queuetrigger.png)

    Este exemplo de gatilho usa uma cadeia de conexão com uma chave chamada **QueueStorage**. Essa configuração de cadeia de conexão deve ser definida no [arquivo local. Settings. JSON](functions-run-local.md#local-settings-file).

3. Examine a classe recém-adicionada. Você vê um método **Run** estático, que é atribuído com o atributo **FunctionName** . Esse atributo indica que o método é o ponto de entrada para a função.

    Por exemplo, a classe C# a seguir representa uma função básica disparada pelo armazenamento de filas:

    ```csharp
    using System;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Host;
    using Microsoft.Extensions.Logging;

    namespace FunctionApp1
    {
        public static class Function1
        {
            [FunctionName("QueueTriggerCSharp")]
            public static void Run([QueueTrigger("myqueue-items", 
                Connection = "QueueStorage")]string myQueueItem, ILogger log)
            {
                log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
            }
        }
    }
    ```

    Um atributo específico de associação é aplicado a cada parâmetro de associação fornecido ao método de ponto de entrada. O atributo usa as informações de associação como parâmetros. No exemplo anterior, o primeiro parâmetro tem um atributo **QueueTrigger** aplicado, indicando a função disparada pela fila. O nome da fila e o nome da configuração da cadeia de conexão são passados como parâmetros para o atributo **QueueTrigger** . Para obter mais informações, consulte [associações de armazenamento de filas do Azure para Azure Functions](functions-bindings-storage-queue.md#trigger).

Você pode usar o procedimento acima para adicionar mais funções ao seu projeto de aplicativo de funções. Cada função no projeto pode ter um gatilho diferente, mas uma função deve ter exatamente um gatilho. Para obter mais informações, consulte [Azure Functions os conceitos de gatilhos e associações](functions-triggers-bindings.md).

## <a name="add-bindings"></a>Adicionar enlaces

Assim como acontece com gatilhos, as associações de entrada e saída são adicionadas à sua função como atributos de associação. Adicione associações a uma função da seguinte maneira:

1. Verifique se você [configurou o projeto para o desenvolvimento local](#configure-the-project-for-local-development).

2. Adicione o pacote de extensão NuGet apropriado para a associação específica. Para obter mais informações, [consulte C# desenvolvimento local usando o Visual Studio](./functions-bindings-register.md#local-csharp) no artigo gatilhos e associações. Os requisitos de pacote NuGet específicos da associação são encontrados no artigo de referência para a associação. Por exemplo, encontre requisitos de pacote para o gatilho de hubs de eventos no [artigo de referência de associação de hubs de eventos](functions-bindings-event-hubs.md).

3. Se houver configurações de aplicativo que a associação precisa, adicione-as à coleção de **valores** no [arquivo de configuração local](functions-run-local.md#local-settings-file). Esses valores são usados quando a função é executada localmente. Quando a função é executada no aplicativo de funções no Azure, as [configurações do aplicativo de funções](#function-app-settings) são usadas.

4. Adicione o atributo de associação apropriado à assinatura do método. No exemplo a seguir, uma mensagem de fila dispara a função e a associação de saída cria uma nova mensagem de fila com o mesmo texto em uma fila diferente.

    ```csharp
    public static class SimpleExampleWithOutput
    {
        [FunctionName("CopyQueueMessage")]
        public static void Run(
            [QueueTrigger("myqueue-items-source", Connection = "AzureWebJobsStorage")] string myQueueItem, 
            [Queue("myqueue-items-destination", Connection = "AzureWebJobsStorage")] out string myQueueItemCopy,
            ILogger log)
        {
            log.LogInformation($"CopyQueueMessage function processed: {myQueueItem}");
            myQueueItemCopy = myQueueItem;
        }
    }
    ```
   A conexão com o armazenamento de filas é obtida na configuração de `AzureWebJobsStorage`. Para obter mais informações, consulte o artigo de referência para a associação específica. 

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="testing-functions"></a>Testar funções

As Ferramentas de Núcleo das Funções do Azure permitem-lhe executar o projeto de funções do Azure no seu computador de programação local. Deverá instalar essas ferramentas da primeira vez que iniciar uma função do Visual Studio.

Para testar a sua função, prima F5. Se solicitado, aceite o pedido do Visual Studio para transferir e instalar as ferramentas de Núcleo das Funções do Azure (CLI). Também poderá ativar a exceção da firewall para que todas as ferramentas possam aceitar os pedidos de HTTP.

Com o projeto em execução, você pode testar seu código da mesma forma que testaria a função implantada. Para obter mais informações, consulte [estratégias para testar seu código no Azure Functions](functions-test-a-function.md). Quando executado no modo de depuração, os pontos de interrupção são atingidos no Visual Studio como esperado. 

<!---
For an example of how to test a queue triggered function, see the [queue triggered function quickstart tutorial](functions-create-storage-queue-triggered-function.md#test-the-function).  
-->

Para saber mais sobre como usar o Azure Functions Core Tools, confira [codificar e testar o Azure Functions localmente](functions-run-local.md).

## <a name="publish-to-azure"></a>Publicar no Azure

Ao publicar do Visual Studio, um dos dois métodos de implantação é usado:

* [Implantação da Web](functions-deployment-technologies.md#web-deploy-msdeploy): empacota e implanta aplicativos do Windows em qualquer servidor IIS.
* Implantação [de zip com execução do pacote habilitada](functions-deployment-technologies.md#zip-deploy): recomendado para implantações de Azure functions.

Use as etapas a seguir para publicar seu projeto em um aplicativo de funções no Azure.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="function-app-settings"></a>Definições da Aplicação de funções

Todas as configurações que você adicionou no local. Settings. JSON também devem ser adicionadas ao aplicativo de funções no Azure. Essas configurações não são carregadas automaticamente quando você publica o projeto.

A maneira mais fácil de carregar as configurações necessárias para seu aplicativo de funções no Azure é usar o link **gerenciar configurações de aplicativo...** que é exibido depois que você publica com êxito o projeto.

![](./media/functions-develop-vs/functions-vstools-app-settings.png)

Isso exibe a caixa de diálogo **configurações do aplicativo** para o aplicativo de funções, onde você pode adicionar novas configurações do aplicativo ou modificar as existentes.

![](./media/functions-develop-vs/functions-vstools-app-settings2.png)

**Local** representa um valor de configuração no arquivo local. Settings. JSON, e **Remote** é a configuração atual no aplicativo de funções no Azure.  Escolha **Adicionar configuração** para criar uma nova configuração de aplicativo. Use o link **Inserir valor do local** para copiar um valor de configuração para o campo **remoto** . As alterações pendentes são gravadas no arquivo de configurações local e no aplicativo de funções quando você seleciona **OK**.

> [!NOTE]
> Por padrão, o arquivo local. Settings. JSON não é verificado no controle do código-fonte. Isso significa que, quando você clona um projeto de funções locais do controle do código-fonte, o projeto não tem um arquivo local. Settings. JSON. Nesse caso, você precisa criar manualmente o arquivo local. Settings. JSON na raiz do projeto para que a caixa de diálogo **configurações do aplicativo** funcione conforme o esperado. 

Você também pode gerenciar as configurações do aplicativo de uma dessas outras maneiras:

* [Usando o portal do Azure](functions-how-to-use-azure-function-app-settings.md#settings).
* [Usando a opção `--publish-local-settings` publish no Azure Functions Core Tools](functions-run-local.md#publish).
* [Usando o CLI do Azure](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set).

## <a name="monitoring-functions"></a>Funções de monitoramento

A maneira recomendada para monitorar a execução de suas funções é integrar seu aplicativo de funções com o Aplicativo Azure insights. Quando você cria um aplicativo de funções no portal do Azure, essa integração é feita para você por padrão. No entanto, quando você cria seu aplicativo de funções durante a publicação do Visual Studio, a integração em seu aplicativo de funções no Azure não é feita.

Para habilitar Application Insights para seu aplicativo de funções:

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

Para saber mais, consulte [monitorar Azure Functions](functions-monitoring.md).

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o Azure Functions Core Tools, confira [codificar e testar o Azure Functions localmente](functions-run-local.md).

Para saber mais sobre como desenvolver funções como bibliotecas de classes do .NET, confira [ C# Azure Functions referência do desenvolvedor](functions-dotnet-class-library.md). Este artigo também fornece links para exemplos de como usar atributos para declarar os vários tipos de associações com suporte pelo Azure Functions.    

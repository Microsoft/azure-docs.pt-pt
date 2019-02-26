---
title: Desenvolver as funções do Azure com o Visual Studio | Documentos da Microsoft
description: Saiba como desenvolver e testar as funções do Azure com as ferramentas de funções do Azure para Visual Studio 2017.
services: functions
documentationcenter: .net
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: glenga
ms.openlocfilehash: 58c7506b71ade8ff70519b895995153896006ade
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56804678"
---
# <a name="develop-azure-functions-using-visual-studio"></a>Desenvolver as funções do Azure com o Visual Studio  

Ferramentas de funções do Azure para Visual Studio 2017 é uma extensão para o Visual Studio que lhe permitem desenvolver, testar e implementar funções c# no Azure. Se esta experiência é o primeiro com as funções do Azure, pode saber mais em [uma introdução às funções do Azure](functions-overview.md).

As ferramentas de funções do Azure fornece as seguintes vantagens: 

* Editar, criar e executar as funções no seu computador de desenvolvimento local. 
* Publicar o seu projeto de funções do Azure diretamente no Azure. 
* Utilização de atributos de WebJobs para declarar ligações de função diretamente no código c# em vez de manter um Function separado para definições de ligação.
* Programe e implemente as funções do c# compiladas previamente. Funções previamente está a fornecem um desempenho de arranque a frio melhor que funções de baseada em script do c#. 
* As suas funções em c# de código enquanto tem todos os benefícios de desenvolvimento do Visual Studio. 

Este artigo fornece detalhes sobre como usar as ferramentas de funções do Azure para Visual Studio 2017 para desenvolver C# funções e publique-os para o Azure. Antes de ler este artigo, deverá concluir os [início rápido das funções para o Visual Studio](functions-create-your-first-function-visual-studio.md). 

> [!IMPORTANT]
> Não misture o desenvolvimento local com o desenvolvimento de portais na mesma function app. Quando publicar a partir de um projeto local para uma aplicação de funções, o processo de implantação substitui quaisquer funções que desenvolveu no portal.

## <a name="prerequisites"></a>Pré-requisitos

Ferramentas de funções do Azure está incluída na carga de trabalho de desenvolvimento do Azure de [Visual Studio 2017 versão 15.5](https://www.visualstudio.com/vs/), ou uma versão posterior. Certifique-se de que inclui a **desenvolvimento do Azure** carga de trabalho na sua instalação do Visual Studio 2017:

![Instalar o Visual Studio 2017 com a carga de trabalho de desenvolvimento do Azure](./media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

Certifique-se de que o Visual Studio está atualizado e que está a utilizar o [versão mais recente](#check-your-tools-version) das ferramentas das funções do Azure.

### <a name="other-requirements"></a>Outros requisitos

Para criar e implementar as funções, também terá de:

* Uma subscrição ativa do Azure. Se não tiver uma subscrição do Azure, [contas gratuitas](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) estão disponíveis.

* Uma conta do Armazenamento do Azure. Para criar uma conta de armazenamento, veja [criar uma conta de armazenamento](../storage/common/storage-quickstart-create-account.md).

### <a name="check-your-tools-version"></a>Verificar a sua versão de ferramentas

1. Partir do **ferramentas** menu, escolha **extensões e atualizações**. Expanda **instalada** > **ferramentas** e escolha **funções do Azure e ferramentas do Web Jobs**.

    ![Verificar a versão de ferramentas de funções](./media/functions-develop-vs/functions-vstools-check-functions-tools.png)

2. Tenha em atenção o instalado **versão**. Pode comparar essa versão com a versão mais recente listada [nas notas de versão](https://github.com/Azure/Azure-Functions/blob/master/VS-AzureTools-ReleaseNotes.md). 

3. Se tiver uma versão mais antiga, Atualize as suas ferramentas no Visual Studio, conforme mostrado na seção a seguir.

### <a name="update-your-tools"></a>Atualizar as suas ferramentas

1. Na **extensões e atualizações** caixa de diálogo, expanda **atualizações** > **Visual Studio Marketplace**, escolha **as funções do Azure e ferramentas do Web Jobs**  e selecione **atualização**.

    ![Atualizar a versão de ferramentas de funções](./media/functions-develop-vs/functions-vstools-update-functions-tools.png)   

2. Após a atualização das ferramentas é transferida, feche o Visual Studio ao acionar que as ferramentas de atualização através do instalador do VSIX.

3. No instalador, escolha **OK** para iniciar e, em seguida **modificar** para atualizar as ferramentas. 

4. Depois de concluída a atualização, escolha **fechar** e reinicie o Visual Studio.

## <a name="create-an-azure-functions-project"></a>Criar um projeto das Funções do Azure

[!INCLUDE [Create a project using the Azure Functions](../../includes/functions-vstools-create.md)]

O modelo de projeto cria um projeto c#, instala o `Microsoft.NET.Sdk.Functions` pacote NuGet e define a estrutura de destino. Funções 1.x destinos do .NET Framework e as funções de destinos de 2.x .NET Standard. O novo projeto tem os seguintes ficheiros:

* **host.json**: Permite-lhe configurar o anfitrião de funções. Estas definições aplicam-se tanto ao executar localmente e no Azure. Para obter mais informações, consulte [referência de Host. JSON](functions-host-json.md).

* **local.settings.json**: Mantém as definições utilizadas ao executar as funções localmente. Estas definições não são utilizadas pelo Azure, elas são usadas pelos [ferramentas de núcleo de funções do Azure](functions-run-local.md). Utilize este ficheiro para especificar as definições da aplicação para variáveis necessárias para as suas funções. Adicione um novo item para o **valores** matriz para cada ligação exigida pelos enlaces de funções no seu projeto. Para obter mais informações, consulte [arquivo de configurações Local](functions-run-local.md#local-settings-file) no artigo de ferramentas de núcleo de funções do Azure.

    >[!IMPORTANT]
    >Porque o ficheiro Settings pode conter segredos, deve exclui-lo de seu controle de origem do projeto. O **Copy to Output Directory** definição para este ficheiro deve ser sempre **copiar se for mais recente**. 

Para obter mais informações, consulte [projeto de biblioteca de classes de funções](functions-dotnet-class-library.md#functions-class-library-project).

## <a name="configure-the-project-for-local-development"></a>Configurar o projeto de desenvolvimento local

O runtime das funções utiliza uma conta de armazenamento do Azure internamente. Para acionam todos os tipos diferentes de HTTP e webhooks, tem de definir o **Values.AzureWebJobsStorage** chave para uma cadeia de ligação de conta de armazenamento do Azure válida. A aplicação de função também pode utilizar o [emulador de armazenamento do Azure](../storage/common/storage-use-emulator.md) para o **AzureWebJobsStorage** ligação definir isto é necessário para o projeto. Para utilizar o emulador, defina o valor de **AzureWebJobsStorage** para `UseDevelopmentStorage=true`. Tem de alterar esta definição para uma ligação de armazenamento real antes da implantação.

Para definir a cadeia de ligação de conta de armazenamento:

1. No Visual Studio, abra **Cloud Explorer**, expanda **conta de armazenamento** > **sua conta de armazenamento**, em seguida, selecione **propriedades**e copie o **cadeia de ligação primária** valor.

2. No seu projeto, abra o ficheiro Settings e defina o valor do **AzureWebJobsStorage** chave para a cadeia de ligação que copiou.

3. Repita o passo anterior para adicionar chaves exclusivas para o **valores** matriz para todas as outras ligações necessária para as suas funções.

## <a name="add-a-function-to-your-project"></a>Adicionar uma função ao seu projeto

Nas funções compiladas previamente, as ligações usadas pela função são definidas aplicando atributos no código. Ao utilizar as ferramentas de funções do Azure para criar as suas funções a partir de modelos fornecidos, esses atributos são aplicados por si. 

1. No **Explorador de Soluções**, clique com o botão direito do rato no nó do projeto e selecione **Adicionar** > **Novo item**. Selecione **função do Azure**, escreva um **nome** da classe e clique em **Add**.

2. Escolher o acionador, defina as propriedades de associação e clique em **criar**. O exemplo seguinte mostra as definições, quando a função de acionada por criar um armazenamento de filas. 

    ![Criar uma função acionada por fila](./media/functions-develop-vs/functions-vstools-create-queuetrigger.png)

    Este exemplo de Acionador utiliza uma cadeia de ligação com uma chave denominada **QueueStorage**. Esta definição de cadeia de ligação deve ser definida na [Settings ficheiro](functions-run-local.md#local-settings-file).

3. Examine a classe recém-adicionada. Verá uma estática **execute** método, o que é atribuído com o **FunctionName** atributo. Este atributo indica que o método é o ponto de entrada para a função.

    Por exemplo, a seguinte classe c# representa uma função de acionada pelo armazenamento de fila básica:

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
            public static void Run([QueueTrigger("myqueue-items", Connection = "QueueStorage")]string myQueueItem, ILogger log)
            {
                log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
            }
        }
    }
    ```
    Um atributo específico do enlace é aplicado a cada parâmetro de ligação fornecido para o método de ponto de entrada. O atributo utiliza as informações de associação como parâmetros. No exemplo anterior, o primeiro parâmetro tem um **QueueTrigger** atributo aplicado, que indica a função acionada por fila. O nome da fila e o nome de definição da cadeia de ligação são passados como parâmetros para o **QueueTrigger** atributo. Para obter mais informações, consulte [enlaces de armazenamento de filas do Azure para as funções do Azure](functions-bindings-storage-queue.md#trigger---c-example).
    
Pode utilizar o procedimento acima para adicionar mais funções ao seu projeto de aplicação de função. Cada função no projeto pode ter um acionador diferente, mas uma função tem de ter exatamente um acionador. Para obter mais informações, consulte [acionadores de funções do Azure e conceitos de enlaces](functions-triggers-bindings.md).

## <a name="add-bindings"></a>Adicionar enlaces

Tal como acontece com disparadores, os enlaces de entrada e saída são adicionados à sua função como atributos de enlace. Adicione enlaces a uma função da seguinte forma:

1. Certifique-se de que tem [configurado o projeto de desenvolvimento local](#configure-the-project-for-local-development).

2. Adicione o pacote de extensão do NuGet adequado para o enlace específico. Para obter mais informações, consulte [Local desenvolvimento em c# com o Visual Studio](./functions-bindings-register.md#local-csharp) no artigo Acionadores e enlaces. Os requisitos de pacote de NuGet específico de ligação se encontram no artigo de referência para o enlace. Por exemplo, localizar os requisitos do pacote para o acionador de Hubs de eventos no [artigo de referência de ligação de Hubs de eventos](functions-bindings-event-hubs.md).

3. Se existirem definições de aplicação que precisa da ligação, adicione-os para o **valores** coleção na [ficheiro de definição local](functions-run-local.md#local-settings-file). Estes valores são utilizados quando a função é executada localmente. Quando a função é executada na aplicação de função do Azure, o [as definições da aplicação de função](#function-app-settings) são utilizados.

4. Adicione o atributo de enlace adequado para a assinatura do método. No exemplo a seguir, uma mensagem de fila aciona a função e o enlace de saída cria uma nova mensagem de fila com o mesmo texto numa fila diferente.

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
A ligação ao armazenamento de filas é obtida a partir do `AzureWebJobsStorage` definição. Para obter mais informações, consulte o artigo de referência para o enlace específico. 

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="testing-functions"></a>Testar funções

As Ferramentas de Núcleo das Funções do Azure permitem-lhe executar o projeto de funções do Azure no seu computador de programação local. Deverá instalar essas ferramentas da primeira vez que iniciar uma função do Visual Studio.

Para testar a sua função, prima F5. Se solicitado, aceite o pedido do Visual Studio para transferir e instalar as ferramentas de Núcleo das Funções do Azure (CLI). Também poderá ativar a exceção da firewall para que todas as ferramentas possam aceitar os pedidos de HTTP.

Com o projeto em execução, pode testar seu código, como testaria função implementada. Para obter mais informações, consulte [estratégias para testar seu código nas funções do Azure](functions-test-a-function.md). Quando em execução no modo de depuração, pontos de interrupção são atingidos no Visual Studio, conforme o esperado. 

<!---
For an example of how to test a queue triggered function, see the [queue triggered function quickstart tutorial](functions-create-storage-queue-triggered-function.md#test-the-function).  
-->

Para saber mais sobre como utilizar as ferramentas de núcleo de funções do Azure, veja [código e testar as funções do Azure localmente](functions-run-local.md).

## <a name="publish-to-azure"></a>Publicar no Azure

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="function-app-settings"></a>Definições da Aplicação de funções

Quaisquer configurações adicionadas no Settings tem de ser adicionadas também para a aplicação de funções no Azure. Estas definições não são carregadas automaticamente quando publica o projeto.

A maneira mais fácil para carregar as definições necessárias para a aplicação de funções no Azure é usar o **gerir as definições da aplicação...**  ligação que é apresentada depois de publicar seu projeto com êxito.

![](./media/functions-develop-vs/functions-vstools-app-settings.png)

Esta ação apresenta os **as definições da aplicação** caixa de diálogo para a aplicação de funções, onde pode adicionar novas definições de aplicação ou modificar as existentes.

![](./media/functions-develop-vs/functions-vstools-app-settings2.png)

**Local** representa um valor de definição no arquivo Settings, e **remoto** é a definição atual na aplicação de função no Azure.  Escolher **Adicionar definição** para criar uma nova definição de aplicação. Utilize o **inserir o valor a partir de locais** link para copiar um valor de definição para o **remoto** campo. As alterações pendentes são escritos para o arquivo de configurações local e a aplicação de função quando seleciona **OK**.

Também pode gerir as definições da aplicação de uma das seguintes outras formas:

* [No portal do Azure](functions-how-to-use-azure-function-app-settings.md#settings).
* [Utilizar o `--publish-local-settings` publicar opção nas ferramentas de núcleo de funções do Azure](functions-run-local.md#publish).
* [Com a CLI do Azure](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set).

## <a name="monitoring-functions"></a>Funções de monitorização

É a forma recomendada para monitorizar a execução da sua função no Azure através da integração com o Azure Application Insights. Quando cria uma aplicação de funções no portal do Azure, esta integração é feita para, por predefinição. No entanto, quando criar a sua aplicação de função durante a publicação do Visual Studio, não é feita a integração na sua aplicação de função no Azure. Em vez disso, obtém incorporada Registro em log, que não é recomendado.

Para ativar o Application Insights para a sua aplicação de função no Azure:

1. Criar uma instância do Application Insights no [portal do Azure](https://portal.azure.com) e copie a chave de instrumentação. Para saber como, veja [manualmente ligar um recurso do App Insights](functions-monitoring.md#manually-connect-an-app-insights-resource).  

1. Adicionar uma aplicação definição denominada `APPINSIGHTS_INSTRUMENTATIONKEY` às definições da aplicação de função no Azure, conforme descrito na [definições da aplicação de função](#function-app-settings). Esta definição de aplicação contém a chave de instrumentação que criou no passo anterior.

1. Remover o `AzureWebJobsDashboard` definição de aplicação a partir da aplicação de função no Azure, o que desativa o registo incorporado.  

Para obter mais informações, consulte [as funções do Azure de Monitor](functions-monitoring.md).

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre as ferramentas de núcleo de funções do Azure, veja [código e testar as funções do Azure localmente](functions-run-local.md).

Para saber mais sobre o desenvolvimento das funções como bibliotecas de classes do .NET, veja [referência para programadores do funções do Azure c#](functions-dotnet-class-library.md). Este artigo também contém ligações para exemplos de como utilizar atributos para declarar os vários tipos de ligações compatíveis com as funções do Azure.    

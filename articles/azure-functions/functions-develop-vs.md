---
title: Desenvolver as Funções do Azure com o Visual Studio
description: Aprenda a desenvolver e testar funções Azure utilizando ferramentas de funções Azure para o Estúdio Visual 2019.
ms.custom: vs-azure, devx-track-csharp
ms.topic: conceptual
ms.date: 06/10/2020
ms.openlocfilehash: 877c82e375b0ea469071402b83fadbd634177f3f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97655820"
---
# <a name="develop-azure-functions-using-visual-studio"></a>Desenvolver as Funções do Azure com o Visual Studio  

O Visual Studio permite-lhe desenvolver, testar e implementar funções de biblioteca de classe C# para Azure. Se esta experiência for a sua primeira com funções Azure, consulte [Uma introdução às Funções Azure](functions-overview.md).

O Visual Studio proporciona os seguintes benefícios quando desenvolve as suas funções: 

* Editar, construir e executar funções no seu computador de desenvolvimento local. 
* Publique o seu projeto Azure Functions diretamente para a Azure e crie recursos Azure conforme necessário. 
* Utilize atributos C# para declarar as ligações de função diretamente no código C#.
* Desenvolver e implementar funções C# pré-compiladas. As funções pré-cumpridas proporcionam um melhor desempenho a frio do que as funções baseadas em scripts C#. 
* Codi o código das suas funções em C# enquanto tem todos os benefícios do desenvolvimento do Estúdio Visual. 

Este artigo fornece detalhes sobre como usar o Visual Studio para desenvolver funções de biblioteca de classe C# e publicá-las no Azure. Antes de ler este artigo, considere completar o [quickstart funções para o Visual Studio](functions-create-your-first-function-visual-studio.md). 

Salvo indicação em contrário, os procedimentos e exemplos apresentados são para o Visual Studio 2019. 

## <a name="prerequisites"></a>Pré-requisitos

- Ferramentas de funções Azure. Para adicionar Ferramentas de Função Azure, inclua a carga de trabalho de **desenvolvimento Azure** na sua instalação visual Studio. A azure Functions Tools está disponível na carga de trabalho de desenvolvimento do Azure a partir do Visual Studio 2017.

- Outros recursos de que necessita, como uma conta Azure Storage, são criados na sua subscrição durante o processo de publicação.

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> No Visual Studio 2017, a carga de trabalho de desenvolvimento do Azure instala as Ferramentas de Funções Azure como uma extensão separada. Ao atualizar a instalação do Visual Studio 2017, certifique-se de que está a utilizar a [versão mais recente](#check-your-tools-version) das ferramentas Azure Functions. As seguintes secções mostram-lhe como verificar e (se necessário) atualizar a extensão das Ferramentas de Funções Azure no Visual Studio 2017. 
>
> Ignore estas secções se estiver a utilizar o Visual Studio 2019.

### <a name="check-your-tools-version-in-visual-studio-2017"></a><a name="check-your-tools-version"></a>Consulte a versão das suas ferramentas no Visual Studio 2017

1. No menu **Ferramentas,** escolha **Extensões e Atualizações.** Expanda   >  **as Ferramentas Instaladas** e, em seguida, escolha **funções Azure e Ferramentas de Trabalho Web**.

    ![Verifique a versão das ferramentas funções](./media/functions-develop-vs/functions-vstools-check-functions-tools.png)

1. Note a **versão** instalada e compare esta versão com a versão mais recente listada nas [notas de lançamento](https://github.com/Azure/Azure-Functions/blob/master/VS-AzureTools-ReleaseNotes.md). 

1. Se a sua versão for mais antiga, atualize as suas ferramentas no Visual Studio, como mostrado na secção seguinte.

### <a name="update-your-tools-in-visual-studio-2017"></a>Atualize as suas ferramentas no Visual Studio 2017

1. No diálogo **extensões e atualizações,** expanda **as atualizações**  >  **Do Mercado do Estúdio Visual,** escolha **Funções Azure e Ferramentas de Emprego Web** e selecione **Update**.

    ![Atualizar a versão de ferramentas funções](./media/functions-develop-vs/functions-vstools-update-functions-tools.png)   

1. Depois de descarregar a atualização das ferramentas, selecione **Close** e, em seguida, feche o Visual Studio para ativar a atualização das ferramentas com o Instalador VSIX.

1. No instalador VSIX, escolha **Modificar** para atualizar as ferramentas. 

1. Depois de concluída a atualização, escolha **Close** e reinicie o Visual Studio.

> [!NOTE]  
> No Visual Studio 2019 e mais tarde, a extensão das ferramentas Azure Functions é atualizada como parte do Visual Studio.  

## <a name="create-an-azure-functions-project"></a>Criar um projeto das Funções do Azure

[!INCLUDE [Create a project using the Azure Functions](../../includes/functions-vstools-create.md)]

Depois de criar um projeto Azure Functions, o modelo de projeto cria um projeto C#, instala o `Microsoft.NET.Sdk.Functions` pacote NuGet e define a estrutura-alvo. O novo projeto tem os seguintes ficheiros:

* **host.js:** Permite-lhe configurar o anfitrião funções. Estas definições aplicam-se tanto quando funcionam localmente como em Azure. Para mais informações, consulte [host.jsa referência.](functions-host-json.md)

* **local.settings.jsem**: Mantém as definições utilizadas durante o funcionamento local. Estas definições não são usadas quando correm em Azure. Para obter mais informações, consulte [o ficheiro de definições locais](#local-settings-file).

    >[!IMPORTANT]
    >Como a local.settings.jsno ficheiro pode conter segredos, deve excluí-lo do controlo de origem do seu projeto. Certifique-se de que a definição **copy to Output Directory** para este ficheiro está definida para **copiar se for mais recente**. 

Para mais informações, consulte o [projeto da biblioteca da classe Functions.](functions-dotnet-class-library.md#functions-class-library-project)

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

O Visual Studio não faz o upload automaticamente das definições em local.settings.jsquando publica o projeto. Para se certificar de que estas definições também existem na sua aplicação de função em Azure, faça o upload depois de publicar o seu projeto. Para obter mais informações, consulte [as definições da aplicação Function](#function-app-settings). Os valores de uma `ConnectionStrings` coleção nunca são publicados.

O seu código também pode ler os valores de definições de aplicações de função como variáveis ambientais. Para obter mais informações, consulte [as variáveis Ambiente.](functions-dotnet-class-library.md#environment-variables)

## <a name="configure-your-build-output-settings"></a>Configure as definições de saída de construção

Ao construir um projeto Azure Functions, as ferramentas de construção otimizam a saída de modo a que apenas uma cópia de quaisquer conjuntos partilhados com as funções sejam preservados. O resultado é uma construção otimizada que poupa o máximo de espaço possível. No entanto, quando se muda para uma versão mais recente de qualquer um dos seus conjuntos de projetos, as ferramentas de construção podem não saber que estes conjuntos devem ser preservados. Para se certificar de que estes conjuntos são preservados durante o processo de otimização, pode especificá-los utilizando `FunctionsPreservedDependencies` elementos no ficheiro do projeto (.csproj):

```xml
  <ItemGroup>
    <FunctionsPreservedDependencies Include="Microsoft.AspNetCore.Http.dll" />
    <FunctionsPreservedDependencies Include="Microsoft.AspNetCore.Http.Extensions.dll" />
    <FunctionsPreservedDependencies Include="Microsoft.AspNetCore.Http.Features.dll" />
  </ItemGroup>
```

## <a name="configure-the-project-for-local-development"></a>Configure o projeto para o desenvolvimento local

O tempo de execução das Funções utiliza uma conta de Armazenamento Azure internamente. Para todos os tipos de gatilho que não http e webhooks, desloque a `Values.AzureWebJobsStorage` chave para uma cadeia de ligação de conta Azure Storage válida. A sua aplicação de função também pode usar o [Emulador de Armazenamento Azure](../storage/common/storage-use-emulator.md) para a `AzureWebJobsStorage` definição de ligação que é exigida pelo projeto. Para utilizar o emulador, deite o valor de `AzureWebJobsStorage` `UseDevelopmentStorage=true` . Altere esta definição para uma cadeia de ligação de conta de armazenamento real antes de ser implantada.

Para definir o fio de ligação da conta de armazenamento:

1. No Estúdio Visual, selecione **View**  >  **Cloud Explorer**.

2. No **Cloud Explorer,** expanda as **Contas de Armazenamento** e, em seguida, selecione a sua conta de armazenamento. No separador **Propriedades,** copie o valor **da cadeia de ligação primária.**

2. No seu projeto, abra a local.settings.jsno ficheiro e desabrocha o valor da `AzureWebJobsStorage` chave para a cadeia de ligação que copiou.

3. Repita o passo anterior para adicionar teclas únicas à `Values` matriz para quaisquer outras ligações necessárias às suas funções. 

## <a name="add-a-function-to-your-project"></a>Adicione uma função ao seu projeto

Nas funções da biblioteca de classes C#, as ligações utilizadas pela função são definidas aplicando atributos no código. Quando cria os gatilhos da sua função a partir dos modelos fornecidos, os atributos do gatilho são aplicados para si. 

1. No **Solution Explorer,** clique com o botão direito no nó do projeto e selecione **Adicionar**  >  **Novo Item**. 

2. Selecione **Azure Function**, insira um **Nome** para a classe e, em seguida, selecione **Adicionar**.

3. Escolha o gatilho, desacione as propriedades de encadernação e, em seguida, selecione **OK**. O exemplo a seguir mostra as definições para criar uma função de gatilho de armazenamento de fila. 

    ![Criar uma função de gatilho de armazenamento de fila](./media/functions-develop-vs/functions-vstools-create-queuetrigger.png)

    Este exemplo do gatilho utiliza uma cadeia de ligação com uma chave chamada `QueueStorage` . Defina esta definição de cadeia de ligação no [local.settings.jsno ficheiro](functions-run-local.md#local-settings-file).

4. Examine a classe recém-adicionada. Vê-se um método estático `Run()` que é atribuído com o `FunctionName` atributo. Este atributo indica que o método é o ponto de entrada para a função.

    Por exemplo, a seguinte classe C# representa uma função básica de gatilho de armazenamento de fila:

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

É aplicado um atributo específico de encadernação a cada parâmetro de encadernação fornecido ao método do ponto de entrada. O atributo toma a informação vinculativa como parâmetros. No exemplo anterior, o primeiro parâmetro tem um `QueueTrigger` atributo aplicado, indicando uma função de gatilho de armazenamento de fila. O nome de configuração do nome da fila e da ligação são passados como parâmetros para o `QueueTrigger` atributo. Para obter mais informações, consulte [as ligações de armazenamento da Fila Azure para as funções Azure](functions-bindings-storage-queue-trigger.md).

Utilize o procedimento acima para adicionar mais funções ao seu projeto de aplicação de função. Cada função no projeto pode ter um gatilho diferente, mas uma função deve ter exatamente um gatilho. Para obter mais informações, consulte [os conceitos de gatilhos e encadernações do Azure Functions](functions-triggers-bindings.md).

## <a name="add-bindings"></a>Adicionar enlaces

Tal como acontece com os gatilhos, as ligações de entrada e saída são adicionadas à sua função como atributos de ligação. Adicione encadernações a uma função da seguinte forma:

1. Certifique-se de que [configura o projeto para o desenvolvimento local.](#configure-the-project-for-local-development)

2. Adicione o pacote de extensão NuGet apropriado para a ligação específica. 

   Para mais informações, consulte [a biblioteca da classe C# com o Visual Studio](./functions-bindings-register.md#local-csharp). Encontre os requisitos de pacote NuGet específicos de encadernação no artigo de referência para a encadernação. Por exemplo, encontre os requisitos do pacote para o gatilho de Centros de Eventos no artigo de referência vinculativo do [Event Hubs](functions-bindings-event-hubs.md).

3. Se existirem configurações de aplicações que a ligação necessita, adicione-as à `Values` coleção no ficheiro de [configuração local.](functions-run-local.md#local-settings-file) 

   A função utiliza estes valores quando funciona localmente. Quando a função é executada na aplicação de função em Azure, utiliza as definições da [aplicação de função](#function-app-settings).

4. Adicione o atributo de encadernação adequado à assinatura do método. No exemplo seguinte, uma mensagem de fila ativa a função e a ligação de saída cria uma nova mensagem de fila com o mesmo texto numa fila diferente.

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
   A ligação ao armazenamento da fila obtém-se a partir da `AzureWebJobsStorage` definição. Para mais informações, consulte o artigo de referência para a vinculação específica. 

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="testing-functions"></a>Testar funções

As Ferramentas de Núcleo das Funções do Azure permitem-lhe executar o projeto de funções do Azure no seu computador de programação local. Para obter mais informações, consulte [Trabalhar com as ferramentas principais das funções Azure.](functions-run-local.md) É solicitado que instale estas ferramentas na primeira vez que inicia uma função a partir do Visual Studio. 

Para testar a sua função no Estúdio Visual:

1. Pressione F5. Se solicitado, aceite o pedido do Visual Studio para transferir e instalar as ferramentas de Núcleo das Funções do Azure (CLI). Também pode ser necessário ativar uma exceção de firewall para que as ferramentas possam lidar com pedidos HTTP.

2. Com o projeto em funcionamento, teste o seu código como se testaria uma função implantada. 

   Para obter mais informações, consulte [Estratégias para testar o seu código em Funções Azure](functions-test-a-function.md). Quando se gere o Visual Studio em modo de depuração, os pontos de rutura são atingidos como esperado.

<!---
For an example of how to test a queue triggered function, see the [queue triggered function quickstart tutorial](functions-create-storage-queue-triggered-function.md#test-the-function).  
-->


## <a name="publish-to-azure"></a>Publicar no Azure

Ao publicar a partir do Visual Studio, utiliza um de dois métodos de implantação:

* [Web Deploy](functions-deployment-technologies.md#web-deploy-msdeploy): Pacotes e implementa aplicações do Windows para qualquer servidor IIS.
* [Implementação zip com pacote de funcionamento ativado](functions-deployment-technologies.md#zip-deploy): Recomendado para implantações de funções Azure.

Utilize os seguintes passos para publicar o seu projeto numa aplicação de função em Azure.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="function-app-settings"></a>Definições da Aplicação de funções

Como o Visual Studio não faz o upload destas definições automaticamente quando publica o projeto, quaisquer definições que adicione no local.settings.jsem que também deve adicionar à aplicação de função em Azure.

A forma mais fácil de carregar as definições necessárias para a sua aplicação de função em Azure é selecionar o link 'Gerir a **azure's App Service** que aparece depois de publicar com sucesso o seu projeto.

:::image type="content" source="./media/functions-develop-vs/functions-vstools-app-settings.png" alt-text="Definições na janela publicar":::

A seleção deste link exibe o diálogo **de definições de Aplicação** para a aplicação de função, onde pode adicionar novas definições de aplicação ou modificar as existentes.

![Definições da aplicação](./media/functions-develop-vs/functions-vstools-app-settings2.png)

**Local** apresenta um valor de definição no local.settings.jsno ficheiro e **o Remote** apresenta um valor de definição atual na aplicação de função em Azure. Escolha adicionar a **definição** para criar uma nova definição de aplicação. Utilize o **valor de Inserção a partir do** link Local para copiar um valor de definição para o campo **Remote.** As alterações pendentes são escritas para o ficheiro de definições locais e para a aplicação de função quando selecionar **OK**.

> [!NOTE]
> Por predefinição, o local.settings.jsno ficheiro não é verificado no controlo de origem. Isto significa que se clonar um projeto local de Funções a partir do controlo de fontes, o projeto não tem uma local.settings.jsem arquivo. Neste caso, é necessário criar manualmente o local.settings.jsno ficheiro na raiz do projeto para que o diálogo **de definições de aplicação** funcione como esperado. 

Também pode gerir as definições de aplicações de uma destas outras formas:

* [Utilize o portal Azure](functions-how-to-use-azure-function-app-settings.md#settings).
* [Utilize a `--publish-local-settings` opção de publicação nas Ferramentas Principais de Funções Azure](functions-run-local.md#publish).
* [Utilize o Azure CLI](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set).

## <a name="monitoring-functions"></a>Funções de monitorização

A forma recomendada de monitorizar a execução das suas funções é integrando a sua aplicação de função com a Azure Application Insights. Quando cria uma aplicação de função no portal Azure, esta integração é feita por padrão. No entanto, quando cria a sua aplicação de função durante a publicação do Visual Studio, a integração na sua aplicação de função no Azure não está feita. Para aprender a ligar os Insights de Aplicação à sua aplicação de função, consulte [a integração de Enable Application Insights](configure-monitoring.md#enable-application-insights-integration).

Para saber mais sobre a monitorização utilizando o Application Insights, consulte [as Funções Do Monitor Azure](functions-monitoring.md).

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre as ferramentas principais do Azure Functions, consulte [Ferramentas Principais de Trabalho com Funções Azure](functions-run-local.md).

Para obter mais informações sobre o desenvolvimento de funções como bibliotecas de classe .NET, consulte [a referência do programador Azure Functions C#](functions-dotnet-class-library.md). Este artigo também se liga a exemplos de como usar atributos para declarar os vários tipos de encadernações suportados por Funções Azure.    

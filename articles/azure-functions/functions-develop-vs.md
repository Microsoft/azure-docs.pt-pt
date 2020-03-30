---
title: Desenvolver as Funções do Azure com o Visual Studio
description: Saiba como desenvolver e testar funções azure utilizando ferramentas de funções Azure para o Estúdio Visual 2019.
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: b3d683153a1e70f4c65dcc5e401f4ee702af8b49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277105"
---
# <a name="develop-azure-functions-using-visual-studio"></a>Desenvolver as Funções do Azure com o Visual Studio  

O Visual Studio permite-lhe desenvolver, testar e implementar funções de biblioteca de classe C# para o Azure. Se esta experiência for a primeira com as Funções Azure, pode aprender mais na [introdução às Funções Azure.](functions-overview.md)

O Visual Studio proporciona os seguintes benefícios ao desenvolver as suas funções: 

* Editar, construir e executar funções no seu computador de desenvolvimento local. 
* Publique o seu projeto Funções Azure diretamente para o Azure e crie recursos Azure conforme necessário. 
* Utilize atributos C# para declarar ligações de função diretamente no código C#.
* Desenvolver e implementar funções C# pré-compiladas. As funções pré-cumpridas proporcionam um melhor desempenho a frio do que as funções baseadas em scripts C#. 
* Codifique as suas funções em C# enquanto tem todos os benefícios do desenvolvimento do Estúdio Visual. 

Este artigo fornece detalhes sobre como usar o Estúdio Visual para desenvolver funções da biblioteca da classe C# e publicá-las no Azure. Antes de ler este artigo, deve concluir as [funções de arranque rápido para o Estúdio Visual](functions-create-your-first-function-visual-studio.md). 

Salvo indicação em contrário, os procedimentos e exemplos mostrados são para o Estúdio Visual 2019. 

## <a name="prerequisites"></a>Pré-requisitos

As Ferramentas de Funções Azure estão incluídas na carga de trabalho de desenvolvimento azure do Estúdio Visual a partir do Visual Studio 2017. Certifique-se de que inclui a carga de trabalho de **desenvolvimento do Azure** na instalação do Estúdio Visual.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Outros recursos de que necessita, como uma conta de Armazenamento Azure, são criados na sua subscrição durante o processo de publicação.

> [!NOTE]
> No Visual Studio 2017, a carga de trabalho de desenvolvimento do Azure instala as Ferramentas de Funções Azure como uma extensão separada. Quando atualizar o seu Visual Studio 2017, certifique-se também de que está a utilizar a [versão mais recente](#check-your-tools-version) das ferramentas Funções Azure. As seguintes secções mostram-lhe como verificar e (se necessário) atualizar a extensão das ferramentas de funções Azure no Estúdio Visual 2017. 
>
> Por favor, ignore esta secção ao utilizar o Visual Studio 2019.

### <a name="check-your-tools-version-in-visual-studio-2017"></a><a name="check-your-tools-version"></a>Consulte a sua versão de ferramentas no Visual Studio 2017

1. No menu **Ferramentas,** escolha **Extensões e Atualizações.** Expandir**ferramentas** **instaladas** > e escolher **funções Azure e ferramentas de trabalho web.**

    ![Verifique a versão das ferramentas Funções](./media/functions-develop-vs/functions-vstools-check-functions-tools.png)

1. Note a **versão**instalada . Pode comparar esta versão com a versão mais recente listada [nas notas de lançamento.](https://github.com/Azure/Azure-Functions/blob/master/VS-AzureTools-ReleaseNotes.md) 

1. Se a sua versão for mais antiga, atualize as suas ferramentas no Visual Studio, como mostra a seguinte secção.

### <a name="update-your-tools-in-visual-studio-2017"></a>Atualize as suas ferramentas no Visual Studio 2017

1. No diálogo de **extensões e atualizações,** expanda **atualizações** > **do Visual Studio Marketplace,** escolha **funções azure e ferramentas de emprego web** e selecione **Update**.

    ![Atualizar a versão 'Ferramentas'](./media/functions-develop-vs/functions-vstools-update-functions-tools.png)   

1. Depois de ser descarregada a atualização das ferramentas, feche o Visual Studio para acionar a atualização das ferramentas utilizando o instalador VSIX.

1. No instalador, escolha **OK** para iniciar e, em seguida, **Modificar** para atualizar as ferramentas. 

1. Depois de a atualização estar completa, escolha **Fechar** e reiniciar o Estúdio Visual.

> [!NOTE]  
> No Visual Studio 2019 e mais tarde, a extensão de ferramentas Azure Functions é atualizada como parte do Visual Studio.  

## <a name="create-an-azure-functions-project"></a>Criar um projeto das Funções do Azure

[!INCLUDE [Create a project using the Azure Functions](../../includes/functions-vstools-create.md)]

O modelo de projeto cria um `Microsoft.NET.Sdk.Functions` projeto C#, instala o pacote NuGet e define a estrutura-alvo. O novo projeto tem os seguintes ficheiros:

* **host.json**: Permite-lhe configurar o anfitrião funções. Estas definições aplicam-se tanto quando se funciona localmente como em Azure. Para mais informações, consulte [host.json reference](functions-host-json.md).

* **local.settings.json**: Mantém as definições utilizadas durante as funções de funcionamento localmente. Estas definições não são usadas quando se corre em Azure. Para mais informações, consulte o ficheiro de [definições locais](#local-settings-file).

    >[!IMPORTANT]
    >Como o ficheiro local.settings.json pode conter segredos, deve excluí-lo do controlo de fontes do seu projeto. A definição **de Copy to Output Diretório** para este ficheiro deve ser sempre copiar se for mais **recente**. 

Para mais informações, consulte o projeto da biblioteca de [classes Functions.](functions-dotnet-class-library.md#functions-class-library-project)

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

As definições em locais.configurações.json não são carregadas automaticamente quando publica o projeto. Para se certificar de que estas definições também existem na sua aplicação de funções no Azure, deve carregá-las depois de publicar o seu projeto. Para saber mais, consulte as definições da [aplicação Function](#function-app-settings).

Os **valores em ConnectionStrings** nunca são publicados.

Os valores de definições da aplicação de função também podem ser lidos no seu código como variáveis ambientais. Para mais informações, consulte [variáveis ambientais.](functions-dotnet-class-library.md#environment-variables)

## <a name="configure-the-project-for-local-development"></a>Configure o projeto para o desenvolvimento local

O tempo de funcionamento das Funções utiliza uma conta de Armazenamento Azure internamente. Para todos os tipos de gatilho sem acordo, que não sejam HTTP e webhooks, deve definir a chave **Values.AzureWebJobsStorage** para uma cadeia de ligação de conta De armazenamento de armazenamento de Azure válida. A sua aplicação de funções também pode utilizar o [emulador](../storage/common/storage-use-emulator.md) de armazenamento Azure para a definição de ligação **AzureWebJobsStorage** que é exigida pelo projeto. Para utilizar o emulador, delineie o `UseDevelopmentStorage=true`valor do **AzureWebJobsStorage** para . Mude esta definição para uma cadeia de ligação à conta de armazenamento real antes da implantação.

Para definir o fio de ligação da conta de armazenamento:

1. No Estúdio Visual, abra o **Cloud Explorer,** expanda **a conta** > de armazenamento a sua conta de**armazenamento,** e depois no separador **Properties** copia o valor de corda de **ligação primária.**

2. No seu projeto, abra o ficheiro local.settings.json e delineie o valor da chave **AzureWebJobsStorage** para a cadeia de ligação que copiou.

3. Repita o passo anterior para adicionar chaves únicas à matriz **Valores** para quaisquer outras ligações exigidas pelas suas funções. 

## <a name="add-a-function-to-your-project"></a>Adicione uma função ao seu projeto

Nas funções da biblioteca da classe C#, as encadernações utilizadas pela função são definidas aplicando atributos no código. Quando cria os gatilhos de função a partir dos modelos fornecidos, os atributos do gatilho são aplicados para si. 

1. No **Explorador de Soluções**, clique com o botão direito do rato no nó do projeto e selecione **Adicionar** > **Novo item**. Selecione **Função Azure,** escreva um **nome** para a classe e clique em **Adicionar**.

2. Escolha o gatilho, detete as propriedades de encadernação e clique em **Criar**. O exemplo seguinte mostra as definições ao criar uma função ativada pelo armazenamento da fila. 

    ![Criar uma função desencadeada pela fila](./media/functions-develop-vs/functions-vstools-create-queuetrigger.png)

    Este exemplo do gatilho usa uma cadeia de ligação com uma chave chamada **QueueStorage**. Esta definição de cadeia de ligação deve ser definida no [ficheiro local.settings.json](functions-run-local.md#local-settings-file).

3. Examine a classe recém-adicionada. Você vê um método de **execução** estático, que é atribuído com o atributo **FunName.** Este atributo indica que o método é o ponto de entrada para a função.

    Por exemplo, a seguinte classe C# representa uma função básica de armazenamento de fila desencadeada:

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

    É aplicado um atributo específico de encadernação a cada parâmetro de ligação fornecido ao método do ponto de entrada. O atributo toma a informação vinculativa como parâmetros. No exemplo anterior, o primeiro parâmetro tem um atributo **QueueTrigger** aplicado, indicando a função desencadeada pela fila. O nome da fila e o nome de definição de cordas de ligação são passados como parâmetros para o atributo **QueueTrigger.** Para mais informações, consulte as [encadernações](functions-bindings-storage-queue-trigger.md)de armazenamento da Fila Azure para funções Azure .

Pode utilizar o procedimento acima para adicionar mais funções ao seu projeto de aplicação de funções. Cada função no projeto pode ter um gatilho diferente, mas uma função deve ter exatamente um gatilho. Para mais informações, consulte as [Funções Azure despoletou e encaderna conceitos.](functions-triggers-bindings.md)

## <a name="add-bindings"></a>Adicionar enlaces

Tal como acontece com os gatilhos, as ligações de entrada e de saída são adicionadas à sua função como atributos vinculativos. Adicione encadernações a uma função da seguinte forma:

1. Certifique-se de [configurar o projeto para o desenvolvimento local.](#configure-the-project-for-local-development)

2. Adicione o pacote de extensão NuGet apropriado para a ligação específica. Para mais informações, consulte [o desenvolvimento local C# utilizando](./functions-bindings-register.md#local-csharp) o Visual Studio no artigo Triggers and Bindings. Os requisitos específicos do pacote NuGet são encontrados no artigo de referência para a encadernação. Por exemplo, encontre os requisitos do pacote para o gatilho dos Centros de Eventos no artigo de referência vinculativo do [Event Hubs](functions-bindings-event-hubs.md).

3. Se existirem definições de aplicações que a encadernação necessita, adicione-as à coleção **Valores** no [ficheiro de definição local](functions-run-local.md#local-settings-file). Estes valores são utilizados quando a função funciona localmente. Quando a função funciona na aplicação de funções em Azure, as definições da aplicação de [função](#function-app-settings) são utilizadas.

4. Adicione o atributo de ligação adequado à assinatura do método. No exemplo seguinte, uma mensagem de fila despoleta a função, e a ligação de saída cria uma nova mensagem de fila com o mesmo texto numa fila diferente.

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
   A ligação ao armazenamento da `AzureWebJobsStorage` fila é obtida a partir da definição. Para mais informações, consulte o artigo de referência para a ligação específica. 

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="testing-functions"></a>Testar funções

As Ferramentas de Núcleo das Funções do Azure permitem-lhe executar o projeto de funções do Azure no seu computador de programação local. Deverá instalar essas ferramentas da primeira vez que iniciar uma função do Visual Studio.

Para testar a sua função, prima F5. Se solicitado, aceite o pedido do Visual Studio para transferir e instalar as ferramentas de Núcleo das Funções do Azure (CLI). Também poderá ativar a exceção da firewall para que todas as ferramentas possam aceitar os pedidos de HTTP.

Com o projeto em execução, pode testar o seu código como testaria a função implementada. Para mais informações, consulte [Estratégias para testar o seu código em Funções Azure](functions-test-a-function.md). Quando se corre em modo dedepura, os breakpoints são atingidos no Estúdio Visual como esperado. 

<!---
For an example of how to test a queue triggered function, see the [queue triggered function quickstart tutorial](functions-create-storage-queue-triggered-function.md#test-the-function).  
-->

Para saber mais sobre a utilização das Ferramentas Nucleares das Funções Azure, consulte as [funções de Código e teste azure localmente](functions-run-local.md).

## <a name="publish-to-azure"></a>Publicar no Azure

Ao publicar no Visual Studio, um dos dois métodos de implementação é usado:

* [Implementação da Web](functions-deployment-technologies.md#web-deploy-msdeploy): pacotes e implementa aplicações do Windows para qualquer servidor IIS.
* [Implantação zip com o run-from-package ativado](functions-deployment-technologies.md#zip-deploy): recomendado para implementações de funções Azure.

Utilize os seguintes passos para publicar o seu projeto numa aplicação de função no Azure.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="function-app-settings"></a>Definições da Aplicação de funções

Quaisquer configurações adicionadas nas configurações locais.definições.json também devem ser adicionadas à aplicação de funções em Azure. Estas definições não são carregadas automaticamente quando publica o projeto.

A forma mais fácil de fazer o upload das definições necessárias para a sua aplicação de funções no Azure é utilizar as Definições de **Aplicação de Gestão...** link que é apresentado depois de publicar com sucesso o seu projeto.

![](./media/functions-develop-vs/functions-vstools-app-settings.png)

Isto exibe o diálogo de Definições de **Aplicação** para a aplicação de funções, onde pode adicionar novas definições de aplicação ou modificar as existentes.

![](./media/functions-develop-vs/functions-vstools-app-settings2.png)

**Local** representa um valor de definição no ficheiro local.settings.json, e **Remote** é a configuração atual na aplicação de função em Azure.  Escolha **adicionar definição** para criar uma nova definição de aplicação. Utilize o **valor de inserção do** link Local para copiar um valor de definição para o campo **Remoto.** As alterações pendentes são escritas no ficheiro de definições locais e na aplicação de funções quando selecionar **OK**.

> [!NOTE]
> Por predefinição, o ficheiro local.settings.json não é verificado no controlo de origem. Isto significa que quando se clona um projeto de Funções locais a partir do controlo de fontes, o projeto não tem um ficheiro local.settings.json. Neste caso, é necessário criar manualmente o ficheiro local.settings.json na raiz do projeto para que o diálogo de Definições de **Aplicação** funcione como esperado. 

Também pode gerir as definições de aplicação de uma destas outras formas:

* [Utilizando o portal Azure.](functions-how-to-use-azure-function-app-settings.md#settings)
* [Utilizando `--publish-local-settings` a opção de publicação nas Ferramentas Core funções do Azure](functions-run-local.md#publish).
* [Utilização do Azure CLI](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set).

## <a name="monitoring-functions"></a>Funções de monitorização

A forma recomendada de monitorizar a execução das suas funções é integrando a sua aplicação de funções com insights de aplicação Do Azure. Quando cria uma aplicação de função no portal Azure, esta integração é feita por padrão. No entanto, quando cria a sua aplicação de funções durante a publicação do Visual Studio, a integração na sua aplicação de funções no Azure não está feita.

Para ativar insights de aplicação para a sua aplicação de funções:

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

Para saber mais, consulte as [Funções Monitor Azure](functions-monitoring.md).

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as Ferramentas Nucleares das Funções Azure, consulte as [funções de Código e teste azure localmente](functions-run-local.md).

Para saber mais sobre o desenvolvimento de funções como bibliotecas de classes .NET, consulte a [referência do desenvolvedor funções Azure C#](functions-dotnet-class-library.md). Este artigo também se liga a exemplos de como usar atributos para declarar os vários tipos de encadernações suportadas pelas Funções Azure.    

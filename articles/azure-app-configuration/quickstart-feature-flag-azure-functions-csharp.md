---
title: Quickstart para adicionar bandeiras de recurso às Funções Azure Microsoft Docs
description: Neste arranque rápido, utilize funções Azure com bandeiras de recurso da Configuração da Aplicação Azure e teste a função localmente.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.date: 8/26/2020
ms.author: alkemper
ms.openlocfilehash: d1dc843ff676429f202c0b9077057d067294f738
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92076169"
---
# <a name="quickstart-add-feature-flags-to-an-azure-functions-app"></a>Quickstart: Adicione bandeiras de recurso a uma aplicação Azure Functions

Neste arranque rápido, cria-se uma implementação da gestão de funcionalidades numa aplicação Azure Functions utilizando a Configuração de Aplicações Azure. Utilizará o serviço de Configuração de Aplicações para armazenar centralmente todas as suas bandeiras de funcionalidades e controlar os seus estados. 

As bibliotecas de Gestão de Recursos .NET alargam o quadro com suporte de bandeira de recurso. Estas bibliotecas são construídas em cima do sistema de configuração .NET. Integram-se com a Configuração de Aplicações através do seu fornecedor de configuração .NET.

## <a name="prerequisites"></a>Pré-requisitos

- Azure subscrição - [crie uma gratuitamente](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) com a carga de trabalho **de desenvolvimento Azure.**
- [Ferramentas Azure Functions](../azure-functions/functions-develop-vs.md#check-your-tools-version)

## <a name="create-an-app-configuration-store"></a>Criar uma loja de configuração de aplicativos

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Selecione **Feature Manager**  >  **+Adicione** para adicionar uma bandeira de recurso chamada `Beta` .

    > [!div class="mx-imgBorder"]
    > ![Ativar a bandeira de recurso chamada Beta](media/add-beta-feature-flag.png)

    Parta `label` e `Description` por enquanto.

8. **Selecione Aplicar** para guardar a nova bandeira de funcionalidade.

## <a name="create-a-functions-app"></a>Criar uma aplicação De Funções

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

## <a name="connect-to-an-app-configuration-store"></a>Conecte-se a uma loja de configuração de aplicativos

1. Clique com o botão direito no seu projeto e **selecione Gerir Pacotes NuGet**. No separador Procurar, procure e adicione os **seguintes** pacotes NuGet ao seu projeto. Verifique `Microsoft.Extensions.DependencyInjection` se está na mais recente construção estável. 

    ```
    Microsoft.Extensions.DependencyInjection
    Microsoft.Extensions.Configuration
    Microsoft.FeatureManagement
    ```


1. Abra *Function1.cs*e adicione os espaços de nome destes pacotes.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.FeatureManagement;
    using Microsoft.Extensions.DependencyInjection;
    ```

1. Adicione o `Function1` construtor estático abaixo para arrancar o fornecedor de configuração de aplicação Azure. Em seguida, adicione dois `static` membros, um campo nomeado `ServiceProvider` para criar um singleton instância de , e uma propriedade `ServiceProvider` abaixo nomeado para criar um `Function1` singleton instância de `FeatureManager` `IFeatureManager` . Em seguida, ligue-se à Configuração da Aplicação `Function1` através da chamada `AddAzureAppConfiguration()` . Este processo irá carregar a configuração no arranque da aplicação. A mesma instância de configuração será usada para todas as chamadas de Funções mais tarde. 

    ```csharp
        // Implements IDisposable, cached for life time of function
        private static ServiceProvider ServiceProvider; 

        static Function1()
        {
            IConfigurationRoot configuration = new ConfigurationBuilder()
                .AddAzureAppConfiguration(options =>
                {
                    options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                           .UseFeatureFlags();
                }).Build();

            var services = new ServiceCollection();                                                                             
            services.AddSingleton<IConfiguration>(configuration).AddFeatureManagement();

            ServiceProvider = services.BuildServiceProvider(); 
        }

        private static IFeatureManager FeatureManager => ServiceProvider.GetRequiredService<IFeatureManager>();
    ```

1. Atualize o `Run` método para alterar o valor da mensagem apresentada dependendo do estado da bandeira de recurso.

    ```csharp
        [FunctionName("Function1")]
        public static async Task<IActionResult> Run(
                [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
                ILogger log)
            {
                string message = await FeatureManager.IsEnabledAsync("Beta")
                     ? "The Feature Flag 'Beta' is turned ON"
                     : "The Feature Flag 'Beta' is turned OFF";
                
                return (ActionResult)new OkObjectResult(message); 
            }
    ```

## <a name="test-the-function-locally"></a>Testar localmente a função

1. Defina uma variável ambiental chamada **ConnectionString,** onde o valor é a chave de acesso que recuperou anteriormente na sua loja de Configuração de Aplicações em **Teclas de Acesso**. Se utilizar o pedido de comando do Windows, executar o seguinte comando e reiniciar a solicitação de comando para permitir que a alteração entre em vigor:

    ```cmd
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Se utilizar o Windows PowerShell, executar o seguinte comando:

    ```azurepowershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Se utilizar o macOS ou o Linux, executar o seguinte comando:

    ```bash
        export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

1. Prima F5 para testar a sua função. Se solicitado, aceite o pedido do Visual Studio para descarregar e instalar **ferramentas Azure Functions Core (CLI).** Também pode ser necessário ativar uma exceção de firewall para que as ferramentas possam lidar com pedidos HTTP.

1. Copie o URL da sua função na saída do tempo de execução das funções do Azure.

    ![Função quickstart depurando em VS](./media/quickstarts/function-visual-studio-debugging.png)

1. Cole o URL do pedido HTTP na barra de endereço do browser. A imagem a seguir mostra a resposta indicando que a bandeira de recurso `Beta` está desativada. 

    ![Sinalização de função quickstart desativada](./media/quickstarts/functions-launch-ff-disabled.png)

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Todos os recursos**e selecione a instância da loja de Configuração de Aplicações que criou.

1. Selecione **'Feature Manager'** e altere o estado da tecla **Beta** para **On**.

1. Volte ao seu comando e cancele o processo de funcionamento premindo `Ctrl-C` .  Reinicie a sua aplicação premindo F5. 

1. Copie o URL da sua função a partir da saída de tempo de funcionamento do Azure Functions utilizando o mesmo processo que no Passo 3. Cole o URL do pedido HTTP na barra de endereço do browser. A resposta do navegador deve ter sido alterada para indicar que a bandeira da funcionalidade `Beta` está ligada, como mostra a imagem abaixo.
 
    ![Bandeira de função quickstart ativada](./media/quickstarts/functions-launch-ff-enabled.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou uma bandeira de recurso e utilizou-a com uma aplicação Azure Functions através do provedor de Configuração de [Aplicações.](/dotnet/api/Microsoft.Extensions.Configuration.AzureAppConfiguration)

- Saiba mais sobre [gestão de recursos.](./concept-feature-management.md)
- [Gerir bandeiras de características](./manage-feature-flags.md).
- [Utilize uma configuração dinâmica numa aplicação Azure Functions](./enable-dynamic-configuration-azure-functions-csharp.md)
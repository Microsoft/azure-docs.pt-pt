---
title: Quickstart para adicionar bandeiras de recurso às funções Azure | Microsoft Docs
description: Neste arranque rápido, utilize funções Azure com bandeiras de recurso da Configuração da Aplicação Azure e teste a função localmente.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.date: 8/26/2020
ms.author: alkemper
ms.openlocfilehash: 96efc0ea6300e482ddeeda8fa177847f02b7e126
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98724259"
---
# <a name="quickstart-add-feature-flags-to-an-azure-functions-app"></a>Quickstart: Adicione bandeiras de recurso a uma aplicação Azure Functions

Neste arranque rápido, cria-se uma aplicação Azure Functions e utiliza bandeiras de funcionalidades na mesmo. Utiliza a gestão de funcionalidades da Azure App Configuration para armazenar centralmente todas as suas bandeiras de funcionalidades e controlar os seus estados.

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

Este projeto utilizará [a injeção de dependência em Funções Azure .NET](../azure-functions/functions-dotnet-dependency-injection.md). Adiciona a Configuração da Aplicação Azure como uma fonte de configuração extra onde as suas bandeiras de recurso estão armazenadas.

1. Clique com o botão direito no seu projeto e **selecione Gerir Pacotes NuGet**. No separador **Procurar,** procure e adicione os pacotes NuGet ao seu projeto.
   - [Microsoft.Extensions.Configuration. AzureAppConfiguration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureAppConfiguration/) versão 4.1.0 ou posterior
   - [Microsoft.FeatureManagement](https://www.nuget.org/packages/Microsoft.FeatureManagement/) versão 2.2.0 ou posterior
   - [Microsoft.Azure.Functions.Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/) versão 1.1.0 ou posterior 

2. Adicione um novo ficheiro, *Startup.cs,* com o seguinte código. Define uma classe chamada `Startup` que implementa a classe `FunctionsStartup` abstrata. Um atributo de montagem é utilizado para especificar o nome do tipo utilizado durante o arranque das Funções Azure.

    ```csharp
    using System;
    using Microsoft.Azure.Functions.Extensions.DependencyInjection;
    using Microsoft.Extensions.Configuration;
    using Microsoft.FeatureManagement;

    [assembly: FunctionsStartup(typeof(FunctionApp.Startup))]

    namespace FunctionApp
    {
        class Startup : FunctionsStartup
        {
            public override void ConfigureAppConfiguration(IFunctionsConfigurationBuilder builder)
            {
            }

            public override void Configure(IFunctionsHostBuilder builder)
            {
            }
        }
    }
    ```


3. Atualize o `ConfigureAppConfiguration` método e adicione o fornecedor de configuração de aplicações Azure como uma fonte de configuração extra, ligando para `AddAzureAppConfiguration()` . 

   O `UseFeatureFlags()` método diz ao fornecedor para carregar bandeiras de características. Todas as bandeiras de recurso têm uma expiração padrão de cache de 30 segundos antes de verificar novamente para alterações. O intervalo de validade pode ser atualizado definindo a `FeatureFlagsOptions.CacheExpirationInterval` propriedade passada para o `UseFeatureFlags` método. 

    ```csharp
    public override void ConfigureAppConfiguration(IFunctionsConfigurationBuilder builder)
    {
        builder.ConfigurationBuilder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                   .Select("_")
                   .UseFeatureFlags();
        });
    }
    ```
   > [!TIP]
   > Se não quiser que qualquer configuração que não seja bandeiras de recurso seja carregada na sua aplicação, pode ligar `Select("_")` para carregar apenas uma chave falsa "_". Por predefinição, todos os valores-chave de configuração na sua loja de Configuração de Aplicações serão carregados se nenhum `Select` método for chamado.

4. Atualize o `Configure` método para disponibilizar serviços de Configuração de Aplicações Azure e gestor de recursos através da injeção de dependência.

    ```csharp
    public override void Configure(IFunctionsHostBuilder builder)
    {
        builder.Services.AddAzureAppConfiguration();
        builder.Services.AddFeatureManagement();
    }
    ```

5. *Abra a Função1.cs*, e adicione os seguintes espaços de nome.

    ```csharp
    using System.Linq;
    using Microsoft.FeatureManagement;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

   Adicione um construtor utilizado para obter casos de `_featureManagerSnapshot` e através da injeção de `IConfigurationRefresherProvider` dependência. A partir do `IConfigurationRefresherProvider` , pode obter o caso de `IConfigurationRefresher` .

    ```csharp
    private readonly IFeatureManagerSnapshot _featureManagerSnapshot;
    private readonly IConfigurationRefresher _configurationRefresher;

    public Function1(IFeatureManagerSnapshot featureManagerSnapshot, IConfigurationRefresherProvider refresherProvider)
    {
        _featureManagerSnapshot = featureManagerSnapshot;
        _configurationRefresher = refresherProvider.Refreshers.First();
    }
    ```

6. Atualize o `Run` método para alterar o valor da mensagem apresentada dependendo do estado da bandeira de recurso.

   O `TryRefreshAsync` método é chamado no início da chamada Funções para refrescar as bandeiras de características. Será um não-op se a janela de tempo de validade do cache não for alcançada. Retire o `await` operador se preferir que as bandeiras de características sejam atualizadas sem bloquear a chamada de Funções atuais. Nesse caso, as chamadas posteriores de Funções receberão valor atualizado.

    ```csharp
    [FunctionName("Function1")]
    public async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
        ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        await _configurationRefresher.TryRefreshAsync();

        string message = await _featureManagerSnapshot.IsEnabledAsync("Beta")
                ? "The Feature Flag 'Beta' is turned ON"
                : "The Feature Flag 'Beta' is turned OFF";

        return (ActionResult)new OkObjectResult(message);
    }
    ```

## <a name="test-the-function-locally"></a>Testar localmente a função

1. Defina uma variável ambiental chamada **ConnectionString,** onde o valor é a cadeia de ligação que recuperou anteriormente na sua loja de Configuração de Aplicações em **Teclas de Acesso**. Se utilizar o pedido de comando do Windows, executar o seguinte comando e reiniciar a solicitação de comando para permitir que a alteração entre em vigor:

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

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Todos os recursos** e selecione a loja de Configuração de Aplicações que criou.

1. Selecione **O Gestor de Funcionalidades** e altere o estado da tecla **Beta** para **On**.

1. Refresque o navegador algumas vezes. Quando a bandeira de recurso em cache expirar após 30 segundos, a página deve ter sido alterada para indicar que a bandeira da função `Beta` está ligada, como mostra a imagem abaixo.
 
    ![Bandeira de função quickstart ativada](./media/quickstarts/functions-launch-ff-enabled.png)

> [!NOTE]
> O código de exemplo utilizado neste tutorial pode ser descarregado a partir do repo GitHub de [Configuração de Aplicação Azure](https://github.com/Azure/AppConfiguration/tree/master/examples/DotNetCore/AzureFunction).

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou uma bandeira de recurso e utilizou-a com uma aplicação Azure Functions através da biblioteca [Microsoft.FeatureManagement.](/dotnet/api/microsoft.featuremanagement)

- Saiba mais sobre [gestão de recursos](./concept-feature-management.md)
- [Gerir sinalizadores de funcionalidades](./manage-feature-flags.md)
- [Utilizar sinalizadores de funcionalidade condicional](./howto-feature-filters-aspnet-core.md)
- [Ativar o lançamento encenado de funcionalidades para públicos direcionados](./howto-targetingfilter-aspnet-core.md)
- [Utilize uma configuração dinâmica numa aplicação Azure Functions](./enable-dynamic-configuration-azure-functions-csharp.md)
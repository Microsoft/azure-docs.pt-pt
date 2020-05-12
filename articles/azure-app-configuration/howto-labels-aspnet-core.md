---
title: Utilizar configuração por ambiente
titleSuffix: Azure App Configuration
description: Utilize etiquetas para fornecer valores de configuração por ambiente.
ms.service: azure-app-configuration
author: lisaguthrie
ms.topic: conceptual
ms.date: 3/12/2020
ms.author: lcozzens
ms.openlocfilehash: 465ae86c5732c9dd54ade1b7096fa8415dfca513
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83118552"
---
# <a name="use-labels-to-enable-configurations-for-different-environments"></a>Utilize etiquetas para permitir configurações para diferentes ambientes

Muitas aplicações precisam de usar configurações diferentes para diferentes ambientes. Suponha que uma aplicação tenha um valor de configuração que define a cadeia de ligação a utilizar para a sua base de dados de back-end. Os desenvolvedores de aplicações usam uma base de dados diferente da utilizada na produção. A cadeia de ligação à base de dados que a aplicação utiliza deve alterar à medida que a aplicação passa do desenvolvimento para a produção.

Na Configuração de Aplicações Azure, pode utilizar etiquetas para definir *valores* diferentes para a mesma tecla. Por exemplo, pode definir uma única chave com diferentes valores para o desenvolvimento e produção. Pode especificar qual a etiqueta a carregar ao ligar à Configuração da Aplicação.

Para demonstrar esta funcionalidade, irá modificar a aplicação web criada em [Quickstart: Criar uma aplicação ASP.NET Core com configuração de aplicações Azure](./quickstart-aspnet-core-app.md) para utilizar diferentes configurações de configuração para desenvolvimento versus produção. Complete o início rápido antes de prosseguir.

## <a name="specify-a-label-when-adding-a-configuration-value"></a>Especifique um rótulo ao adicionar um valor de configuração

No portal Azure, vá ao Explorer de **Configuração** e encontre a tecla *TestApp:Settings:FontColor* que criou no arranque rápido. Selecione o menu de contexto e, em seguida, selecione **Adicionar Valor**.

> [!div class="mx-imgBorder"]
> ![Adicionar item do menu Valor](media/labels-add-value.png)

No ecrã **Add Value,** introduza um **valor** de **vermelho** e um **rótulo** de **desenvolvimento.** Deixe o **tipo de conteúdo** vazio. Selecione **Aplicar**.

## <a name="load-configuration-values-with-a-specified-label"></a>Valores de configuração de carga com uma etiqueta especificada

Por predefinição, a Configuração da Aplicação Azure apenas carrega valores de configuração sem etiqueta. Se definiu etiquetas para os seus valores de configuração, deverá especificar as etiquetas a utilizar ao ligar-se à Configuração da Aplicação.

Na secção anterior, criou um valor de configuração diferente para o ambiente de desenvolvimento. Usa a `HostingEnvironment.EnvironmentName` variável para determinar dinamicamente em que ambiente a aplicação funciona atualmente. Para saber mais, consulte [Use vários ambientes em ASP.NET Core](/aspnet/core/fundamentals/environments).

Valores de configuração de carga com a etiqueta correspondente ao ambiente atual, passando o nome do ambiente para o `Select` método:

```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options =>
                    options
                        .Connect(Environment.GetEnvironmentVariable("AppConfigConnectionString"))
                        // Load configuration values with no label
                        .Select(KeyFilter.Any, LabelFilter.Null)
                        // Override with any configuration values specific to current hosting env
                        .Select(KeyFilter.Any, hostingContext.HostingEnvironment.EnvironmentName)
                );
            })
            .UseStartup<Startup>());
```

> [!IMPORTANT]
> O snippet de código anterior carrega a cadeia de ligação de configuração da aplicação a partir de uma variável ambiental chamada `AppConfigConnectionString` . Certifique-se de que esta variável ambiental está corretamente definida.

O `Select` método é chamado duas vezes. Da primeira vez, carrega valores de configuração sem etiqueta. Em seguida, carrega valores de configuração com a etiqueta correspondente ao ambiente atual. Estes valores específicos do ambiente sobrepõem-se a quaisquer valores correspondentes sem rótulo. Não é preciso definir valores específicos do ambiente para cada chave. Se uma chave não tiver um valor com uma etiqueta correspondente ao ambiente atual, utiliza o valor sem etiqueta.

## <a name="test-in-different-environments"></a>Teste em diferentes ambientes

Abra o `launchSettings.json` ficheiro sob o `Properties` diretório. Encontre a `config` entrada abaixo `profiles` . Na `environmentVariables` secção, desloque a `ASPNETCORE_ENVIRONMENT` variável para `Production` .

Com os novos valores definidos, construa e execute a sua aplicação.

```dotnetcli
dotnet build
dotnet run
```

Use um navegador web para ir `http://localhost:5000` a . Vai notar que a cor da fonte é preta.

![Aplicação web em execução com configuração de produção](media/labels-website-prod.png)

Atualizar `launchSettings.json` para definir a `ASPNETCORE_ENVIRONMENT` variável para `Development` . Execute novamente `dotnet run`. 

Vai notar que a cor da fonte é agora vermelha. Isto porque a aplicação utiliza agora o valor `TestApp:Settings:FontColor` de que tem o `Development` rótulo. Todos os outros valores de configuração permanecem os mesmos que os seus valores de produção.

![Execução de aplicação web com configuração de desenvolvimento](media/labels-website-dev.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Configuração em ASP.NET Core](/aspnet/core/fundamentals/configuration/)

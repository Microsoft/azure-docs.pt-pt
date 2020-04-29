---
title: Utilizar configuração por ambiente
titleSuffix: Azure App Configuration
description: Utilize etiquetas para fornecer valores de configuração por ambiente
ms.service: azure-app-configuration
author: lisaguthrie
ms.topic: conceptual
ms.date: 3/12/2020
ms.author: lcozzens
ms.openlocfilehash: d1a3323154fc73b453e041a064cfcd36299b8a08
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80056806"
---
# <a name="use-labels-to-enable-different-configurations-for-different-environments"></a>Utilize etiquetas para permitir configurações diferentes para diferentes ambientes

Muitas aplicações precisam de usar configurações diferentes para diferentes ambientes. Suponha que uma aplicação tenha um valor de configuração que define a cadeia de ligação a utilizar para a sua base de dados de back-end. Os desenvolvedores da aplicação usam uma base de dados diferente da utilizada na produção. A cadeia de ligação à base de dados utilizada pela aplicação deve ser alterada à medida que a aplicação passa do desenvolvimento para a produção.

Na Configuração de Aplicações Azure, pode utilizar etiquetas para definir *valores* diferentes para a mesma tecla. Por exemplo, pode definir uma única chave com diferentes valores para *Desenvolvimento* e *Produção.* Pode especificar qual a etiqueta(s) a carregar ao ligar à Configuração da Aplicação.

Para demonstrar esta funcionalidade, vamos modificar a aplicação web criada em [Quickstart: Criar uma aplicação ASP.NET Core com configuração de aplicação Azure](./quickstart-aspnet-core-app.md) para utilizar diferentes configurações de configuração para desenvolvimento vs. produção. Por favor, complete o início rápido antes de prosseguir.

## <a name="specify-a-label-when-adding-a-configuration-value"></a>Especifique um rótulo ao adicionar um valor de configuração

No portal Azure, entre no Explorer de **Configuração** e localize a tecla *TestApp:Settings:FontColor* que criou no arranque rápido. Selecione o menu de contexto e, em seguida, clique em **Adicionar Valor**.

> [!div class="mx-imgBorder"]
> ![Adicionar item do menu Valor](media/labels-add-value.png)

No ecrã **Add Value,** introduza um **valor** de **vermelho** e um **rótulo** de **desenvolvimento.** Deixe o **tipo de conteúdo** vazio. Selecione **Aplicar**.

## <a name="loading-configuration-values-with-a-specified-label"></a>Carregar valores de configuração com uma etiqueta especificada

Por predefinição, a Configuração da Aplicação Azure apenas carrega valores de configuração sem etiqueta. Se definiu etiquetas para os seus valores de configuração, deverá especificar a(s) etiqueta(s) a utilizar ao ligar-se à Configuração da Aplicação.

Na última secção, criou um valor de configuração diferente para o ambiente *de Desenvolvimento.* Usa a `HostingEnvironment.EnvironmentName` variável para determinar dinamicamente em que ambiente a aplicação está atualmente a funcionar. Para saber mais, consulte [Use vários ambientes em ASP.NET Core](/aspnet/core/fundamentals/environments).

Valores de configuração de carga com a etiqueta `Select` correspondente ao ambiente atual, passando o nome do ambiente para o método:

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
> O snippet de código acima carrega a cadeia `AppConfigConnectionString`de ligação de configuração da app a partir de uma variável ambiental chamada . Certifique-se de que esta variável ambiental está corretamente definida.

O `Select` método é chamado duas vezes. Da primeira vez, carrega valores de configuração sem etiqueta. Em seguida, carrega valores de configuração com a etiqueta correspondente ao ambiente atual. Estes valores específicos do ambiente sobrepõem-se a quaisquer valores correspondentes sem rótulo. Não é necessário definir valores específicos do ambiente para cada chave. Se uma chave não tiver um valor com uma etiqueta correspondente ao ambiente atual, então o valor sem etiqueta é utilizado.

## <a name="testing-in-different-environments"></a>Testes em diferentes ambientes

Para testar os diferentes `launchSettings.json` valores `Properties` de configuração, abra o ficheiro sob o diretório. Localize `config` a `profiles`entrada abaixo . . Na `environmentVariables` secção, desloque a `ASPNETCORE_ENVIRONMENT` variável para `Production`.

Com os novos valores definidos, construa e execute a sua aplicação.

```dotnetcli
dotnet build
dotnet run
```

Use um navegador web `http://localhost:5000`para navegar para . Vai notar que a cor da fonte é preta.

![Aplicação web em execução com configuração de produção](media/labels-website-prod.png)

Agora `launchSettings.json` atualize `ASPNETCORE_ENVIRONMENT` para `Development`definir a variável para . Execute novamente `dotnet run`. Vai notar que a cor da fonte é agora vermelha. Isto porque a aplicação utiliza `TestApp:Settings:FontColor` agora `Development` o valor de que tem o rótulo. Todos os outros valores de configuração permanecem os mesmos que os seus valores de produção.

![Execução de aplicação web com configuração de desenvolvimento](media/labels-website-dev.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Configuração em ASP.NET Core](/aspnet/core/fundamentals/configuration/)

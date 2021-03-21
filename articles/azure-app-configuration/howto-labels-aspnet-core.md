---
title: Use a configuração por ambiente
titleSuffix: Azure App Configuration
description: Utilize etiquetas para fornecer valores de configuração por ambiente.
ms.service: azure-app-configuration
author: AlexandraKemperMS
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 3/12/2020
ms.author: alkemper
ms.openlocfilehash: 84286df063994f3def15079cb9b190550d5bd977
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96929621"
---
# <a name="use-labels-to-enable-configurations-for-different-environments"></a>Utilize etiquetas para permitir configurações para diferentes ambientes

Muitas aplicações precisam de usar configurações diferentes para diferentes ambientes. Suponha que uma aplicação tem um valor de configuração que define o fio de ligação a utilizar para a sua base de dados back-end. Os desenvolvedores de aplicações usam uma base de dados diferente da utilizada na produção. A cadeia de ligação da base de dados que a aplicação utiliza deve ser alterada à medida que a aplicação passa do desenvolvimento para a produção.

Na Configuração da Aplicação Azure, pode utilizar etiquetas para definir *valores* diferentes para a mesma tecla. Por exemplo, pode definir uma única chave com diferentes valores para o desenvolvimento e produção. Pode especificar qual a etiqueta a carregar ao ligar à Configuração da Aplicação.

Para demonstrar esta funcionalidade, irá modificar a aplicação web criada no [Quickstart: Criar uma aplicação Core ASP.NET com configuração de aplicação Azure](./quickstart-aspnet-core-app.md) para utilizar diferentes configurações de configuração para desenvolvimento versus produção. Complete o quickstart antes de prosseguir.

## <a name="specify-a-label-when-adding-a-configuration-value"></a>Especifique um rótulo ao adicionar um valor de configuração

No portal Azure, vá ao **Explorador de Configuração** e encontre a tecla *TestApp:FontColor* que criou no arranque rápido. Selecione o menu de contexto e, em seguida, **selecione Add Value**.

> [!div class="mx-imgBorder"]
> ![Adicionar item do menu de valor](media/labels-add-value.png)

No ecrã **Add Value,** insira um **valor** **vermelho** e um **rótulo** de **desenvolvimento.** Deixe **o tipo de conteúdo** vazio. Selecione **Aplicar**.

## <a name="load-configuration-values-with-a-specified-label"></a>Valores de configuração de carga com uma etiqueta especificada

Por predefinição, a Configuração da Aplicação Azure apenas carrega valores de configuração sem etiqueta. Se definiu as etiquetas para os seus valores de configuração, deverá especificar as etiquetas a utilizar ao ligar-se à Configuração da Aplicação.

Na secção anterior, criou um valor de configuração diferente para o ambiente de desenvolvimento. Utiliza a `HostingEnvironment.EnvironmentName` variável para determinar dinamicamente em que ambiente a aplicação funciona atualmente. Para saber mais, consulte [Utilizar vários ambientes no ASP.NET Core](/aspnet/core/fundamentals/environments).

Valores de configuração de carga com o rótulo correspondente ao ambiente atual, passando o nome do ambiente para o `Select` método:

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
> O corte de código anterior carrega a cadeia de ligação de configuração da aplicação a partir de uma variável ambiental chamada `AppConfigConnectionString` . Certifique-se de que esta variável ambiental está corretamente definida.

O `Select` método chama-se duas vezes. Da primeira vez, carrega valores de configuração sem etiqueta. Em seguida, carrega valores de configuração com a etiqueta correspondente ao ambiente atual. Estes valores específicos do ambiente sobrepõem-se a quaisquer valores correspondentes sem etiqueta. Não é preciso definir valores específicos do ambiente para cada chave. Se uma chave não tiver um valor com um rótulo correspondente ao ambiente atual, utiliza o valor sem etiqueta.

## <a name="test-in-different-environments"></a>Teste em diferentes ambientes

Abra o `launchSettings.json` ficheiro sob o `Properties` diretório. Encontre a `config` entrada em `profiles` . Na `environmentVariables` secção, desacione `ASPNETCORE_ENVIRONMENT` a variável para `Production` .

Com os novos valores definidos, construa e execute a sua aplicação.

```dotnetcli
dotnet build
dotnet run
```

Use um navegador web para ir a `http://localhost:5000` . Vai notar que a cor da fonte é preta.

![Aplicação web em execução com configuração de produção](media/labels-website-prod.png)

Atualizar `launchSettings.json` para definir a `ASPNETCORE_ENVIRONMENT` variável para `Development` . Execute novamente `dotnet run`. 

Vai notar que a cor da fonte está agora vermelha. Isto porque a aplicação agora usa o valor do `TestApp:Settings:FontColor` que tem o `Development` rótulo. Todos os outros valores de configuração permanecem os mesmos que os seus valores de produção.

![Aplicação web em execução com configuração de desenvolvimento](media/labels-website-dev.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Configuração no núcleo ASP.NET](/aspnet/core/fundamentals/configuration/)

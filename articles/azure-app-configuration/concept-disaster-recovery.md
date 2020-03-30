---
title: Resiliência da configuração da app Azure e recuperação de desastres
description: Lean como implementar resiliência e recuperação de desastres com configuração de aplicações azure.
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 96ef09ac081aa328014217592a7fcd3ed6314c0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523769"
---
# <a name="resiliency-and-disaster-recovery"></a>Resiliência e recuperação após desastre

Atualmente, a Configuração de Aplicações Azure é um serviço regional. Cada loja de configuração é criada numa determinada região do Azure. Uma paralisação em toda a região afeta todas as lojas daquela região. A Configuração de Aplicações não oferece falhas automáticas a outra região. Este artigo fornece orientações gerais sobre como pode usar várias lojas de configuração em todas as regiões de Azure para aumentar a geo-resiliência da sua aplicação.

## <a name="high-availability-architecture"></a>Arquitetura de alta disponibilidade

Para realizar o despedimento transfronteiriço, precisa de criar várias lojas de Configuração de Aplicações em diferentes regiões. Com esta configuração, a sua aplicação tem pelo menos uma loja de configuração adicional para recorrer se a loja primária ficar inacessível. O diagrama que se segue ilustra a topologia entre a sua aplicação e as suas lojas de configuração primária e secundária:

![Lojas georedundantes](./media/geo-redundant-app-configuration-stores.png)

A sua aplicação carrega a sua configuração a partir das lojas primárias e secundárias em paralelo. Ao fazê-lo aumenta a possibilidade de obter com sucesso os dados de configuração. É responsável por manter os dados em ambas as lojas sincronizados. As seguintes secções explicam como pode construir georesiliência na sua aplicação.

## <a name="failover-between-configuration-stores"></a>Falha entre lojas de configuração

Tecnicamente, a sua candidatura não está a executar uma falha. Está a tentar recuperar simultaneamente o mesmo conjunto de dados de configuração de duas lojas de Configuração de Aplicações. Disponha o seu código de modo a que carregue primeiro da loja secundária e depois da loja primária. Esta abordagem garante que os dados de configuração na loja primária têm precedência sempre que estão disponíveis. O seguinte código de corte mostra como pode implementar este arranjo em .NET Core:

#### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            var settings = config.Build();
            config.AddAzureAppConfiguration(settings["ConnectionString_SecondaryStore"], optional: true)
                  .AddAzureAppConfiguration(settings["ConnectionString_PrimaryStore"], optional: true);
        })
        .UseStartup<Startup>();
    
```

#### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(settings["ConnectionString_SecondaryStore"], optional: true)
                    .AddAzureAppConfiguration(settings["ConnectionString_PrimaryStore"], optional: true);
            })
            .UseStartup<Startup>());
```
---

Reparem `optional` que o `AddAzureAppConfiguration` parâmetro passou para a função. Quando definido `true`para , este parâmetro evita que a aplicação não continue se a função não conseguir carregar os dados de configuração.

## <a name="synchronization-between-configuration-stores"></a>Sincronização entre lojas de configuração

É importante que as suas lojas de configuração geo-redundantes tenham todos o mesmo conjunto de dados. Pode utilizar a função **Export** in App Configuration para copiar dados da loja primária para o secundário a pedido. Esta função está disponível através do portal Azure e do CLI.

A partir do portal Azure, pode empurrar uma alteração para outra loja de configuração seguindo estes passos.

1. Vá ao separador **Import/Export** e selecione **Export** > **App Configuration** > **Target** > **Selecione um recurso**.

1. Na nova lâmina que abre, especifique a subscrição, o grupo de recursos e o nome de recursos da sua loja secundária, em seguida, selecione **Apply**.

1. O UI é atualizado para que possa escolher quais os dados de configuração que pretende exportar para a sua loja secundária. Pode deixar o valor de tempo predefinido como está e definir tanto **a etiqueta da etiqueta como** a etiqueta **para** o mesmo valor. Selecione **Aplicar**.

1. Repita os passos anteriores para todas as alterações de configuração.

Para automatizar este processo de exportação, utilize o Azure CLI. O seguinte comando mostra como exportar uma única mudança de configuração da loja primária para a segunda:

```azurecli
    az appconfig kv export --destination appconfig --name {PrimaryStore} --label {Label} --dest-name {SecondaryStore} --dest-label {Label}
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a aumentar a sua aplicação para alcançar a geo-resiliência durante o tempo de execução para a Configuração de Aplicações. Também pode incorporar dados de configuração a partir da Configuração de Aplicações no tempo de construção ou implementação. Para mais informações, consulte [Integrar com um pipeline CI/CD](./integrate-ci-cd-pipeline.md).

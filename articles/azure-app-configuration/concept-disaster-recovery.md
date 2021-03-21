---
title: Resiliência da configuração da app Azure e recuperação de desastres
description: Incline-se como implementar resiliência e recuperação de desastres com a Configuração da App Azure.
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 07/09/2020
ms.openlocfilehash: 7910e2092259081aade799fc662052e5a1375e25
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96930488"
---
# <a name="resiliency-and-disaster-recovery"></a>Resiliência e recuperação após desastre

Atualmente, a Azure App Configuration é um serviço regional. Cada loja de configuração é criada numa região particular de Azure. Uma paralisação por toda a região afeta todas as lojas daquela região. A Configuração de Aplicações não oferece falhas automáticas para outra região. Este artigo fornece orientações gerais sobre como você pode usar várias lojas de configuração em todas as regiões de Azure para aumentar a geo-resiliência da sua aplicação.

## <a name="high-availability-architecture"></a>Arquitetura de alta disponibilidade

Para realizar redundâncias entre regiões, é necessário criar várias lojas de Configuração de Aplicações em diferentes regiões. Com esta configuração, a sua aplicação tem pelo menos uma loja de configuração adicional para voltar a ser interdesecendo se a loja primária ficar inacessível. O diagrama a seguir ilustra a topologia entre a sua aplicação e as suas lojas de configuração primária e secundária:

![Lojas geo-redundantes](./media/geo-redundant-app-configuration-stores.png)

A sua aplicação carrega a sua configuração a partir das lojas primárias e secundárias em paralelo. Ao fazê-lo, aumenta a possibilidade de obter com sucesso os dados de configuração. É responsável por manter os dados em ambas as lojas sincronizados. As seguintes secções explicam como pode construir geo-resiliência na sua aplicação.

## <a name="failover-between-configuration-stores"></a>Falha entre lojas de configuração

Tecnicamente, a sua candidatura não está a executar um fracasso. Está a tentar recuperar o mesmo conjunto de dados de configuração de duas lojas de Configuração de Aplicações simultaneamente. Disponha o seu código de modo a que este seja carregado na loja secundária primeiro e depois na loja primária. Esta abordagem garante que os dados de configuração na loja primária têm precedência sempre que estão disponíveis. O seguinte corte de código mostra como pode implementar este arranjo em .NET Core:

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

Note o `optional` parâmetro passado para a `AddAzureAppConfiguration` função. Quando definido `true` para , este parâmetro impede que a aplicação não continue se a função não conseguir carregar os dados de configuração.

## <a name="synchronization-between-configuration-stores"></a>Sincronização entre lojas de configuração

É importante que as suas lojas de configuração geodu redundantes tenham todos o mesmo conjunto de dados. Há duas maneiras de o conseguir:

### <a name="backup-manually-using-the-export-function"></a>Cópia de segurança manualmente utilizando a função Exportação

Pode utilizar a função **Exportação** na Configuração de Aplicações para copiar dados da loja primária para o secundário a pedido. Esta função está disponível tanto através do portal Azure como do CLI.

A partir do portal Azure, pode empurrar uma mudança para outra loja de configuração seguindo estes passos.

1. Vá ao **separador Importação/Exportação** e selecione o target de configuração da aplicação **de**  >  **exportação**  >    >  **Selecione um recurso.**

1. Na nova lâmina que abre, especifique a subscrição, o grupo de recursos e o nome do recurso da sua loja secundária e, em seguida, selecione **Apply**.

1. A UI é atualizada para que possa escolher os dados de configuração que pretende exportar para a sua loja secundária. Pode deixar o valor de tempo padrão como está e definir tanto **a partir da etiqueta como do** **rótulo** para o mesmo valor. Selecione **Aplicar**. Repita isto para todas as etiquetas da sua loja primária.

1. Repita os passos anteriores sempre que a sua configuração for muda.

O processo de exportação também pode ser alcançado utilizando o Azure CLI. O seguinte comando mostra como exportar todas as configurações da loja primária para a secundária:

```azurecli
    az appconfig kv export --destination appconfig --name {PrimaryStore} --dest-name {SecondaryStore} --label * --preserve-labels -y
```

### <a name="backup-automatically-using-azure-functions"></a>Backup automaticamente utilizando funções Azure

O processo de backup pode ser automatizado utilizando funções Azure. Aproveita a integração com a Azure Event Grid na Configuração de Aplicações. Uma vez configurado, a Configuração de Aplicações publicará eventos na Grade de Eventos para quaisquer alterações feitas aos valores-chave numa loja de configuração. Assim, uma aplicação Azure Functions pode ouvir estes eventos e dados de backup em conformidade. Para mais detalhes, consulte o tutorial sobre [como fazer backup nas lojas de Configuração de aplicações automaticamente](./howto-backup-config-store.md).

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a aumentar a sua aplicação para alcançar a geo-resiliência durante o tempo de funcionamento para a Configuração da Aplicação. Também pode incorporar dados de configuração a partir da Configuração de Aplicações no tempo de construção ou implementação. Para obter mais informações, consulte [Integrar-se com um pipeline CI/CD](./integrate-ci-cd-pipeline.md).

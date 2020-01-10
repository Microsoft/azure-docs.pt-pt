---
title: Resiliência de configuração Azure App e recuperação de desastre
description: Saiba como implementar resiliência e recuperação de desastre com a configuração de Azure App.
author: yegu-ms
ms.author: yegu
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: f2f914ec993670b8ba7a596f873234afd9ffc8e8
ms.sourcegitcommit: 2c59a05cb3975bede8134bc23e27db5e1f4eaa45
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/05/2020
ms.locfileid: "75665060"
---
# <a name="resiliency-and-disaster-recovery"></a>Resiliência e recuperação após desastre

Atualmente, Azure App configuração é um serviço regional. Cada repositório de configuração é criado em uma determinada região do Azure. Uma interrupção em toda a região afeta todas as lojas nessa região. A configuração do aplicativo não oferece failover automático para outra região. Este artigo fornece diretrizes gerais sobre como você pode usar vários armazenamentos de configuração em regiões do Azure para aumentar a resiliência geográfica do seu aplicativo.

## <a name="high-availability-architecture"></a>Arquitetura de alta disponibilidade

Para perceber a redundância entre regiões, você precisa criar vários repositórios de configuração de aplicativo em regiões diferentes. Com essa configuração, seu aplicativo tem pelo menos um repositório de configuração adicional para fazer o fallback se o repositório primário se tornar inacessível. O diagrama a seguir ilustra a topologia entre seu aplicativo e seus armazenamentos de configuração primários e secundários:

![Armazenamentos com redundância geográfica](./media/geo-redundant-app-configuration-stores.png)

Seu aplicativo carrega sua configuração de armazenamentos primários e secundários em paralelo. Isso aumenta a chance de obter os dados de configuração com êxito. Você é responsável por manter os dados em ambos os repositórios sincronizados. As seções a seguir explicam como você pode criar resiliência geográfica em seu aplicativo.

## <a name="failover-between-configuration-stores"></a>Failover entre repositórios de configuração

Tecnicamente, seu aplicativo não está executando um failover. Ele está tentando recuperar o mesmo conjunto de dados de configuração de dois repositórios de configuração de aplicativo simultaneamente. Organize seu código para que ele carregue a partir do armazenamento secundário primeiro e, em seguida, o repositório primário. Essa abordagem garante que os dados de configuração no repositório primário tenham precedência sempre que estiverem disponíveis. O trecho de código a seguir mostra como você pode implementar essa disposição no CLI do .NET Core:

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
    }
```

Observe o parâmetro `optional` passado para a função `AddAzureAppConfiguration`. Quando definido como `true`, esse parâmetro impede que o aplicativo falhe se a função não puder carregar dados de configuração.

## <a name="synchronization-between-configuration-stores"></a>Sincronização entre repositórios de configuração

É importante que sua configuração com redundância geográfica armazene todos tenham o mesmo conjunto de dados. Você pode usar a função de **exportação** na configuração do aplicativo para copiar dados do repositório primário para o secundário sob demanda. Essa função está disponível por meio do portal do Azure e da CLI.

No portal do Azure, você pode enviar uma alteração para outro repositório de configuração seguindo estas etapas.

1. Vá para a guia **importação/exportação** e selecione **Exportar** > **configuração de aplicativo** > **destino** > **Selecione um recurso**.

2. Na nova folha que é aberta, especifique a assinatura, o grupo de recursos e o nome do recurso do armazenamento secundário e, em seguida, selecione **aplicar**.

3. A interface do usuário é atualizada para que você possa escolher quais dados de configuração deseja exportar para o armazenamento secundário. Você pode deixar o valor de hora padrão como está e definir ambos **de rótulo** e **para rotular** para o mesmo valor. Selecione **Aplicar**.

4. Repita as etapas anteriores para todas as alterações de configuração.

Para automatizar esse processo de exportação, use o CLI do Azure. O comando a seguir mostra como exportar uma única alteração de configuração do repositório primário para o secundário:

    az appconfig kv export --destination appconfig --name {PrimaryStore} --label {Label} --dest-name {SecondaryStore} --dest-label {Label}

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu a aumentar seu aplicativo para obter resiliência geográfica durante o tempo de execução para a configuração do aplicativo. Você também pode inserir dados de configuração da configuração do aplicativo no momento da compilação ou implantação. Para obter mais informações, consulte [integrar com um pipeline de CI/CD](./integrate-ci-cd-pipeline.md).


---
title: Configuração de aplicações após desastre e resiliência a recuperação do Azure | Documentos da Microsoft
description: Uma visão geral de como implementar a recuperação após desastre e resiliência com a configuração de aplicações do Azure.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.service: azure-app-configuration
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 05/29/2019
ms.author: yegu
ms.openlocfilehash: c05957cda16c96b841433483a90429aab2b4d22d
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706505"
---
# <a name="resiliency-and-disaster-recovery"></a>Resiliência e recuperação após desastre

Atualmente, a configuração de aplicações do Azure é um serviço regional. Cada arquivo de configuração é criado numa região do Azure específica. Uma interrupção de toda a região afeta todos os arquivos de nessa região. Configuração de aplicações não oferece a ativação pós-falha automática para outra região. Este artigo fornece orientações gerais sobre como pode usar vários arquivos de configuração entre regiões do Azure para aumentar a resiliência de georreplicação do seu aplicativo.

## <a name="high-availability-architecture"></a>Arquitetura de elevada disponibilidade

Para obter redundância entre regiões, terá de criar vários arquivos de configuração de aplicação em regiões diferentes. Com esta configuração, a aplicação tem do arquivo de configuração adicionais, pelo menos, um recorrer ao se o arquivo primário ficar inacessível. O diagrama seguinte ilustra a topologia entre seu aplicativo e seus arquivos de configuração primários e secundários:

![Lojas com redundância geográfica](./media/geo-redundant-app-configuration-stores.png)

A aplicação carrega a configuração das lojas primárias e secundárias em paralelo. Isso aumenta a possibilidade de obter com êxito os dados de configuração. É responsável por manter os dados em ambos os armazenamentos em sincronia. As secções seguintes explicam como criar geo-resiliência na sua aplicação.

## <a name="failover-between-configuration-stores"></a>Ativação pós-falha entre arquivos de configuração

Tecnicamente, a aplicação não está a executar uma ativação pós-falha. Esta é a tentar obter o mesmo conjunto de dados de configuração a partir de dois arquivos de configuração de aplicações em simultâneo. Organize seu código para que ele carrega primeiro na loja secundária e, em seguida, armazenar a principal. Esta abordagem garante que os dados de configuração no arquivo primário tem precedência, sempre que está disponível. O fragmento de código seguinte mostra como implementar essa organização na CLI do núcleo do .NET:

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

Observe que o `optional` parâmetro passado para o `AddAzureAppConfiguration` função. Quando definido como `true`, este parâmetro impede a aplicação de conseguir continuar se a função não é possível carregar dados de configuração.

## <a name="synchronization-between-configuration-stores"></a>Sincronização entre os arquivos de configuração

É importante que todos os seus arquivos de configuração com redundância geográfica têm o mesmo conjunto de dados. Pode utilizar o **exportar** função numa configuração de aplicação para copiar dados de arquivo primário para o secundário a pedido. Esta função está disponível através do portal do Azure e a CLI.

No portal do Azure, pode emitir uma alteração para outro arquivo de configuração ao seguir estes passos.

1. Vá para o **importação/exportação** separador e selecione **exportar** > **configuração de aplicações** > **destino**  >  **Selecionar um recurso**.

2. No novo painel apresentado, especifique a subscrição, o grupo de recursos e o nome de recurso do seu armazenamento secundário e, em seguida, selecione **aplicar**.

3. A interface do Usuário é atualizada para que pode escolher os dados de configuração que pretende exportar para o seu armazenamento secundário. Pode deixar o valor de tempo predefinido como está e definir ambos **da etiqueta** e **etiqueta** para o mesmo valor. Selecione **Aplicar**.

4. Repita os passos anteriores para todas as alterações de configuração.

Para automatizar este processo de exportação, utilize a CLI do Azure. O comando seguinte mostra como exportar uma alteração de configuração única do arquivo primário para o secundário:

    az appconfig kv export --destination appconfig --name {PrimaryStore} --label {Label} --dest-name {SecondaryStore} --dest-label {Label}

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a aumentar a sua aplicação para obter geo-resiliência durante o tempo de execução para a configuração de aplicação. Também pode incorporar dados de configuração de configuração de aplicações em tempo de compilação ou de implementação. Para obter mais informações, consulte [integrar com um pipeline CI/CD](./integrate-ci-cd-pipeline.md).


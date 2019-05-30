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
ms.openlocfilehash: 39e7a62899a7d1d6feb5bfd3b45ad4adc3c996a0
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66394028"
---
# <a name="resiliency-and-disaster-recovery"></a>Resiliência e recuperação após desastre

Atualmente a configuração de aplicações do Azure é um serviço regional. Cada arquivo de configuração é criado numa região do Azure específica. Uma interrupção de toda a região irá afetar todos os arquivos de nessa região. Configuração de aplicações não oferece o failover automático para outra região. Este artigo fornece orientações gerais sobre como pode usar vários arquivos de configuração entre regiões do Azure para aumentar a resiliência de georreplicação do seu aplicativo.

## <a name="high-availability-architecture"></a>Arquitetura de elevada disponibilidade

Para obter redundância entre regiões, terá de criar vários arquivos de configuração de aplicação em regiões diferentes. Com esta configuração, a aplicação irá ter, pelo menos, um arquivo de configuração adicionais para reverter para com o armazenamento primário fica inacessível. Segue-se um diagrama que ilustra a topologia entre seu aplicativo e seus arquivos de configuração primária e secundária.

![Lojas com redundância geográfica](./media/geo-redundant-app-configuration-stores.png)

Seu aplicativo carregará a respetiva configuração das lojas primárias e secundárias em paralelo. Isso aumenta a possibilidade de com êxito ao obter os dados de configuração significativamente. É responsável por manter os dados em ambos os armazenamentos em sincronia. As secções seguintes explicam como criar geo-resiliência na sua aplicação.

## <a name="failover-between-configuration-stores"></a>Ativação pós-falha entre arquivos de configuração

Tecnicamente, a aplicação não está a executar uma ativação pós-falha. Esta está a tentar obter o mesmo conjunto de dados de configuração a partir de dois arquivos de configuração de aplicação em simultâneo. Deve dispor seu código, de modo que ele for carregado pela primeira vez da loja secundária primeiro e, em seguida, armazenar a principal. Essa abordagem garante que os dados de configuração no arquivo primário precedência sempre que estiverem disponíveis. O fragmento de código abaixo mostra como pode implementar isso em .NET Core.

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

Observe que o `optional` parâmetro passado para o `AddAzureAppConfiguration` função. Quando definido como `true`, este parâmetro irá impedir que o aplicativo falhar continuar se a função não é possível carregar dados de configuração.

## <a name="synchronization-between-configuration-stores"></a>Sincronização entre os arquivos de configuração

É importante que todos os seus arquivos de configuração com redundância geográfica têm o mesmo conjunto de dados. Pode utilizar o **exportar** função numa configuração de aplicação para copiar dados do arquivo primário para a secundária sob demanda. Esta função está disponível através do portal do Azure e a CLI.

No portal do Azure, pode emitir uma alteração para outro arquivo de configuração ao seguir estes passos:

1. Navegue para **importação/exportação** separador, selecione **exportar**, selecione **configuração de aplicação** como o **destino** de serviço, clique em  **Selecione um recurso**.

2. No novo painel que abriu a cópia de segurança, especifique a subscrição, o grupo de recursos e o nome de recurso do seu armazenamento secundário e, em seguida, clique em **aplicar**.

3. A interface do Usuário será atualizada para que pode escolher os dados de configuração que pretende exportar para o seu armazenamento secundário. Pode deixar o valor de tempo predefinido como está e definir ambos **da etiqueta** e, a **etiqueta** para o mesmo valor. Clique em **Aplicar**.

4. Repita os passos acima para todas as alterações de configuração.

Pode automatizar esse processo com a CLI do Azure a exportar. O comando a seguir mostra como exportar uma alteração de configuração única do arquivo primário para o secundário.

    az appconfig kv export --destination appconfig --name {PrimaryStore} --label {Label} --dest-name {SecondaryStore} --dest-label {Label}

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu a aumentar a sua aplicação para obter geo-resiliência durante o tempo de execução para a configuração de aplicação. Em alternativa, pode incorporar dados de configuração de configuração de aplicações em tempo de compilação ou de implementação. Para obter mais informações, consulte [integrar com um pipeline CI/CD](./integrate-ci-cd-pipeline.md).


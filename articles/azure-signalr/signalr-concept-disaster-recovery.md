---
title: Resiliência e recuperação de desastre no serviço de Signaler do Azure
description: Uma visão geral de como configurar várias instâncias do serviço Signalr para obter resiliência e recuperação de desastres
author: chenkennt
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: kenchen
ms.openlocfilehash: cf0f345b0fbf9fea2512f72c1996c9a1597cc0cd
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747645"
---
# <a name="resiliency-and-disaster-recovery"></a>Resiliência e recuperação após desastre

Resiliência e recuperação de desastre é uma necessidade comum para sistemas online. O serviço de sinalizador do Azure já garante a disponibilidade de 99,9%, mas ainda é um serviço regional.
Sua instância de serviço está sempre em execução em uma região e não fará failover para outra região quando houver uma interrupção em toda a região.

Em vez disso, nosso SDK de serviço fornece uma funcionalidade para dar suporte a várias instâncias de serviço de sinalização e alternar automaticamente para outras instâncias quando algumas delas não estiverem disponíveis.
Com esse recurso, você poderá se recuperar quando ocorrer um desastre, mas precisará configurar a topologia de sistema certa por conta própria. Você aprenderá a fazer isso neste documento.

## <a name="high-available-architecture-for-signalr-service"></a>Arquitetura alta disponível para o serviço Signalr

Para ter resiliência entre regiões para o serviço Signalr, você precisa configurar várias instâncias de serviço em regiões diferentes. Então, quando uma região está inativa, as outras podem ser usadas como backup.
Ao conectar várias instâncias de serviço ao servidor de aplicativos, há duas funções, primária e secundária.
Primário é uma instância que está fazendo o tráfego online e o secundário é uma instância de backup totalmente funcional, mas para o primário.
Em nossa implementação de SDK, Negotiate retornará somente pontos de extremidade primários, portanto, em casos normais, os clientes se conectam somente aos pontos de extremidade primários.
Mas quando a instância primária está inativa, Negotiate retornará pontos de extremidade secundários para que o cliente ainda possa fazer conexões.
A instância primária e o servidor de aplicativos são conectados por meio de conexões de servidor normais, mas a instância secundária e o servidor de aplicativos são conectados por meio de um tipo especial de conexão chamada conexão fraca.
A principal diferença de uma conexão fraca é que ela não aceita o roteamento de conexão de cliente, pois a instância secundária está localizada em outra região. O roteamento de um cliente para outra região não é uma opção ideal (aumenta a latência).

Uma instância de serviço pode ter diferentes funções ao se conectar a vários servidores de aplicativos.
Uma configuração típica para o cenário entre regiões é ter dois (ou mais) pares de instâncias de serviço de sinalização e servidores de aplicativos.
Dentro de cada par, o servidor de aplicativos e o serviço de sinalização estão localizados na mesma região, e o serviço de sinalização é conectado ao servidor de aplicativos como uma função primária.
Entre cada servidor de aplicativo de pares e o serviço de sinalização também estão conectados, mas o Signalr se torna um secundário ao se conectar ao servidor em outra região.

Com essa topologia, a mensagem de um servidor ainda pode ser entregue a todos os clientes, pois todos os servidores de aplicativos e as instâncias do serviço de sinalização são interconectados.
Mas quando um cliente está conectado, ele é sempre roteado para o servidor de aplicativos na mesma região para obter a latência de rede ideal.

Veja abaixo um diagrama que ilustra essa topologia:

![topology](media/signalr-concept-disaster-recovery/topology.png)

## <a name="configure-app-servers-with-multiple-signalr-service-instances"></a>Configurar servidores de aplicativos com várias instâncias do serviço Signalr

Depois que você tiver os servidores de aplicativo e serviço de Signalr criados em cada região, poderá configurar seus servidores de aplicativo para se conectar a todas as instâncias de serviço do Signalr.

Há duas maneiras de fazer isso:

### <a name="through-config"></a>Por meio da configuração

Você já deve saber como definir a cadeia de conexão do serviço Signalr por meio de variáveis de ambiente/configurações de aplicativo/Web. cofig, em uma entrada de configuração chamada `Azure:SignalR:ConnectionString`.
Se você tiver vários pontos de extremidade, poderá defini-los em várias entradas de configuração, cada um com o seguinte formato:

```
Azure:SignalR:ConnectionString:<name>:<role>
```

Aqui `<name>` é o nome do ponto de extremidade e `<role>` é sua função (primária ou secundária).
O nome é opcional, mas será útil se você quiser personalizar ainda mais o comportamento de roteamento entre vários pontos de extremidade.

### <a name="through-code"></a>Por meio de código

Se você preferir armazenar as cadeias de conexão em outro lugar, você também poderá lê-las em seu código e usá-las como parâmetros ao chamar `AddAzureSignalR()` (em ASP.NET Core) ou `MapAzureSignalR()` (em ASP.NET).

Este é o código de exemplo:

ASP.NET Core:

```cs
services.AddSignalR()
        .AddAzureSignalR(options => options.Endpoints = new ServiceEndpoint[]
        {
            new ServiceEndpoint("<connection_string1>", EndpointType.Primary, "region1"),
            new ServiceEndpoint("<connection_string2>", EndpointType.Secondary, "region2"),
        });
```

ASP.NET:

```cs
app.MapAzureSignalR(GetType().FullName, hub,  options => options.Endpoints = new ServiceEndpoint[]
    {
        new ServiceEndpoint("<connection_string1>", EndpointType.Primary, "region1"),
        new ServiceEndpoint("<connection_string2>", EndpointType.Secondary, "region2"),
    };
```

Você pode configurar várias instâncias primárias ou secundárias. Se houver várias instâncias primárias e/ou secundárias, Negotiate retornará um ponto de extremidade na seguinte ordem:

1. Se houver pelo menos uma instância primária online, retorne uma instância online aleatória.
2. Se todas as instâncias primárias estiverem inativas, retorne uma instância online secundária aleatória.

## <a name="failover-sequence-and-best-practice"></a>Sequência de failover e prática recomendada

Agora você tem a configuração de topologia do sistema correta. Sempre que uma instância de serviço de sinalização estiver inoperante, o tráfego online será roteado para outras instâncias.
Veja o que acontece quando uma instância primária está inoperante (e é recuperada após algum tempo):

1. A instância do serviço primário está inativa, todas as conexões do servidor nesta instância serão descartadas.
2. Todos os servidores conectados a essa instância o marcarão como offline, e Negotiate interromperá o retorno desse ponto de extremidade e começará a retornar o ponto de extremidade secundário.
3. Todas as conexões de cliente nessa instância também serão fechadas, os clientes serão reconectados. Como os servidores de aplicativos agora retornam um ponto de extremidade secundário, os clientes se conectarão à instância secundária.
4. Agora, a instância secundária usa todo o tráfego online. Todas as mensagens do servidor para os clientes ainda podem ser entregues à medida que o secundário está conectado a todos os servidores de aplicativos. Mas o cliente para mensagens do servidor só é roteado para o servidor de aplicativos na mesma região.
5. Depois que a instância primária for recuperada e novamente online, o servidor de aplicativo restabelecerá as conexões a ela e a marcará como online. Negociar agora retornará o ponto de extremidade primário novamente para que novos clientes sejam conectados novamente ao primário. Mas os clientes existentes não serão descartados e continuarão sendo roteados para o secundário até que eles se desconectem.

Os diagramas abaixo ilustram como o failover é feito no serviço Signalr:

Fig. 1 antes do failover ![antes do failover](media/signalr-concept-disaster-recovery/before-failover.png)

Fig. 2 após o failover ![após o failover](media/signalr-concept-disaster-recovery/after-failover.png)

Fig. 3 tempo curto após a recuperação principal ![tempo curto após a recuperação principal](media/signalr-concept-disaster-recovery/after-recover.png)

Você pode ver em caso normal somente o servidor de aplicativo primário e o serviço de sinalização têm tráfego online (em azul).
Após o failover, o servidor de aplicativos secundário e o serviço de sinalização também se tornarão ativos.
Depois que o serviço de sinalizador primário estiver online novamente, os novos clientes se conectarão ao Signalr primário. Mas os clientes existentes ainda se conectam ao secundário, portanto, ambas as instâncias têm tráfego.
Depois que todos os clientes existentes forem desconectados, o sistema voltará para o normal (Fig. 1).

Há dois padrões principais para implementar uma arquitetura de alta disponibilidade entre regiões:

1. A primeira é ter um par de servidor de aplicativos e instância de serviço de sinalização que ocupa todo o tráfego online e ter outro par como um backup (chamado ativo/passivo, ilustrado em Fig. 1). 
2. A outra é ter dois (ou mais) pares de servidores de aplicativos e instâncias de serviço de sinalização, cada um fazendo parte do tráfego online e serve como backup para outros pares (chamados ativo/ativo, semelhante a Fig. 3).

O serviço signalr pode dar suporte a ambos os padrões, a principal diferença é como você implementa servidores de aplicativos.
Se os servidores de aplicativos estiverem ativos/passivos, o serviço Signalr também será ativo/passivo (pois o servidor de aplicativo primário retornará apenas sua instância de serviço do Signalr principal).
Se os servidores de aplicativos estiverem ativos/ativos, o serviço Signalr também estará ativo/ativo (pois todos os servidores de aplicativos retornarão suas próprias instâncias de Signaler primárias, para que todos possam obter tráfego).

Observe que, independentemente dos padrões que você optar por usar, você precisará conectar cada instância do serviço Signalr a um servidor de aplicativos como primário.

Também devido à natureza da conexão do Signalr (é uma conexão longa), os clientes terão quedas de conexão quando houver um desastre e o failover ocorrer.
Você precisará lidar com esses casos no lado do cliente para torná-lo transparente para seus clientes finais. Por exemplo, reconecte depois que uma conexão é fechada.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu a configurar seu aplicativo para obter resiliência para o serviço Signalr. Para entender mais detalhes sobre conexão de servidor/cliente e roteamento de conexão no serviço Signalr, você pode ler [Este artigo](signalr-concept-internals.md) para os elementos internos do serviço signalr.

Para cenários de dimensionamento, como fragmentação, que usam várias instâncias em conjunto para lidar com um grande número de conexões, leia [como dimensionar várias instâncias](signalr-howto-scale-multi-instances.md).

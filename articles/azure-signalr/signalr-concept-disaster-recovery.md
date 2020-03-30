---
title: Resiliência e recuperação de desastres no Serviço De Sinalização Azure
description: Uma visão geral sobre como configurar múltiplas instâncias de serviço SignalR para alcançar resiliência e recuperação de desastres
author: chenkennt
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: kenchen
ms.openlocfilehash: cf0f345b0fbf9fea2512f72c1996c9a1597cc0cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73747645"
---
# <a name="resiliency-and-disaster-recovery"></a>Resiliência e recuperação após desastre

A resiliência e a recuperação de desastres são uma necessidade comum para os sistemas online. O Serviço De Sinalização Azure já garante 99,9% de disponibilidade, mas continua a ser um serviço regional.
A sua instância de serviço está sempre a decorrer numa região e não falha em outra região quando há uma paragem em toda a região.

Em vez disso, o nosso serviço SDK fornece uma funcionalidade para suportar várias instâncias de serviço SignalR e mudar automaticamente para outras instâncias quando algumas delas não estão disponíveis.
Com esta funcionalidade, poderá recuperar quando ocorrer um desastre, mas terá de configurar a topologia do sistema certa sozinho. Vaiaprender a fazê-lo neste documento.

## <a name="high-available-architecture-for-signalr-service"></a>Arquitetura alta disponível para serviço SignalR

Para ter resiliência transversal para o serviço SignalR, é necessário configurar várias instâncias de serviço em diferentes regiões. Assim, quando uma região está em baixo, as outras podem ser usadas como reserva.
Ao ligar várias instâncias de serviço ao servidor de aplicações, existem duas funções, primárias e secundárias.
Primário é um caso que está tomando tráfego on-line e secundário é um exemplo totalmente funcional, mas de reserva para primárias.
Na nossa implementação do SDK, a negociação só devolverá pontos finais primários, pelo que, em caso normal, os clientes apenas se ligam a pontos finais primários.
Mas quando a instância primária estiver em baixo, a negociação devolverá pontos finais secundários para que o cliente ainda possa fazer ligações.
A instância primária e o servidor de aplicações estão ligados através de ligações normais do servidor, mas a instância secundária e o servidor de aplicações estão ligados através de um tipo especial de ligação chamado ligação fraca.
A principal diferença de uma ligação fraca é que não aceita o encaminhamento de ligação ao cliente, porque a instância secundária está localizada noutra região. Encaminhar um cliente para outra região não é uma escolha ideal (aumenta a latência).

Uma instância de serviço pode ter diferentes funções quando se conecta a vários servidores de aplicações.
Uma configuração típica para o cenário da região transversal é ter dois (ou mais) pares de instâncias de serviço SignalR e servidores de aplicações.
Dentro de cada servidor de aplicações de pares e o serviço SignalR estão localizados na mesma região, e o serviço SignalR está ligado ao servidor de aplicações como uma função principal.
Entre cada servidor de aplicações de pares e o serviço SignalR também estão ligados, mas o SignalR torna-se secundário quando se conecta ao servidor noutra região.

Com esta topologia, a mensagem de um servidor ainda pode ser entregue a todos os clientes, uma vez que todos os servidores de aplicações e instâncias de serviço SignalR estão interligadas.
Mas quando um cliente está conectado, é sempre encaminhado para o servidor de aplicações na mesma região para obter a latência ideal da rede.

Abaixo está um diagrama que ilustra tal topologia:

![topology](media/signalr-concept-disaster-recovery/topology.png)

## <a name="configure-app-servers-with-multiple-signalr-service-instances"></a>Configure servidores de aplicativos com várias instâncias de serviço SignalR

Assim que tiver o serviço SignalR e servidores de aplicações criados em cada região, pode configurar os seus servidores de aplicações para se ligar a todas as instâncias de serviço SignalR.

Há duas maneiras de o fazer:

### <a name="through-config"></a>Através de config

Já deve saber como definir a cadeia de ligação de serviço SignalR através de variáveis ambientais/configurações de aplicações/web.cofig, numa entrada de config chamada `Azure:SignalR:ConnectionString`.
Se tiver vários pontos finais, pode defini-los em várias entradas de config, cada uma no seguinte formato:

```
Azure:SignalR:ConnectionString:<name>:<role>
```

Aqui `<name>` está o nome do `<role>` ponto final e é o seu papel (primário ou secundário).
O nome é opcional, mas será útil se quiser personalizar ainda mais o comportamento de encaminhamento entre vários pontos finais.

### <a name="through-code"></a>Através do código

Se preferir armazenar as cordas de ligação noutro lugar, também pode lê-las `AddAzureSignalR()` no seu código `MapAzureSignalR()` e usá-las como parâmetros ao ligar (em ASP.NET Core) ou (em ASP.NET).

Aqui está o código da amostra:

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

Pode configurar várias instâncias primárias ou secundárias. Se houver múltiplas instâncias primárias e/ou secundárias, a negociação devolverá um ponto final na seguinte ordem:

1. Se houver pelo menos uma instância primária on-line, devolva uma instância primária aleatória online.
2. Se todos os casos primários estiverem em baixo, devolva uma instância secundária secundária aleatória online.

## <a name="failover-sequence-and-best-practice"></a>Sequência de failover e boas práticas

Agora tens a configuração de topologia do sistema certa. Sempre que uma instância de serviço SignalR estiver em baixo, o tráfego on-line será encaminhado para outras instâncias.
Eis o que acontece quando uma instância primária está em baixo (e recupera passado algum tempo):

1. A instância de serviço principal está em baixo, todas as ligações do servidor nesta instância serão eliminadas.
2. Todos os servidores ligados a esta instância marcarão como offline, e negociará deixará de devolver este ponto final e começará a devolver o ponto final secundário.
3. Todas as ligações com clientes neste caso também serão encerradas, os clientes voltarão a ligar-se. Uma vez que os servidores de aplicações agora devolvem o ponto final secundário, os clientes vão ligar-se à instância secundária.
4. Agora a instância secundária leva todo o tráfego on-line. Todas as mensagens do servidor para os clientes ainda podem ser entregues, uma vez que o secundário está ligado a todos os servidores de aplicações. Mas as mensagens de cliente para servidor são apenas encaminhadas para o servidor de aplicações na mesma região.
5. Após a ocorrência primária ser recuperada e novamente on-line, o servidor de aplicações restabelecerá as ligações e marcará-as como online. A negociação voltará agora a devolver o ponto final primário de novo para que os novos clientes estejam ligados às primárias. Mas os clientes existentes não serão abandonados e continuarão a ser encaminhados para o secundário até se desligarem.

Os diagramas abaixo ilustram como a falha é feita no serviço SignalR:

Fig.1 Antes ![da falha antes da falha](media/signalr-concept-disaster-recovery/before-failover.png)

Fig.2 Após ![falha após falha](media/signalr-concept-disaster-recovery/after-failover.png)

Fig.3 Pouco tempo depois ![de as primárias recuperarem pouco tempo após recuperação primária](media/signalr-concept-disaster-recovery/after-recover.png)

Pode ver no caso normal que apenas o servidor primário de aplicações e o serviço SignalR têm tráfego on-line (em azul).
Após a falha, o servidor secundário de aplicações e o serviço SignalR também se tornam ativos.
Após o serviço de SignalR primário voltar a funcionar, os novos clientes ligar-se-ão ao SignalR primário. Mas os clientes existentes ainda se ligam ao secundário, pelo que ambos os casos têm tráfego.
Depois de todos os clientes existentes se desligarem, o seu sistema voltará ao normal (Fig.1).

Existem dois padrões principais para a implementação de uma arquitetura disponível de região transversal:

1. O primeiro é ter um par de servidores de aplicações e instância de serviço SignalR tomando todo o tráfego on-line, e ter outro par como uma cópia de segurança (chamada ativa/passiva, ilustrada em Fig.1). 
2. O outro é ter dois (ou mais) pares de servidores de aplicações e instâncias de serviço SignalR, cada um tomando parte do tráfego on-line e serve de backup para outros pares (chamados ativos/ativos, semelhantes ao Fig.3).

O serviço SignalR pode suportar ambos os padrões, a principal diferença é como implementa servidores de aplicações.
Se os servidores de aplicações estiverem ativos/passivos, o serviço SignalR também será ativo/passivo (uma vez que o servidor principal de aplicações apenas devolve a sua principal instância de serviço SignalR).
Se os servidores de aplicações estiverem ativos/ativos, o serviço SignalR também estará ativo/ativo (uma vez que todos os servidores de aplicações devolverão as suas próprias instâncias primárias de SignalR, para que todos possam obter tráfego).

Note-se que independentemente dos padrões que escolher utilizar, terá de ligar cada instância de serviço SignalR a um servidor de aplicações como primário.

Também devido à natureza da ligação SignalR (é uma ligação longa), os clientes vão experimentar quedas de ligação quando há um desastre e a falha ocorre.
Terá de lidar com estes casos ao lado do cliente para torná-lo transparente para os seus clientes finais. Por exemplo, religue a ligação depois de uma ligação estar fechada.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a configurar a sua aplicação para obter resiliência para o serviço SignalR. Para compreender mais detalhes sobre a ligação servidor/cliente e encaminhamento de ligação no serviço SignalR, pode ler [este artigo](signalr-concept-internals.md) para os internos do serviço SignalR.

Para cenários de escala, como sharding, que usam várias instâncias em conjunto para lidar com um grande número de ligações, leia [como escalar várias instâncias](signalr-howto-scale-multi-instances.md).

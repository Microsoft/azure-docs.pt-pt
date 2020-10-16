---
title: Resiliência e recuperação de desastres no Serviço Azure SignalR
description: Uma visão geral sobre como configurar várias instâncias de serviço SignalR para alcançar resiliência e recuperação de desastres
author: chenkennt
ms.service: signalr
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 03/01/2019
ms.author: kenchen
ms.openlocfilehash: b1cb48d1ae858dbcd0df80780b4c3cee3deac75b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90976497"
---
# <a name="resiliency-and-disaster-recovery-in-azure-signalr-service"></a>Resiliência e recuperação de desastres no Serviço Azure SignalR

A resiliência e a recuperação de desastres são uma necessidade comum de sistemas online. O Serviço Azure SignalR já garante 99,9% de disponibilidade, mas ainda é um serviço regional.
A sua instância de serviço está sempre a decorrer numa região e não vai falhar para outra região quando há uma paragem em toda a região.

Em vez disso, o nosso serviço SDK fornece uma funcionalidade para suportar várias instâncias de serviço SignalR e mudar automaticamente para outras instâncias quando algumas delas não estão disponíveis.
Com esta funcionalidade, poderás recuperar quando ocorrer um desastre, mas terás de configurar sozinho a topologia do sistema certo. Aprenderá a fazê-lo neste documento.

## <a name="high-available-architecture-for-signalr-service"></a>Alta arquitetura disponível para o serviço SignalR

Para ter resiliência transversal para o serviço SignalR, é necessário configurar várias instâncias de serviço em diferentes regiões. Assim, quando uma região está em baixo, as outras podem ser usadas como apoio.
Ao ligar várias instâncias de serviço ao servidor de aplicações, existem duas funções, primárias e secundárias.
Primária é um caso que está tomando tráfego on-line e secundário é uma instância totalmente funcional, mas backup para o primário.
Na nossa implementação SDK, o negociação só devolverá os pontos finais primários para que, no caso normal, os clientes apenas se conectem aos pontos finais primários.
Mas quando a primeira instância estiver em baixo, as negociações devolverão os pontos finais secundários para que o cliente ainda possa fazer ligações.
As instâncias primárias e o servidor de aplicações estão conectados através de ligações normais do servidor, mas as instâncias secundárias e o servidor de aplicações estão ligados através de um tipo especial de ligação chamado ligação fraca.
A principal diferença de uma ligação fraca é que não aceita o encaminhamento de ligação ao cliente, porque a instância secundária está localizada noutra região. Encaminhar um cliente para outra região não é uma escolha ideal (aumenta a latência).

Uma instância de serviço pode ter funções diferentes ao ligar-se a vários servidores de aplicações.
Uma configuração típica para cenário de região cruzada é ter dois (ou mais) pares de instâncias de serviço SignalR e servidores de aplicações.
Dentro de cada servidor de aplicativos de pares e o serviço SignalR estão localizados na mesma região, e o serviço SignalR está ligado ao servidor de aplicações como um papel principal.
Entre cada servidor de aplicativos de pares e serviço SignalR também estão conectados, mas o SignalR torna-se secundário ao ligar-se ao servidor noutra região.

Com esta topologia, a mensagem de um servidor ainda pode ser entregue a todos os clientes, uma vez que todos os servidores de aplicações e instâncias de serviço SignalR estão interligados.
Mas quando um cliente está conectado, é sempre encaminhado para o servidor de aplicações na mesma região para obter a latência ideal da rede.

Abaixo está um diagrama que ilustra tal topologia:

![O diagrama mostra duas regiões cada uma com um servidor de aplicações e um serviço SignalR, onde cada servidor está associado ao serviço SignalR na sua região como primário e com o serviço na outra região como secundário.](media/signalr-concept-disaster-recovery/topology.png)

## <a name="configure-app-servers-with-multiple-signalr-service-instances"></a>Configurar servidores de aplicações com múltiplas instâncias de serviço SignalR

Uma vez criados servidores de serviço SignalR e aplicativos em cada região, pode configurar os seus servidores de aplicações para se conectar a todas as instâncias de serviço signalR.

Há duas maneiras de o fazer:

### <a name="through-config"></a>Através de config

Já deve saber como definir a cadeia de ligação de serviço SignalR através de variáveis ambientais/definições de aplicação/web.cofig, numa entrada config chamada `Azure:SignalR:ConnectionString` .
Se tiver vários pontos finais, pode defini-los em múltiplas entradas config, cada uma no seguinte formato:

```
Azure:SignalR:ConnectionString:<name>:<role>
```

Aqui `<name>` está o nome do ponto final e é o seu papel `<role>` (primário ou secundário).
O nome é opcional, mas será útil se quiser personalizar ainda mais o comportamento de encaminhamento entre vários pontos finais.

### <a name="through-code"></a>Através do código

Se preferir armazenar as cadeias de ligação em outro lugar, também pode lê-las no seu código e usá-las como parâmetros ao ligar `AddAzureSignalR()` (em ASP.NET Core) ou `MapAzureSignalR()` (em ASP.NET).

Aqui está o código de amostra:

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

1. Se houver pelo menos uma instância primária online, devolva uma instância primária aleatória online.
2. Se todas as instâncias primárias estiverem em baixo, devolva uma instância secundária aleatória online.

## <a name="failover-sequence-and-best-practice"></a>Sequência de failover e boas práticas

Agora tens a configuração correta da topologia do sistema. Sempre que uma instância de serviço do SignalR estiver em baixo, o tráfego online será encaminhado para outras instâncias.
Aqui está o que acontece quando uma instância primária está em baixo (e recupera após algum tempo):

1. A instância de serviço principal está em baixo, todas as ligações do servidor neste caso serão retiradas.
2. Todos os servidores ligados a este caso irão marcá-lo como offline, e o negocial deixará de devolver este ponto final e começará a devolver o ponto final secundário.
3. Todas as ligações com o cliente neste caso também serão encerradas, os clientes voltarão a ligar-se. Uma vez que os servidores de aplicações regressam agora ao ponto final secundário, os clientes ligar-se-ão a instâncias secundárias.
4. Agora a segunda instância leva todo o tráfego online. Todas as mensagens do servidor para os clientes ainda podem ser entregues, uma vez que o secundário está ligado a todos os servidores de aplicações. Mas as mensagens de cliente para servidores são apenas encaminhadas para o servidor de aplicações na mesma região.
5. Após a recuperação e reinter-a-forma, o servidor de aplicações restabelecerá as ligações e marcará-as como online. A negociação vai agora devolver o principal ponto final novamente para que novos clientes estejam ligados de volta ao primário. Mas os clientes existentes não serão abandonados e continuarão a ser encaminhados para secundários até se desligarem.

Os diagramas abaixo ilustram como o failover é feito no serviço SignalR:

Fig.1 Antes de falhar ![ antes do failover](media/signalr-concept-disaster-recovery/before-failover.png)

Fig.2 Após o failover ![ após o failover](media/signalr-concept-disaster-recovery/after-failover.png)

Fig.3 Pouco tempo após a recuperação primária ![ Pouco tempo após recuperação primária](media/signalr-concept-disaster-recovery/after-recover.png)

Pode ver no caso normal que apenas o servidor de aplicações primárias e o serviço SignalR têm tráfego online (em azul).
Após o failover, o servidor de aplicações secundárias e o serviço SignalR também ficam ativos.
Depois do serviço de SinalR primário estar novamente on-line, novos clientes ligar-se-ão ao SignalR primário. Mas os clientes existentes ainda se ligam ao secundário, por isso ambas as instâncias têm tráfego.
Depois de todos os clientes existentes desligarem-se, o seu sistema voltará ao normal (Fig.1).

Existem dois padrões principais para a implementação de uma arquitetura transversal de alta disponíveis:

1. O primeiro é ter um par de servidores de aplicações e uma instância de serviço SignalR tomando todo o tráfego on-line, e ter outro par como uma cópia de segurança (chamada ativa/passiva, ilustrada em Fig.1). 
2. O outro é ter dois (ou mais) pares de servidores de aplicações e instâncias de serviço SignalR, cada um que participa do tráfego online e serve como backup para outros pares (chamados ativos/ativos, semelhantes ao Fig.3).

O serviço SignalR pode suportar ambos os padrões, a principal diferença é como implementa servidores de aplicações.
Se os servidores de aplicações estiverem ativos/passivos, o serviço SignalR também será ativo/passivo (uma vez que o servidor de aplicações primárias apenas devolve a sua primeira instância de serviço SignalR).
Se os servidores de aplicações estiverem ativos/ativos, o serviço SignalR também estará ativo/ativo (uma vez que todos os servidores de aplicações irão devolver as suas próprias instâncias primárias do SignalR, para que todos possam obter tráfego).

Seja notado independentemente dos padrões que escolher utilizar, terá de ligar cada instância de serviço SignalR a um servidor de aplicações como principal.

Também devido à natureza da ligação SignalR (é uma ligação longa), os clientes vão experimentar quedas de conexão quando há um desastre e falha ocorrer.
Terá de lidar com estes casos ao lado do cliente para torná-lo transparente para os seus clientes finais. Por exemplo, reconecte-se depois de fechado um ligação.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a configurar a sua aplicação para obter resiliência para o serviço SignalR. Para obter mais detalhes sobre a ligação servidor/cliente e o encaminhamento de ligação no serviço SignalR, pode ler [este artigo](signalr-concept-internals.md) para os internos do serviço SignalR.

Para cenários de escala, tais como o fragmento, que utilizam várias instâncias em conjunto para lidar com um grande número de ligações, leia [como escalar várias instâncias](signalr-howto-scale-multi-instances.md).

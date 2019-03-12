---
title: Resiliência e recuperação após desastre no serviço Azure SignalR
description: Uma descrição geral sobre como configurar a várias instâncias do serviço SignalR para alcançar a resiliência e recuperação após desastre
author: chenkennt
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: kenchen
ms.openlocfilehash: 69a2d9e7858c0f152056e821c19caa9852b420d5
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57555250"
---
# <a name="resiliency-and-disaster-recovery"></a>Resiliência e recuperação após desastre

Resiliência e recuperação após desastre é necessário comuns para sistemas online. Serviço Azure SignalR já garante a disponibilidade de 99,9%, mas ainda é um serviço regional.
A instância de serviço está sempre em execução numa única região e não a ativação pós-falha para outra região quando houver uma interrupção de toda a região.

Em vez disso, o nosso serviço SDK fornece uma funcionalidade para suportar várias instâncias do serviço SignalR e mudar automaticamente para outras instâncias quando alguns deles não estão disponíveis.
Com esta funcionalidade, poderá recuperar quando existe após desastre ocorre, mas terá de configurar a topologia do sistema correto sozinho. Ficará a saber como fazê-lo neste documento.

## <a name="high-available-architecture-for-signalr-service"></a>Alta arquitetura disponível para o serviço SignalR

Para ter a resiliência de entre várias regiões para o serviço SignalR, terá de configurar várias instâncias do serviço em regiões diferentes. Então, quando uma região está inativa, as outras podem ser utilizadas como cópia de segurança.
Ao ligar-se várias instâncias do serviço para o servidor de aplicações, existem duas funções primárias e secundárias.
Principal é uma instância que está a demorar tráfego online e secundária é uma instância totalmente funcional, mas cópia de segurança principal.
Em nossa implementação de SDK, negociar irá devolver apenas os pontos de extremidade primários, de modo normal caso os clientes só ligam a pontos de extremidade primários.
Mas quando a instância primária estiver desativado, negociar irá devolver pontos de extremidade secundários, para que o cliente ainda pode fazer conexões.
Instância principal e o servidor de aplicações se encontram ligados através de ligações de servidor normal, mas servidor secundário de instância e aplicações estão ligados através de um tipo especial de ligações chamado ligações fracas.
A principal diferença de uma conexão fraca é que ela não aceita encaminhamento de ligação de cliente, porque a instância secundária está normalmente localizada noutra região. Encaminhamento de um cliente para outra região, normalmente, não é uma opção ideal (aumenta a latência).

Uma instância de serviço pode ter diferentes funções, ao ligar a vários servidores de aplicação.
Uma configuração típica para cruzada região cenário consiste em pares de dois (ou mais) de instâncias de serviço do SignalR e servidores de aplicações.
Dentro de cada aplicação de par o servidor e o serviço SignalR estão localizados na mesma região, e o serviço SignalR está ligado para o servidor de aplicação como uma função primária.
Entre cada aplicação de pares servidor e o serviço SignalR também estão ligados, mas SignalR se torna uma secundária quando ligar ao servidor noutra região.

Com esta topologia, mensagem de um servidor ainda pode ser entregue a todos os clientes, como todos os servidores de aplicações e instâncias do serviço SignalR estão interligadas.
Mas, quando um cliente está ligado, sempre é encaminhado para o servidor de aplicações na mesma região para alcançar a latência de rede não ideais.

Segue-se um diagrama que ilustra essa topologia:

![topology](media/signalr-concept-disaster-recovery/topology.png)

## <a name="configure-app-servers-with-multiple-signalr-service-instances"></a>Configure os servidores de aplicação com várias instâncias do serviço SignalR

Quando tiver servidores de serviço e a aplicação do SignalR criados em cada região, pode configurar os servidores de aplicação para ligar a todas as instâncias de serviço do SignalR.

Existem duas formas, pode fazer isso:

### <a name="through-config"></a>Por meio de configuração

Já deve saber como definir a cadeia de ligação de serviço SignalR por meio de settings/web.cofig variáveis/aplicação de ambiente, por meio de uma entrada de configuração com o nome `Azure:SignalR:ConnectionString`.
Se tiver vários pontos de extremidade, pode defini-las em várias entradas de configuração, cada um no seguinte formato:

```
Azure:SignalR:Connection:<name>:<role>
```

Aqui `<name>` é o nome do ponto de extremidade e `<role>` é sua função (primário ou secundário).
Nome é opcional, mas é útil se desejar personalizar ainda mais o comportamento do encaminhamento entre vários pontos de extremidade.

### <a name="through-code"></a>Por meio de código

Se preferir armazenar a cadeia de ligação em algum lugar, também pode lê-lo em seu código e usá-los como parâmetros ao chamar `AddAzureSignalR()` (em ASP.NET Core) ou `MapAzureSignalR()` (no ASP.NET).

Eis o código de exemplo:

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

## <a name="failover-sequence-and-best-practice"></a>Sequência de ativação pós-falha e nas práticas recomendadas

Agora tem a configuração de topologia do sistema correto. Sempre que uma instância de serviço do SignalR estiver desativado, o tráfego online será encaminhado para as outras instâncias.
Eis o que acontece quando uma instância primária estiver em baixo (e recupera após algum tempo):

1. Instância de serviço primário estiver em baixo, todas as ligações de servidor nesta instância serão removidas.
2. Todos os servidores ligados a esta instância serão marcá-la como offline e negociar irá parar de retornar este ponto final e iniciar a retornar o ponto final secundário.
3. Todas as ligações de cliente nesta instância também serão fechadas, os clientes restabelecer a ligação. Uma vez que os servidores de aplicações agora retornarem o ponto final secundário, os clientes estabelecerão ligação à instância secundária.
4. Agora a instância secundária assume todo o tráfego online. Todas as mensagens de servidor para os clientes podem ainda ser entregues como secundário está ligado a todos os servidores de aplicação. Mas o cliente para mensagens de servidor apenas são encaminhados para o servidor de aplicação na mesma região.
5. Após a instância principal é recuperada e voltar a ficar online, o servidor de aplicações restabelecer as ligações à mesma e marcá-la como online. Negocie irá agora retorno ponto final primário novamente por isso, novos clientes estão ligados ao principal. Mas os clientes existentes não ser removidos e continuarão a ser encaminhado para o secundário até que eles desligar propriamente ditas.

Abaixo diagramas mostram como a ativação pós-falha é feita no serviço SignalR:

Ativação pós-falha antes de fig.1 ![antes de ativação pós-falha](media/signalr-concept-disaster-recovery/before-failover.png)

Ativação pós-falha depois de fig.2 ![após a ativação pós-falha](media/signalr-concept-disaster-recovery/after-failover.png)

Fig.3 pouco tempo depois de recuperar primário ![pouco tempo depois de recuperar primário](media/signalr-concept-disaster-recovery/after-recover.png)

Pode ver na normal caso primário apenas o servidor de aplicações e serviço SignalR online o tráfego (a azul).
Após a ativação pós-falha, servidor secundário da aplicação e serviço SignalR também fique ativas.
Depois do serviço de SignalR principal esteja novamente online, os novos clientes irão ligar ao SignalR primário. Mas os clientes existentes continuar a ligar para o secundário para que ambas as instâncias tenham tráfego.
Afinal de contas existentes desligar de clientes, o seu sistema será voltar ao normal (Fig.1).

Existem dois padrões principais para implementar uma arquitetura de elevada disponibilidade entre regiões:

1. A primeira é ter um par de servidor de aplicações e a instância de serviço do SignalR tirar todo o tráfego online e ter outro par de cópia de segurança (chamado ativo/passivo, ilustrada na Fig.1). 
2. O outro é ter dois (ou mais) pares de servidores de aplicações e instâncias de serviço do SignalR, cada uma passar parte do tráfego online e serve como cópia de segurança para outros pares (ativo/ativo, semelhante a Fig.3 chamado).

Serviço SignalR pode oferecer suporte a ambos os padrões, a principal diferença é como implementar servidores de aplicações.
Se tiver servidores de aplicação ativo/passivo, o serviço SignalR também vai estar ativo/passivo (como o servidor de aplicação principal retorna apenas a instância de serviço SignalR primária).
Se tiver servidores de aplicação ativo/ativo, o serviço SignalR também vai estar ativo/ativo (como todos os servidores de aplicações irão devolver a suas próprias instâncias principal do SignalR, para que todos eles podem fazer o tráfego).

Volte a ser observado, independentemente de quais padrões de optar por utilizar, terá de ligar a cada instância de serviço do SignalR para um servidor de aplicações como principal.

Também devido à natureza de ligação do SignalR (é uma ligação longa), os clientes terão quedas de ligação quando houver um desastre e ativação pós-falha ocorrer.
Terá de lidar com esses casos no lado do cliente para torná-la transparente para os seus clientes finais. Por exemplo, voltar a ligar depois de uma ligação foi fechada.

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu a configurar a sua aplicação para obter resiliência para o serviço SignalR. Para saber mais detalhes sobre a ligação de servidor/cliente e o encaminhamento da ligação no serviço do SignalR, pode ler [este artigo](signalr-concept-internals.md) para elementos internos do serviço de SignalR.
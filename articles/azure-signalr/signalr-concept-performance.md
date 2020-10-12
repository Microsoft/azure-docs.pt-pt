---
title: Guia de desempenho do Azure SignalR Service
description: Uma visão geral do desempenho e referência do Serviço Azure SignalR. Métricas-chave a ter em conta no planeamento da capacidade.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 68cad32be177fa20794399157fca89e87c2f8f59
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "74157660"
---
# <a name="performance-guide-for-azure-signalr-service"></a>Guia de desempenho do Azure SignalR Service

Um dos principais benefícios da utilização do Serviço Azure SignalR é a facilidade de dimensionar as aplicações SignalR. Num cenário de grande escala, o desempenho é um fator importante. 

Neste guia, vamos introduzir os fatores que afetam o desempenho da aplicação signalR. Descreveremos o desempenho típico em diferentes cenários de casos de uso. No final, vamos introduzir o ambiente e as ferramentas que pode usar para gerar um relatório de desempenho.

## <a name="term-definitions"></a>Definições de prazo

*Entrada*: A mensagem de entrada para o Serviço Azure SignalR.

*Saída*: A mensagem de saída do Serviço Azure SignalR.

*Largura de banda*: O tamanho total de todas as mensagens em 1 segundo.

*Modo predefinido*: O modo de funcionamento predefinido quando foi criada uma instância do Serviço Azure SignalR. O Azure SignalR Service espera que o servidor da aplicação estabeleça uma ligação com o mesmo antes de aceitar quaisquer ligações ao cliente.

*Modo serverless*: Um modo no qual o Serviço Azure SignalR aceita apenas ligações ao cliente. Não é permitida qualquer ligação ao servidor.

## <a name="overview"></a>Descrição geral

O Serviço Azure SignalR define sete níveis Standard para diferentes capacidades de desempenho. Este guia responde às seguintes perguntas:

-   Qual é o desempenho típico do Serviço Azure SignalR para cada nível?

-   O Serviço Azure SignalR satisfaz os meus requisitos de produção de mensagens (por exemplo, envio de 100.000 mensagens por segundo)?

-   Para o meu cenário específico, qual é o nível adequado para mim? Ou como posso selecionar o nível adequado?

-   Que tipo de servidor de aplicativos (tamanho VM) é adequado para mim? Quantos devo enviar?

Para responder a estas questões, este guia dá primeiro uma explicação de alto nível dos fatores que afetam o desempenho. Em seguida, ilustra as mensagens máximas de entrada e saída para cada níveis para casos de uso típico: **eco,** **transmissão,** **envio para grupo,** e **enviar para a ligação** (conversação entre pares).

Este guia não pode cobrir todos os cenários (e casos de uso diferentes, tamanhos de mensagem, padrões de envio de mensagens, e assim por diante). Mas fornece alguns métodos para ajudá-lo:

- Avalie o seu requisito aproximado para as mensagens de entrada ou saída.
- Encontre os níveis adequados verificando a tabela de desempenho.

## <a name="performance-insight"></a>Insight de desempenho

Esta secção descreve as metodologias de avaliação de desempenho e, em seguida, lista todos os fatores que afetam o desempenho. No final, fornece métodos para ajudá-lo a avaliar os requisitos de desempenho.

### <a name="methodology"></a>Metodologia

*A produção* e *a latência* são dois aspetos típicos da verificação de desempenho. Para o Serviço Azure SignalR, cada nível SKU tem a sua própria política de aceleração de produção. A política define *a produção máxima permitida (largura* de banda de entrada e saída) como a produção máxima alcançada quando 99% das mensagens têm latência que é menos de 1 segundo.

A latência é o tempo da ligação que envia a mensagem para receber a mensagem de resposta do Serviço Azure SignalR. Vamos tomar **eco** como um exemplo. Cada ligação de cliente adiciona um carimbo de tempo na mensagem. O centro do servidor de aplicações envia a mensagem original de volta para o cliente. Assim, o atraso de propagação é facilmente calculado por cada ligação ao cliente. A data é anexada para cada mensagem **transmitida,** **enviar para o grupo,** e **enviar para a ligação**.

Para simular milhares de conexões de clientes simultâneas, várias VMs são criadas numa rede privada virtual em Azure. Todos estes VMs ligam-se à mesma instância do Serviço Azure SignalR.

No modo predefinido do Serviço Azure SignalR, os VMs do servidor de aplicações são implantados na mesma rede privada virtual que os VM do cliente. Todos os VMs do cliente e vMs do servidor de aplicações são implantados na mesma rede da mesma região para evitar a latência transversal.

### <a name="performance-factors"></a>Fatores de desempenho

Teoricamente, a capacidade do Serviço Azure SignalR é limitada por recursos computatórios: CPU, memória e rede. Por exemplo, mais ligações ao Serviço Azure SignalR fazem com que o serviço utilize mais memória. Para tráfego de mensagens maiores (por exemplo, cada mensagem é maior do que 2.048 bytes), o Serviço Azure SignalR precisa de gastar mais ciclos de CPU para processar o tráfego. Entretanto, a largura de banda da rede Azure também impõe um limite para o tráfego máximo.

O tipo de transporte é outro fator que afeta o desempenho. Os três tipos são [WebSocket,](https://en.wikipedia.org/wiki/WebSocket) [Server-Sent-Event](https://en.wikipedia.org/wiki/Server-sent_events)e [Long-Poling](https://en.wikipedia.org/wiki/Push_technology). 

WebSocket é um protocolo de comunicação bidirecional e duplex completo sobre uma única ligação TCP. Server-Sent-Event é um protocolo unidirecional para empurrar mensagens do servidor para o cliente. Long-Polling exige que os clientes regiscam periodicamente informações do servidor através de um pedido HTTP. Para a mesma API nas mesmas condições, o WebSocket tem o melhor desempenho, o Server-Sent-Event é mais lento e Long-Polling é o mais lento. O Serviço Azure SignalR recomenda o WebSocket por padrão.

O custo de encaminhamento de mensagens também limita o desempenho. O Serviço Azure SignalR desempenha um papel como router de mensagens, que encaminha a mensagem de um conjunto de clientes ou servidores para outros clientes ou servidores. Um cenário diferente ou API requer uma política de encaminhamento diferente. 

Para **eco,** o cliente envia uma mensagem para si mesmo, e o destino de encaminhamento também é em si. Este padrão tem o menor custo de encaminhamento. Mas para **a transmissão**, **enviar para o grupo**, e enviar para a **conexão**, o Serviço Azure SignalR precisa de procurar as ligações-alvo através da estrutura interna de dados distribuídas. Este processamento extra utiliza mais CPU, memória e largura de banda de rede. Como resultado, o desempenho é mais lento.

No modo padrão, o servidor de aplicações também pode tornar-se um estrangulamento para certos cenários. O Azure SignalR SDK tem de invocar o hub, mantendo uma ligação ao vivo com cada cliente através de sinais de batimento cardíaco.

No modo sem servidor, o cliente envia uma mensagem por http post, que não é tão eficiente como webSocket.

Outro fator é o protocolo: JSON e [MessagePack](https://msgpack.org/index.html). O MessagePack é menor em tamanho e é entregue mais rapidamente do que o JSON. No entanto, o MessagePack pode não melhorar o desempenho. O desempenho do Serviço Azure SignalR não é sensível aos protocolos porque não descodifica a carga útil da mensagem durante o encaminhamento de mensagens de clientes para servidores ou vice-versa.

Em resumo, os seguintes fatores afetam a capacidade de entrada e saída:

-   Nível SKU (CPU/memória)

-   Número de ligações

-   Tamanho da mensagem

-   Taxa de envio de mensagens

-   Tipo de transporte (WebSocket, Server-Sent-Event ou Long-Polling)

-   Cenário de utilização (custo de encaminhamento)

-   Servidor de aplicações e conexões de serviço (no modo servidor)


### <a name="finding-a-proper-sku"></a>Encontrar um SKU adequado

Como avaliar a capacidade de entrada/saída ou descobrir qual o nível adequado para uma caixa de utilização específica?

Assuma que o servidor de aplicações é suficientemente poderoso e não é o estrangulamento de desempenho. Em seguida, verifique a largura de banda máxima de entrada e saída para cada nível.

#### <a name="quick-evaluation"></a>Avaliação rápida

Vamos simplificar a avaliação primeiro assumindo algumas definições padrão: 

- O tipo de transporte é WebSocket.
- O tamanho da mensagem é 2.048 bytes.
- Uma mensagem é enviada a cada 1 segundo.
- O Serviço Azure SignalR está no modo predefinido.

Cada nível tem a sua largura de banda máxima de entrada e largura de banda de saída. Uma experiência suave do utilizador não é garantida após a ligação de entrada ou saída exceder o limite.

**O Eco** dá a largura de banda máxima de entrada porque tem o menor custo de encaminhamento. **A transmissão** define a largura de banda de mensagem máxima de saída.

*Não* exceda os valores destacados nas duas tabelas seguintes.

|       Eco                        | Unidade1 | Unidade2 | Unidade5 | Unidade10 | Unidade20 | Unidade50 | Unidade100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| Ligações                       | 1,000 | 2.000 | 5000 | 10,000 | 20 000 | 50 000 | 100.000 |
| **Largura de banda de entrada** | **2 MBps**    | **4 MBps**    | **10 MBps**   | **20 MBps**    | **40 MBps**    | **100 MBps**   | **200 MBps**    |
| Largura de banda de saída | 2 MBps   | 4 MBps   | 10 MBps  | 20 MBps   | 40 MBps   | 100 MBps  | 200 MBps   |


|     Difusão             | Unidade1 | Unidade2 | Unidade5  | Unidade10 | Unidade20 | Unidade50  | Unidade100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Ligações               | 1,000 | 2.000 | 5000  | 10,000 | 20 000 | 50 000  | 100.000 |
| Largura de banda de entrada  | 4 KBps   | 4 KBps   | 4 KBps    | 4 KBps    | 4 KBps    | 4 KBps     | 4 KBps    |
| **Largura de banda de saída** | **4 MBps**    | **8 MBps**    | **20 MBps**    | **40 MBps**    | **80 MBps**    | **200 MBps**    | **400 MBps**   |

*A largura de banda de entrada* e a largura de banda de *saída* são o tamanho total da mensagem por segundo.  Aqui estão as fórmulas para eles:
```
  inboundBandwidth = inboundConnections * messageSize / sendInterval
  outboundBandwidth = outboundConnections * messageSize / sendInterval
```

- *entradasConnections*: O número de ligações que enviam a mensagem.

- *outboundConnections*: O número de ligações que recebem a mensagem.

- *mensagemSize*: O tamanho de uma única mensagem (valor médio). Uma pequena mensagem com menos de 1.024 bytes tem um impacto de desempenho semelhante a uma mensagem de 1.024 byte.

- *sendInterval*: O tempo de envio de uma mensagem. Normalmente é 1 segundo por mensagem, o que significa enviar uma mensagem a cada segundo. Um intervalo menor significa enviar mais mensagem num período de tempo. Por exemplo, 0,5 segundos por mensagem significa enviar duas mensagens a cada segundo.

- *Ligações*: O limiar máximo comprometido para o Serviço Azure SignalR para cada nível. Se o número de ligação for aumentado ainda mais, sofrerá de estrangulamento de ligação.

#### <a name="evaluation-for-complex-use-cases"></a>Avaliação para casos de utilização complexa

##### <a name="bigger-message-size-or-different-sending-rate"></a>Tamanho de mensagem maior ou taxa de envio diferente

O caso do uso real é mais complicado. Pode enviar uma mensagem superior a 2.048 bytes, ou a taxa de envio de mensagens não é uma mensagem por segundo. Vejamos a transmissão da Unit100 como um exemplo para descobrir como avaliar o seu desempenho.

A tabela a seguir mostra um verdadeiro caso de utilização da **transmissão**. Mas o tamanho da mensagem, contagem de ligação e taxa de envio de mensagens são diferentes do que assumimos na secção anterior. A questão é como podemos deduzir qualquer um desses itens (tamanho da mensagem, contagem de ligações ou taxa de envio de mensagens) se soubermos apenas dois deles.

| Difusão  | Tamanho da mensagem | Mensagens de entrada por segundo | Ligações | Enviar intervalos |
|---|---------------------|--------------------------|-------------|-------------------------|
| 1 | 20 KB                | 1                        | 100.000     | 5 seg                      |
| 2 | 256 KB               | 1                        | 8,000       | 5 seg                      |

A seguinte fórmula é fácil de inferir com base na fórmula anterior:

```
outboundConnections = outboundBandwidth * sendInterval / messageSize
```

Para a Unidade100, a largura de banda máxima de saída é de 400 MB da tabela anterior. Para um tamanho de mensagem de 20 KB, as ligações de saída máxima devem ser de 400 MB \* 5 / 20 KB = 100.000, que corresponde ao valor real.

##### <a name="mixed-use-cases"></a>Casos de uso misto

O caso de uso real normalmente mistura os quatro casos básicos de utilização: **eco,** **transmissão,** **envio para grupo,** e **enviar para a conexão**. A metodologia que utiliza para avaliar a capacidade é:

1. Divida os casos de uso misto em quatro casos básicos de utilização.
1. Calcular a largura de banda de entrada e saída máxima utilizando separadamente as fórmulas anteriores.
1. Em suma, os cálculos de largura de banda para obter a largura de banda máxima de entrada/saída total. 

Em seguida, apanhe o nível adequado a partir das mesas de largura de banda máxima de entrada/saída.

> [!NOTE]
> Para o envio de uma mensagem a centenas ou milhares de pequenos grupos, ou para milhares de clientes que enviam uma mensagem uns aos outros, o custo do encaminhamento tornar-se-á dominante. Tome em consideração este impacto.

Para o caso de envio de uma mensagem aos clientes, certifique-se de que o servidor da aplicação *não* é o estrangulamento. A seguinte secção "Estudo de Caso" dá orientações sobre quantos servidores de aplicações precisa e quantas ligações de servidor deve configurar.

## <a name="case-study"></a>Estudo de caso

As seguintes secções passam por quatro casos típicos de utilização para o transporte WebSocket: **eco,** **transmissão,** **envio para grupo,** e **enviar para a ligação**. Para cada cenário, a secção lista a capacidade atual de entrada e saída do Serviço Azure SignalR. Também explica os principais fatores que afetam o desempenho.

No modo predefinido, o servidor de aplicações cria cinco ligações de servidor com o Serviço Azure SignalR. O servidor de aplicações utiliza o Serviço Azure SignalR SDK por padrão. Nos seguintes resultados dos testes de desempenho, as ligações do servidor são aumentadas para 15 (ou mais para transmitir e enviar uma mensagem para um grande grupo).

Casos de uso diferentes têm requisitos diferentes para servidores de aplicações. **A transmissão** precisa de um pequeno número de servidores de aplicações. **Eco** ou **enviar para a ligação** precisa de muitos servidores de aplicações.

Em todos os casos de utilização, o tamanho da mensagem padrão é de 2.048 bytes, e o intervalo de envio da mensagem é de 1 segundo.

### <a name="default-mode"></a>Modo predefinido

Clientes, servidores de aplicações web e Serviço Azure SignalR estão envolvidos no modo predefinido. Cada cliente significa uma única ligação.

#### <a name="echo"></a>Eco

Em primeiro lugar, uma aplicação web conecta-se ao Serviço Azure SignalR. Em segundo lugar, muitos clientes conectam-se à aplicação web, que redireciona os clientes para o Serviço Azure SignalR com o token de acesso e ponto final. Em seguida, os clientes estabelecem ligações WebSocket com o Serviço Azure SignalR.

Depois de todos os clientes estabelecerem ligações, começam a enviar uma mensagem que contém uma marca de tempo para o centro específico a cada segundo. O centro ecoa a mensagem de volta ao seu cliente original. Cada cliente calcula a latência quando recebe a mensagem de eco de volta.

No diagrama seguinte, 5 a 8 (tráfego em destaque vermelho) estão em loop. O loop é executado por uma duração predefinido (5 minutos) e obtém a estatística de toda a latência da mensagem.

![Tráfego para o caso de utilização do eco](./media/signalr-concept-performance/echo.png)

O comportamento do **eco** determina que a largura de banda máxima de entrada é igual à largura de banda máxima de saída. Para obter detalhes, consulte a seguinte tabela.

|       Eco                        | Unidade1 | Unidade2 | Unidade5 | Unidade10 | Unidade20 | Unidade50 | Unidade100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| Ligações                       | 1,000 | 2.000 | 5000 | 10,000 | 20 000 | 50 000 | 100.000 |
| Mensagens de entrada/saída por segundo | 1,000 | 2.000 | 5000 | 10,000 | 20 000 | 50 000 | 100.000 |
| Largura de banda de entrada/saída | 2 MBps   | 4 MBps   | 10 MBps  | 20 MBps   | 40 MBps   | 100 MBps  | 200 MBps   |

Neste caso de utilização, cada cliente invoca o hub definido no servidor de aplicações. O centro apenas chama o método definido no lado original do cliente. Este centro é o centro mais leve para **o eco.**

```
        public void Echo(IDictionary<string, object> data)
        {
            Clients.Client(Context.ConnectionId).SendAsync("RecordLatency", data);
        }
```

Mesmo para este simples hub, a pressão de tráfego no servidor da aplicação é proeminente à medida que a carga de entrada de **eco** aumenta. Esta pressão de tráfego requer muitos servidores de aplicações para grandes níveis SKU. A tabela que se segue lista a contagem do servidor de aplicações para cada nível.


|    Eco          | Unidade1 | Unidade2 | Unidade5 | Unidade10 | Unidade20 | Unidade50 | Unidade100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Ligações      | 1,000 | 2.000 | 5000 | 10,000 | 20 000 | 50 000 | 100.000 |
| Contagem de servidores de aplicativos | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> O número de ligação do cliente, o tamanho da mensagem, a taxa de envio de mensagens, o nível SKU e o CPU/memória do servidor da aplicação afetam o desempenho geral do **eco**.

#### <a name="broadcast"></a>Difusão

Para **transmissão**, quando a aplicação web recebe a mensagem, transmite para todos os clientes. Quanto mais clientes há para transmitir, mais tráfego de mensagens há para todos os clientes. Veja o seguinte diagrama.

![Tráfego para o caso de uso de transmissão](./media/signalr-concept-performance/broadcast.png)

Um pequeno número de clientes estão a transmitir. A largura de banda da mensagem de entrada é pequena, mas a largura de banda de saída é enorme. A largura de banda de saída aumenta à medida que a ligação do cliente ou a taxa de transmissão aumentam.

A tabela seguinte resume as ligações máximas do cliente, a contagem de mensagens de entrada/saída e a largura de banda.

|     Difusão             | Unidade1 | Unidade2 | Unidade5  | Unidade10 | Unidade20 | Unidade50  | Unidade100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Ligações               | 1,000 | 2.000 | 5000  | 10,000 | 20 000 | 50 000  | 100.000 |
| Mensagens de entrada por segundo  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Mensagens de saída por segundo | 2.000 | 4000 | 10,000 | 20 000 | 40.000 | 100.000 | 200,000 |
| Largura de banda de entrada  | 4 KBps   | 4 KBps   | 4 KBps    | 4 KBps    | 4 KBps    | 4 KBps     | 4 KBps     |
| Largura de banda de saída | 4 MBps   | 8 MBps   | 20 MBps   | 40 MBps   | 80 MBps   | 200 MBps   | 400 MBps   |

Os clientes de radiodifusão que postam mensagens não são mais do que quatro. Precisam de menos servidores de aplicações em comparação com o **eco** porque o valor da mensagem de entrada é pequeno. Dois servidores de aplicações são suficientes para considerações de SLA e desempenho. Mas deve aumentar as ligações do servidor predefinidos para evitar desequilíbrios, especialmente para a Unit50 e a Unit100.

|   Difusão      | Unidade1 | Unidade2 | Unidade5 | Unidade10 | Unidade20 | Unidade50 | Unidade100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Ligações      | 1,000 | 2.000 | 5000 | 10,000 | 20 000 | 50 000 | 100.000 |
| Contagem de servidores de aplicativos | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
> Aumente as ligações padrão do servidor de 5 para 40 em cada servidor de aplicações para evitar possíveis ligações desequilibradas do servidor ao Serviço Azure SignalR.
>
> O número de ligação do cliente, o tamanho da mensagem, a taxa de envio de mensagens e o nível SKU afetam o desempenho global da **transmissão**.

#### <a name="send-to-group"></a>Enviar para o grupo

O **caso de uso** de grupo tem um padrão de tráfego semelhante para **transmitir**. A diferença é que, após os clientes estabelecerem ligações WebSocket com o Serviço Azure SignalR, devem juntar-se a grupos antes de poderem enviar uma mensagem a um grupo específico. O diagrama seguinte ilustra o fluxo de tráfego.

![Tráfego para o caso de utilização enviado para grupo](./media/signalr-concept-performance/sendtogroup.png)

A contagem de membros do grupo e de grupo são dois fatores que afetam o desempenho. Para simplificar a análise, definimos dois tipos de grupos:

- **Grupo pequeno**: Cada grupo tem 10 ligações. O número do grupo é igual a (contagem máxima de ligação) / 10. Por exemplo, para a Unidade1, se houver 1.000 contagens de ligação, então temos 1000 / 10 = 100 grupos.

- **Grande grupo**: O número do grupo é sempre 10. A contagem de membros do grupo é igual a (contagem máxima de ligação) / 10. Por exemplo, para a Unidade1, se houver 1.000 contagens de ligação, então cada grupo tem 1000 / 10 = 100 membros.

**Enviar para o grupo** traz um custo de encaminhamento para o Serviço Azure SignalR porque tem que encontrar as ligações-alvo através de uma estrutura de dados distribuída. À medida que as ligações de envio aumentam, o custo aumenta.

##### <a name="small-group"></a>Pequeno grupo

O custo do encaminhamento é significativo para o envio de mensagens a muitos pequenos grupos. Atualmente, a implementação do Serviço Azure SignalR atinge o limite de custos de encaminhamento na Unidade50. Adicionar mais CPU e memória não ajuda, por isso a Unit100 não pode melhorar ainda mais com o design. Se precisar de mais largura de banda de entrada, contacte o suporte do cliente.

|   Enviar para um pequeno grupo     | Unidade1 | Unidade2 | Unidade5  | Unidade10 | Unidade20 | Unidade50 | Unidade100 |
|---------------------------|-------|-------|--------|--------|--------|--------|---------|
| Ligações               | 1,000 | 2.000 | 5000  | 10,000 | 20 000 | 50 000 | 100.000
| Contagem de membros do grupo        | 10    | 10    | 10     | 10     | 10     | 10     | 10 
| Contagem de grupos               | 100   | 200   | 500    | 1,000  | 2.000  | 5000  | 10,000 
| Mensagens de entrada por segundo  | 200   | 400   | 1,000  | 2.500  | 4000  | 7,000  | 7,000   |
| Largura de banda de entrada  | 400 KBps  | 800 KBps  | 2 MBps     | 5 MBps     | 8 MBps     | 14 MBps    | 14 MBps     |
| Mensagens de saída por segundo | 2.000 | 4000 | 10,000 | 25.000 | 40.000 | 70,000 | 70,000  |
| Largura de banda de saída | 4 MBps    | 8 MBps    | 20 MBps    | 50 MBps     | 80 MBps    | 140 MBps   | 140 MBps    |

Muitas ligações com o cliente estão a ligar para o hub, pelo que o número do servidor de aplicações também é fundamental para o desempenho. A tabela que se segue lista as contagens do servidor de aplicações sugerido.

|  Enviar para um pequeno grupo   | Unidade1 | Unidade2 | Unidade5 | Unidade10 | Unidade20 | Unidade50 | Unidade100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Ligações      | 1,000 | 2.000 | 5000 | 10,000 | 20 000 | 50 000 | 100.000 |
| Contagem de servidores de aplicativos | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> O número de ligação do cliente, o tamanho da mensagem, a taxa de envio de mensagens, o custo de encaminhamento, o nível SKU e a memória do servidor da aplicação afetam o desempenho geral do **envio para o pequeno grupo**.

##### <a name="big-group"></a>Grande grupo

Para **enviar para um grande grupo,** a largura de banda de saída torna-se o estrangulamento antes de atingir o limite de custo de encaminhamento. A tabela que se segue lista a largura de banda máxima de saída, que é quase a mesma da **emissão.**

|    Enviar para um grande grupo      | Unidade1 | Unidade2 | Unidade5  | Unidade10 | Unidade20 | Unidade50  | Unidade100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Ligações               | 1,000 | 2.000 | 5000  | 10,000 | 20 000 | 50 000  | 100.000
| Contagem de membros do grupo        | 100   | 200   | 500    | 1,000  | 2.000  | 5000   | 10,000 
| Contagem de grupos               | 10    | 10    | 10     | 10     | 10     | 10      | 10
| Mensagens de entrada por segundo  | 20    | 20    | 20     | 20     | 20     | 20      | 20      |
| Largura de banda de entrada  | 80 KBps   | 40 KBps   | 40 KBps    | 20 KBps    | 40 KBps    | 40 KBps     | 40 KBps     |
| Mensagens de saída por segundo | 2.000 | 4000 | 10,000 | 20 000 | 40.000 | 100.000 | 200,000 |
| Largura de banda de saída | 8 MBps    | 8 MBps    | 20 MBps    | 40 MBps    | 80 MBps    | 200 MBps    | 400 MBps    |

A contagem de ligação de envio não é superior a 40. O fardo no servidor de aplicações é pequeno, pelo que o número sugerido de aplicações web é pequeno.

|  Enviar para um grande grupo  | Unidade1 | Unidade2 | Unidade5 | Unidade10 | Unidade20 | Unidade50 | Unidade100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Ligações      | 1,000 | 2.000 | 5000 | 10,000 | 20 000 | 50 000 | 100.000 |
| Contagem de servidores de aplicativos | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
> Aumente as ligações padrão do servidor de 5 para 40 em cada servidor de aplicações para evitar possíveis ligações desequilibradas do servidor ao Serviço Azure SignalR.
> 
> O número de ligação do cliente, o tamanho da mensagem, a taxa de envio de mensagens, o custo de encaminhamento e o nível SKU afetam o desempenho global do **envio para o grande grupo.**

#### <a name="send-to-connection"></a>Enviar para a ligação

No caso de **envio para ligação,** quando os clientes estabelecem as ligações ao Serviço Azure SignalR, cada cliente liga para um hub especial para obter o seu próprio ID de ligação. O benchmark de desempenho recolhe todos os IDs de ligação, baralha-os e atribui-os a todos os clientes como alvo de envio. Os clientes continuam a enviar a mensagem para a ligação ao alvo até que o teste de desempenho termine.

![Tráfego para o caso de utilização de envio-a-cliente](./media/signalr-concept-performance/sendtoclient.png)

O custo de encaminhamento para **envio para a ligação** é semelhante ao custo **de envio para o pequeno grupo**.

À medida que a contagem de ligação aumenta, o custo de encaminhamento limita o desempenho global. A Unidade50 atingiu o limite. Como resultado, a Unidade100 não pode melhorar mais.

O quadro seguinte é um resumo estatístico após muitas rondas de execução do envio para referência **de ligação.**

|   Enviar para a ligação   | Unidade1 | Unidade2 | Unidade5 | Unidade10 | Unidade20 | Unidade50          | Unidade100         |
|------------------------------------|-------|-------|-------|--------|--------|-----------------|-----------------|
| Ligações                        | 1,000 | 2.000 | 5000 | 10,000 | 20 000 | 50 000          | 100.000         |
| Mensagens de entrada/saída por segundo | 1,000 | 2.000 | 5000 | 8,000  | 9000  | 20 000 | 20 000 |
| Largura de banda de entrada/saída | 2 MBps    | 4 MBps    | 10 MBps   | 16 MBps    | 18 MBps    | 40 MBps       | 40 MBps       |

Este caso de utilização requer uma carga elevada no lado do servidor da aplicação. Consulte a contagem de servidores de aplicações sugeridas na tabela seguinte.

|  Enviar para a ligação  | Unidade1 | Unidade2 | Unidade5 | Unidade10 | Unidade20 | Unidade50 | Unidade100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Ligações      | 1,000 | 2.000 | 5000 | 10,000 | 20 000 | 50 000 | 100.000 |
| Contagem de servidores de aplicativos | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> O número de ligação do cliente, o tamanho da mensagem, a taxa de envio de mensagens, o custo de encaminhamento, o nível SKU e o CPU/memória para o servidor da aplicação afetam o desempenho geral do **envio para a ligação**.

#### <a name="aspnet-signalr-echo-broadcast-and-send-to-small-group"></a>ASP.NET SignalR eco, emissão e envio para pequeno grupo

O Serviço Azure SignalR fornece a mesma capacidade de desempenho para ASP.NET SignalR. 

O teste de desempenho utiliza aplicações web Azure do Plano de [Serviço Padrão S3](https://azure.microsoft.com/pricing/details/app-service/windows/) para ASP.NET SignalR.

A tabela seguinte dá a contagem de aplicações web sugeridas para ASP.NET **eco**SignalR .

|   Eco           | Unidade1 | Unidade2 | Unidade5 | Unidade10 | Unidade20 | Unidade50 | Unidade100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Ligações      | 1,000 | 2.000 | 5000 | 10,000 | 20 000 | 50 000 | 100.000 |
| Contagem de servidores de aplicativos | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

A tabela seguinte dá a contagem de aplicações web sugeridas para ASP.NET **transmissão**SignalR .

|  Difusão       | Unidade1 | Unidade2 | Unidade5 | Unidade10 | Unidade20 | Unidade50 | Unidade100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Ligações      | 1,000 | 2.000 | 5000 | 10,000 | 20 000 | 50 000 | 100.000 |
| Contagem de servidores de aplicativos | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

A tabela seguinte dá a contagem de aplicações web sugeridas para ASP.NET SignalR **enviar para o pequeno grupo**.

|  Enviar para um pequeno grupo     | Unidade1 | Unidade2 | Unidade5 | Unidade10 | Unidade20 | Unidade50 | Unidade100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Ligações      | 1,000 | 2.000 | 5000 | 10,000 | 20 000 | 50 000 | 100.000 |
| Contagem de servidores de aplicativos | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

### <a name="serverless-mode"></a>Modo sem servidor

Os clientes e o Serviço Azure SignalR estão envolvidos no modo sem servidor. Cada cliente significa uma única ligação. O cliente envia mensagens através da API REST a outro cliente ou mensagens de transmissão para todos.

O envio de mensagens de alta densidade através da API REST não é tão eficiente como a utilização do WebSocket. Requer que construa sempre uma nova ligação HTTP, e isso é um custo extra no modo sem servidor.

#### <a name="broadcast-through-rest-api"></a>Transmissão através da REST API
Todos os clientes estabelecem ligações WebSocket com o Serviço Azure SignalR. Em seguida, alguns clientes começam a transmitir através da API REST. A mensagem que envia (entrada) é toda através do HTTP Post, o que não é eficiente em comparação com o WebSocket.

|   Transmissão através da REST API     | Unidade1 | Unidade2 | Unidade5  | Unidade10 | Unidade20 | Unidade50  | Unidade100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Ligações               | 1,000 | 2.000 | 5000  | 10,000 | 20 000 | 50 000  | 100.000 |
| Mensagens de entrada por segundo  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Mensagens de saída por segundo | 2.000 | 4000 | 10,000 | 20 000 | 40.000 | 100.000 | 200,000 |
| Largura de banda de entrada  | 4 KBps    | 4 KBps    | 4 KBps     | 4 KBps     | 4 KBps     | 4 KBps      | 4 KBps      |
| Largura de banda de saída | 4 MBps    | 8 MBps    | 20 MBps    | 40 MBps    | 80 MBps    | 200 MBps    | 400 MBps    |

#### <a name="send-to-user-through-rest-api"></a>Enviar ao utilizador através da REST API
O benchmark atribui nomes de utilizador a todos os clientes antes de começarem a ligar-se ao Serviço Azure SignalR. Depois de os clientes estabelecerem ligações WebSocket com o Serviço Azure SignalR, começam a enviar mensagens a outros através do HTTP Post.

|   Enviar ao utilizador através da REST API | Unidade1 | Unidade2 | Unidade5  | Unidade10 | Unidade20 | Unidade50  | Unidade100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Ligações               | 1,000 | 2.000 | 5000  | 10,000 | 20 000 | 50 000  | 100.000 |
| Mensagens de entrada por segundo  | 300   | 600   | 900    | 1,300  | 2.000  | 10,000  | 18 000  |
| Mensagens de saída por segundo | 300   | 600   | 900    | 1,300  | 2.000  | 10,000  | 18 000 |
| Largura de banda de entrada  | 600 KBps  | 1,2 MBps  | 1,8 MBps   | 2,6 MBps   | 4 MBps     | 10 MBps     | 36 MBps    |
| Largura de banda de saída | 600 KBps  | 1,2 MBps  | 1,8 MBps   | 2,6 MBps   | 4 MBps     | 10 MBps     | 36 MBps    |

## <a name="performance-test-environments"></a>Ambientes de teste de desempenho

Para todos os casos de uso listados anteriormente, realizamos os testes de desempenho em um ambiente Azure. No máximo, usamos 50 VMs de clientes e 20 VMs de servidor de aplicações. Aqui estão alguns detalhes:

- Tamanho VM do cliente: StandardDS2V2 (2 vCPU, memória 7G)

- Tamanho do servidor de aplicações VM: StandardF4sV2 (4 vCPU, memória 8G)

- Ligações ao servidor Azure SignalR SDK: 15

## <a name="performance-tools"></a>Ferramentas de desempenho

Pode encontrar ferramentas de desempenho para o Serviço Azure SignalR no [GitHub](https://github.com/Azure/azure-signalr-bench/).

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você tem uma visão geral do desempenho do Serviço Azure SignalR em cenários típicos de caso de uso.

Para obter detalhes sobre os internos do serviço e escalonamento para o mesmo, leia os seguintes guias:

* [Detalhes internos do Serviço SignalR do Azure](signalr-concept-internals.md)
* [Escala do serviço Azure SignalR](signalr-howto-scale-multi-instances.md)

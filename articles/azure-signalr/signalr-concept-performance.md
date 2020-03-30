---
title: Guia de desempenho do Azure SignalR Service
description: Uma visão geral do desempenho e referência do Serviço De Sinalização Azure. Métricas-chave a considerar ao planear a capacidade.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 68cad32be177fa20794399157fca89e87c2f8f59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74157660"
---
# <a name="performance-guide-for-azure-signalr-service"></a>Guia de desempenho do Azure SignalR Service

Um dos principais benefícios da utilização do Serviço De Sinalização Azure é a facilidade de dimensionamento de aplicações SignalR. Num cenário de grande escala, o desempenho é um fator importante. 

Neste guia, vamos introduzir os fatores que afetam o desempenho da aplicação SignalR. Descreveremos o desempenho típico em diferentes cenários de uso. No final, vamos introduzir o ambiente e as ferramentas que você pode usar para gerar um relatório de desempenho.

## <a name="term-definitions"></a>Definições de prazo

*Entrada*: A mensagem de entrada para o Serviço De Sinalização Azure.

*Saída*: A mensagem de saída do Serviço De Sinalização Azure.

*Largura de banda*: O tamanho total de todas as mensagens em 1 segundo.

*Modo predefinido*: O modo de funcionamento predefinido quando foi criada uma instância de Serviço De Sinalizador Azure. O Serviço De Sinalização Azure espera que o servidor de aplicações estabeleça uma ligação com o mesmo antes de aceitar quaisquer ligações ao cliente.

*Modo sem servidores*: Um modo em que o Serviço De Sinalização Azure aceita apenas ligações ao cliente. Não é permitida nenhuma ligação ao servidor.

## <a name="overview"></a>Descrição geral

O Serviço De Sinalização Azure define sete níveis Standard para diferentes capacidades de desempenho. Este guia responde às seguintes perguntas:

-   Qual é o desempenho típico do Serviço De Sinalização Azure para cada nível?

-   O Serviço De Sinalização Azure cumpre os meus requisitos de entrada de mensagens (por exemplo, envio de 100.000 mensagens por segundo)?

-   Para o meu cenário específico, qual o nível adequado para mim? Ou como posso selecionar o nível adequado?

-   Que tipo de servidor de aplicativos (tamanho VM) é adequado para mim? Quantos deles devo destacar?

Para responder a estas perguntas, este guia dá primeiro uma explicação de alto nível sobre os fatores que afetam o desempenho. Em seguida, ilustra as mensagens máximas de entrada e saída para cada nível para casos de utilização típica: **eco,** **transmissão,** **envio para grupo,** e **enviar para a ligação** (conversação entre pares).

Este guia não pode cobrir todos os cenários (e diferentes casos de uso, tamanhos de mensagens, padrões de envio de mensagens, e assim por diante). Mas fornece alguns métodos para ajudá-lo:

- Avalie o seu requisito aproximado para as mensagens de entrada ou saída.
- Encontre as camadas adequadas verificando a tabela de desempenho.

## <a name="performance-insight"></a>Insight de desempenho

Esta secção descreve as metodologias de avaliação de desempenho e, em seguida, enumera todos os fatores que afetam o desempenho. No final, fornece métodos para ajudá-lo a avaliar os requisitos de desempenho.

### <a name="methodology"></a>Metodologia

*A entrada* e *a latência* são dois aspetos típicos da verificação de desempenho. Para o Serviço De Sinalização Azure, cada nível SKU tem a sua própria política de aceleração de entrada. A política define *o máximo de entrada permitido (largura* de banda de entrada e saída) como o máximo alcançado quando 99% das mensagens têm latência que é menos de 1 segundo.

A latência é o tempo de tempo da ligação que envia a mensagem para receber a mensagem de resposta do Serviço De Sinalização Azure. Vejamos o **eco** como exemplo. Todas as ligações com clientes adicionam um carimbo de tempo na mensagem. O centro do servidor de aplicações envia a mensagem original de volta ao cliente. Assim, o atraso de propagação é facilmente calculado por todas as ligações do cliente. O carimbo de tempo é fixado para cada mensagem **em transmissão,** **enviar para grupo,** e **enviar para a ligação**.

Para simular milhares de ligações de clientes simultâneos, vários VMs são criados numa rede privada virtual em Azure. Todos estes VMs ligam-se à mesma instância do Serviço de Sinalização Azure.

No modo padrão do Serviço De Sinalização Azure, os VMs do servidor de aplicações são implantados na mesma rede privada virtual que os VMs dos clientes. Todos os VMs de clientes e VMs de servidores de aplicações são implantados na mesma rede da mesma região para evitar a latência transversal.

### <a name="performance-factors"></a>Fatores de desempenho

Teoricamente, a capacidade do Serviço De Sinalização Azure é limitada por recursos de computação: CPU, memória e rede. Por exemplo, mais ligações ao Serviço De Sinalização Azure fazem com que o serviço utilize mais memória. Para um maior tráfego de mensagens (por exemplo, cada mensagem é maior do que 2.048 bytes), o Serviço de Sinalização Azure precisa de gastar mais ciclos de CPU para processar o tráfego. Entretanto, a largura de banda da rede Azure também impõe um limite para o tráfego máximo.

O tipo de transporte é outro fator que afeta o desempenho. Os três tipos são [WebSocket,](https://en.wikipedia.org/wiki/WebSocket) [Server-Sent-Event](https://en.wikipedia.org/wiki/Server-sent_events)e [Long-Polling](https://en.wikipedia.org/wiki/Push_technology). 

WebSocket é um protocolo de comunicação bidirecional e full-duplex sobre uma única ligação TCP. Server-Sent-Event é um protocolo unidirecional para empurrar mensagens de servidor para cliente. A Long-Polling exige que os clientes sondarperiodmente informações do servidor através de um pedido HTTP. Para a mesma API nas mesmas condições, o WebSocket tem o melhor desempenho, server-Sent-Event é mais lento, e long-polling é o mais lento. O Serviço de Sinalização Azure recomenda webSocket por defeito.

O custo de encaminhamento de mensagens também limita o desempenho. O Serviço De Sinalização Azure desempenha um papel como router de mensagens, que encaminha a mensagem de um conjunto de clientes ou servidores para outros clientes ou servidores. Um cenário diferente ou API requer uma política de encaminhamento diferente. 

Para **eco,** o cliente envia uma mensagem para si mesmo, e o destino de encaminhamento também é ele mesmo. Este padrão tem o menor custo de encaminhamento. Mas para **transmissão**, **enviar para grupo**, e enviar para a **ligação**, o Serviço De Sinalização Azure precisa de procurar as ligações-alvo através da estrutura de dados distribuída internamente. Este processamento extra utiliza mais CPU, memória e largura de banda da rede. Como resultado, o desempenho é mais lento.

No modo predefinido, o servidor de aplicações também pode tornar-se um estrangulamento para determinados cenários. O Azure SignalR SDK tem de invocar o hub, mantendo uma ligação ao vivo com todos os clientes através de sinais de batimentocardíaco.

No modo sem servidor, o cliente envia uma mensagem por post HTTP, que não é tão eficiente como o WebSocket.

Outro fator é o protocolo: JSON e [MessagePack](https://msgpack.org/index.html). O MessagePack é menor em tamanho e é entregue mais rapidamente que o JSON. No entanto, o MessagePack pode não melhorar o desempenho. O desempenho do Serviço De Sinalização Azure não é sensível aos protocolos porque não descodifica a carga útil da mensagem durante o envio de mensagens de clientes para servidores ou vice-versa.

Em resumo, os seguintes fatores afetam a capacidade de entrada e saída:

-   Nível SKU (CPU/memória)

-   Número de ligações

-   Tamanho da mensagem

-   Taxa de envio de mensagem

-   Tipo de transporte (WebSocket, Server-Sent-Event ou Long-Polling)

-   Cenário de caso de utilização (custo de encaminhamento)

-   Servidor de aplicativos e ligações de serviço (no modo servidor)


### <a name="finding-a-proper-sku"></a>Encontrar um SKU adequado

Como pode avaliar a capacidade de entrada/saída ou descobrir qual o nível adequado para uma caixa de utilização específica?

Assuma que o servidor de aplicações é poderoso o suficiente e não é o estrangulamento de desempenho. Em seguida, verifique a largura de banda máxima de entrada e saída para cada nível.

#### <a name="quick-evaluation"></a>Avaliação rápida

Vamos simplificar a avaliação primeiro assumindo algumas definições predefinidas: 

- O tipo de transporte é WebSocket.
- O tamanho da mensagem é de 2.048 bytes.
- Uma mensagem é enviada a cada 1 segundo.
- O Serviço De Sinalização Azure encontra-se no modo predefinido.

Cada nível tem a sua largura de banda máxima de entrada e largura de banda de saída. Uma experiência suave do utilizador não é garantida após a ligação de entrada ou saída exceder o limite.

**O Echo** dá a largura de banda máxima de entrada porque tem o menor custo de encaminhamento. **A transmissão** define a largura máxima de banda da mensagem de saída.

Não *not* exceda os valores realçados nas duas tabelas seguintes.

|       Eco                        | Unidade 1 | Unidade 2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| Ligações                       | 1,000 | 2.000 | 5000 | 10,000 | 20 000 | 50 000 | 100 000 |
| **Largura de banda de entrada** | **2 MBps**    | **4 MBps**    | **10 MBps**   | **20 MBps**    | **40 MBps**    | **100 MBps**   | **200 MBps**    |
| Largura de banda de saída | 2 MBps   | 4 MBps   | 10 MBps  | 20 MBps   | 40 MBps   | 100 MBps  | 200 MBps   |


|     Difusão             | Unidade 1 | Unidade 2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Ligações               | 1,000 | 2.000 | 5000  | 10,000 | 20 000 | 50 000  | 100 000 |
| Largura de banda de entrada  | 4 KBps   | 4 KBps   | 4 KBps    | 4 KBps    | 4 KBps    | 4 KBps     | 4 KBps    |
| **Largura de banda de saída** | **4 MBps**    | **8 MBps**    | **20 MBps**    | **40 MBps**    | **80 MBps**    | **200 MBps**    | **400 MBps**   |

*A largura de banda de entrada* e a largura de banda de *saída* são o tamanho total da mensagem por segundo.  Aqui estão as fórmulas para eles:
```
  inboundBandwidth = inboundConnections * messageSize / sendInterval
  outboundBandwidth = outboundConnections * messageSize / sendInterval
```

- *ligações de entrada*: O número de ligações que enviam a mensagem.

- *outboundConnections*: O número de ligações que recebem a mensagem.

- *mensagemTamanho*: O tamanho de uma única mensagem (valor médio). Uma pequena mensagem que é menos de 1.024 bytes tem um impacto de desempenho semelhante a uma mensagem de 1.024 bytes.

- *envioIntervalo*: O tempo de envio de uma mensagem. Normalmente é 1 segundo por mensagem, o que significa enviar uma mensagem a cada segundo. Um intervalo menor significa enviar mais mensagem num período de tempo. Por exemplo, 0,5 segundos por mensagem significa enviar duas mensagens a cada segundo.

- *Ligações*: O limiar máximo comprometido para o Serviço de Sinalização Azure para cada nível. Se o número de ligação for aumentado ainda mais, sofrerá de estrangulamento de ligação.

#### <a name="evaluation-for-complex-use-cases"></a>Avaliação para casos de uso complexo

##### <a name="bigger-message-size-or-different-sending-rate"></a>Maior tamanho de mensagem ou taxa de envio diferente

O caso de uso real é mais complicado. Pode enviar uma mensagem superior a 2.048 bytes, ou a taxa de envio de mensagens não é uma mensagem por segundo. Vamos levar a transmissão da Unit100 como um exemplo para descobrir como avaliar o seu desempenho.

A tabela seguinte mostra um caso de **transmissão**real. Mas o tamanho da mensagem, a contagem de ligação e a taxa de envio de mensagens são diferentes do que assumimos na secção anterior. A questão é como podemos deduzir qualquer um desses itens (tamanho de mensagem, contagem de ligação ou taxa de envio de mensagens) se conhecemos apenas dois deles.

| Difusão  | Tamanho da mensagem | Mensagens de entrada por segundo | Ligações | Enviar intervalos |
|---|---------------------|--------------------------|-------------|-------------------------|
| 1 | 20 KB                | 1                        | 100 000     | 5 seg                      |
| 2 | 256 KB               | 1                        | 8,000       | 5 seg                      |

A fórmula que se segue é fácil de inferir com base na fórmula anterior:

```
outboundConnections = outboundBandwidth * sendInterval / messageSize
```

Para a Unit100, a largura de banda máxima de saída é de 400 MB da tabela anterior. Para um tamanho de mensagem de 20 KB, as ligações máximas de saída devem ser de 400 MB \* 5 / 20 KB = 100.000, o que corresponde ao valor real.

##### <a name="mixed-use-cases"></a>Casos de uso misto

O caso de utilização real mistura tipicamente os quatro casos de utilização básica: **eco,** **transmissão,** **envio para grupo,** e **envio para ligação**. A metodologia que utiliza para avaliar a capacidade é:

1. Divida os casos de utilização mista em quatro casos básicos de utilização.
1. Calcular a largura de banda máxima de entrada e saída da mensagem utilizando separadamente as fórmulas anteriores.
1. Soma os cálculos de largura de banda para obter a largura de banda máxima total de entrada/saída. 

Em seguida, retire o nível adequado a partir das tabelas máximas de largura de banda de entrada/saída.

> [!NOTE]
> Para enviar uma mensagem a centenas ou milhares de pequenos grupos, ou para milhares de clientes que enviam uma mensagem uns aos outros, o custo de encaminhamento tornar-se-á dominante. Tenha em conta este impacto.

Para o caso de envio de uma mensagem aos clientes, certifique-se de que o servidor da aplicação *não* é o estrangulamento. A seguinte secção "Case study" dá diretrizes sobre quantos servidores de aplicações precisa e quantas ligações de servidor deve configurar.

## <a name="case-study"></a>Estudo de caso

As seguintes secções passam por quatro casos de utilização típicos para o transporte WebSocket: **eco,** **transmissão,** **envio para grupo,** e **enviar para a ligação**. Para cada cenário, a secção lista a capacidade atual de entrada e saída para o Serviço De Sinalização Azure. Também explica os principais fatores que afetam o desempenho.

No modo predefinido, o servidor de aplicações cria cinco ligações de servidor com o Serviço De Sinalizador Azure. O servidor de aplicações utiliza o Serviço De Sinalização Azure SDK por padrão. Nos seguintes resultados dos testes de desempenho, as ligações do servidor são aumentadas para 15 (ou mais para transmitir e enviar uma mensagem para um grande grupo).

Casos de uso diferentes têm requisitos diferentes para servidores de aplicações. **A transmissão** precisa de um pequeno número de servidores de aplicações. **O Eco** ou **o envio para a ligação** precisam de muitos servidores de aplicações.

Em todos os casos de utilização, o tamanho da mensagem padrão é de 2.048 bytes, e o intervalo de envio da mensagem é de 1 segundo.

### <a name="default-mode"></a>Modo padrão

Clientes, servidores de aplicações web e Serviço De Sinalização Azure estão envolvidos no modo padrão. Cada cliente representa uma única ligação.

#### <a name="echo"></a>Eco

Primeiro, uma aplicação web liga-se ao Serviço De Sinalização Azure. Em segundo lugar, muitos clientes conectam-se à aplicação web, que redireciona os clientes para o Serviço De Sinalização Azure com o token de acesso e ponto final. Em seguida, os clientes estabelecem ligações WebSocket com o Serviço De Sinalização Azure.

Depois de todos os clientes estabelecerem ligações, começam a enviar uma mensagem que contém um carimbo de tempo para o centro específico a cada segundo. O centro faz eco da mensagem para o seu cliente original. Cada cliente calcula a latência quando recebe a mensagem de eco de volta.

No diagrama seguinte, 5 a 8 (tráfego vermelho destacado) estão em loop. O loop funciona durante uma duração predefinida (5 minutos) e obtém a estatística de toda a latência da mensagem.

![Tráfego para o caso de uso de eco](./media/signalr-concept-performance/echo.png)

O comportamento do **eco** determina que a largura de banda máxima de entrada é igual à largura de banda máxima de saída. Para obter detalhes, consulte a seguinte tabela.

|       Eco                        | Unidade 1 | Unidade 2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| Ligações                       | 1,000 | 2.000 | 5000 | 10,000 | 20 000 | 50 000 | 100 000 |
| Mensagens de entrada/saída por segundo | 1,000 | 2.000 | 5000 | 10,000 | 20 000 | 50 000 | 100 000 |
| Largura de banda de entrada/saída | 2 MBps   | 4 MBps   | 10 MBps  | 20 MBps   | 40 MBps   | 100 MBps  | 200 MBps   |

Neste caso de utilização, cada cliente invoca o hub definido no servidor da aplicação. O centro apenas chama o método definido no lado original do cliente. Este centro é o centro mais leve para **o eco.**

```
        public void Echo(IDictionary<string, object> data)
        {
            Clients.Client(Context.ConnectionId).SendAsync("RecordLatency", data);
        }
```

Mesmo para este simples hub, a pressão de tráfego no servidor da aplicação é proeminente à medida que **a** carga de mensagem de entrada eco aumenta. Esta pressão de tráfego requer muitos servidores de aplicações para grandes níveis SKU. A tabela seguinte lista a contagem do servidor da aplicação para cada nível.


|    Eco          | Unidade 1 | Unidade 2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Ligações      | 1,000 | 2.000 | 5000 | 10,000 | 20 000 | 50 000 | 100 000 |
| Contagem de servidores de aplicativos | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> O número de ligação do cliente, o tamanho da mensagem, a taxa de envio de mensagens, o nível SKU e o CPU/memória do servidor da aplicação afetam o desempenho global do **eco**.

#### <a name="broadcast"></a>Difusão

Para **transmissão**, quando a aplicação da web recebe a mensagem, transmite a todos os clientes. Quanto mais clientes houver para transmitir, mais tráfego de mensagens há para todos os clientes. Veja o seguinte diagrama.

![Tráfego para o caso de uso de transmissão](./media/signalr-concept-performance/broadcast.png)

Um pequeno número de clientes está a transmitir. A largura de banda da mensagem de entrada é pequena, mas a largura de banda de saída é enorme. A largura de banda da mensagem de saída aumenta à medida que a ligação do cliente ou a taxa de transmissão aumentam.

A tabela que se segue resume as ligações máximas do cliente, a contagem de mensagens de entrada/saída e a largura de banda.

|     Difusão             | Unidade 1 | Unidade 2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Ligações               | 1,000 | 2.000 | 5000  | 10,000 | 20 000 | 50 000  | 100 000 |
| Mensagens de entrada por segundo  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Mensagens de saída por segundo | 2.000 | 4000 | 10,000 | 20 000 | 40.000 | 100 000 | 200,000 |
| Largura de banda de entrada  | 4 KBps   | 4 KBps   | 4 KBps    | 4 KBps    | 4 KBps    | 4 KBps     | 4 KBps     |
| Largura de banda de saída | 4 MBps   | 8 MBps   | 20 MBps   | 40 MBps   | 80 MBps   | 200 MBps   | 400 MBps   |

Os clientes de radiodifusão que publicam mensagens não são mais do que quatro. Precisam de menos servidores de aplicações em comparação com o **eco** porque o valor da mensagem de entrada é pequeno. Dois servidores de aplicações são suficientes tanto para considerações de SLA como de desempenho. Mas deve aumentar as ligações padrão do servidor para evitar desequilíbrios, especialmente para unit50 e Unit100.

|   Difusão      | Unidade 1 | Unidade 2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Ligações      | 1,000 | 2.000 | 5000 | 10,000 | 20 000 | 50 000 | 100 000 |
| Contagem de servidores de aplicativos | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
> Aumente as ligações padrão do servidor de 5 a 40 em cada servidor de aplicações para evitar possíveis ligações desequilibradas do servidor ao Serviço De sinalização Azure.
>
> O número de ligação do cliente, o tamanho da mensagem, a taxa de envio de mensagens e o nível SKU afetam o desempenho global da **transmissão**.

#### <a name="send-to-group"></a>Enviar para o grupo

O **caso de uso** de grupo tem um padrão de tráfego semelhante para **transmitir**. A diferença é que, depois de os clientes estabelecerem ligações WebSocket com o Serviço De Sinalização Azure, devem juntar-se a grupos antes de poderem enviar uma mensagem a um grupo específico. O diagrama que se segue ilustra o fluxo de tráfego.

![Tráfego para o caso de uso de envio para grupo](./media/signalr-concept-performance/sendtogroup.png)

Membros do grupo e contagem de grupos são dois fatores que afetam o desempenho. Para simplificar a análise, definimos dois tipos de grupos:

- **Pequeno grupo**: Cada grupo tem 10 ligações. O número do grupo é igual a (contagem de ligação máxima) / 10. Por exemplo, para a Unit1, se houver 1.000 contagens de ligação, então temos 1000 / 10 = 100 grupos.

- **Grande grupo**: O número do grupo é sempre 10. A contagem de membros do grupo é igual a (contagem de ligação máxima) / 10. Por exemplo, para a Unit1, se houver 1.000 contagens de ligação, então cada grupo tem 1000 / 10 = 100 membros.

**O envio para o grupo** traz um custo de encaminhamento para o Serviço De Sinalização Azure porque tem de encontrar as ligações-alvo através de uma estrutura de dados distribuída. À medida que as ligações de envio aumentam, o custo aumenta.

##### <a name="small-group"></a>Pequeno grupo

O custo de encaminhamento é significativo para o envio de mensagens a muitos pequenos grupos. Atualmente, a implementação do Serviço De Sinalização Azure atinge o limite de custos de encaminhamento na Unit50. Adicionar mais CPU e memória não ajuda, por isso a Unit100 não pode melhorar ainda mais pelo design. Se precisar de mais largura de banda de entrada, contacte o apoio ao cliente.

|   Enviar para pequeno grupo     | Unidade 1 | Unidade 2 | Unit5  | Unit10 | Unit20 | Unit50 | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|--------|---------|
| Ligações               | 1,000 | 2.000 | 5000  | 10,000 | 20 000 | 50 000 | 100 000
| Contagem de membros do grupo        | 10    | 10    | 10     | 10     | 10     | 10     | 10 
| Contagem de grupos               | 100   | 200   | 500    | 1,000  | 2.000  | 5000  | 10,000 
| Mensagens de entrada por segundo  | 200   | 400   | 1,000  | 2.500  | 4000  | 7,000  | 7,000   |
| Largura de banda de entrada  | 400 KBps  | 800 KBps  | 2 MBps     | 5 MBps     | 8 MBps     | 14 MBps    | 14 MBps     |
| Mensagens de saída por segundo | 2.000 | 4000 | 10,000 | 25.000 | 40.000 | 70,000 | 70,000  |
| Largura de banda de saída | 4 MBps    | 8 MBps    | 20 MBps    | 50 MBps     | 80 MBps    | 140 MBps   | 140 MBps    |

Muitas ligações com clientes estão a chamar o hub, por isso o número do servidor da aplicação também é fundamental para o desempenho. A tabela seguinte lista as contagens de servidor de aplicações sugeridas.

|  Enviar para pequeno grupo   | Unidade 1 | Unidade 2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Ligações      | 1,000 | 2.000 | 5000 | 10,000 | 20 000 | 50 000 | 100 000 |
| Contagem de servidores de aplicativos | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> O número de ligação do cliente, tamanho da mensagem, taxa de envio de mensagens, custo de encaminhamento, nível SKU e CPU/memória do servidor da aplicação afetam o desempenho global do **envio para um pequeno grupo**.

##### <a name="big-group"></a>Grande grupo

Para **enviar para um grande grupo**, a largura de banda de saída torna-se o estrangulamento antes de atingir o limite de custo de encaminhamento. A tabela seguinte enumera a largura de banda máxima de saída, que é quase a mesma para **a transmissão**.

|    Enviar para um grande grupo      | Unidade 1 | Unidade 2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Ligações               | 1,000 | 2.000 | 5000  | 10,000 | 20 000 | 50 000  | 100 000
| Contagem de membros do grupo        | 100   | 200   | 500    | 1,000  | 2.000  | 5000   | 10,000 
| Contagem de grupos               | 10    | 10    | 10     | 10     | 10     | 10      | 10
| Mensagens de entrada por segundo  | 20    | 20    | 20     | 20     | 20     | 20      | 20      |
| Largura de banda de entrada  | 80 KBps   | 40 KBps   | 40 KBps    | 20 KBps    | 40 KBps    | 40 KBps     | 40 KBps     |
| Mensagens de saída por segundo | 2.000 | 4000 | 10,000 | 20 000 | 40.000 | 100 000 | 200,000 |
| Largura de banda de saída | 8 MBps    | 8 MBps    | 20 MBps    | 40 MBps    | 80 MBps    | 200 MBps    | 400 MBps    |

A contagem de ligações não é superior a 40. O fardo no servidor de aplicações é pequeno, pelo que o número sugerido de aplicações web é pequeno.

|  Enviar para um grande grupo  | Unidade 1 | Unidade 2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Ligações      | 1,000 | 2.000 | 5000 | 10,000 | 20 000 | 50 000 | 100 000 |
| Contagem de servidores de aplicativos | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
> Aumente as ligações padrão do servidor de 5 a 40 em cada servidor de aplicações para evitar possíveis ligações desequilibradas do servidor ao Serviço De sinalização Azure.
> 
> O número de ligação do cliente, o tamanho da mensagem, a taxa de envio de mensagens, o custo de encaminhamento e o nível SKU afetam o desempenho global do **envio para um grande grupo**.

#### <a name="send-to-connection"></a>Enviar para a ligação

No caso de uso de **ligação,** quando os clientes estabelecem as ligações ao Serviço De Sinalização Azure, cada cliente chama um hub especial para obter o seu próprio ID de ligação. O benchmark de desempenho recolhe todas as iDs de ligação, baralha-as e reatribui-as a todos os clientes como alvo de envio. Os clientes continuam a enviar a mensagem para a ligação alvo até que o teste de desempenho termine.

![Tráfego para o caso de uso de envio para cliente](./media/signalr-concept-performance/sendtoclient.png)

O custo de encaminhamento para **o envio para a ligação** é semelhante ao custo de envio para um pequeno **grupo**.

À medida que a contagem de ligação aumenta, o custo de encaminhamento limita o desempenho global. A Unit50 atingiu o limite. Como resultado, a Unit100 não pode melhorar ainda mais.

A tabela seguinte é um resumo estatístico após muitas rondas de execução do envio para referência **de ligação.**

|   Enviar para a ligação   | Unidade 1 | Unidade 2 | Unit5 | Unit10 | Unit20 | Unit50          | Unit100         |
|------------------------------------|-------|-------|-------|--------|--------|-----------------|-----------------|
| Ligações                        | 1,000 | 2.000 | 5000 | 10,000 | 20 000 | 50 000          | 100 000         |
| Mensagens de entrada/saída por segundo | 1,000 | 2.000 | 5000 | 8,000  | 9000  | 20 000 | 20 000 |
| Largura de banda de entrada/saída | 2 MBps    | 4 MBps    | 10 MBps   | 16 MBps    | 18 MBps    | 40 MBps       | 40 MBps       |

Esta caixa de utilização requer alta carga no lado do servidor da aplicação. Consulte a contagem de servidores de aplicações sugerida na tabela seguinte.

|  Enviar para a ligação  | Unidade 1 | Unidade 2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Ligações      | 1,000 | 2.000 | 5000 | 10,000 | 20 000 | 50 000 | 100 000 |
| Contagem de servidores de aplicativos | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> O número de ligação do cliente, tamanho da mensagem, taxa de envio de mensagens, custo de encaminhamento, nível SKU e CPU/memória para o servidor da aplicação afetam o desempenho global do **envio para a ligação**.

#### <a name="aspnet-signalr-echo-broadcast-and-send-to-small-group"></a>ASP.NET SignalR eco, transmissão e envio para pequeno grupo

O Serviço De Sinalização Azure fornece a mesma capacidade de desempenho para ASP.NET SignalR. 

O teste de desempenho utiliza aplicações Web Azure do [Plano de Serviço Padrão S3](https://azure.microsoft.com/pricing/details/app-service/windows/) para ASP.NET SignalR.

A tabela seguinte dá a contagem de aplicações web sugerida para ASP.NET **echo**SignalR .

|   Eco           | Unidade 1 | Unidade 2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Ligações      | 1,000 | 2.000 | 5000 | 10,000 | 20 000 | 50 000 | 100 000 |
| Contagem de servidores de aplicativos | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

A tabela seguinte dá a contagem de aplicações web sugerida para ASP.NET **transmissão**SignalR .

|  Difusão       | Unidade 1 | Unidade 2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Ligações      | 1,000 | 2.000 | 5000 | 10,000 | 20 000 | 50 000 | 100 000 |
| Contagem de servidores de aplicativos | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

A tabela seguinte dá a contagem de aplicações web sugerida para ASP.NET o SignalR **enviar para um pequeno grupo**.

|  Enviar para pequeno grupo     | Unidade 1 | Unidade 2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Ligações      | 1,000 | 2.000 | 5000 | 10,000 | 20 000 | 50 000 | 100 000 |
| Contagem de servidores de aplicativos | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

### <a name="serverless-mode"></a>Modo servidor

Os clientes e o Serviço De Sinalização Azure estão envolvidos no modo sem servidores. Cada cliente representa uma única ligação. O cliente envia mensagens através da API REST a outro cliente ou transmite mensagens a todos.

Enviar mensagens de alta densidade através da API REST não é tão eficiente como usar o WebSocket. Requer que construa sempre uma nova ligação HTTP, e isso é um custo extra no modo sem servidores.

#### <a name="broadcast-through-rest-api"></a>Transmissão através da Rest API
Todos os clientes estabelecem ligações WebSocket com o Serviço De Sinalização Azure. Então alguns clientes começam a transmitir através da API REST. O envio de mensagens (entrada) é tudo através do HTTP Post, que não é eficiente em comparação com o WebSocket.

|   Transmissão através da Rest API     | Unidade 1 | Unidade 2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Ligações               | 1,000 | 2.000 | 5000  | 10,000 | 20 000 | 50 000  | 100 000 |
| Mensagens de entrada por segundo  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Mensagens de saída por segundo | 2.000 | 4000 | 10,000 | 20 000 | 40.000 | 100 000 | 200,000 |
| Largura de banda de entrada  | 4 KBps    | 4 KBps    | 4 KBps     | 4 KBps     | 4 KBps     | 4 KBps      | 4 KBps      |
| Largura de banda de saída | 4 MBps    | 8 MBps    | 20 MBps    | 40 MBps    | 80 MBps    | 200 MBps    | 400 MBps    |

#### <a name="send-to-user-through-rest-api"></a>Enviar ao utilizador através da Rest API
O benchmark atribui nomes de utilizador a todos os clientes antes de começarem a ligar-se ao Serviço De Sinalização Azure. Depois de os clientes estabelecerem ligações WebSocket com o Serviço De Sinalização Azure, começam a enviar mensagens a outros através do HTTP Post.

|   Enviar ao utilizador através da Rest API | Unidade 1 | Unidade 2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Ligações               | 1,000 | 2.000 | 5000  | 10,000 | 20 000 | 50 000  | 100 000 |
| Mensagens de entrada por segundo  | 300   | 600   | 900    | 1,300  | 2.000  | 10,000  | 18 000  |
| Mensagens de saída por segundo | 300   | 600   | 900    | 1,300  | 2.000  | 10,000  | 18 000 |
| Largura de banda de entrada  | 600 KBps  | 1,2 MBps  | 1,8 MBps   | 2,6 MBps   | 4 MBps     | 10 MBps     | 36 MBps    |
| Largura de banda de saída | 600 KBps  | 1,2 MBps  | 1,8 MBps   | 2,6 MBps   | 4 MBps     | 10 MBps     | 36 MBps    |

## <a name="performance-test-environments"></a>Ambientes de teste de desempenho

Para todos os casos de utilização listados anteriormente, realizamos os testes de desempenho num ambiente Azure. No máximo, usamos 50 VMs de clientes e 20 VMs de servidor de aplicações. Aqui estão alguns detalhes:

- Tamanho VM do cliente: StandardDS2V2 (2 vCPU, 7G de memória)

- Tamanho VM do servidor de aplicações: StandardF4sV2 (4 vCPU, 8G de memória)

- Ligações do servidor SDK Do Sinal Azure: 15

## <a name="performance-tools"></a>Ferramentas de desempenho

Pode encontrar ferramentas de desempenho para o Serviço De Sinalização Azure no [GitHub](https://github.com/Azure/azure-signalr-bench/).

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você tem uma visão geral do desempenho do Serviço De Sinalização Azure em cenários típicos de uso.

Para obter detalhes sobre os internos do serviço e escalonando para ele, leia os seguintes guias:

* [Elementos internos do Azure SignalR Service](signalr-concept-internals.md)
* [Escala de serviço de sinalização Azure](signalr-howto-scale-multi-instances.md)

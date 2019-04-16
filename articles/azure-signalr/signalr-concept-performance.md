---
title: Guia de desempenho do serviço Azure SignalR
description: Uma visão geral do desempenho do serviço Azure SignalR.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: zhshang
ms.openlocfilehash: f7cc05c8c2a299d809c4386d119fef58fa2548d5
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/15/2019
ms.locfileid: "59579245"
---
# <a name="performance-guide-for-azure-signalr-service"></a>Guia de desempenho do serviço Azure SignalR

Uma das principais vantagens da utilização do serviço Azure SignalR é a facilidade de dimensionamento de aplicações do SignalR. Num cenário em grande escala, desempenho, é um fator importante. 

Neste guia, Apresentaremos os fatores que afetam o desempenho de aplicações do SignalR. Vamos descrever desempenho típico em diferentes cenários de casos de utilização. No fim, Apresentaremos o ambiente e as ferramentas que pode utilizar para gerar um relatório de desempenho.

## <a name="term-definitions"></a>Definições do termo

*Entrada*: A mensagem de entrada para o serviço Azure SignalR.

*Saída*: Mensagem de saída do serviço Azure SignalR.

*Largura de banda*: O tamanho total de todas as mensagens dentro de 1 segundo.

*Modo predefinido*: O modo de trabalho padrão, quando foi criada uma instância do serviço Azure SignalR. Serviço Azure SignalR espera que o servidor de aplicação para estabelecer uma ligação com o mesmo antes de ela aceita ligações de cliente.

*Modo sem servidor*: Um modo em que o serviço Azure SignalR aceita apenas ligações de cliente. É permitida sem uma ligação de servidor.

## <a name="overview"></a>Descrição geral

Serviço Azure SignalR define sete escalões Standard para as capacidades de desempenho diferente. Este guia responde às seguintes perguntas:

-   O que é o desempenho do serviço Azure SignalR típico para cada camada?

-   O serviço Azure SignalR cumpre as minhas necessidades de débito de mensagem (por exemplo, envio 100.000 de mensagens por segundo)?

-   Para o meu cenário específico, qual dos escalões é adequado para mim? Ou, como selecionar o escalão correto?

-   Que tipo de servidor de aplicações (tamanho da VM) é adequado para mim? Quantos deles deve posso implementar?

Para responder essas perguntas, este guia fornece primeiro uma explicação de alto nível dos fatores que afetam o desempenho. Ilustra, em seguida, as mensagens de entrada e saídas máximas para cada camada para casos de utilização típica: **eco**, **difusão**, **enviá-las para grupo**, e **enviar para ligação** (ponto-a-ponto bate-papo).

Este guia não pode abranger todos os cenários (e diferentes casos de utilização, tamanhos de mensagem, padrões de envio de mensagens e assim por diante). Mas ele fornece alguns métodos para ajudá-lo a:

- Avalie seus requisitos aproximados para as mensagens de entrada ou saídas.
- Encontre os escalões de adequada ao verificar a tabela de desempenho.

## <a name="performance-insight"></a>Informações de desempenho

Esta secção descreve as metodologias de avaliação de desempenho e, em seguida, apresenta uma lista de todos os fatores que afetam o desempenho. No final da contas, ele fornece métodos que ajudam a avaliar requisitos de desempenho.

### <a name="methodology"></a>Metodologia

*Débito* e *latência* dois aspectos típico da verificação de desempenho. Para o serviço Azure SignalR, cada escalão SKU tem sua própria política de limitação de taxa de transferência. Define a política *o máximo permitido de débito (entrada e saída da largura de banda)* como o máximo débito alcançado quando 99 por cento de mensagens têm latência que é inferior a 1 segundo.

A latência é o intervalo de tempo da ligação de enviar a mensagem a receber a mensagem de resposta do serviço Azure SignalR. Vamos dar **eco** como exemplo. Cada ligação de cliente adiciona um carimbo de data / hora na mensagem. Hub do servidor da aplicação envia a mensagem original para o cliente. Portanto, o atraso de propagação facilmente é calculado por cada ligação de cliente. O carimbo de data / hora é anexado para todas as mensagens no **difusão**, **send ao grupo**, e **enviar para ligação**.

Para simular milhares de conexões de cliente simultâneas, várias VMs são criadas numa rede privada virtual no Azure. Todas estas VMs ligar-se para a mesma instância do serviço Azure SignalR.

No modo padrão do serviço Azure SignalR, as VMs do servidor de aplicação são implementados na mesma rede privada virtual como cliente VMs. Todas as VMs de cliente e servidor de aplicações VMs são implementadas na mesma rede da mesma região para evitar a latência entre regiões.

### <a name="performance-factors"></a>Fatores de desempenho

Teoricamente, a capacidade do serviço Azure SignalR é limitada por recursos de computação: CPU, memória e rede. Por exemplo, mais ligações ao serviço Azure SignalR fazer com que o serviço para utilizar mais memória. Para maior tráfego de mensagens (por exemplo, cada mensagem é superior a 2048 bytes), precisa do serviço Azure SignalR gastar mais ciclos de CPU para processar o tráfego. Enquanto isso, a largura de banda de rede do Azure também impõe um limite para o tráfego máximo.

O tipo de transporte é outro fator que afeta o desempenho. Os três tipos são [WebSocket](https://en.wikipedia.org/wiki/WebSocket), [evento Server enviados](https://en.wikipedia.org/wiki/Server-sent_events), e [consulta longa de](https://en.wikipedia.org/wiki/Push_technology). 

WebSocket é bidirecional e protocolo de comunicação de full duplex através de uma única ligação de TCP. Evento Server enviados é um protocolo de unidirecional para enviar mensagens de servidor ao cliente. Consulta longa de exige que os clientes consultar periodicamente informações do servidor através de um pedido HTTP. Para a mesma API sob as condições mesmo, WebSocket tem o melhor desempenho, evento Server enviados é mais lento e consulta longa é o mais lento. Serviço Azure SignalR recomenda WebSocket por predefinição.

O custo de roteamento de mensagem também limita o desempenho. Serviço Azure SignalR desempenha uma função como um roteador de mensagem, que encaminha a mensagem de um conjunto de clientes ou servidores para outros clientes ou servidores. Um cenário diferente ou a API requer uma política de encaminhamento diferente. 

Para **eco**, o cliente envia uma mensagem para si próprio e o destino de encaminhamento, também é em si. Este padrão tem o custo mais baixo de encaminhamento. Mas para **difusão**, **send ao grupo**, e **enviar para ligação**, precisa do serviço Azure SignalR procurar as ligações de destino através dos dados distribuídos internos estrutura. Este processamento extra utiliza mais CPU, memória e largura de banda de rede. Como resultado, o desempenho é mais lento.

No modo padrão, o servidor de aplicação também pode se tornar um afunilamento em determinados cenários. Tem o SDK do Azure SignalR para invocar o hub, enquanto mantém uma ligação em direto com cada cliente através de sinais de heartbeat.

No modo sem servidor, o cliente envia uma mensagem por HTTP post, que não é tão eficiente quanto WebSocket.

Outro fator é o protocolo: JSON e [MessagePack](https://msgpack.org/index.html). MessagePack é mais pequeno de tamanho e entregues mais rapidamente do que o JSON. MessagePack poderá não melhorar o desempenho, no entanto. O desempenho do serviço Azure SignalR não é sensível a protocolos porque ele não decodificar o payload da mensagem durante o encaminhamento de mensagens de clientes para servidores ou vice-versa.

Em resumo, os seguintes fatores afetam a capacidade de entrada e saída:

-   Escalão de SKU (CPU/memória)

-   Número de ligações

-   Tamanho da mensagem

-   velocidade de envio de mensagem

-   Tipo de transporte (WebSocket, evento Server enviados ou consulta longa)

-   Cenário de casos de utilização (custo de encaminhamento)

-   ligações de servidor e serviço de aplicações (no modo de servidor)


### <a name="finding-a-proper-sku"></a>Encontrar um SKU adequado

Como pode avaliar a capacidade de entrada/saída ou localizar qual dos escalões é adequado para um caso de uso específico?

Partem do princípio de que o servidor de aplicações é poderoso o suficiente e não o afunilamento do desempenho. Em seguida, verifique a entrada e saída largura de banda máxima para cada camada.

#### <a name="quick-evaluation"></a>Avaliação rápida

Vamos simplificar primeiro a avaliação, partindo do princípio de algumas definições padrão: 

- O tipo de transporte é WebSocket.
- O tamanho da mensagem é 2.048 bytes.
- Uma mensagem for enviada a cada 1 segundo.
- Serviço Azure SignalR está no modo padrão.

Cada escalão tem sua própria largura de banda de entrada máxima e largura de banda de saída. Não é garantida que uma suave experiência de usuário após a ligação de entrada ou saída excede o limite.

**Eco** dá a largura de banda de entrada máxima porque tem o custo mais baixo de encaminhamento. **Difusão** define a largura de banda máxima de mensagens de saída.

Fazer *não* exceder os valores realçados em duas tabelas a seguir.

|       Echo                        | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| Ligações                       | 1,000 | 2.000 | 5.000 | 10,000 | 20,000 | 50,000 | 100 000 |
| **Largura de banda de entrada** | **2 MBps**    | **4 MBps**    | **10 MBps**   | **20 MBps**    | **40 MBps**    | **100 MBps**   | **200 MBps**    |
| Largura de banda de saída | 2 MBps   | 4 MBps   | 10 MBps  | 20 MBps   | 40 MBps   | 100 MBps  | 200 MBps   |


|     Difusão             | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Ligações               | 1,000 | 2.000 | 5.000  | 10,000 | 20,000 | 50,000  | 100 000 |
| Largura de banda de entrada  | 4 kBps   | 4 kBps   | 4 kBps    | 4 kBps    | 4 kBps    | 4 kBps     | 4 kBps    |
| **Largura de banda de saída** | **4 MBps**    | **8 MBps**    | **20 MBps**    | **40 MBps**    | **80 MBps**    | **200 MBps**    | **400 MBps**   |

*Largura de banda de entrada* e *largura de banda de saída* têm o tamanho total de mensagens por segundo.  Aqui estão as fórmulas para os mesmos:
```
  inboundBandwidth = inboundConnections * messageSize / sendInterval
  outboundBandwidth = outboundConnections * messageSize / sendInterval
```

- *inboundConnections*: O número de ligações de enviar a mensagem.

- *outboundConnections*: O número de ligações a receber a mensagem.

- *messageSize*: O tamanho de uma única mensagem (valor médio). Uma pequena mensagem que menos de 1.024 bytes tem um impacto de desempenho que é semelhante a uma mensagem de 1.024 bytes.

- *sendInterval*: O tempo de enviar uma mensagem. Normalmente, é 1 segundo por mensagem, o que significa a enviar uma mensagem a cada segundo. Um intervalo menor significa o envio de mensagem mais num período de tempo. Por exemplo, 0,5 segundos por mensagem significa enviar mensagens de dois a cada segundo.

- *Ligações*: O empenhada limite máximo para o serviço Azure SignalR para cada camada. Se o número de ligação é aumentado ainda mais, ele sofrerá de limitação de conexão.

#### <a name="evaluation-for-complex-use-cases"></a>Avaliação para casos de utilização complexos

##### <a name="bigger-message-size-or-different-sending-rate"></a>Maior tamanho de mensagem ou a taxa de envio de diferente

A utilização real é mais complicado. Ele pode enviar uma mensagem maior do que os bytes 2.048 ou a taxa de mensagem de envio é não uma mensagem por segundo. Vamos dar a difusão do Unit100 como exemplo para encontrar como avaliar o desempenho dele.

A tabela seguinte mostra um caso de uso real **difusão**. Mas o tamanho da mensagem, a contagem de ligação e a taxa de envio de mensagens são diferentes das, supúnhamos na secção anterior. A pergunta é como podemos pode deduzir qualquer um desses itens (tamanho da mensagem, total de ligações ou taxa de envio de mensagem) se sabemos somente dois deles.

| Difusão  | Tamanho da mensagem | Mensagens de entrada por segundo | Ligações | Enviar a intervalos |
|---|---------------------|--------------------------|-------------|-------------------------|
| 1 | 20 KB                | 1                        | 100 000     | 5 s                      |
| 2 | 256 KB               | 1                        | 8,000       | 5 s                      |

A seguinte fórmula é fácil inferir com base na fórmula anterior:

```
outboundConnections = outboundBandwidth * sendInterval / messageSize
```

Para Unit100, a largura de banda de saída máxima é de 400 MB da tabela anterior. Para um tamanho de mensagem de 20 KB, o número máximo de ligações de saída deve ser de 400 MB \* 5 / 20 KB = 100 000, que corresponde ao valor real.

##### <a name="mixed-use-cases"></a>Casos de uso misto

O caso de uso real normalmente mistura os quatro casos de uso básico em conjunto: **eco**, **difusão**, **enviá-las para grupo**, e **enviar para ligação**. A metodologia que utilizar para avaliar a capacidade é:

1. Divida os casos de uso combinado em quatro casos de utilização básica.
1. Calcule a largura de banda máxima de mensagens de entrada e saída ao utilizar as fórmulas anteriores em separado.
1. Soma os cálculos de largura de banda para obter a máxima de entrada/saída largura de banda total. 

Em seguida, selecionam o escalão adequado das tabelas de largura de banda máxima de entrada/saída.

> [!NOTE]
> Para enviar uma mensagem para centenas ou milhares de pequenos grupos ou para milhares de clientes a enviar uma mensagem para si, o custo de roteamento irá tornar-se dominante. Tire esse impacto em conta.

Para o caso de utilização de enviar uma mensagem para os clientes, certifique-se de que o servidor de aplicação está *não* o afunilamento. A seguinte secção de "Estudo de caso" fornece diretrizes sobre o número de servidores de aplicação é necessário e o número de ligações de servidor devem configurar.

## <a name="case-study"></a>Caso prático

As secções seguintes passam por quatro casos de utilização típicos para WebSocket transporte: **eco**, **difusão**, **enviá-las para grupo**, e **enviar para ligação**. Para cada cenário, a seção lista a capacidade atual de entrada e saída para o serviço Azure SignalR. Também explica os principais fatores que afetam o desempenho.

No modo padrão, o servidor de aplicação cria cinco ligações de servidor com o serviço Azure SignalR. O servidor de aplicação utiliza o SDK do serviço Azure SignalR, por predefinição. Nos seguintes resultados de teste de desempenho, as ligações de servidor são aumentadas a 15 (ou mais para difundir e enviar uma mensagem a um grupo grande).

Casos de utilização diferentes têm diferentes requisitos para servidores de aplicações. **Difusão** precisa pequeno número de servidores de aplicações. **Eco** ou **enviar para ligação** tem muitos servidores de aplicação.

Em todos os casos de utilização, o tamanho da mensagem predefinido é de 2048 bytes e o intervalo de envio de mensagem é de 1 segundo.

### <a name="default-mode"></a>Modo predefinido

Os clientes, servidores de aplicações web e o serviço Azure SignalR estão envolvidos no modo predefinido. Todos os clientes significa uma única ligação.

#### <a name="echo"></a>Echo

Em primeiro lugar, uma aplicação web liga-se ao serviço Azure SignalR. Em segundo lugar, muitos clientes ligarem à aplicação web, que redireciona os clientes para o serviço Azure SignalR com o token de acesso e o ponto final. Em seguida, os clientes estabelecem ligações de WebSocket com o serviço Azure SignalR.

Depois de todos os clientes estabelecem ligações, eles começar a enviar uma mensagem que contém um carimbo de hora para o hub específico a cada segundo. O hub ecoa a mensagem de volta para o cliente original. Todos os clientes calcula a latência ao receber a mensagem de eco novamente.

O diagrama a seguir, 5 a 8 (tráfego realçado vermelho) estão num loop. O loop é executado durante um período de predefinição (5 minutos) e obtém a estatística de todos os latência da mensagem.

![Tráfego para o caso de utilização de eco](./media/signalr-concept-performance/echo.png)

O comportamento das **eco** determina que a largura de banda de entrada máxima é igual a largura de banda de saída máxima. Para obter detalhes, consulte a tabela seguinte.

|       Echo                        | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| Ligações                       | 1,000 | 2.000 | 5.000 | 10,000 | 20,000 | 50,000 | 100 000 |
| Mensagens de entrada/saída por segundo | 1,000 | 2.000 | 5.000 | 10,000 | 20,000 | 50,000 | 100 000 |
| Largura de banda de entrada/saída | 2 MBps   | 4 MBps   | 10 MBps  | 20 MBps   | 40 MBps   | 100 MBps  | 200 MBps   |

Neste caso, cada cliente invoca o hub definido no servidor da aplicação. O hub apenas chama o método definido no lado do cliente original. Este hub é o hub mais leve para **eco**.

```
        public void Echo(IDictionary<string, object> data)
        {
            Clients.Client(Context.ConnectionId).SendAsync("RecordLatency", data);
        }
```

Mesmo para este hub simple, a pressão de tráfego no servidor da aplicação é proeminente como o **eco** aumentos de carga de mensagem de entrada. Este pressão de tráfego requer muitos servidores de aplicações para os escalões SKU grandes. A tabela seguinte lista a contagem de servidor de aplicação para cada camada.


|    Echo          | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Ligações      | 1,000 | 2.000 | 5.000 | 10,000 | 20,000 | 50,000 | 100 000 |
| Contagem de servidor de aplicação | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> O cliente ligação número, tamanho da mensagem, taxa de camada de SKU e da CPU/memória de servidor da aplicação de envio de mensagens de afetam o desempenho geral do **eco**.

#### <a name="broadcast"></a>Difusão

Para **difusão**, quando a aplicação web recebe a mensagem, difunde a todos os clientes. Os clientes mais lá estão a difusão, o tráfego de mensagens mais há para todos os clientes. Consulte o diagrama seguinte.

![Tráfego para o caso de utilização de difusão](./media/signalr-concept-performance/broadcast.png)

Um pequeno número de clientes está difundindo. A largura de banda de mensagem de entrada é pequena, mas a largura de banda de saída é enorme. A largura de banda de mensagens de saída aumenta à medida que a ligação de cliente ou aumenta a taxa de difusão.

A tabela seguinte resume as ligações de cliente máximo, contagem de mensagens de entrada/saída e largura de banda.

|     Difusão             | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Ligações               | 1,000 | 2.000 | 5.000  | 10,000 | 20,000 | 50,000  | 100 000 |
| Mensagens de entrada por segundo  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Mensagens de saída por segundo | 2.000 | 4,000 | 10,000 | 20,000 | 40,000 | 100 000 | 200,000 |
| Largura de banda de entrada  | 4 kBps   | 4 kBps   | 4 kBps    | 4 kBps    | 4 kBps    | 4 kBps     | 4 kBps     |
| Largura de banda de saída | 4 MBps   | 8 MBps   | 20 MBps   | 40 MBps   | 80 MBps   | 200 MBps   | 400 MBps   |

Os clientes de difusão publicam mensagens são mais do que quatro. Eles precisam de menos servidores de aplicação em comparação com **eco** porque a quantidade de mensagem de entrada é pequena. Dois servidores de aplicação são suficientes para considerações sobre o SLA e o desempenho. Mas deve aumentar as ligações de servidor padrão para evitar desequilíbrio, especialmente para Unit50 e Unit100.

|   Difusão      | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Ligações      | 1,000 | 2.000 | 5.000 | 10,000 | 20,000 | 50,000 | 100 000 |
| Contagem de servidor de aplicação | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
> Aumente as ligações de servidor predefinida de 5 para 40 em todos os servidores de aplicações para evitar ligações de servidor desequilibrada possível ao serviço Azure SignalR.
>
> O número de ligação de cliente, o tamanho de mensagem, taxa de envio de mensagem e escalão de SKU afetam o desempenho global para **difusão**.

#### <a name="send-to-group"></a>Envio ao grupo

O **envio ao grupo** caso de utilização tem um padrão semelhante de tráfego para **difusão**. A diferença é que depois dos clientes estabelecem ligações de WebSocket com o serviço Azure SignalR, tem de associar grupos antes de podem enviar uma mensagem a um grupo específico. O diagrama seguinte ilustra o fluxo de tráfego.

![Tráfego para o caso de utilização do grupo de envio](./media/signalr-concept-performance/sendtogroup.png)

Membro do grupo e a contagem de grupo são dois fatores que afetam o desempenho. Para simplificar a análise, podemos definir dois tipos de grupos:

- **Pequeno grupo**: Cada grupo tem 10 ligações. O número de grupo é igual a (contagem de ligação máximo) / 10. Por exemplo, para Unit1, se existirem 1.000 contagens de ligação, em seguida, temos 1000 / 10 = 100 grupos.

- **Grande grupo**: O número de grupo é sempre 10. O número de membros do grupo é igual a (contagem de ligação máximo) / 10. Por exemplo, para Unit1, se existirem 1.000 contagens de ligação, em seguida, cada grupo tem 1000 / 10 = 100 membros.

**Enviar para o grupo** traz um encaminhamento de custo para o serviço Azure SignalR, porque tem de localizar as ligações de destino por meio de uma estrutura de dados distribuídos. À medida que as ligações de envio aumentam, aumenta o custo.

##### <a name="small-group"></a>Pequeno grupo

O custo de encaminhamento é significativo para enviar mensagens para vários pequenos grupos. Atualmente, a implementação do serviço Azure SignalR atinge o limite de custo de encaminhamento Unit50. Adicionar mais CPU e memória não ajudar, para que Unit100 não é possível melhorar ainda mais ao design. Se precisar de mais de entrada de largura de banda, contacte o suporte técnico.

|   Enviar para o pequeno grupo     | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50 | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|--------|---------|
| Ligações               | 1,000 | 2.000 | 5.000  | 10,000 | 20,000 | 50,000 | 100 000
| Contagem de membros do grupo        | 10    | 10    | 10     | 10     | 10     | 10     | 10 
| Contagem de grupo               | 100   | 200   | 500    | 1,000  | 2.000  | 5.000  | 10,000 
| Mensagens de entrada por segundo  | 200   | 400   | 1,000  | 2,500  | 4,000  | 7,000  | 7,000   |
| Largura de banda de entrada  | 400 kBps  | 800 kBps  | 2 MBps     | 5 MBps     | 8 MBps     | 14 MBps    | 14 MBps     |
| Mensagens de saída por segundo | 2.000 | 4,000 | 10,000 | 25,000 | 40,000 | 70,000 | 70,000  |
| Largura de banda de saída | 4 MBps    | 8 MBps    | 20 MBps    | 50 MBps     | 80 MBps    | 140 MBps   | 140 MBps    |

Muitas ligações de cliente estão a chamar o hub, para que o número de servidor de aplicação também é fundamental para o desempenho. A tabela seguinte lista as contagens de servidor de aplicações sugeridas.

|  Enviar para o pequeno grupo   | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Ligações      | 1,000 | 2.000 | 5.000 | 10,000 | 20,000 | 50,000 | 100 000 |
| Contagem de servidor de aplicação | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> O cliente ligação número, tamanho da mensagem, a taxa, encaminhamento custo, o escalão SKU e da CPU/memória de servidor da aplicação de envio de mensagens de afetam o desempenho geral do **enviar pequeno grupo**.

##### <a name="big-group"></a>Grande grupo

Para **enviar para o grupo de grande**, a largura de banda de saída se torna o afunilamento antes de atingir o encaminhamento de limite de custos. A tabela seguinte apresenta uma lista de saída largura de banda máxima, que é quase o mesmo que para **difusão**.

|    Enviar para o grupo de grandes volume      | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Ligações               | 1,000 | 2.000 | 5.000  | 10,000 | 20,000 | 50,000  | 100 000
| Contagem de membros do grupo        | 100   | 200   | 500    | 1,000  | 2.000  | 5.000   | 10,000 
| Contagem de grupo               | 10    | 10    | 10     | 10     | 10     | 10      | 10
| Mensagens de entrada por segundo  | 20    | 20    | 20     | 20     | 20     | 20      | 20      |
| Largura de banda de entrada  | 80 kBps   | 40 kBps   | 40 kBps    | 20 kBps    | 40 kBps    | 40 kBps     | 40 kBps     |
| Mensagens de saída por segundo | 2.000 | 4,000 | 10,000 | 20,000 | 40,000 | 100 000 | 200,000 |
| Largura de banda de saída | 8 MBps    | 8 MBps    | 20 MBps    | 40 MBps    | 80 MBps    | 200 MBps    | 400 MBps    |

A contagem de ligação de envio é não mais de 40. A carga sobre o servidor de aplicações é pequena, para que o número sugerido de aplicações web é pequeno.

|  Enviar para o grupo de grandes volume  | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Ligações      | 1,000 | 2.000 | 5.000 | 10,000 | 20,000 | 50,000 | 100 000 |
| Contagem de servidor de aplicação | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
> Aumente as ligações de servidor predefinida de 5 para 40 em todos os servidores de aplicações para evitar ligações de servidor desequilibrada possível ao serviço Azure SignalR.
> 
> O número de ligação de cliente, o tamanho de mensagem, taxa de envio de mensagem, custo de encaminhamento e escalão de SKU afetam o desempenho geral do **enviar para o grupo de grande**.

#### <a name="send-to-connection"></a>Enviar para a ligação

Na **enviar para ligação** caso de utilização, quando os clientes estabelecem as ligações ao serviço Azure SignalR, todos os clientes chama um hub especial para obter o seu próprio ID de ligação. O parâmetro de comparação do desempenho recolhe todos os IDs de ligação, mistura-los e reatribui-los a todos os clientes como um destino de envio. Os clientes manterem a enviar a mensagem para a ligação de destino até termina o teste de desempenho.

![Tráfego para o caso de utilização do cliente a enviar](./media/signalr-concept-performance/sendtoclient.png)

O encaminhamento de custos para **enviar para a ligação** é semelhante ao custo para **enviar pequeno grupo**.

À medida que aumenta a contagem de ligação, o custo de encaminhamento limita o desempenho geral. Unit50 atingiu o limite. Como resultado, Unit100 não é possível aprimorar ainda mais.

A tabela seguinte é um resumo de estatístico após a muitas rodadas de executar o **enviar para ligação** benchmark.

|   Enviar para a ligação   | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50          | Unit100         |
|------------------------------------|-------|-------|-------|--------|--------|-----------------|-----------------|
| Ligações                        | 1,000 | 2.000 | 5.000 | 10,000 | 20,000 | 50,000          | 100 000         |
| Mensagens de entrada/saída por segundo | 1,000 | 2.000 | 5.000 | 8,000  | 9,000  | 20,000 | 20,000 |
| Largura de banda de entrada/saída | 2 MBps    | 4 MBps    | 10 MBps   | 16 MBps    | 18 MBps    | 40 MBps       | 40 MBps       |

Este caso de utilização requer uma carga elevada no lado do servidor de aplicação. Ver o servidor de aplicações sugeridas contar na tabela seguinte.

|  Enviar para a ligação  | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Ligações      | 1,000 | 2.000 | 5.000 | 10,000 | 20,000 | 50,000 | 100 000 |
| Contagem de servidor de aplicação | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> O cliente ligação número, tamanho da mensagem, a mensagem a enviar de taxa, encaminhamento custo, o escalão SKU e da CPU/memória para o servidor de aplicação afeta o desempenho geral do **enviar para ligação**.

#### <a name="aspnet-signalr-echo-broadcast-and-send-to-small-group"></a>Eco SignalR de ASP.NET, difusão e enviá-las para pequeno grupo

Serviço Azure SignalR fornece a mesma capacidade de desempenho para o SignalR de ASP.NET. 

O teste de desempenho utiliza aplicações Web do Azure partir [Standard S3 de plano de serviço](https://azure.microsoft.com/pricing/details/app-service/windows/) para ASP.NET SignalR.

A tabela seguinte fornece a contagem de aplicação web sugeridos para ASP.NET SignalR **eco**.

|   Echo           | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Ligações      | 1,000 | 2.000 | 5.000 | 10,000 | 20,000 | 50,000 | 100 000 |
| Contagem de servidor de aplicação | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

A tabela seguinte fornece a contagem de aplicação web sugeridos para ASP.NET SignalR **difusão**.

|  Difusão       | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Ligações      | 1,000 | 2.000 | 5.000 | 10,000 | 20,000 | 50,000 | 100 000 |
| Contagem de servidor de aplicação | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

A tabela seguinte fornece a contagem de aplicação web sugeridos para ASP.NET SignalR **enviar pequeno grupo**.

|  Enviar para o pequeno grupo     | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Ligações      | 1,000 | 2.000 | 5.000 | 10,000 | 20,000 | 50,000 | 100 000 |
| Contagem de servidor de aplicação | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

### <a name="serverless-mode"></a>Modo sem servidor

Os clientes e o serviço Azure SignalR estão envolvidos no modo sem servidor. Todos os clientes significa uma única ligação. O cliente envia mensagens através da API REST para mensagens de outro cliente ou de difusão para todos.

Enviar mensagens de alta densidade através da API REST não seja tão eficiente quanto usar WebSocket. Ele requer que crie uma nova conexão HTTP sempre, e é custos no modo sem servidor adicionais.

#### <a name="broadcast-through-rest-api"></a>Difusão através da REST API
Todos os clientes estabelecem ligações de WebSocket com o serviço Azure SignalR. Alguns clientes, em seguida, começar a difusão através da API REST. A mensagem de envio (entrada) é tudo através de HTTP Post, que não é eficiente em comparação com WebSocket.

|   Difusão através da REST API     | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Ligações               | 1,000 | 2.000 | 5.000  | 10,000 | 20,000 | 50,000  | 100 000 |
| Mensagens de entrada por segundo  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Mensagens de saída por segundo | 2.000 | 4,000 | 10,000 | 20,000 | 40,000 | 100 000 | 200,000 |
| Largura de banda de entrada  | 4 kBps    | 4 kBps    | 4 kBps     | 4 kBps     | 4 kBps     | 4 kBps      | 4 kBps      |
| Largura de banda de saída | 4 MBps    | 8 MBps    | 20 MBps    | 40 MBps    | 80 MBps    | 200 MBps    | 400 MBps    |

#### <a name="send-to-user-through-rest-api"></a>Enviar ao utilizador através da REST API
O parâmetro de comparação atribui nomes de utilizador para todos os clientes antes de começar a ligar ao serviço Azure SignalR. Depois dos clientes estabelecem ligações de WebSocket com o serviço Azure SignalR, eles comece a enviar mensagens para outras pessoas através de HTTP Post.

|   Enviar ao utilizador através da REST API | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Ligações               | 1,000 | 2.000 | 5.000  | 10,000 | 20,000 | 50,000  | 100 000 |
| Mensagens de entrada por segundo  | 300   | 600   | 900    | 1,300  | 2.000  | 10,000  | 18,000  |
| Mensagens de saída por segundo | 300   | 600   | 900    | 1,300  | 2.000  | 10,000  | 18,000 |
| Largura de banda de entrada  | 600 kBps  | 1.2 MBps  | 1.8 MBps   | 2.6 MBps   | 4 MBps     | 10 MBps     | 36 MBps    |
| Largura de banda de saída | 600 kBps  | 1.2 MBps  | 1.8 MBps   | 2.6 MBps   | 4 MBps     | 10 MBps     | 36 MBps    |

## <a name="performance-test-environments"></a>Ambientes de teste de desempenho

Para todos os casos listados anteriormente de utilização, realizamos os testes de desempenho no ambiente do Azure. No máximo, nós usamos 50 VMs de cliente e servidor de aplicações de 20 VMs. Aqui estão alguns detalhes:

- Tamanho da VM do cliente: StandardDS2V2 (2 vCPU, memória de 7G)

- Tamanho da VM de servidor de aplicações: StandardF4sV2 (4 vCPU, memória de 8G)

- Ligações de servidor de SignalR SDK do Azure: 15

## <a name="performance-tools"></a>Ferramentas de desempenho

Pode encontrar ferramentas de desempenho para o serviço Azure SignalR sobre [GitHub](https://github.com/Azure/azure-signalr-bench/tree/master/SignalRServiceBenchmarkPlugin).

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, obteve uma visão geral do desempenho do serviço Azure SignalR em cenários de casos de utilização típicos.

Para obter detalhes sobre as operações internas do serviço e o dimensionamento para ele, leia os guias seguintes:

* [Elementos internos de serviço SignalR do Azure](signalr-concept-internals.md)
* [Serviço do Azure SignalR dimensionamento](signalr-howto-scale-multi-instances.md)

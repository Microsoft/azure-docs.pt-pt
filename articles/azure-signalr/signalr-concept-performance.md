---
title: Guia de desempenho do serviço Azure SignalR
description: Uma visão geral do desempenho do serviço do Azure SignalR.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: zhshang
ms.openlocfilehash: 53139dd253c491ea6578fd0b9cbada4e7b331c7d
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/11/2019
ms.locfileid: "59502042"
---
# <a name="performance-guide-for-azure-signalr-service"></a>Guia de desempenho do serviço Azure SignalR

Um dos principais benefícios para utilizar o serviço Azure SignalR é a facilidade para dimensionar aplicações de SignalR. Num cenário em grande escala, desempenho, se torna um fator importante. Neste guia, Apresentaremos os fatores que têm impacto sobre o desempenho de aplicativos do SignalR e, em cenários de casos de utilização diferentes, o que é o desempenho típico? No final, estamos também apresentará o ambiente e as ferramentas utilizadas para gerar o relatório de desempenho.

## <a name="terms-definition"></a>Definição de termos

*ASRS*: Serviço Azure SignalR

*Entrada*: a mensagem de entrada para o serviço Azure SignalR

*Saída*: mensagem de saída do serviço Azure SignalR

*Largura de banda*: tamanho total de todas as mensagens dentro de 1 segundo

*Modo predefinido*: ASRS espera que o servidor de aplicação para estabelecer ligação com o mesmo antes de aceitar quaisquer ligações de cliente. É o modo de trabalho padrão quando um ASRS foi criado.

*Modo sem servidor*: ASRS só aceita ligações de cliente. É permitida sem uma ligação de servidor.

## <a name="overview"></a>Descrição geral

ASRS define sete escalões Standard para as capacidades de desempenho diferentes, e este guia tenciona responder às seguintes perguntas:

-   O que é o desempenho de ASRS típico para cada camada?

-   O ASRS cumpre meus requisitos de débito de mensagem, por exemplo, envio de 100 000 mensagens por segundo?

-   Para o meu cenário específico, qual dos escalões é adequado para mim? Ou, como selecionar o escalão correto?

-   Que tipo de servidor de aplicações (tamanho da VM) é adequado para mim e quantos deles deverá posso implementar?

Para responder essas perguntas, este guia de desempenho primeiro fornece uma explicação de alto nível sobre os fatores que ter impactos no desempenho, em seguida, ilustra as mensagens de entrada e saídas máximas para cada camada para casos de utilização típica: **eco**, **difusão**, **grupo de envio**, e **send para ligação** (bate-papo ponto a ponto).

Não é possível para este documento abranger todos os cenários (e diferentes casos de uso, o tamanho de mensagem diferente ou padrão de envio de mensagens etc.). No entanto, ele fornece alguns métodos de avaliação para ajudar os utilizadores aproximadamente avaliar a respetiva necessidade das mensagens de entrada ou saídas e depois de encontrar os escalões corretos, verificando a tabela de desempenho.

## <a name="performance-insight"></a>Informações de desempenho

Esta secção descreve as metodologias de avaliação de desempenho, em seguida, apresenta uma lista de todos os fatores que tem impactos no desempenho. No final da contas, ele fornece métodos para ajudar a avaliar os requisitos de desempenho.

### <a name="methodology"></a>Metodologia

**Débito** e **latência** dois aspectos típico da verificação de desempenho. Para ASRS, o escalão de SKU diferente tem diferente débito política de limitação. Este documento define **o máximo permitido de débito (entrada e saída da largura de banda)** como o máximo débito alcançado quando 99% das mensagens tem latência de menos de 1 segundo.

A latência é o intervalo de tempo da ligação de envio de mensagem para receber a mensagem de resposta de ASRS. Vamos dar **eco** como exemplo, cada ligação de cliente adiciona um carimbo na mensagem. Hub do servidor de aplicações envia a mensagem original para o cliente. Portanto, o atraso de propagação facilmente é calculado por cada ligação de cliente. O carimbo de hora é anexado para todas as mensagens no **difusão**, **send-para-group**, e **send para ligação**.

Para simular milhares de conexões de clientes simultâneos, várias VMs são criadas numa rede privada virtual no Azure. Todas estas VMs ligar-se para a mesma instância ASRS.

No modo predefinido ASRS, as VMs do servidor de aplicação também são implementados na mesma rede privada virtual como cliente VMs.

Todas as VMs de cliente e servidor de aplicações que VMS são implementadas na mesma rede da mesma região para evitar cruzada latência de região.

### <a name="performance-factors"></a>Fatores de desempenho

Teoricamente, capacidade ASRS é limitada por recursos de computação: CPU, memória e rede. Por exemplo, mais ligações para ASRS, mais memória ASRS consumidos. Para maior tráfego de mensagens, por exemplo, cada mensagem for superior a 2048 bytes, ele requer ASRS gastar mais ciclos de CPU para processar também. Enquanto isso, a largura de banda de rede do Azure também impõe um limite para o tráfego máximo.

O tipo de transporte [WebSocket](https://en.wikipedia.org/wiki/WebSocket), [eventos servidor enviados](https://en.wikipedia.org/wiki/Server-sent_events), ou [consulta longa de](https://en.wikipedia.org/wiki/Push_technology), é o desempenho de afeta outro fator. WebSocket é um protocolo de comunicação bidirecional e full duplex através de uma única ligação de TCP. No entanto, o evento servidor enviada é uni-direcional protocolo a mensagem push do servidor ao cliente. Consulta longa de exige que os clientes consultar informações do servidor através de pedido HTTP periodicamente. Para a mesma API sob a mesma condição, WebSocket tem o melhor desempenho, eventos servidor enviada é mais lento e consulta longa é o mais lento. ASRS recomenda WebSocket por predefinição.

Além disso, o custo de roteamento de mensagem também limita o desempenho. ASRS desempenha uma função como um roteador de mensagem, que encaminha a mensagem de um conjunto de clientes ou servidores para outros clientes ou servidores. Cenário diferente ou API requer a política de encaminhamento diferente. Para **eco**, o cliente envia uma mensagem para si próprio e o destino de encaminhamento, também é em si. Este padrão tem o custo mais baixo de encaminhamento. Mas para **difusão**, **send-para-group**, **send para ligação**, ASRS tem de procurar as ligações de destino através da estrutura de dados distribuídos interno, que consome mais CPU, memória e até mesmo largura de banda da rede. Como resultado, o desempenho é mais lento do que **eco**.

No modo padrão, o servidor de aplicações pode também se torne um estrangulamento para determinados cenários, porque tem de invocar o Hub, enquanto isso mantém a ligação em direto com cada cliente através de sinais de heartbeat Azure SignalR SDK.

No modo sem servidor, o cliente envia mensagem por HTTP post, que não é tão eficiente quanto WebSocket.

Outro fator é o protocolo: JSON e [MessagePack](https://msgpack.org/index.html). MessagePack é mais pequeno de tamanho e entregues mais rapidamente do que o JSON. Intuitivamente, MessagePack beneficiariam desempenho, mas desempenho ASRS não sejam confidencial com protocolos, uma vez que ele não descodificar a carga da mensagem durante o encaminhamento de mensagens de clientes para servidores ou vice-versa.

Em resumo, os seguintes fatores têm impacto na capacidade de entrada e saída:

-   Escalão de SKU (CPU/memória)

-   Número de ligações

-   Tamanho da mensagem

-   velocidade de envio de mensagem

-   tipo de transporte (WebSocket/servidor-enviados-eventos/consulta longa)

-   utilizar o cenário de caso de (custo de encaminhamento)

-   ligações de servidor e serviço de aplicações (no modo de servidor)


### <a name="find-a-proper-sku"></a>Encontrar um SKU adequado

Como avaliar a capacidade de entrada/saída ou como localizar qual dos escalões é adequado para um caso de uso específico?

Vamos supor que o servidor de aplicação é poderoso o suficiente e não é o afunilamento do desempenho. Em seguida, podemos verificar a entrada e saída largura de banda máxima para cada camada.

#### <a name="quick-evaluation"></a>Avaliação rápida

Vamos simplificar primeiro a avaliação, partindo do princípio de algumas definições padrão: WebSocket é utilizado, tamanho da mensagem é 2048 bytes, enviar mensagem cada 1 segundo, e está em default modo.

Cada escalão tem sua própria largura de banda de entrada máxima e largura de banda de saída. Não é garantida suave experiência de usuário assim que a entrada ou saída excede o limite.

**Eco** dá a largura de banda de entrada máxima porque tem o custo mais baixo de encaminhamento. **Difusão** define a largura de banda máxima de mensagens de saída.

Fazer **não** exceder os valores realçados em duas tabelas a seguir.

|       Echo                        | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| Ligações                       | 1,000 | 2.000 | 5.000 | 10,000 | 20,000 | 50,000 | 100 000 |
| **Largura de banda de entrada (bytes/s)** | **2M**    | **4M**    | **10 MILHÕES**   | **20M**    | **40M**    | **100 MILHÕES**   | **200M**    |
| Largura de banda de saída (bytes/s) | 2M    | 4M    | 10 MILHÕES   | 20M    | 40M    | 100 MILHÕES   | 200M    |


|     Difusão             | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Ligações               | 1,000 | 2.000 | 5.000  | 10,000 | 20,000 | 50,000  | 100 000 |
| Largura de banda de entrada (bytes/s)  | 4K    | 4K    | 4K     | 4K     | 4K     | 4K      | 4K     |
| **Largura de banda de saída (bytes/s)** | **4M**    | **8M**    | **20M**    | **40M**    | **80M**    | **200M**    | **400M**   |

A largura de banda de entrada e fórmulas de largura de banda de saída:
```
  inboundBandwidth = inboundConnections * messageSize / sendInterval
  outboundBandwidth = outboundConnections * messageSize / sendInterval
```

*inboundConnections*: o número de ligações de envio de mensagem

*outboundConnections*: o número de ligações a receber mensagem

*messageSize*: o tamanho de uma única mensagem (valor médio). Para pequenas mensagens cujo tamanho é inferior a 1024 bytes, ele tem o impacto de desempenho semelhantes como mensagem de 1024 bytes.

*sendInterval*: o tempo de enviar uma mensagem, normalmente, ela é de 1 segundo por mensagem, o que significa a enviar uma mensagem a cada segundo. SendInterval menor significa a enviar mensagem mais em determinado período de tempo. Por exemplo, 0,5 segundo por mensagem significa enviar mensagens de dois a cada segundo.

*Ligações* é o ASRS confirmado limite máximo para cada camada. Se o número de ligação é aumentado ainda mais, ele sofrerá de limitação de conexão.

*Largura de banda de entrada* e *largura de banda de saída* têm o tamanho total de mensagens por segundo. Estou aqui "significa megabyte para manter a simplicidade.

#### <a name="evaluation-for-complex-use-cases"></a>Avaliação para casos de utilização complexos

##### <a name="bigger-message-size-or-different-sending-rate"></a>Maior tamanho de mensagem ou a taxa de envio de diferente

A utilização real é mais complicado. Pode enviar mensagens superior a 2048 bytes ou envio de mensagem de taxa é não uma mensagem por segundo. Vamos dar a difusão do unit100 como exemplo para encontrar como avaliar o desempenho dele.

A tabela seguinte mostra um caso real de **difusão**, mas o tamanho da mensagem, a contagem de ligação e a taxa de envio de mensagens são diferentes das, supúnhamos na secção anterior. A pergunta é como podemos pode deduzir qualquer um desses itens (tamanho da mensagem, total de ligações ou taxa de envio de mensagem) se sabemos apenas 2 deles.

| Difusão  | Tamanho da mensagem (bytes) | (Mensagens/s) de entrada | Ligações | Enviar a intervalos (segundo) |
|---|---------------------|--------------------------|-------------|-------------------------|
| 1 | 20 K                 | 1                        | 100 000     | 5                       |
| 2 | 256 K                | 1                        | 8,000       | 5                       |

A seguinte fórmula é facilmente para ser inferido com base na fórmula anterior existente:

```
outboundConnections = outboundBandwidth * sendInterval / messageSize
```

Para unit100, nós sabemos, a largura de banda de saída máxima é de 400M da tabela anterior, em seguida, para o tamanho de mensagem de 20-K, o número máximo de ligações de saída deve ser 400M \* 5 / 20 mil = 100.000, que corresponde ao valor real.

##### <a name="mixed-use-cases"></a>Casos de uso misto

O caso de uso real normalmente mistura os quatro casos de uso básico em conjunto: **eco**, **difusão**, **enviá-las para grupo**, ou **enviar para ligação**. A metodologia usada para avaliar a capacidade é dividir os casos de uso combinado em quatro casos de utilização básica **calcular a largura de banda máxima de mensagens de entrada e saída** separadamente a utilizar as fórmulas acima e somá-las para obter o total largura de banda de entrada/saída máxima. Em seguida, selecionam o escalão adequado das tabelas de largura de banda máxima de entrada/saída.

Enquanto isso, para enviar a mensagem para centenas ou milhares de pequenos grupos ou milhares de clientes a enviar mensagem para si, o custo de roteamento se tornará dominante. Esse impacto deve ser levado em conta. Obter mais detalhes são abordados nas seções a seguir "Estudo de caso".

Para o caso de utilização de enviar a mensagem para os clientes, certifique-se o servidor de aplicações está **não** o afunilamento. Secção de "Estudo de caso" permite que a diretriz de quantos servidores de aplicação tem de e o número de ligações de servidor devem ser configuradas.

## <a name="case-study"></a>Caso prático

As secções seguintes passam por quatro casos de utilização típicos para WebSocket transporte: **eco**, **difusão**, **send para grupo**, e **send para ligação** . Para cada cenário, lista os ASRS atual de entrada e saída capacidade, enquanto isso explica o que é que os fatores principais no desempenho.

Em default o modo de servidor de aplicação, através do SDK do serviço Azure SignalR por predefinição, cria cinco ligações de servidor com ASRS. Teste o desempenho resultado abaixo, as ligações são aumentadas a 15 (ou mais para difusão e enviar a mensagem para o grupo de grande) do servidor.

Diferentes casos de utilização tem requisitos diferentes nos servidores de aplicações. **Difusão** precisa pequeno número de servidores de aplicações. **Eco** ou **send para ligação** tem muitos servidores de aplicação.

Em todos os casos de utilização, o tamanho da mensagem predefinido é de 2048 bytes e intervalo de envio de mensagem é de 1 segundo.

## <a name="default-mode"></a>Modo predefinido

Os clientes, servidores de aplicações web e ASRS estão envolvidas neste modo. Todos os clientes significa uma única ligação.

### <a name="echo"></a>Echo

Em primeiro lugar, as aplicações web ligar-se ao ASRS. Em segundo lugar, muitos clientes se conectam à aplicação web, que redirecionar os clientes para ASRS com o token de acesso e o ponto final. Em seguida, os clientes estabelecem ligações de WebSocket com ASRS.

Depois de todos os clientes estabelecem ligações, eles começar a enviar a mensagem, que contém um carimbo para o Hub específico a cada segundo. O Hub ecoa a mensagem de volta para o cliente original. Todos os clientes calcula a latência ao receber a mensagem de eco novamente.

Os passos 5\~8 (tráfego realçado vermelho) estão num loop, o que será executado durante um período de predefinição (5 minutos) e obter a estatística de todos os latência da mensagem.
O guia de desempenho mostra o máximo número de ligação de cliente.

![Echo](./media/signalr-concept-performance/echo.png)

**Eco**do comportamento determina que a largura de banda de entrada máxima é igual a largura de banda de saída máxima. Consulte a tabela seguinte.

|       Echo                        | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| Ligações                       | 1,000 | 2.000 | 5.000 | 10,000 | 20,000 | 50,000 | 100 000 |
| De entrada/saída (mensagens/s) | 1,000 | 2.000 | 5.000 | 10,000 | 20,000 | 50,000 | 100 000 |
| Largura de banda de entrada/saída (bytes/s) | 2M    | 4M    | 10 MILHÕES   | 20M    | 40M    | 100 MILHÕES   | 200M    |

Neste caso, cada cliente invoca o hub definido no servidor da aplicação. O hub apenas chama o método definido no lado do cliente original. Este hub é o hub ponderado mais leve para **eco**.

```
        public void Echo(IDictionary<string, object> data)
        {
            Clients.Client(Context.ConnectionId).SendAsync("RecordLatency", data);
        }
```

Mesmo para este hub simple, a pressão de tráfego no servidor de aplicação também é proeminente como o **eco** aumentos de mensagem de entrada. Por conseguinte, requer muitos servidores de aplicações para os escalões SKU grandes. A tabela seguinte lista a contagem de servidor de aplicação para cada camada.


|    Echo          | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Ligações      | 1,000 | 2.000 | 5.000 | 10,000 | 20,000 | 50,000 | 100 000 |
| Contagem de servidor de aplicação | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
>
> O cliente ligação número, tamanho da mensagem, o envio de taxa, a camada de SKU e a CPU/memória de servidor de aplicações de mensagens de tem impacto no desempenho geral de **eco**.

### <a name="broadcast"></a>Difusão

Para **difusão**, quando a aplicação web recebe a mensagem, difunde a todos os clientes. Quanto mais clientes para difusão, o tráfego de mensagens mais a todos os clientes. Consulte o diagrama seguinte.

![Difusão](./media/signalr-concept-performance/broadcast.png)

Características de difusão é que há um pequeno número de clientes de difusão, que significa que a largura de banda de mensagem de entrada é pequena, mas a largura de banda de saída é enorme. A largura de banda de mensagens de saída aumenta à medida que a ligação de cliente ou aumenta a taxa de difusão.

As ligações de cliente máximo, contagem de mensagens de entrada/saída e largura de banda estão resumidos na tabela seguinte.

|     Difusão             | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Ligações               | 1,000 | 2.000 | 5.000  | 10,000 | 20,000 | 50,000  | 100 000 |
| (Mensagens/s) de entrada  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Saída (mensagens/s) | 2.000 | 4,000 | 10,000 | 20,000 | 40,000 | 100 000 | 200,000 |
| Largura de banda de entrada (bytes/s)  | 4K    | 4K    | 4K     | 4K     | 4K     | 4K      | 4K      |
| Largura de banda de saída (bytes/s) | 4M    | 8M    | 20M    | 40M    | 80M    | 200M    | 400M    |

Os clientes de difusão publicam mensagens são mais do que 4, portanto, requer menos servidores de aplicação em comparação com **eco** como a quantidade de mensagem de entrada é pequena. Dois servidores de aplicação são suficientes para consideração de SLA e o desempenho. Mas as ligações de servidor padrão devem ser aumentadas para evitar o problema desequilibrado especialmente para Unit50 e Unit100.

|   Difusão      | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Ligações      | 1,000 | 2.000 | 5.000 | 10,000 | 20,000 | 50,000 | 100 000 |
| Contagem de servidor de aplicação | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
>
> Aumente as ligações de servidor predefinida de 5 para 40 em todos os servidores de aplicações para evitar ligações possíveis server desequilibrada ASRS.
>
> O número de ligação de cliente, o tamanho de mensagem, taxa de envio de mensagem e escalão de SKU tem impacto no desempenho geral para **difusão**

### <a name="send-to-group"></a>Envio ao grupo

**Grupo de envio** tem o padrão de tráfego semelhante, exceto que depois de estabelecer ligações de WebSocket com ASRS de clientes, tem de associar grupos antes de podem enviar mensagem para um grupo específico. O fluxo de tráfego é ilustrado pelo diagrama seguinte.

![Enviar para o grupo](./media/signalr-concept-performance/sendtogroup.png)

Membro do grupo e a contagem de grupo são dois fatores com impacto no desempenho. Para simplificar a análise, podemos definir dois tipos de grupos: grupo de pequeno e grande grupo.

- `small group`: 10 ligações em cada grupo. O número de grupo é igual a (contagem de ligação máximo) / 10. Por exemplo, para 1 unidade, se existirem contagens de ligação de 1000, em seguida, temos 1000 / 10 = 100 grupos.

- `Big group`: Número de grupo é sempre 10. O número de membros do grupo é igual a (contagem de ligação máximo) / 10. Por exemplo, para 1 unidade, se existirem contagens de ligação de 1000, em seguida, cada grupo tem 1000 / 10 = 100 membros.

**Grupo de envio** traz o custo de encaminhamento para ASRS porque tem de localizar as ligações de destino por meio de uma estrutura de dados distribuídos. À medida que aumentam as ligações de envio, o custo aumenta também.

#### <a name="small-group"></a>Pequeno grupo

O custo de encaminhamento é significativo para enviar mensagens para vários pequenos grupos. Atualmente, a implementação de ASRS atinge o limite de custo de encaminhamento em unit50. Adicionar mais CPU e memória não ajudar, para que unit100 não é possível melhorar ainda mais ao design. Se exigir entrada mais largura de banda, contacte o suporte técnico para personalização.

|   Enviar para o pequeno grupo     | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50 | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|--------|---------|
| Ligações               | 1,000 | 2.000 | 5.000  | 10,000 | 20,000 | 50,000 | 100 000
| Contagem de membros do grupo        | 10    | 10    | 10     | 10     | 10     | 10     | 10 
| Contagem de grupo               | 100   | 200   | 500    | 1,000  | 2.000  | 5.000  | 10,000 
| (Mensagens/s) de entrada  | 200   | 400   | 1,000  | 2,500  | 4,000  | 7,000  | 7,000   |
| Largura de banda de entrada (bytes/s)  | 400 K  | 800 K  | 2M     | 5M     | 8M     | 14M    | 14M     |
| Saída (mensagens/s) | 2.000 | 4,000 | 10,000 | 25,000 | 40,000 | 70,000 | 70,000  |
| Largura de banda de saída (bytes/s) | 4M    | 8M    | 20M    | 50 MILHÕES     | 80M    | 140M   | 140M    |

Existem muitas ligações de cliente chamar o hub, por conseguinte, o número da aplicação do servidor também é muito importante para o desempenho. A contagem de servidor de aplicação sugerida é listada na tabela seguinte.

|  Enviar para o pequeno grupo   | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Ligações      | 1,000 | 2.000 | 5.000 | 10,000 | 20,000 | 50,000 | 100 000 |
| Contagem de servidor de aplicação | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
>
> O número de ligação de cliente, tamanho da mensagem, taxa de envio de mensagem, encaminhamento de custo, escalão de SKU e da CPU/memória do servidor de aplicações tem impacto no desempenho geral do **send a pequeno grupo**.

#### <a name="big-group"></a>Grande grupo

Para **send para big-grupo**, a largura de banda de saída se torna o afunilamento antes de atingir o encaminhamento de limite de custos. A tabela seguinte apresenta uma lista de saída largura de banda máxima, que é quase o mesmo como **difusão**.

|    Enviar para o grupo de grandes volume      | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Ligações               | 1,000 | 2.000 | 5.000  | 10,000 | 20,000 | 50,000  | 100 000
| Contagem de membros do grupo        | 100   | 200   | 500    | 1,000  | 2.000  | 5.000   | 10,000 
| Contagem de grupo               | 10    | 10    | 10     | 10     | 10     | 10      | 10
| (Mensagens/s) de entrada  | 20    | 20    | 20     | 20     | 20     | 20      | 20      |
| Largura de banda de entrada (bytes/s)  | 80 K   | 40 K   | 40 K    | 20 K    | 40 K    | 40 K     | 40 K     |
| Saída (mensagens/s) | 2.000 | 4,000 | 10,000 | 20,000 | 40,000 | 100 000 | 200,000 |
| Largura de banda de saída (bytes/s) | 8M    | 8M    | 20M    | 40M    | 80M    | 200M    | 400M    |

A contagem de ligação de envio é não mais de 40, a carga no servidor de aplicação é pequena, também é pequeno, portanto, o número de aplicações web sugerida.

|  Enviar para o grupo de grandes volume  | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Ligações      | 1,000 | 2.000 | 5.000 | 10,000 | 20,000 | 50,000 | 100 000 |
| Contagem de servidor de aplicação | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
>
> Aumente as ligações de servidor predefinida de 5 para 40 em todos os servidores de aplicações para evitar ligações possíveis server desequilibrada ASRS.
> 
> O número de ligação de cliente, o tamanho de mensagem, taxa de envio de mensagem, custo de encaminhamento e escalão de SKU tem impacto no desempenho geral do **send para big-grupo**.

### <a name="send-to-connection"></a>Enviar para a ligação

Neste caso, quando os clientes estabelecem as ligações para ASRS, cada cliente chama um hub especial para obter o seu próprio ID de ligação. O parâmetro de comparação do desempenho é responsável para recolher todos os IDs de ligação, shuffle-los e reatribua-os para todos os clientes como um destino de envio. Os clientes manterem a enviar mensagem para a ligação de destino até termina o teste de desempenho.

![Enviar para o cliente](./media/signalr-concept-performance/sendtoclient.png)

O encaminhamento de custos para **Send-para-connection** é semelhante a como **send a pequeno grupo**.

À medida que aumenta a contagem de ligação, o desempenho global é limitado pelo custo de encaminhamento. Unidade de 50 atingiu o limite. Como resultado, não é possível aprimorar ainda mais 100 de unidade.

A tabela seguinte é uma estatística de resumida após a muitas rodadas de execução **send para ligação** benchmark

|   Enviar para a ligação   | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50          | Unit100         |
|------------------------------------|-------|-------|-------|--------|--------|-----------------|-----------------|
| Ligações                        | 1,000 | 2.000 | 5.000 | 10,000 | 20,000 | 50,000          | 100 000         |
| Entrada / saída (mensagens/s) | 1,000 | 2.000 | 5.000 | 8,000  | 9,000  | 20,000 | 20,000 |
| Entrada / saída largura de banda (bytes/s) | 2M    | 4M    | 10 MILHÕES   | 16M    | 18M    | 40M       | 40M       |

Este caso de utilização requer uma carga elevada no lado do servidor de aplicação. Ver o servidor de aplicações sugeridas contar na tabela seguinte.

|  Enviar para a ligação  | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Ligações      | 1,000 | 2.000 | 5.000 | 10,000 | 20,000 | 50,000 | 100 000 |
| Contagem de servidor de aplicação | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
>
> O número de ligação de cliente, tamanho da mensagem, taxa de envio de mensagem, encaminhamento de custo, escalão de SKU e da CPU/memória do servidor de aplicações tem impacto no desempenho geral do **send para ligação**.

### <a name="aspnet-signalr-echobroadcastsend-to-connection"></a>O SignalR de ASP.NET eco/difusão/send para ligação

ASRS fornece a mesma capacidade de desempenho para o SignalR de ASP.NET. Esta secção fornece a contagem de aplicação web sugeridos para ASP.NET SignalR **eco**, **difusão**, e **send a pequeno grupo**.

O teste de desempenho utiliza a aplicação Web do Azure de [Standard S3 de plano de serviço](https://azure.microsoft.com/pricing/details/app-service/windows/) para ASP.NET SignalR.

- `echo`

|   Echo           | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Ligações      | 1,000 | 2.000 | 5.000 | 10,000 | 20,000 | 50,000 | 100 000 |
| Contagem de servidor de aplicação | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

- `broadcast`

|  Difusão       | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Ligações      | 1,000 | 2.000 | 5.000 | 10,000 | 20,000 | 50,000 | 100 000 |
| Contagem de servidor de aplicação | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

- `Send-to-small-group`

|  Enviar para o pequeno grupo     | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Ligações      | 1,000 | 2.000 | 5.000 | 10,000 | 20,000 | 50,000 | 100 000 |
| Contagem de servidor de aplicação | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

## <a name="serverless-mode"></a>Modo sem servidor

Os clientes e ASRS estão envolvidas neste modo. Todos os clientes significa uma única ligação. O cliente envia mensagens através da REST API para mensagens de outro cliente ou de difusão para todos.

Enviar mensagens de alta densidade através da REST API não é tão eficiente quanto WebSocket, porque é necessária para criar uma nova conexão HTTP sempre - custos no modo sem servidor adicionais.

### <a name="broadcast-through-rest-api"></a>Difusão através da REST API
Todos os clientes estabelecem ligações de WebSocket com ASRS. Alguns clientes, em seguida, começar a difusão através da REST API. A mensagem de envio (entrada) são tudo através de HTTP Post, que não é eficiente em comparação com WebSocket.

|   Difusão através da REST API     | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Ligações               | 1,000 | 2.000 | 5.000  | 10,000 | 20,000 | 50,000  | 100 000 |
| (Mensagens/s) de entrada  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Saída (mensagens/s) | 2.000 | 4,000 | 10,000 | 20,000 | 40,000 | 100 000 | 200,000 |
| Largura de banda de entrada (bytes/s)  | 4K    | 4K    | 4K     | 4K     | 4K     | 4K      | 4K      |
| Largura de banda de saída (bytes/s) | 4M    | 8M    | 20M    | 40M    | 80M    | 200M    | 400M    |

### <a name="send-to-user-through-rest-api"></a>Enviar ao utilizador através da REST API
O parâmetro de comparação atribui os nomes de utilizador para todos os clientes antes de este começar ASRS a ligar. Depois dos clientes estabeleceram ligações de WebSocket com ASRS, eles comece a enviar mensagens para outras pessoas através de HTTP Post.

|   Enviar ao utilizador através da REST API | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Ligações               | 1,000 | 2.000 | 5.000  | 10,000 | 20,000 | 50,000  | 100 000 |
| (Mensagens/s) de entrada  | 300   | 600   | 900    | 1,300  | 2.000  | 10,000  | 18,000  |
| Saída (mensagens/s) | 300   | 600   | 900    | 1,300  | 2.000  | 10,000  | 18,000 |
| Largura de banda de entrada (bytes/s)  | 600 K  | 1.2 M  | 1.8 M   | 2.6 M   | 4M     | 10 MILHÕES     | 36M    |
| Largura de banda de saída (bytes/s) | 600 K  | 1.2 M  | 1.8 M   | 2.6 M   | 4M     | 10 MILHÕES     | 36M    |

## <a name="performance-test-environments"></a>Ambientes de teste de desempenho

O teste de desempenho para todos os casos de utilização listadas acima foram realizados no ambiente do Azure. Na maioria das VMs de 50 cliente e servidor de aplicação 20 VMs são utilizadas.

Tamanho da VM do cliente: StandardDS2V2 (2 vCPU, memória de 7G)

Tamanho da VM de servidor de aplicações: StandardF4sV2 (4 vCPU, memória de 8G)

Ligações de servidor de SignalR SDK do Azure: 15

## <a name="performance-tools"></a>Ferramentas de desempenho

https://github.com/Azure/azure-signalr-bench/tree/master/SignalRServiceBenchmarkPlugin

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, obtém uma visão geral do desempenho do serviço SignalR em cenários de casos de utilização típicos.

Para obter mais detalhes sobre as operações internas de serviço SignalR e dimensionamento para o serviço SignalR, leia o guia seguinte.

* [Elementos internos do serviço Azure SignalR](signalr-concept-internals.md)
* [Serviço do Azure SignalR dimensionamento](signalr-howto-scale-multi-instances.md)
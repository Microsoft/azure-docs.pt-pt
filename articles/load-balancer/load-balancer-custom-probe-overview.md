---
title: Sondas de saúde à escala e fornecer HA para o seu serviço
titleSuffix: Azure Load Balancer
description: Neste artigo, aprenda a usar sondas de saúde para monitorizar casos por trás do Azure Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
manager: kumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: e22908dc5d445f105c199e594443cd051eb4be41
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89051360"
---
# <a name="load-balancer-health-probes"></a>Sondas de estado de funcionamento do Balanceador de Carga

Ao utilizar regras de equilíbrio de carga com o Azure Load Balancer, é necessário especificar sondas sanitárias para permitir que o Balancer de Carga detete o estado do ponto final de backend.  A configuração da sonda de saúde e as respostas da sonda determinam quais as instâncias de backend pool que receberão novos fluxos. Pode utilizar sondas de saúde para detetar a falha de uma aplicação num ponto final de backend. Também pode gerar uma resposta personalizada a uma sonda de saúde e usar a sonda de saúde para o controlo do fluxo para gerir a carga ou o tempo de inatividade planeado. Quando uma sonda de saúde falha, o Balanceador de Carga deixará de enviar novos fluxos para o respetivo caso pouco saudável. A conectividade de saída não é impactada, apenas a conectividade de entrada é impactada.

As sondas de saúde suportam vários protocolos. A disponibilidade de um protocolo específico de sonda de saúde varia de acordo com o Balancer de Carga SKU.  Adicionalmente, o comportamento do serviço varia de acordo com o Balancer de Carga SKU, como mostrado nesta tabela:

| | SKU Standard | SKU Básico |
| --- | --- | --- |
| **[Tipos de sonda](#types)** | TCP, HTTP, HTTPS | TCP, HTTP |
| **[Sonda para baixo comportamento](#probedown)** | Todas as sondas para baixo, todos os fluxos de TCP continuam. | Todas as sondas para baixo, todos os fluxos de TCP expiram. | 


>[!IMPORTANT]
>Reveja este documento na íntegra, incluindo [orientações importantes](#design) de design abaixo para criar um serviço fiável.

>[!IMPORTANT]
>As sondas de saúde do Balancer de Carga originam-se do endereço IP 168.63.129.16 e não devem ser bloqueadas para que as sondas marquem o seu caso.  Analise [o endereço IP da fonte de sonda](#probesource) para mais detalhes.

>[!IMPORTANT]
>Independentemente do limiar de tempo configurado, as sondas de saúde http(S) Load Balancer sondam automaticamente uma instância se o servidor devolver qualquer código de estado que não seja HTTP 200 OK ou se a ligação for terminada através do reset TCP.

## <a name="probe-configuration"></a><a name="probes"></a>Configuração da sonda

A configuração da sonda de saúde consiste entre os seguintes elementos:

- Duração do intervalo entre sondas individuais
- Número de respostas da sonda que têm de ser observadas antes da transição da sonda para um estado diferente
- Protocolo da sonda
- Porto da sonda
- CAMINHO HTTP para utilizar para HTTP GET ao utilizar sondas HTTP(S)

>[!NOTE]
>Uma definição de sonda não é obrigatória ou verificada quando se utiliza Azure PowerShell, Azure CLI, Templates ou API. Os testes de validação da sonda só são feitos quando se utilizam o Portal Azure.

## <a name="understanding-application-signal-detection-of-the-signal-and-reaction-of-the-platform"></a>Compreender o sinal de aplicação, a deteção do sinal e a reação da plataforma

O número de respostas da sonda aplica-se a ambos

- o número de sondas bem sucedidas que permitem que um caso seja marcado como até, e
- o número de sondas cronometradas que fazem com que um caso seja marcado como baixo.

Os valores de intervalo e intervalo especificados determinam se uma instância será marcada como para cima ou para baixo.  A duração do intervalo multiplicada pelo número de respostas da sonda determina a duração durante a qual as respostas da sonda devem ser detetadas.  E o serviço reagirá depois de as sondas necessárias terem sido alcançadas.

Podemos ilustrar ainda mais o comportamento com um exemplo. Se tiver definido o número de respostas da sonda para 2 e o intervalo para 5 segundos, isto significa que 2 falhas de tempo de sonda devem ser observadas num intervalo de 10 segundos.  Como o momento em que uma sonda é enviada não é sincronizado quando a sua aplicação pode mudar de estado, podemos vincular o tempo para detetar por dois cenários:

1. Se a sua aplicação começar a produzir uma resposta de sonda de tempo de saída pouco antes da chegada da primeira sonda, a deteção destes eventos demorará 10 segundos (intervalos de 2 x 5 segundos) mais a duração da aplicação começando a sinalizar um intervalo para quando a primeira sonda chegar.  Pode assumir que esta deteção demora um pouco mais de 10 segundos.
2. Se a sua aplicação começar a produzir uma resposta de sonda de tempo de saída logo após a chegada da primeira sonda, a deteção destes eventos não começará até que a próxima sonda chegue (e os tempos fora) mais 10 segundos (intervalos de 2 x 5 segundos).  Pode assumir que esta deteção demora menos de 15 segundos.

Para este exemplo, uma vez que a deteção tenha ocorrido, a plataforma demorará um pouco de tempo a reagir a esta mudança.  Isto significa que dependendo de 

1. quando a aplicação começa a mudar de estado e
2. quando esta alteração for detetada e satisfaer os critérios necessários (número de sondas enviadas no intervalo especificado) e
3. quando a deteção foi comunicada através da plataforma 

pode presumir que a reação a uma resposta da sonda de tempo para intervalo levará entre um mínimo de pouco mais de 10 segundos e um máximo de pouco mais de 15 segundos para reagir a uma mudança no sinal da aplicação.  Este exemplo é dado para ilustrar o que está a acontecer, no entanto, não é possível prever uma duração exata para além das orientações aproximadas acima ilustradas neste exemplo.

>[!NOTE]
>A sonda de saúde vai sondar todas as instâncias de execução na piscina de backend. Se um caso for interrompido, não será sondado até que tenha sido reiniciado.

## <a name="probe-types"></a><a name="types"></a>Tipos de sonda

O protocolo utilizado pela sonda de saúde pode ser configurado para um dos seguintes:

- [Ouvintes da TCP](#tcpprobe)
- [Pontos finais HTTP](#httpprobe)
- [Pontos finais HTTPS](#httpsprobe)

Os protocolos disponíveis dependem do Balanceador de Carga utilizado:

|| TCP | HTTP | HTTPS |
| --- | --- | --- | --- |
| **SKU Standard** |    &#9989; |   &#9989; |   &#9989; |
| **SKU Básico** |   &#9989; |   &#9989; | &#10060; |

### <a name="tcp-probe"></a><a name="tcpprobe"></a> Sonda TCP

As sondas TCP iniciam uma ligação realizando um aperto de mão TCP aberto de três vias com a porta definida.  As sondas TCP terminam uma ligação com um aperto de mão TCP de quatro vias.

O intervalo mínimo da sonda é de 5 segundos e o número mínimo de respostas pouco saudáveis é 2.  A duração total de todos os intervalos não pode exceder 120 segundos.

Uma sonda TCP falha quando:
* O ouvinte da TCP sobre o caso não responde de todo durante o período de tempo limite.  Uma sonda é marcada com base no número de pedidos de sonda cronometrados, que foram configurados para ficar sem resposta antes de marcar a sonda.
* A sonda recebe um reset TCP a partir do caso.

O seguinte ilustra como pode expressar este tipo de configuração de sonda num modelo de Gestor de Recursos:

```json
    {
      "name": "tcp",
      "properties": {
        "protocol": "Tcp",
        "port": 1234,
        "intervalInSeconds": 5,
        "numberOfProbes": 2
      },
```

### <a name="http--https-probe"></a><a name="httpprobe"></a><a name="httpsprobe"></a>SONDA HTTP / HTTPS

>[!NOTE]
>A sonda HTTPS só está disponível para [o Balanceador de Carga Padrão.](load-balancer-standard-overview.md)

As sondas HTTP e HTTPS baseiam-se na sonda TCP e emitem um HTTP GET com o caminho especificado. Ambas as sondas suportam caminhos relativos para o HTTP GET. As sondas HTTPS são as mesmas que as sondas HTTP com a adição de um invólucro de Segurança da Camada de Transporte (TLS, anteriormente conhecido como SSL). A sonda de saúde é marcada quando o caso responde com um estado HTTP 200 dentro do período de tempo limite.  A sonda de saúde tenta verificar a porta da sonda de saúde configurada a cada 15 segundos por defeito. O intervalo mínimo da sonda é de 5 segundos. A duração total de todos os intervalos não pode exceder 120 segundos.

As sondas HTTP / HTTPS também podem ser úteis para implementar a sua própria lógica para remover instâncias da rotação do balanceador de carga se a porta da sonda for também o ouvinte do próprio serviço. Por exemplo, pode decidir remover uma instância se for superior a 90% de CPU e devolver um estado HTTP não-200. 

> [!NOTE] 
> A sonda HTTPS requer a utilização de certificados baseados que tenham um haxixe de assinatura mínima de SHA256 em toda a cadeia.

Se utilizar os Serviços Cloud e tiver funções web que usam w3wp.exe, também obtém uma monitorização automática do seu website. Falhas no código do seu site devolvem um estado não-200 à sonda do balançador de carga.

Uma sonda HTTP / HTTPS falha quando:
* O ponto final da sonda devolve um código de resposta HTTP que não seja 200 (por exemplo, 403, 404 ou 500). Isto marcará a sonda de saúde imediatamente. 
* O ponto final da sonda não responde de todo durante o intervalo mínimo da sonda e o período de tempo de 30 segundos. Vários pedidos de sonda podem ficar sem resposta antes que a sonda seja marcada como não funcionando e até que a soma de todos os intervalos de tempo tenha sido alcançada.
* O ponto final da sonda fecha a ligação através de um reset TCP.

O seguinte ilustra como pode expressar este tipo de configuração de sonda num modelo de Gestor de Recursos:

```json
    {
      "name": "http",
      "properties": {
        "protocol": "Http",
        "port": 80,
        "requestPath": "/",
        "intervalInSeconds": 5,
        "numberOfProbes": 2
      },
```

```json
    {
      "name": "https",
      "properties": {
        "protocol": "Https",
        "port": 443,
        "requestPath": "/",
        "intervalInSeconds": 5,
        "numberOfProbes": 2
      },
```

### <a name="guest-agent-probe-classic-only"></a><a name="guestagent"></a>Sonda de agente convidado (apenas clássico)

As funções de serviço em nuvem (funções de trabalhador e funções web) utilizam um agente convidado para monitorização da sonda por padrão.  Uma sonda de agente convidado é uma configuração de último recurso.  Utilize sempre uma sonda de saúde explicitamente com uma sonda TCP ou HTTP. Uma sonda de agente convidado não é tão eficaz como sondas explicitamente definidas para a maioria dos cenários de aplicação.

Uma sonda de agente convidado é um cheque do agente convidado dentro do VM. Em seguida, ouve e responde com uma resposta HTTP 200 OK apenas quando a instância está no estado Ready. (Outros estados estão ocupados, reciclando ou parando.)

Para obter mais informações, consulte [configurar o ficheiro de definição de serviço (csdef) para sondas de saúde](https://msdn.microsoft.com/library/azure/ee758710.aspx) ou [começar por criar um equilibrador de carga pública para serviços na nuvem](https://docs.microsoft.com/azure/load-balancer/load-balancer-get-started-internet-classic-cloud#check-load-balancer-health-status-for-cloud-services).

Se o agente convidado não responder com HTTP 200 OK, o equilibrador de carga marca a ocorrência como não respondendo. Em seguida, deixa de enviar fluxos para este caso. O balançador de carga continua a verificar o caso. 

Se o agente convidado responder com um HTTP 200, o balançador de carga envia novos fluxos para esse caso novamente.

Quando utiliza uma função web, o código do site normalmente funciona em w3wp.exe, que não é monitorizado pelo tecido Azure ou pelo agente convidado. Falhas na w3wp.exe (por exemplo, respostas HTTP 500) não são reportadas ao agente convidado. Consequentemente, o equilibrador de carga não tira esse exemplo da rotação.

<a name="health"></a>
## <a name="probe-up-behavior"></a><a name="probehealth"></a>Sondar o comportamento

As sondas de saúde TCP, HTTP e HTTPS são consideradas saudáveis e marcam o ponto final como saudável quando:

* A sonda de saúde é bem sucedida uma vez depois das botas VM.
* O número especificado de sondas necessárias para marcar o ponto final de backend como saudável foi alcançado.

Qualquer ponto final que tenha alcançado um estado saudável é elegível para receber novos fluxos.  

> [!NOTE]
> Se a sonda de saúde flutuar, o equilibrador de carga espera mais tempo antes de colocar o ponto final de backend de volta no estado saudável. Este tempo de espera extra protege o utilizador e a infraestrutura e é uma política intencional.

## <a name="probe-down-behavior"></a><a name="probedown"></a>Sonda para baixo comportamento

### <a name="tcp-connections"></a>Ligações TCP

As novas ligações TCP conseguirão manter o ponto final saudável.

Se a sonda de saúde de um backend endpoint falhar, as ligações TCP estabelecidas a este ponto final de backend continuam.

Se todas as sondas para todas as instâncias numa piscina de backend falharem, nenhum fluxo novo será enviado para a piscina de backend. O Balanceador de Carga Padrão permitirá que os fluxos TCP estabelecidos continuem.  O Balancer de Carga Básica terminará todos os fluxos TCP existentes para a piscina de backend.
 
O Balancer de Carga é um passe através do serviço (não termina as ligações TCP) e o fluxo é sempre entre o cliente e o so convidado do VM e a aplicação. Uma piscina com todas as sondas para baixo fará com que um frontend não responda às tentativas abertas de ligação TCP (SYN), uma vez que não existe um ponto final saudável para receber o fluxo e responder com um SYN-ACK.

### <a name="udp-datagrams"></a>Datagramas de UDP

Os datagramas da UDP serão entregues em pontos finais saudáveis.

A UDP não tem ligação e não existe um estado de fluxo rastreado para a UDP. Se a sonda de saúde de backend endpoint falhar, os fluxos de UDP existentes passarão para outro caso saudável na piscina de backend.

Se todas as sondas para todas as instâncias de um pool de backend falharem, os fluxos UDP existentes terminarão para saldos de carga básico e padrão.

<a name="source"></a>
## <a name="probe-source-ip-address"></a><a name="probesource"></a>Endereço IP de fonte de sonda

O Load Balancer utiliza um serviço de sondagem distribuído para o seu modelo de saúde interna. O serviço de sondagem reside em cada hospedeiro onde os VMs e podem ser programados a pedido para gerar sondas de saúde de acordo com a configuração do cliente. O tráfego da sonda de saúde é diretamente entre o serviço de sondagem que gera a sonda de saúde e o VM do cliente. Todas as sondas de saúde load balancer originam do endereço IP 168.63.129.16 como fonte.  Pode utilizar o espaço de endereço IP dentro de um VNet que não é espaço RFC1918.  Utilizando um endereço IP de propriedade global, a Microsoft reduz a hipótese de um endereço IP entrar em conflito com o espaço de endereço IP que utiliza dentro do VNet.  Este endereço IP é o mesmo em todas as regiões e não muda e não é um risco para a segurança, porque só o componente interno da plataforma Azure pode obter um pacote a partir deste endereço IP. 

A etiqueta de serviço AzureLoadBalancer identifica este endereço IP de origem nos [seus grupos de segurança](../virtual-network/security-overview.md) de rede e permite o tráfego de sondas de saúde por padrão.

Para além das sondas de saúde Load Balancer, as [seguintes operações utilizam este endereço IP:](../virtual-network/what-is-ip-address-168-63-129-16.md)

- Permite ao Agente VM comunicar com a plataforma para sinalizar que está em estado "Pronto"
- Permite que a comunicação com o servidor virtual DNS forneça resolução de nomes filtrados a clientes que não definem servidores DNS personalizados.  Esta filtragem garante que os clientes só podem resolver os hostnames da sua implantação.
- Permite ao VM obter um endereço IP dinâmico a partir do serviço DHCP em Azure.

## <a name="design-guidance"></a><a name="design"></a> Orientação de design

As sondas de saúde são usadas para tornar o seu serviço resiliente e permitir a sua escala. Uma configuração errada ou um mau padrão de design podem afetar a disponibilidade e escalabilidade do seu serviço. Reveja todo este documento e considere qual é o impacto para o seu cenário quando esta resposta da sonda é marcada para baixo ou marcada, e como impacta a disponibilidade do seu cenário de aplicação.

Ao desenhar o modelo de saúde para a sua aplicação, deve sondar uma porta num ponto final de backend que reflita a saúde dessa instância __e__ o serviço de aplicação que está a prestar.  A porta de aplicação e a porta da sonda não são necessárias para serem as mesmas.  Em alguns cenários, pode ser desejável que a porta da sonda seja diferente da porta onde a sua aplicação presta serviço.  

Por vezes, pode ser útil para a sua aplicação gerar uma resposta de sonda de saúde para não só detetar a saúde da sua aplicação, mas também sinalizar diretamente para o Balancer de Carga se o seu caso deve receber ou não novos fluxos.  Pode manipular a resposta da sonda para permitir que a sua aplicação crie a retropressão e o acelerador de entrega de novos fluxos para um caso, falhando a sonda de saúde ou preparando-se para a manutenção da sua aplicação e iniciando a drenagem do seu cenário.  Ao utilizar o Balancer de Carga Padrão, um sinal [de sonda para baixo](#probedown) permitirá sempre que os fluxos de TCP continuem até que o tempo de activamento ou o fecho da ligação. 

Para o equilíbrio da carga UDP, deve gerar um sinal de sonda de saúde personalizado a partir do ponto final de backend e utilizar uma sonda de saúde TCP, HTTP ou HTTPS que direcione o ouvinte correspondente para refletir a saúde da sua aplicação UDP.

Ao utilizar [as regras de equilíbrio de carga dos portos HA](load-balancer-ha-ports-overview.md) com [o Balanceador de Carga Padrão,](load-balancer-standard-overview.md)todas as portas são equilibradas em carga e uma única resposta da sonda de saúde deve refletir o estado de toda a instância.

Não traduza ou proxy uma sonda de saúde através do caso que recebe a sonda de saúde para outro caso no seu VNet, uma vez que esta configuração pode levar a falhas em cascata no seu cenário.  Considere o seguinte cenário: um conjunto de aparelhos de terceiros é implantado no pool de backend de um recurso load balancer para fornecer escala e redundância para os aparelhos e a sonda de saúde está configurada para sondar uma porta que o aparelho de terceiros proxie ou se traduz para outras máquinas virtuais atrás do aparelho.  Se sondar a mesma porta que está a utilizar para traduzir ou solicitar pedidos às outras máquinas virtuais por detrás do aparelho, qualquer resposta da sonda de uma única máquina virtual atrás do aparelho marcará o próprio aparelho morto. Esta configuração pode levar a uma falha em cascata de todo o cenário de aplicação como resultado de um único ponto final de backend atrás do aparelho.  O gatilho pode ser uma falha intermitente da sonda que fará com que o Balancer de Carga marque o destino original (a instância do aparelho) e, por sua vez, pode desativar todo o seu cenário de aplicação. Em vez disso, sondar a saúde do aparelho. A seleção da sonda para determinar o sinal de saúde é uma consideração importante para os cenários de aparelhos virtuais de rede (NVA) e deve consultar o seu fornecedor de aplicações para saber qual é o sinal de saúde adequado para tais cenários.

Se não permitir a [origem ip](#probesource) da sonda nas suas políticas de firewall, a sonda de saúde falhará, uma vez que não consegue chegar ao seu caso.  Por sua vez, o Balanceador de Carga marcará o seu caso devido à falha da sonda de saúde.  Esta configuração errada pode fazer com que o seu cenário de aplicação equilibrado falhe.

Para a sonda de saúde do Load Balancer para marcar o seu caso, **deve** permitir este endereço IP em quaisquer [grupos de segurança](../virtual-network/security-overview.md) da rede Azure e políticas locais de firewall.  Por predefinição, todos os grupos de segurança da rede incluem a [etiqueta de serviço](../virtual-network/security-overview.md#service-tags) AzureLoadBalancer para permitir o tráfego de sondas de saúde.

Se desejar testar uma falha da sonda de saúde ou marcar uma instância individual, pode utilizar um [grupo de segurança](../virtual-network/security-overview.md) de rede para bloquear explicitamente a sonda de saúde (porto de destino ou IP de [origem)](#probesource)e simular a falha de uma sonda.

Não configuure o seu VNet com a gama de endereços IP da Microsoft que contém 168.63.129.16.  Tais configurações colidirão com o endereço IP da sonda de saúde e podem fazer com que o seu cenário falhe.

Se tiver várias interfaces no seu VM, tem de garantir que responde à sonda na interface em que a recebeu.  Pode ter de obter o endereço de rede de origem traduzir este endereço no VM numa base de interface.

Não ative [os tempos de TCP](https://tools.ietf.org/html/rfc1323).  Permitir que os timetamps TCP possam fazer com que as sondas de saúde falhem devido à queda dos pacotes TCP pela pilha de OS TCP convidada da VM, o que resulta na marcação do Balancer de Carga no respetivo ponto final.  Os selos temporais TCP são ativados rotineiramente por padrão em imagens VM endurecidas de segurança e devem ser desativados.

## <a name="monitoring"></a>Monitorização

[O balanceador de carga padrão](load-balancer-standard-overview.md) público e interno expõe por ponto final e o estado da sonda de saúde de backend como métricas multidimensionais através do Azure Monitor. Estas métricas podem ser consumidas por outros serviços Azure ou aplicações parceiras. 

O Balancer de Carga Pública Básica expõe o estado da sonda de saúde resumido por piscina de backend através de registos do Azure Monitor.  Os registos do Monitor Azure não estão disponíveis para os balançadores de carga básico internos.  Pode utilizar [registos do Azure Monitor](load-balancer-monitor-log.md) para verificar o estado de saúde da sonda e contagem de sondas do balançador de carga pública. O registo pode ser usado com Power BI ou Azure Operational Insights para fornecer estatísticas sobre o estado de saúde do equilibrador de carga.

## <a name="limitations"></a>Limitações

- As sondas HTTPS não suportam a autenticação mútua com um certificado de cliente.
- Deve assumir que as sondas de saúde falharão quando os picos de tempo TCP estiverem ativados.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o [Balanceador de Carga Standard](load-balancer-standard-overview.md)
- [Começar a criar um balanceador de carga pública em Resource Manager utilizando o PowerShell](quickstart-load-balancer-standard-public-powershell.md)
- [REST API para sondas de saúde](https://docs.microsoft.com/rest/api/load-balancer/loadbalancerprobes/)
- Solicite novas capacidades de sonda de saúde com [a voz do Utilizador do Balancer de Carga](https://aka.ms/lbuservoice)

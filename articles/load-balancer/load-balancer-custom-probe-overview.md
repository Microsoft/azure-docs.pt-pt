---
title: Utilizar o Balanceador de carga do Azure para dimensionar e proporcionar elevada disponibilidade para o seu serviço de sondas de estado de funcionamento
titlesuffix: Azure Load Balancer
description: Saiba como utilizar sondas de estado de funcionamento para monitorizar instâncias por trás do Balanceador de carga
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/14/2018
ms.author: kumud
ms.openlocfilehash: dc22f1392d14529c2944dde67315a278fca4fa73
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57195341"
---
# <a name="load-balancer-health-probes"></a>As sondas de estado de funcionamento do Balanceador de carga

O Balanceador de carga do Azure fornece as sondas de estado de funcionamento para utilização com as regras de balanceamento de carga.  Respostas de configuração e a sonda de sonda de estado de funcionamento determinam a quais instâncias de conjunto de back-end irão receber novos fluxos. Pode utilizar sondas de estado de funcionamento para detetar a falha de um aplicativo numa instância de back-end. Também pode gerar uma resposta personalizada para uma sonda de estado de funcionamento e utilizar a sonda de estado de funcionamento de controlo de fluxo para gerir a carga ou períodos de indisponibilidade planeados. Quando uma sonda de estado de funcionamento falha, o Balanceador de carga para a enviar novos fluxos para a respetiva instância de mau estado de funcionamento.

Sondas de estado de funcionamento suportam vários protocolos. A disponibilidade de um tipo específico de sonda de estado de funcionamento para suportar um protocolo específico varia consoante o SKU do Balanceador de carga.  Além disso, o comportamento do serviço varia consoante o SKU do Balanceador de carga.

| | SKU Standard | SKU Básico |
| --- | --- | --- |
| [Tipos de sonda](#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Sonda de comportamento](#probedown) | Todas as sondas, continuam a todos os fluxos TCP. | Todas as sondas para baixo, terminam a todos os fluxos TCP. | 

> [!IMPORTANT]
> As sondas de estado de funcionamento do Balanceador de carga provêm do endereço IP 168.63.129.16 e não tem de ser bloqueadas para sondas marcar a sua instância.  Revisão [endereço IP de origem de sonda](#probesource) para obter detalhes.

## <a name="types"></a>Tipos de sonda

A sonda de estado de funcionamento pode ser configurada para utilizar os seguintes três protocolos de serviços de escuta:

- [Serviços de escuta TCP](#tcpprobe)
- [Pontos de extremidade HTTP](#httpprobe)
- [Pontos finais de HTTPS](#httpsprobe)

Os tipos de sondas de estado de funcionamento disponíveis variam consoante o SKU do Balanceador de carga selecionado:

|| TCP | HTTP | HTTPS |
| --- | --- | --- | --- |
| SKU Standard |    &#9989; |   &#9989; |   &#9989; |
| SKU Básico |   &#9989; |   &#9989; | &#10060; |

Independentemente de qual tipo de sonda que escolher, sondas de estado de funcionamento podem observar qualquer porta numa instância de back-end, incluindo a porta em que o serviço real é fornecido.

### <a name="tcpprobe"></a> Sonda TCP

Sondas TCP iniciam uma ligação ao efetuar um handshake TCP aberto de três vias com a porta definido.  Sondas TCP terminam uma ligação com um handshake TCP fechar de quatro vias.

O intervalo de sonda mínimo é de 5 segundos e o número mínimo de respostas de mau estado de funcionamento é 2.  A duração total de todos os intervalos não pode exceder os 120 segundos.

Uma sonda TCP falha quando:
* O serviço de escuta TCP na instância não responde durante o período de tempo limite.  Uma sonda é marcada para baixo com base no número de solicitações de sonda com falha, o que foram configuradas para ir sem resposta antes de os marcar para baixo a sonda.
* A sonda recebe uma reposição da instância TCP.

#### <a name="resource-manager-template"></a>Modelo do Resource Manager

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

### <a name="httpprobe"></a> <a name="httpsprobe"></a> HTTP / HTTPS de sonda

> [!NOTE]
> Sonda HTTPS apenas está disponível para [Balanceador de carga Standard](load-balancer-standard-overview.md).

Sondas HTTP e HTTPS aumentar a sonda TCP e emitir um HTTP GET com o caminho especificado. Ambas estas sondas suportam caminhos relativos para o HTTP GET. Sondas HTTPS são os mesmos, como as sondas HTTP com a adição da Transport Layer Security (TLS, anteriormente conhecido como SSL) wrapper. A sonda de estado de funcionamento está marcado como cópia de segurança quando a instância responde com um Estado HTTP 200 dentro do período de tempo limite.  A sonda de estado de funcionamento tenta verificar a cada 15 segundos da porta de sonda de estado de funcionamento configurado por predefinição. O intervalo de sonda mínimo é de 5 segundos. A duração total de todos os intervalos não pode exceder os 120 segundos.

HTTP / HTTPS sondas também podem ser útil se desejar expressar a sonda de estado de funcionamento.  Implemente a sua própria lógica para remover instâncias de rotação do Balanceador de carga, se a porta de sonda também é o serviço de escuta para o próprio serviço. Por exemplo, pode decidir remover uma instância se ele for superior a 90% da CPU e devolver um Estado de HTTP não 200. 

Se utilizar os serviços Cloud e ter funções da web que utilizam w3wp.exe, alcança automáticas, monitorização do seu Web site. Falhas no código do seu site devolver um Estado que não 200 para a sonda de Balanceador de carga.

Um HTTP / HTTPS sonda falha quando:
* Ponto final da sonda devolve um código de resposta HTTP que não 200 (por exemplo, 403, 404 ou 500). Esta ação marca para baixo a sonda de estado de funcionamento imediatamente. 
* Ponto final da sonda não responde durante o período de tempo limite de segundos de 31. Várias solicitações de sondagem ficarem sem resposta antes da sonda é marcada como não está em execução e até que a soma de todos os intervalos de tempo limite foi atingida.
* Ponto final da sonda fecha a ligação através de uma reposição TCP.

#### <a name="resource-manager-templates"></a>Modelos do Resource Manager

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

### <a name="guestagent"></a>Pesquisa do agente convidado (apenas clássica)

Funções de serviço cloud (funções de trabalho e funções da web) usar um agente de convidado para a sonda de monitorização por predefinição.  Uma sonda de agente convidado é uma última configuração do recurso.  Utilize sempre uma sonda de estado de funcionamento explicitamente com um TCP ou uma sonda HTTP. Uma sonda de agente convidado não é tão eficaz quanto sondas explicitamente definidas na maioria dos cenários de aplicação.

Uma sonda de agente convidado é uma verificação do agente convidado dentro da VM. Em seguida, escuta e responde com uma resposta HTTP 200 OK, apenas quando a instância está no estado pronto. (Outros Estados são ocupado, reciclagem ou a parar.)

Para obter mais informações, consulte [configurar o ficheiro de definição de serviço (. csdef) para sondas de estado de funcionamento](https://msdn.microsoft.com/library/azure/ee758710.aspx) ou [comece por criar um balanceador de carga público para os serviços cloud](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services).

Se o agente convidado não responder com HTTP 200 OK, o Balanceador de carga marca a instância como não responsivo. Em seguida, pára a enviar de fluxos para essa instância. O Balanceador de carga continua a verificar a instância. 

Se o agente convidado responde com um HTTP 200, o Balanceador de carga envia novos fluxos a essa instância novamente.

Quando utiliza uma função da web, normalmente, executa o código de site no w3wp.exe, que não é monitorizado pelo Azure agente de recursos de infraestrutura ou de convidado. Falhas no w3wp.exe (por exemplo, as respostas HTTP 500) não são relatadas para o agente convidado. Conseqüentemente, o Balanceador de carga não Use essa instância da rotação.

<a name="health"></a>
## <a name="probehealth"></a>Sonda de estado de funcionamento

Sondas de estado de funcionamento TCP, HTTP e HTTPS são consideradas íntegros e marcar a instância de função em bom estado quando:

* A sonda de estado de funcionamento é efetuada com êxito uma vez depois da VM é inicializado.
* Foi atingido o número especificado de sondas necessária para marcar a instância de função em bom estado.

> [!NOTE]
> Se a sonda de estado de funcionamento varia, o Balanceador de carga aguarda mais antes da funcionalidade guarda a instância de função em bom estado. Este tempo de espera extra protege o utilizador e a infraestrutura e é uma política intencional.

## <a name="probe-count-and-timeout"></a>Contagem de sonda e o tempo limite

Comportamento de sonda depende:

* O número de sondas com êxito, que permitem que uma instância seja marcado como períodos.
* O número de sondas de falha que fazer com que uma instância seja marcado como inativo.

Os valores de tempo limite e o intervalo especificados, determinar se uma instância foi marcada como ou reduzir verticalmente.

## <a name="probedown"></a>Sonda de comportamento

### <a name="tcp-connections"></a>Ligações TCP

Novas ligações de TCP serão concluída com êxito a restantes instâncias de back-end em bom estado.

Se a sonda de estado de funcionamento de uma instância de back-end falha, continuam a conexões TCP estabelecidas para esta instância de back-end.

Se todas as sondas para todas as instâncias de um conjunto de back-end falharem, não existem fluxos novo serão enviados para o conjunto de back-end. Balanceador de carga Standard permitirá estabelecidos fluxos TCP para continuar.  Balanceador de carga básico irá terminar todos os fluxos TCP existentes para o conjunto de back-end.
 
Balanceador de carga é um pass-through (não terminar ligações de TCP) de serviço e o fluxo é sempre entre o aplicativo e sistema operacional convidado da VM e cliente. Um conjunto com todas as sondas para baixo fará com que um front-end não responder a conexão TCP tentativas abertas (SYN) porque não existe nenhuma instância de bom estado de funcionamento de back-end para receber o fluxo e responder com um SYN-ACK.

### <a name="udp-datagrams"></a>Datagramas UDP

Os datagramas UDP serão entregue a instâncias de back-end em bom estado.

UDP é sem ligações e não existe nenhum Estado de fluxo controlado por UDP. Se falhar a sonda de estado de funcionamento de qualquer instância de back-end, os fluxos UDP existentes podem mover para outra instância de bom estado de funcionamento do conjunto de back-end.

Se todas as sondas para todas as instâncias de um conjunto de back-end falharem, os fluxos UDP existentes irão terminar para básico e Standard balanceadores de carga.

<a name="source"></a>
## <a name="probesource"></a>Endereço IP de origem de sonda

Balanceador de carga utiliza um serviço de pesquisa distribuído para o modelo de estado de funcionamento interno. O serviço de pesquisa reside em cada anfitrião onde as VMs e podem ser programada sob demanda para gerar as sondas de estado de funcionamento por configuração do cliente. O tráfego de sonda de estado de funcionamento é diretamente entre o serviço de pesquisa que gera a sonda de estado de funcionamento e o cliente VM. Todas as sondas de estado de funcionamento do Balanceador de carga provêm do endereço IP 168.63.129.16 como a origem.  Pode usar o espaço de endereços IP dentro de uma VNet que não seja RFC1918 espaço.  Utilizar um globalmente reservado, pertencente à Microsoft endereço IP reduz a chance de um conflito de endereços IP com o espaço de endereços IP que utilizar dentro da VNet.  Este endereço IP é o mesmo em todas as regiões e não é alterado e não é um risco de segurança porque apenas o componente de plataforma do Azure interno pode obter um pacote a partir deste endereço IP. 

A etiqueta de serviço de AzureLoadBalancer identifica este endereço IP de origem no seu [grupos de segurança de rede](../virtual-network/security-overview.md) e permite o tráfego de sonda de estado de funcionamento por predefinição.

Além de sondas de estado de funcionamento do Balanceador de carga, as seguintes operações de utilizam este endereço IP:

- Permite que o agente da VM para comunicar com a plataforma sinalizar que está num estado "Pronto"
- Permite a comunicação com o servidor virtual de DNS para fornecer a resolução de nomes filtrada para os clientes que não defina servidores DNS personalizados.  Esta filtragem garante que os clientes só podem resolver os nomes de anfitrião da implementação deles.
- Permite que a VM obter um endereço IP dinâmico do serviço DHCP no Azure.

## <a name="design"></a> Orientações de conceção

Sondas de estado de funcionamento são utilizadas para manter o seu serviço resilientes e permitir que dimensione. Uma configuração incorreta ou um padrão de um design inadequado pode afetar a disponibilidade e escalabilidade do seu serviço. Leia todo este documento e considere o que o impacto para o seu cenário é quando esta resposta de pesquisa está marcada para baixo ou marcados como cópia de segurança e como elas impactam a disponibilidade do seu cenário de aplicação.

Ao conceber o modelo de estado de funcionamento para a sua aplicação, deve sonda uma porta numa instância de back-end que reflete o estado de funcionamento dessa instância __e__ ao serviço de aplicações está a fornecer.  A porta da aplicação e a porta de sonda não precisem de ser o mesmo.  Em alguns cenários, poderá ser desejável para a porta de sonda ser diferente do que a porta de que seu aplicativo fornece o serviço no.  

Por vezes, pode ser útil para a sua aplicação gerar uma resposta de sonda de estado de funcionamento não só a detetar o estado de funcionamento do aplicativo, mas também sinalizar diretamente para o Balanceador de carga, se a sua instância deve receber ou não receber novos fluxos.  Pode manipular a resposta de sonda para permitir que a aplicação entrega backpressure e limitação de novos fluxos para uma instância para criar a sonda de estado de funcionamento a falhar ou se preparar para a manutenção da sua aplicação e iniciar a ser drenado seu cenário.  Ao utilizar o Balanceador de carga Standard, uma [sonda inativo](#probedown) sinal sempre será permitem que os fluxos TCP continuar até o fechamento de ligação ou tempo limite ocioso. 

Para balanceamento de carga UDP, deve gerar um sinal de sonda de estado de funcionamento personalizados a partir da instância de back-end e utilizar a sonda de estado de funcionamento ou um TCP, HTTP ou HTTPS, visando o serviço de escuta correspondente para refletir o estado de funcionamento da sua aplicação de UDP.

Ao usar [regras de balanceamento de carga para portas HA](load-balancer-ha-ports-overview.md) com [Balanceador de carga Standard](load-balancer-standard-overview.md), todas as portas têm balanceada de carga e uma resposta de sonda de estado de funcionamento único deve refletir o estado de toda a instância.

Traduz ou pesquisa de um Estado de funcionamento de proxy por meio da instância que recebe a sonda de estado de funcionamento para outra instância na sua VNet, como esta configuração pode levar a falhas em cascata no seu cenário.  Considere o seguinte cenário: um conjunto de aplicações de terceiros é implementado no conjunto de back-end de um recurso de Balanceador de carga para fornecer dimensionamento e a redundância para os dispositivos e a sonda de estado de funcionamento está configurada para uma porta de sonda que os proxies de aplicação de terceiros ou traduz-se outras máquinas virtuais por trás da aplicação.  Se a pesquisa a mesma porta que estiver a utilizar para traduzir ou pedidos de proxy para outras máquinas virtuais por trás da aplicação, qualquer resposta de pesquisa de uma única máquina virtual por trás da aplicação serão marcadas como a aplicação em si inativos. Esta configuração pode levar a uma falha em cascata do cenário completo de aplicação como resultado de uma instância de back-end único por trás da aplicação.  O acionador pode ser uma falha de intermitente sonda que fará com que o Balanceador de carga marcar para baixo o destino original (a instância da aplicação) e por sua vez, pode desativar seu cenário de aplicativo inteiro. Sonda o estado de funcionamento da aplicação em si em vez disso. A seleção da sonda para determinar o sinal de estado de funcionamento é uma consideração importante para cenários de aplicações virtuais (NVA) de rede e tem de consultar o fornecedor da aplicação para o que é o sinal de estado de funcionamento adequado para estes cenários.

Se não permitir a [IP de origem](#probesource) da sonda nas suas políticas de firewall, a sonda de estado de funcionamento falhará, pois é não é possível alcançar a sua instância.  Por sua vez, o Balanceador de carga marcará-se para baixo da sua instância devido à falha de sonda de estado de funcionamento.  Esta configuração incorreta pode fazer com que o seu cenário de aplicação com balanceamento de carga efetuar a ativação.

Para a sonda de estado de funcionamento do Balanceador de carga marcar a sua instância, **tem** permitir que este endereço IP em qualquer serviço [grupos de segurança de rede](../virtual-network/security-overview.md) e políticas de local firewall.  Por predefinição, todos os grupos de segurança de rede incluem a [etiqueta de serviço](../virtual-network/security-overview.md#service-tags) AzureLoadBalancer para permitir o tráfego de sonda de estado de funcionamento.

Se desejar testar uma falha de sonda de estado de funcionamento ou marcar para baixo de uma instância individual, pode utilizar um [grupos de segurança de rede](../virtual-network/security-overview.md) para bloquear explicitamente a sonda de estado de funcionamento (porta de destino ou [IP de origem](#probesource)) e simular o Falha de uma sonda.

Não configure a sua VNet com o Microsoft pertence o intervalo de endereços IP que contém 168.63.129.16.  Essas configurações irão entrar em conflito com o endereço IP da sonda de estado de funcionamento e podem fazer com que o seu cenário de falha.

Se tiver várias interfaces na sua VM, terá de assegurar a que responder à sonda na interface de que utilizador o recebeu.  Poderá ter de rede de origem traduzir com um endereço este endereço na VM numa base por interface.

Não ative [carimbos de data / TCP](https://tools.ietf.org/html/rfc1323).  Ativar TCP carimbos fará com que sondas de estado de funcionamento para falhar devido a pacotes TCP, que está a ser ignorados pelo convidado da VM pilha de TCP de SO, o que resulta no balanceador de carga a marcação para baixo o respetivo ponto de extremidade.  Carimbos de data / TCP rotineiramente está ativadas por predefinição na segurança de VM imagens codificadas e tem de ser desativada.

## <a name="monitoring"></a>Monitorização

Públicas e internas [Balanceador de carga Standard](load-balancer-standard-overview.md) expor por ponto final e de back-end estado de sonda de estado de funcionamento de instância, como métricas multidimensionais através do Azure Monitor. Estas métricas podem ser consumidas por outros serviços do Azure ou aplicações de terceiro arty. 

Balanceador de carga básico público expõe o estado de sonda de estado de funcionamento resumido por conjunto de back-end através de registos do Azure Monitor.  Registos de Monitor do Azure não estão disponíveis para balanceadores de carga básico interno.  Pode usar [registos do Azure Monitor](load-balancer-monitor-log.md) para verificar o estado de funcionamento de sonda de Balanceador de carga público e a contagem de sonda. O registo pode ser utilizado com o Power BI ou informações operacionais do Azure para fornecer estatísticas sobre o estado de funcionamento do Balanceador de carga.

## <a name="limitations"></a>Limitações

- Sondas HTTPS não suportam autenticação mútua com um certificado de cliente.
- Sondas de estado de funcionamento irão falhar quando os carimbos de data / TCP está ativados.

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre o [Balanceador de Carga Standard](load-balancer-standard-overview.md)
- [Introdução à criação de um balanceador de carga público no Resource Manager com o PowerShell](load-balancer-get-started-internet-arm-ps.md)
- [API de REST para sondas de estado de funcionamento](https://docs.microsoft.com/rest/api/load-balancer/loadbalancerprobes/)
- Pedir novas capacidades de sonda de estado de funcionamento com [Uservoice do Balanceador de carga](https://aka.ms/lbuservoice)

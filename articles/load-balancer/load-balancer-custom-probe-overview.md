---
title: Use Azure Load Balancer investigações de integridade para dimensionar e fornecer alta disponibilidade para seu serviço
titlesuffix: Azure Load Balancer
description: Saiba como usar investigações de integridade para monitorar instâncias por trás Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: 62af688c6090b61f2596ab376cb479c270b87759
ms.sourcegitcommit: f272ba8ecdbc126d22a596863d49e55bc7b22d37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2019
ms.locfileid: "72274110"
---
# <a name="load-balancer-health-probes"></a>Sondas de estado de funcionamento do Balanceador de Carga

Ao usar regras de balanceamento de carga com Azure Load Balancer, você precisa especificar uma investigação de integridade para permitir que Load Balancer detecte o status do ponto de extremidade de back-end.  A configuração das respostas de investigação e investigação de integridade determinam quais instâncias de pool de back-end receberão novos fluxos. Você pode usar investigações de integridade para detectar a falha de um aplicativo em um ponto de extremidade de back-end. Você também pode gerar uma resposta personalizada para uma investigação de integridade e usar a investigação de integridade para controle de fluxo para gerenciar o tempo de inatividade planejado ou de carga. Quando uma investigação de integridade falhar, Load Balancer deixará de enviar novos fluxos para a respectiva instância não íntegra.

As investigações de integridade dão suporte a vários protocolos. A disponibilidade de um protocolo de investigação de integridade específico varia de acordo com Load Balancer SKU.  Além disso, o comportamento do serviço varia de acordo com Load Balancer SKU, conforme mostrado nesta tabela:

| | SKU Standard | SKU Básico |
| --- | --- | --- |
| [Tipos de investigação](#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Comportamento de investigação](#probedown) | Todas as investigações, todos os fluxos de TCP continuam. | Todas as investigações, todos os fluxos de TCP expiram. | 


>[!IMPORTANT]
>Examine este documento em sua totalidade, incluindo [diretrizes de design](#design) importantes abaixo para criar um serviço confiável.

>[!IMPORTANT]
>Load Balancer investigações de integridade se originam do endereço IP 168.63.129.16 e não devem ser bloqueadas para investigações para marcar sua instância.  Examine o [endereço IP de origem da investigação](#probesource) para obter detalhes.

## <a name="probes"></a>Configuração de investigação

A configuração de investigação de integridade consiste nos seguintes elementos:

- Duração do intervalo entre investigações individuais
- Número de respostas de investigação que precisam ser observadas antes da transição da investigação para um estado diferente
- Protocolo da investigação
- Porta da investigação
- Caminho HTTP a ser usado para HTTP GET ao usar investigações HTTP (S)

## <a name="understanding-application-signal-detection-of-the-signal-and-reaction-of-the-platform"></a>Compreendendo o sinal do aplicativo, a detecção do sinal e a reação da plataforma

O número de respostas de investigação se aplica a ambos

- o número de investigações bem-sucedidas que permitem que uma instância seja marcada como ativa e
- o número de investigações com falha que fazem com que uma instância seja marcada como inativa.

Os valores de tempo limite e intervalo especificados determinam se uma instância será marcada como up ou down.  A duração do intervalo multiplicado pelo número de respostas de investigação determina a duração durante a qual as respostas de investigação precisam ser detectadas.  E o serviço reagirá depois que as investigações necessárias tiverem sido atingidas.

Podemos ilustrar o comportamento mais detalhadamente com um exemplo. Se você tiver definido o número de respostas de investigação como 2 e o intervalo como 5 segundos, isso significa que 2 falhas de investigação devem ser observadas em um intervalo de 10 segundos.  Como a hora em que uma investigação é enviada não é sincronizada quando seu aplicativo pode mudar de estado, podemos associar o tempo para detectar por dois cenários:

1. Se seu aplicativo começar a produzir uma resposta de investigação com falha logo antes da chegada da primeira investigação, a detecção desses eventos levará 10 segundos (intervalos de 2 x 5 segundos) mais a duração do aplicativo que começa a sinalizar uma falha em quando o primeiro a investigação chega.  Você pode assumir que essa detecção leva um pouco mais de 10 segundos.
2. Se seu aplicativo começar a produzir uma resposta de investigação com falha logo após a chegada da primeira investigação, a detecção desses eventos não começará até que a próxima investigação chegue (e falhe) Além de outros 10 segundos (intervalos de 2 x 5 segundos).  Você pode assumir que essa detecção leva menos de 15 segundos.

Para este exemplo, depois que a detecção tiver ocorrido, a plataforma levará um pouco de tempo para reagir a essa alteração.  Isso significa um dependendo de 

1. Quando o aplicativo começa a mudar o estado e
2. Quando essa alteração é detectada e atende aos critérios necessários (número de investigações enviadas no intervalo especificado) e
3. Quando a detecção foi comunicada na plataforma 

Você pode assumir que a reação a uma investigação com falha levará entre um mínimo de 10 segundos e um número um pouco mais de 15 segundos para reagir a uma alteração no sinal do aplicativo.  Este exemplo é fornecido para ilustrar o que está ocorrendo, no entanto, não é possível prever uma duração exata além das diretrizes aproximadas acima ilustradas neste exemplo.
 
## <a name="types"></a>Tipos de investigação

O protocolo usado pela investigação de integridade pode ser configurado para um dos seguintes:

- [Ouvintes TCP](#tcpprobe)
- [Pontos de extremidade HTTP](#httpprobe)
- [Pontos de extremidade HTTPS](#httpsprobe)

Os protocolos disponíveis dependem do Load Balancer SKU usado:

|| TCP | HTTP | HTTPS |
| --- | --- | --- | --- |
| SKU Standard |    &#9989; |   &#9989; |   &#9989; |
| SKU Básico |   &#9989; |   &#9989; | &#10060; |

### <a name="tcpprobe"></a>Investigação TCP

As investigações TCP iniciam uma conexão executando um handshake TCP de três vias aberta com a porta definida.  As investigações TCP encerram uma conexão com um handshake TCP de fechamento de quatro vias.

O intervalo de investigação mínimo é de 5 segundos e o número mínimo de respostas não íntegras é 2.  A duração total de todos os intervalos não pode exceder 120 segundos.

Uma investigação TCP falha quando:
* O ouvinte TCP na instância não responde durante o período de tempo limite.  Uma investigação é marcada com base no número de solicitações de investigação com falha, que foram configuradas para não responder antes de marcar a investigação.
* A investigação recebe uma redefinição de TCP da instância.

O seguinte ilustra como você pode expressar esse tipo de configuração de investigação em um modelo do Resource Manager:

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

### <a name="httpprobe"></a><a name="httpsprobe"></a> Investigação de http/https

>[!NOTE]
>A investigação de HTTPS só está disponível para [Standard Load Balancer](load-balancer-standard-overview.md).

As investigações HTTP e HTTPS são compiladas na investigação TCP e emitem um GET HTTP com o caminho especificado. Ambas as investigações dão suporte a caminhos relativos para o HTTP GET. As investigações de HTTPS são as mesmas que as investigações HTTP com a adição de um invólucro de segurança de camada de transporte (TLS, anteriormente conhecido como SSL). A investigação de integridade é marcada quando a instância responde com um status HTTP 200 dentro do período de tempo limite.  A investigação de integridade tenta verificar a porta de investigação de integridade configurada a cada 15 segundos por padrão. O intervalo de investigação mínimo é de 5 segundos. A duração total de todos os intervalos não pode exceder 120 segundos.

As investigações HTTP/HTTPS também podem ser úteis para implementar sua própria lógica para remover instâncias da rotação do balanceador de carga se a porta de investigação também for o ouvinte para o próprio serviço. Por exemplo, você pode decidir remover uma instância se ela estiver acima de 90% de CPU e retornar um status de HTTP não 200. 

Se você usar os serviços de nuvem e tiver funções Web que usam w3wp. exe, também obterá o monitoramento automático do seu site. As falhas no código do seu site retornam um status diferente de 200 para a investigação do balanceador de carga.

Uma investigação de HTTP/HTTPS falha quando:
* O ponto de extremidade de investigação retorna um código de resposta HTTP diferente de 200 (por exemplo, 403, 404 ou 500). Isso marcará a investigação de integridade imediatamente. 
* O ponto de extremidade de investigação não responde durante o período de tempo limite de 31 segundos. Várias solicitações de investigação podem ficar sem resposta antes que a investigação seja marcada como não sendo executada e até que a soma de todos os intervalos de tempo limite tenha sido atingida.
* O ponto de extremidade de investigação fecha a conexão por meio de uma redefinição de TCP.

O seguinte ilustra como você pode expressar esse tipo de configuração de investigação em um modelo do Resource Manager:

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

### <a name="guestagent"></a>Investigação do agente convidado (somente clássico)

As funções de serviço de nuvem (funções de trabalho e funções da Web) usam um agente convidado para monitoramento de investigação por padrão.  Uma investigação de agente convidado é uma configuração de último recurso.  Sempre use uma investigação de integridade explicitamente com uma investigação TCP ou HTTP. Uma investigação de agente convidado não é tão eficaz quanto as investigações definidas explicitamente para a maioria dos cenários de aplicativo.

Uma investigação de agente convidado é uma verificação do agente convidado dentro da VM. Em seguida, ele escuta e responde com uma resposta HTTP 200 OK somente quando a instância está no estado pronto. (Outros Estados estão ocupados, reciclando ou parando.)

Para obter mais informações, consulte [Configurar o arquivo de definição de serviço (csdef) para investigações de integridade](https://msdn.microsoft.com/library/azure/ee758710.aspx) ou começar [criando um balanceador de carga público para serviços de nuvem](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services).

Se o agente convidado não responder com HTTP 200 OK, o balanceador de carga marcará a instância como sem resposta. Em seguida, ele para de enviar fluxos para essa instância. O balanceador de carga continua verificando a instância. 

Se o agente convidado responder com um HTTP 200, o balanceador de carga enviará novos fluxos para essa instância novamente.

Quando você usa uma função Web, o código do site normalmente é executado em w3wp. exe, que não é monitorado pela malha do Azure ou pelo agente convidado. As falhas no w3wp. exe (por exemplo, respostas HTTP 500) não são relatadas ao agente convidado. Consequentemente, o balanceador de carga não tira essa instância da rotação.

<a name="health"></a>
## <a name="probehealth"></a>Comportamento de investigação

As investigações de integridade TCP, HTTP e HTTPS são consideradas íntegras e marcam o ponto de extremidade back-end como íntegro quando:

* A investigação de integridade é bem-sucedida uma vez após a inicialização da VM.
* O número especificado de investigações necessárias para marcar o ponto de extremidade de back-end como íntegro foi atingido.

Qualquer ponto de extremidade de back-end que tenha atingido um estado íntegro é qualificado para receber novos fluxos.  

> [!NOTE]
> Se a investigação de integridade flutuar, o balanceador de carga aguarda mais tempo antes de colocar o ponto de extremidade de back-end no estado íntegro. Esse tempo de espera extra protege o usuário e a infraestrutura e é uma política intencional.

## <a name="probedown"></a>Comportamento de investigação

### <a name="tcp-connections"></a>Conexões TCP

As novas conexões TCP terão sucesso no ponto de extremidade de back-end íntegro.

Se uma investigação de integridade do ponto de extremidade de back-end falhar, as conexões TCP estabelecidas com esse ponto de extremidade de back-end

Se todas as investigações de todas as instâncias em um pool de back-end falharem, nenhum fluxo novo será enviado para o pool de back-end. Standard Load Balancer permitirá que fluxos TCP sejam estabelecidos para continuar.  O Load Balancer básico encerrará todos os fluxos TCP existentes para o pool de back-end.
 
Load Balancer é um serviço de passagem (não encerra as conexões TCP) e o fluxo está sempre entre o cliente e o sistema operacional convidado e o aplicativo da VM. Um pool com todas as investigações fará com que um front-end não responda a SYN (tentativas de abertura de conexão TCP), pois não há nenhum ponto de extremidade de back-end íntegro para receber o fluxo e responder com um SYN-ACK.

### <a name="udp-datagrams"></a>Datagramas UDP

Os datagramas UDP serão entregues aos pontos de extremidade de back-end íntegros.

O UDP é sem conexão e não há um estado de fluxo rastreado para UDP. Se qualquer investigação de integridade do ponto de extremidade de back-end falhar, os fluxos UDP existentes poderão ser movidos para outra instância íntegra no pool de back-end.

Se todas as investigações de todas as instâncias em um pool de back-end falharem, os fluxos UDP existentes serão encerrados para os balanceadores de carga básico e Standard.

<a name="source"></a>
## <a name="probesource"></a>Endereço IP de origem da investigação

Load Balancer usa um serviço de investigação distribuído para seu modelo de integridade interno. O serviço de investigação reside em cada host em que as VMs podem ser programadas sob demanda para gerar investigações de integridade de acordo com a configuração do cliente. O tráfego de investigação de integridade é diretamente entre o serviço de investigação que gera a investigação de integridade e a VM do cliente. Todas as investigações de integridade Load Balancer originam-se do endereço IP 168.63.129.16 como sua origem.  Você pode usar o espaço de endereço IP dentro de uma VNet que não é RFC1918 espaço.  Usando um endereço IP de propriedade da Microsoft reservado globalmente, reduz a possibilidade de um conflito de endereço IP com o espaço de endereço IP usado na VNet.  Esse endereço IP é o mesmo em todas as regiões e não é alterado e não é um risco de segurança porque apenas o componente da plataforma interna do Azure pode originar um pacote desse endereço IP. 

A marca de serviço AzureLoadBalancer identifica esse endereço IP de origem em seus [grupos de segurança de rede](../virtual-network/security-overview.md) e permite o tráfego de investigação de integridade por padrão.

Além de Load Balancer investigações de integridade, as [seguintes operações usam esse endereço IP](../virtual-network/what-is-ip-address-168-63-129-16.md):

- Permite que o agente de VM se comunique com a plataforma para sinalizar que está em um estado "pronto"
- Permite a comunicação com o servidor virtual DNS para fornecer resolução de nomes filtrados para clientes que não definem servidores DNS personalizados.  Essa filtragem garante que os clientes só possam resolver os nomes de host de sua implantação.
- Permite que a VM obtenha um endereço IP dinâmico do serviço DHCP no Azure.

## <a name="design"></a>Diretrizes de design

As investigações de integridade são usadas para tornar seu serviço resiliente e permitir que ele seja dimensionado. Um padrão de configuração incorreta ou de design inadequado pode afetar a disponibilidade e a escalabilidade do seu serviço. Examine este documento inteiro e considere qual é o impacto em seu cenário quando essa resposta de investigação é marcada ou marcada, e como ela afeta a disponibilidade do cenário do aplicativo.

Quando você cria o modelo de integridade para seu aplicativo, você deve investigar uma porta em um ponto de extremidade de back-end que reflita a integridade dessa instância __e__ o serviço de aplicativo que você está fornecendo.  A porta do aplicativo e a porta de investigação não precisam ser as mesmas.  Em alguns cenários, pode ser desejável que a porta de investigação seja diferente da porta em que seu aplicativo fornece serviço.  

Às vezes, pode ser útil para seu aplicativo gerar uma resposta de investigação de integridade para não apenas detectar a integridade do aplicativo, mas também sinalizar diretamente para Load Balancer se sua instância deve receber ou não receber novos fluxos.  Você pode manipular a resposta de investigação para permitir que seu aplicativo crie uma pressão e limite a entrega de novos fluxos a uma instância, falhando na investigação de integridade ou se preparar para manutenção do seu aplicativo e começar a drenar seu cenário.  Ao usar Standard Load Balancer, um sinal de [investigação inativo](#probedown) sempre permitirá que os fluxos TCP continuem até o tempo limite ocioso ou o encerramento da conexão. 

Para o balanceamento de carga de UDP, você deve gerar um sinal de investigação de integridade personalizado do ponto de extremidade de back-end e usar uma investigação de integridade TCP, HTTP ou HTTPS direcionando o ouvinte correspondente para refletir a integridade do aplicativo UDP.

Ao usar [regras de balanceamento de carga de portas de alta disponibilidade](load-balancer-ha-ports-overview.md) com [Standard Load Balancer](load-balancer-standard-overview.md), todas as portas têm balanceamento de carga e uma única resposta de investigação de integridade deve refletir o status da instância inteira.

Não traduza ou faça proxy de uma investigação de integridade por meio da instância que recebe a investigação de integridade para outra instância em sua VNet, pois essa configuração pode levar a falhas em cascata em seu cenário.  Considere o seguinte cenário: um conjunto de dispositivos de terceiros é implantado no pool de back-end de um recurso de Load Balancer para fornecer escala e redundância para os dispositivos e a investigação de integridade é configurada para investigar uma porta que os proxies de dispositivo de terceiros ou traduz para outras máquinas virtuais por trás do dispositivo.  Se você investigar a mesma porta que está usando para traduzir ou solicitações de proxy para as outras máquinas virtuais por trás do dispositivo, qualquer resposta de investigação de uma única máquina virtual por trás do dispositivo marcará o dispositivo em si. Essa configuração pode levar a uma falha em cascata de todo o cenário do aplicativo como resultado de um único ponto de extremidade de back-end por trás do dispositivo.  O gatilho pode ser uma falha de investigação intermitente que fará Load Balancer marcar o destino original (a instância do dispositivo) e, por sua vez, poderá desabilitar todo o cenário do aplicativo. Investigue a integridade do próprio dispositivo em vez disso. A seleção da investigação para determinar o sinal de integridade é uma consideração importante para cenários de NVA (soluções de virtualização de rede) e você deve consultar o fornecedor do aplicativo para saber qual é o sinal de integridade apropriado para esses cenários.

Se você não permitir o [IP de origem](#probesource) da investigação em suas políticas de firewall, a investigação de integridade falhará, pois não é possível acessar sua instância.  Por sua vez, Load Balancer irá marcar sua instância devido à falha de investigação de integridade.  Essa configuração incorreta pode causar falha no cenário do aplicativo com balanceamento de carga.

Para a investigação de integridade de Load Balancer para marcar sua instância, você **deve** permitir esse endereço IP em quaisquer [grupos de segurança de rede](../virtual-network/security-overview.md) e políticas de firewall locais do Azure.  Por padrão, cada grupo de segurança de rede inclui a [marca de serviço](../virtual-network/security-overview.md#service-tags) AzureLoadBalancer para permitir o tráfego de investigação de integridade.

Se você quiser testar uma falha de investigação de integridade ou marcar uma instância individual, poderá usar um [grupo de segurança de rede](../virtual-network/security-overview.md) para bloquear explicitamente a investigação de integridade (porta de destino ou IP de [origem](#probesource)) e simular a falha de uma investigação.

Não configure sua VNet com o intervalo de endereços IP de propriedade da Microsoft que contém 168.63.129.16.  Essas configurações colidirão com o endereço IP da investigação de integridade e poderão causar falha no cenário.

Se você tiver várias interfaces em sua VM, você precisará garantir que você responda à investigação na interface em que você a recebeu.  Talvez seja necessário que o endereço de rede de origem converta esse endereço na VM por interface.

Não habilite [carimbos de data/hora TCP](https://tools.ietf.org/html/rfc1323).  Habilitar carimbos de data/hora TCP pode causar falha em investigações de integridade devido a pacotes TCP sendo descartados pela pilha TCP do sistema operacional convidado da VM, o que resulta em Load Balancer marcando o respectivo ponto de extremidade.  Os carimbos de data/hora TCP são rotineiramente habilitados por padrão em imagens de VM protegidas por segurança e devem ser desabilitados.

## <a name="monitoring"></a>Monitorização

Os [Standard Load Balancer](load-balancer-standard-overview.md) públicos e internos expõem por ponto de extremidade e status de investigação de integridade do ponto de extremidade de back-end como métricas multidimensionais através de Azure monitor Essas métricas podem ser consumidas por outros serviços do Azure ou aplicativos de parceiro. 

O Load Balancer público básico expõe o status de investigação de integridade resumido por pool de back-end por meio de logs Azure Monitor.  Os logs de Azure Monitor não estão disponíveis para balanceadores de carga básicos internos.  Você pode usar [os logs de Azure monitor](load-balancer-monitor-log.md) para verificar o status de integridade da investigação do balanceador de carga público e a contagem de investigação. O registro em log pode ser usado com Power BI ou informações operacionais do Azure para fornecer estatísticas sobre o status de integridade do balanceador de carga.

## <a name="limitations"></a>Limitações

- As investigações HTTPS não dão suporte à autenticação mútua com um certificado de cliente.
- Você deve assumehHealth investigações falharão quando os carimbos de data/hora TCP estiverem habilitados.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o [Balanceador de Carga Standard](load-balancer-standard-overview.md)
- [Introdução à criação de um balanceador de carga público no Gerenciador de recursos usando o PowerShell](load-balancer-get-started-internet-arm-ps.md)
- [API REST para investigações de integridade](https://docs.microsoft.com/rest/api/load-balancer/loadbalancerprobes/)
- Solicitar novas capacidades de investigação de integridade com [UserVoice de Load Balancer](https://aka.ms/lbuservoice)

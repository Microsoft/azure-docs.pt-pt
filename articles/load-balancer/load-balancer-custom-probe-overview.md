---
title: Use Azure Load Balancer investigações de integridade para dimensionar e fornecer alta disponibilidade para seu serviço
titlesuffix: Azure Load Balancer
description: Saiba como utilizar sondas de estado de funcionamento para monitorizar instâncias por trás do Balanceador de carga
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
ms.openlocfilehash: d9873c4d8d4ca2b74532706f079c7384bf38db42
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033823"
---
# <a name="load-balancer-health-probes"></a>Sondas de estado de funcionamento do Balanceador de Carga

Ao usar regras de balanceamento de carga com Azure Load Balancer, você precisa especificar uma investigação de integridade para permitir que Load Balancer detecte o status do ponto de extremidade de back-end.  A configuração das respostas de investigação e investigação de integridade determinam quais instâncias de pool de back-end receberão novos fluxos. Você pode usar investigações de integridade para detectar a falha de um aplicativo em um ponto de extremidade de back-end. Você também pode gerar uma resposta personalizada para uma investigação de integridade e usar a investigação de integridade para controle de fluxo para gerenciar o tempo de inatividade planejado ou de carga. Quando uma investigação de integridade falhar, Load Balancer deixará de enviar novos fluxos para a respectiva instância não íntegra.

As investigações de integridade dão suporte a vários protocolos. A disponibilidade de um protocolo de investigação de integridade específico varia de acordo com Load Balancer SKU.  Além disso, o comportamento do serviço varia de acordo com Load Balancer SKU, conforme mostrado nesta tabela:

| | SKU Standard | SKU Básico |
| --- | --- | --- |
| [Tipos de sonda](#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Sonda de comportamento](#probedown) | Todas as sondas, continuam a todos os fluxos TCP. | Todas as investigações, todos os fluxos de TCP expiram. | 


>[!IMPORTANT]
>Examine este documento em sua totalidade, incluindo [diretrizes de design](#design) importantes abaixo para criar um serviço confiável.

>[!IMPORTANT]
>Load Balancer investigações de integridade se originam do endereço IP 168.63.129.16 e não devem ser bloqueadas para investigações para marcar sua instância.  Revisão [endereço IP de origem de sonda](#probesource) para obter detalhes.

## <a name="probes"></a>Configuração de investigação

A configuração de investigação de integridade consiste nos seguintes elementos:

- Duração do intervalo entre investigações individuais
- Número de respostas de investigação que precisam ser observadas antes da transição da investigação para um estado diferente
- Protocolo da investigação
- Porta da investigação
- Caminho HTTP a ser usado para HTTP GET ao usar investigações HTTP (S)

> [!NOTE]
> Uma definição de investigação não é obrigatória ou verificada ao usar Azure PowerShell, CLI do Azure, modelos ou API. Testes de validação de investigação só são feitos ao usar o portal do Azure.

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
 
## <a name="types"></a>Tipos de sonda

O protocolo usado pela investigação de integridade pode ser configurado para um dos seguintes:

- [Serviços de escuta TCP](#tcpprobe)
- [Pontos de extremidade HTTP](#httpprobe)
- [Pontos finais de HTTPS](#httpsprobe)

Os protocolos disponíveis dependem do Load Balancer SKU usado:

|| TCP | HTTP | HTTPS |
| --- | --- | --- | --- |
| SKU Standard |    &#9989; |   &#9989; |   &#9989; |
| SKU Básico |   &#9989; |   &#9989; | &#10060; |

### <a name="tcpprobe"></a> Sonda TCP

Sondas TCP iniciam uma ligação ao efetuar um handshake TCP aberto de três vias com a porta definido.  As investigações TCP encerram uma conexão com um handshake TCP de fechamento de quatro vias.

O intervalo de sonda mínimo é de 5 segundos e o número mínimo de respostas de mau estado de funcionamento é 2.  A duração total de todos os intervalos não pode exceder 120 segundos.

Uma sonda TCP falha quando:
* O serviço de escuta TCP na instância não responde durante o período de tempo limite.  Uma investigação é marcada com base no número de solicitações de investigação com falha, que foram configuradas para não responder antes de marcar a investigação.
* A sonda recebe uma reposição da instância TCP.

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

### <a name="httpprobe"></a> <a name="httpsprobe"></a> HTTP / HTTPS de sonda

>[!NOTE]
>Sonda HTTPS apenas está disponível para [Balanceador de carga Standard](load-balancer-standard-overview.md).

As investigações HTTP e HTTPS são compiladas na investigação TCP e emitem um GET HTTP com o caminho especificado. Ambas estas sondas suportam caminhos relativos para o HTTP GET. Sondas HTTPS são os mesmos, como as sondas HTTP com a adição da Transport Layer Security (TLS, anteriormente conhecido como SSL) wrapper. A sonda de estado de funcionamento está marcado como cópia de segurança quando a instância responde com um Estado HTTP 200 dentro do período de tempo limite.  A investigação de integridade tenta verificar a porta de investigação de integridade configurada a cada 15 segundos por padrão. O intervalo de sonda mínimo é de 5 segundos. A duração total de todos os intervalos não pode exceder 120 segundos.

As investigações HTTP/HTTPS também podem ser úteis para implementar sua própria lógica para remover instâncias da rotação do balanceador de carga se a porta de investigação também for o ouvinte para o próprio serviço. Por exemplo, pode decidir remover uma instância se ele for superior a 90% da CPU e devolver um Estado de HTTP não 200. 

Se utilizar os serviços Cloud e ter funções da web que utilizam w3wp.exe, alcança automáticas, monitorização do seu Web site. Falhas no código do seu site devolver um Estado que não 200 para a sonda de Balanceador de carga.

Um HTTP / HTTPS sonda falha quando:
* Ponto final da sonda devolve um código de resposta HTTP que não 200 (por exemplo, 403, 404 ou 500). Isso marcará a investigação de integridade imediatamente. 
* O ponto de extremidade de investigação não responde durante o período de tempo limite de 31 segundos. Várias solicitações de investigação podem ficar sem resposta antes que a investigação seja marcada como não sendo executada e até que a soma de todos os intervalos de tempo limite tenha sido atingida.
* Ponto final da sonda fecha a ligação através de uma reposição TCP.

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

### <a name="guestagent"></a>Pesquisa do agente convidado (apenas clássica)

Funções de serviço cloud (funções de trabalho e funções da web) usar um agente de convidado para a sonda de monitorização por predefinição.  Uma investigação de agente convidado é uma configuração de último recurso.  Sempre use uma investigação de integridade explicitamente com uma investigação TCP ou HTTP. Uma sonda de agente convidado não é tão eficaz quanto sondas explicitamente definidas na maioria dos cenários de aplicação.

Uma sonda de agente convidado é uma verificação do agente convidado dentro da VM. Em seguida, escuta e responde com uma resposta HTTP 200 OK, apenas quando a instância está no estado pronto. (Outros Estados são ocupado, reciclagem ou a parar.)

Para obter mais informações, consulte [configurar o ficheiro de definição de serviço (. csdef) para sondas de estado de funcionamento](https://msdn.microsoft.com/library/azure/ee758710.aspx) ou [comece por criar um balanceador de carga público para os serviços cloud](https://docs.microsoft.com/azure/load-balancer/load-balancer-get-started-internet-classic-cloud#check-load-balancer-health-status-for-cloud-services).

Se o agente convidado não responder com HTTP 200 OK, o Balanceador de carga marca a instância como não responsivo. Em seguida, pára a enviar de fluxos para essa instância. O Balanceador de carga continua a verificar a instância. 

Se o agente convidado responde com um HTTP 200, o Balanceador de carga envia novos fluxos a essa instância novamente.

Quando utiliza uma função da web, normalmente, executa o código de site no w3wp.exe, que não é monitorizado pelo Azure agente de recursos de infraestrutura ou de convidado. Falhas no w3wp.exe (por exemplo, as respostas HTTP 500) não são relatadas para o agente convidado. Conseqüentemente, o Balanceador de carga não Use essa instância da rotação.

<a name="health"></a>
## <a name="probehealth"></a>Comportamento de investigação

As investigações de integridade TCP, HTTP e HTTPS são consideradas íntegras e marcam o ponto de extremidade back-end como íntegro quando:

* A investigação de integridade é bem-sucedida uma vez após a inicialização da VM.
* O número especificado de investigações necessárias para marcar o ponto de extremidade de back-end como íntegro foi atingido.

Qualquer ponto de extremidade de back-end que tenha atingido um estado íntegro é qualificado para receber novos fluxos.  

> [!NOTE]
> Se a investigação de integridade flutuar, o balanceador de carga aguarda mais tempo antes de colocar o ponto de extremidade de back-end no estado íntegro. Este tempo de espera extra protege o utilizador e a infraestrutura e é uma política intencional.

## <a name="probedown"></a>Sonda de comportamento

### <a name="tcp-connections"></a>Ligações TCP

As novas conexões TCP terão sucesso no ponto de extremidade de back-end íntegro.

Se uma investigação de integridade do ponto de extremidade de back-end falhar, as conexões TCP estabelecidas com esse ponto de extremidade de back-end

Se todas as sondas para todas as instâncias de um conjunto de back-end falharem, não existem fluxos novo serão enviados para o conjunto de back-end. Balanceador de carga Standard permitirá estabelecidos fluxos TCP para continuar.  Balanceador de carga básico irá terminar todos os fluxos TCP existentes para o conjunto de back-end.
 
Load Balancer é um serviço de passagem (não encerra as conexões TCP) e o fluxo está sempre entre o cliente e o sistema operacional convidado e o aplicativo da VM. Um pool com todas as investigações fará com que um front-end não responda a SYN (tentativas de abertura de conexão TCP), pois não há nenhum ponto de extremidade de back-end íntegro para receber o fluxo e responder com um SYN-ACK.

### <a name="udp-datagrams"></a>Datagramas UDP

Os datagramas UDP serão entregues aos pontos de extremidade de back-end íntegros.

UDP é sem ligações e não existe nenhum Estado de fluxo controlado por UDP. Se qualquer investigação de integridade do ponto de extremidade de back-end falhar, os fluxos UDP existentes poderão ser movidos para outra instância íntegra no pool de back-end.

Se todas as sondas para todas as instâncias de um conjunto de back-end falharem, os fluxos UDP existentes irão terminar para básico e Standard balanceadores de carga.

<a name="source"></a>
## <a name="probesource"></a>Endereço IP de origem de sonda

Balanceador de carga utiliza um serviço de pesquisa distribuído para o modelo de estado de funcionamento interno. O serviço de investigação reside em cada host em que as VMs podem ser programadas sob demanda para gerar investigações de integridade de acordo com a configuração do cliente. O tráfego de investigação de integridade é diretamente entre o serviço de investigação que gera a investigação de integridade e a VM do cliente. Todas as sondas de estado de funcionamento do Balanceador de carga provêm do endereço IP 168.63.129.16 como a origem.  Você pode usar o espaço de endereço IP dentro de uma VNet que não é RFC1918 espaço.  Usando um endereço IP de propriedade da Microsoft reservado globalmente, reduz a possibilidade de um conflito de endereço IP com o espaço de endereço IP usado na VNet.  Esse endereço IP é o mesmo em todas as regiões e não é alterado e não é um risco de segurança porque apenas o componente da plataforma interna do Azure pode originar um pacote desse endereço IP. 

A marca de serviço AzureLoadBalancer identifica esse endereço IP de origem em seus [grupos de segurança de rede](../virtual-network/security-overview.md) e permite o tráfego de investigação de integridade por padrão.

Além de Load Balancer investigações de integridade, as [seguintes operações usam esse endereço IP](../virtual-network/what-is-ip-address-168-63-129-16.md):

- Permite que o agente da VM para comunicar com a plataforma sinalizar que está num estado "Pronto"
- Permite a comunicação com o servidor virtual de DNS para fornecer a resolução de nomes filtrada para os clientes que não defina servidores DNS personalizados.  Esta filtragem garante que os clientes só podem resolver os nomes de anfitrião da implementação deles.
- Permite que a VM obtenha um endereço IP dinâmico do serviço DHCP no Azure.

## <a name="design"></a>Diretrizes de design

As investigações de integridade são usadas para tornar seu serviço resiliente e permitir que ele seja dimensionado. Um padrão de configuração incorreta ou de design inadequado pode afetar a disponibilidade e a escalabilidade do seu serviço. Examine este documento inteiro e considere qual é o impacto em seu cenário quando essa resposta de investigação é marcada ou marcada, e como ela afeta a disponibilidade do cenário do aplicativo.

Quando você cria o modelo de integridade para seu aplicativo, você deve investigar uma porta em um ponto de extremidade de back-end que reflita a integridade dessa instância __e__ o serviço de aplicativo que você está fornecendo.  A porta do aplicativo e a porta de investigação não precisam ser as mesmas.  Em alguns cenários, pode ser desejável que a porta de investigação seja diferente da porta em que seu aplicativo fornece serviço.  

Às vezes, pode ser útil para seu aplicativo gerar uma resposta de investigação de integridade para não apenas detectar a integridade do aplicativo, mas também sinalizar diretamente para Load Balancer se sua instância deve receber ou não receber novos fluxos.  Você pode manipular a resposta de investigação para permitir que seu aplicativo crie uma pressão e limite a entrega de novos fluxos a uma instância, falhando na investigação de integridade ou se preparar para manutenção do seu aplicativo e começar a drenar seu cenário.  Ao usar Standard Load Balancer, um sinal de [investigação inativo](#probedown) sempre permitirá que os fluxos TCP continuem até o tempo limite ocioso ou o encerramento da conexão. 

Para o balanceamento de carga de UDP, você deve gerar um sinal de investigação de integridade personalizado do ponto de extremidade de back-end e usar uma investigação de integridade TCP, HTTP ou HTTPS direcionando o ouvinte correspondente para refletir a integridade do aplicativo UDP.

Ao usar [regras de balanceamento de carga de portas de alta disponibilidade](load-balancer-ha-ports-overview.md) com [Standard Load Balancer](load-balancer-standard-overview.md), todas as portas têm balanceamento de carga e uma única resposta de investigação de integridade deve refletir o status da instância inteira.

Não traduza ou faça proxy de uma investigação de integridade por meio da instância que recebe a investigação de integridade para outra instância em sua VNet, pois essa configuração pode levar a falhas em cascata em seu cenário.  Considere o seguinte cenário: um conjunto de dispositivos de terceiros é implantado no pool de back-end de um recurso de Load Balancer para fornecer escala e redundância para os dispositivos e a investigação de integridade é configurada para investigar uma porta que os proxies de dispositivo de terceiros ou traduz para outras máquinas virtuais por trás do dispositivo.  Se você investigar a mesma porta que está usando para traduzir ou solicitações de proxy para as outras máquinas virtuais por trás do dispositivo, qualquer resposta de investigação de uma única máquina virtual por trás do dispositivo marcará o dispositivo em si. Essa configuração pode levar a uma falha em cascata de todo o cenário do aplicativo como resultado de um único ponto de extremidade de back-end por trás do dispositivo.  O gatilho pode ser uma falha de investigação intermitente que fará Load Balancer marcar o destino original (a instância do dispositivo) e, por sua vez, poderá desabilitar todo o cenário do aplicativo. Investigue a integridade do próprio dispositivo em vez disso. A seleção da investigação para determinar o sinal de integridade é uma consideração importante para cenários de NVA (soluções de virtualização de rede) e você deve consultar o fornecedor do aplicativo para saber qual é o sinal de integridade apropriado para esses cenários.

Se você não permitir o [IP de origem](#probesource) da investigação em suas políticas de firewall, a investigação de integridade falhará, pois não é possível acessar sua instância.  Por sua vez, o Balanceador de carga marcará-se para baixo da sua instância devido à falha de sonda de estado de funcionamento.  Essa configuração incorreta pode causar falha no cenário do aplicativo com balanceamento de carga.

Para a investigação de integridade de Load Balancer para marcar sua instância, você **deve** permitir esse endereço IP em quaisquer [grupos de segurança de rede](../virtual-network/security-overview.md) e políticas de firewall locais do Azure.  Por padrão, cada grupo de segurança de rede inclui a [marca de serviço](../virtual-network/security-overview.md#service-tags) AzureLoadBalancer para permitir o tráfego de investigação de integridade.

Se você quiser testar uma falha de investigação de integridade ou marcar uma instância individual, poderá usar um [grupo de segurança de rede](../virtual-network/security-overview.md) para bloquear explicitamente a investigação de integridade (porta de destino ou IP de [origem](#probesource)) e simular a falha de uma investigação.

Não configure sua VNet com o intervalo de endereços IP de propriedade da Microsoft que contém 168.63.129.16.  Essas configurações colidirão com o endereço IP da investigação de integridade e poderão causar falha no cenário.

Se tiver várias interfaces na sua VM, terá de assegurar a que responder à sonda na interface de que utilizador o recebeu.  Talvez seja necessário que o endereço de rede de origem converta esse endereço na VM por interface.

Não habilite [carimbos de data/hora TCP](https://tools.ietf.org/html/rfc1323).  Habilitar carimbos de data/hora TCP pode causar falha em investigações de integridade devido a pacotes TCP sendo descartados pela pilha TCP do sistema operacional convidado da VM, o que resulta em Load Balancer marcando o respectivo ponto de extremidade.  Os carimbos de data/hora TCP são rotineiramente habilitados por padrão em imagens de VM protegidas por segurança e devem ser desabilitados.

## <a name="monitoring"></a>Monitorização

Os [Standard Load Balancer](load-balancer-standard-overview.md) públicos e internos expõem por ponto de extremidade e status de investigação de integridade do ponto de extremidade de back-end como métricas multidimensionais através de Azure monitor Essas métricas podem ser consumidas por outros serviços do Azure ou aplicativos de parceiro. 

O Load Balancer público básico expõe o status de investigação de integridade resumido por pool de back-end por meio de logs Azure Monitor.  Os logs de Azure Monitor não estão disponíveis para balanceadores de carga básicos internos.  Você pode usar [os logs de Azure monitor](load-balancer-monitor-log.md) para verificar o status de integridade da investigação do balanceador de carga público e a contagem de investigação. O registo pode ser utilizado com o Power BI ou informações operacionais do Azure para fornecer estatísticas sobre o estado de funcionamento do Balanceador de carga.

## <a name="limitations"></a>Limitações

- Sondas HTTPS não suportam autenticação mútua com um certificado de cliente.
- Você deve assumehHealth investigações falharão quando os carimbos de data/hora TCP estiverem habilitados.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o [Balanceador de Carga Standard](load-balancer-standard-overview.md)
- [Introdução à criação de um balanceador de carga público no Resource Manager com o PowerShell](load-balancer-get-started-internet-arm-ps.md)
- [API de REST para sondas de estado de funcionamento](https://docs.microsoft.com/rest/api/load-balancer/loadbalancerprobes/)
- Pedir novas capacidades de sonda de estado de funcionamento com [Uservoice do Balanceador de carga](https://aka.ms/lbuservoice)

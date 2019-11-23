---
title: Resolver problemas do Balanceador de Carga do Azure
description: Saiba como solucionar problemas conhecidos com o Azure Load Balancer.
services: load-balancer
documentationcenter: na
author: asudbring
manager: dcscontentpm
ms.custom: seodoc18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/19/2019
ms.author: allensu
ms.openlocfilehash: eab86b3643dde2a6e854d73c38b5267c65fb7e3e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74214769"
---
# <a name="troubleshoot-azure-load-balancer"></a>Resolver problemas do Balanceador de Carga do Azure

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Esta página fornece informações de solução de problemas comuns de Azure Load Balancer. Quando a conectividade do Load Balancer não está disponível, os sintomas mais comuns são os seguintes: 
- As VMs por trás da Load Balancer não estão respondendo às investigações de integridade 
- As VMs por trás da Load Balancer não estão respondendo ao tráfego na porta configurada

Quando os clientes externos para as VMs de back-end passam pelo balanceador de carga, o endereço IP dos clientes será usado para a comunicação. Verifique se o endereço IP dos clientes foi adicionado à lista de permissões NSG. 

## <a name="symptom-vms-behind-the-load-balancer-are-not-responding-to-health-probes"></a>Sintoma: as VMs por trás da Load Balancer não estão respondendo a investigações de integridade
Para que os servidores de back-end participem do conjunto de balanceadores de carga, eles devem passar na verificação de investigação. Para obter mais informações sobre investigações de integridade, consulte [noções básicas sobre investigações de Load Balancer](load-balancer-custom-probe-overview.md). 

As VMs do pool de back-end Load Balancer podem não estar respondendo às investigações devido a qualquer um dos seguintes motivos: 
- Load Balancer VM do pool de back-end não está íntegra 
- Load Balancer VM do pool de back-end não está escutando na porta de investigação 
- O firewall ou um grupo de segurança de rede está bloqueando a porta nas VMs do pool de back-end Load Balancer 
- Outras configurações incorretas no Load Balancer

### <a name="cause-1-load-balancer-backend-pool-vm-is-unhealthy"></a>Causa 1: Load Balancer VM do pool de back-end não está íntegra 

**Validação e resolução**

Para resolver esse problema, faça logon nas VMs participantes e verifique se o estado da VM é íntegro e pode responder a **PsPing** ou **TCPING** de outra VM no pool. Se a VM não estiver íntegra ou não puder responder à investigação, você deverá retificar o problema e colocar a VM de volta em estado íntegro antes de poder participar do balanceamento de carga.

### <a name="cause-2-load-balancer-backend-pool-vm-is-not-listening-on-the-probe-port"></a>Causa 2: Load Balancer VM do pool de back-end não está escutando na porta de investigação
Se a VM estiver íntegra, mas não estiver respondendo à investigação, então um possível motivo pode ser que a porta de investigação não esteja aberta na VM participante ou que a VM não esteja escutando nessa porta.

**Validação e resolução**

1. Faça logon na VM de back-end. 
2. Abra um prompt de comando e execute o seguinte comando para validar se há um aplicativo escutando na porta de investigação:   
            netstat-a
3. Se o estado da porta não estiver listado como **ouvindo**, configure a porta apropriada. 
4. Como alternativa, selecione outra porta, que está listada como **escuta**, e atualize a configuração do balanceador de carga de acordo.              

### <a name="cause-3-firewall-or-a-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vms"></a>Causa 3: o firewall ou um grupo de segurança de rede está bloqueando a porta nas VMs do pool de back-end do balanceador de carga  
Se o firewall na VM estiver bloqueando a porta de investigação, ou se um ou mais grupos de segurança de rede configurados na sub-rede ou na VM, não estiver permitindo que a investigação alcance a porta, a VM não poderá responder à investigação de integridade.          

**Validação e resolução**

* Se o firewall estiver habilitado, verifique se ele está configurado para permitir a porta de investigação. Caso contrário, configure o firewall para permitir o tráfego na porta de investigação e teste novamente. 
* Na lista de grupos de segurança de rede, verifique se o tráfego de entrada ou de saída na porta de investigação tem interferência. 
* Além disso, verifique se uma regra **negar todos os** grupos de segurança de rede na NIC da VM ou na sub-rede que tem uma prioridade mais alta do que a regra padrão que permite investigações lb & tráfego (grupos de segurança de rede devem permitir Load Balancer IP de 168.63.129.16). 
* Se qualquer uma dessas regras estiver bloqueando o tráfego de investigação, remova e reconfigure as regras para permitir o tráfego de investigação.  
* Teste se a VM agora começou a responder às investigações de integridade. 

### <a name="cause-4-other-misconfigurations-in-load-balancer"></a>Causa 4: outras configurações incorretas no Load Balancer
Se todas as causas anteriores parecerem ser validadas e resolvidas corretamente, e a VM de back-end ainda não responder à investigação de integridade, teste manualmente a conectividade e colete alguns rastreamentos para entender a conectividade.

**Validação e resolução**

* Use o **Psping** de uma das outras VMs na VNet para testar a resposta da porta de investigação (exemplo: .\psping.exe-t 10.0.0.4:3389) e registrar os resultados. 
* Use o **TCPing** de uma das outras VMs na VNet para testar a resposta da porta de investigação (exemplo: .\tcping.exe 10.0.0.4 3389) e registrar os resultados. 
* Se nenhuma resposta for recebida nesses testes de ping,
    - Execute um rastreamento netsh simultâneo na VM do pool de back-end de destino e outra VM de teste da mesma VNet. Agora, execute um teste PsPing por algum tempo, colete alguns rastreamentos de rede e pare o teste. 
    - Analise a captura de rede e veja se há pacotes de entrada e de saída relacionados à consulta ping. 
        - Se nenhum pacote de entrada for observado na VM do pool de back-end, potencialmente haverá grupos de segurança de rede ou configuração incorreta do UDR que bloqueia o tráfego. 
        - Se nenhum pacote de saída for observado na VM do pool de back-end, a VM precisará ser verificada em busca de problemas não relacionados (por exemplo, aplicativo que bloqueia a porta de investigação). 
    - Verifique se os pacotes de investigação estão sendo forçados para outro destino (possivelmente por meio de configurações de UDR) antes de atingir o balanceador de carga. Isso pode fazer com que o tráfego nunca chegue à VM de back-end. 
* Altere o tipo de investigação (por exemplo, HTTP para TCP) e configure a porta correspondente nas ACLs de grupos de segurança de rede e no firewall para validar se o problema é com a configuração de resposta de investigação. Para obter mais informações sobre configuração de investigação de integridade, consulte [configuração de investigação de integridade de balanceamento de carga do ponto de extremidade](https://blogs.msdn.microsoft.com/mast/2016/01/26/endpoint-load-balancing-heath-probe-configuration-details/).

## <a name="symptom-vms-behind-load-balancer-are-not-responding-to-traffic-on-the-configured-data-port"></a>Sintoma: VMs por trás de Load Balancer não estão respondendo ao tráfego na porta de dados configurada

Se uma VM do pool de back-end estiver listada como íntegra e responder às investigações de integridade, mas ainda não estiver participando do balanceamento de carga ou não estiver respondendo ao tráfego de dados, isso pode ser devido a qualquer uma das seguintes razões: 
* Load Balancer VM do pool de back-end não está escutando na porta de dados 
* O grupo de segurança de rede está bloqueando a porta na VM do pool de back-end Load Balancer  
* Acessando o Load Balancer da mesma VM e NIC 
* Acessando a Internet Load Balancer frontend da VM do pool de back-end Load Balancer participante 

### <a name="cause-1-load-balancer-backend-pool-vm-is-not-listening-on-the-data-port"></a>Causa 1: Load Balancer VM do pool de back-end não está escutando na porta de dados 
Se uma VM não responder ao tráfego de dados, talvez seja porque a porta de destino não está aberta na VM participante ou a VM não está escutando nessa porta. 

**Validação e resolução**

1. Faça logon na VM de back-end. 
2. Abra um prompt de comando e execute o seguinte comando para validar se há um aplicativo escutando na porta de dados:  netstat-a 
3. Se a porta não estiver listada com o estado "ouvindo", configure a porta do ouvinte apropriada 
4. Se a porta estiver marcada como ouvindo, verifique o aplicativo de destino nessa porta para obter os possíveis problemas.

### <a name="cause-2-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vm"></a>Causa 2: o grupo de segurança de rede está bloqueando a porta na VM do pool de back-end Load Balancer  

Se um ou mais grupos de segurança de rede configurados na sub-rede ou na VM estiver bloqueando o IP ou a porta de origem, a VM não poderá responder.

Para o Load Balancer público, o endereço IP dos clientes da Internet será usado para comunicação entre os clientes e as VMs de back-end do balanceador de carga. Verifique se o endereço IP dos clientes é permitido no grupo de segurança de rede da VM de back-end.

1. Liste os grupos de segurança de rede configurados na VM de back-end. Para obter mais informações, consulte [gerenciar grupos de segurança de rede](../virtual-network/manage-network-security-group.md)
1. Na lista de grupos de segurança de rede, verifique se:
    - o tráfego de entrada ou saída na porta de dados tem interferência. 
    - uma regra de grupo de segurança de rede **negar tudo** na NIC da VM ou na sub-rede que tem uma prioridade mais alta que a regra padrão que permite Load Balancer investigações e tráfego (grupos de segurança de rede devem permitir Load Balancer IP de 168.63.129.16, ou seja, a porta de investigação)
1. Se alguma das regras estiver bloqueando o tráfego, remova e reconfigure essas regras para permitir o tráfego de dados.  
1. Teste se a VM agora começou a responder às investigações de integridade.

### <a name="cause-3-accessing-the-load-balancer-from-the-same-vm-and-network-interface"></a>Causa 3: acessando a Load Balancer da mesma VM e interface de rede 

Se seu aplicativo hospedado na VM de back-end de um Load Balancer estiver tentando acessar outro aplicativo hospedado na mesma VM de back-end na mesma interface de rede, será um cenário sem suporte e falhará. 

**Resolução** Você pode resolver esse problema por meio de um dos seguintes métodos:
* Configure VMs de pool de back-end separadas por aplicativo. 
* Configure o aplicativo em VMs de NIC dupla para que cada aplicativo estivesse usando sua própria interface de rede e endereço IP. 

### <a name="cause-4-accessing-the-internal-load-balancer-frontend-from-the-participating-load-balancer-backend-pool-vm"></a>Causa 4: acessando o front-end interno de Load Balancer da VM do pool de back-end Load Balancer participante

Se uma Load Balancer interna for configurada em uma VNet e uma das VMs de back-end do participante estiver tentando acessar o frontend interno de Load Balancer, poderão ocorrer falhas quando o fluxo for mapeado para a VM de origem. Não há suporte para esse cenário. Reveja as [limitações](load-balancer-overview.md#limitations) de uma discussão detalhada.

**Resolução** Há várias maneiras de desbloquear esse cenário, incluindo o uso de um proxy. Avalie o gateway de aplicativo ou outros proxies de terceiros (por exemplo, Nginx ou HAProxy). Para obter mais informações sobre o gateway de aplicativo, consulte [visão geral do gateway de aplicativo](../application-gateway/application-gateway-introduction.md)

## <a name="additional-network-captures"></a>Capturas de rede adicionais
Se você decidir abrir um caso de suporte, colete as informações a seguir para uma resolução mais rápida. Escolha uma única VM de back-end para executar os seguintes testes:
- Use Psping de uma das VMs de back-end na VNet para testar a resposta da porta de investigação (exemplo: Psping 10.0.0.4:3389) e registrar os resultados. 
- Se nenhuma resposta for recebida nesses testes de ping, execute um rastreamento netsh simultâneo na VM de back-end e na VM de teste de VNet enquanto você executa o PsPing e, em seguida, interrompa o rastreamento netsh. 
  
## <a name="next-steps"></a>Passos seguintes

Se as etapas anteriores não resolverem o problema, abra um [tíquete de suporte](https://azure.microsoft.com/support/options/).


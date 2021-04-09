---
title: Resolução de problemas Azure Load Balancer estado da sonda de saúde
description: Saiba como resolver problemas conhecidos com o estado da sonda de saúde Azure Load Balancer.
services: load-balancer
documentationcenter: na
author: asudbring
manager: dcscontentpm
ms.custom: seodoc18
ms.service: load-balancer
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/02/2020
ms.author: allensu
ms.openlocfilehash: 28823c997cd974d5061829df88680ed52075caa0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98029213"
---
# <a name="troubleshoot-azure-load-balancer-health-probe-status"></a>Resolução de problemas Azure Load Balancer estado da sonda de saúde

Esta página fornece informações comuns sobre a resolução de problemas da sonda de saúde Azure Load Balancer.

## <a name="symptom-vms-behind-the-load-balancer-are-not-responding-to-health-probes"></a>Sintoma: VMs por trás do Balanceador de Carga não estão respondendo a sondas de saúde
Para que os servidores de backend participem no conjunto do balançador de carga, devem passar a verificação da sonda. Para obter mais informações sobre sondas de saúde, consulte [as sondas Understanding Load Balancer](load-balancer-custom-probe-overview.md). 

Os VMs do pool de backend balancer de carga podem não estar a responder às sondas devido a nenhuma das seguintes razões: 
- Load Balancer backend pool VM não é saudável 
- Load Balancer backend pool VM não está a ouvir na porta da sonda 
- Firewall, ou um grupo de segurança de rede está bloqueando a porta nos VMs da piscina de backend balancer de carga 
- Outras configurações erradas no Balanceador de Carga

### <a name="cause-1-load-balancer-backend-pool-vm-is-unhealthy"></a>Causa 1: Load Balancer backend pool VM não é saudável

**Validação e resolução**

Para resolver este problema, inicie sessão nos VMs participantes e verifique se o estado de VM está saudável, e pode responder a **PsPing** ou **TCPing** de outro VM na piscina. Se o VM não estiver saudável, ou não for capaz de responder à sonda, deve retificar o problema e levar o VM de volta a um estado saudável antes de poder participar no equilíbrio de carga.

### <a name="cause-2-load-balancer-backend-pool-vm-is-not-listening-on-the-probe-port"></a>Causa 2: Load Balancer pool VM não está a ouvir na porta da sonda
Se o VM é saudável, mas não está a responder à sonda, então uma razão possível pode ser que a porta da sonda não esteja aberta no VM participante, ou o VM não esteja a ouvir naquela porta.

**Validação e resolução**

1. Faça login no VM de backend.
2. Abra um pedido de comando e execute o seguinte comando para validar há uma aplicação ouvindo na porta da sonda: netstat -an
3. Se o estado do porto não estiver listado como **LISTENING,** configuure a porta adequada. 
4. Em alternativa, selecione outra porta, que está listada como **LISTENING**, e atualize a configuração do balançador de carga em conformidade.

### <a name="cause-3-firewall-or-a-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vms"></a>Causa 3: Firewall, ou um grupo de segurança de rede está bloqueando a porta no suporte de reserva VMs
Se a firewall do VM estiver a bloquear a porta da sonda, ou um ou mais grupos de segurança de rede configurados na sub-rede ou no VM, não permitir que a sonda chegue à porta, o VM não consegue responder à sonda de saúde.

**Validação e resolução**

1. Se a firewall estiver ativada, verifique se está configurada para permitir a porta da sonda. Caso contrário, configuure a firewall para permitir o tráfego na porta da sonda e volte a testar.
2. A partir da lista de grupos de segurança da rede, verifique se o tráfego de entrada ou saída na porta da sonda tem interferência.
3. Além disso, verifique se um **Deny Todos os** grupos de segurança da rede se pronunciam sobre o NIC do VM ou a sub-rede que tem uma prioridade superior à regra padrão que permite que as sondas LB & tráfego (os grupos de segurança da rede devem permitir o Load Balancer IP de 168.63.129.16).
4. Se alguma destas regras estiver a bloquear o tráfego da sonda, remova e reconfigure as regras para permitir o tráfego da sonda.  
5. Teste se o VM já começou a responder às sondas de saúde.

### <a name="cause-4-other-misconfigurations-in-load-balancer"></a>Causa 4: Outras configurações erradas no Balanceador de Carga
Se todas as causas anteriores parecem ser validadas e resolvidas corretamente, e o VM de backend ainda não responde à sonda de saúde, então teste manualmente para conectividade, e recolher alguns vestígios para entender a conectividade.

**Validação e resolução**

1. Utilize **a Psping** de um dos outros VMs dentro do VNet para testar a resposta da porta da sonda (exemplo: .\psping.exe -t 10.0.0.4:3389) e resultados recorde. 
2. Utilize **TCPing** de um dos outros VMs dentro do VNet para testar a resposta da porta da sonda (exemplo: .\tcping.exe 10.0.0.4 3389) e registar resultados. 
3. Se nenhuma resposta for recebida nestes testes de ping, então
    - Executar um rastreio de Netsh simultâneo no suporte do alvo VM e outro VM de teste do mesmo VNet. Agora, fazer um teste de PsPing durante algum tempo, recolher alguns vestígios de rede, e depois parar o teste. 
    - Analise a captura de rede e veja se existem pacotes de entrada e saída relacionados com a consulta de ping. 
        - Se não forem observados pacotes de entrada no pool de backend VM, existe potencialmente um grupo de segurança de rede ou uma configuração errada da UDR que bloqueia o tráfego. 
        - Se não forem observados pacotes de saída no pool de backend VM, o VM precisa de ser verificado sobre quaisquer problemas não relacionados (por exemplo, aplicação que bloqueia a porta da sonda). 
    - Verifique se os pacotes da sonda estão a ser forçados a outro destino (possivelmente através de definições de UDR) antes de chegar ao equilibrador de carga. Isto pode fazer com que o tráfego nunca chegue ao VM backend. 
4. Altere o tipo de sonda (por exemplo, HTTP para TCP) e configure a porta correspondente nos grupos de segurança de rede ACLs e firewall para validar se o problema estiver com a configuração da resposta da sonda. Para obter mais informações sobre a configuração da sonda de saúde, consulte [a configuração da sonda de equilíbrio de carga endpoint](/archive/blogs/mast/endpoint-load-balancing-heath-probe-configuration-details).

## <a name="next-steps"></a>Passos seguintes

Se os passos anteriores não resolverem o problema, abra um [bilhete de apoio](https://azure.microsoft.com/support/options/).
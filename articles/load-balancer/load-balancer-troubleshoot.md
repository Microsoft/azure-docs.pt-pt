---
title: Resolver problemas do Balanceador de Carga do Azure
description: Aprenda a resolver problemas conhecidos com o Azure Load Balancer.
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
ms.date: 01/28/2020
ms.author: allensu
ms.openlocfilehash: 0db91f3c661ebd3087a4389f4112a7214c992227
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82925954"
---
# <a name="troubleshoot-azure-load-balancer"></a>Resolver problemas do Balanceador de Carga do Azure
<p class="alert is-flex is-primary"><span class="has-padding-left-medium has-padding-top-extra-small"><a class="button is-primary" href="https://azurevirtualsupportagent.services.microsoft.com?content=fb23185b-6c56-d9f1-7ce1-758c978e08e1" target='_blank'>Comece a</a></span>resolver o seu problema rapidamente usando o<span class="has-padding-small">nosso agente virtual para executar <b>diagnósticos automatizados.</b> </span> Declaração <sub>de Privacidade</sub> <span class="has-padding-small"> <a href="https://privacy.microsoft.com/privacystatement" target='_blank'> <div align="right"></div></a></span></p>
Esta página fornece informações de resolução de problemas para questões básicas e standard comuns do Equilíbrio de Carga Azure. Para mais informações sobre o Standard Load Balancer, consulte a visão geral do [Balancer de Carga Padrão](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics).

Quando a conectividade do Balancer de Carga não está disponível, os sintomas mais comuns são os seguintes: 

- VMs por trás do Balancer de Carga não estão respondendo a sondas de saúde 
- VMs por trás do Balancer de carga não estão respondendo ao tráfego na porta configurada

Quando os clientes externos para os VMs de backend passarem pelo equilibrador de carga, o endereço IP dos clientes será usado para a comunicação. Certifique-se de que o endereço IP dos clientes é adicionado à lista de permitir o NSG. 

## <a name="symptom-vms-behind-the-load-balancer-are-not-responding-to-health-probes"></a>Sintoma: VMs por trás do Balancer de Carga não estão a responder a sondas de saúde
Para que os servidores de backend participem no conjunto de balanceadores de carga, devem passar a verificação da sonda. Para obter mais informações sobre sondas de saúde, consulte [Understanding Load Balancer Probes](load-balancer-custom-probe-overview.md). 

Os VMs do pool de backend load Balancer podem não estar a responder às sondas devido a qualquer uma das seguintes razões: 
- Piscina de backend Load Balancer VM não é saudável 
- Piscina de backend Load Balancer VM não está a ouvir na porta da sonda 
- Firewall, ou um grupo de segurança de rede está bloqueando a porta no Pool De reserva Load Balancer 
- Outras configurações erradas no Balancer de Carga

### <a name="cause-1-load-balancer-backend-pool-vm-is-unhealthy"></a>Causa 1: Piscina de backend do Balancer de carga VM não é saudável 

**Validação e resolução**

Para resolver este problema, inicie sessão nas VMs participantes e verifique se o estado VM é saudável, e pode responder a **PsPing** ou **TCPing** de outro VM na piscina. Se o VM não for saudável, ou não conseguir responder à sonda, deve retificar o problema e levar o VM de volta a um estado saudável antes de poder participar no equilíbrio de carga.

### <a name="cause-2-load-balancer-backend-pool-vm-is-not-listening-on-the-probe-port"></a>Causa 2: Piscina de backend do Balancer de carga VM não está a ouvir na porta da sonda
Se o VM for saudável, mas não estiver a responder à sonda, então uma razão possível pode ser que a porta da sonda não esteja aberta no VM participante, ou o VM não esteja a ouvir essa porta.

**Validação e resolução**

1. Faça login no VM traseiro. 
2. Abra um pedido de comando e execute o seguinte comando para validar que há uma aplicação de escuta na porta da sonda:   
            netstat - um
3. Se o estado portuário não estiver listado como **LISTENING,** configure a porta adequada. 
4. Em alternativa, selecione outra porta, que esteja listada como **LISTENING**, e atualize a configuração do equilíbrio de carga em conformidade.              

### <a name="cause-3-firewall-or-a-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vms"></a>Causa 3: Firewall ou um grupo de segurança de rede está bloqueando a porta no suporte de suporte de carga VMs  
Se a firewall do VM estiver a bloquear a porta da sonda, ou se um ou mais grupos de segurança de rede configurados na sub-rede ou no VM, não permitir que a sonda chegue à porta, o VM não consegue responder à sonda de saúde.          

**Validação e resolução**

* Se a firewall estiver ativada, verifique se está configurada para permitir a porta da sonda. Caso contrário, configure a firewall para permitir o tráfego na porta da sonda e teste novamente. 
* Da lista de grupos de segurança da rede, verifique se o tráfego de entrada ou saída na porta da sonda tem interferência. 
* Além disso, verifique se um **Deny Todos os** grupos de segurança da rede se pronunciam sobre o NIC do VM ou a sub-rede que tem uma prioridade superior à regra padrão que permite que as sondas LB & tráfego (os grupos de segurança da rede devem permitir o Balancer IP de 168.63.129.16). 
* Se alguma destas regras estiver a bloquear o tráfego da sonda, remova e reconfigure as regras para permitir o tráfego da sonda.  
* Teste se o VM já começou a responder às sondas de saúde. 

### <a name="cause-4-other-misconfigurations-in-load-balancer"></a>Causa 4: Outras configurações erradas no Balancer de Carga
Se todas as causas anteriores parecem ser validadas e resolvidas corretamente, e o VM traseiro ainda não responde à sonda de saúde, teste manualmente para a conectividade e recolha alguns vestígios para entender a conectividade.

**Validação e resolução**

* Utilize a **PSPde sacana** de um dos outros VMs dentro da VNet para testar a resposta da porta da sonda (exemplo: .\psping.exe -t 10.0.0.4.3389) e resultados recorde. 
* Utilize **O TCPing** de um dos outros VMs dentro do VNet para testar a resposta da porta da sonda (exemplo: .\tcping.exe 10.0.0.4 3389) e resultados recorde. 
* Se nenhuma resposta for recebida nestes testes de ping, então
    - Faça um rastreio simultâneo de Netsh na piscina de backend do alvo VM e outro VM de teste do mesmo VNet. Agora, faça um teste de PsPing por algum tempo, recolha alguns vestígios de rede, e depois pare o teste. 
    - Analise a captura da rede e veja se existem pacotes de entrada e saída relacionados com a consulta de ping. 
        - Se não forem observados pacotes de entrada na piscina de backend VM, existe potencialmente um grupo de segurança de rede ou uma configuração errada uDR bloqueando o tráfego. 
        - Se não forem observados pacotes de saída na piscina de backend VM, o VM precisa de ser verificado para quaisquer problemas não relacionados (por exemplo, aplicação que bloqueie a porta da sonda). 
    - Verifique se os pacotes da sonda estão a ser forçados a outro destino (possivelmente através de definições uDR) antes de chegar ao equilíbrio de carga. Isto pode fazer com que o tráfego nunca chegue ao VM traseiro. 
* Altere o tipo de sonda (por exemplo, HTTP para TCP) e configure a porta correspondente nos grupos de segurança da rede ACLs e firewall para validar se o problema estiver na configuração da resposta da sonda. Para obter mais informações sobre a configuração da sonda de saúde, consulte a configuração da sonda de [saúde Endpoint Load Balanceing](https://blogs.msdn.microsoft.com/mast/2016/01/26/endpoint-load-balancing-heath-probe-configuration-details/).

## <a name="symptom-vms-behind-load-balancer-are-not-responding-to-traffic-on-the-configured-data-port"></a>Sintoma: VMs por trás do Balancer de Carga não estão a responder ao tráfego na porta de dados configurada

Se um VM de reserva de backend for listado como saudável e responder às sondas de saúde, mas ainda não estiver a participar no Equilíbrio de Carga, ou não estiver a responder ao tráfego de dados, pode ser devido a qualquer uma das seguintes razões: 
* Load Balancer Backend pool VM não está a ouvir na porta de dados 
* O grupo de segurança da rede está a bloquear a porta na piscina de backend load Balancer VM  
* Acesso ao Balancer de Carga a partir do mesmo VM e NIC 
* Aceder ao frontend do Balancer de Carga da Internet a partir do pool de backend do Balancer de Carga participante VM 

### <a name="cause-1-load-balancer-backend-pool-vm-is-not-listening-on-the-data-port"></a>Causa 1: Piscina de backend do Balancer de carga VM não está a ouvir na porta de dados 
Se um VM não responder ao tráfego de dados, pode ser porque ou a porta-alvo não está aberta no VM participante, ou, o VM não está a ouvir essa porta. 

**Validação e resolução**

1. Faça login no VM traseiro. 
2. Abra um pedido de comando e execute o seguinte comando  para validar que há uma aplicação de escuta na porta de dados: netstat -a 
3. Se a porta não estiver listada com o Estado "LISTENING", configure a porta de escuta adequada 
4. Se a porta estiver marcada como Escuta, verifique a aplicação-alvo naquela porta para obter eventuais problemas.

### <a name="cause-2-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vm"></a>Causa 2: Grupo de segurança da rede está bloqueando a porta na piscina de backend Load Balancer VM  

Se um ou mais grupos de segurança de rede configurados na subnet ou no VM estiverem a bloquear o IP ou a porta de origem, então o VM não pode responder.

Para o equilibrador de carga pública, o endereço IP dos clientes da Internet será utilizado para a comunicação entre os clientes e os VMs de backend do equilibrador de carga. Certifique-se de que o endereço IP dos clientes é permitido no grupo de segurança de rede da VM.

1. Enumerar os grupos de segurança da rede configurados no VM traseiro. Para mais informações, consulte [Gerir grupos de segurança da rede](../virtual-network/manage-network-security-group.md)
1. Da lista de grupos de segurança da rede, verifique se:
    - o tráfego de entrada ou saída na porta de dados tem interferência. 
    - **a Negar Todas as** regras do grupo de segurança da rede no NIC do VM ou na subnet que tem uma prioridade maior que a regra padrão que permite sondas e tráfego de Balancer de carga (os grupos de segurança da rede devem permitir o Balancer IP de 168.63.129.16, que é a porta da sonda)
1. Se alguma das regras estiver a bloquear o tráfego, remova e reconfigure essas regras para permitir o tráfego de dados.  
1. Teste se o VM já começou a responder às sondas de saúde.

### <a name="cause-3-accessing-the-load-balancer-from-the-same-vm-and-network-interface"></a>Causa 3: Aceder ao Balancer de Carga a partir da mesma interface VM e Rede 

Se a sua aplicação alojada no VM de backend de um Balancer de carga estiver a tentar aceder a outra aplicação hospedada no mesmo VM de backend sobre a mesma Interface de Rede, é um cenário não suportado e falhará. 

**Resolução** Pode resolver esta questão através de um dos seguintes métodos:
* Configure vMs de piscina de backend separados por aplicação. 
* Configure a aplicação em VMs NIC duplos para que cada aplicação usasse a sua própria interface de rede e endereço IP. 

### <a name="cause-4-accessing-the-internal-load-balancer-frontend-from-the-participating-load-balancer-backend-pool-vm"></a>Causa 4: Aceder à face frontal interna do Balancer de Carga a partir do pool de backend do Balancer de Carga participante VM

Se um Balancer de carga interno estiver configurado dentro de um VNet, e um dos VMs de backend participante estiver a tentar aceder à extremidade frontal interna do Balancer de Carga, podem ocorrer falhas quando o fluxo é mapeado para o VM originário. Este cenário não é suportado. Rever [limitações](concepts.md#limitations) para uma discussão detalhada.

**Resolução** Existem várias formas de desbloquear este cenário, incluindo o uso de um proxy. Avaliar o Gateway de Aplicação ou outros proxies de terceiros (por exemplo, nginx ou haproxy). Para mais informações sobre o Gateway de Aplicação, consulte [a visão geral do Gateway de Aplicação](../application-gateway/application-gateway-introduction.md)

## <a name="symptom-cannot-change-backend-port-for-existing-lb-rule-of-a-load-balancer-which-has-vm-scale-set-deployed-in-the-backend-pool"></a>Sintoma: Não pode alterar a porta de backend para a regra LB existente de um equilibrista de carga que tem o Conjunto de Escala VM implantado na piscina de backend. 
### <a name="cause--the-backend-port-cannot-be-modified-for-a-load-balancing-rule-thats-used-by-a-health-probe-for-load-balancer-referenced-by-vm-scale-set"></a>Causa : A porta de backend não pode ser modificada para uma regra de equilíbrio de carga utilizada por uma sonda de saúde para equilibrador de carga referenciada por Conjunto de Balança VM.
**Resolução** Para alterar a porta, pode remover a sonda de saúde atualizando o Conjunto de Escala VM, atualizar a porta e, em seguida, configurar novamente a sonda de saúde.

## <a name="symptom-small-traffic-is-still-going-through-load-balancer-after-removing-vms-from-backend-pool-of-the-load-balancer"></a>Sintoma: O pequeno tráfego ainda está a passar pelo equilibrista de carga depois de remover VMs da piscina de backend do equilibrador de carga. 
### <a name="cause--vms-removed-from-backend-pool-should-no-longer-receive-traffic-the-small-amount-of-network-traffic-could-be-related-to-storage-dns-and-other-functions-within-azure"></a>Causa: VMs removidos da piscina de backend não devem mais receber tráfego. A pequena quantidade de tráfego de rede pode estar relacionada com armazenamento, DNS e outras funções dentro do Azure. 
Para verificar, pode conduzir um rastreio de rede. O FQDN utilizado para as suas contas de armazenamento blob está listado dentro das propriedades de cada conta de armazenamento.  A partir de uma máquina virtual dentro da sua subscrição Azure, pode realizar um nslookup para determinar o IP Azure atribuído a essa conta de armazenamento.

## <a name="additional-network-captures"></a>Capturas adicionais de rede
Se decidir abrir um caso de suporte, recolha as seguintes informações para uma resolução mais rápida. Escolha um VM de backend único para efetuar os seguintes testes:
- Utilize a PSPde saca de um dos VMs traseiros dentro da VNet para testar a resposta da sonda port (exemplo: psping 10.0.0.4:3389) e resultados recorde. 
- Se não for recebida nenhuma resposta nestes testes de ping, faça um traço netsh simultâneo no VM de backend e no VM de teste VNet enquanto executa o PsPing e pare o traço netsh. 
 
## <a name="next-steps"></a>Passos seguintes

Se os passos anteriores não resolverem a questão, abra um bilhete de [apoio](https://azure.microsoft.com/support/options/).


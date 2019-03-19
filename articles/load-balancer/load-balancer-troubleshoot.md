---
title: Resolver problemas do Balanceador de Carga do Azure
titlesuffix: Azure Load Balancer
description: Resolver problemas conhecidos com o Balanceador de carga do Azure
services: load-balancer
documentationcenter: na
author: chadmath
manager: cshepard
ms.custom: seodoc18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/09/2018
ms.author: genli
ms.openlocfilehash: c5f92d564a93823fd9c0f932fa95f20d4e827761
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58108836"
---
# <a name="troubleshoot-azure-load-balancer"></a>Resolver problemas do Balanceador de Carga do Azure

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Esta página fornece informações de resolução de problemas para as perguntas mais comuns do Balanceador de carga do Azure. Quando a conectividade de Balanceador de carga não estiver disponível, os sintomas mais comuns são os seguintes: 
- VMs atrás do Balanceador de carga não estão a responder às sondas de estado de funcionamento 
- VMs atrás do Balanceador de carga não estão a responder ao tráfego na porta configurada

## <a name="symptom-vms-behind-the-load-balancer-are-not-responding-to-health-probes"></a>Sintoma: VMs atrás do Balanceador de carga não estão a responder às sondas de estado de funcionamento
Para os servidores de back-end participar no conjunto de Balanceador de carga, tem de passar a verificação de sonda. Para obter mais informações sobre as sondas de estado de funcionamento, consulte [sondas do Balanceador de carga da compreensão](load-balancer-custom-probe-overview.md). 

O conjunto de back-end de Balanceador de carga VMs pode não estar a responder às sondas devido a qualquer um dos seguintes motivos: 
- O conjunto de back-end de Balanceador de carga VM está em mau estado de funcionamento 
- VM não está a escutar na porta de pesquisa no conjunto de back-end do Balanceador de carga 
- Firewall ou um grupo de segurança de rede está a bloquear a porta no conjunto de back-end de Balanceador de carga VMs 
- Outras configurações incorretas no balanceador de carga

### <a name="cause-1-load-balancer-backend-pool-vm-is-unhealthy"></a>Fazer com que 1: O conjunto de back-end de Balanceador de carga VM está em mau estado de funcionamento 

**Resolução e validação**

Para resolver este problema, inicie sessão para as VMs de participantes, verifique se o estado da VM está em bom estado e pode responder **PsPing** ou **TCPing** partir de outra VM no conjunto. Se a VM está danificada ou não consegue responder para a sonda, tem de resolver o problema e obter a VM de volta para um bom estado de funcionamento antes de poder participar em balanceamento de carga.

### <a name="cause-2-load-balancer-backend-pool-vm-is-not-listening-on-the-probe-port"></a>Causa 2: VM não está a escutar na porta de pesquisa no conjunto de back-end do Balanceador de carga
Se a VM está em bom estada, mas não está a responder a pesquisa, em seguida, um motivo possível pode ser que a porta de sonda não está aberta na participação VM ou a VM não está à escuta nessa porta.

**Resolução e validação**

1. Inicie sessão para a VM de back-end. 
2. Abra uma linha de comandos e execute o seguinte comando validar a existir, é uma aplicação em escuta na porta de pesquisa:   
            netstat - an
3. Se o estado da porta não está listado como **LISTENING**, configure a porta correta. 
4. Em alternativa, selecione outra porta, o que está listada como **LISTENING**e a atualização de configuração de Balanceador de carga em conformidade.              

### <a name="cause-3-firewall-or-a-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vms"></a>Causa 3: Firewall ou um grupo de segurança de rede está a bloquear a porta no conjunto de back-end de Balanceador de carga VMs  
Se a firewall na VM está a bloquear a porta de sonda ou um ou mais grupos de segurança configurados na sub-rede ou na VM de rede, não está a permitir que a sonda alcançar a porta, a VM não consegue responder para a sonda de estado de funcionamento.          

**Resolução e validação**

* Se o firewall estiver ativado, verifique se está configurado para permitir que a porta de sonda. Caso contrário, configure a firewall para permitir o tráfego na porta de pesquisa e teste novamente. 
* Na lista de grupos de segurança de rede, verifique se o tráfego de entrada ou de saída na porta de pesquisa tem interferência. 
* Além disso, verifique se uma **negar todos os** grupos de segurança de rede da regra na NIC de VM ou a sub-rede que tem uma prioridade mais alta do que a regra predefinida que permite que as sondas LB & tráfego (grupos de segurança de rede tem de permitir IP do Balanceador de carga do 168.63.129.16). 
* Se qualquer uma dessas regras estão a bloquear o tráfego de pesquisa, remover e reconfigurar as regras para permitir o tráfego de pesquisa.  
* Teste se a VM agora começou a responder às sondas do Estado de funcionamento. 

### <a name="cause-4-other-misconfigurations-in-load-balancer"></a>Fazer com que 4: Outras configurações incorretas no balanceador de carga
Se as causas anteriores parecem ser validado e resolvido corretamente, e o VM de back-end ainda não responde para a sonda de estado de funcionamento, em seguida, manualmente de teste de conectividade e recolher algumas rastreios para compreender a conectividade.

**Resolução e validação**

* Uso **Psping** de uma das outras VMs dentro da VNet para testar a resposta de porta de sonda (exemplo:.\psping.exe -t 10.0.0.4:3389) e registrar resultados. 
* Uso **TCPing** de uma das outras VMs dentro da VNet para testar a resposta de porta de sonda (exemplo:.\tcping.exe 10.0.0.4 3389) e registrar resultados. 
* Se for recebida nenhuma resposta nesses testes de ping, em seguida
    - Execute um rastreio Netsh simultâneo no pool de back-end de destino VM e noutra VM de teste da mesma VNet. Agora, executar um teste de PsPing durante algum tempo, recolher algumas rastreios de rede e, em seguida, pare o teste. 
    - Analisar a captura de rede e ver se existem pacotes de entrada e saídas relacionados com a consulta de ping. 
        - Se não existem pacotes de entrada foram observados na VM do agrupamento de back-end, há potencialmente um grupos de segurança de rede ou de configuração incorrecta da UDR a bloquear o tráfego. 
        - Se não existem pacotes de saída são observados na VM do agrupamento de back-end, a VM tem de ser verificada a quaisquer problemas não relacionados (por exemplo, a porta de sonda de bloqueio de aplicação). 
    - Certifique-se de que se os pacotes de sonda estão a ser forçados para outro destino (possivelmente por meio de configurações de UDR) antes de atingir o Balanceador de carga. Isso pode fazer com que o tráfego para nunca chegam o VM de back-end. 
* Alterar o tipo de pesquisa (por exemplo, HTTP para TCP) e configure a porta correspondente em ACLs de grupos de segurança de rede e de firewall para validar se o problema é com a configuração da resposta de pesquisa. Para obter mais informações sobre a configuração de sonda de estado de funcionamento, consulte [configuração de pesquisa de estado de funcionamento de ponto final de balanceamento de carga](https://blogs.msdn.microsoft.com/mast/2016/01/26/endpoint-load-balancing-heath-probe-configuration-details/).

## <a name="symptom-vms-behind-load-balancer-are-not-responding-to-traffic-on-the-configured-data-port"></a>Sintoma: VMs por trás do Balanceador de carga não estão a responder ao tráfego na porta de dados configurada

Se um conjunto de back-end VM está listada como bom estado de funcionamento e responde às sondas de estado de funcionamento, mas ainda não está a participar no balanceamento de carga ou não está a responder ao tráfego de dados, ele pode dever-se a qualquer um dos seguintes motivos: 
* VM não está a escutar a porta de dados do agrupamento de back-end de Balanceador de carga 
* Grupo de segurança de rede está a bloquear a porta no conjunto de back-end de Balanceador de carga VM  
* Acessar o Balanceador de carga a partir da mesma VM e NIC 
* Acessando o front-end de Balanceador de carga do conjunto de back-end de Balanceador de carga VM participante 

### <a name="cause-1-load-balancer-backend-pool-vm-is-not-listening-on-the-data-port"></a>Fazer com que 1: VM não está a escutar a porta de dados no conjunto de back-end do Balanceador de carga 
Se uma VM não responder ao tráfego de dados, poderá ser porque a porta de destino não está aberta na VM participante, ou, a VM não está à escuta nessa porta. 

**Resolução e validação**

1. Inicie sessão para a VM de back-end. 
2. Abra uma linha de comandos e execute o seguinte comando para validar aqui é uma aplicação em escuta na porta de dados:  netstat - an 
3. Se a porta não está listada com o estado "ESCUTAR", configure a porta de serviço de escuta adequada 
4. Se a porta é marcada como Listening, em seguida, verifique o aplicativo de destino nessa porta para quaisquer problemas possíveis. 

### <a name="cause-2-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vm"></a>Causa 2: Grupo de segurança de rede está a bloquear a porta no conjunto de back-end de Balanceador de carga VM  

Se um ou mais grupos de segurança de rede configurado na sub-rede ou na VM, está a bloquear o IP de origem ou a porta, em seguida, a VM não consegue responder.

* Liste os grupos de segurança de rede configurados no back-end da VM. Para obter mais informações, consulte [gerir grupos de segurança de rede](../virtual-network/manage-network-security-group.md).
* Na lista de grupos de segurança de rede, verifique se:
    - o tráfego de entrada ou de saída na porta de dados tem interferência. 
    - um **negar todos os** na NIC de VM ou a sub-rede que tem uma prioridade mais alta que a regra predefinida que permite que o Balanceador de carga sondas a regra do grupo de segurança de rede e de tráfego (grupos de segurança de rede tem de permitir IP do Balanceador de carga de 168.63.129.16, que é a porta de sonda) 
* Se qualquer uma das regras estão a bloquear o tráfego, remova e reconfigure essas regras para permitir o tráfego de dados.  
* Teste se a VM agora começou a responder às sondas de estado de funcionamento.

### <a name="cause-3-accessing-the-load-balancer-from-the-same-vm-and-network-interface"></a>Causa 3: Acessando o Balanceador de carga a partir da mesma interface de rede e de VM 

Se seu aplicativo hospedado no back-end da VM de um balanceador de carga está a tentar aceder a outro aplicativo hospedado no mesmo back-end da VM através da mesma Interface de rede, ele é um cenário não suportado e irá falhar. 

**Resolução** pode resolver este problema através de um dos seguintes métodos:
* Configure o conjunto de back-end separados VMs por aplicação. 
* Configure a aplicação em VMs de NIC dupla, para que cada aplicativo estava usando sua própria interface de rede e o endereço IP. 

### <a name="cause-4-accessing-the-internal-load-balancer-frontend-from-the-participating-load-balancer-backend-pool-vm"></a>Fazer com que 4: Acessando o front-end de Balanceador de carga interno do conjunto de back-end de Balanceador de carga VM participante

Se um balanceador de carga interno é configurado no interior de uma VNet e uma das VMs de back-end participante está tentando acessar o front-end de Balanceador de carga interno, as falhas podem ocorrer quando o fluxo está mapeado para a VM de origem. Este cenário não é suportado. Revisão [limitações](load-balancer-overview.md#limitations) para um debate detalhado.

**Resolução** existem várias formas para desbloquear este cenário, incluindo a utilização de um proxy. Avalie o Gateway de aplicação ou outros proxies de terceiros 3º (por exemplo, nginx ou haproxy). Para obter mais informações sobre o Gateway de aplicação, consulte [descrição geral do Gateway de aplicação](../application-gateway/application-gateway-introduction.md)

## <a name="additional-network-captures"></a>Captura de rede adicionais
Se optar por abrir um incidente de suporte, recolha as seguintes informações para uma resolução mais rápida. Escolha um back-end único VM para executar os seguintes testes:
- Utilizar o Psping a partir de um back-end VMs dentro da VNet para testar a resposta de porta de sonda (exemplo: psping 10.0.0.4:3389) e registrar resultados. 
- Se for recebida nenhuma resposta nesses testes de ping, execute um rastreio de Netsh simultâneo no VM de back-end e a VM de teste de VNet enquanto executar o PsPing, em seguida, pare o rastreio Netsh. 
  
## <a name="next-steps"></a>Passos Seguintes

Se os passos anteriores não resolverem o problema, abra um [pedido de suporte](https://azure.microsoft.com/support/options/).


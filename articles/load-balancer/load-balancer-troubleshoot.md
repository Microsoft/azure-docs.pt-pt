---
title: Resolver problemas do Balanceador de Carga do Azure
description: Saiba como resolver problemas conhecidos com o Azure Load Balancer.
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
ms.date: 01/28/2020
ms.author: allensu
ms.openlocfilehash: 22922972049ec78cc26f4d060fa1981d1f23a3ce
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2020
ms.locfileid: "92912451"
---
# <a name="troubleshoot-azure-load-balancer"></a>Resolver problemas do Balanceador de Carga do Azure

Esta página fornece informações de resolução de problemas para perguntas básicas e padrão comum do Balançador de Carga. Para obter mais informações sobre o Balanceador de Carga Padrão, consulte [a visão geral do Balancer de Carga Padrão](load-balancer-standard-diagnostics.md).

Quando a conectividade do Balanceador de Carga não está disponível, os sintomas mais comuns são os seguintes: 

- Os VMs por trás do Balanceador de Carga não estão a responder a sondas de saúde 
- Os VMs por trás do Balanceador de Carga não estão a responder ao tráfego na porta configurada

Quando os clientes externos para os VMs de backend passarem pelo equilibrador de carga, o endereço IP dos clientes será utilizado para a comunicação. Certifique-se de que o endereço IP dos clientes é adicionado à lista de autorizações da NSG. 

## <a name="symptom-no-outbound-connectivity-from-standard-internal-load-balancers-ilb"></a>Sintoma: Sem conectividade de saída dos Balanceadores de Carga Internos Padrão (ILB)

**Validação e resolução**

Os ILBs standard são **seguros por defeito** . Os ILBs básicos permitiram a ligação à internet através de um endereço IP público *oculto.* Isto não é recombatido para cargas de trabalho de produção, uma vez que o endereço IP não é estático nem bloqueado através de NSGs que possui. Se mudou recentemente de um ILB Básico para um ILB padrão, deve criar um IP público explicitamente através da configuração [outbound](egress-only.md) que bloqueia o IP através de NSGs. Também pode utilizar um [NAT Gateway](../virtual-network/nat-overview.md) na sua sub-rede.

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
2. Abra um pedido de comando e execute o seguinte comando para validar que há uma aplicação ouvindo na porta da sonda:   
            netstat -um
3. Se o estado do porto não estiver listado como **LISTENING,** configuure a porta adequada. 
4. Em alternativa, selecione outra porta, que está listada como **LISTENING** , e atualize a configuração do balançador de carga em conformidade.              

### <a name="cause-3-firewall-or-a-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vms"></a>Causa 3: Firewall, ou um grupo de segurança de rede está bloqueando a porta no suporte de reserva VMs  
Se a firewall do VM estiver a bloquear a porta da sonda, ou um ou mais grupos de segurança de rede configurados na sub-rede ou no VM, não permitir que a sonda chegue à porta, o VM não consegue responder à sonda de saúde.          

**Validação e resolução**

* Se a firewall estiver ativada, verifique se está configurada para permitir a porta da sonda. Caso contrário, configuure a firewall para permitir o tráfego na porta da sonda e volte a testar. 
* A partir da lista de grupos de segurança da rede, verifique se o tráfego de entrada ou saída na porta da sonda tem interferência. 
* Além disso, verifique se um **Deny Todos os** grupos de segurança da rede se pronunciam sobre o NIC do VM ou a sub-rede que tem uma prioridade superior à regra padrão que permite que as sondas LB & tráfego (os grupos de segurança da rede devem permitir o Load Balancer IP de 168.63.129.16). 
* Se alguma destas regras estiver a bloquear o tráfego da sonda, remova e reconfigure as regras para permitir o tráfego da sonda.  
* Teste se o VM já começou a responder às sondas de saúde. 

### <a name="cause-4-other-misconfigurations-in-load-balancer"></a>Causa 4: Outras configurações erradas no Balanceador de Carga
Se todas as causas anteriores parecem ser validadas e resolvidas corretamente, e o VM de backend ainda não responde à sonda de saúde, então teste manualmente para conectividade, e recolher alguns vestígios para entender a conectividade.

**Validação e resolução**

* Utilize **a Psping** de um dos outros VMs dentro do VNet para testar a resposta da porta da sonda (exemplo: .\psping.exe -t 10.0.0.4:3389) e resultados recorde. 
* Utilize **TCPing** de um dos outros VMs dentro do VNet para testar a resposta da porta da sonda (exemplo: .\tcping.exe 10.0.0.4 3389) e registar resultados. 
* Se nenhuma resposta for recebida nestes testes de ping, então
    - Executar um rastreio de Netsh simultâneo no suporte do alvo VM e outro VM de teste do mesmo VNet. Agora, fazer um teste de PsPing durante algum tempo, recolher alguns vestígios de rede, e depois parar o teste. 
    - Analise a captura de rede e veja se existem pacotes de entrada e saída relacionados com a consulta de ping. 
        - Se não forem observados pacotes de entrada no pool de backend VM, existe potencialmente um grupo de segurança de rede ou uma configuração errada da UDR que bloqueia o tráfego. 
        - Se não forem observados pacotes de saída no pool de backend VM, o VM precisa de ser verificado sobre quaisquer problemas não relacionados (por exemplo, aplicação que bloqueia a porta da sonda). 
    - Verifique se os pacotes da sonda estão a ser forçados a outro destino (possivelmente através de definições de UDR) antes de chegar ao equilibrador de carga. Isto pode fazer com que o tráfego nunca chegue ao VM backend. 
* Altere o tipo de sonda (por exemplo, HTTP para TCP) e configure a porta correspondente nos grupos de segurança de rede ACLs e firewall para validar se o problema estiver com a configuração da resposta da sonda. Para obter mais informações sobre a configuração da sonda de saúde, consulte [a configuração da sonda de equilíbrio de carga endpoint](https://blogs.msdn.microsoft.com/mast/2016/01/26/endpoint-load-balancing-heath-probe-configuration-details/).

## <a name="symptom-vms-behind-load-balancer-are-not-responding-to-traffic-on-the-configured-data-port"></a>Sintoma: VMs por trás do Balanceador de Carga não estão respondendo ao tráfego na porta de dados configurada

Se um pool de backend VM for listado como saudável e responder às sondas de saúde, mas ainda não participar no Equilíbrio de Carga, ou não estiver a responder ao tráfego de dados, pode ser devido a qualquer uma das seguintes razões: 
* Load Balancer Backend pool VM não está a ouvir na porta de dados 
* O grupo de segurança da rede está a bloquear a porta na piscina de backend balancer de carga VM  
* Aceder ao Equilibrador de Carga a partir do mesmo VM e NIC 
* Aceder ao frontend do Balançador de Carga de Internet a partir do pool de backend balancer de carga participante VM 

### <a name="cause-1-load-balancer-backend-pool-vm-is-not-listening-on-the-data-port"></a>Causa 1: Load Balancer pool VM não está a ouvir na porta de dados 
Se um VM não responder ao tráfego de dados, pode ser porque ou a porta-alvo não está aberta no VM participante, ou, o VM não está a ouvir nessa porta. 

**Validação e resolução**

1. Faça login no VM de backend. 
2. Abra uma pronta de comando e execute o seguinte comando para validar há uma aplicação ouvindo na porta de dados:  
            netstat -um 
3. Se o porto não estiver listado com Estado "LISTENING", configuure a porta de escuta adequada 
4. Se a porta estiver marcada como Escuta, verifique a aplicação-alvo na porta para eventuais problemas.

### <a name="cause-2-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vm"></a>Causa 2: O grupo de segurança da rede está a bloquear a porta na piscina de backend balancer de carga VM  

Se um ou mais grupos de segurança de rede configurados na sub-rede ou no VM estiverem a bloquear o IP ou a porta de origem, então o VM não consegue responder.

Para o balançador de carga pública, o endereço IP dos clientes da Internet será utilizado para a comunicação entre os clientes e os VMs de backend de reequilibradores de carga. Certifique-se de que o endereço IP dos clientes é permitido no grupo de segurança de rede da VM.

1. Listar os grupos de segurança da rede configurados no VM backend. Para obter mais informações, consulte [Gerir grupos de segurança de rede](../virtual-network/manage-network-security-group.md)
1. Da lista de grupos de segurança da rede, verifique se:
    - o tráfego de entrada ou saída na porta de dados tem interferência. 
    - a **Negar Todas as** regras do grupo de segurança da rede sobre o NIC do VM ou a sub-rede que tem uma prioridade maior que a regra padrão que permite as sondas e o tráfego do Balancer de Carga (os grupos de segurança da rede devem permitir o Load Balancer IP de 168.63.129.16, ou seja, a porta da sonda)
1. Se alguma das regras estiver a bloquear o tráfego, remova e reconfigure essas regras para permitir o tráfego de dados.  
1. Teste se o VM já começou a responder às sondas de saúde.

### <a name="cause-3-accessing-the-load-balancer-from-the-same-vm-and-network-interface"></a>Causa 3: Aceder ao Balanceador de Carga a partir da mesma interface VM e Rede 

Se a sua aplicação hospedada no VM de backend de um Balancer de Carga estiver a tentar aceder a outra aplicação hospedada no mesmo VM de backend sobre a mesma Interface de Rede, é um cenário não suportado e falhará. 

**Resolução** Pode resolver esta questão através de um dos seguintes métodos:
* Configurar vMs separados de backend pool por aplicação. 
* Configure a aplicação em VMs NIC duplos para que cada aplicação estivesse a utilizar a sua própria interface de Rede e endereço IP. 

### <a name="cause-4-accessing-the-internal-load-balancer-frontend-from-the-participating-load-balancer-backend-pool-vm"></a>Causa 4: Aceder ao frontend interno do balançador de carga a partir da piscina de backend balancer participante VM

Se um Balancer de Carga interno estiver configurado dentro de um VNet, e um dos VMs de backend do participante estiver a tentar aceder ao frontend interno do Balancer de Carga, podem ocorrer falhas quando o fluxo é mapeado para o VM originário. Este cenário não é suportado.

**Resolução** Existem várias formas de desbloquear este cenário, incluindo o uso de um representante. Avaliar o Gateway de Aplicações ou outros proxies de terceiros (por exemplo, nginx ou haproxy). Para mais informações sobre o Gateway de Aplicações, consulte [a visão geral do Gateway de aplicações](../application-gateway/application-gateway-introduction.md)

**Detalhes** Os Balançadores de Carga Interna não traduzem ligações originadas de saída para a extremidade frontal de um Balanceador de Carga interno porque ambos estão no espaço de endereço IP privado. Os Balançadores de Carga Pública fornecem ligações de saída de [endereços](load-balancer-outbound-connections.md) IP privados dentro da rede virtual para endereços IP públicos. Para os equilibradores de carga internos, esta abordagem evita a exaustão potencial da porta SNAT dentro de um espaço de endereço IP interno único, onde a tradução não é necessária.

Um efeito colateral é que se um fluxo de saída de um VM na piscina traseira tenta um fluxo para a extremidade frontal do Balancer de Carga interno na sua piscina _e_ é mapeado de volta para si mesmo, as duas pernas do fluxo não correspondem. Como não combinam, o fluxo falha. O fluxo é bem sucedido se o fluxo não mapear de volta para o mesmo VM na piscina traseira que criou o fluxo para a extremidade dianteira.

Quando o fluxo volta para si mesmo, o fluxo de saída parece originar-se do VM para a extremidade frontal e o fluxo de entrada correspondente parece originar-se do VM para si mesmo. Do ponto de vista do sistema operativo convidado, as partes de entrada e saída do mesmo fluxo não correspondem dentro da máquina virtual. A pilha de TCP não reconhecerá estas metades do mesmo fluxo que fazer parte do mesmo fluxo. A fonte e o destino não coincidem. Quando o fluxo mapeia para qualquer outro VM na piscina traseira, as metades do fluxo correspondem e o VM pode responder ao fluxo.

O sintoma para este cenário é intervalos intermitentes de ligação quando o fluxo volta ao mesmo backend que originou o fluxo. As soluções comuns incluem a inserção de uma camada de procuração por trás do balanceador de carga interno e a utilização de regras de estilo Direct Server Return (DSR). Para obter mais informações, consulte [Multiple Frontends for Azure Load Balancer](load-balancer-multivip-overview.md).

Pode combinar um Balancer de Carga interno com qualquer procuração de terceiros ou utilizar o Gateway de [Aplicação](../application-gateway/application-gateway-introduction.md) interno para cenários de procuração com HTTP/HTTPS. Embora possa utilizar um Balancer de Carga público para mitigar esta questão, o cenário resultante é propenso à [exaustão do SNAT](load-balancer-outbound-connections.md). Evite esta segunda abordagem, a menos que seja cuidadosamente gerida.

## <a name="symptom-cannot-change-backend-port-for-existing-lb-rule-of-a-load-balancer-which-has-vm-scale-set-deployed-in-the-backend-pool"></a>Sintoma: Não é possível alterar a porta de backend para a regra LB existente de um equilibrador de carga que tem conjunto de escala VM implantado na piscina de backend. 
### <a name="cause--the-backend-port-cannot-be-modified-for-a-load-balancing-rule-thats-used-by-a-health-probe-for-load-balancer-referenced-by-vm-scale-set"></a>Causa : A porta de backend não pode ser modificada para uma regra de equilíbrio de carga que é utilizada por uma sonda de saúde para o balanceador de carga referenciado pelo Conjunto de Escala VM.
**Resolução** Para alterar a porta, pode remover a sonda de saúde atualizando o Conjunto de Escala VM, atualizar a porta e, em seguida, configurar novamente a sonda de saúde.

## <a name="symptom-small-traffic-is-still-going-through-load-balancer-after-removing-vms-from-backend-pool-of-the-load-balancer"></a>Sintoma: O pequeno tráfego continua a passar pelo equilibrador de carga depois de remover os VMs do pool de backend do equilibrador de carga. 
### <a name="cause--vms-removed-from-backend-pool-should-no-longer-receive-traffic-the-small-amount-of-network-traffic-could-be-related-to-storage-dns-and-other-functions-within-azure"></a>Causa: Os VM removidos da piscina de backend não devem mais receber tráfego. A pequena quantidade de tráfego de rede pode estar relacionada com armazenamento, DNS e outras funções dentro de Azure. 
Para verificar, pode efetuar um rastreio de rede. O FQDN utilizado para as suas contas de armazenamento blob está listado nas propriedades de cada conta de armazenamento.  A partir de uma máquina virtual dentro da sua subscrição Azure, pode efetuar um nslookup para determinar o IP Azure atribuído a essa conta de armazenamento.

## <a name="additional-network-captures"></a>Capturas adicionais de rede
Se decidir abrir um caso de apoio, recolhê as seguintes informações para uma resolução mais rápida. Escolha um único VM de backend para realizar os seguintes testes:
- Utilize a PSPing de um dos VMs de backend dentro da VNet para testar a resposta da porta da sonda (exemplo: psping 10.0.0.4:3389) e resultados recorde. 
- Se não for recebida qualquer resposta nestes testes de ping, faça um rastreio de Netsh simultâneo no VM de backend e no VM de teste VNet enquanto corre PsPing, então pare o rastreio de Netsh. 
 
## <a name="symptom-load-balancer-in-failed-state"></a>Sintoma: Balanceador de carga em estado de falha 

**Resolução**

- Assim que identificar o recurso que está em estado de falha, vá ao [Azure Resource Explorer](https://resources.azure.com/) e identifique o recurso neste estado. 
- Atualize o toggle no canto superior direito para Ler/Escrever.
- Clique em Editar para obter o recurso em estado falhado.
- Clique em PUT seguido por GET para garantir que o estado de provisionamento foi atualizado para Sucesso.
- Em seguida, pode prosseguir com outras ações, uma vez que o recurso está fora do estado falhado.


## <a name="next-steps"></a>Passos seguintes

Se os passos anteriores não resolverem o problema, abra um [bilhete de apoio](https://azure.microsoft.com/support/options/).


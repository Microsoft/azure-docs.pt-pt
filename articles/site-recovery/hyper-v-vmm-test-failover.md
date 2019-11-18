---
title: Executar uma análise de recuperação de desastre do NHyper-V em um site secundário com Azure Site Recovery
description: Saiba como executar uma análise de DR para VMs do Hyper-V em nuvens do VMM para um datacenter local secundário usando Azure Site Recovery.
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: 0363911574a076b13cb72591fb2564364e096c76
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132936"
---
# <a name="run-a-dr-drill-for-hyper-v-vms-to-a-secondary-site"></a>Executar uma análise de DR para VMs do Hyper-V para um site secundário


Este artigo descreve como fazer uma análise de DR (recuperação de desastre) para VMs do Hyper-V que são gerenciadas em nuvens System Center Virtual Machine Manager V (MM), em um site local secundário, usando [Azure site Recovery](site-recovery-overview.md).

Você executa um failover de teste para validar sua estratégia de replicação e executa uma análise de recuperação de desastre sem qualquer perda de dados ou tempo de inatividade. Um failover de teste não tem nenhum impacto sobre a replicação em andamento ou em seu ambiente de produção. 

## <a name="how-do-test-failovers-work"></a>Como funcionam os failovers de teste?

Você executa um failover de teste do site primário para o secundário. Se você simplesmente deseja verificar se uma VM faz failover, é possível executar um teste sem configurar nada no site secundário. Se você quiser verificar se o failover de aplicativo funciona conforme o esperado, será necessário configurar a rede e a infraestrutura no local secundário.
- Você pode executar um failover de teste em uma única VM ou em um [plano de recuperação](site-recovery-create-recovery-plans.md).
- Você pode executar um failover de teste sem uma rede, com uma rede existente ou com uma rede criada automaticamente. Mais detalhes sobre essas opções são fornecidos na tabela a seguir.
    - Você pode executar um failover de teste sem uma rede. Essa opção será útil se você simplesmente quiser verificar se uma VM foi capaz de fazer failover, mas não poderá verificar nenhuma configuração de rede.
    - Execute o failover com uma rede existente. Recomendamos que você não use uma rede de produção.
    - Execute o failover e permita que Site Recovery crie automaticamente uma rede de teste. Nesse caso, Site Recovery criará a rede automaticamente e a limpará quando o failover de teste for concluído.
- Você precisa selecionar um ponto de recuperação para o failover de teste: 
    - **Mais recente processado**: essa opção falha em uma VM para o último ponto de recuperação processado pelo site Recovery. Esta opção proporciona um RTO (Objetivo de Tempo de Recuperação) baixo, porque não é despendido tempo ao processar os dados não processados.
    - **Consistente com o aplicativo mais recente**: essa opção faz failover de uma VM para o ponto de recuperação consistente com o aplicativo mais recente processado pelo site Recovery. 
    - **Mais recente**: essa opção primeiro processa todos os dados que foram enviados para site Recovery serviço, para criar um ponto de recuperação para cada VM antes de fazer failover para ela. Essa opção fornece o RPO mais baixo (objetivo de ponto de recuperação), pois a VM criada após o failover terá todos os dados replicados para Site Recovery quando o failover foi disparado.
    - **Várias VMs processadas mais recentemente**: disponíveis para planos de recuperação que incluem uma ou mais VMs com consistência de várias VMs habilitada. As VMs com a configuração habilitada fazem failover para o ponto de recuperação consistente de várias VMs comum mais recente. Outras VMs fazem failover para o último ponto de recuperação processado.
    - **Várias VMs mais recentes consistentes**com o aplicativo: essa opção está disponível para planos de recuperação com uma ou mais VMs com consistência de várias VMs habilitada. As VMs que fazem parte de um grupo de replicação fazem failover para o ponto de recuperação mais recente consistente com o aplicativo de várias VMs comum. Outras VMs fazem failover para o ponto de recuperação consistente com o aplicativo mais recente.
    - **Personalizado**: Use essa opção para fazer failover de uma VM específica para um ponto de recuperação específico.



## <a name="prepare-networking"></a>Preparar a rede

Ao executar um failover de teste, você será solicitado a selecionar as configurações de rede para máquinas de réplica de teste, conforme resumido na tabela.

| **Opção** | **Detalhes** | |
| --- | --- | --- |
| **Nenhum** | A VM de teste é criada no host no qual a VM de réplica está localizada. Ele não é adicionado à nuvem e não está conectado a nenhuma rede.<br/><br/> Você pode conectar o computador a uma rede VM depois que ela tiver sido criada.| |
| **Usar existente** | A VM de teste é criada no host no qual a VM de réplica está localizada. Ele não é adicionado à nuvem.<br/><br/>Crie uma rede VM isolada da sua rede de produção.<br/><br/>Se você estiver usando uma rede baseada em VLAN, recomendamos que você crie uma rede lógica separada (não usada em produção) no VMM para essa finalidade. Essa rede lógica é usada para criar redes VM para failovers de teste.<br/><br/>A rede lógica deve ser associada a pelo menos um dos adaptadores de rede de todos os servidores Hyper-V que estão hospedando máquinas virtuais.<br/><br/>Para redes lógicas de VLAN, os sites de rede que você adiciona à rede lógica devem ser isolados.<br/><br/>Se você estiver usando uma rede lógica baseada em virtualização de rede do Windows, Azure Site Recovery criar automaticamente redes VM isoladas. | |
| **Criar uma rede** | Uma rede de teste temporária é criada automaticamente com base na configuração que você especificar em **rede lógica** e seus sites de rede relacionados.<br/><br/> O failover verifica se as VMs são criadas.<br/><br/> Você deve usar essa opção se um plano de recuperação usar mais de uma rede VM.<br/><br/> Se você estiver usando redes de virtualização de rede do Windows, essa opção poderá criar automaticamente redes VM com as mesmas configurações (sub-redes e pools de endereços IP) na rede da máquina virtual de réplica. Essas redes VM são limpas automaticamente após a conclusão do failover de teste.<br/><br/> A VM de teste é criada no host no qual a máquina virtual de réplica existe. Ele não é adicionado à nuvem.|

### <a name="best-practices"></a>Melhores práticas

- Testar uma rede de produção causa tempo de inatividade para cargas de trabalho de produção. Peça aos seus usuários que não usem aplicativos relacionados quando a análise de recuperação de desastre estiver em andamento.

- A rede de teste não precisa corresponder ao tipo de rede lógica do VMM usado para failover de teste. Mas, algumas combinações não funcionam:

     - Se a réplica usar o isolamento baseado em VLAN e DHCP, a rede VM da réplica não precisará de um pool de endereços IP estáticos. Portanto, o uso da virtualização de rede do Windows para o failover de teste não funcionará porque nenhum pool de endereços está disponível. 
        
     - O failover de teste não funcionará se a rede de réplica não usar nenhum isolamento e a rede de teste usar a virtualização de rede do Windows. Isso ocorre porque a rede de não isolamento não tem as sub-redes necessárias para criar uma rede de virtualização de rede do Windows.
        
- Recomendamos que você não use a rede selecionada para o mapeamento de rede para o failover de teste.

- Como as máquinas virtuais de réplica são conectadas às redes de VM mapeadas após o failover depende de como a rede VM é configurada no console do VMM.


### <a name="vm-network-configured-with-no-isolation-or-vlan-isolation"></a>Rede VM configurada sem isolamento ou isolamento de VLAN

Se uma rede VM estiver configurada no VMM sem isolamento ou isolamento de VLAN, observe o seguinte:

- Se o DHCP for definido para a rede VM, a máquina virtual de réplica será conectada à ID da VLAN por meio das configurações especificadas para o site de rede na rede lógica associada. A máquina virtual recebe seu endereço IP do servidor DHCP disponível.
- Você não precisa definir um pool de endereços IP estáticos para a rede VM de destino. Se um pool de endereços IP estáticos for usado para a rede VM, a máquina virtual de réplica será conectada à ID da VLAN por meio das configurações especificadas para o site de rede na rede lógica associada.
- A máquina virtual recebe seu endereço IP do pool definido para a rede VM. Se um pool de endereços IP estáticos não estiver definido na rede VM de destino, haverá falha na alocação de endereço IP. Crie o pool de endereços IP nos servidores VMM de origem e de destino que serão usados para proteção e recuperação.

### <a name="vm-network-with-windows-network-virtualization"></a>Rede VM com virtualização de rede do Windows

Se uma rede VM estiver configurada no VMM com a virtualização de rede do Windows, observe o seguinte:

- Você deve definir um pool estático para a rede VM de destino, independentemente se a rede VM de origem está configurada para usar DHCP ou um pool de endereços IP estáticos. 
- Se você definir o DHCP, o servidor VMM de destino atuará como um servidor DHCP e fornecerá um endereço IP do pool definido para a rede VM de destino.
- Se o uso de um pool de endereços IP estáticos for definido para o servidor de origem, o servidor VMM de destino alocará um endereço IP do pool. Em ambos os casos, a alocação de endereço IP falhará se um pool de endereços IP estáticos não estiver definido.



## <a name="prepare-the-infrastructure"></a>Preparar a infraestrutura

Se você simplesmente deseja verificar se uma VM pode fazer failover, você pode executar um failover de teste sem uma infraestrutura. Se desejar fazer uma análise completa de DR para testar o failover de aplicativo, você precisará preparar a infraestrutura no site secundário:

- Se você executar um failover de teste usando uma rede existente, prepare Active Directory, DHCP e DNS nessa rede.
- Se você executar um failover de teste com a opção de criar uma rede VM automaticamente, precisará adicionar recursos de infraestrutura à rede criada automaticamente, antes de executar o failover de teste. Em um plano de recuperação, você pode facilitar isso adicionando uma etapa manual antes do grupo-1 no plano de recuperação que você pretende usar para o failover de teste. Em seguida, adicione os recursos de infraestrutura à rede criada automaticamente antes de executar o failover de teste.


### <a name="prepare-dhcp"></a>Preparar o DHCP
Se as máquinas virtuais envolvidas no failover de teste usarem DHCP, crie um servidor DHCP de teste na rede isolada para fins de failover de teste.


### <a name="prepare-active-directory"></a>Preparar Active Directory
Para executar um failover de teste para testes de aplicativos, você precisa de uma cópia do ambiente de Active Directory de produção em seu ambiente de teste. Para obter mais informações, examine as [considerações de failover de teste para Active Directory](site-recovery-active-directory.md#test-failover-considerations).

### <a name="prepare-dns"></a>Preparar o DNS
Prepare um servidor DNS para o failover de teste da seguinte maneira:

* **DHCP**: se as máquinas virtuais usarem DHCP, o endereço IP do DNS de teste deverá ser atualizado no servidor DHCP de teste. Se você estiver usando um tipo de rede de virtualização de rede do Windows, o servidor VMM atuará como o servidor DHCP. Portanto, o endereço IP do DNS deve ser atualizado na rede de failover de teste. Nesse caso, as máquinas virtuais se registram no servidor DNS relevante.
* **Endereço estático**: se as máquinas virtuais usarem um endereço IP estático, o endereço IP do servidor DNS de teste deverá ser atualizado na rede de failover de teste. Talvez seja necessário atualizar o DNS com o endereço IP das máquinas virtuais de teste. Você pode usar o seguinte script de exemplo para essa finalidade:

        Param(
        [string]$Zone,
        [string]$name,
        [string]$IP
        )
        $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
        $newrecord = $record.clone()
        $newrecord.RecordData[0].IPv4Address  =  $IP
        Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord



## <a name="run-a-test-failover"></a>Executar uma ativação pós-falha de teste

Este procedimento descreve como executar um failover de teste para um plano de recuperação. Como alternativa, você pode executar o failover para uma única máquina virtual na guia **máquinas virtuais** .

1. Selecione **planos de recuperação** > *recoveryplan_name*. Clique em **failover** > **failover de teste**.
2. Na folha **failover de teste** , especifique como as VMs de réplica devem ser conectadas às redes após o failover de teste.
3. Acompanhe o progresso do failover na guia **trabalhos** .
4. Após a conclusão do failover, verifique se as VMs são iniciadas com êxito.
5. Quando terminar, clique em **failover de teste de limpeza** no plano de recuperação. Em **Notas**, registe e guarde todas as observações associadas à ativação pós-falha de teste. Esta etapa exclui todas as VMs e redes que foram criadas por Site Recovery durante o failover de teste. 

![Ativação pós-falha de teste](./media/hyper-v-vmm-test-failover/TestFailover.png)
 


> [!TIP]
> O endereço IP fornecido a uma máquina virtual durante o failover de teste é o mesmo endereço IP que a máquina virtual receberia para um failover planejado ou não planejado (supondo que o endereço IP esteja disponível na rede de failover de teste). Se o mesmo endereço IP não estiver disponível na rede de failover de teste, a máquina virtual receberá outro endereço IP que está disponível na rede de failover de teste.



### <a name="run-a-test-failover-to-a-production-network"></a>Executar um failover de teste para uma rede de produção

Recomendamos que você não execute um failover de teste para sua rede de site de recuperação de produção que você especificou durante o mapeamento de rede. Mas se você precisar validar a conectividade de rede de ponta a ponta em uma VM com failover, observe os seguintes pontos:

* Certifique-se de que a VM primária seja desligada quando você estiver fazendo o failover de teste. Caso contrário, duas máquinas virtuais com a mesma identidade serão executadas na mesma rede ao mesmo tempo. Essa situação pode levar a consequências indesejadas.
* As alterações feitas nas VMs de failover de teste são perdidas quando você limpa as máquinas virtuais de failover de teste. Essas alterações não são replicadas de volta para as VMs primárias.
* Os testes como esse levam ao tempo de inatividade para seu aplicativo de produção. Peça que os usuários do aplicativo não usem o aplicativo quando a análise de DR estiver em andamento.  


## <a name="next-steps"></a>Passos seguintes
Depois de executar uma análise de DR com êxito, você pode [executar um failover completo](site-recovery-failover.md).




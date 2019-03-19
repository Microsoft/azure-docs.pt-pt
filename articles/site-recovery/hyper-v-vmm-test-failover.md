---
title: Executar um teste de recuperação após desastre de VMs de Hyper-V para um site secundário com o Azure Site Recovery | Documentos da Microsoft
description: Saiba como executar um teste de DR para as VMs de Hyper-V em clouds do VMM num Datacenter secundário no local com o Azure Site Recovery.
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: dc8deb16f7d124c5fb11568f25050eee99a245b8
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58096763"
---
# <a name="run-a-dr-drill-for-hyper-v-vms-to-a-secondary-site"></a>Executar um teste de DR para as VMs de Hyper-V para um site secundário


Este artigo descreve como fazer um teste de (DR recuperação) após desastre para VMs de Hyper-V geridos em clouds do System Center Virtual Machine Manager V(MM) para um site secundário no local, utilizando [do Azure Site Recovery](site-recovery-overview.md).

Executar uma ativação pós-falha de teste para validar a sua estratégia de replicação e efetuar um teste de DR sem qualquer perda de dados ou de indisponibilidade. Uma ativação pós-falha de teste não tem nenhum impacto sobre a replicação em curso ou em seu ambiente de produção. 

## <a name="how-do-test-failovers-work"></a>Como testar as ativações pós-falha trabalho?

Executar uma ativação pós-falha dos principais para o site secundário. Se quiser simplesmente verificar que uma VM efetua a ativação pós-falha, pode executar uma ativação pós-falha de teste sem configurar nada no site secundário. Se quiser verificar se funciona de ativação pós-falha de aplicação, conforme esperado, terá de configurar o funcionamento em rede e infraestrutura na localização secundária.
- Pode executar uma ativação pós-falha de teste numa única VM, ou num [plano de recuperação](site-recovery-create-recovery-plans.md).
- Pode executar uma ativação pós-falha de teste sem uma rede, com uma rede existente, ou com uma rede criada automaticamente. Obter mais detalhes sobre estas opções são fornecidos na tabela abaixo.
    - Pode executar uma ativação pós-falha de teste sem uma rede. Esta opção é útil se quiser simplesmente verificar que uma VM foi capaz de fazer a ativação pós-falha, mas não será possível verificar a qualquer configuração de rede.
    - Execute a ativação pós-falha com uma rede existente. Recomendamos que não utilize uma rede de produção.
    - Executar a ativação pós-falha e deixar que criar automaticamente uma rede de teste de recuperação de sites. Neste caso Site Recovery irá criar a rede automaticamente e limpá-los quando a ativação pós-falha de teste está concluída.
- Tem de selecionar um ponto de recuperação para ativação pós-falha de teste: 
    - **Processado mais recentemente**: Esta opção faz a uma VM através do ponto de recuperação mais recente processado pelo Site Recovery. Esta opção proporciona um RTO (Objetivo de Tempo de Recuperação) baixo, porque não é despendido tempo ao processar os dados não processados.
    - **Mais recente consistente com a aplicação**: Esta opção de ativação pós-falha numa VM para o ponto mais recente recuperação consistente com a aplicação processado pelo Site Recovery. 
    - **Mais recente**: Esta opção processa primeiro todos os dados que tenham sido enviados para o serviço de recuperação de Site, para criar um ponto de recuperação para cada VM antes de realizar a ativação pós-falha para o mesmo. Esta opção disponibiliza o último RPO (objetivo de ponto de recuperação), uma vez que a VM criada após a ativação pós-falha terá todos os dados replicados para o Site Recovery quando a ativação pós-falha foi acionada.
    - **MULTI-VM mais recente processado**: Disponível para planos de recuperação que incluem uma ou mais VMs que tenham a consistência multi VM ativada. As VMs com a definição ativada com a ativação pós-falha do ponto de recuperação consistente de várias VMS mais recente. Outras VMs com a ativação pós-falha do ponto de recuperação processado mais recente.
    - **MULTI-VM mais recente consistente com a aplicação**: Esta opção está disponível para planos de recuperação com uma ou mais VMs que tenham a consistência multi VM ativada. VMs que fazem parte de um grupo de replicação a ativação pós-falha do ponto de recuperação consistentes com aplicações de várias VMS mais recente. Outras VMs com a ativação pós-falha para o ponto de recuperação consistente com a aplicação mais recente.
    - **Custom**: Utilize esta opção para efetuar a ativação pós-falha de uma VM específica para um ponto de recuperação específico.



## <a name="prepare-networking"></a>Preparar o sistema de rede

Quando executa uma ativação pós-falha de teste, lhe forem pedidas para selecionar as definições de rede para máquinas de réplica de teste, conforme resumido na tabela.

| **Opção** | **Detalhes** | |
| --- | --- | --- |
| **Nenhum** | A VM de teste é criada no anfitrião onde está localizada a VM de réplica. Não é adicionado para a cloud e não está ligado a nenhuma rede.<br/><br/> Pode ligar a máquina a uma rede VM depois de este ter sido criado.| |
| **Utilizar existente** | A VM de teste é criada no anfitrião onde está localizada a VM de réplica. Ele não é adicionado para a cloud.<br/><br/>Crie uma rede VM isolada da rede de produção.<br/><br/>Se estiver a utilizar uma rede baseada em VLAN, recomendamos que crie uma rede lógica separada (não utilizada em produção) no VMM para esta finalidade. Esta rede lógica é utilizada para criar redes VM para ativações pós-falha de teste.<br/><br/>A rede lógica deve ser associada a, pelo menos, um dos adaptadores de rede de todos os servidores de Hyper-V que alojam máquinas virtuais.<br/><br/>Para redes lógicas de VLAN, sites de rede que adicionar à rede lógica devem ser isolados.<br/><br/>Se estiver a utilizar uma rede lógica baseada em Virtualização de rede do Windows, o Azure Site Recovery cria automaticamente redes VM isoladas. | |
| **Criar uma rede** | Uma rede de teste temporário é criada automaticamente com base na definição que especificou no **rede lógica** e dos respetivos sites de rede relacionados.<br/><br/> Ativação pós-falha verifica que as VMs são criadas.<br/><br/> Deve utilizar esta opção se a um plano de recuperação utilizar mais de uma rede VM.<br/><br/> Se estiver a utilizar redes de Virtualização de rede do Windows, esta opção pode criar automaticamente redes VM com as mesmas configurações de (sub-redes e conjuntos de endereços IP) na rede de máquina virtual de réplica. Estas redes VM são limpas automaticamente após a conclusão da ativação pós-falha de teste.<br/><br/> O teste de VM é criada no anfitrião no qual existe a máquina virtual de réplica. Ele não é adicionado para a cloud.|

### <a name="best-practices"></a>Melhores práticas

- Uma rede de produção de teste faz com que o tempo de inatividade para cargas de trabalho de produção. Peça aos seus utilizadores para não utilizar aplicações relacionadas ao teste de recuperação após desastre está em curso.

- A rede de teste não têm de corresponder o tipo de rede lógica de VMM utilizado para ativação pós-falha de teste. Mas, algumas combinações não funcionam:

     - Se a réplica utilizar DHCP e o isolamento baseado em VLAN, a rede VM para a réplica não precisa de um conjunto de endereços IP estáticos. Assim, usando a virtualização de rede do Windows para ativação pós-falha de teste não funcionará porque não existem conjuntos de endereços disponíveis. 
        
     - Ativação pós-falha de teste não funcionará se a rede de réplica utiliza sem isolamento, e a rede de teste utiliza Virtualização de rede do Windows. Isto acontece porque a rede sem isolamento não tem sub-redes necessárias para criar uma rede de Virtualização de rede do Windows.
        
- Recomendamos que não utilize a rede que selecionou para mapeamento de rede, para ativação pós-falha de teste.

- Como as máquinas virtuais de réplica estão ligadas a redes VM mapeadas após a ativação pós-falha depende de como a rede VM é configurada na consola do VMM.


### <a name="vm-network-configured-with-no-isolation-or-vlan-isolation"></a>Rede VM configurada sem isolamento ou isolamento de VLAN

Se uma rede VM é configurada no VMM, sem isolamento ou isolamento de VLAN, tenha em atenção o seguinte:

- Se o DHCP for definido para a rede VM, a máquina virtual de réplica está ligada para o ID de VLAN através das definições de que são especificadas para o site de rede na rede lógica associada. A máquina virtual recebe o respetivo endereço IP do servidor DHCP disponível.
- Não precisa de definir um conjunto de endereços IP estáticos para a rede VM de destino. Se um conjunto de endereços IP estáticos é utilizado para a rede VM, a máquina virtual de réplica está ligada para o ID de VLAN através das definições de que são especificadas para o site de rede na rede lógica associada.
- A máquina virtual recebe o respetivo endereço IP do conjunto que está definido para a rede VM. Se um conjunto de endereços IP estáticos não está definido na rede VM de destino, a alocação de endereços IP irá falhar. Crie o conjunto de endereços IP em servidores do VMM de origem e de destino que irá utilizar para proteção e recuperação.

### <a name="vm-network-with-windows-network-virtualization"></a>Rede VM com Virtualização de rede do Windows

Se uma rede VM é configurada no VMM com a virtualização de rede do Windows, tenha em atenção o seguinte:

- Deve definir um conjunto estático para a rede de VM de destino, independentemente se a rede VM de origem está configurada para utilizar o DHCP ou um conjunto de endereços IP estáticos. 
- Se definir o DHCP, o servidor do VMM de destino funciona como um servidor DHCP e fornece um endereço IP do conjunto que está definido para a rede VM de destino.
- Se a utilização de um conjunto de endereços IP estáticos for definida para o servidor de origem, o servidor do VMM de destino aloca um endereço IP do conjunto. Em ambos os casos, a alocação de endereços IP irá falhar se não for definido um conjunto de endereços IP estáticos.



## <a name="prepare-the-infrastructure"></a>Preparar a infraestrutura

Se quiser simplesmente verificar que uma VM pode efetuar a ativação pós-falha, pode executar uma ativação pós-falha de teste sem uma infraestrutura. Se quiser fazer uma exploração de DR completa para testar ativação pós-falha de aplicação, terá de preparar a infraestrutura no site secundário:

- Se executar uma ativação pós-falha de teste usando uma rede existente, prepare o Active Directory, DHCP e DNS na rede.
- Se executar uma ativação pós-falha de teste com a opção para criar automaticamente uma rede VM, terá de adicionar recursos de infraestrutura de rede criados automaticamente, antes de executar a ativação pós-falha de teste. Num plano de recuperação, pode facilitar isso adicionando um passo manual antes de 1 de grupo no plano de recuperação que pretende utilizar para ativação pós-falha de teste. Em seguida, adicione os recursos de infraestrutura para a rede automaticamente criada antes de executar a ativação pós-falha de teste.


### <a name="prepare-dhcp"></a>Preparar o DHCP
Se as máquinas virtuais envolvido na ativação pós-falha de teste utilizar DHCP, crie um servidor DHCP de teste dentro da rede isolada com o objetivo de ativação pós-falha de teste.


### <a name="prepare-active-directory"></a>Preparar o Active Directory
Para executar uma ativação pós-falha de teste para teste de aplicativos, precisa de uma cópia do ambiente do Active Directory de produção no seu ambiente de teste. Para obter mais informações, reveja os [considerações de ativação pós-falha para o Active Directory de teste](site-recovery-active-directory.md#test-failover-considerations).

### <a name="prepare-dns"></a>Prepare DNS
Prepare um servidor DNS para a ativação pós-falha de teste da seguinte forma:

* **DHCP**: Se as máquinas virtuais utilizar DHCP, o endereço IP do teste DNS deve ser atualizado no servidor DHCP de teste. Se estiver a utilizar um tipo de rede de Virtualização de rede do Windows, o servidor VMM atua como o servidor DHCP. Por conseguinte, o endereço IP de DNS deve ser atualizado na rede de ativação pós-falha de teste. Neste caso, as máquinas virtuais se registrar para o servidor DNS relevante.
* **Endereço estático**: Se as máquinas virtuais utilizam um endereço IP estático, o endereço IP do servidor DNS de teste deve ser atualizado na rede de ativação pós-falha de teste. Poderá ter de atualizar o DNS com o endereço IP de máquinas de virtuais de teste. Pode utilizar o seguinte script de exemplo para essa finalidade:

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

Este procedimento descreve como executar uma ativação pós-falha de teste para um plano de recuperação. Em alternativa, pode executar a ativação pós-falha para uma única máquina virtual no **máquinas virtuais** separador.

1. Selecione **planos de recuperação** > *recoveryplan_name*. Clique em **ativação pós-falha** > **ativação pós-falha de teste**.
2. Sobre o **testar ativação pós-falha** painel, especifique a forma como as VMs de réplica devem estar ligadas a redes após a ativação pós-falha de teste.
3. Controlar o progresso de ativação pós-falha no **tarefas** separador.
4. Após a conclusão da ativação pós-falha, certifique-se de que as VMs iniciam com êxito.
5. Quando tiver terminado, clique em **ativação pós-falha de teste de limpeza** no plano de recuperação. Em **Notas**, registe e guarde todas as observações associadas à ativação pós-falha de teste. Este passo elimina quaisquer VMs e redes que foram criadas pelo Site Recovery durante a ativação pós-falha de teste. 

![Ativação pós-falha de teste](./media/hyper-v-vmm-test-failover/TestFailover.png)
 


> [!TIP]
> O endereço IP fornecido para uma máquina virtual durante a ativação pós-falha de teste é o mesmo endereço IP que receberia a máquina virtual para uma ativação de pós-falha não planeada ou não planeada (a pressupor que o endereço IP está disponível na rede de ativação pós-falha de teste). Se o mesmo endereço IP não está disponível na rede de ativação pós-falha de teste, a máquina virtual recebe outro endereço IP que está disponível na rede de ativação pós-falha de teste.



### <a name="run-a-test-failover-to-a-production-network"></a>Executar uma ativação pós-falha para uma rede de produção

Recomendamos que não executar uma ativação pós-falha de teste para a sua rede de site de recuperação de produção que especificou durante o mapeamento da rede. Mas se precisar de validar a conectividade de rede ponto a ponto numa VM com ativação pós-falha, tenha em atenção os seguintes pontos:

* Certifique-se de que a VM principal está encerrada ao fazer a ativação pós-falha de teste. Caso contrário, serão executados na mesma rede duas máquinas virtuais com a mesma identidade ao mesmo tempo. Nessa situação pode levar a conseqüências indesejadas.
* Todas as alterações que fizer a ativação pós-falha de teste as VMs são perdidas quando limpar as máquinas de virtuais de ativação pós-falha de teste. Estas alterações não são replicadas para as VMs primárias.
* Teste como este leva para o período de indisponibilidade para a sua aplicação de produção. Peça aos utilizadores da aplicação para não utilizar a aplicação quando o exploração de DR está em curso.  


## <a name="next-steps"></a>Passos Seguintes
Depois de executar uma exploração de DR com êxito, poderá [executar uma ativação pós-falha completa](site-recovery-failover.md).




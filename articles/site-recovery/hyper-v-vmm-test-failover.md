---
title: Faça um exercício de recuperação de desastres NHyper-V para um local secundário com recuperação do site Azure
description: Aprenda a executar um berbequim DR para VMs hiper-V em nuvens VMM para um centro de dados secundário no local usando a Recuperação do Site Azure.
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: 0363911574a076b13cb72591fb2564364e096c76
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79257969"
---
# <a name="run-a-dr-drill-for-hyper-v-vms-to-a-secondary-site"></a>Faça um exercício DR para VMs hiper-V para um local secundário


Este artigo descreve como fazer um exercício de recuperação de desastres (DR) para VMs hiper-V que são geridos em nuvens de Gestor de Máquinas Virtuais V(MM) do System Center, para um local secundário no local, utilizando a Recuperação do [Site Azure.](site-recovery-overview.md)

Executa uma falha de teste para validar a sua estratégia de replicação e executa um berbequim DR sem qualquer perda de dados ou tempo de inatividade. Uma falha no teste não tem qualquer impacto na replicação em curso, ou no seu ambiente de produção. 

## <a name="how-do-test-failovers-work"></a>Como funcionam as falhas nos testes?

Faz um teste falhado desde o primário até ao local secundário. Se simplesmente quiser verificar se um VM falha, pode executar uma falha de teste sem configurar nada no local secundário. Se pretender verificar as falhas da aplicação como esperado, terá de configurar a rede e a infraestrutura no local secundário.
- Você pode executar um teste failover em um único VM, ou em um plano de [recuperação](site-recovery-create-recovery-plans.md).
- Pode executar um failover de teste sem uma rede, com uma rede existente, ou com uma rede criada automaticamente. Mais detalhes sobre estas opções são fornecidos na tabela abaixo.
    - Pode executar um teste sem rede. Esta opção é útil se simplesmente quiser verificar se um VM foi capaz de falhar, mas não poderá verificar qualquer configuração de rede.
    - Executar o failover com uma rede existente. Recomendamos que não utilize uma rede de produção.
    - Executar a falha e deixar a Recuperação do Site criar automaticamente uma rede de teste. Neste caso, a Recuperação do Site criará a rede automaticamente e limpá-la-á quando o teste estiver concluído.
- É necessário selecionar um ponto de recuperação para a falha do teste: 
    - **Mais recente processado**: Esta opção falha um VM até ao mais recente ponto de recuperação processado pela Recuperação do Site. Esta opção proporciona um RTO (Objetivo de Tempo de Recuperação) baixo, porque não é despendido tempo ao processar os dados não processados.
    - **Mais recente aplicação consistente**: Esta opção falha sobre um VM para o mais recente ponto de recuperação consistente de aplicações processado pela Recovery do Site. 
    - **Mais recente**: Esta opção processa primeiro todos os dados que foram enviados para o serviço de Recuperação do Site, para criar um ponto de recuperação para cada VM antes de falhar. Esta opção fornece o RPO mais baixo (Objetivo do Ponto de Recuperação), porque o VM criado após a falha terá todos os dados replicados para a Recuperação do Local quando a falha foi desencadeada.
    - **Mais recente processo multi-VM**: Disponível para planos de recuperação que incluem um ou mais VMs que tenham consistência multi-VM ativada. Os VMs com a definição ativada falham até ao último ponto de recuperação consistente multi-VM comum. Outros VMs falham até ao último ponto de recuperação processado.
    - **Mais recente aplicação multi-VM consistente**: Esta opção está disponível para planos de recuperação com um ou mais VMs que têm consistência multi-VM ativada. VMs que fazem parte de um grupo de replicação falham até ao mais recente ponto de recuperação consistente com aplicação multi-VM comum. Outros VMs falham no seu mais recente ponto de recuperação consistente com aplicações.
    - **Personalizado**: Utilize esta opção para falhar sobre um VM específico para um determinado ponto de recuperação.



## <a name="prepare-networking"></a>Preparar networking

Quando executa uma falha de teste, é-lhe pedido que selecione as definições de rede para máquinas de réplica de teste, como resumido na tabela.

| **Opção** | **Detalhes** | |
| --- | --- | --- |
| **Nenhuma.** | O VM de teste é criado no hospedeiro no qual está localizada a réplica VM. Não é adicionado à nuvem, e não está ligado a nenhuma rede.<br/><br/> Pode ligar a máquina a uma rede VM depois de ter sido criada.| |
| **Usar existente** | O VM de teste é criado no hospedeiro no qual está localizada a réplica VM. Não é adicionado à nuvem.<br/><br/>Crie uma rede VM isolada da sua rede de produção.<br/><br/>Se estiver a utilizar uma rede baseada em VLAN, recomendamos que crie uma rede lógica separada (não utilizada na produção) em VMM para o efeito. Esta rede lógica é usada para criar redes VM para falhas de teste.<br/><br/>A rede lógica deve ser associada a pelo menos um dos adaptadores de rede de todos os servidores Hyper-V que estão hospedando máquinas virtuais.<br/><br/>Para as redes lógicas VLAN, os sites de rede que adiciona à rede lógica devem ser isolados.<br/><br/>Se estiver a utilizar uma rede lógica baseada em Virtualização da Rede Windows, a Recuperação do Site Azure cria automaticamente redes VM isoladas. | |
| **Criar uma rede** | Uma rede de teste temporária é criada automaticamente com base na definição que especifica na **Rede Lógica** e nos seus sites de rede relacionados.<br/><br/> Falha verifica se os VMs são criados.<br/><br/> Deve utilizar esta opção se um plano de recuperação utilizar mais do que uma rede VM.<br/><br/> Se estiver a utilizar redes de virtualização da Rede Windows, esta opção pode criar automaticamente redes VM com as mesmas definições (subredes e conjuntos de endereços IP) na rede da máquina virtual de réplica. Estas redes VM são limpas automaticamente após a falha do teste estar completa.<br/><br/> O VM de teste é criado no hospedeiro no qual a máquina virtual de réplica existe. Não é adicionado à nuvem.|

### <a name="best-practices"></a>Melhores práticas

- Testar uma rede de produção causa tempo de inatividade para cargas de trabalho de produção. Peça aos seus utilizadores para não utilizarem aplicações relacionadas quando o exercício de recuperação de desastres estiver em curso.

- A rede de teste não precisa de corresponder ao tipo de rede lógica VMM utilizado para o teste de falha. Mas algumas combinações não funcionam:

     - Se a réplica utilizar o isolamento baseado em DHCP e VLAN, a rede VM para a réplica não necessita de um conjunto de endereços IP estáticos. Assim, a utilização da Virtualização da Rede Windows para o failover do teste não funcionará porque não existem piscinas de endereços disponíveis. 
        
     - O failover do teste não funcionará se a rede de réplicas não utilizar isolamento, e a rede de teste utiliza a Virtualização da Rede Windows. Isto porque a rede sem isolamento não tem as subredes necessárias para criar uma rede de virtualização da rede Windows.
        
- Recomendamos que não utilize a rede selecionada para mapeamento de rede, para o teste failover.

- A forma como as máquinas virtuais de réplica são ligadas às redes VM mapeadas após a falha depende de como a rede VM está configurada na consola VMM.


### <a name="vm-network-configured-with-no-isolation-or-vlan-isolation"></a>Rede VM configurada sem isolamento ou isolamento VLAN

Se uma rede VM estiver configurada em VMM sem isolamento, ou isolamento VLAN, note o seguinte:

- Se o DHCP estiver definido para a rede VM, a réplica da máquina virtual está ligada ao ID VLAN através das definições especificadas para o site de rede na rede lógica associada. A máquina virtual recebe o seu endereço IP do servidor DHCP disponível.
- Você não precisa definir um conjunto de endereços IP estático para a rede VM alvo. Se for utilizado um conjunto de endereços IP estático para a rede VM, a máquina virtual de réplica está ligada ao ID VLAN através das definições especificadas para o site de rede na rede lógica associada.
- A máquina virtual recebe o seu endereço IP da piscina que está definida para a rede VM. Se um conjunto de endereços IP estático não estiver definido na rede VM alvo, a atribuição de endereços IP falhará. Crie o conjunto de endereços IP tanto na fonte como nos servidores VMM de origem e alvo que utilizará para proteção e recuperação.

### <a name="vm-network-with-windows-network-virtualization"></a>Rede VM com virtualização da rede Windows

Se uma rede VM estiver configurada em VMM com virtualização da rede Windows, note o seguinte:

- Deve definir um pool estático para a rede VM alvo, independentemente de a rede VM de origem estar configurada para utilizar DHCP ou um conjunto de endereços IP estático. 
- Se definir o DHCP, o servidor VMM alvo funciona como um servidor DHCP e fornece um endereço IP a partir da piscina que está definido para a rede VM alvo.
- Se a utilização de um conjunto de endereços IP estático for definido para o servidor de origem, o servidor VMM alvo atribui um endereço IP da piscina. Em ambos os casos, a atribuição de endereços IP falhará se não for definido um conjunto de endereços IP estáticos.



## <a name="prepare-the-infrastructure"></a>Preparar a infraestrutura

Se simplesmente quiser verificar se um VM pode falhar, pode executar um teste sem uma infraestrutura. Se quiser fazer um exercício COMPLETO de DR para testar o failover da aplicação, precisa de preparar a infraestrutura no local secundário:

- Se executar uma falha de teste utilizando uma rede existente, prepare o Ative Directory, DHCP e DNS nessa rede.
- Se executar uma falha de teste com a opção de criar automaticamente uma rede VM, precisa de adicionar recursos de infraestrutura à rede criada automaticamente, antes de executar o failover do teste. Num plano de recuperação, pode facilitar isso adicionando um passo manual antes do Grupo 1 no plano de recuperação que vai utilizar para o teste falhar. Em seguida, adicione os recursos de infraestrutura à rede criada automaticamente antes de executar o failover do teste.


### <a name="prepare-dhcp"></a>Preparar dHCP
Se as máquinas virtuais envolvidas no teste falharem a utilização do DHCP, crie um servidor DHCP de teste dentro da rede isolada para efeitos de falha no teste.


### <a name="prepare-active-directory"></a>Preparar Diretório Ativo
Para executar uma falha de teste para testes de aplicação, precisa de uma cópia do ambiente de produção Ative Directy no seu ambiente de teste. Para mais informações, reveja as considerações de failover do [teste para o Diretório Ativo](site-recovery-active-directory.md#test-failover-considerations).

### <a name="prepare-dns"></a>Preparar DNS
Prepare um servidor DNS para a falha do teste da seguinte forma:

* **DHCP**: Se as máquinas virtuais utilizarem DHCP, o endereço IP do DNS de teste deve ser atualizado no servidor DHCP de teste. Se estiver a utilizar um tipo de rede de Virtualização da Rede Windows, o servidor VMM funciona como o servidor DHCP. Por conseguinte, o endereço IP do DNS deve ser atualizado na rede de failover do teste. Neste caso, as máquinas virtuais registam-se no servidor DNS relevante.
* **Endereço estático**: Se as máquinas virtuais utilizarem um endereço IP estático, o endereço IP do servidor DNS de teste deve ser atualizado na rede de failover do teste. Pode ser necessário atualizar o DNS com o endereço IP das máquinas virtuais de teste. Pode utilizar o seguinte script de amostra para o efeito:

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

Este procedimento descreve como executar um teste falhado para um plano de recuperação. Em alternativa, pode executar a falha para uma única máquina virtual no separador **Máquinas Virtuais.**

1. Selecione Planos de **Recuperação** > *recoveryplan_name*. Clique em **Failover** > **Failover**do teste .
2. Na lâmina failover do **teste,** especifique como as VMs réplicas devem ser ligadas às redes após a falha do teste.
3. Falha no progresso no separador **Jobs.**
4. Depois de concluída a falha, verifique se os VMs começam com sucesso.
5. Quando terminar, clique em teste de **limpeza** falhando no plano de recuperação. Em **Notas**, registe e guarde todas as observações associadas à ativação pós-falha de teste. Este passo elimina quaisquer VMs e redes que foram criadas pela Recovery do Site durante a falha do teste. 

![Ativação pós-falha de teste](./media/hyper-v-vmm-test-failover/TestFailover.png)
 


> [!TIP]
> O endereço IP dado a uma máquina virtual durante a falha do teste é o mesmo endereço IP que a máquina virtual receberia para uma falha planeada ou não planeada (presumindo-se que o endereço IP está disponível na rede de failover do teste). Se o mesmo endereço IP não estiver disponível na rede de failover do teste, a máquina virtual recebe outro endereço IP que está disponível na rede de failover do teste.



### <a name="run-a-test-failover-to-a-production-network"></a>Executar um teste failover para uma rede de produção

Recomendamos que não faça um teste de falha na rede do site de recuperação de produção que especificou durante o mapeamento da rede. Mas se precisar de validar a conectividade da rede de ponta a ponta num VM falhado, note os seguintes pontos:

* Certifique-se de que o VM primário é desligado quando estiver a fazer o teste falhar. Se não o fizer, duas máquinas virtuais com a mesma identidade estarão a funcionar na mesma rede ao mesmo tempo. Esta situação pode levar a consequências indesejadas.
* Quaisquer alterações que faça no teste falham os VMs quando limpar o teste falha as máquinas virtuais. Estas alterações não são replicadas de volta aos VMs primários.
* Testes como este levam a tempo de inatividade para a sua aplicação de produção. Peça aos utilizadores da aplicação que não utilizem a aplicação quando a broca DR estiver em curso.  


## <a name="next-steps"></a>Passos seguintes
Depois de ter feito uma broca DR com sucesso, pode [executar uma falha completa](site-recovery-failover.md).




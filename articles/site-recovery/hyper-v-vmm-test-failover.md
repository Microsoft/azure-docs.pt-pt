---
title: Executar um exercício de recuperação de desastres NHyper-V para um local secundário com recuperação do local de Azure
description: Aprenda a executar um berbequim DR para VMs hiper-V em nuvens VMM para um centro de dados secundário no local usando a recuperação do local de Azure.
author: sideeksh
manager: gaggupta
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: sideeksh
ms.openlocfilehash: 569af28f5773d843f49dd9c8143b45e308ae142e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87420418"
---
# <a name="run-a-dr-drill-for-hyper-v-vms-to-a-secondary-site"></a>Faça um exercício DR para Hiper-V VMs para um local secundário


Este artigo descreve como fazer uma broca de recuperação de desastres (DR) para VMs hiper-V que são geridos em nuvens do System Center Virtual Machine Manager V(MM), para um local secundário no local, utilizando [a recuperação do local de Azure.](site-recovery-overview.md)

Executa um teste de falha para validar a sua estratégia de replicação e realiza um berbequim DR sem qualquer perda de dados ou tempo de inatividade. Um teste de failover não tem qualquer impacto na replicação em curso, ou no seu ambiente de produção. 

## <a name="how-do-test-failovers-work"></a>Como funcionam os testes de failovers?

Faz-se um teste de falha desde a primária até ao local secundário. Se simplesmente quiser verificar se um VM falha, pode executar um teste sem configurar nada no local secundário. Se pretender verificar o failover da app como esperado, terá de configurar a rede e a infraestrutura no local secundário.
- Pode executar um teste de falha num único VM, ou num [plano de recuperação](site-recovery-create-recovery-plans.md).
- Pode executar um teste sem rede, com uma rede existente, ou com uma rede criada automaticamente. Mais detalhes sobre estas opções são fornecidos na tabela abaixo.
    - Pode executar um teste sem rede. Esta opção é útil se simplesmente quiser verificar se um VM foi capaz de falhar, mas não poderá verificar qualquer configuração de rede.
    - Executar o failover com uma rede existente. Recomendamos que não utilize uma rede de produção.
    - Executar o failover e deixar a Recuperação do Site criar automaticamente uma rede de teste. Neste caso, a Recuperação do Site criará a rede automaticamente e limpá-la-á quando o teste de falha estiver concluído.
- Tem de selecionar um ponto de recuperação para o teste de falha: 
    - **Mais recente processo**: Esta opção falha um VM sobre o último ponto de recuperação processado pela Recuperação do Site. Esta opção proporciona um RTO (Objetivo de Tempo de Recuperação) baixo, porque não é despendido tempo ao processar os dados não processados.
    - **Aplicações mais recentes consistentes**: Esta opção falha sobre um VM para o mais recente ponto de recuperação consistente de aplicações processado pela Recuperação do Site. 
    - **Mais recente**: Esta opção processa primeiro todos os dados enviados para o serviço de Recuperação de Sítios, para criar um ponto de recuperação para cada VM antes de falhar. Esta opção fornece o RPO mais baixo (Objetivo de Ponto de Recuperação), porque o VM criado após o failover terá todos os dados replicados para a Recuperação do Local quando a falha foi desencadeada.
    - **Processo multi-VM mais recente**: Disponível para planos de recuperação que incluam um ou mais VMs que têm consistência multi-VM ativada. Os VMs com a definição ativa falham no último ponto de recuperação consistente multi-VM comum. Outros VMs falham no último ponto de recuperação processado.
    - **Aplicações multi-VM mais recentes consistentes**: Esta opção está disponível para planos de recuperação com um ou mais VMs que têm consistência multi-VM ativada. Os VMs que fazem parte de um grupo de replicação falham no mais recente ponto de recuperação comum multi-VM consistente com aplicações. Outros VMs falham no seu último ponto de recuperação consistente com aplicações.
    - **Costume**: Utilize esta opção para falhar sobre um VM específico para um determinado ponto de recuperação.



## <a name="prepare-networking"></a>Preparar rede

Quando executar um teste de falha, é-lhe pedido que selecione as definições de rede para testar máquinas de réplicas, como resumido na tabela.

| **Opção** | **Detalhes** | |
| --- | --- | --- |
| **Nenhuma** | O VM de teste é criado no hospedeiro no qual está localizada a réplica VM. Não é adicionado à nuvem, e não está ligado a nenhuma rede.<br/><br/> Pode ligar a máquina a uma rede VM depois de ter sido criada.| |
| **Utilizar existente** | O VM de teste é criado no hospedeiro no qual está localizada a réplica VM. Não é adicionado à nuvem.<br/><br/>Crie uma rede VM isolada da sua rede de produção.<br/><br/>Se estiver a utilizar uma rede baseada em VLAN, recomendamos que crie uma rede lógica separada (não utilizada na produção) em VMM para este fim. Esta rede lógica é utilizada para criar redes VM para falhas de teste.<br/><br/>A rede lógica deve ser associada a pelo menos um dos adaptadores de rede de todos os servidores Hiper-V que estão a hospedar máquinas virtuais.<br/><br/>Para as redes lógicas VLAN, os sites de rede que adiciona à rede lógica devem ser isolados.<br/><br/>Se estiver a utilizar uma rede lógica baseada em virtualização da rede windows Network, a Azure Site Recovery cria automaticamente redes VM isoladas. | |
| **Criar uma rede** | Uma rede de teste temporária é criada automaticamente com base na definição que especifica na **Rede Lógica** e nos seus sites de rede relacionados.<br/><br/> Verificações de falhas que vMs são criados.<br/><br/> Deve utilizar esta opção se um plano de recuperação utilizar mais do que uma rede VM.<br/><br/> Se estiver a utilizar redes de virtualização da rede Windows Network, esta opção pode criar automaticamente redes VM com as mesmas definições (sub-redes e piscinas de endereços IP) na rede da máquina virtual réplica. Estas redes VM são limpas automaticamente após a conclusão do teste.<br/><br/> O VM de teste é criado no hospedeiro no qual existe a réplica da máquina virtual. Não é adicionado à nuvem.|

### <a name="best-practices"></a>Melhores práticas

- Testar uma rede de produção provoca tempo de inatividade para cargas de trabalho de produção. Peça aos seus utilizadores para não usarem aplicações relacionadas quando o exercício de recuperação de desastres estiver em andamento.

- A rede de teste não necessita de corresponder ao tipo de rede lógica VMM utilizado para o teste de failover. Mas algumas combinações não funcionam:

     - Se a réplica utilizar o isolamento baseado em DHCP e VLAN, a rede VM para a réplica não necessita de um conjunto de endereços IP estático. Assim, a utilização da Virtualização da Rede Windows para o teste de failover não funcionará porque não existem piscinas de endereços disponíveis. 
        
     - A falha de teste não funcionará se a rede de réplica não utilizar isolamento, e a rede de testes utilizar virtualização da rede Windows. Isto porque a rede de não isolamento não tem as sub-redes necessárias para criar uma rede de virtualização da rede Windows Network.
        
- Recomendamos que não utilize a rede selecionada para mapeamento de rede, para teste de failover.

- A forma como as máquinas virtuais de réplica estão ligadas a redes VM mapeadas após o failover depende da configuração da rede VM na consola VMM.


### <a name="vm-network-configured-with-no-isolation-or-vlan-isolation"></a>Rede VM configurada sem isolamento ou isolamento VLAN

Se uma rede VM estiver configurada em VMM sem isolamento, ou isolamento VLAN, note o seguinte:

- Se o DHCP for definido para a rede VM, a máquina virtual réplica está ligada ao ID VLAN através das definições especificadas para o site da rede na rede lógica associada. A máquina virtual recebe o seu endereço IP a partir do servidor DHCP disponível.
- Não é necessário definir um conjunto de endereços IP estático para a rede VM alvo. Se for utilizado um conjunto de endereços IP estático para a rede VM, a máquina virtual réplica está ligada ao ID VLAN através das definições especificadas para o site da rede na rede lógica associada.
- A máquina virtual recebe o seu endereço IP a partir da piscina definida para a rede VM. Se um conjunto de endereços IP estático não estiver definido na rede VM alvo, a atribuição de endereços IP falhará. Crie o conjunto de endereços IP tanto nos servidores VMM de origem como nos servidores VMM que utilizará para proteção e recuperação.

### <a name="vm-network-with-windows-network-virtualization"></a>Rede VM com Virtualização da Rede Windows

Se uma rede VM estiver configurada em VMM com virtualização da rede Windows, note o seguinte:

- Deve definir uma piscina estática para a rede VM alvo, independentemente de a rede VM de origem estar configurada para utilizar o DHCP ou um conjunto de endereços IP estático. 
- Se definir o DHCP, o servidor VMM alvo atua como um servidor DHCP e fornece um endereço IP a partir da piscina que está definido para a rede VM alvo.
- Se a utilização de um conjunto de endereços IP estático for definida para o servidor de origem, o servidor VMM alvo atribui um endereço IP a partir da piscina. Em ambos os casos, a atribuição de endereços IP falhará se não for definida uma cobertura de endereço IP estática.



## <a name="prepare-the-infrastructure"></a>Preparar a infraestrutura

Se simplesmente quiser verificar se um VM pode falhar, pode executar um teste sem uma infraestrutura. Se quiser fazer um berbequim DR completo para testar o failover da aplicação, tem de preparar a infraestrutura no local secundário:

- Se executar um teste de falha utilizando uma rede existente, prepare o Ative Directory, DHCP e DNS nessa rede.
- Se executar um teste de falha com a opção de criar automaticamente uma rede VM, tem de adicionar recursos de infraestrutura à rede criada automaticamente, antes de executar o teste de falha. Num plano de recuperação, pode facilitar isso adicionando um passo manual antes do Grupo 1 no plano de recuperação que vai utilizar para o teste de failover. Em seguida, adicione os recursos de infraestrutura à rede criada automaticamente antes de executar o teste de failover.


### <a name="prepare-dhcp"></a>Preparar DHCP
Se as máquinas virtuais envolvidas no teste de failover utilizarem o DHCP, crie um servidor DHCP de teste dentro da rede isolada para efeitos de falha de teste.


### <a name="prepare-active-directory"></a>Preparar Diretório Ativo
Para executar um teste de failover para testes de aplicação, você precisa de uma cópia do ambiente de produção Ative Directory no seu ambiente de teste. Para obter mais informações, reveja as [considerações de failover](site-recovery-active-directory.md#test-failover-considerations)do teste para o Ative Directory .

### <a name="prepare-dns"></a>Preparar DNS
Prepare um servidor DNS para o teste de falha da seguinte forma:

* **DHCP**: Se as máquinas virtuais utilizarem o DHCP, o endereço IP do DNS de teste deve ser atualizado no servidor DHCP de teste. Se estiver a utilizar um tipo de rede de Virtualização da Rede Windows, o servidor VMM funciona como o servidor DHCP. Por conseguinte, o endereço IP do DNS deve ser atualizado na rede de falha de teste. Neste caso, as máquinas virtuais registam-se no servidor DNS relevante.
* **Endereço estático**: Se as máquinas virtuais utilizarem um endereço IP estático, o endereço IP do servidor DNS de teste deve ser atualizado na rede de falha de teste. Poderá ser necessário atualizar o DNS com o endereço IP das máquinas virtuais de teste. Pode utilizar o seguinte script de amostra para este fim:

  ```powershell
  Param(
  [string]$Zone,
  [string]$name,
  [string]$IP
  )
  $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
  $newrecord = $record.clone()
  $newrecord.RecordData[0].IPv4Address  =  $IP
  Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord
  ```

## <a name="run-a-test-failover"></a>Executar uma ativação pós-falha de teste

Este procedimento descreve como executar um teste de failover para um plano de recuperação. Em alternativa, pode executar o failover para uma única máquina virtual no separador **Máquinas Virtuais.**

1. Selecione **Planos de Recuperação**  >  *recoveryplan_name*. Clique **em Failover**  >  **Test Failover**.
2. Na lâmina **de failover** de teste, especifique como as réplicas de VM devem ser ligadas a redes após a falha do teste.
3. Track failover progress on the **Jobs** tab.
4. Após a conclusão do failover, verifique se os VMs começam com sucesso.
5. Quando terminar, clique em **teste de limpeza no** plano de recuperação. Em **Notas,** registem e guarde quaisquer observações associadas ao teste. Este passo elimina quaisquer VMs e redes que foram criadas pela Recuperação do Local durante o failover do teste. 

![Ativação pós-falha de teste](./media/hyper-v-vmm-test-failover/TestFailover.png)
 


> [!TIP]
> O endereço IP dado a uma máquina virtual durante a falha de teste é o mesmo endereço IP que a máquina virtual receberia para uma falha planeada ou não planeada (presumindo-se que o endereço IP está disponível na rede de failover de teste). Se o mesmo endereço IP não estiver disponível na rede de falha de teste, a máquina virtual recebe outro endereço IP disponível na rede de falha de teste.



### <a name="run-a-test-failover-to-a-production-network"></a>Executar um teste falhado para uma rede de produção

Recomendamos que não faça um teste de falha na rede do site de recuperação de produção que especificou durante o mapeamento da rede. Mas se precisar de validar a conectividade da rede de ponta a ponta num VM falhado, note os seguintes pontos:

* Certifique-se de que o VM primário é desligado quando estiver a fazer o teste falhar. Caso não o faça, duas máquinas virtuais com a mesma identidade estarão a funcionar na mesma rede ao mesmo tempo. Esta situação pode levar a consequências indesejadas.
* Quaisquer alterações que faça ao teste de failover VMs perdem-se quando limpa o teste que falha em máquinas virtuais. Estas alterações não são replicadas de volta aos VM primários.
* Testes como este levam a tempo de inatividade para a sua aplicação de produção. Peça aos utilizadores da aplicação para não utilizarem a aplicação quando a broca DR estiver em andamento.  


## <a name="next-steps"></a>Passos seguintes
Depois de ter feito um exercício DR com sucesso, pode [executar um fracasso total](site-recovery-failover.md).




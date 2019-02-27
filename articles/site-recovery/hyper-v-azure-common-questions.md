---
title: Perguntas comuns - Hyper-V para recuperação após desastre do Azure com o Azure Site Recovery | Documentos da Microsoft
description: Este artigo resume as perguntas comuns sobre como configurar a recuperação após desastre para VMs de Hyper-V no local para o Azure com o site do Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.date: 12/27/2018
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: 498dc3f81968b60deb4f6f70466938026ac48f6a
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56868005"
---
# <a name="common-questions---hyper-v-to-azure-disaster-recovery"></a>Perguntas comuns - Hyper-V para recuperação após desastre do Azure

Este artigo fornece respostas a perguntas comuns que vemos ao replicar VMs de Hyper-V no local para o Azure. 


## <a name="general"></a>Geral

### <a name="how-is-site-recovery-priced"></a>Como é que o Site Recovery é cobrado?
Revisão [preços do Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/) detalhes.

### <a name="how-do-i-pay-for-azure-vms"></a>Como pago para VMs do Azure?
Durante a replicação, os dados são replicados para o armazenamento do Azure e não será necessário prestar quaisquer alterações VM. Quando executar uma ativação pós-falha para o Azure, o Site Recovery cria automaticamente máquinas de virtuais de IaaS do Azure. Depois disso é-lhe cobrada para recursos de computação que consumir no Azure.

## <a name="azure"></a>Azure

### <a name="what-do-i-need-in-azure"></a>O que é necessário no Azure?
Terá de uma subscrição do Azure, um cofre dos serviços de recuperação, uma conta de armazenamento e uma rede virtual. O cofre, a conta de armazenamento e a rede tem de ser na mesma região.

### <a name="what-azure-storage-account-do-i-need"></a>Que conta de armazenamento do Azure é necessário?
Precisa de uma conta de armazenamento LRS ou GRS. Recomendamos GRS para que os dados sejam resilientes se ocorrer uma falha regional ou se a região primária não pode ser recuperada. Armazenamento Premium é suportado.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>Minha conta do Azure precisa de permissões para criar VMs?
Se for um administrador de subscrição, tem as permissões de replicação que precisa. Se não tiver, terá permissões para criar uma VM do Azure no grupo de recursos e rede virtual que especificou quando configurar a recuperação de sites e permissões para escrever na conta de armazenamento selecionada. [Saiba mais](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines).

### <a name="is-replication-data-sent-to-site-recovery"></a>Dados de replicação são enviados para o Site Recovery?
Não, a recuperação de sites não interceta os dados replicados e não tem quaisquer informações sobre o que está em execução nas suas VMs. Os dados de replicação são trocados entre anfitriões Hyper-V e o armazenamento do Azure. A Recuperação de Sites não tem capacidade para intercetar esses dados. Apenas os metadados necessários para orquestrar a replicação e a ativação pós-falha são enviados para o serviço de Recuperação de Sites.  

Site Recovery é ISO 27001:2013, 27018, HIPAA, DPA certified e está no processo de SOC2 e FedRAMP JAB.

### <a name="can-we-keep-on-premises-metadata-within-a-geographic-regions"></a>Vamos guardar os metadados de locais dentro de um regiões geográficas?
Sim. Quando cria um cofre numa região, podemos assegurar que todos os metadados usados pelo Site Recovery permanece dentro do limite de geográfico dessa região.

### <a name="does-site-recovery-encrypt-replication"></a>A Recuperação de Sites faz encriptação de replicação?
Sim, ambas as encriptação em trânsito e [encriptação no Azure](https://docs.microsoft.com/azure/storage/storage-service-encryption) são suportados.


## <a name="deployment"></a>Implementação

### <a name="what-can-i-do-with-hyper-v-to-azure-replication"></a>O que posso fazer com o Hyper-V para replicação do Azure?

- **Recuperação após desastre**: Pode configurar a recuperação após desastre. Neste cenário, é possível replicar VMs de Hyper-V no local para o armazenamento do Azure:
    - Pode replicar VMs no Azure. Se a sua infraestrutura no local não estiver disponível, ativação pós-falha para o Azure.
    - Quando efetuar a ativação pós-falha, as VMs do Azure são criadas com os dados replicados. Pode aceder a aplicações e cargas de trabalho nas VMs do Azure.
    - Quando o seu datacenter no local estiver novamente disponível, pode reativação pós-falha do Azure para o seu site no local.
- **Migração**: Pode utilizar o Site Recovery para migrar VMs de Hyper-V no local para o armazenamento do Azure. Em seguida, efetuar a ativação pós-falha do local para o Azure. Após a ativação pós-falha, as suas aplicações e cargas de trabalho estão disponíveis e em execução em VMs do Azure.


### <a name="what-do-i-need-on-premises"></a>O que fazer, preciso no local?

Precisa de um ou mais VMs em execução num ou mais autónomos ou anfitriões de Hyper-V em cluster. Também pode replicar VMs em execução em anfitriões geridos pelo System Center Virtual Machine Manager (VMM).
    - Se não estiver a executar o VMM, durante a implementação da recuperação de sites, agrupa os anfitriões de Hyper-V e clusters em sites Hyper-V. Instale os agentes de recuperação de sites (agente do Azure Site Recovery Provider e serviços de recuperação) em cada anfitrião de Hyper-V.
    - Se os anfitriões de Hyper-V estiverem localizados numa nuvem do VMM, orquestra a replicação no VMM. Instalar o Site Recovery Provider no servidor do VMM e o agente dos serviços de recuperação em cada anfitrião de Hyper-V. Mapear entre redes VM/lógica de VMM e VNets do Azure.
    - 
[Saiba mais](hyper-v-azure-architecture.md) sobre o Hyper-V, a arquitetura do Azure.

### <a name="can-i-replicate-vms-located-on-a-hyper-v-cluster"></a>Pode replicar VMs localizadas num cluster de Hyper-V?

Sim, o Site Recovery suporta anfitriões de Hyper-V em cluster. Tenha em atenção que:

- Todos os nós do cluster devem ser registados no mesmo cofre.
- Se não estiver a utilizar o VMM, todos os anfitriões de Hyper-V no cluster devem ser adicionados ao mesmo site de Hyper-V.
- Instalar o agente do Azure Site Recovery Provider e serviços de recuperação em cada anfitrião de Hyper-V no cluster e adicione cada anfitrião para um site de Hyper-V.
- Não existem passos específicos precisa ser feito no cluster.
- Se executar a ferramenta Planeador de implementações do Hyper-V, a ferramenta recolhe os dados de perfil do nó que está a ser executado e onde a VM está em execução. A ferramenta não é possível recolher todos os dados de um nó que estiver desativado, mas irá controlar a esse nó. Depois do nó está em execução, a ferramenta é iniciada a recolher os dados de perfil VM da mesma (se a VM é parte da lista VM de perfil e está em execução no nó).
- Se uma VM num anfitrião Hyper-V no Cofre de recuperação de Site é migrada para outro anfitrião Hyper-V no mesmo cluster ou para um anfitrião autónomo, a replicação para a VM não é afetada. O anfitrião de Hyper-V tem de cumprir [pré-requisitos](hyper-v-azure-support-matrix.md#on-premises-servers)e ser configurado num cofre de recuperação de sites. 


### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>Posso proteger as VMs quando Hyper-V está em execução num sistema operativo cliente?
Não, as VMs têm de estar localizadas num servidor de anfitrião Hyper-V que está a ser executado na máquina do servidor Windows suportada. Se precisar de proteger um computador cliente poderia [replicá-la como uma máquina física](physical-azure-disaster-recovery.md) para o Azure.

### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>Pode replicar máquinas virtuais de 2.ª geração do Hyper-V para o Azure?
Sim. Recuperação de sites converte de geração 2 para a geração 1 durante a ativação pós-falha. Na reativação pós-falha da máquina é convertida novamente para a geração 2.

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>Pode automatizar os cenários de recuperação de sites com um SDK?
Sim. Pode automatizar fluxos de trabalho da Recuperação de Sites utilizando a API REST, PowerShell ou o SDK do Azure. Cenários suportados atualmente para a replicação de Hyper-V para o Azure com o PowerShell:

- [Replicar o Hyper-V sem o VMM com o PowerShell](hyper-v-azure-powershell-resource-manager.md)
- [Replicação de Hyper-V com o VMM com o Powershell](hyper-v-vmm-powershell-resource-manager.md)

## <a name="replication"></a>Replicação

### <a name="where-do-on-premises-vms-replicate-to"></a>Onde replicar VMs no local para o?
Dados são replicados para o armazenamento do Azure. Quando executa uma ativação pós-falha, o Site Recovery cria automaticamente as VMs do Azure da conta de armazenamento.

### <a name="what-apps-can-i-replicate"></a>Que aplicações posso replicar?
Pode replicar qualquer aplicação ou carga de trabalho de executar uma VM de Hyper-V que está em conformidade com [requisitos de replicação](hyper-v-azure-support-matrix.md#replicated-vms). Recuperação de sites fornece suporte para a replicação com suporte a aplicativos, para que aplicações podem ser realizadas a ativação pós-falha e falhou para um Estado inteligente. Site Recovery integra-se com aplicações da Microsoft, como SharePoint, Exchange, Dynamics, SQL Server e Active Directory e trabalha diretamente com fornecedores importantes, incluindo a Oracle, SAP, IBM e Red Hat. [Saiba mais](site-recovery-workload.md) sobre proteção de carga de trabalho.

### <a name="whats-the-replication-process"></a>O que é o processo de replicação?

1. Quando a replicação inicial for acionada, é criado um instantâneo de VM de Hyper-V.
2. Discos rígidos virtuais na VM são replicados um de cada, até estarem todos copiados para o Azure. Isto poderá demorar algum tempo, dependendo do tamanho VM e largura de banda de rede. Saiba como aumentar a largura de banda de rede.
3. Se ocorrerem alterações de disco enquanto a replicação inicial está em curso, o controlador de replicação de réplica do Hyper-V controla as alterações como registos de replicação de Hyper-V (. hrl). Estes ficheiros de registo estão localizados na mesma pasta que os discos. Cada disco tem um ficheiro. hrl associado que é enviado para o armazenamento secundário. Os ficheiros de instantâneo e de registo consomem recursos do disco quando a replicação inicial está em curso.
4. Quando a replicação inicial for concluída, o instantâneo da VM é eliminado.
5. Quaisquer alterações de disco no registo são sincronizadas e unidas ao disco principal.
6. Depois de concluída a replicação inicial, a proteção de finalizar na tarefa de máquina virtual é executada. Ele configura a rede e outras definições de pós-replicação, para que a VM está protegida.
7. Nesta fase, pode verificar as definições de VM para se certificar de que fique preparada para ativação pós-falha. Pode executar um teste de recuperação após desastre (ativação pós-falha de teste) para a VM, para verificar que ele falha mais conforme esperado.
8. Após a replicação inicial, começa a replicação delta, de acordo com a política de replicação.
9. As alterações são ficheiros de. hrl com sessão iniciada. Cada disco que está configurado para replicação tem um ficheiro .hrl associado.
10. O registo é enviado para a conta de armazenamento do cliente. Quando um registo está em trânsito para o Azure, as alterações ao disco principal são controladas noutro ficheiro de registo na mesma pasta.
11. Durante a replicação inicial e delta, pode monitorizar a VM no portal do Azure.

[Saiba mais](hyper-v-azure-architecture.md#replication-process) sobre o processo de replicação.

### <a name="can-i-replicate-to-azure-with-a-site-to-site-vpn"></a>Pode replicar para o Azure com uma VPN de site a site?

O site Recovery replica dados no local para o armazenamento do Azure através de um ponto final público ou com peering público do ExpressRoute. Não é suportada a replicação através de uma rede VPN de site a site.

### <a name="can-i-replicate-to-azure-with-expressroute"></a>Pode replicar para o Azure com o ExpressRoute?

Sim, o ExpressRoute pode ser utilizado para replicar VMs no Azure. O site Recovery replica dados para uma conta de armazenamento do Azure através de um ponto final público e tem de configurar [peering público](../expressroute/expressroute-circuit-peerings.md#publicpeering) para replicação do Site Recovery. Depois de VMs com a ativação pós-falha para uma rede virtual do Azure, pode acessá-los usando [peering privado](../expressroute/expressroute-circuit-peerings.md#privatepeering).


### <a name="why-cant-i-replicate-over-vpn"></a>Por que não é possível replicar através de VPN?

Ao replicar para o Azure, o tráfego de replicação atinge os pontos finais públicos de uma conta de armazenamento do Azure, assim, apenas pode replicar através da internet pública com o ExpressRoute (peering público) e VPN não funciona. 

### <a name="what-are-the-replicated-vm-requirements"></a>Quais são os requisitos de VM replicados?

Para a replicação, uma VM de Hyper-V tem de executar um sistema operativo suportado. Além disso, a VM tem de cumprir os requisitos para VMs do Azure. [Saiba mais](hyper-v-azure-support-matrix.md#replicated-vms) na matriz de suporte.

### <a name="how-often-can-i-replicate-to-azure"></a>Com que frequência posso replicar para o Azure?

VMs de Hyper-V podem ser replicadas cada 30 segundos (exceto para o armazenamento premium), 5 minutos ou 15 minutos.

###<a name="can-i-extend-replication"></a>Posso expandir a replicação?
Não é suportada a replicação expandida ou em cadeia. Pedir esta funcionalidade no [fórum de comentários](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-initial-replication"></a>Posso fazer uma replicação inicial offline?
Tal não é suportado. Pedir esta funcionalidade no [fórum de comentários](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-disks"></a>Pode excluir discos?
Sim, pode excluir discos da replicação. 

### <a name="can-i-replicate-vms-with-dynamic-disks"></a>Pode replicar VMs com discos dinâmicos?
Discos dinâmicos podem ser replicados. O disco do sistema operativo tem de ser um disco básico.



## <a name="security"></a>Segurança

### <a name="what-access-does-site-recovery-need-to-hyper-v-hosts"></a>Que acesso o Site Recovery precisa de anfitriões Hyper-V

Recuperação de sites precisa de aceder aos anfitriões de Hyper-V para replicar as VMs que selecionou. O site Recovery instala o seguinte em anfitriões Hyper-V:

- Se não estiver a executar o VMM, o Azure Site Recovery Provider e agente dos Recovery Services estão instalados em cada anfitrião.
- Se estiver a executar o VMM, o agente dos serviços de recuperação é instalado em cada anfitrião. O fornecedor é executado no servidor do VMM.


### <a name="what-does-site-recovery-install-on-hyper-v-vms"></a>O que a recuperação de Site a instalar em VMs de Hyper-V?

Recuperação de site explicitamente não instala nada em VMs de Hyper-V ativada para replicação.




## <a name="failover-and-failback"></a>Ativação pós-falha e reativação pós-falha


### <a name="how-do-i-fail-over-to-azure"></a>Como posso efetuar a ativação pós-falha para o Azure?

Pode executar uma ativação pós-falha planeada ou não planeada de VMs de Hyper-V no local para o Azure.
    - Se executar uma ativação pós-falha planeada, as VMs de origem são desligadas para garantir que não há perda de dados.
    - Se o site primário não estiver acessível, pode executar uma ativação pós-falha não planeada.
    - Pode efetuar a ativação pós-falha de uma máquina individual ou criar planos de recuperação, para orquestrar a ativação pós-falha de várias máquinas.
    - Executar uma ativação pós-falha. Após a conclusão da primeira fase de ativação pós-falha, deve conseguir ver a réplica de criar VMs no Azure. Pode atribuir um endereço IP público à VM, se necessário. Em seguida, consolida a ativação pós-falha, para começar a aceder à carga de trabalho da VM do Azure de réplica.
   

### <a name="how-do-i-access-azure-vms-after-failover"></a>Como posso acessar as VMs do Azure após a ativação pós-falha?
Após a ativação pós-falha, pode acessar as VMs do Azure através de uma ligação de Internet segura, através de uma VPN de site a site ou através do Azure ExpressRoute. Terá de preparar uma série de coisas para se ligar. [Saiba mais](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)

### <a name="is-failed-over-data-resilient"></a>É efetuada a ativação pós-falha dados resilientes?
O Azure foi concebido para ser resiliente. Recuperação de site foi desenvolvida para ativação pós-falha para um datacenter secundário do Azure, de acordo com o SLA do Azure. Quando ocorre a ativação pós-falha, devemos verificar se os metadados e cofres permanecem na mesma região geográfica que escolheu para o cofre.

### <a name="is-failover-automatic"></a>A ativação pós-falha é automática?
[Ativação pós-falha](site-recovery-failover.md) não é automática. Inicia as ativações pós-falha com um clique único no portal ou pode utilizar [PowerShell](/powershell/module/azurerm.siterecovery) para acionar uma ativação pós-falha.

### <a name="how-do-i-fail-back"></a>Como posso efetuar a ativação pós-falha?

Após a sua infraestrutura no local novamente em funcionamento, pode efetuar a reativação pós-falha. Reativação pós-falha ocorre em três fases:

1. Pode iniciar uma ativação pós-falha planeada do Azure para o site no local com algumas opções diferentes:

    - Minimize o tempo de inatividade: Se utilizar esta opção de recuperação de Site sincroniza os dados antes da ativação pós-falha. Ele verifica a existência de blocos de dados alterados e transfere-os para o site no local, enquanto mantém a VM do Azure em execução, minimizando o tempo de inatividade. Quando especificar manualmente que a ativação pós-falha deverá ser concluída, a VM do Azure é encerrada, quaisquer alterações de final delta são copiadas e inicia a ativação pós-falha.
    - Transferência completa: Com esta opção os dados são sincronizados durante a ativação pós-falha. Esta opção transfere o disco inteiro. É mais rápido porque não existem as somas de verificação são calculadas, mas há mais tempo de inatividade. Utilize esta opção se esteve executando a réplica de VMs do Azure durante algum tempo ou se a VM no local tiver sido eliminada.

2. Pode selecionar a falha de volta para a mesma VM ou a uma VM alternativa. Pode especificar que a recuperação de Site deve criar a VM, se ainda não exista.
3. Depois de concluída a sincronização inicial, selecione para concluir a ativação pós-falha. Depois de terminar, pode iniciar sessão na VM no local para verificar que tudo está a funcionar conforme esperado. No portal do Azure, pode ver que as VMs do Azure foram paradas.
4. Consolida a ativação pós-falha para concluir a cópia de segurança e começar a aceder à carga de trabalho da VM no local novamente.
5. Depois de tem realizarão a reativação pós-falha cargas de trabalho, ativar a replicação inversa, para que as VMs no local novamente a replicar para o Azure.

### <a name="can-i-fail-back-to-a-different-location"></a>Pode falhar novamente para uma localização diferente?
Sim, se a ativação pós-falha para o Azure, pode reativação pós-falha para uma localização diferente se original não estiver disponível. [Saiba mais](hyper-v-azure-failback.md#failback-to-an-alternate-location-in-hyper-v-environment).

---
title: Perguntas comuns - VMware para recuperação após desastre do Azure com o Azure Site Recovery | Documentos da Microsoft
description: Este artigo resume comuns perguntas preencha recuperação após desastre de VMs de VMware no local para o Azure com o Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.date: 04/08/2019
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: 2ab29c6e41204104320f4c2f583a24e53786bf3c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59360527"
---
# <a name="common-questions---vmware-to-azure-replication"></a>Perguntas comuns - VMware para replicação do Azure

Este artigo fornece respostas a perguntas comuns quando estiver a implementar recuperação após desastre de VMs de VMware no local para o Azure. 

## <a name="general"></a>Geral 
### <a name="what-do-i-need-for-vmware-vm-disaster-recovery"></a>O que é necessário para a recuperação de desastres da VM de VMware?

[Saiba mais sobre](vmware-azure-architecture.md) os componentes envolvidos na recuperação após desastre de VMs de VMware. 

### <a name="can-i-use-site-recovery-to-migrate-vmware-vms-to-azure"></a>Posso utilizar o Site Recovery para migrar VMs de VMware para o Azure?

Sim, além de utilizar o Site Recovery para configurar a recuperação após desastre para VMs de VMware, também pode utilizar Site Recovery para migrar VMs de VMware no local para o Azure. Neste cenário, replicar VMs de VMware no local para o armazenamento do Azure. Em seguida, efetuar a ativação pós-falha do local para o Azure. Após a ativação pós-falha, as suas aplicações e cargas de trabalho estão disponíveis e em execução em VMs do Azure. O processo é semelhante à configuração de recuperação após desastre, exceto que numa migração não a reativação pós-falha do Azure.


### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>Minha conta do Azure precisa de permissões para criar VMs?
Se for um administrador de subscrição, tem as permissões de replicação que precisa. Se não tiver, precisa de permissões para criar uma VM do Azure no grupo de recursos e especificar quando configurar a recuperação de sites e permissões para escrever na conta de armazenamento selecionada ou geridos com base em disco na sua configuração de rede virtual. [Saiba mais](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines).

### <a name="what-applications-can-i-replicate"></a>Que aplicações posso replicar?
Pode replicar qualquer aplicação ou carga de trabalho em execução numa VM de VMware que está em conformidade com [requisitos de replicação](vmware-physical-azure-support-matrix.md##replicated-machines).
- Recuperação de sites fornece suporte para a replicação com suporte a aplicativos, para que aplicações podem ser realizadas a ativação pós-falha e falhou para um Estado inteligente.
- Site Recovery integra-se com aplicações da Microsoft, como SharePoint, Exchange, Dynamics, SQL Server e Active Directory e trabalha diretamente com fornecedores importantes, incluindo a Oracle, SAP, IBM e Red Hat.
- [Saiba mais](site-recovery-workload.md) sobre proteção de carga de trabalho.

### <a name="can-i-use-a-guest-os-server-license-on-azure"></a>Pode utilizar uma licença de servidor do sistema operacional convidado no Azure?
Sim, podem utilizar os clientes do Microsoft Software Assurance [benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) para reduzir os custos de licenciamento **máquinas do Windows Server** que são migradas para o Azure ou utilizar o Azure para recuperação após desastre.

## <a name="security"></a>Segurança

### <a name="what-access-does-site-recovery-need-to-vmware-servers"></a>Que acesso o Site Recovery precisa aos servidores VMware?
O Site Recovery precisa de ter acesso a servidores VMware para:

- Configurar uma VM de VMware com o servidor de configuração do Site Recovery,
- Detete automaticamente as VMs para a replicação. 


### <a name="what-access-does-site-recovery-need-to-vmware-vms"></a>Que acesso o Site Recovery precisa de VMs do VMware?

- Para replicar, uma VM de VMware tem de ter o serviço de mobilidade de recuperação de sites instalada e em execução. Pode implementar a ferramenta manualmente, ou especificar que a recuperação de Site deve fazer uma instalação push do serviço, quando ativa a replicação para uma VM. 
- Durante a replicação, VMs comunicam com o Site Recovery da seguinte forma:
    - As VMs a comunicar com o servidor de configuração na porta HTTPS 443 para a gestão de replicação.
    - VMs enviam dados de replicação para o servidor de processos na porta HTTPS 9443 (pode ser modificada).
    - Se ativar a consistência multi-VM, as VMs comunicam entre si pela porta 20004.


### <a name="is-replication-data-sent-to-site-recovery"></a>Dados de replicação são enviados para o Site Recovery?
Não, a recuperação de sites não interceta os dados replicados e não tem quaisquer informações sobre o que está em execução nas suas VMs. Os dados de replicação são trocados entre os hipervisores de VMware e de armazenamento do Azure. A Recuperação de Sites não tem capacidade para intercetar esses dados. Apenas os metadados necessários para orquestrar a replicação e a ativação pós-falha são enviados para o serviço de Recuperação de Sites.  

Site Recovery é ISO 27001:2013, 27018, HIPAA, DPA certified e está no processo de SOC2 e FedRAMP JAB.


## <a name="pricing"></a>Preços
### <a name="how-can-i-calculate-approximate-charges-for-vmware-disaster-recovery"></a>Como posso calcular custos aproximados para a recuperação de desastre do VMware?

Pode utilizar o [Calculadora de preços](https://aka.ms/asr_pricing_calculator) de estimar os custos ao utilizar o Site Recovery.

Para obter estimativa detalhada nos custos, execute a ferramenta deployment planner para [VMware](https://aka.ms/siterecovery_deployment_planner)e utilizar os [relatório de estimativa de custos](https://aka.ms/asr_DP_costreport).

### <a name="is-there-any-difference-in-cost-between-replicating-to-storage-or-directly-to-managed-disks"></a>Existe alguma diferença no custo entre a replicar para o armazenamento ou diretamente para os managed disks?

Discos geridos são cobrados ligeiramente diferente de contas de armazenamento. [Saiba mais](https://azure.microsoft.com/pricing/details/managed-disks/) sobre os preços de disco gerido.

## <a name="mobility-service"></a>Serviço de Mobilidade

### <a name="where-can-i-find-the-mobility-service-installers"></a>Onde posso encontrar os programas de instalação do serviço de mobilidade?
Os programas de instalação estão no **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository** pasta no servidor de configuração.

## <a name="how-do-i-install-the-mobility-service"></a>Como posso instalar o serviço de mobilidade?
Instalar em cada VM que pretende replicar, utilizando um número de métodos:
- [Instalação de push](vmware-physical-mobility-service-overview.md#push-installation)
- [Instalação manual](vmware-physical-mobility-service-overview.md#install-mobility-agent-through-ui) partir da IU ou do Powershell.
- Implantação usando uma ferramenta de implantação, como [System Center Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md).

## <a name="managed-disks"></a>Managed disks

### <a name="where-does-site-recovery-replicate-data-to"></a>Onde é que o Site Recovery replicar dados para?

Site Recovery replica as VMs de VMware no local e servidores físicos para o managed disks no Azure.
- O servidor de processos de recuperação de Site escreve os registos de replicação para uma conta de armazenamento de cache na região de destino.
- Estes registos são utilizados para criar pontos de recuperação nos discos geridos.
- Quando ocorre a ativação pós-falha, o ponto de recuperação que selecionar é utilizado para criar o disco gerido de destino.
- As VMs que anteriormente foram replicadas para uma conta de armazenamento (antes de Março de 2019) não são afetadas.


### <a name="can-i-replicate-new-machines-to-storage-accounts"></a>Pode replicar máquinas novas contas de armazenamento?

Não, a partir de Março de 2019, o portal, pode replicar apenas para o Azure discos geridos. 

Replicação de novas VMs para uma conta de armazenamento só está disponível com o PowerShell ou a API de REST (versão 2018-01-10 ou 2016 a 08-10).

### <a name="what-are-the-benefits-in-replicating-to-managed-disks"></a>Quais são os benefícios no replicar para o managed disks?

[Saiba como](https://azure.microsoft.com/blog/simplify-disaster-recovery-with-managed-disks-for-vmware-and-physical-servers/) Site Recovery simplifica a recuperação após desastre com discos geridos.


### <a name="can-i-change-the-managed-disk-type-after-machine-is-protected"></a>Posso alterar o tipo de disco gerido depois do computador está protegido?

Sim, pode facilmente [alterar o tipo de disco gerido](https://docs.microsoft.com/azure/virtual-machines/windows/convert-disk-storage). No entanto, se puder o tipo de disco gerido, aguarde para pontos de recuperação nova seja gerado se tiver de testar ativação pós-falha ou ativação pós-falha após a alteração.

### <a name="can-i-switch-replication-from-managed-disks-to-unmanaged-disks"></a>Posso alternar os replicação a partir de discos geridos para discos não geridos?

Não, mudar de gerido para não gerenciado não é suportada.

## <a name="replication"></a>Replicação


### <a name="what-are-the-replicated-vm-requirements"></a>Quais são os requisitos de VM replicados?

[Saiba mais](vmware-physical-azure-support-matrix.md##replicated-machines) sobre requisitos de VM de VMware/físico do servidor e o suporte.

### <a name="how-often-can-i-replicate-to-azure"></a>Com que frequência posso replicar para o Azure?
A replicação é contínua ao replicar VMs de VMware para Azure.


### <a name="can-i-extend-replication"></a>Posso expandir a replicação?
Não é suportada a replicação expandida ou em cadeia. Pedir esta funcionalidade no [fórum de comentários](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-initial-replication"></a>Posso fazer uma replicação inicial offline?
Tal não é suportado. Pedir esta funcionalidade no [fórum de comentários](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-disks-from-replication"></a>Pode excluir discos da replicação?
Sim, pode excluir discos.

### <a name="can-i-replicate-vms-with-dynamic-disks"></a>Pode replicar VMs com discos dinâmicos?
Discos dinâmicos podem ser replicados. O disco do sistema operativo tem de ser um disco básico.

### <a name="if-i-use-replication-groups-for-multi-vm-consistency-can-i-add-a-new-vm-to-an-existing-replication-group"></a>Se utilizar grupos de replicação consistência multi-VM, posso adicionar uma nova VM a um grupo de replicação existente?
Sim, pode adicionar novas VMs a um grupo de replicação existente quando ativa a replicação para eles.
- Não é possível adicionar uma VM a um grupo de replicação existente, depois de replicação é iniciada.
- Não é possível criar um grupo de replicação de VMs existentes.

### <a name="can-i-modify-vms-that-are-replicating-by-adding-or-resizing-disks"></a>Pode modificar as VMs que estão a replicar com a adição ou redimensionar discos?

Para a replicação de VMware para o Azure pode modificar o tamanho do disco. Se pretender adicionar novos discos, que terá de adicionar o disco e reativar a proteção da VM.

### <a name="can-i-migrate-on-premises-machines-to-a-new-vcenter-server-without-impacting-ongoing-replication"></a>Posso migrar máquinas no local para um novo servidor do vCenter sem afetar a replicação em curso?
Não, a alteração do Vcenter ou migração irá afetar a replicação em curso. Terá de configurar a recuperação de Site com o novo vCenter Server e ative novamente a replicação para máquinas.

### <a name="can-i-replicate-to-a-cachetarget-storage-account-which-has-a-vnet-with-azure-storage-firewalls-configured-on-it"></a>Pode replicar para uma conta de armazenamento de cache/destino que tem uma VNet (com firewalls de armazenamento do Azure), configurada no mesmo?
Não, o Site Recovery não suporta a replicação para o armazenamento numa Vnet.





## <a name="component-upgrade"></a>Atualização de componente

### <a name="my-version-of-the-mobility-services-agent-or-configuration-server-is-old-and-my-upgrade-failed-what-do-i-do"></a>A minha versão do agente ou configuração de servidor dos serviços de mobilidade é antigo e meu atualização falhou. O que posso fazer?  

Recuperação de site segue o modelo de suporte de N-4. [Saiba mais](https://aka.ms/asr_support_statement) sobre como atualizar a partir de versões muito antigas.

### <a name="where-can-i-find-the-release-notesupdate-rollups-of-azure-site-recovery"></a>Onde posso encontrar os versão notas/update rollups do Azure Site Recovery?

[Saiba mais](site-recovery-whats-new.md) sobre novas atualizações, e [obter informação de rollup](service-updates-how-to.md).

### <a name="where-can-i-find-upgrade-information-for-disaster-recovery-to-azure"></a>Onde posso encontrar informações de atualização para a recuperação após desastre para o Azure?

[Saiba mais sobre](https://aka.ms/asr_vmware_upgrades) atualizar.

## <a name="do-i-need-to-reboot-source-machines-for-each-upgrade"></a>É necessário reiniciar as máquinas de origem para cada atualização?

Embora recomendada, não é obrigatório para cada atualização. [Saiba mais](https://aka.ms/asr_vmware_upgrades).


## <a name="configuration-server"></a>Servidor de configuração

### <a name="what-does-the-configuration-server-do"></a>O que faz o servidor de configuração?

O servidor de configuração é executado no local para componentes do Site Recovery, incluindo:
- O servidor de configuração propriamente dita que coordena as comunicações entre no local e o Azure e gere a replicação de dados.
- O servidor de processos atua como um gateway de replicação. Recebe dados de replicação; otimiza-os com a colocação em cache, compressão e encriptação; e envia-os para o armazenamento do Azure. O servidor de processos também faz uma instalação push do serviço de mobilidade em VMs e efetua a deteção automática de VMs de VMware no local.
- O servidor de destino principal processa dados de replicação durante a reativação pós-falha do Azure.

[Saiba mais](vmware-azure-architecture.md) sobre os componentes de servidor de configuração e os processos.

### <a name="where-do-i-set-up-the-configuration-server"></a>Onde posso configurar o servidor de configuração?
Precisa de uma VM do VMware único elevada disponibilidade no local para o servidor de configuração. Para a recuperação de desastres do servidor físico, pode instalar o servidor de configuração numa máquina física.

### <a name="what-do-i-need-for-the-configuration-server"></a>O que é necessário para o servidor de configuração?

Reveja os [pré-requisitos](vmware-azure-deploy-configuration-server.md#prerequisites).

### <a name="can-i-manually-set-up-the-configuration-server-instead-of-using-a-template"></a>Posso manualmente configurar o servidor de configuração em vez de utilizar um modelo?
Recomendamos que [criar a VM do servidor de configuração](vmware-azure-deploy-configuration-server.md) com a versão mais recente do modelo OVF. Se por algum motivo não pode, por exemplo não tem acesso ao servidor VMware, pode [transferir](physical-azure-set-up-source.md) o ficheiro de configuração do portal e configurar o servidor de configuração.

### <a name="can-a-configuration-server-replicate-to-more-than-one-region"></a>Um servidor de configuração pode replicar para o mais do que uma região?
Não. Para fazer isso, é necessário um servidor de configuração em cada região.

### <a name="can-i-host-a-configuration-server-in-azure"></a>Pode alojar um servidor de configuração no Azure?
Ainda que possível, a VM do Azure com o servidor de configuração têm de comunicar com a sua infraestrutura de VMware no local e VMs. Isso adiciona latência e tem impacto sobre a replicação em curso.

### <a name="how-do-i-update-the-configuration-server"></a>Como posso atualizar o servidor de configuração?

[Saiba](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) como atualizar o servidor de configuração.
- Pode encontrar as informações de atualização mais recentes sobre o [página de atualizações do Azure](https://azure.microsoft.com/updates/?product=site-recovery).
- Pode transferir a versão mais recente a partir do portal. Em alternativa, pode transferir diretamente a versão mais recente do servidor de configuração a partir da [Microsoft Download Center](https://aka.ms/asrconfigurationserver).
- Se tiver uma versão mais de quatro versões mais antigas do que a versão atual, consulte a nossa [instrução de suporte](https://aka.ms/asr_support_statement) para atualização orientação.

### <a name="should-i-back-up-the-configuration-server"></a>Deve criar cópia de segurança do servidor de configuração?
Recomendamos que faça cópias de segurança agendadas regulares do servidor de configuração.
- Para a reativação pós-falha concluída com êxito, a VM que está a ser realizarão a reativação pós-falha tem de existir na base de dados de servidor de configuração.
- O servidor de configuração tem de estar em execução e num estado ligado.
- [Saiba mais](vmware-azure-manage-configuration-server.md) sobre tarefas de gestão de servidor de configuração comuns.

### <a name="when-im-setting-up-the-configuration-server-can-i-download-and-install-mysql-manually"></a>Quando eu estou configurar o servidor de configuração, posso transferir e instalar manualmente o MySQL?

Sim. Transferir o MySQL e colocá-la a **C:\Temp\ASRSetup** pasta. Em seguida, instale manualmente. Quando configurar a VM do servidor de configuração e aceite os termos, MySQL será listada como **já instalado** na **transfira e instale**.

### <a name="can-i-avoid-downloading-mysql-but-let-site-recovery-install-it"></a>Pode evitar o download de MySQL mas permitir que o Site Recovery instalá-lo?

Sim. Transferir o instalador do MySQL e colocá-la a **C:\Temp\ASRSetup** pasta.  Ao configurar a VM do servidor de configuração, aceite os termos e clique em **transferir e instalar**. O portal irá utilizar o instalador que adicionou ao instalar o MySQL.
 
### <a name="can-i-use-the-configuration-server-vm-for-anything-else"></a>Pode utilizar o servidor de configuração de VM para qualquer outra coisa?
Não, só deve utilizar a VM para o servidor de configuração. 

### <a name="can-i-clone-a-configuration-server-and-use-it-for-orchestration"></a>Pode clonar um servidor de configuração e utilizá-lo para orquestração?
Não, deve configurar um servidor de configuração atualizados para evitar problemas de registo.

### <a name="can-i-change-the-vault-in-which-the-configuration-server-is-registered"></a>Pode alterar o Cofre no qual o servidor de configuração está registado?
Não. Depois de um cofre está associado com o servidor de configuração, não pode ser alterado. Revisão [este artigo](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault) para saber mais sobre o novo Registro.

### <a name="can-i-use-the-same-configuration-server-for-disaster-recovery-of-both-vmware-vms-and-physical-servers"></a>Pode utilizar o mesmo servidor de configuração para recuperação após desastre de VMs de VMware e servidores físicos
Sim, mas tenha em atenção que a máquina física só pode ser falha volta a uma VM de VMware.

### <a name="where-can-i-download-the-passphrase-for-the-configuration-server"></a>Onde posso transferir a frase de acesso para o servidor de configuração?
[Saiba como](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase) transferir a frase de acesso.

### <a name="where-can-i-download-vault-registration-keys"></a>Onde posso transferir as chaves de registo do Cofre?

No cofre dos serviços de recuperação, clique em **servidores de configuração** na **infraestrutura do Site Recovery** > **gerir**. Em seguida, em **servidores**, selecione **chave de registo do Download** para transferir o ficheiro de credenciais do cofre.







## <a name="failover-and-failback"></a>Ativação pós-falha e reativação pós-falha
### <a name="can-i-use-the-process-server-at-on-premises-for-failback"></a>Posso utilizar o servidor de processos no local para a reativação pós-falha?
Recomendamos vivamente a criação de um servidor de processos no Azure para fins de reativação pós-falha, para evitar latências de transferência de dados. Além disso, no caso de separado da rede de VMs de origem com a rede com acesso do Azure no servidor de configuração, é essencial para utilizar o servidor de processo criado no Azure para reativação pós-falha.

### <a name="can-i-retain-the-ip-address-on-failover"></a>Pode manter o endereço IP na ativação pós-falha?
Sim, pode manter o endereço IP na ativação pós-falha. Certifique-se de que especifica o endereço IP de destino nas definições de "Computação e rede" para a VM antes da ativação pós-falha. Além disso, encerre as máquinas no momento da ativação pós-falha para evitar conflitos de endereços IP durante a reativação pós-falha.

### <a name="can-i-change-the-target-vm-size-or-vm-type-before-failover"></a>Posso alterar o tamanho da VM de destino ou o tipo VM antes da ativação pós-falha?
Sim, pode alterar o tipo ou tamanho da VM em qualquer altura antes da ativação pós-falha nas definições de computação e rede da Vm replicada, no portal.

### <a name="how-far-back-can-i-recover"></a>Qual a amplitude posso recuperar?
Para VMware para o Azure, o ponto de recuperação mais antigo, que pode usar é 72 horas.

### <a name="how-do-i-access-azure-vms-after-failover"></a>Como posso acessar as VMs do Azure após a ativação pós-falha?
Após a ativação pós-falha, pode acessar as VMs do Azure através de uma ligação de Internet segura, através de uma VPN de site a site ou através do Azure ExpressRoute. Terá de preparar uma série de coisas para se ligar. [Saiba mais](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)

### <a name="is-failed-over-data-resilient"></a>É efetuada a ativação pós-falha dados resilientes?

O Azure foi concebido para ser resiliente. Recuperação de site foi desenvolvida para ativação pós-falha para um datacenter secundário do Azure, de acordo com o SLA do Azure. Quando ocorre a ativação pós-falha, devemos verificar se os metadados e cofres permanecem na mesma região geográfica que escolheu para o cofre.

### <a name="is-failover-automatic"></a>A ativação pós-falha é automática?
[Ativação pós-falha](site-recovery-failover.md) não é automática. Inicia as ativações pós-falha com um clique único no portal ou pode utilizar [PowerShell](/powershell/module/az.recoveryservices) para acionar uma ativação pós-falha.

### <a name="can-i-fail-back-to-a-different-location"></a>Pode falhar novamente para uma localização diferente?
Sim, se a ativação pós-falha para o Azure, pode reativação pós-falha para uma localização diferente se original não estiver disponível. [Saiba mais](concepts-types-of-failback.md#alternate-location-recovery-alr).

### <a name="why-do-i-need-a-vpn-or-expressroute-to-fail-back"></a>Por que eu preciso uma VPN ou ExpressRoute para a reativação pós-falha?
Quando realizar a ativação pós-falha do Azure, dados do Azure são copiados para a VM no local e o acesso privado é necessário.

### <a name="can-i-resize-the-azure-vm-after-failover"></a>Pode redimensionar a VM do Azure após a ativação pós-falha?
Não, não é possível alterar o tamanho ou tipo de VM de destino após a ativação pós-falha.


## <a name="automation-and-scripting"></a>Automatização e criação de scripts

### <a name="can-i-set-up-replication-with-scripting"></a>Pode definir a replicação com scripts?
Sim. Pode automatizar fluxos de trabalho de recuperação de Site utilizando a API Rest, PowerShell ou o SDK do Azure. [Saiba mais](vmware-azure-disaster-recovery-powershell.md).

## <a name="performance-and-capacity"></a>Desempenho e capacidade
### <a name="can-i-throttle-replication-bandwidth"></a>Posso limitar a largura de banda de replicação?
Sim. [Saiba mais](site-recovery-plan-capacity-vmware.md).


## <a name="next-steps"></a>Passos Seguintes
* [Revisão](vmware-physical-azure-support-matrix.md) dar suporte aos requisitos.
* [Configurar](vmware-azure-tutorial.md) VMware para replicação do Azure.

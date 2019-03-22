---
title: Perguntas comuns - VMware para recuperação após desastre do Azure com o Azure Site Recovery | Documentos da Microsoft
description: Este artigo resume as perguntas mais comuns ao configurar a recuperação após desastre de VMs de VMware no local para o Azure com o Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.date: 03/21/2019
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: 82ae36eaaf4616dbd85760a0962f301a2b1a20f5
ms.sourcegitcommit: 5e4ca656baf3c7d370ab3c0fbad0278aa2c9f1e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58319385"
---
# <a name="common-questions---vmware-to-azure-replication"></a>Perguntas comuns - VMware para replicação do Azure

Este artigo fornece respostas a perguntas comuns que vemos ao implementar a recuperação após desastre de VMs de VMware no local para o Azure. Se tiver questões depois de ler este artigo, publique o [fórum de serviços de recuperação do Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).


## <a name="general"></a>Geral
### <a name="how-is-site-recovery-priced"></a>Como é que o Site Recovery é cobrado?
Revisão [preços do Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/) detalhes.

### <a name="how-do-i-pay-for-azure-vms"></a>Como pago para VMs do Azure?
Durante a replicação, os dados são replicados para o armazenamento do Azure e não será necessário prestar quaisquer alterações VM. Quando executar uma ativação pós-falha para o Azure, o Site Recovery cria automaticamente máquinas de virtuais de IaaS do Azure. Depois disso é-lhe cobrada para recursos de computação que consumir no Azure.

### <a name="what-can-i-do-with-vmware-to-azure-replication"></a>O que posso fazer com o VMware para replicação do Azure?
- **Recuperação após desastre**: Pode configurar a recuperação após desastre. Neste cenário, é possível replicar VMs de VMware no local para o armazenamento do Azure. Em seguida, se a sua infraestrutura no local não estiver disponível, pode efetuar a ativação pós-falha para o Azure. Quando efetuar a ativação pós-falha, as VMs do Azure são criadas com os dados replicados. Pode aceder a aplicações e cargas de trabalho em VMs do Azure, até que o seu datacenter no local estiver novamente disponível. Em seguida, pode reativação pós-falha do Azure para o seu site no local.
- **Migração**: Pode utilizar o Site Recovery para migrar VMs de VMware no local para o Azure. Neste cenário, replicar VMs de VMware no local para o armazenamento do Azure. Em seguida, efetuar a ativação pós-falha do local para o Azure. Após a ativação pós-falha, as suas aplicações e cargas de trabalho estão disponíveis e em execução em VMs do Azure.

## <a name="azure"></a>Azure
### <a name="what-do-i-need-in-azure"></a>O que é necessário no Azure?
Precisa de uma subscrição do Azure, um cofre dos serviços de recuperação, uma conta de armazenamento de cache, discos geridos e uma rede virtual. O cofre, a conta de armazenamento de cache, gerido (s) e rede tem de estar na mesma região.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>Minha conta do Azure precisa de permissões para criar VMs?
Se for um administrador de subscrição, tem as permissões de replicação que precisa. Se não tiver, precisa de permissões para criar uma VM do Azure no grupo de recursos e especificar quando configurar a recuperação de sites e permissões para escrever na conta de armazenamento selecionada ou geridos com base em disco na sua configuração de rede virtual. [Saiba mais](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines).

### <a name="can-i-use-guest-os-server-license-on-azure"></a>Pode utilizar a licença de servidor do SO convidado no Azure?
Sim, podem utilizar os clientes do Microsoft Software Assurance [benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) para reduzir os custos de licenciamento **máquinas do Windows Server** que são migradas para o Azure ou utilizar o Azure para recuperação após desastre.

## <a name="pricing"></a>Preços

### <a name="how-are-licensing-charges-handled-during-replication-after-failover"></a>Como os custos de licenciamento são manipulados durante a replicação, após a ativação pós-falha?

Consulte nossas FAQ de licenciamento [aqui](https://aka.ms/asr_pricing_FAQ) para obter mais informações.

### <a name="how-can-i-calculate-approximate-charges-during-the-use-of-site-recovery"></a>Como calcular custos aproximados durante o uso do Site Recovery?

Pode usar [Calculadora de preços](https://aka.ms/asr_pricing_calculator) de estimar os custos ao utilizar o Azure Site Recovery. Para obter estimativa detalhada nos custos, execute a ferramenta deployment planner (https://aka.ms/siterecovery_deployment_planner) e analisar os [relatório de estimativa de custos](https://aka.ms/asr_DP_costreport).

### <a name="is-there-any-difference-in-cost-when-i-replicate-directly-to-managed-disk"></a>Existe alguma diferença no custo quando faço replicação diretamente para o disco gerido?

Discos geridos são cobrados ligeiramente diferente de contas de armazenamento. Veja o exemplo abaixo para um disco de origem do tamanho de 100 GiB. O exemplo é específico para diferencial custo de armazenamento. Esse custo não inclui o custo de armazenamento de cache, instantâneos e as transações.

* Conta de armazenamento Standard Vs. Disco gerido de HDD padrão

    - **Disco de armazenamento aprovisionado para ASR**: S10
    - **Conta de armazenamento Standard faturada à consumidos volume**: 5 $ por mês
    - **Disco gerido Standard cobrado no volume aprovisionado**: 5.89 us $ por mês

* Conta de armazenamento Premium Vs. Premium SSD Managed Disk 
    - **Disco de armazenamento aprovisionado para ASR**: P10
    - **Conta de armazenamento Premium é cobrada no volume aprovisionado**: 17.92 us $ por mês
    - **Disco gerido Premium cobrado no volume aprovisionado**: 17.92 us $ por mês

Obter mais informações sobre [detalhes de preços dos discos geridos](https://azure.microsoft.com/pricing/details/managed-disks/).

### <a name="do-i-incur-additional-charges-for-cache-storage-account-with-managed-disks"></a>Existem custos adicionais para a conta de armazenamento de Cache com discos geridos?

Não, que não incorra em custos adicionais para a cache. A cache sempre é parte do VMware para a arquitetura do Azure. Ao replicar a conta de armazenamento standard, este armazenamento de cache faz parte da mesma conta de armazenamento de destino.

### <a name="i-have-been-an-azure-site-recovery-user-for-over-a-month-do-i-still-get-the-first-31-days-free-for-every-protected-instance"></a>Sou utilizador do Azure Site Recovery há mais de um mês. Continuo a receber os primeiros 31 dias gratuitos para cada instância protegida?

Sim, não interessa há quanto tempo utiliza o Azure Site Recovery. Cada instância protegida não incorre em custos do Azure Site Recovery nos primeiros 31 dias. Por exemplo, se tiver dez instâncias protegidas há seis meses e ligar mais uma instância ao Azure Site Recovery, não existirão custos relativos ao Azure Site Recovery para a 11.ª instância nos primeiros 31 dias. As primeiras 10 instâncias continuam a incorrer em custos do Azure Site Recovery uma vez que estão protegidas há mais de 31 dias.

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>Durante os primeiros 31 dias, terei quaisquer outros custos com o Azure?

Sim, embora o Azure Site Recovery seja gratuito durante os primeiros 31 dias de uma instância protegida, pode incorrer em custos de Armazenamento do Azure, de transações de armazenamento e de transferência de dados. Uma máquina virtual recuperada também pode ter custos de computação do Azure.

### <a name="what-charges-do-i-incur-while-using-azure-site-recovery"></a>Em que custos incorro ao utilizar o Azure Site Recovery?

Consulte a nossa [FAQ sobre a custos incorridos](https://aka.ms/asr_pricing_FAQ) para obter informações detalhadas.

### <a name="is-there-a-cost-associated-to-perform-dr-drillstest-failover"></a>Existe um custo associado para efetuar a ativação pós-falha de explorações de DR/teste?

Não existe nenhum custo separado para exploração de DR. Haverá custos de computação depois da máquina virtual é criada a ativação pós-falha de teste da publicação.

## <a name="azure-site-recovery-components-upgrade"></a>Atualizar os componentes de recuperação de sites do Azure

### <a name="my-mobility-agentconfiguration-serverprocess-server-version-is-very-old-and-my-upgrade-has-failed-how-should-i-upgrade-to-latest-version"></a>Meu mobilidade/configuração do agente de servidor/versão do servidor de processo é muito antigo e meu atualização falhou. Como devo atualizar para versão mais recente?

O Azure Site Recovery segue o modelo de suporte de N-4. Consulte a nossa [instrução de suporte](https://aka.ms/asr_support_statement) para entender os detalhes sobre como atualizar a partir de versões muito antigas.

### <a name="where-can-i-find-the-release-notesupdate-rollups-of-azure-site-recovery"></a>Onde posso encontrar os versão notas/update rollups do Azure Site Recovery?

Consulte a [documento](https://aka.ms/asr_update_rollups) para obter informações de notas de versão. Pode encontrar ligações de instalação dos respectivos componentes em cada update rollup.

### <a name="how-should-i-upgrade-site-recovery-components-for-on-premises-vmware-or-physical-site-to-azure"></a>Como devo atualizar componentes do Site Recovery para VMware no local ou num site físico para o Azure?

Consulte a nossa orientação fornecida [aqui](https://aka.ms/asr_vmware_upgrades) atualizar seus componentes.

## <a name="is-reboot-of-source-machine-mandatory-for-each-upgrade"></a>Reiniciar o computador de origem é obrigatório para cada atualização?

Embora recomendada, não é obrigatório para cada atualização. Consultar [aqui](https://aka.ms/asr_vmware_upgrades) para limpar as diretrizes.

## <a name="on-premises"></a>Local

### <a name="what-do-i-need-on-premises"></a>O que fazer, preciso no local?

No local terá de:
- Componentes de recuperação, instalados numa única VM VMware do site.
- Uma infraestrutura do VMware, com, pelo menos, um anfitrião ESXi e vamos recomendar um vCenter server.
- Uma ou mais VMs de VMware para replicar.

[Saiba mais](vmware-azure-architecture.md) sobre VMware para a arquitetura do Azure.

O servidor de configuração no local pode ser implementado da seguinte forma:

- Recomendamos que implantar o servidor de configuração como uma VM do VMware com um modelo de OVA com o servidor de configuração previamente instalado.
- Se por qualquer motivo não é possível utilizar um modelo, pode configurar o servidor de configuração manualmente. [Saiba mais](physical-azure-disaster-recovery.md#set-up-the-source-environment).



### <a name="where-do-on-premises-vms-replicate-to"></a>Onde replicar VMs no local para o?
Dados são replicados para o armazenamento do Azure. Quando executa uma ativação pós-falha, o Site Recovery é automaticamente cria VMs do Azure da conta de armazenamento ou com base na sua configuração de disco gerido.

## <a name="replication"></a>Replicação

### <a name="what-applications-can-i-replicate"></a>Que aplicações posso replicar?
Pode replicar qualquer aplicação ou carga de trabalho em execução numa VM de VMware que está em conformidade com [requisitos de replicação](vmware-physical-azure-support-matrix.md##replicated-machines). Recuperação de sites fornece suporte para a replicação com suporte a aplicativos, para que aplicações podem ser realizadas a ativação pós-falha e falhou para um Estado inteligente. Site Recovery integra-se com aplicações da Microsoft, como SharePoint, Exchange, Dynamics, SQL Server e Active Directory e trabalha diretamente com fornecedores importantes, incluindo a Oracle, SAP, IBM e Red Hat. [Saiba mais](site-recovery-workload.md) sobre proteção de carga de trabalho.

### <a name="can-i-protect-a-virtual-machine-that-has-docker-disk-configuration"></a>Pode proteger uma máquina virtual que tem a configuração de disco do Docker?

Não, isso é um cenário não suportado.

### <a name="can-i-replicate-to-azure-with-a-site-to-site-vpn"></a>Pode replicar para o Azure com uma VPN de site a site?
O site Recovery replica dados no local para o armazenamento do Azure através de um ponto final público ou com peering público do ExpressRoute. Não é suportada a replicação através de uma rede VPN de site a site.

### <a name="can-i-replicate-to-azure-with-expressroute"></a>Pode replicar para o Azure com o ExpressRoute?
Sim, o ExpressRoute pode ser utilizado para replicar VMs no Azure. O site Recovery replica dados ao armazenamento do Azure através de um ponto final público. Terá de configurar [peering público](../expressroute/expressroute-circuit-peerings.md#publicpeering) ou [peering da Microsoft](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) para utilizar o ExpressRoute para replicação do Site Recovery. Peering da Microsoft é o domínio de encaminhamento recomendado para a replicação. Certifique-se de que o [requisitos de sistema de rede](vmware-azure-configuration-server-requirements.md#network-requirements) também foram atendidos para a replicação. Depois de VMs com a ativação pós-falha para uma rede virtual do Azure, pode acessá-los usando [peering privado](../expressroute/expressroute-circuit-peerings.md#privatepeering).

### <a name="how-can-i-change-storage-account-after-machine-is-protected"></a>Como posso alterar a conta de armazenamento depois do computador está protegido?

Terá de desativar e ativar a replicação atualizar ou mudar o tipo de conta de armazenamento.

### <a name="can-i-replicate-to-storage-accounts-for-new-machine"></a>Pode replicar para contas de armazenamento para a nova máquina?

Não, a partir do Mar "19, pode replicar para o managed disks no Azure no portal. A replicação para contas de armazenamento para uma nova máquina só está disponível por meio da REST API e Powershell. Utilize a versão 2016 a 08-10 ou 2018-01-10 de API para replicar a contas de armazenamento.

### <a name="what-are-the-benefits-in-replicating-to-managed-disks"></a>Quais são os benefícios no replicar para o managed disks?

Leia o artigo sobre como [do Azure Site Recovery simplifica a recuperação após desastre com discos geridos](https://azure.microsoft.com/blog/simplify-disaster-recovery-with-managed-disks-for-vmware-and-physical-servers/).

### <a name="how-can-i-change-managed-disk-type-after-machine-is-protected"></a>Como posso alterar o tipo de disco gerido depois do computador está protegido?

Sim, pode facilmente alterar o tipo de disco gerido. [Saiba mais](https://docs.microsoft.com/azure/virtual-machines/windows/convert-disk-storage). No entanto, depois de alterar o tipo de disco gerido, certifique-se de que aguardar que os pontos de recuperação nova seja gerado se precisa testar ativação pós-falha ou ativação pós-falha publicar esta atividade.

### <a name="can-i-switch-the-replication-from-managed-disks-to-unmanaged-disks"></a>Pode alternar a replicação a partir de discos geridos para discos não geridos?

Não, mudar de gerido para não gerenciado não é suportada.

### <a name="why-cant-i-replicate-over-vpn"></a>Por que não é possível replicar através de VPN?

Ao replicar para o Azure, o tráfego de replicação atinge os pontos finais públicos do armazenamento do Azure, assim, apenas pode replicar através da internet pública com o ExpressRoute (peering público) e VPN não funciona.

### <a name="can-i-use-riverbed-steelheads-for-replication"></a>Pode utilizar Riverbed SteelHeads para a replicação?

O nosso parceiro Riverbed, fornece uma orientação detalhada sobre como trabalhar com o Azure Site Recovery. Consulte seus [guia de soluções](https://community.riverbed.com/s/article/DOC-4627).

### <a name="what-are-the-replicated-vm-requirements"></a>Quais são os requisitos de VM replicados?

Para a replicação, uma VM do VMware tem de executar um sistema operativo suportado. Além disso, a VM tem de cumprir os requisitos para VMs do Azure. [Saiba mais](vmware-physical-azure-support-matrix.md##replicated-machines) na matriz de suporte.

### <a name="how-often-can-i-replicate-to-azure"></a>Com que frequência posso replicar para o Azure?
A replicação é contínua ao replicar VMs de VMware para Azure.

### <a name="can-i-retain-the-ip-address-on-failover"></a>Pode manter o endereço IP na ativação pós-falha?
Sim, pode manter o endereço IP na ativação pós-falha. Certifique-se de que mencionou o endereço IP de destino no painel "Computação e rede" antes da ativação pós-falha. Além disso, certifique-se para encerrar as máquinas no momento da ativação pós-falha para evitar conflitos IP no momento da reativação pós-falha.

### <a name="can-i-extend-replication"></a>Posso expandir a replicação?
Não é suportada a replicação expandida ou em cadeia. Pedir esta funcionalidade no [fórum de comentários](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-initial-replication"></a>Posso fazer uma replicação inicial offline?
Tal não é suportado. Pedir esta funcionalidade no [fórum de comentários](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-disks"></a>Pode excluir discos?
Sim, pode excluir discos da replicação.

### <a name="can-i-change-the-target-vm-size-or-vm-type-before-failover"></a>Posso alterar o tamanho da VM de destino ou o tipo VM antes da ativação pós-falha?
Sim, pode alterar o tipo ou tamanho da VM qualquer altura antes da ativação pós-falha, acedendo a computação e rede definições do item de replicação no portal.

### <a name="can-i-replicate-vms-with-dynamic-disks"></a>Pode replicar VMs com discos dinâmicos?
Discos dinâmicos podem ser replicados. O disco do sistema operativo tem de ser um disco básico.

### <a name="if-i-use-replication-groups-for-multi-vm-consistency-can-i-add-a-new-vm-to-an-existing-replication-group"></a>Se utilizar grupos de replicação consistência multi-VM, posso adicionar uma nova VM a um grupo de replicação existente?
Sim, pode adicionar novas VMs a um grupo de replicação existente quando ativa a replicação para eles. Não é possível adicionar uma VM a um grupo de replicação existente, depois de replicação é iniciada e não é possível criar um grupo de replicação de VMs existentes.

### <a name="can-i-modify-vms-that-are-replicating-by-adding-or-resizing-disks"></a>Pode modificar as VMs que estão a replicar com a adição ou redimensionar discos?

Para a replicação de VMware para o Azure pode modificar o tamanho do disco. Se pretender adicionar novos discos, que terá de adicionar o disco e reativar a proteção da VM.

### <a name="can-i-migrate-on-premises-machines-to-a-new-vcenter-without-impacting-ongoing-replication"></a>Posso migrar em máquinas no local para um novo Vcenter sem afetar a replicação em curso?
Não, a alteração do Vcenter ou migração irá afetar a replicação em curso. Terá de configurar a ASR com o novo Vcenter e ativar a replicação para máquinas.

### <a name="can-i-replicate-to-cachetarget-storage-account-which-has-a-vnet-with-azure-storage-firewalls-configured-on-it"></a>Pode replicar a conta de armazenamento de cache/destino que tem uma Vnet (com firewalls de armazenamento do Azure), configurada no mesmo?
Não, o Azure Site Recovery não suporta replicação para o armazenamento numa Vnet.

## <a name="configuration-server"></a>Servidor de configuração

### <a name="what-does-the-configuration-server-do"></a>O que faz o servidor de configuração?
O servidor de configuração é executado no local para componentes do Site Recovery, incluindo:
- O servidor de configuração que coordena as comunicações entre no local e o Azure e gere a replicação de dados.
- O servidor de processos atua como um gateway de replicação. Recebe dados de replicação. otimiza-os com colocação em cache, compressão e encriptação; e envia-o para o armazenamento do Azure., o servidor de processos também instala o serviço de mobilidade nas VMs que pretende replicar e efetua a deteção automática de VMs de VMware no local.
- O servidor de destino principal processa dados de replicação durante a reativação pós-falha do Azure.

[Saiba mais](vmware-azure-architecture.md) sobre os componentes de servidor de configuração e os processos.

### <a name="where-do-i-set-up-the-configuration-server"></a>Onde posso configurar o servidor de configuração?
Precisa de uma VM do VMware único elevada disponibilidade no local para o servidor de configuração.

### <a name="what-are-the-requirements-for-the-configuration-server"></a>Quais são os requisitos para o servidor de configuração?

Reveja os [pré-requisitos](vmware-azure-deploy-configuration-server.md#prerequisites).

### <a name="can-i-manually-set-up-the-configuration-server-instead-of-using-a-template"></a>Posso manualmente configurar o servidor de configuração em vez de utilizar um modelo?
Recomendamos que utilize a versão mais recente do modelo OVF para [criar a VM do servidor de configuração](vmware-azure-deploy-configuration-server.md). Se por algum motivo não pode, por exemplo não tem acesso ao servidor VMware, pode [transfira o ficheiro de configuração unificada](physical-azure-set-up-source.md) no portal, e executá-la numa VM.

### <a name="can-a-configuration-server-replicate-to-more-than-one-region"></a>Um servidor de configuração pode replicar para o mais do que uma região?
Não. Para tal, terá de configurar um servidor de configuração em cada região.

### <a name="can-i-host-a-configuration-server-in-azure"></a>Pode alojar um servidor de configuração no Azure?
Ainda que possível, a VM do Azure com o servidor de configuração têm de comunicar com a sua infraestrutura de VMware no local e VMs. Isso pode adicionar as latências e afetar os replicação em curso.

### <a name="how-do-i-update-the-configuration-server"></a>Como posso atualizar o servidor de configuração?
[Saiba mais sobre](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) a atualizar o servidor de configuração. Pode encontrar as informações de atualização mais recente na [página de atualizações do Azure](https://azure.microsoft.com/updates/?product=site-recovery). Pode transferir a versão mais recente do servidor de configuração de também diretamente [Microsoft Download Center](https://aka.ms/asrconfigurationserver). Se tiver uma versão mais antiga do que as 4 versões de versão atual, consulte a nossa [instrução de suporte](https://aka.ms/asr_support_statement) para atualização orientação.

### <a name="should-i-backup-the-deployed-configuration-server"></a>Deve posso criar cópias de segurança do servidor de configuração implementada?
Recomendamos que faça cópias de segurança agendadas regulares do servidor de configuração. Para a reativação pós-falha concluída com êxito, a máquina virtual que está a ser realizarão a reativação pós-falha tem de existir na base de dados de servidor de configuração e o servidor de configuração tem de estar em execução e num estado ligado. Pode saber mais sobre tarefas de gestão de servidor de configuração comuns [aqui](vmware-azure-manage-configuration-server.md).

### <a name="when-im-setting-up-the-configuration-server-can-i-download-and-install-mysql-manually"></a>Quando eu estou configurar o servidor de configuração, posso transferir e instalar manualmente o MySQL?

Sim. Transferir o MySQL e colocá-la a **C:\Temp\ASRSetup** pasta. Em seguida, instale manualmente. Quando configurar a VM do servidor de configuração e aceite os termos, MySQL será listada como **já instalado** na **transfira e instale**.

### <a name="can-i-avoid-downloading-mysql-but-let-site-recovery-install-it"></a>Pode evitar o download de MySQL mas permitir que o Site Recovery instalá-lo?

Sim. Transferir o instalador do MySQL e colocá-la a **C:\Temp\ASRSetup** pasta.  Ao configurar a VM do servidor de configuração, aceite os termos e clique em **transferir e instalar**, o portal irá utilizar o instalador que adicionou ao instalar o MySQL.
 
### <a name="can-i-use-the-configuration-server-vm-for-anything-else"></a>Pode utilizar o servidor de configuração de VM para qualquer outra coisa?
Não, só deve utilizar a VM para o servidor de configuração. 

### <a name="can-i-clone-a-configuration-server-and-use-it-for-orchestration"></a>Pode clonar um servidor de configuração e utilizá-lo para orquestração?
Não, deve configurar um servidor de configuração atualizados para evitar problemas de registo.

### <a name="can-i-change-the-vault-registered-in-the-configuration-server"></a>Pode alterar o Cofre registado no servidor de configuração?
Não. Depois de um cofre é registrado no servidor de configuração, não pode ser alterado. Revisão [este artigo](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault) para obter os passos de re-registo.

### <a name="can-i-use-the-same-configuration-server-for-disaster-recovery-of-both-vmware-vms-and-physical-servers"></a>Pode utilizar o mesmo servidor de configuração para recuperação após desastre de VMs de VMware e servidores físicos
Sim, mas tenha em atenção que a máquina física pode apenas ser falha novamente para uma VM do VMware.

### <a name="where-can-i-download-the-passphrase-for-the-configuration-server"></a>Onde posso transferir a frase de acesso para o servidor de configuração?
[Rever este artigo](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase) para saber mais sobre a frase de acesso a transferir.

### <a name="where-can-i-download-vault-registration-keys"></a>Onde posso transferir as chaves de registo do Cofre?

Na **Cofre de serviços de recuperação**, **gerir** > **infraestrutura do Site Recovery** > **deservidoresdeconfiguração**. Na **servidores**, selecione **chave de registo do Download** para transferir o ficheiro de credenciais do cofre.



## <a name="mobility-service"></a>Serviço de Mobilidade

### <a name="where-can-i-find-the-mobility-service-installers"></a>Onde posso encontrar os programas de instalação do serviço de mobilidade?
Os programas de instalação são guardados na **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository** pasta no servidor de configuração.

## <a name="how-do-i-install-the-mobility-service"></a>Como posso instalar o serviço de mobilidade?
Instalar em cada VM que pretende replicar, com um [instalação push](vmware-azure-install-mobility-service.md), ou [instalação manual](vmware-physical-mobility-service-install-manual.md) partir da IU ou do Powershell. Em alternativa, pode implementar utilizando uma ferramenta de implantação, como [System Center Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md).



## <a name="security"></a>Segurança

### <a name="what-access-does-site-recovery-need-to-vmware-servers"></a>Que acesso o Site Recovery precisa aos servidores VMware?
O Site Recovery precisa de ter acesso a servidores VMware para:

- Configure uma VM de VMware que executam o servidor de configuração e outros componentes do Site Recovery no local. [Saiba mais](vmware-azure-deploy-configuration-server.md)
- Detete automaticamente as VMs para a replicação. Saiba mais sobre como preparar uma conta para a deteção automática. [Saiba mais](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)


### <a name="what-access-does-site-recovery-need-to-vmware-vms"></a>Que acesso o Site Recovery precisa de VMs do VMware?

- Para replicar, uma VM de VMware tem de ter o serviço de mobilidade de recuperação de sites instalada e em execução. Pode implementar a ferramenta manualmente, ou especificar que a recuperação de Site deve fazer uma instalação push do serviço, quando ativa a replicação para uma VM. Para a instalação de push, recuperação de sites precisa de uma conta que pode utilizar para instalar o componente de serviço. [Saiba mais](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-mobility-service-installation). O servidor de processos (em execução por predefinição no servidor de configuração) executa esta instalação. [Saiba mais](vmware-azure-install-mobility-service.md) sobre a instalação do serviço de mobilidade.
- Durante a replicação, VMs comunicam com o Site Recovery da seguinte forma:
    - As VMs a comunicar com o servidor de configuração na porta HTTPS 443 para a gestão de replicação.
    - VMs enviam dados de replicação para o servidor de processos na porta HTTPS 9443 (pode ser modificada).
    - Se ativar a consistência multi-VM, as VMs comunicam entre si pela porta 20004.


### <a name="is-replication-data-sent-to-site-recovery"></a>Dados de replicação são enviados para o Site Recovery?
Não, a recuperação de sites não interceta os dados replicados e não tem quaisquer informações sobre o que está em execução nas suas VMs. Os dados de replicação são trocados entre os hipervisores de VMware e de armazenamento do Azure. A Recuperação de Sites não tem capacidade para intercetar esses dados. Apenas os metadados necessários para orquestrar a replicação e a ativação pós-falha são enviados para o serviço de Recuperação de Sites.  

Site Recovery é ISO 27001:2013, 27018, HIPAA, DPA certified e está no processo de SOC2 e FedRAMP JAB.

### <a name="can-we-keep-on-premises-metadata-within-a-geographic-regions"></a>Vamos guardar os metadados de locais dentro de um regiões geográficas?
Sim. Quando cria um cofre numa região, podemos assegurar que todos os metadados usados pelo Site Recovery permanece dentro do limite de geográfico dessa região.

### <a name="does-site-recovery-encrypt-replication"></a>A Recuperação de Sites faz encriptação de replicação?
Sim, ambas as encriptação em trânsito e [encriptação no Azure](https://docs.microsoft.com/azure/storage/storage-service-encryption) são suportados.


## <a name="failover-and-failback"></a>Ativação pós-falha e reativação pós-falha
### <a name="can-i-use-the-process-server-at-on-premises-for-failback"></a>Posso utilizar o servidor de processos no local para a reativação pós-falha?
É vivamente recomendado para criar um servidor de processos no Azure para finalidade de reativação pós-falha para evitar latências de transferência de dados. Além disso, no caso de separado da rede de VMs de origem com a rede com acesso do Azure no servidor de configuração, em seguida, é essencial para utilizar o servidor de processo criado no Azure para reativação pós-falha.

### <a name="how-far-back-can-i-recover"></a>Qual a amplitude posso recuperar?
Para VMware para o Azure, o ponto de recuperação mais antigo, que pode usar é 72 horas.

### <a name="how-do-i-access-azure-vms-after-failover"></a>Como posso acessar as VMs do Azure após a ativação pós-falha?
Após a ativação pós-falha, pode acessar as VMs do Azure através de uma ligação de Internet segura, através de uma VPN de site a site ou através do Azure ExpressRoute. Terá de preparar uma série de coisas para se ligar. [Saiba mais](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)

### <a name="is-failed-over-data-resilient"></a>É efetuada a ativação pós-falha dados resilientes?
O Azure foi concebido para ser resiliente. Recuperação de site foi desenvolvida para ativação pós-falha para um datacenter secundário do Azure, de acordo com o SLA do Azure. Quando ocorre a ativação pós-falha, devemos verificar se os metadados e cofres permanecem na mesma região geográfica que escolheu para o cofre.

### <a name="is-failover-automatic"></a>A ativação pós-falha é automática?
[Ativação pós-falha](site-recovery-failover.md) não é automática. Inicia as ativações pós-falha com um clique único no portal ou pode utilizar [PowerShell](/powershell/module/azurerm.siterecovery) para acionar uma ativação pós-falha.

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

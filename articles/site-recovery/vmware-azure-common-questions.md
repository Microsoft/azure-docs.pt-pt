---
title: Perguntas comuns sobre a recuperação de desastres do VMware com o Azure Site Recovery
description: Obtenha respostas para perguntas comuns sobre a recuperação de desastre de VMs VMware locais para o Azure usando Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.date: 11/14/2019
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: 16892ec729f56f8c8e1713379285e07fbc0dd4d1
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/26/2019
ms.locfileid: "75495421"
---
# <a name="common-questions-about-vmware-to-azure-replication"></a>Perguntas comuns sobre a replicação do VMware para o Azure

Este artigo responde a perguntas comuns que podem surgir ao implantar a recuperação de desastre de VMs (máquinas virtuais) VMware locais no Azure.

## <a name="general"></a>Geral

### <a name="what-do-i-need-for-vmware-vm-disaster-recovery"></a>O que é necessário para a recuperação de desastres de VM VMware?

[Saiba mais sobre os componentes envolvidos](vmware-azure-architecture.md) na recuperação de desastres de VMs VMware.

### <a name="can-i-use-site-recovery-to-migrate-vmware-vms-to-azure"></a>Posso usar Site Recovery para migrar VMs VMware para o Azure?

Sim. Além de usar Site Recovery para configurar a recuperação de desastre completa para VMs VMware, você também pode usar Site Recovery para migrar VMs VMware locais para o Azure. Nesse cenário, você Replica VMs VMware locais para o armazenamento do Azure. Em seguida, faça failover do local para o Azure. Após o failover, seus aplicativos e cargas de trabalho estão disponíveis e em execução em VMs do Azure. O processo é como configurar a recuperação de desastre completa, exceto que, em uma migração, você não pode fazer failback do Azure.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>Minha conta do Azure precisa de permissões para criar VMs?

Se você for um administrador de assinatura, terá as permissões de replicação de que precisa. Se você não for um administrador, precisará de permissões para executar estas ações:

- Crie uma VM do Azure no grupo de recursos e na rede virtual que você especificar ao configurar o Site Recovery.
- Grave na conta de armazenamento ou no disco gerenciado selecionado com base em sua configuração.

[Saiba mais](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) sobre as permissões necessárias.

### <a name="what-applications-can-i-replicate"></a>Quais aplicativos posso replicar?

Você pode replicar qualquer aplicativo ou carga de trabalho em execução em uma VM VMware que atenda aos [requisitos de replicação](vmware-physical-azure-support-matrix.md#replicated-machines).

- O Site Recovery dá suporte à replicação com reconhecimento de aplicativos, para que os aplicativos possam realizar failover e failback para um estado inteligente.
- O Site Recovery integra-se com aplicativos da Microsoft, como SharePoint, Exchange, Dynamics, SQL Server e Active Directory. Ele também funciona em conjunto com os principais fornecedores, incluindo Oracle, SAP, IBM e Red Hat.

[Saiba mais](site-recovery-workload.md) sobre proteção de carga de trabalho.

### <a name="can-i-use-a-guest-os-server-license-on-azure"></a>Posso usar uma licença de servidor de SO convidado no Azure?

Sim, os clientes do Microsoft Software Assurance podem usar [benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) para economizar em custos de licenciamento para computadores Windows Server que são migrados para o Azure ou para usar o Azure para recuperação de desastres.

## <a name="security"></a>Segurança

### <a name="what-access-to-vmware-servers-does-site-recovery-need"></a>Que tipo de acesso a servidores VMware Site Recovery precisa?

O Site Recovery precisa de ter acesso a servidores VMware para:

- Configure uma VM do VMware executando o servidor de configuração Site Recovery.
- Descobrir automaticamente as VMs para replicação.

### <a name="what-access-to-vmware-vms-does-site-recovery-need"></a>Que tipo de acesso às VMs VMware Site Recovery precisa?

- Para replicar, uma VM VMware deve ter o serviço de mobilidade Site Recovery instalado e em execução. Você pode implantar a ferramenta manualmente ou pode especificar que Site Recovery fazer uma instalação por push do serviço ao habilitar a replicação para uma VM.
- Durante a replicação, as VMs se comunicam com Site Recovery da seguinte maneira:
    - As VMs se comunicam com o servidor de configuração na porta HTTPS 443 para o gerenciamento de replicação.
    - As VMs enviam dados de replicação para o servidor de processo na porta HTTPS 9443. (Essa configuração pode ser modificada.)
    - Se você habilitar a consistência de várias VMS, as VMs se comunicarão umas com as outras pela porta 20004.

### <a name="is-replication-data-sent-to-site-recovery"></a>Os dados de replicação são enviados para Site Recovery?

Não, Site Recovery não intercepta dados replicados e não tem nenhuma informação sobre o que está sendo executado em suas VMs. Os dados de replicação são trocados entre hipervisores VMware e armazenamento do Azure. A Recuperação de Sites não tem capacidade para intercetar esses dados. Apenas os metadados necessários para orquestrar a replicação e a ativação pós-falha são enviados para o serviço de Recuperação de Sites.  

Site Recovery é certificado para ISO 27001:2013 e 27018, HIPAA e DPA. Ele está no processo de avaliações SOC2 e FedRAMP JAB.

## <a name="pricing"></a>Preços

### <a name="how-do-i-calculate-approximate-charges-for-vmware-disaster-recovery"></a>Como fazer calcular encargos aproximados para a recuperação de desastres do VMware?

Use a [calculadora de preços](https://aka.ms/asr_pricing_calculator) para estimar os custos ao usar site Recovery.

Para obter uma estimativa detalhada dos custos, execute a ferramenta planejador de implantação para [VMware](https://aka.ms/siterecovery_deployment_planner) e use o [relatório estimativa de custo](https://aka.ms/asr_DP_costreport).

### <a name="is-there-any-difference-in-cost-between-replicating-to-storage-or-directly-to-managed-disks"></a>Há alguma diferença no custo entre replicar para armazenamento ou diretamente para discos gerenciados?

Os discos gerenciados são cobrados de forma ligeiramente diferente das contas de armazenamento. [Saiba mais](https://azure.microsoft.com/pricing/details/managed-disks/) sobre os preços do disco gerenciado.

### <a name="is-there-any-difference-in-cost-when-replicating-to-general-purpose-v2-storage-account"></a>Há alguma diferença no custo ao replicar para Uso Geral conta de armazenamento v2?

Normalmente, você verá um aumento no custo das transações incorridos nas contas de armazenamento GPv2, uma vez que Azure Site Recovery tem transações pesadas. [Leia mais](../storage/common/storage-account-upgrade.md#pricing-and-billing) para estimar a alteração.

## <a name="mobility-service"></a>Serviço de Mobilidade

### <a name="where-can-i-find-the-mobility-service-installers"></a>Onde posso encontrar os instaladores do serviço de mobilidade?

Os instaladores estão na pasta%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository no servidor de configuração.

## <a name="how-do-i-install-the-mobility-service"></a>Como fazer instalar o serviço de mobilidade?

Em cada VM que você deseja replicar, instale o serviço por um dos vários métodos:

- [Instalação por push](vmware-physical-mobility-service-overview.md#push-installation)
- [Instalação manual](vmware-physical-mobility-service-overview.md#install-mobility-agent-through-ui) da interface do usuário ou do PowerShell
- Implantação usando uma ferramenta de implantação como o [System Center Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md)

## <a name="managed-disks"></a>Managed Disks

### <a name="where-does-site-recovery-replicate-data-to"></a>Para onde Site Recovery replicar dados?

Site Recovery Replica VMs VMware locais e servidores físicos para Managed disks no Azure.

- O servidor de processo de Site Recovery grava os logs de replicação em uma conta de armazenamento de cache na região de destino.
- Esses logs são usados para criar pontos de recuperação em discos gerenciados pelo Azure que têm o prefixo de **asrseeddisk**.
- Quando ocorre o failover, o ponto de recuperação selecionado é usado para criar um novo disco gerenciado de destino. Esse disco gerenciado é anexado à VM no Azure.
- As VMs que foram replicadas anteriormente para uma conta de armazenamento (antes de março de 2019) não são afetadas.

### <a name="can-i-replicate-new-machines-to-storage-accounts"></a>Posso replicar novos computadores para contas de armazenamento?

Não. A partir de março de 2019, no portal do Azure, você pode replicar somente para o Azure Managed disks.

A replicação de novas VMs para uma conta de armazenamento está disponível somente usando o PowerShell ou a API REST (versão 2018-01-10 ou 2016-08-10).

### <a name="what-are-the-benefits-of-replicating-to-managed-disks"></a>Quais são os benefícios da replicação para o Managed disks?

[Saiba como](https://azure.microsoft.com/blog/simplify-disaster-recovery-with-managed-disks-for-vmware-and-physical-servers/) Site Recovery simplifica a recuperação de desastres com discos gerenciados.

### <a name="can-i-change-the-managed-disk-type-after-a-machine-is-protected"></a>Posso alterar o tipo de disco gerenciado depois que um computador é protegido?

Sim, você pode [alterar facilmente o tipo de disco gerenciado](https://docs.microsoft.com/azure/virtual-machines/windows/convert-disk-storage) para replicações em andamento. Antes de alterar o tipo, certifique-se de que nenhuma URL de assinatura de acesso compartilhado seja gerada no disco gerenciado:

1. Vá para o recurso de **disco gerenciado** no portal do Azure e verifique se você tem uma faixa de URL de assinatura de acesso compartilhado na folha **visão geral** .
1. Se a faixa estiver presente, selecione-a para cancelar a exportação em andamento.
1. Altere o tipo do disco nos próximos minutos. Se você alterar o tipo de disco gerenciado, aguarde até que os pontos de recuperação novos sejam gerados pelo Azure Site Recovery.
1. Use os novos pontos de recuperação para qualquer failover de teste ou failover no futuro.

### <a name="can-i-switch-replication-from-managed-disks-to-unmanaged-disks"></a>Posso alternar a replicação de discos gerenciados para discos não gerenciados?

Não. Não há suporte para alternar de gerenciado para não gerenciado.

## <a name="replication"></a>Replicação

### <a name="what-are-the-replicated-vm-requirements"></a>Quais são os requisitos de VM replicados?

[Saiba mais](vmware-physical-azure-support-matrix.md#replicated-machines) sobre os requisitos de suporte para VMs VMware e servidores físicos.

### <a name="how-often-can-i-replicate-to-azure"></a>Com que frequência eu posso replicar no Azure?

A replicação é contínua ao replicar VMs VMware para o Azure.

### <a name="can-i-extend-replication"></a>Posso estender a replicação?

Não é suportada a replicação expandida ou em cadeia. Solicite esse recurso no [Fórum de comentários](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-initial-replication"></a>Posso fazer uma replicação inicial offline?

Não há suporte para replicação offline. Solicite esse recurso no [Fórum de comentários](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="what-is-asrseeddisk"></a>O que é o asrseeddisk?

Para cada disco de origem, os dados são replicados em um disco gerenciado no Azure. Esse disco tem o prefixo de **asrseeddisk**. Ele armazena a cópia do disco de origem e todos os instantâneos de ponto de recuperação.

### <a name="can-i-exclude-disks-from-replication"></a>Posso excluir discos da replicação?

Sim, você pode excluir discos.

### <a name="can-i-replicate-vms-that-have-dynamic-disks"></a>Posso replicar VMs que têm discos dinâmicos?

Discos dinâmicos podem ser replicados. O disco do sistema operacional deve ser um disco básico.

### <a name="if-i-use-replication-groups-for-multi-vm-consistency-can-i-add-a-new-vm-to-an-existing-replication-group"></a>Se eu usar grupos de replicação para consistência de várias VMS, posso adicionar uma nova VM a um grupo de replicação existente?

Sim, você pode adicionar novas VMs a um grupo de replicação existente ao habilitar a replicação para eles. No entanto,

- Você não pode adicionar uma VM a um grupo de replicação existente após o início da replicação.
- Você não pode criar um grupo de replicação para VMs existentes.

### <a name="can-i-modify-vms-that-are-replicating-by-adding-or-resizing-disks"></a>Posso modificar as VMs que estão replicando adicionando ou redimensionando discos?

Para a replicação do VMware para o Azure, você pode modificar o tamanho do disco das VMs de origem. Se você quiser adicionar novos discos, deverá adicionar o disco e reabilitar a proteção para a VM.

### <a name="can-i-migrate-on-premises-machines-to-a-new-vcenter-server-without-impacting-ongoing-replication"></a>Posso migrar computadores locais para um novo vCenter Server sem afetar a replicação em andamento?

Não. Uma alteração do VMware vCenter ou da migração afetará a replicação em andamento. Configure Site Recovery com o novo vCenter Server e habilite a replicação para computadores novamente.

### <a name="can-i-replicate-to-a-cache-or-target-storage-account-that-has-a-virtual-network-with-azure-firewalls-configured-on-it"></a>Posso replicar para uma conta de armazenamento de destino ou de cache que tenha uma rede virtual (com firewalls do Azure) configurada nele?

Não, Site Recovery não dá suporte à replicação para o armazenamento do Azure em redes virtuais.

## <a name="component-upgrade"></a>Atualização de componente

### <a name="my-version-of-the-mobility-services-agent-or-configuration-server-is-old-and-my-upgrade-failed-what-do-i-do"></a>Minha versão do agente de serviços de mobilidade ou do servidor de configuração é antiga e a minha atualização falhou. O que devo fazer?

Site Recovery segue o modelo de suporte N-4. [Saiba mais](https://aka.ms/asr_support_statement) sobre como atualizar de versões muito antigas.

### <a name="where-can-i-find-the-release-notes-and-update-rollups-for-azure-site-recovery"></a>Onde posso encontrar as notas de versão e os pacotes cumulativos de atualizações para Azure Site Recovery?

[Saiba mais sobre novas atualizações](site-recovery-whats-new.md)e [Obtenha informações de rollup](service-updates-how-to.md).

### <a name="where-can-i-find-upgrade-information-for-disaster-recovery-to-azure"></a>Onde posso encontrar informações de atualização para recuperação de desastres no Azure?

[Saiba mais sobre a atualização](https://aka.ms/asr_vmware_upgrades).

## <a name="do-i-need-to-reboot-source-machines-for-each-upgrade"></a>É necessário reinicializar computadores de origem para cada atualização?

Uma reinicialização é recomendada, mas não obrigatória para cada atualização. [Saiba mais](https://aka.ms/asr_vmware_upgrades).

## <a name="configuration-server"></a>Servidor de configuração

### <a name="what-does-the-configuration-server-do"></a>O que o servidor de configuração faz?

O servidor de configuração executa os componentes de Site Recovery locais, incluindo:

- O próprio servidor de configuração. O servidor coordena as comunicações entre os componentes locais e o Azure e gerencia a replicação de dados.
- O servidor de processo, que atua como um gateway de replicação. Este servidor:
    1. Recebe os dados de replicação.
    2. Otimiza os dados com caching, compactação e criptografia.
    3. Envia os dados para o armazenamento do Azure.
  O servidor de processo também faz uma instalação por push do serviço de mobilidade em VMs e executa a descoberta automática de VMs VMware locais.
- O servidor de destino mestre, que lida com os dados de replicação durante o failback do Azure.

[Saiba mais](vmware-azure-architecture.md) sobre os componentes e processos do servidor de configuração.

### <a name="where-do-i-set-up-the-configuration-server"></a>Onde configuro o servidor de configuração?

Você precisa de uma VM VMware local única e altamente disponível para o servidor de configuração. Para a recuperação de desastres do servidor físico, instale o servidor de configuração em um computador físico.

### <a name="what-do-i-need-for-the-configuration-server"></a>O que é necessário para o servidor de configuração?

Examine os [pré-requisitos](vmware-azure-deploy-configuration-server.md#prerequisites).

### <a name="can-i-manually-set-up-the-configuration-server-instead-of-using-a-template"></a>Posso configurar manualmente o servidor de configuração em vez de usar um modelo?

Recomendamos que você [crie a VM do servidor de configuração](vmware-azure-deploy-configuration-server.md) usando a versão mais recente do modelo de Open VIRTUALIZATION Format (OVF). Se você não puder usar o modelo (por exemplo, se não tiver acesso ao servidor VMware), [Baixe](physical-azure-set-up-source.md) o arquivo de instalação do portal e configure o servidor de configuração.

### <a name="can-a-configuration-server-replicate-to-more-than-one-region"></a>Um servidor de configuração pode replicar para mais de uma região?

Não. Para replicar em mais de uma região, você precisa de um servidor de configuração em cada região.

### <a name="can-i-host-a-configuration-server-in-azure"></a>Posso hospedar um servidor de configuração no Azure?

Embora seja possível, a VM do Azure que executa o servidor de configuração precisaria se comunicar com sua infraestrutura do VMware e VMs locais. Essa comunicação adiciona latência e afeta a replicação contínua.

### <a name="how-do-i-update-the-configuration-server"></a>Como fazer atualizar o servidor de configuração?

[Saiba](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) como atualizar o servidor de configuração.

- Você pode encontrar as informações de atualização mais recentes na [página atualizações do Azure](https://azure.microsoft.com/updates/?product=site-recovery).
- Você pode baixar a versão mais recente no Portal. Ou então, você pode baixar a versão mais recente do servidor de configuração diretamente do [centro de download da Microsoft](https://aka.ms/asrconfigurationserver).
- Se sua versão tiver mais de quatro versões anteriores à versão atual, consulte a [declaração de suporte](https://aka.ms/asr_support_statement) para obter diretrizes de atualização.

### <a name="should-i-back-up-the-configuration-server"></a>Devo fazer backup do servidor de configuração?

Recomendamos fazer backups agendados regulares do servidor de configuração.

- Para um failback bem-sucedido, a VM que está sendo reprovada deve existir no banco de dados do servidor de configuração.
- O servidor de configuração deve estar em execução e em um estado conectado.
- [Saiba mais](vmware-azure-manage-configuration-server.md) sobre as tarefas comuns de gerenciamento do servidor de configuração.

### <a name="when-im-setting-up-the-configuration-server-can-i-download-and-install-mysql-manually"></a>Quando estou Configurando o servidor de configuração, posso baixar e instalar o MySQL manualmente?

Sim. Baixe o MySQL e coloque-o na pasta C:\Temp\ASRSetup Em seguida, instale-o manualmente. Quando você configurar a VM do servidor de configuração e aceitar os termos, o MySQL será listado como **já instalado** em **baixar e instalar**.

### <a name="can-i-avoid-downloading-mysql-but-let-site-recovery-install-it"></a>Posso evitar o download do MySQL, mas permitir que Site Recovery o instale?

Sim. Baixe o instalador do MySQL e coloque-o na pasta C:\Temp\ASRSetup Ao configurar a VM do servidor de configuração, aceite os termos e selecione **baixar e instalar**. O portal usará o instalador que você adicionou para instalar o MySQL.

### <a name="can-i-use-the-configuration-server-vm-for-anything-else"></a>Posso usar a VM do servidor de configuração para qualquer outra coisa?

Não. Use a VM somente para o servidor de configuração.

### <a name="can-i-clone-a-configuration-server-and-use-it-for-orchestration"></a>Posso clonar um servidor de configuração e usá-lo para orquestração?

Não. Configure um novo servidor de configuração para evitar problemas de registro.

### <a name="can-i-change-the-vault-in-which-the-configuration-server-is-registered"></a>Posso alterar o cofre no qual o servidor de configuração está registrado?

Não. Depois que um cofre é associado ao servidor de configuração, ele não pode ser alterado. [Saiba mais](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault) sobre como registrar um servidor de configuração com um cofre diferente.

### <a name="can-i-use-the-same-configuration-server-for-disaster-recovery-of-both-vmware-vms-and-physical-servers"></a>Posso usar o mesmo servidor de configuração para recuperação de desastre de VMs VMware e servidores físicos?

Sim, mas observe que a máquina física pode passar por failback apenas para uma VM VMware.

### <a name="where-can-i-download-the-passphrase-for-the-configuration-server"></a>Onde posso baixar a senha para o servidor de configuração?

[Saiba](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase) como baixar a frase secreta.

### <a name="where-can-i-download-vault-registration-keys"></a>Onde posso baixar chaves de registro do cofre?

No cofre dos serviços de recuperação, selecione **servidores de configuração** no **site Recovery infraestrutura** > **gerenciar**. Em seguida, em **servidores**, selecione **baixar chave de registro** para baixar o arquivo de credenciais do cofre.

### <a name="can-a-single-configuration-server-be-used-to-protect-multiple-vcenter-instances"></a>Um único servidor de configuração pode ser usado para proteger várias instâncias do vCenter?

Sim, um único servidor de configuração pode proteger VMs entre vários vCenters.  Não há limite de quantas instâncias do vCenter podem ser adicionadas ao servidor de configuração, no entanto, os limites de quantas VMs um único servidor de configuração pode proteger se aplicam.

### <a name="can-a-single-configuration-server-protect-multiple-clusters-within-vcenter"></a>Um único servidor de configuração pode proteger vários clusters no vCenter?

Sim, Azure Site Recovery pode proteger VMs em diferentes clusters.

## <a name="process-server"></a>Servidor de processo

### <a name="why-am-i-unable-to-select-the-process-server-when-i-enable-replication"></a>Por que não posso selecionar o servidor de processo quando habilito a replicação?

As atualizações nas versões 9,24 e posteriores agora exibem a [integridade do servidor de processo quando você habilita a replicação](vmware-azure-enable-replication.md#enable-replication). Esse recurso ajuda a evitar a limitação do servidor de processo e minimizar o uso de servidores de processo não íntegros.

### <a name="how-do-i-update-the-process-server-to-version-924-or-later-for-accurate-health-information"></a>Como fazer atualizar o servidor de processo para a versão 9,24 ou posterior para obter informações precisas de integridade?

A partir da [versão 9,24](service-updates-how-to.md#links-to-currently-supported-update-rollups), mais alertas foram adicionados para indicar a integridade do servidor de processo. [Atualize seus componentes do site Recovery para a versão 9,24 ou posterior](service-updates-how-to.md#links-to-currently-supported-update-rollups) para que todos os alertas sejam gerados.

## <a name="failover-and-failback"></a>Ativação pós-falha e reativação pós-falha

### <a name="can-i-use-the-on-premises-process-server-for-failback"></a>Posso usar o servidor de processo local para failback?

É altamente recomendável criar um servidor de processo no Azure para fins de failback, para evitar latências de transferência de dados. Além disso, caso você tenha separado a rede de VMs de origem com a rede voltada para o Azure no servidor de configuração, é essencial usar o servidor de processo criado no Azure para failback.

### <a name="can-i-keep-the-ip-address-on-failover"></a>Posso manter o endereço IP no failover?

Sim, você pode manter o endereço IP no failover. Certifique-se de especificar o endereço IP de destino nas configurações de **computação e rede** para a VM antes do failover. Além disso, desligue os computadores no momento do failover para evitar conflitos de endereço IP durante o failback.

### <a name="can-i-change-the-target-vm-size-or-vm-type-before-failover"></a>Posso alterar o tamanho ou o tipo da VM de destino antes do failover?

Sim, você pode alterar o tipo ou o tamanho da VM a qualquer momento antes do failover. No portal, use as configurações de **computação e rede** para a VM replicada.

### <a name="how-far-back-can-i-recover"></a>Até que distância posso recuperar?

Para o VMware para o Azure, o ponto de recuperação mais antigo que você pode usar é de 72 horas.

### <a name="how-do-i-access-azure-vms-after-failover"></a>Como fazer acessar VMs do Azure após o failover?

Após o failover, você pode acessar as VMs do Azure por meio de uma conexão segura com a Internet, em uma VPN site a site ou no Azure ExpressRoute. Para se conectar, você deve preparar várias coisas. [Saiba mais](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).

### <a name="is-failed-over-data-resilient"></a>O failover de dados é resiliente?

O Azure foi concebido para ser resiliente. O Site Recovery é projetado para failover para um datacenter secundário do Azure, conforme exigido pelo contrato de nível de serviço (SLA) do Azure. Quando ocorre o failover, verificamos se os seus metadados e cofres permanecem na mesma região geográfica que você escolheu para o cofre.

### <a name="is-failover-automatic"></a>A ativação pós-falha é automática?

O [failover](site-recovery-failover.md) não é automático. Você inicia um failover fazendo uma única seleção no portal ou pode usar o [PowerShell](/powershell/module/az.recoveryservices) para disparar um failover.

### <a name="can-i-fail-back-to-a-different-location"></a>Posso fazer failback para um local diferente?

Sim. Se você fez failover no Azure, poderá fazer failback para um local diferente se o original não estiver disponível. [Saiba mais](concepts-types-of-failback.md#alternate-location-recovery-alr).

### <a name="why-do-i-need-a-vpn-or-expressroute-with-private-peering-to-fail-back"></a>Por que preciso de uma VPN ou ExpressRoute com emparelhamento privado para fazer failback?

Quando você realiza o failback do Azure, os dados do Azure são copiados de volta para sua VM local e o acesso privado é necessário.


## <a name="automation-and-scripting"></a>Automação e scripts

### <a name="can-i-set-up-replication-with-scripting"></a>Posso configurar a replicação com scripts?

Sim. Você pode automatizar fluxos de trabalho de Site Recovery usando a API REST, o PowerShell ou o SDK do Azure. [Saiba mais](vmware-azure-disaster-recovery-powershell.md).

## <a name="performance-and-capacity"></a>Desempenho e capacidade

### <a name="can-i-throttle-replication-bandwidth"></a>Posso limitar a largura de banda de replicação?

Sim. [Saiba mais](site-recovery-plan-capacity-vmware.md).

## <a name="next-steps"></a>Passos seguintes

- [Examine](vmware-physical-azure-support-matrix.md) os requisitos de suporte.
- [Configurar](vmware-azure-tutorial.md) Replicação do VMware para o Azure.

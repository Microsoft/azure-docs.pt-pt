---
title: Questões comuns sobre recuperação de desastres VMware com recuperação do site Azure
description: Obtenha respostas a perguntas comuns sobre a recuperação de desastres de VMware VMs para Azure utilizando a Recuperação do Site Azure.
ms.date: 11/14/2019
ms.topic: conceptual
ms.openlocfilehash: 8f292e7f624b80e8e13514a714c5759d88fbe57c
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2020
ms.locfileid: "94379995"
---
# <a name="common-questions-about-vmware-to-azure-replication"></a>Perguntas comuns sobre a replicação do VMware para o Azure

Este artigo responde a questões comuns que podem surgir quando implementa a recuperação de desastres de máquinas virtuais VMware (VMs) no local para Azure.

## <a name="general"></a>Geral

### <a name="what-do-i-need-for-vmware-vm-disaster-recovery"></a>O que preciso para a recuperação de desastres em VMware VMware?

[Conheça os componentes envolvidos](vmware-azure-architecture.md) na recuperação de desastres de VMware VMs.

### <a name="can-i-use-site-recovery-to-migrate-vmware-vms-to-azure"></a>Posso usar a Recuperação do Site para migrar VMware VMs para Azure?

Yes. Além de utilizar a Recuperação do Site para configurar a recuperação total de desastres para VMware VMs, também pode utilizar a Recuperação do Site para migrar VMware VMs para Azure no local. Neste cenário, replica vMware VMs no local para Azure Storage. Depois, falhas de entradas no local para o Azure. Após o failover, as suas apps e cargas de trabalho estão disponíveis e em execução em VMs Azure. O processo é como preparar a recuperação total de desastres, exceto que numa migração não se pode fugir do Azure.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>A minha conta Azure precisa de permissões para criar VMs?

Se for um administrador de subscrição, tem as permissões de replicação de que necessita. Se não é administrador, precisa de permissões para tomar estas ações:

- Crie um VM Azure no grupo de recursos e na rede virtual que especifique quando configurar a Recuperação do Site.
- Escreva na conta de armazenamento ou no disco gerido selecionado com base na sua configuração.

[Saiba mais](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) sobre as permissões necessárias.

### <a name="what-applications-can-i-replicate"></a>Que aplicações posso replicar?

Pode replicar qualquer aplicação ou carga de trabalho em execução num VMware VM que satisfaça os [requisitos de replicação](vmware-physical-azure-support-matrix.md#replicated-machines).

- A Recuperação do Site suporta a replicação consciente da aplicação, para que as aplicações possam ser falhadas e falhadas de volta a um estado inteligente.
- A Recuperação do Site integra-se com aplicações da Microsoft como SharePoint, Exchange, Dynamics, SQL Server e Ative Directory. Também trabalha em estreita colaboração com os principais fornecedores, incluindo Oracle, SAP, IBM e Red Hat.

[Saiba mais](site-recovery-workload.md) sobre proteção de carga de trabalho.

### <a name="can-i-use-a-guest-os-server-license-on-azure"></a>Posso usar uma licença de servidor de SO de hóspedes no Azure?

Sim, os clientes da Microsoft Software Assurance podem usar [o Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) para economizar nos custos de licenciamento das máquinas do Windows Server que são migradas para a Azure, ou para usar o Azure para a recuperação de desastres.

## <a name="security"></a>Segurança

### <a name="what-access-to-vmware-servers-does-site-recovery-need"></a>Que acesso aos servidores VMware precisa da Recuperação do Site?

O Site Recovery precisa de ter acesso a servidores VMware para:

- Configurar um VMware VM com o servidor de configuração do site Recovery.
- Descubra automaticamente VMs para replicação.

### <a name="what-access-to-vmware-vms-does-site-recovery-need"></a>Que acesso aos VMware VMs precisa de Recuperação de Sítio?

- Para replicar, um VMware VM deve ter o serviço de Mobilidade de Recuperação do Local instalado e em funcionamento. Pode implantar a ferramenta manualmente ou pode especificar que a Recuperação do Local faça uma instalação de pressão do serviço quando ativar a replicação de um VM.
- Durante a replicação, os VMs comunicam com a Recuperação do Local da seguinte forma:
    - Os VMs comunicam com o servidor de configuração na porta HTTPS 443 para gestão de replicação.
    - Os VMs enviam dados de replicação para o servidor de processo na porta HTTPS 9443. (Esta definição pode ser modificada.)
    - Se ativar a consistência multi-VM, os VM comunicam entre si durante a porta 20004.

### <a name="is-replication-data-sent-to-site-recovery"></a>Os dados de replicação são enviados para a Recuperação do Local?

Não, a Recuperação do Site não interceta dados replicados e não tem qualquer informação sobre o que está a ser feito nos seus VMs. Os dados de replicação são trocados entre hipervisores VMware e Azure Storage. A Recuperação de Sites não tem capacidade para intercetar esses dados. Apenas os metadados necessários para orquestrar a replicação e a ativação pós-falha são enviados para o serviço de Recuperação de Sites.

A Recuperação do Site está certificada para a ISO 27001:2013 e 27018, HIPAA e DPA. Está no processo de avaliações soc2 e fedRAMP JAB.

## <a name="pricing"></a>Preços

### <a name="how-do-i-calculate-approximate-charges-for-vmware-disaster-recovery"></a>Como calculo os encargos aproximados para a recuperação de desastres da VMware?

Utilize a [calculadora de preços](https://aka.ms/asr_pricing_calculator) para estimar os custos durante a utilização da Recuperação do Local.

Para uma estimativa detalhada dos custos, executar a ferramenta de planejador de implementação para [VMware](./site-recovery-deployment-planner.md) e utilizar o [relatório de estimativa de custos](./site-recovery-vmware-deployment-planner-cost-estimation.md).

### <a name="is-there-any-difference-in-cost-between-replicating-to-storage-or-directly-to-managed-disks"></a>Existe alguma diferença de custo entre a replicação para o armazenamento ou diretamente para discos geridos?

Os discos geridos são cobrados de forma ligeiramente diferente das contas de armazenamento. [Saiba mais](https://azure.microsoft.com/pricing/details/managed-disks/) sobre preços de discos geridos.

### <a name="is-there-any-difference-in-cost-when-replicating-to-general-purpose-v2-storage-account"></a>Existe alguma diferença de custos ao replicar-se na conta de armazenamento V2 de Finalidade Geral?

Normalmente, verá um aumento do custo das transações incorridas nas contas de armazenamento do GPv2, uma vez que a Recuperação do Site Azure é pesada. [Leia mais](../storage/common/storage-account-upgrade.md#pricing-and-billing) para estimar a mudança.

## <a name="mobility-service"></a>Serviço de Mobilidade

### <a name="where-can-i-find-the-mobility-service-installers"></a>Onde posso encontrar os instaladores do serviço de mobilidade?

Os instaladores encontram-se na pasta %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository no servidor de configuração.

## <a name="how-do-i-install-the-mobility-service"></a>Como instalo o serviço de Mobilidade?

Em cada VM que pretende replicar, instale o serviço por um de vários métodos:

- [Instalação push](vmware-physical-mobility-service-overview.md#push-installation)
- [Instalação manual](vmware-physical-mobility-service-overview.md#install-the-mobility-service-using-ui) da UI ou PowerShell
- Implementação utilizando uma ferramenta de implementação como [o Gestor de Configurações](vmware-azure-mobility-install-configuration-mgr.md)

## <a name="managed-disks"></a>Managed disks

### <a name="where-does-site-recovery-replicate-data-to"></a>Para onde é que a Recuperação do Sítio replica os dados?

A Recuperação do Site replica VMware VMs e servidores físicos para discos geridos em Azure.

- O servidor de processo de recuperação do site escreve registos de replicação numa conta de armazenamento de cache na região alvo.
- Estes registos são utilizados para criar pontos de recuperação em discos geridos pelo Azure que têm prefixo de **asrseeddisk**.
- Quando ocorre uma falha, o ponto de recuperação selecionado é utilizado para criar um novo disco gerido pelo alvo. Este disco gerido é ligado ao VM em Azure.
- Os VM que foram anteriormente replicados numa conta de armazenamento (antes de março de 2019) não são afetados.

### <a name="can-i-replicate-new-machines-to-storage-accounts"></a>Posso replicar novas máquinas nas contas de armazenamento?

Não. A partir de março de 2019, no portal Azure, só é possível replicar os discos geridos pelo Azure.

A replicação de novos VMs numa conta de armazenamento só está disponível utilizando o PowerShell[(az.RecoveryServices module version 1.4.5](https://www.powershellgallery.com/packages/Az.RecoveryServices/1.4.5)) ou a API REST (versão 2018-01-10 ou 2016-08-10). [Aprenda](./vmware-azure-disaster-recovery-powershell.md) a configurar a replicação utilizando os comandos PowerShell.

### <a name="what-are-the-benefits-of-replicating-to-managed-disks"></a>Quais são os benefícios de replicar discos geridos?

[Saiba como](https://azure.microsoft.com/blog/simplify-disaster-recovery-with-managed-disks-for-vmware-and-physical-servers/) A Recuperação do Site simplifica a recuperação de desastres com discos geridos.

### <a name="can-i-change-the-managed-disk-type-after-a-machine-is-protected"></a>Posso alterar o tipo de disco gerido depois de uma máquina estar protegida?

Sim, pode [facilmente alterar o tipo de disco gerido](../virtual-machines/windows/convert-disk-storage.md) para replicações em curso. Antes de alterar o tipo, certifique-se de que não é gerado nenhum URL de assinatura de acesso partilhado no disco gerido:

1. Vá ao recurso **De Disco Gerido** no portal Azure e verifique se tem um banner URL de assinatura de acesso partilhado na lâmina do **visão geral.**
1. Se o banner estiver presente, selecione-o para cancelar a exportação em curso.
1. Mude o tipo de disco nos próximos minutos. Se alterar o tipo de disco gerido, aguarde que novos pontos de recuperação sejam gerados pela Recuperação do Site Azure.
1. Utilize os novos pontos de recuperação para qualquer falha de teste ou falha no futuro.

### <a name="can-i-switch-replication-from-managed-disks-to-unmanaged-disks"></a>Posso mudar a replicação de discos geridos para discos não geridos?

Não. Mudar de gerido para não gerido não é suportado.

## <a name="replication"></a>Replicação

### <a name="what-are-the-replicated-vm-requirements"></a>Quais são os requisitos de VM replicados?

[Saiba mais](vmware-physical-azure-support-matrix.md#replicated-machines) sobre os requisitos de suporte para VMware VMs e servidores físicos.

### <a name="how-often-can-i-replicate-to-azure"></a>Com que frequência posso replicar-me ao Azure?

A replicação é contínua ao replicar VMware VMs para Azure.

### <a name="can-i-extend-replication"></a>Posso estender a replicação?

Não é suportada a replicação expandida ou em cadeia. Solicite esta funcionalidade no [fórum de comentários.](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959)

### <a name="can-i-do-an-offline-initial-replication"></a>Posso fazer uma replicação inicial offline?

A replicação offline não é suportada. Solicite esta funcionalidade no [fórum de comentários.](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from)

### <a name="what-is-asrseeddisk"></a>O que é assardisco?

Para cada disco de origem, os dados são replicados num disco gerido em Azure. Este disco tem o prefixo de **asrseeddisk**. Armazena a cópia do disco de origem e todas as imagens do ponto de recuperação.

### <a name="can-i-exclude-disks-from-replication"></a>Posso excluir discos da replicação?

Sim, pode excluir discos.

### <a name="can-i-replicate-vms-that-have-dynamic-disks"></a>Posso replicar VMs que têm discos dinâmicos?

Os discos dinâmicos podem ser replicados. O disco do sistema operativo deve ser um disco básico.

### <a name="if-i-use-replication-groups-for-multi-vm-consistency-can-i-add-a-new-vm-to-an-existing-replication-group"></a>Se eu usar grupos de replicação para consistência multi-VM, posso adicionar um novo VM a um grupo de replicação existente?

Sim, pode adicionar novos VMs a um grupo de replicação existente quando ativar a replicação para eles. No entanto:

- Não é possível adicionar um VM a um grupo de replicação existente após a replicação ter começado.
- Não se pode criar um grupo de replicação para os VM existentes.

### <a name="can-i-modify-vms-that-are-replicating-by-adding-or-resizing-disks"></a>Posso modificar VMs que estão a replicar adicionando ou redimensionando discos?

Para a replicação de VMware para Azure, pode modificar o tamanho do disco de VMs de origem. Se quiser adicionar discos novos, deve adicionar o disco e a proteção reenerável para o VM.

### <a name="can-i-migrate-on-premises-machines-to-a-new-vcenter-server-without-impacting-ongoing-replication"></a>Posso migrar máquinas no local para um novo servidor vCenter sem afetar a replicação em curso?

Consulte a nossa [orientação](vmware-azure-manage-vcenter.md#migrate-all-vms-to-a-new-server) para migrar máquinas para um novo vCenter

### <a name="can-i-replicate-to-a-cache-or-target-storage-account-that-has-a-virtual-network-with-azure-firewalls-configured-on-it"></a>Posso replicar-me numa conta de armazenamento de cache ou alvo que tenha uma rede virtual (com firewalls Azure) configurada?

Não, a Recuperação do Site não suporta a replicação do Azure Storage em redes virtuais.

### <a name="what-is-the-frequency-of-generation-of-crash-consistent-recovery-points"></a>Qual é a frequência da geração de pontos de recuperação consistentes?

A recuperação do site gera pontos de recuperação consistentes a cada 5 minutos.

## <a name="component-upgrade"></a>Atualização de componentes

### <a name="my-version-of-the-mobility-services-agent-or-configuration-server-is-old-and-my-upgrade-failed-what-do-i-do"></a>A minha versão do agente de serviços de mobilidade ou servidor de configuração é antiga, e a minha atualização falhou. O que posso fazer?

A Recuperação do Site segue o modelo de suporte N-4. [Saiba mais](./service-updates-how-to.md#support-statement-for-azure-site-recovery) sobre como fazer upgrade a partir de versões muito antigas.

### <a name="where-can-i-find-the-release-notes-and-update-rollups-for-azure-site-recovery"></a>Onde posso encontrar as notas de lançamento e atualizações para a Recuperação do Site Azure?

[Conheça as novas atualizações](site-recovery-whats-new.md)e [obtenha informações sobre rollup](service-updates-how-to.md).

### <a name="where-can-i-find-upgrade-information-for-disaster-recovery-to-azure"></a>Onde posso encontrar informações de atualização para a recuperação de desastres para Azure?

[Saiba mais sobre o upgrade.](./service-updates-how-to.md#vmware-vmphysical-server-disaster-recovery-to-azure)

## <a name="do-i-need-to-reboot-source-machines-for-each-upgrade"></a>Preciso de reiniciar as máquinas de origem para cada atualização?

Recomenda-se um reboot, mas não obrigatório para cada atualização. [Saiba mais](./service-updates-how-to.md#reboot-after-mobility-service-upgrade).

## <a name="configuration-server"></a>Servidor de configuração

### <a name="what-does-the-configuration-server-do"></a>O que faz o servidor de configuração?

O servidor de configuração executa os componentes de recuperação do local, incluindo:

- O servidor de configuração em si. O servidor coordena as comunicações entre os componentes no local e o Azure, e gere a replicação de dados.
- O servidor de processo, que funciona como um portal de replicação. Este servidor:
    1. Recebe os dados de replicação.
    2. Otimiza os dados com caching, compressão e encriptação.
    3. Envia os dados para o Azure Storage.
  O servidor de processo também faz uma instalação push do Serviço de Mobilidade em VMs e executa a descoberta automática de VMware VMware no local.
- O servidor principal alvo, que trata os dados de replicação durante a falha do Azure.

[Saiba mais](vmware-azure-architecture.md) sobre os componentes e processos do servidor de configuração.

### <a name="where-do-i-set-up-the-configuration-server"></a>Onde configurar o servidor de configuração?

Precisa de um VMware VMware VM, único, altamente disponível, para o servidor de configuração. Para a recuperação de desastres físicos do servidor, instale o servidor de configuração numa máquina física.

### <a name="what-do-i-need-for-the-configuration-server"></a>O que preciso para o servidor de configuração?

Reveja os [pré-requisitos.](vmware-azure-deploy-configuration-server.md#prerequisites)

### <a name="can-i-manually-set-up-the-configuration-server-instead-of-using-a-template"></a>Posso configurar manualmente o servidor de configuração em vez de usar um modelo?

Recomendamos que [crie o VM do servidor de configuração](vmware-azure-deploy-configuration-server.md) utilizando a versão mais recente do modelo Open Virtualization Format (OVF). Se não conseguir utilizar o modelo (por exemplo, se não tiver acesso ao servidor VMware), [descarregue](physical-azure-set-up-source.md) o ficheiro de configuração a partir do portal e confiem do servidor de configuração.

### <a name="can-a-configuration-server-replicate-to-more-than-one-region"></a>Um servidor de configuração pode replicar-se em mais de uma região?

Não. Para se replicar em mais de uma região, precisa de um servidor de configuração em cada região.

### <a name="can-i-host-a-configuration-server-in-azure"></a>Posso hospedar um servidor de configuração em Azure?

Embora seja possível, o Azure VM que executa o servidor de configuração precisaria de comunicar com a infraestrutura vMware e VMs no local. Esta comunicação adiciona latência e impacta a replicação contínua.

### <a name="how-do-i-update-the-configuration-server"></a>Como actualro o servidor de configuração?

[Saiba](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) como atualizar o servidor de configuração.

- Pode encontrar as últimas informações de atualização na página de atualizações do [Azure](https://azure.microsoft.com/updates/?product=site-recovery).
- Pode descarregar a versão mais recente do portal. Ou, pode descarregar a versão mais recente do servidor de configuração diretamente do [Microsoft Download Center](https://aka.ms/asrconfigurationserver).
- Se a sua versão for mais de quatro versões mais antigas do que a versão atual, consulte a [declaração de suporte](./service-updates-how-to.md#support-statement-for-azure-site-recovery) para obter orientação de atualização.

### <a name="should-i-back-up-the-configuration-server"></a>Devo fazer o reforço do servidor de configuração?

Recomendamos a utilização de cópias de segurança regulares do servidor de configuração.

- Para um failback bem sucedido, o VM que está a ser falhado deve existir na base de dados do servidor de configuração.
- O servidor de configuração deve estar a funcionar e num estado ligado.
- [Saiba mais](vmware-azure-manage-configuration-server.md) sobre tarefas comuns de gestão de servidores de configuração.

### <a name="when-im-setting-up-the-configuration-server-can-i-download-and-install-mysql-manually"></a>Quando estou a configurar o servidor de configuração, posso descarregar e instalar o MySQL manualmente?

Yes. Descarregue o MySQL e coloque-o na pasta C:\Temp\ASRSetup. Em seguida, instale-o manualmente. Quando configurar o VM do servidor de configuração e aceitar os termos, o MySQL será listado como **já instalado** no Download **e instalado**.

### <a name="can-i-avoid-downloading-mysql-but-let-site-recovery-install-it"></a>Posso evitar descarregar o MySQL mas deixar a Recuperação do Site instalá-lo?

Yes. Descarregue o instalador MySQL e coloque-o na pasta C:\Temp\ASRSetup. Quando configurar o VM do servidor de configuração, aceite os termos e selecione **Descarregamento e instalação.** O portal utilizará o instalador que adicionou para instalar o MySQL.

### <a name="can-i-use-the-configuration-server-vm-for-anything-else"></a>Posso usar o VM do servidor de configuração para qualquer outra coisa?

Não. Utilize o VM apenas para o servidor de configuração.

### <a name="can-i-clone-a-configuration-server-and-use-it-for-orchestration"></a>Posso clonar um servidor de configuração e usá-lo para orquestração?

Não. Crie um novo servidor de configuração para evitar problemas de registo.

### <a name="can-i-change-the-vault-in-which-the-configuration-server-is-registered"></a>Posso alterar o cofre no qual o servidor de configuração está registado?

Não. Depois de um cofre estar associado ao servidor de configuração, não pode ser alterado. [Saiba](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault) como registar um servidor de configuração com um cofre diferente.

### <a name="can-i-use-the-same-configuration-server-for-disaster-recovery-of-both-vmware-vms-and-physical-servers"></a>Posso usar o mesmo servidor de configuração para a recuperação de desastres tanto de VMware vMs como de servidores físicos?

Sim, mas note que a máquina física só pode ser falhada para um VMware VM.

### <a name="where-can-i-download-the-passphrase-for-the-configuration-server"></a>Onde posso descarregar a palavra-passe para o servidor de configuração?

[Saiba](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase) como baixar a palavra-passe.

### <a name="where-can-i-download-vault-registration-keys"></a>Onde posso baixar as chaves de registo do cofre?

No cofre dos Serviços de Recuperação, selecione **Servidores de Configuração** na **Gestão da Infraestrutura de Recuperação do Local**  >  **Manage**. Em seguida, em **Servers,** **selecione Baixar** a chave de registo para descarregar o ficheiro de credenciais do cofre.

### <a name="can-a-single-configuration-server-be-used-to-protect-multiple-vcenter-instances"></a>Um único servidor de configuração pode ser usado para proteger várias instâncias do vCenter?

Sim, um único servidor de configuração pode proteger VMs em vários vCenters.  Não há limite para quantas instâncias vCenter podem ser adicionadas ao servidor de configuração, no entanto os limites para quantos VMs um único servidor de configuração pode proteger aplicam-se.

### <a name="can-a-single-configuration-server-protect-multiple-clusters-within-vcenter"></a>Um único servidor de configuração pode proteger vários clusters dentro do vCenter?

Sim, a Recuperação do Sítio Azure pode proteger VMs em diferentes clusters.

## <a name="process-server"></a>Servidor de processo

### <a name="why-am-i-unable-to-select-the-process-server-when-i-enable-replication"></a>Por que não consigo selecionar o servidor de processo quando posso ativar a replicação?

As atualizações nas versões 9.24 e, mais tarde, exibem agora a [saúde do servidor de processos quando ativa a replicação](vmware-azure-enable-replication.md#enable-replication). Esta funcionalidade ajuda a evitar o estrangulamento do servidor de processos e a minimizar o uso de servidores de processos pouco saudáveis.

### <a name="how-do-i-update-the-process-server-to-version-924-or-later-for-accurate-health-information"></a>Como actualro o servidor de processo para a versão 9.24 ou posterior para obter informações precisas sobre saúde?

Começando pela [versão 9.24,](service-updates-how-to.md#links-to-currently-supported-update-rollups)foram adicionados mais alertas para indicar a saúde do servidor de processos. [Atualize os componentes de Recuperação do Site para a versão 9.24 ou mais tarde](service-updates-how-to.md#links-to-currently-supported-update-rollups) para que todos os alertas sejam gerados.

### <a name="how-can-i-ensure-high-availability-of-the-process-server"></a>Como posso garantir uma elevada disponibilidade do servidor de processos?

Ao configurar mais do que um servidor de processo, o design proporciona flexibilidade para mover máquinas protegidas de um servidor de processo pouco saudável para o servidor de processo de funcionamento. O movimento de uma máquina de um servidor de processo para outro deve ser iniciado explicitamente/manualmente através dos passos definidos aqui: [mover VMs entre servidores de processo .](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load)

## <a name="failover-and-failback"></a>Ativação pós-falha e reativação pós-falha

### <a name="can-i-use-the-on-premises-process-server-for-failback"></a>Posso usar o servidor de processo no local para falhar?

Recomendamos vivamente a criação de um servidor de processo em Azure para fins de falha, para evitar atrasos de transferência de dados. Além disso, caso tenha separado a rede VMs de origem com a rede de face Azure no servidor de configuração, é essencial utilizar o servidor de processo criado em Azure para falha.

### <a name="can-i-keep-the-ip-address-on-failover"></a>Posso manter o endereço IP em falha?

Sim, pode manter o endereço IP em falha. Certifique-se de que especifica o endereço IP alvo nas definições **de Computação e Rede** para o VM antes do failover. Além disso, desligue as máquinas no momento do failover para evitar conflitos de endereço IP durante o failback.

### <a name="can-i-change-the-target-vm-size-or-vm-type-before-failover"></a>Posso alterar o tamanho VM ou vm alvo antes de falhar?

Sim, pode alterar o tipo ou tamanho do VM a qualquer momento antes de falhar. No portal, utilize as definições **de Computação e Rede** para o VM replicado.

### <a name="how-far-back-can-i-recover"></a>Até onde posso me recuperar?

Para vMware a Azure, o ponto de recuperação mais antigo que pode usar é de 72 horas.

### <a name="how-do-i-access-azure-vms-after-failover"></a>Como acedo aos VMs Azure após o fracasso?

Após falha, pode aceder a VMs Azure através de uma ligação de internet segura, através de uma VPN site-to-site ou através do Azure ExpressRoute. Para se conectar, tem que preparar várias coisas. [Saiba mais](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).

### <a name="is-failed-over-data-resilient"></a>Os dados falhados são resistentes?

O Azure foi concebido para ser resiliente. A Recuperação do Sítio é concebida para o failover para um centro de dados secundário do Azure, conforme exigido pelo acordo de nível de serviço Azure (SLA). Quando ocorrer uma falha, certificamo-nos de que os seus metadados e cofres permanecem na mesma região geográfica que escolheu para o seu cofre.

### <a name="is-failover-automatic"></a>A ativação pós-falha é automática?

[Falhar](site-recovery-failover.md) não é automático. Inicia-se uma falha fazendo uma única seleção no portal, ou pode utilizar o [PowerShell](/powershell/module/az.recoveryservices) para desencadear uma falha.

### <a name="can-i-fail-back-to-a-different-location"></a>Posso voltar a um local diferente?

Yes. Se falhaste no Azure, podes falhar num local diferente se o original não estiver disponível. [Saiba mais](concepts-types-of-failback.md#alternate-location-recovery-alr).

### <a name="why-do-i-need-a-vpn-or-expressroute-with-private-peering-to-fail-back"></a>Por que preciso de uma VPN ou ExpressRoute com um olhar privado para falhar?

Quando recua do Azure, os dados do Azure são copiados de volta para o seu VM no local, e o acesso privado é necessário.


## <a name="automation-and-scripting"></a>Automação e scripting

### <a name="can-i-set-up-replication-with-scripting"></a>Posso configurar a replicação com o scripting?

Yes. Pode automatizar fluxos de trabalho de Recuperação de Sítio utilizando a API de Repouso, a PowerShell ou a Azure SDK. [Saiba mais](vmware-azure-disaster-recovery-powershell.md).

## <a name="performance-and-capacity"></a>Desempenho e capacidade

### <a name="can-i-throttle-replication-bandwidth"></a>Posso acelerar a largura de banda da replicação?

Yes. [Saiba mais](site-recovery-plan-capacity-vmware.md).

## <a name="next-steps"></a>Próximos passos

- [Rever os](vmware-physical-azure-support-matrix.md) requisitos de apoio.
- [Configurar](vmware-azure-tutorial.md) VMware para replicação Azure.
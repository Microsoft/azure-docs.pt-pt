---
title: Matriz de suporte da cópia de segurança de uma VM do Azure
description: Fornece um resumo das definições e limitações de suporte ao fazer o backup dos VMs Azure com o serviço Azure Backup.
ms.topic: conceptual
ms.date: 09/13/2019
ms.custom: references_regions
ms.openlocfilehash: 3d1238bffd785a47b6e0ffab70366a15dfff0ef3
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2021
ms.locfileid: "99218116"
---
# <a name="support-matrix-for-azure-vm-backup"></a>Matriz de suporte da cópia de segurança de uma VM do Azure

Pode utilizar o [serviço de backup Azure](backup-overview.md) para fazer backup em máquinas e cargas de trabalho no local e máquinas virtuais Azure (VMs). Este artigo resume as definições e limitações de suporte quando faz backup de VMs Azure com Azure Backup.

Outras matrizes de suporte:

- [Matriz de suporte geral](backup-support-matrix.md) para backup Azure
- [Matriz de suporte](backup-support-matrix-mabs-dpm.md) para backup Azure servidor / Gestor de Proteção de Dados do Centro de Sistema (DPM)
- [Matriz de suporte](backup-support-matrix-mars-agent.md) para cópia de segurança com o agente Microsoft Azure Recovery Services (MARS)

## <a name="supported-scenarios"></a>Cenários suportados

Eis como pode recuar e restaurar os VMs Azure com o serviço Azure Backup.

**Cenário** | **Cópia de segurança** | **Agente** |**Restaurar**
--- | --- | --- | ---
Backup direto dos VMs Azure  | Apoie todo o VM.  | Não é necessário nenhum agente adicional no Azure VM. O Azure Backup instala e utiliza uma extensão ao [agente Azure VM](../virtual-machines/extensions/agent-windows.md) que está em execução no VM. | Restaurar da seguinte forma:<br/><br/> - **Criar um VM básico.** Isto é útil se o VM não tiver configuração especial, como vários endereços IP.<br/><br/> - **Restaurar o disco VM**. Restaurar o disco. Em seguida, prenda-o a um VM existente, ou crie um novo VM a partir do disco utilizando o PowerShell.<br/><br/> - **Substitua o disco VM**. Se existir um VM e utilizar discos geridos (não encriptados), pode restaurar um disco e usá-lo para substituir um disco existente no VM.<br/><br/> - **Restaurar ficheiros/pastas específicos**. Pode restaurar ficheiros/pastas a partir de um VM em vez de de todo o VM.
Backup direto dos VMs Azure (apenas Windows)  | Fazer o back up ficheiros/pastas/volume específicos. | Instale o [agente dos Serviços de Recuperação Azure](backup-azure-file-folder-backup-faq.md).<br/><br/> Pode executar o agente MARS ao lado da extensão de reserva para o agente Azure VM fazer cópia de segurança do VM ao nível de ficheiro/pasta. | Restaurar pastas/ficheiros específicos.
Faça backup Azure VM para servidor de backup  | Fazer a ressalto de ficheiros/pastas/volumes; sistema estado/ficheiros metálicos nus; dados da aplicação para System Center DPM ou para Microsoft Azure Backup Server (MABS).<br/><br/> DPM/MABS, em seguida, recua para o cofre de reserva. | Instale o agente de proteção DPM/MABS no VM. O agente MARS está instalado no DPM/MABS.| Restaurar ficheiros/pastas/volumes; sistema estado/ficheiros metálicos nus; dados de aplicativos.

Saiba mais sobre a cópia de segurança [utilizando um servidor de backup](backup-architecture.md#architecture-back-up-to-dpmmabs) e sobre os [requisitos de suporte](backup-support-matrix-mabs-dpm.md).

## <a name="supported-backup-actions"></a>Ações de backup apoiadas

**Ação** | **Suporte**
--- | ---
Suporte um VM que é desligado/offline VM | Suportado.<br/><br/> O snapshot é apenas consistente com falhas, não consistente com aplicações.
Faça o back up discos após migrar para discos geridos | Suportado.<br/><br/> O reforço vai continuar a trabalhar. nenhuma ação necessária.
Faça o back up discos geridos após permitir o bloqueio do grupo de recursos | Não suportado.<br/><br/> O Azure Backup não consegue eliminar os pontos de restauro mais antigos e as cópias de segurança começarão a falhar quando o limite máximo dos pontos de restauro for atingido.
Modificar a política de backup para um VM | Suportado.<br/><br/> O VM será apoiado utilizando as definições de programação e retenção em nova política. Se as definições de retenção forem estendidas, os pontos de recuperação existentes são marcados e mantidos. Se forem reduzidos, os pontos de recuperação existentes serão podados no próximo trabalho de limpeza e eventualmente eliminados.
Cancele um trabalho de reserva| Suportado durante o processo de instantâneo.<br/><br/> Não suportado quando a foto está sendo transferida para o cofre.
Apoiar o VM numa região ou subscrição diferente |Não suportado.<br><br>Para fazer uma cópia de segurança com sucesso, as máquinas virtuais devem estar na mesma subscrição que o cofre para cópia de segurança.
Backups por dia (através da extensão Azure VM) | Um reforço programado por dia.<br/><br/>O serviço Azure Backup suporta até nove backups a pedido por dia, mas a Microsoft recomenda não mais do que quatro backups diários a pedido para garantir o melhor desempenho.
Backups por dia (através do agente MARS) | Três reforços programados por dia.
Backups por dia (via DPM/MABS) | Dois reforços programados por dia.
Backup mensal/anual| Não suportado quando se apoia com a extensão Azure VM. Apenas diariamente e semanalmente é suportado.<br/><br/> Pode definir a política para reter backups diários/semanais para o período de retenção mensal/anual.
Regulação automática do relógio | Não suportado.<br/><br/> O Azure Backup não se ajusta automaticamente para alterações de horário de verão ao fazer o backup de um VM.<br/><br/>  Modifique a apólice manualmente, se necessário.
[Funcionalidades de segurança para backup híbrido](./backup-azure-security-feature.md) |Não é suportado por desativação de funcionalidades de segurança.
Apoiar o VM cujo tempo de máquina foi alterado | Não suportado.<br/><br/> Se o tempo da máquina for alterado para uma data futura depois de permitir a cópia de segurança para esse VM, no entanto, mesmo que a mudança de tempo seja revertida, a cópia de segurança bem sucedida não é garantida.

## <a name="operating-system-support-windows"></a>Suporte ao sistema operativo (Windows)

A tabela seguinte resume os sistemas operativos suportados ao fazer o backup dos VMs windows Azure.

**Cenário** | **Suporte ao OS**
--- | ---
Apoiar com a extensão do agente Azure VM | - Cliente Do Windows 10 (apenas 64 bits) <br/><br/>- Windows Server 2019 (Datacenter/Datacenter Core/Standard) <br/><br/> - Windows Server 2016 (Datacenter/Datacenter Core/Standard) <br/><br/> - Windows Server 2012 R2 (Datacenter/Standard) <br/><br/> - Windows Server 2012 (Datacenter/Standard) <br/><br/> - Windows Server 2008 R2 (RTM e SP1 Standard)  <br/><br/> - Windows Server 2008 (apenas 64 bits)
Voltar para o agente MARS | [Sistemas](backup-support-matrix-mars-agent.md#supported-operating-systems) operativos suportados.
Voltar para cima com dPM/MABS | Sistemas operativos suportados para cópias de segurança com [MABS](backup-mabs-protection-matrix.md) e [DPM](/system-center/dpm/dpm-protection-matrix).

O Azure Backup não suporta sistemas operativos de 32 bits.

## <a name="support-for-linux-backup"></a>Suporte para backup Linux

Aqui está o que é suportado se quiser apoiar as máquinas Linux.

**Ação** | **Suporte**
--- | ---
Apoie os VMs Linux Azure com o agente Linux Azure VM | Arquivar cópias de segurança consistentes.<br/><br/> Cópia de segurança consistente com aplicações utilizando [scripts personalizados.](backup-azure-linux-app-consistent.md)<br/><br/> Durante a restauração, pode criar um novo VM, restaurar um disco e usá-lo para criar um VM, ou restaurar um disco e usá-lo para substituir um disco num VM existente. Também pode restaurar ficheiros e pastas individuais.
Apoiar os VMs linux Azure com agente MARS | Não suportado.<br/><br/> O agente MARS só pode ser instalado em máquinas Windows.
Apoiar os VMs Linux Azure com DPM/MABS | Não suportado.

## <a name="operating-system-support-linux"></a>Suporte ao sistema operativo (Linux)

Para backups Azure VM Linux, a Azure Backup suporta a lista de distribuições Linux [endossadas pela Azure](../virtual-machines/linux/endorsed-distros.md). Tenha em atenção o seguinte:

- O Azure Backup não suporta o Core OS Linux.
- O Azure Backup não suporta sistemas operativos de 32 bits.
- Outras distribuições linux próprias podem funcionar desde que o [agente Azure VM para Linux](../virtual-machines/extensions/agent-linux.md) esteja disponível no VM, e enquanto python for suportado.
- O Azure Backup não suporta um Linux VM configurado por procuração se não tiver a versão Python 2.7 instalada.
- O Azure Backup não suporta o backup de ficheiros NFS que são montados a partir do armazenamento, ou de qualquer outro servidor NFS, para máquinas Linux ou Windows. Só faz a parte de trás dos discos que estão ligados localmente ao VM.

## <a name="backup-frequency-and-retention"></a>Frequência e retenção de backup

**Definição** | **Limites**
--- | ---
Pontos de recuperação máximos por instância protegida (máquina/carga de trabalho) | 9999.
Prazo máximo de validade para um ponto de recuperação | Sem limite (99 anos).
Frequência máxima de backup para o cofre (extensão Azure VM) | Uma vez por dia.
Frequência máxima de backup para cofre (agente MARS) | Três reforços por dia.
Frequência máxima de backup para DPM/MABS | A cada 15 minutos para o SQL Server.<br/><br/> Uma vez por hora para outras cargas de trabalho.
Retenção de pontos de recuperação | Diariamente, semanalmente, mensal e anualmente.
Período de retenção máximo | Depende da frequência de reserva.
Pontos de recuperação no disco DPM/MABS | 64 para servidores de ficheiros e 448 para servidores de aplicações.<br/><br/> Os pontos de recuperação de fita são ilimitados para DPM no local.

## <a name="supported-restore-methods"></a>Métodos de restauro apoiados

**Opção de restauro** | **Detalhes**
--- | ---
**Criar uma nova VM** | Cria e coloca uma VM em execução rapidamente a partir de um ponto de restauro.<br/><br/> Pode especificar um nome para o VM, selecionar o grupo de recursos e a rede virtual (VNet) no qual será colocado e especificar uma conta de armazenamento para o VM restaurado. A nova VM tem de ser criada na mesma região que a VM de origem.
**Restaurar disco** | Restaura um disco da VM, que pode ser utilizado para criar uma nova VM.<br/><br/> O Azure Backup fornece um modelo para ajudar a personalizar e criar uma VM. <br/><br> O trabalho de restauro gera um modelo que pode descarregar e usar para especificar as definições de VM personalizadas e criar um VM.<br/><br/> Os discos são copiados para o Grupo de Recursos que especificar.<br/><br/> Em alternativa, pode ligar o disco a um VM existente ou criar um novo VM utilizando o PowerShell.<br/><br/> Esta opção é útil se quiser personalizar a VM, adicionar definições de configuração que esta não tinha quando foi criada a cópia de segurança ou adicionar definições que têm de ser configuradas através do modelo ou do PowerShell.
**Substituir existente** | Pode restaurar um disco e usá-lo para substituir um disco no VM existente.<br/><br/> A VM atual tem de existir. Se tiver sido apagada, esta opção não pode ser utilizada.<br/><br/> O Azure Backup tira uma fotografia do VM existente antes de substituir o disco e armazena-o no local de preparação que especifica. Os discos existentes e ligados à VM são substituídos pelo ponto de restauro selecionado.<br/><br/> O instantâneo é copiado para o cofre, e mantido de acordo com a política de retenção. <br/><br/> Após a operação do disco de substituição, o disco original é mantido no grupo de recursos. Pode optar por eliminar manualmente os discos originais se não forem necessários. <br/><br/>A substituição existente é suportada por VMs geridos não encriptados e por VMs [criados com imagens personalizadas](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/). Não é suportado por discos não geridos e [VMs generalizados.](../virtual-machines/windows/capture-image-resource.md)<br/><br/> Se o ponto de restauro tiver mais ou menos discos do que o VM atual, então o número de discos no ponto de restauração só refletirá a configuração VM.<br><br> A substituição dos atuais também é suportada por VMs por recursos ligados, como identidade gerida atribuída pelo utilizador e [Cofre-Chave](../active-directory/managed-identities-azure-resources/overview.md) . [](../key-vault/general/overview.md)
**Entre regiões (região secundária)** | A restauração da Região Transversal pode ser usada para restaurar os VMs Azure na região secundária, que é uma [região emparelhada Azure.](../best-practices-availability-paired-regions.md#what-are-paired-regions)<br><br> Pode restaurar todos os VMs Azure para o ponto de recuperação selecionado se a cópia de segurança for feita na região secundária.<br><br> Esta funcionalidade está disponível para as seguintes opções:<br> <li> [Criar uma VM](./backup-azure-arm-restore-vms.md#create-a-vm) <br> <li> [Restaurar discos](./backup-azure-arm-restore-vms.md#restore-disks) <br><br> Atualmente, não apoiamos a opção [Substituir discos existentes.](./backup-azure-arm-restore-vms.md#replace-existing-disks)<br><br> Permissões<br> A operação de restauro na região secundária pode ser realizada por administradores de backup e administradores de aplicações.

## <a name="support-for-file-level-restore"></a>Suporte para restauro ao nível do ficheiro

**Restaurar** | **Suportado**
--- | ---
Restaurar ficheiros através de sistemas operativos | Pode restaurar ficheiros em qualquer máquina que tenha o mesmo (ou compatível) SO que o VM de back-up. Consulte a [tabela oss compatível](backup-azure-restore-files-from-vm.md#step-3-os-requirements-to-successfully-run-the-script).
Restaurar ficheiros de VMs encriptados | Não suportado.
Restaurar ficheiros a partir de contas de armazenamento restritas à rede | Não suportado.
Restaurar ficheiros em VMs usando espaços de armazenamento do Windows | Restaurar não suportado no mesmo VM.<br/><br/> Em vez disso, restaure os ficheiros num VM compatível.
Restaurar ficheiros em Linux VM usando matrizes LVM/raid | Restaurar não suportado no mesmo VM.<br/><br/> Restaurar num VM compatível.
Restaurar ficheiros com definições especiais de rede | Restaurar não suportado no mesmo VM. <br/><br/> Restaurar num VM compatível.

## <a name="support-for-vm-management"></a>Apoio à gestão de VM

A tabela seguinte resume o suporte para cópias de segurança durante as tarefas de gestão de VM, tais como a adição ou substituição de discos VM.

**Restaurar** | **Suportado**
--- | ---
Restaurar através da subscrição/região/zona. | Não suportado.
Restaurar a um VM existente | Utilize a opção de substituição do disco.
Restaurar o disco com conta de armazenamento ativada para encriptação do serviço de armazenamento Azure (SSE) | Não suportado.<br/><br/> Restaurar a uma conta que não tenha SSE ativada.
Restaurar para contas de armazenamento misto |Não suportado.<br/><br/> Com base no tipo de conta de armazenamento, todos os discos restaurados serão premium ou standard, e não misturados.
Restaurar vM diretamente para um conjunto de disponibilidade | Para discos geridos, pode restaurar o disco e utilizar a opção definida pela disponibilidade no modelo.<br/><br/> Não suportado por discos não geridos. Para discos não geridos, restaure o disco e, em seguida, crie um VM no conjunto de disponibilidade.
Restaurar o backup de VMs não geridos após a atualização para a gestão de VM| Suportado.<br/><br/> Pode restaurar os discos e, em seguida, criar um VM gerido.
Restaurar vM para restaurar o ponto antes do VM foi migrado para discos geridos | Suportado.<br/><br/> Restaura para discos não geridos (predefinição), converte os discos restaurados para o disco gerido e cria um VM com os discos geridos.
Restaurar um VM que foi apagado. | Suportado.<br/><br/> Pode restaurar o VM a partir de um ponto de recuperação.
Restaurar um controlador de domínio VM  | Suportado. Para mais informações, consulte [O controlador de domínio Restore VMs](backup-azure-arm-restore-vms.md#restore-domain-controller-vms).
Restaurar VM em diferentes redes virtuais |Suportado.<br/><br/> A rede virtual deve estar na mesma subscrição e região.

## <a name="vm-compute-support"></a>Suporte ao cálculo VM

**Computação** | **Suporte**
--- | ---
Tamanho da VM |Qualquer tamanho Azure VM com pelo menos 2 núcleos CPU e RAM de 1 GB.<br/><br/> [Saiba mais.](../virtual-machines/sizes.md)
Apoiar VMs em [conjuntos de disponibilidade](../virtual-machines/availability.md#availability-sets) | Suportado.<br/><br/> Não é possível restaurar um VM num conjunto disponível utilizando a opção de criar rapidamente um VM. Em vez disso, quando restaurar o VM, restaure o disco e use-o para implantar um VM, ou restaurar um disco e usá-lo para substituir um disco existente.
Back up VMs que são implantados com [Benefício de Utilização Híbrida (HUB)](../virtual-machines/windows/hybrid-use-benefit-licensing.md) | Suportado.
Back up VMs que são implantados a partir do [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?filters=virtual-machine-images)<br/><br/> (Publicado pela Microsoft, terceiros) |Suportado.<br/><br/> O VM deve estar a executar um sistema operativo suportado.<br/><br/> Ao recuperar ficheiros sobre o VM, só pode restaurar um SISTEMA compatível (não um SO anterior ou posterior). Não restauramos VMs Azure Marketplace apoiados como VMs, pois estes precisam de informações de compra. Só são restaurados como discos.
Back up VMs que são implantados a partir de uma imagem personalizada (terceiro) |Suportado.<br/><br/> O VM deve estar a executar um sistema operativo suportado.<br/><br/> Ao recuperar ficheiros sobre o VM, só pode restaurar um SISTEMA compatível (não um SO anterior ou posterior).
Back up VMs que são migrados para Azure| Suportado.<br/><br/> Para fazer uma proteção do VM, o agente VM tem de ser instalado na máquina migratória.
Apoiar consistência multi-VM | O Azure Backup não fornece consistência de dados e aplicações em vários VMs.
Backup com [Definições de Diagnóstico](../azure-monitor/platform/platform-logs-overview.md)  | Sem apoio. <br/><br/> Se a restauração do VM Azure com definições de diagnóstico for acionada utilizando a opção [Create New,](backup-azure-arm-restore-vms.md#create-a-vm) então a restauração falha.
Restauro de VMs fixados em zona | Suportado (para um VM que é apoiado depois de janeiro de 2019 e onde estão disponíveis [zonas de disponibilidade).](https://azure.microsoft.com/global-infrastructure/availability-zones/)<br/><br/>Atualmente, apoiamos restaurar a mesma zona que está presa em VMs. No entanto, se a zona não estiver disponível devido a uma paragem, a restauração falhará.
GMs gen2 | Suportado <br> A Azure Backup suporta cópia de segurança e restauro de [VMs da Gen2](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/). Quando estes VMs são restaurados do ponto de recuperação, são restaurados como [VMs gen2](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/).
Backup de VMs Azure com fechaduras | Não suportado para VMs não geridos. <br><br> Suportado para VMs geridos.
[Spot VMs](../virtual-machines/spot-vms.md) | Sem apoio. A Azure Backup restaura os VMs spot como VMs Azure regulares.
[Anfitrião Dedicado Azure](https://docs.microsoft.com/azure/virtual-machines/dedicated-hosts) | Suportado
Configuração de Espaços de Armazenamento do Windows de VMs Azure autónomos | Suportado 

## <a name="vm-storage-support"></a>Suporte de armazenamento VM

**Componente** | **Suporte**
--- | ---
Discos de dados Azure VM | Suporte para cópia de segurança de VMs Azure com até 32 discos.<br><br> O suporte para cópia de segurança de VMs Azure com discos não geridos ou VMs clássicos é apenas de 16 discos.
Tamanho do disco de dados | O tamanho do disco individual pode ser até 32 TB e um máximo de 256 TB combinados para todos os discos num VM.
Tipo de armazenamento | HDD padrão, SSD padrão, SSD premium.
Managed disks | Suportado.
Discos encriptados | Suportado.<br/><br/> Os VMs Azure ativados com encriptação de disco Azure podem ser apoiados (com ou sem a aplicação AD AZure).<br/><br/> Os VMs encriptados não podem ser recuperados ao nível do ficheiro/pasta. Tens de recuperar todo o VM.<br/><br/> Pode ativar a encriptação em VMs que já estão protegidos por Azure Backup.
Discos com acelerador de escrita ativados | A partir de 23 de novembro de 2020, apoiado apenas nas regiões coreana Central (KRC) e África do Sul Norte (SAN) para um número limitado de assinaturas. Para as subscrições suportadas, o Azure Backup irá fazer backup às máquinas virtuais com discos que são Write Accelerated (WA) ativados durante a cópia de segurança.<br><br>Para as regiões não apoiadas, a conectividade da Internet é necessária no VM para tirar fotos de Máquinas Virtuais com WA ativadas.<br><br> **Nota importante**: Nestas regiões não apoiadas, as máquinas virtuais com discos WA necessitam de conectividade com a Internet para uma cópia de segurança bem sucedida (mesmo que esses discos estejam excluídos da cópia de segurança).
Retrocede & Restaurar VMs/discos desduplicados | A Azure Backup não suporta a deduplicação. Para mais informações, consulte este [artigo](./backup-support-matrix.md#disk-deduplication-support) <br/> <br/>  - A Azure Backup não desduplica através de VMs no cofre dos Serviços de Recuperação <br/> <br/>  - Se houver VMs em estado de desduplicação durante a restauração, os ficheiros não podem ser restaurados porque o cofre não entende o formato. No entanto, pode executar com sucesso a restauração completa do VM.
Adicione o disco ao VM protegido | Suportado.
Redimensione o disco em VM protegido | Suportado.
Armazenamento compartilhado| O backup de VMs utilizando o Cluster Shared Volume (CSV) ou Scale-Out File Server não é suportado. É provável que os escritores do CSV falhem durante o backup. No restauro, os discos que contêm volumes de CSV podem não aparecer.
[Discos compartilhados](../virtual-machines/disks-shared-enable.md) | Não suportado.
Discos Ultra SSD | Não suportado. Para mais detalhes, consulte estas [limitações.](selective-disk-backup-restore.md#limitations)
[Discos temporários](https://docs.microsoft.com/azure/virtual-machines/managed-disks-overview#temporary-disk) | Os discos temporários não são apoiados pela Azure Backup.

## <a name="vm-network-support"></a>Suporte à rede VM

**Componente** | **Suporte**
--- | ---
Número de interfaces de rede (NICs) | Até ao máximo o número de NICs suportados para um tamanho Azure VM específico.<br/><br/> Os NICs são criados quando o VM é criado durante o processo de restauração.<br/><br/> O número de NICs no VM restaurado espelha o número de NICs no VM quando ativa a proteção. Remover OS NICs depois de ativar a proteção não afeta a contagem.
Balanceador de carga externo/interno |Suportado. <br/><br/> [Saiba mais](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) sobre a restauração de VMs com configurações especiais de rede.
Vários endereços IP reservados |Suportado. <br/><br/> [Saiba mais](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) sobre a restauração de VMs com configurações especiais de rede.
VMs com vários adaptadores de rede| Suportado. <br/><br/> [Saiba mais](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) sobre a restauração de VMs com configurações especiais de rede.
VMs com endereços IP públicos| Suportado.<br/><br/> Associe um endereço IP público existente com o NIC, ou crie um endereço e associe-o ao NIC após a restauração.
Grupo de segurança de rede (NSG) em NIC/sub-rede. |Suportado.
Endereço IP estático | Não suportado.<br/><br/> Um novo VM que é criado a partir de um ponto de restauro é atribuído um endereço IP dinâmico.<br/><br/> Para VMs clássicos, não é possível fazer uma reserva de VM com um endereço IP reservado e sem ponto final definido.
Endereço IP dinâmico |Suportado.<br/><br/> Se o NIC na fonte VM utilizar um endereço IP dinâmico, por predefinição o NIC no VM restaurado também o utilizará.
Gestor de Tráfego do Azure| Suportado.<br/><br/>Se o VM de apoio estiver no Traffic Manager, adicione manualmente o VM restaurado à mesma instância do Gestor de Tráfego.
DNS do Azure |Suportado.
DNS Personalizado |Suportado.
Conectividade de saída via http proxy | Suportado.<br/><br/> Um representante autenticado não é apoiado.
Pontos finais de serviço de rede virtual| Suportado.<br/><br/> As definições de firewall e de armazenamento de rede virtuais devem permitir o acesso de todas as redes.

## <a name="vm-security-and-encryption-support"></a>Suporte de segurança e encriptação vM

O Azure Backup suporta encriptação para dados em trânsito e em repouso:

Tráfego de rede para Azure:

- O tráfego de backup dos servidores para o cofre dos Serviços de Recuperação é encriptado utilizando o Padrão de Encriptação Avançada 256.
- Os dados de cópia de segurança são enviados por um link HTTPS seguro.
- Os dados de cópia de segurança são armazenados no cofre dos Serviços de Recuperação sob forma encriptada.
- Só você tem a chave de encriptação para desbloquear estes dados. A Microsoft não pode desencriptar os dados de backup em nenhum momento.

  > [!WARNING]
  > Depois de configurar o cofre, só tem acesso à chave de encriptação. A Microsoft nunca mantém uma cópia e não tem acesso à chave. Se a chave estiver extraviada, a Microsoft não poderá recuperar os dados de backup.

Segurança de dados:

- Ao fazer o backup dos VMs Azure, é necessário configurar a encriptação *dentro* da máquina virtual.
- O Azure Backup suporta a Encriptação do Disco Azure, que utiliza o BitLocker em máquinas virtuais do Windows e **nós, a dm-crypt** em máquinas virtuais Linux.
- No back-end, o Azure Backup utiliza a [encriptação do Serviço de Armazenamento do Azure](../storage/common/storage-service-encryption.md), que protege os dados inativos.

**Máquina** | **Em trânsito** | **Em repouso**
--- | --- | ---
Máquinas Windows no local sem DPM/MABS | ![Yes][green] | ![Yes][green]
VMs do Azure | ![Yes][green] | ![Yes][green]
Instalações/VMs Azure com DPM | ![Yes][green] | ![Yes][green]
Em instalações/VMs Azure com MABS | ![Yes][green] | ![Yes][green]

## <a name="vm-compression-support"></a>Suporte à compressão VM

A cópia de segurança suporta a compressão do tráfego de backup, tal como resumida na tabela seguinte. Tenha em atenção o seguinte:

- Para os VMs Azure, a extensão VM lê os dados diretamente da conta de armazenamento Azure sobre a rede de armazenamento. Não é necessário comprimir este tráfego.
- Se estiver a utilizar DPM ou MABS, pode poupar largura de banda comprimindo os dados antes de ser apoiado em DPM/MABS.

**Máquina** | **Comprimir para MABS/DPM (TCP)** | **Comprimir para cofre (HTTPS)**
--- | --- | ---
Máquinas Windows no local sem DPM/MABS | ND | ![Sim][green]
VMs do Azure | ND | ND
Instalações/VMs Azure com DPM | ![Yes][green] | ![Yes][green]
Em instalações/VMs Azure com MABS | ![Yes][green] | ![Sim][green]

## <a name="next-steps"></a>Próximos passos

- [Apoiar os VMs do Azure.](backup-azure-arm-vms-prepare.md)
- [Faça o backup das máquinas do Windows diretamente,](tutorial-backup-windows-server-to-azure.md)sem um servidor de reserva.
- [Crie mABS](backup-azure-microsoft-azure-backup.md) para apoio ao Azure e, em seguida, faça o backup das cargas de trabalho para o MABS.
- [Confiem dPM](backup-azure-dpm-introduction.md) para apoio ao Azure e, em seguida, rememtra as cargas de trabalho para dPM.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png

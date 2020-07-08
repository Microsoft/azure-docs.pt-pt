---
title: Matriz de suporte da cópia de segurança de uma VM do Azure
description: Fornece um resumo das definições e limitações de suporte ao fazer o backup dos VMs Azure com o serviço Azure Backup.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: 904240e066a83fa1278d663b8614b5b9269ba4d3
ms.sourcegitcommit: f684589322633f1a0fafb627a03498b148b0d521
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/06/2020
ms.locfileid: "85970676"
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
Backup direto dos VMs Azure  | Apoie todo o VM.  | Não é necessário nenhum agente adicional no Azure VM. A Azure Backup instala e utiliza uma extensão ao [agente Azure VM](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) que está em execução no VM. | Restaurar da seguinte forma:<br/><br/> - **Criar um VM básico.** Isto é útil se o VM não tiver configuração especial, como vários endereços IP.<br/><br/> - **Restaurar o disco VM**. Restaurar o disco. Em seguida, prenda-o a um VM existente, ou crie um novo VM a partir do disco utilizando o PowerShell.<br/><br/> - **Substitua o disco VM**. Se existir um VM e utilizar discos geridos (não encriptados), pode restaurar um disco e usá-lo para substituir um disco existente no VM.<br/><br/> - **Restaurar ficheiros/pastas específicos**. Pode restaurar ficheiros/pastas a partir de um VM em vez de de todo o VM.
Backup direto dos VMs Azure (apenas Windows)  | Fazer o back up ficheiros/pastas/volume específicos. | Instale o [agente dos Serviços de Recuperação Azure](backup-azure-file-folder-backup-faq.md).<br/><br/> Pode executar o agente MARS ao lado da extensão de reserva para o agente Azure VM fazer cópia de segurança do VM ao nível de ficheiro/pasta. | Restaurar pastas/ficheiros específicos.
Faça backup Azure VM para servidor de backup  | Fazer a ressalto de ficheiros/pastas/volumes; sistema estado/ficheiros metálicos nus; dados da aplicação para System Center DPM ou para Microsoft Azure Backup Server (MABS).<br/><br/> DPM/MABS, em seguida, recua para o cofre de reserva. | Instale o agente de proteção DPM/MABS no VM. O agente MARS está instalado no DPM/MABS.| Restaurar ficheiros/pastas/volumes; sistema estado/ficheiros metálicos nus; dados de aplicativos.

Saiba mais sobre a cópia de segurança [utilizando um servidor de backup](backup-architecture.md#architecture-back-up-to-dpmmabs) e sobre os [requisitos de suporte](backup-support-matrix-mabs-dpm.md).

>[!NOTE]
> **O Azure Backup suporta agora a cópia de segurança seletiva do disco e restaura utilizando a solução de backup da Máquina Virtual Azure.**
>
>Hoje em dia, o Azure Backup suporta o backup de todos os discos (Sistema Operativo e dados) num VM em conjunto utilizando a solução de backup da Máquina Virtual. Com a funcionalidade de exclusão do disco, obtém-se uma opção de backup de um ou alguns dos muitos discos de dados num VM. Isto fornece uma solução eficiente e rentável para as suas necessidades de backup e restauro. Cada ponto de recuperação contém dados dos discos incluídos na operação de backup, o que permite ainda ter um subconjunto de discos restaurados a partir do ponto de recuperação dado durante a operação de restauro. Isto aplica-se para restaurar tanto do instantâneo como do cofre.
>
>**Para se inscrever para a pré-visualização, escreva-nos emAskAzureBackupTeam@microsoft.com**

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
[Funcionalidades de segurança para backup híbrido](https://docs.microsoft.com/azure/backup/backup-azure-security-feature) |Não é suportado por desativação de funcionalidades de segurança.
Apoiar o VM cujo tempo de máquina foi alterado | Não suportado.<br/><br/> Se o tempo da máquina for alterado para uma data futura depois de permitir a cópia de segurança para esse VM; No entanto, mesmo que a mudança de tempo seja revertida, o backup bem sucedido não é garantido.  

## <a name="operating-system-support-windows"></a>Suporte ao sistema operativo (Windows)

A tabela seguinte resume os sistemas operativos suportados ao fazer o backup dos VMs windows Azure.

**Cenário** | **Suporte ao OS**
--- | ---
Apoiar com a extensão do agente Azure VM | - Cliente Do Windows 10 (apenas 64 bits) <br/><br/>- Windows Server 2019 (Datacenter/Datacenter Core/Standard) <br/><br/> - Windows Server 2016 (Datacenter/Datacenter Core/Standard) <br/><br/> - Windows Server 2012 R2 (Datacenter/Standard) <br/><br/> - Windows Server 2012 (Datacenter/Standard) <br/><br/> - Windows Server 2008 R2 (RTM e SP1 Standard)  <br/><br/> - Windows Server 2008 (apenas 64 bits)
Voltar para o agente MARS | [Sistemas](backup-support-matrix-mars-agent.md#supported-operating-systems) operativos suportados.
Voltar para cima com dPM/MABS | Sistemas operativos suportados para cópias de segurança com [MABS](backup-mabs-protection-matrix.md) e [DPM](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807).

O Azure Backup não suporta sistemas operativos de 32 bits.

## <a name="support-for-linux-backup"></a>Suporte para backup Linux

Aqui está o que é suportado se quiser apoiar as máquinas Linux.

**Ação** | **Suporte**
--- | ---
Apoie os VMs Linux Azure com o agente Linux Azure VM | Arquivar cópias de segurança consistentes.<br/><br/> Cópia de segurança consistente com aplicações utilizando [scripts personalizados.](backup-azure-linux-app-consistent.md)<br/><br/> Durante a restauração, pode criar um novo VM, restaurar um disco e usá-lo para criar um VM, ou restaurar um disco e usá-lo para substituir um disco num VM existente. Também pode restaurar ficheiros e pastas individuais.
Apoiar os VMs linux Azure com agente MARS | Não suportado.<br/><br/> O agente MARS só pode ser instalado em máquinas Windows.
Apoiar os VMs Linux Azure com DPM/MABS | Não suportado.

## <a name="operating-system-support-linux"></a>Suporte ao sistema operativo (Linux)

Para backups Azure VM Linux, a Azure Backup suporta a lista de distribuições Linux [endossadas pela Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros). Tenha em atenção o seguinte:

- O Azure Backup não suporta o Core OS Linux.
- O Azure Backup não suporta sistemas operativos de 32 bits.
- Outras distribuições linux próprias podem funcionar desde que o [agente Azure VM para Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) esteja disponível no VM, e enquanto python for suportado.
- O Azure Backup não suporta um Linux VM configurado por procuração se não tiver a versão Python 2.7 instalada.

## <a name="backup-frequency-and-retention"></a>Frequência e retenção de backup

**Definição** | **Limites**
--- | ---
Pontos de recuperação máximos por instância protegida (máquina/carga de trabalho) | 9999.
Prazo máximo de validade para um ponto de recuperação | Sem limite.
Frequência máxima de backup para o cofre (extensão Azure VM) | Uma vez por dia.
Frequência máxima de backup para cofre (agente MARS) | Três reforços por dia.
Frequência máxima de backup para DPM/MABS | A cada 15 minutos para o SQL Server.<br/><br/> Uma vez por hora para outras cargas de trabalho.
Retenção de pontos de recuperação | Diariamente, semanalmente, mensal e anualmente.
Período de retenção máximo | Depende da frequência de reserva.
Pontos de recuperação no disco DPM/MABS | 64 para servidores de ficheiros e 448 para servidores de aplicações.<br/><br/> Os pontos de recuperação de fita são ilimitados para DPM no local.

## <a name="supported-restore-methods"></a>Métodos de restauro apoiados

**Opção de restauro** | **Detalhes**
--- | ---
**Crie uma nova VM** | Rapidamente cria e obtém um VM básico a funcionar a partir de um ponto de restauro.<br/><br/> Pode especificar um nome para o VM, selecionar o grupo de recursos e a rede virtual (VNet) no qual será colocado e especificar uma conta de armazenamento para o VM restaurado. O novo VM deve ser criado na mesma região que a VM de origem.
**Restaurar disco** | Restaura um disco VM, que pode ser usado para criar um novo VM.<br/><br/> O Azure Backup fornece um modelo para ajudá-lo a personalizar e criar um VM. <br/><br> O trabalho de restauro gera um modelo que pode descarregar e usar para especificar as definições de VM personalizadas e criar um VM.<br/><br/> Os discos são copiados para o Grupo de Recursos que especifica.<br/><br/> Em alternativa, pode ligar o disco a um VM existente ou criar um novo VM utilizando o PowerShell.<br/><br/> Esta opção é útil se pretender personalizar o VM, adicionar definições de configuração que não estavam lá no momento da cópia de segurança, ou adicionar definições que devem ser configuradas usando o modelo ou PowerShell.
**Substituir os existentes** | Pode restaurar um disco e usá-lo para substituir um disco no VM existente.<br/><br/> O VM atual deve existir. Se for apagado, esta opção não pode ser usada.<br/><br/> O Azure Backup tira uma fotografia do VM existente antes de substituir o disco e armazena-o no local de preparação que especifica. Os discos existentes ligados ao VM são substituídos pelo ponto de restauro selecionado.<br/><br/> O instantâneo é copiado para o cofre, e mantido de acordo com a política de retenção. <br/><br/> Após a operação do disco de substituição, o disco original é mantido no grupo de recursos. Pode optar por eliminar manualmente os discos originais se não forem necessários. <br/><br/>A substituição existente é suportada por VMs geridos não encriptados. Não é suportado para discos não geridos, [VMs generalizados,](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)ou para VMs [criados usando imagens personalizadas](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/).<br/><br/> Se o ponto de restauro tiver mais ou menos discos do que o VM atual, então o número de discos no ponto de restauração só refletirá a configuração VM.<br><br> Substituir os VM existentes por recursos ligados (como [identidade gerida atribuída pelo utilizador](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) ou Key [Vault)](https://docs.microsoft.com/azure/key-vault/key-vault-overview)porque a aplicação de clientes de backup não tem permissões sobre estes recursos enquanto executa a restauração.
**Região Transversal (região secundária)** | A restauração da Região Transversal pode ser usada para restaurar os VMs Azure na região secundária, que é uma [região emparelhada Azure.](https://docs.microsoft.com/azure/best-practices-availability-paired-regions#what-are-paired-regions)<br><br> Pode restaurar todos os VMs Azure para o ponto de recuperação selecionado se a cópia de segurança for feita na região secundária.<br><br> Esta funcionalidade está disponível para as opções abaixo:<br> * [Criar um VM](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#create-a-vm) <br> * [Restaurar Discos](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-disks) <br><br> Atualmente, não apoiamos a opção [Substituir discos existentes.](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#replace-existing-disks)<br><br> Permissões<br> A operação de restauro na região secundária pode ser realizada por administradores de backup e administradores de aplicações.

## <a name="support-for-file-level-restore"></a>Suporte para restauro ao nível do ficheiro

**Restaurar** | **Suportado**
--- | ---
Restaurar ficheiros através de sistemas operativos | Pode restaurar ficheiros em qualquer máquina que tenha o mesmo (ou compatível) SO que o VM de back-up. Consulte a [tabela oss compatível](backup-azure-restore-files-from-vm.md#system-requirements).
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
Restaurar um controlador de domínio (DC) VM que faz parte de uma configuração multi-DC através do portal | Suportado se restaurar o disco e criar um VM utilizando o PowerShell.
Restaurar VM em diferentes redes virtuais |Suportado.<br/><br/> A rede virtual deve estar na mesma subscrição e região.

## <a name="vm-compute-support"></a>Suporte ao cálculo VM

**Computação** | **Suporte**
--- | ---
Tamanho da VM |Qualquer tamanho Azure VM com pelo menos 2 núcleos CPU e RAM de 1 GB.<br/><br/> [Saiba mais.](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)
Apoiar VMs em [conjuntos de disponibilidade](https://docs.microsoft.com/azure/virtual-machine-scale-sets/availability#availability-sets) | Suportado.<br/><br/> Não é possível restaurar um VM num conjunto disponível utilizando a opção de criar rapidamente um VM. Em vez disso, quando restaurar o VM, restaure o disco e use-o para implantar um VM, ou restaurar um disco e usá-lo para substituir um disco existente.
Back up VMs que são implantados com [Benefício de Utilização Híbrida (HUB)](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) | Suportado.
Apoiar VMs que são implantados em um [conjunto de escala](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview) |Não suportado.
Back up VMs que são implantados a partir do [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?filters=virtual-machine-images)<br/><br/> (Publicado pela Microsoft, terceiros) |Suportado.<br/><br/> O VM deve estar a executar um sistema operativo suportado.<br/><br/> Ao recuperar ficheiros sobre o VM, só pode restaurar um SISTEMA compatível (não um SO anterior ou posterior). Não restauramos os VMs do Azure Marketplace apoiados como VMs, pois estes precisam de informações de compra, mas apenas como Discos.
Back up VMs que são implantados a partir de uma imagem personalizada (terceiro) |Suportado.<br/><br/> O VM deve estar a executar um sistema operativo suportado.<br/><br/> Ao recuperar ficheiros sobre o VM, só pode restaurar um SISTEMA compatível (não um SO anterior ou posterior).
Back up VMs que são migrados para Azure| Suportado.<br/><br/> Para fazer uma proteção do VM, o agente VM tem de ser instalado na máquina migratória.
Apoiar consistência multi-VM | O Azure Backup não fornece consistência de dados e aplicações em vários VMs.
Backup com [Definições de Diagnóstico](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview)  | Sem apoio. <br/><br/> Se a restauração do VM Azure com definições de diagnóstico for acionada utilizando a opção [Create New,](backup-azure-arm-restore-vms.md#create-a-vm) então a restauração falha.
Restauro de VMs fixados em zona | Suportado (para VM que é apoiado após janeiro de 2019 e onde estão disponíveis [zona de disponibilidade).](https://azure.microsoft.com/global-infrastructure/availability-zones/)<br/><br/>Atualmente, apoiamos o restauro para a mesma zona que está presa em VMs. No entanto, se a zona não estiver disponível, o restauro falha.
GMs gen2 | Suportado <br> A Azure Backup suporta cópia de segurança e restauro de [VMs da Gen2](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/). Quando estes VMs são restaurados a partir do ponto de recuperação, são restaurados como [VMs Gen2](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/).
Backup de VMs Azure com fechaduras | Não suportado para VMs não geridos. <br><br> Suportado para VMs geridos.

## <a name="vm-storage-support"></a>Suporte de armazenamento VM

**Componente** | **Suporte**
--- | ---
Discos de dados Azure VM | O apoio ao backup dos VMs Azure com até 32 discos está em pré-visualização pública em todas as regiões, exceto National Clouds (Governo Azure, Azure China e Azure Alemanha).<br><br> O suporte para cópia de segurança de VMs Azure com discos não geridos ou VMs clássicos é apenas de 16 discos.
Tamanho do disco de dados | O tamanho do disco individual pode ser até 32 TB e um máximo de 256 TB combinados para todos os discos num VM.
Tipo de armazenamento | HDD padrão, SSD padrão, SSD premium.
Managed disks | Suportado.
Discos encriptados | Suportado.<br/><br/> Os VMs Azure ativados com encriptação de disco Azure podem ser apoiados (com ou sem a aplicação AD AZure).<br/><br/> Os VMs encriptados não podem ser recuperados ao nível do ficheiro/pasta. Tens de recuperar todo o VM.<br/><br/> Pode ativar a encriptação em VMs que já estão protegidos por Azure Backup.
Discos com acelerador de escrita ativados | Não suportado.<br/><br/> A cópia de segurança Azure exclui automaticamente os discos com o Write Accelerator (WA) ativado durante a cópia de segurança. Uma vez que não estão apoiados, não será capaz de restaurar estes discos a partir de Pontos de Recuperação do VM. <br><br> **Nota importante:** As máquinas virtuais com discos WA precisam de conectividade com a Internet para uma cópia de segurança bem sucedida (mesmo que esses discos estejam excluídos da cópia de segurança.)
Retrocede & Restaurar VMs/discos desduplicados | A Azure Backup não suporta a desduplicação. Para mais informações, consulte este [artigo](https://docs.microsoft.com/azure/backup/backup-support-matrix#disk-deduplication-support) <br/> <br/>  - A Azure Backup não desduplica através de VMs no cofre dos Serviços de Recuperação <br/> <br/>  - Se houver VMs em estado de desduplicação durante a restauração, os ficheiros não podem ser restaurados, uma vez que o cofre não entende o formato. No entanto, poderá realizar com sucesso a restauração completa do VM.
Adicione o disco ao VM protegido | Suportado.
Redimensione o disco em VM protegido | Suportado.
Armazenamento compartilhado| Não é suportado o backup de VMs utilizando o Cluster Shared Volume (CSV) ou o Servidor de Ficheiros Scale-Out. É provável que os escritores do CSV falhem durante o backup. No restauro, os discos que contêm volumes de CSV podem não aparecer.
[Discos partilhados](https://docs.microsoft.com/azure/virtual-machines/windows/disks-shared-enable) | Não suportado.

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
Traffic Manager do Azure| Suportado.<br/><br/>Se o VM de apoio estiver no Traffic Manager, adicione manualmente o VM restaurado à mesma instância do Gestor de Tráfego.
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
- Só você tem a palavra-passe para desbloquear estes dados. A Microsoft não pode desencriptar os dados de backup em nenhum momento.

  > [!WARNING]
  > Depois de configurar o cofre, só tem acesso à chave de encriptação. A Microsoft nunca mantém uma cópia e não tem acesso à chave. Se a chave estiver extraviada, a Microsoft não poderá recuperar os dados de backup.

Segurança de dados:

- Ao fazer o backup dos VMs Azure, é necessário configurar a encriptação *dentro* da máquina virtual.
- O Azure Backup suporta a Encriptação do Disco Azure, que utiliza o BitLocker em máquinas virtuais do Windows e **nós, a dm-crypt** em máquinas virtuais Linux.
- No back-end, o Azure Backup utiliza a [encriptação do Serviço de Armazenamento do Azure](../storage/common/storage-service-encryption.md), que protege os dados inativos.

**Máquina** | **Em trânsito** | **Em repouso**
--- | --- | ---
Máquinas Windows no local sem DPM/MABS | ![Sim][green] | ![Sim][green]
VMs do Azure | ![Sim][green] | ![Sim][green]
Instalações/VMs Azure com DPM | ![Sim][green] | ![Sim][green]
Em instalações/VMs Azure com MABS | ![Sim][green] | ![Sim][green]

## <a name="vm-compression-support"></a>Suporte à compressão VM

A cópia de segurança suporta a compressão do tráfego de backup, tal como resumida na tabela seguinte. Tenha em atenção o seguinte:

- Para os VMs Azure, a extensão VM lê os dados diretamente da conta de armazenamento Azure sobre a rede de armazenamento. Não é necessário comprimir este tráfego.
- Se estiver a utilizar DPM ou MABS, pode poupar largura de banda comprimindo os dados antes de ser apoiado em DPM/MABS.

**Máquina** | **Comprimir para MABS/DPM (TCP)** | **Comprimir para cofre (HTTPS)**
--- | --- | ---
Máquinas Windows no local sem DPM/MABS | ND | ![Sim][green]
VMs do Azure | ND | ND
Instalações/VMs Azure com DPM | ![Sim][green] | ![Sim][green]
Em instalações/VMs Azure com MABS | ![Sim][green] | ![Sim][green]

## <a name="next-steps"></a>Próximos passos

- [Apoiar os VMs do Azure.](backup-azure-arm-vms-prepare.md)
- [Faça o backup das máquinas do Windows diretamente,](tutorial-backup-windows-server-to-azure.md)sem um servidor de reserva.
- [Crie mABS](backup-azure-microsoft-azure-backup.md) para apoio ao Azure e, em seguida, faça o backup das cargas de trabalho para o MABS.
- [Confiem dPM](backup-azure-dpm-introduction.md) para apoio ao Azure e, em seguida, rememtra as cargas de trabalho para dPM.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png

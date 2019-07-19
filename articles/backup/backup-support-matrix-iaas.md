---
title: Matriz de suporte de backup do Azure para backup de VM do Azure
description: Fornece um resumo das configurações de suporte e limitações ao fazer backup de VMs do Azure com o serviço de backup do Azure.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: raynew
ms.openlocfilehash: 3823bca0601f825323a44773f8c70be371ec8781
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68311653"
---
# <a name="support-matrix-for-azure-vm-backup"></a>Matriz de suporte para backup de VM do Azure
Você pode usar o [serviço de backup do Azure](backup-overview.md) para fazer backup de máquinas locais e cargas de trabalho e VMs (máquinas virtuais) do Azure. Este artigo resume as configurações de suporte e as limitações ao fazer backup de VMs do Azure com o backup do Azure.

Outras matrizes de suporte:

- [Matriz de suporte geral](backup-support-matrix.md) para o backup do Azure
- [Matriz de suporte](backup-support-matrix-mabs-dpm.md) para o servidor de backup do Azure/backup do System Center Data Protection Manager (DPM)
- [Matriz de suporte](backup-support-matrix-mars-agent.md) para backup com o agente de serviços de recuperação do Microsoft Azure (MARS)

## <a name="supported-scenarios"></a>Cenários suportados

Veja como você pode fazer backup e restaurar VMs do Azure com o serviço de backup do Azure.

**Cenário** | **Cópia de segurança** | **Agente** |**Restaurar**
--- | --- | --- | ---
Backup direto de VMs do Azure  | Faça backup de toda a VM.  | Nenhum agente é necessário na VM do Azure. O backup do Azure é instalado e usa uma extensão para o [agente de VM do Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) que está em execução na VM. | Restaure da seguinte maneira:<br/><br/> - **Crie uma VM básica**. Isso será útil se a VM não tiver nenhuma configuração especial, como vários endereços IP.<br/><br/> - **Restaure o disco da VM**. Restaure o disco. Em seguida, anexe-o a uma VM existente ou crie uma nova VM do disco usando o PowerShell.<br/><br/> - **Substituir disco da VM**. Se uma VM existir e ela usar discos gerenciados (não criptografados), você poderá restaurar um disco e usá-lo para substituir um disco existente na VM.<br/><br/> - **Restaurar arquivos/pastas específicos**. Você pode restaurar arquivos/pastas de uma VM em vez de uma VM inteira.
Backup direto de VMs do Azure (somente Windows)  | Fazer backup de arquivos/pastas/volumes específicos. | Instale o [agente dos serviços de recuperação do Azure](backup-azure-file-folder-backup-faq.md).<br/><br/> Você pode executar o agente MARS junto com a extensão de backup do agente de VM do Azure para fazer backup da VM no nível de arquivo/pasta. | Restaurar pastas/arquivos específicos.
Fazer backup da VM do Azure no servidor de backup  | Fazer backup de arquivos/pastas/volumes; arquivos de estado do sistema/bare-metal; dados de aplicativo para o System Center DPM ou para Backup do Microsoft Azure Server (MABS).<br/><br/> O DPM/MABS faz backup para o cofre de backup. | Instale o agente de proteção do DPM/MABS na VM. O agente MARS é instalado no DPM/MABS.| Restaurar arquivos/pastas/volumes; arquivos de estado do sistema/bare-metal; dados do aplicativo.

Saiba mais sobre [o backup usando um servidor de backup](backup-architecture.md#architecture-back-up-to-dpmmabs) e sobre [os requisitos de suporte](backup-support-matrix-mabs-dpm.md).

## <a name="supported-backup-actions"></a>Ações de backup com suporte

**ação** | **Suporte**
--- | ---
Habilitar backup ao criar uma VM do Windows Azure | Com suporte para: <br/><br/> -Windows Server 2019 (datacenter/Data Center Core/Standard) <br/><br/> -Windows Server 2016 (datacenter/Data Center Core/Standard) <br/><br/> -Windows Server 2012 R2 (datacenter/padrão) <br/><br/> -Windows Server 2008 R2 (RTM e SP1 Standard)
Habilitar backup ao criar uma VM do Linux | Com suporte para:<br/><br/> -Servidor Ubuntu: 18, 4, 17,10, 17, 4, 16, 4 (LTS), 14, 4 (LTS)<br/><br/> -Red Hat: RHEL 6.7, 6.8, 6.9, 7.2, 7.3, 7.4<br/><br/> -SUSE Linux Enterprise Server: 11 SP4, 12 SP2, 12 SP3, 15 <br/><br/> Debian 8, 9<br/><br/> CentOS 6,9, 7,3<br/><br/> -Oracle Linux: 6.7, 6.8, 6.9, 7.2, 7.3
Fazer backup de uma VM que está desligada/VM offline | Suportado.<br/><br/> O instantâneo é consistente somente com falha, não consistente com o aplicativo.
Fazer backup de discos após a migração para o Managed disks | Suportado.<br/><br/> O backup continuará a funcionar. nenhuma ação necessária.
Fazer backup de discos gerenciados depois de habilitar o bloqueio do grupo de recursos | Não suportado.<br/><br/> O backup do Azure não pode excluir os pontos de restauração mais antigos, e os backups começarão a falhar quando o limite máximo de pontos de restauração for atingido.
Modificar a política de backup para uma VM | Suportado.<br/><br/> O backup da VM será feito usando as configurações de agendamento e retenção em nova política. Se as configurações de retenção forem estendidas, os pontos de recuperação existentes serão marcados e mantidos. Se eles forem reduzidos, os pontos de recuperação existentes serão removidos no próximo trabalho de limpeza e eventualmente excluídos.
Cancelar um trabalho de backup | Com suporte durante o processo de instantâneo.<br/><br/> Sem suporte quando o instantâneo está sendo transferido para o cofre.
Fazer backup da VM em uma região ou assinatura diferente |  Não suportado.
Backups por dia (por meio da extensão de VM do Azure) | Um backup agendado por dia.<br/><br/> Você pode fazer até quatro backups sob demanda por dia.
Backups por dia (por meio do agente MARS) | Três backups agendados por dia.
Backups por dia (por meio do DPM/MABS) | Dois backups agendados por dia.
Backup mensal/anual   | Sem suporte ao fazer backup com a extensão de VM do Azure. Há suporte apenas para Daily e Weekly.<br/><br/> Você pode configurar a política para reter backups diários/semanais por período de retenção mensal/anual.
Ajuste automático do relógio | Não suportado.<br/><br/> O backup do Azure não é ajustado automaticamente para alterações de horário de verão ao fazer backup de uma VM.<br/><br/>  Modifique a política manualmente, conforme necessário.
[Recursos de segurança para o backup híbrido](https://docs.microsoft.com/azure/backup/backup-azure-security-feature) |  Não há suporte para a desabilitação de recursos de segurança.
Fazer backup da VM cuja hora da máquina foi alterada | Não suportado.<br/><br/> Se a hora da máquina for alterada para uma data/hora futura depois de habilitar o backup para essa VM; No entanto, mesmo se a alteração de tempo for revertida, o backup bem-sucedido não será garantido.  


## <a name="operating-system-support-windows"></a>Suporte ao sistema operacional (Windows)

A tabela a seguir resume os sistemas operacionais com suporte ao fazer backup de VMs do Windows Azure.

**Cenário** | **Suporte do so**
--- | ---
Fazer backup com a extensão do agente de VM do Azure | Cliente do Windows: Não suportado<br/><br/>-Windows Server 2019 (datacenter/Data Center Core/Standard) <br/><br/> -Windows Server 2016 (datacenter/Data Center Core/Standard) <br/><br/> -Windows Server 2012 R2 (datacenter/padrão) <br/><br/> -Windows Server 2008 R2 (RTM e SP1 Standard)
Fazer backup com o agente MARS | Sistemas operacionais [com suporte](backup-support-matrix-mars-agent.md#support-for-direct-backups) .
Fazer backup com o DPM/MABS | Sistemas operacionais com suporte para backup com o [mAbs](backup-mabs-protection-matrix.md) e [o DPM](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807).

## <a name="support-for-linux-backup"></a>Suporte para backup do Linux

Aqui estão as suportadas se você quiser fazer backup de computadores Linux.

**ação** | **Suporte**
--- | ---
Fazer backup de VMs do Linux Azure com o agente de VM do Azure para Linux | Backup consistente de arquivo.<br/><br/> Backup consistente com o aplicativo usando [scripts personalizados](backup-azure-linux-app-consistent.md).<br/><br/> Durante a restauração, você pode criar uma nova VM, restaurar um disco e usá-lo para criar uma VM ou restaurar um disco e usá-lo para substituir um disco em uma VM existente. Você também pode restaurar arquivos e pastas individuais.
Fazer backup de VMs do Linux Azure com o agente MARS | Não suportado.<br/><br/> O agente MARS só pode ser instalado em computadores Windows.
Fazer backup de VMs do Linux Azure com o DPM/MABS | Não suportado.

## <a name="operating-system-support-linux"></a>Suporte ao sistema operacional (Linux)

Para backups do Linux de VM do Azure, o backup do Azure dá suporte à lista de distribuições do Linux [endossadas pelo Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros). Tenha em atenção o seguinte:

- O backup do Azure não dá suporte ao sistema operacional principal Linux.
- O backup do Azure não dá suporte a sistemas operacionais de 32 bits.
- Outras distribuições do Linux traga seu próprio trabalho podem funcionar contanto que o [agente de VM do Azure para Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) esteja disponível na VM e, desde que o Python tenha suporte.
- O backup do Azure não oferece suporte a uma VM do Linux configurada por proxy se não tiver a versão 2,7 do Python instalada.


## <a name="backup-frequency-and-retention"></a>Frequência de backup e retenção

**Definição** | **Limites**
--- | ---
Pontos de recuperação máximos por instância protegida (máquina/carga de trabalho) | 9999.
Tempo máximo de expiração para um ponto de recuperação | Sem limite.
Frequência máxima de backup para o cofre (extensão de VM do Azure) | Uma vez por dia.
Frequência máxima de backup para o cofre (agente MARS) | Três backups por dia.
Frequência máxima de backup para o DPM/MABS | A cada 15 minutos para SQL Server.<br/><br/> Uma vez por hora para outras cargas de trabalho.
Retenção do ponto de recuperação | Diário, semanal, mensal e anual.
Período de retenção máximo | Depende da frequência de backup.
Pontos de recuperação no disco do DPM/MABS | 64 para servidores de arquivos e 448 para servidores de aplicativos.<br/><br/> Os pontos de recuperação de fita são ilimitados para o DPM local.

## <a name="supported-restore-methods"></a>Métodos de restauração com suporte

**Método de restauração** | **Detalhes**
--- | ---
Criar uma nova VM | Você pode criar uma VM durante o processo de restauração. <br/><br/> Essa opção Obtém uma VM básica em funcionamento. Você pode especificar o nome da VM, o grupo de recursos, a rede virtual, a sub-rede e o armazenamento.  
Restaurar discos | Você pode restaurar um disco e usá-lo para criar uma VM.<br/><br/> Quando você seleciona essa opção, o backup do Azure copia dados do cofre para uma conta de armazenamento que você selecionar. O trabalho de restauração gera um modelo. Você pode baixar esse modelo, usá-lo para especificar configurações de VM personalizadas e criar uma VM.<br/><br/> Essa opção permite que você especifique mais configurações que a opção anterior para criar uma VM.<br/><br/>
Substituir um disco existente | Você pode restaurar um disco e, em seguida, usar o disco restaurado para substituir um disco que esteja atualmente em uma VM.
Restaurar arquivos | Você pode recuperar arquivos de um ponto de recuperação selecionado. Você baixa um script para montar o disco da VM do ponto de recuperação. Em seguida, navegue pelos volumes de disco para encontrar os arquivos/pastas que você deseja recuperar e desmonte o disco quando terminar.

## <a name="support-for-file-level-restore"></a>Suporte para restauração em nível de arquivo

**Restaurar** | **Suportado**
--- | ---
Restaurando arquivos entre sistemas operacionais | Você pode restaurar arquivos em qualquer computador que tenha o mesmo sistema operacional (ou compatível) que a VM de backup. Consulte a [tabela de so compatível](backup-azure-restore-files-from-vm.md#system-requirements).
Restaurando arquivos em VMs clássicas | Não suportado.
Restaurando arquivos de VMs criptografadas | Não suportado.
Restaurando arquivos de contas de armazenamento restritas à rede | Não suportado.
Restaurando arquivos em VMs usando espaços de armazenamento do Windows | Restore não tem suporte na mesma VM.<br/><br/> Em vez disso, restaure os arquivos em uma VM compatível.
Restaurar arquivos na VM do Linux usando matrizes LVM/RAID | Restore não tem suporte na mesma VM.<br/><br/> Restaurar em uma VM compatível.
Restaurar arquivos com configurações de rede especiais | Restore não tem suporte na mesma VM. <br/><br/> Restaurar em uma VM compatível.

## <a name="support-for-vm-management"></a>Suporte para gerenciamento de VM

A tabela a seguir resume o suporte para backup durante as tarefas de gerenciamento de VM, como adicionar ou substituir discos de VM.

**Restaurar** | **Suportado**
--- | ---
Restaurar entre a assinatura/região/zona. | Não suportado.
Restaurar para uma VM existente | Use a opção substituir disco.
Restaurar disco com a conta de armazenamento habilitada para o Azure Criptografia do Serviço de Armazenamento (SSE) | Não suportado.<br/><br/> Restaure para uma conta que não tenha a SSE habilitada.
Restaurar para contas de armazenamento mistas | Não suportado.<br/><br/> Com base no tipo de conta de armazenamento, todos os discos restaurados serão Premium ou Standard e não misturados.
Restaurar para a conta de armazenamento usando o ZRS (armazenamento com redundância de zona) | Com suporte (para a VM que é submetida a backup depois de Jan 2019 e onde a [zona de disponibilidade](https://azure.microsoft.com/global-infrastructure/availability-zones/) está disponível)
Restaurar a VM diretamente para um conjunto de disponibilidade | Para discos gerenciados, você pode restaurar o disco e usar a opção conjunto de disponibilidade no modelo.<br/><br/> Não há suporte para discos não gerenciados. Para discos não gerenciados, restaure o disco e, em seguida, crie uma VM no conjunto de disponibilidade.
Restaurar o backup de VMs não gerenciadas após a atualização para a VM gerenciada| Suportado.<br/><br/> Você pode restaurar discos e, em seguida, criar uma VM gerenciada.
Restaurar a VM para o ponto de restauração antes de a VM ser migrada para o Managed disks | Suportado.<br/><br/> Você restaura para discos não gerenciados (padrão), converte os discos restaurados em disco gerenciado e cria uma VM com os discos gerenciados.
Restaure uma VM que foi excluída. | Suportado.<br/><br/> Você pode restaurar a VM de um ponto de recuperação.
Restaurar uma VM de DC (controlador de domínio) que faz parte de uma configuração de vários DCS por meio do portal | Com suporte se você restaurar o disco e criar uma VM usando o PowerShell.
Restaurar a VM em uma rede virtual diferente |   Suportado.<br/><br/> A rede virtual deve estar na mesma assinatura e região.

## <a name="vm-compute-support"></a>Suporte de computação de VM

**Computação** | **Suporte**
--- | ---
Tamanho da VM |   Qualquer tamanho de VM do Azure com pelo menos 2 núcleos de CPU e 1 GB de RAM.<br/><br/> [Saiba mais.](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)
Fazer backup de VMs em [conjuntos de disponibilidade](https://docs.microsoft.com/azure/virtual-machines/windows/regions-and-availability#availability-sets) | Suportado.<br/><br/> Não é possível restaurar uma VM em um conjunto disponível usando a opção para criar rapidamente uma VM. Em vez disso, ao restaurar a VM, restaure o disco e use-o para implantar uma VM ou restaurar um disco e usá-lo para substituir um disco existente.
Fazer backup de VMs em [zonas de disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-overview) |  Não suportado.
Fazer backup de VMs implantadas com o [benefício de uso híbrido (Hub)](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) | Suportado.
Fazer backup de VMs implantadas em um [conjunto](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview) de dimensionamento |  Não suportado.
Fazer backup de VMs implantadas do [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?filters=virtual-machine-images)<br/><br/> (Publicado pela Microsoft, terceiros) |  Suportado.<br/><br/> A VM deve estar executando um sistema operacional com suporte.<br/><br/> Ao recuperar arquivos na VM, você pode restaurar somente para um sistema operacional compatível (não um sistema operacional anterior ou posterior). Não restauramos as VMs do Azure Marketplace apoiadas como VMs, pois elas precisam comprar informações, mas somente como discos.
Fazer backup de VMs implantadas de uma imagem personalizada (terceiros) |   Suportado.<br/><br/> A VM deve estar executando um sistema operacional com suporte.<br/><br/> Ao recuperar arquivos na VM, você pode restaurar somente para um sistema operacional compatível (não um sistema operacional anterior ou posterior).
Fazer backup de VMs que são migradas para o Azure  | Suportado.<br/><br/> Para fazer backup da VM, o agente de VM deve ser instalado no computador migrado.
Fazer backup de consistência de várias VMs | O backup do Azure não fornece consistência de aplicativos e dados em várias VMs.
Backup com [configurações de diagnóstico](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview)  | Sem suporte. <br/><br/> Se a restauração da VM do Azure com configurações de diagnóstico for disparada usando a opção [criar nova](backup-azure-arm-restore-vms.md#create-a-vm) , a restauração falhará.


## <a name="vm-storage-support"></a>Suporte ao armazenamento de VM

**Componente** | **Suporte**
--- | ---
Discos de dados de VM do Azure | Faça backup de uma VM com 16 ou menos discos de dados. <br/><br/> Oferece suporte a tamanhos de disco de até 4 TB.
Tamanho do disco de dados | O disco individual pode ter até 4095 GB.<br/><br/> Se seus cofres estiverem executando a versão mais recente do backup do Azure (conhecida como restauração instantânea), haverá suporte para tamanhos de disco de até 4 TB. [Saiba mais](backup-instant-restore-capability.md).  
Tipo de armazenamento | HDD Standard, SSD padrão, SSD Premium. <br/><br/> SSD Standard terá suporte se seus cofres forem atualizados para a versão mais recente do backup de VM do Azure (conhecida como restauração instantânea). [Saiba mais](backup-instant-restore-capability.md).
Managed disks | Suportado.
Discos criptografados | Suportado.<br/><br/> As VMs do Azure habilitadas com Azure Disk Encryption podem ser submetidas a backup (com ou sem o aplicativo do Azure AD).<br/><br/> As VMs criptografadas não podem ser recuperadas no nível de arquivo/pasta. Você deve recuperar toda a VM.<br/><br/> Você pode habilitar a criptografia em VMs que já estão protegidas pelo backup do Azure.
Discos com Acelerador de Gravação habilitados | Não suportado.<br/><br/> O backup do Azure exclui automaticamente os discos com Acelerador de Gravação habilitado durante o backup. Como não é feito backup, você não poderá restaurar esses discos de pontos de recuperação da VM.
Fazer backup de discos com eliminação de duplicação | Não suportado.
Adicionar disco à VM protegida | Suportado.
Redimensionar disco na VM protegida | Suportado.
Armazenamento compartilhado| O backup de VMs usando Volume Compartilhado Clusterizado (CSV) ou servidor de arquivos de escalabilidade horizontal não é recomendado. Os gravadores de CSV provavelmente falharão durante o backup. Na restauração, os discos que contêm volumes CSV podem não vir.



## <a name="vm-network-support"></a>Suporte à rede VM

**Componente** | **Suporte**
--- | ---
Número de adaptadores de rede (NICs) | Até o número máximo de NICs com suporte para um tamanho de VM do Azure específico.<br/><br/> As NICs são criadas quando a VM é criada durante o processo de restauração.<br/><br/> O número de NICs na VM restaurada espelha o número de NICs na VM quando você habilita a proteção. A remoção de NICs depois que você habilita a proteção não afeta a contagem.
Balanceador de carga externo/interno |   Suportado. <br/><br/> [Saiba mais](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) sobre como restaurar VMs com configurações de rede especiais.
Vários endereços IP reservados |    Suportado. <br/><br/> [Saiba mais](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) sobre como restaurar VMs com configurações de rede especiais.
VMs com vários adaptadores de rede  | Suportado. <br/><br/> [Saiba mais](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) sobre como restaurar VMs com configurações de rede especiais.
VMs com endereços IP públicos    | Suportado.<br/><br/> Associe um endereço IP público existente à NIC ou crie um endereço e associe-o à NIC após a restauração.
Grupo de segurança de rede (NSG) na NIC/sub-rede. |   Suportado.
Endereço de IP Reservado (estático) | Não suportado.<br/><br/> Não é possível fazer backup de uma VM com um endereço IP reservado e nenhum ponto de extremidade definido.
Endereço IP dinâmico |    Suportado.<br/><br/> Se a NIC na VM de origem usar o endereçamento de IP dinâmico, por padrão, a NIC na VM restaurada também a usará.
Traffic Manager do Azure   | Suportado.<br/><br/>Se a VM com backup estiver no Gerenciador de tráfego, adicione manualmente a VM restaurada à mesma instância do Gerenciador de tráfego.
DNS do Azure | Suportado.
DNS Personalizado |    Suportado.
Conectividade de saída via proxy HTTP | Suportado.<br/><br/> Não há suporte para um proxy autenticado.
Pontos finais de serviço de rede virtual   | Suportado.<br/><br/> As configurações de firewall e de conta de armazenamento de rede virtual devem permitir o acesso de todas as redes.



## <a name="vm-security-and-encryption-support"></a>Segurança de VM e suporte de criptografia

O backup do Azure dá suporte à criptografia para dados em trânsito e em repouso:

Tráfego de rede para o Azure:

- O tráfego de backup de servidores para o cofre dos serviços de recuperação é criptografado usando o criptografia AES 256.
- Os dados de backup são enviados por um link HTTPS seguro.
- Os dados de backup são armazenados no cofre dos serviços de recuperação em formato criptografado.
- Somente você tem a frase secreta para desbloquear esses dados. A Microsoft não pode descriptografar os dados de backup a qualquer momento.

  > [!WARNING]
  > Depois de configurar o cofre, somente você terá acesso à chave de criptografia. A Microsoft nunca mantém uma cópia e não tem acesso à chave. Se a chave estiver incorreta, a Microsoft não poderá recuperar os dados de backup.

Segurança de dados:

- Ao fazer backup de VMs do Azure, você precisa configurar a criptografia *na* máquina virtual.
- O backup do Azure dá suporte a Azure Disk Encryption, que usa o BitLocker em máquinas virtuais do Windows e **DMI-cript** em máquinas virtuais Linux.
- No back-end, o Azure Backup utiliza a [encriptação do Serviço de Armazenamento do Azure](../storage/common/storage-service-encryption.md), que protege os dados inativos.


**Tradução** | **Em trânsito** | **Em repouso**
--- | --- | ---
Computadores Windows locais sem o DPM/MABS | ![Sim][green] | ![Sim][green]
VMs do Azure | ![Sim][green] | ![Sim][green]
VMs locais/do Azure com o DPM | ![Sim][green] | ![Sim][green]
VMs locais/do Azure com MABS | ![Sim][green] | ![Sim][green]



## <a name="vm-compression-support"></a>Suporte à compactação de VM

O backup dá suporte à compactação de tráfego de backup, conforme resumido na tabela a seguir. Tenha em atenção o seguinte:

- Para VMs do Azure, a extensão de VM lê os dados diretamente da conta de armazenamento do Azure pela rede de armazenamento. Não é necessário compactar esse tráfego.
- Se você estiver usando o DPM ou o MABS, poderá economizar largura de banda compactando os dados antes de fazer backup no DPM/MABS.

**Tradução** | **Compactar para MABS/DPM (TCP)** | **Compactar para o cofre (HTTPS)**
--- | --- | ---
Computadores Windows locais sem o DPM/MABS | ND | ![Sim][green]
VMs do Azure | ND | ND
VMs locais/do Azure com o DPM | ![Sim][green] | ![Sim][green]
VMs locais/do Azure com MABS | ![Sim][green] | ![Sim][green]


## <a name="next-steps"></a>Passos Seguintes

- [Fazer backup de VMs do Azure](backup-azure-arm-vms-prepare.md).
- [Faça backup de computadores Windows diretamente](tutorial-backup-windows-server-to-azure.md), sem um servidor de backup.
- [Configure o mAbs](backup-azure-microsoft-azure-backup.md) para backup no Azure e, em seguida, faça backup das cargas de trabalho para mAbs.
- [Configure o DPM](backup-azure-dpm-introduction.md) para backup no Azure e, em seguida, faça backup das cargas de trabalho no DPM.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png

---
title: Matriz de suporte de cópia de segurança do Azure para cópia de segurança de VM do Azure
description: Fornece um resumo das definições de suporte e limitações ao fazer backup de VMs do Azure com o serviço de cópia de segurança do Azure.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/17/2019
ms.author: raynew
ms.openlocfilehash: 2bd499c3afc423047dda9ff3ad657d110dab282a
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56673652"
---
# <a name="support-matrix-for-azure-vm-backup"></a>Matriz de suporte para cópia de segurança de VM do Azure
Pode utilizar o [serviço de cópia de segurança do Azure](backup-overview.md) para fazer uma cópia de segurança de máquinas no local e cargas de trabalho e as VMs do Azure. Este artigo resume as definições de suporte e limitações ao fazer backup de máquinas virtuais do Azure (VMs) com o Azure Backup.

Outras matrizes de suporte:

- [Matriz de suporte geral](backup-support-matrix.md) para Backkup do Azure
- [Matriz de suporte](backup-support-matrix-mabs-dpm.md) para cópia de segurança de servidor/DOM a cópia de segurança do Microsoft Azure
- [Matriz de suporte](backup-support-matrix-mars-agent.md) para cópia de segurança com o agente MARS



## <a name="supported-scenarios"></a>Cenários suportados

Eis como pode criar cópias de segurança e restaurar VMs do Azure com o serviço de cópia de segurança do Azure.


**Cenário** | **Cópia de segurança** | **Agente** |**Restaurar** 
--- | --- | --- | ---
**Cópia de segurança direta de VMs do Azure** | Fazer cópias de segurança de toda a VM  | Nenhum agente é necessária na VM do Azure. O Azure Backup instala e usa uma extensão para o [agente de VM do Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) em execução na VM. | Restaure da seguinte forma:<br/><br/> - **Criar uma VM básica**. Isto é útil se a VM não tem nenhuma configuração especial, como endereços IP de vários conjuntos.<br/><br/> - **Restaurar o disco da VM**. Restaure o disco. Em seguida, anexá-lo a uma VM existente ou criar uma nova VM a partir do disco com o PowerShell.<br/><br/> - **Substituir o disco da VM**. Se existe uma VM e utiliza discos geridos (sem criptografia), pode restaurar um disco e utilizá-lo para substituir um disco existente na VM.<br/><br/> - **Restaurar ficheiros/pastas específicas**. Pode restaurar ficheiros/pastas a partir de uma VM, em vez de toda a VM.  
**Cópia de segurança direta de VMs do Azure (apenas Windows)** | Fazer cópias de segurança de ficheiros/pastas/volume do específico | Instalar o [agente dos serviços de recuperação do Azure (MARS) da Microsoft](backup-azure-file-folder-backup-faq.md).<br/><br/> Pode executar o agente de MARS juntamente com a extensão de cópia de segurança para o agente de VM do Azure criar cópias de segurança a VM ao nível do ficheiro/pasta. | Restaure ficheiros/pastas específicos.
**Fazer cópias de segurança de VM do Azure para servidor de cópia de segurança** |  Fazer cópias de segurança de ficheiros/pastas/volumes; Estado/bare-metal arquivos do sistema; dados de aplicação para o System Center DPM ou o Microsoft Azure Backup Server (servidor MAB).<br/><br/> O DPM/MABS, em seguida, efetua cópias de segurança para o Cofre de cópia de segurança | Instale o agente de proteção do MABS/DPM na VM. O agente MARS está instalado no DPM/MABS.| Restaurar ficheiros/pastas/volumes; Estado/bare-metal arquivos do sistema; dados da aplicação. 

Saiba mais sobre a cópia de segurança [a utilizar um servidor de cópia de segurança](backup-architecture.md#architecture-back-up-to-dpmmabs), e [suportar requisitos](backup-support-matrix-mabs-dpm.md).


## <a name="supported-backup-actions"></a>Ações de cópia de segurança suportadas

**Ação** | **Suporte**
--- | ---
Ativar cópia de segurança quando criar uma VM do Windows Azure | Suporte para:  Windows Server 2016 (núcleos de Datacenter/Datacenter); Windows Server 2012 R2 DataCenter; Windows Server 2008 R2 SP1
Ativar cópia de segurança, quando cria uma VM do Linux | Suporte para:<br/><br/> - Ubuntu Server: 1710, 1704, 1604 (LTS), 1404 (LTS)<br/><br/> -Red Hat: RHEL 6.7, 6.8, 6.9, 7.2, 7.3, 7.4<br/><br/> - SUSE Linux Enterprise Server: 11 SP4, 12 SP2, 12 SP3<br/><br/> -Debian: 8, 9<br/><br/> - CentOS: 6.9, 7.3<br/><br/> Oracle Linux: 6.7, 6.8, 6.9, 7.2, 7.3
Fazer cópias de segurança de uma VM que VM de encerramento/offline/buscando | Suportado.<br/><br/> Instantâneo é consistente de falhas única, não consistente com a aplicação.
Criar cópias de segurança discos depois de migrar para discos geridos. | Suportado.<br/><br/> Cópia de segurança continuarão a funcionar. Não é necessário realizar qualquer ação.
Fazer cópias de segurança de discos geridos depois de ativar o bloqueio do grupo de recursos | Não suportado.<br/><br/> Cópia de segurança não é possível eliminar os pontos mais antigos do recurso e as cópias de segurança começarão a falhar quando for atingido o limite máximo de pontos de restauro.
Modificar a política de cópia de segurança para uma VM | Suportado.<br/><br/> A VM será efetuada utilizando as definições de agendamento e retenção na nova política. Se as definições de retenção estão expandidas, pontos de recuperação existentes são marcados e mantidos. Se eles estão reduzidos, pontos de recuperação existentes serão eliminados na próxima tarefa de limpeza e, eventualmente, eliminados.
Cancelar uma tarefa de cópia de segurança | Suportado durante o processo de instantâneo.<br/><br/> Não é suportada quando o instantâneo é que está a ser transferido para o cofre. 
Criar cópias de segurança da VM para uma região ou subscrição diferente |  Não suportado.
Cópias de segurança por dia (por meio da extensão de VM do Azure) | Uma cópia de segurança agendada por dia.<br/><br/> Pode demorar até quatro cópias de segurança a pedido por dia.
Cópias de segurança por dia (por meio do agente de MARS) | Três cópias de segurança agendadas por dia. 
Cópias de segurança por dia (por meio do DPM/MABS) | Duas cópias de segurança agendadas por dia.
Cópia de segurança mensais/anuais   | Não é suportada quando a cópia de segurança com a extensão de VM do Azure. Apenas é suportado diárias e semanais.<br/><br/> Pode configurar a política de modo a reter cópias de segurança diárias/semanais durante período de retenção mensais/anuais.
Ajuste automático do relógio | Não suportado.<br/><br/> O Azure Backup não use o ajuste automático do relógio para alterações de economia de hora de Verão em conta ao fazer backup de VM.<br/><br/>  Modificar a política manualmente, conforme necessário.
[Recursos de segurança para cópia de segurança híbridas](https://docs.microsoft.com/azure/backup/backup-azure-security-feature) |  Desativar as funcionalidades não é suportada.



## <a name="operating-system-support-windows"></a>Suporte do sistema operativo (Windows)

A tabela resume os sistemas operativos suportados, ao fazer backup de VMs do Windows Azure.

**Cenário** | **Suporte do sistema operacional** 
--- | ---
Criar cópias de segurança com a extensão de agente de VM do Azure | Cliente do Windows: Não suportado<br/><br/> Windows Server: Windows Server 2008 R2 ou posterior 
Criar cópias de segurança com o agente MARS | [Suportado](backup-support-matrix-mars-agent.md#support-for-direct-backups) sistemas operativos.
Criar cópias de segurança por meio do DPM/MABS | Sistemas operativos suportados para a cópia de segurança com [MABS](backup-mabs-protection-matrix.md) e [DPM](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807).

## <a name="support-for-linux-backup"></a>Suporte para cópia de segurança do Linux

Eis o que é suportado se quiser fazer uma cópia de segurança de máquinas do Linux.

**Ação** | **Suporte**
--- | --- 
Fazer cópias de segurança de VMs do Linux do Azure com o agente de VM do Linux do Azure | Cópia de segurança consistente de ficheiros.<br/><br/> A utilizar a cópia de segurança consistente com a aplicação [scripts personalizados](backup-azure-linux-app-consistent.md).<br/><br/> Durante o restauro pode criar uma nova VM, restaurar um disco e usá-lo para criar uma VM, ou restaurar um disco e utilizá-lo para substituir um disco numa VM existente. Também pode restaurar ficheiros e pastas individuais.
Fazer cópias de segurança de VMs do Linux do Azure com o agente MARS | Não suportado.<br/><br/> O agente de MARS só pode ser instalado em máquinas do Windows.
Fazer cópias de segurança de VMs do Linux do Azure com o DPM/MABS | Não suportado.

## <a name="operating-system-support-linux"></a>Suporte do sistema operativo (Linux)

Para cópias de segurança do Linux de VM do Azure, o Azure Backup suporta a lista de Linux [distribuições apoiadas pelo Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros). Tenha em atenção que:

- O Azure Backup não suporta Core OS Linux.
- O Azure Backup não suporta sistemas operativos de 32 bits.
- Outras distribuições do Linux bring-your-own podem funcionar, desde o [agente de VM do Azure para Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) está disponível na VM, e o Python seja suportado.



## <a name="backup-frequency-and-retention"></a>Frequência de cópia de segurança e retenção

**Definição** | **Limites** 
--- | --- 
Pontos de recuperação máximo por instância protegida (máquina/carga de trabalho | 9999
Tempo de expiração máximo para um ponto de recuperação | Sem limite
Frequência de cópia de segurança máxima para o cofre (extensão de VM do Azure) | Uma vez por dia
Frequência de cópia de segurança máxima para o cofre (agente de MARS) | Três cópias de segurança por dia.
Frequência de cópia de segurança máxima para o DPM/MABS | A cada 15 minutos para o SQL Server<br/><br/> Uma vez a uma hora para outras cargas de trabalho.
Retenção do ponto de recuperação | Diariamente, semanalmente, mensalmente, anualmente.
Período de retenção máximo | Depende da frequência de cópia de segurança.
Pontos de recuperação no disco do DPM/MABS | 64 para servidores de ficheiros, 448 para servidores de aplicações.<br/><br/> Pontos de recuperação de banda são ilimitados para o DPM no local.


## <a name="supported-restore-methods"></a>Métodos de restauro suportados

**Método Restore** | **Detalhes**
--- | ---
**Criar uma nova VM** | Pode criar uma VM durante o processo de restauração. <br/><br/> Esta opção obtém uma VM básica em execução. Pode especificar o VM nome, grupo de recursos, rede virtual, sub-rede e armazenamento.  
**Restaurar um disco** | Pode restaurar um disco e utilizá-lo para criar uma VM<br/><br/> Quando seleciona esta opção, cópia de segurança do Azure copia dados do cofre para uma conta de armazenamento que selecionar. A tarefa de restauro gera um modelo. Pode transferir este modelo e utilizá-la para especificar definições de VM personalizadas e criar uma VM.<br/><br/> Esta opção permite-lhe especificar as definições mais que a opção anterior para criar uma VM.<br/><br/>
**Substituir um disco existente** | Pode restaurar um disco e, em seguida, utilizar o disco restaurado para substituir um disco que está atualmente numa VM.
**Restaurar ficheiros** | Pode recuperar ficheiros a partir de um ponto de recuperação selecionado. Transferir um script para montar o disco VM a partir do ponto de recuperação. Em seguida, procure os volumes de disco para localizar as ficheiros/pastas que pretende recuperar e desmontar o disco quando tiver terminado.

## <a name="support-for-file-level-restore"></a>Suporte para o restauro ao nível do ficheiro

**Restaurar** | **Suportado**
--- | --- 
Restaurar ficheiros em sistemas operativos | Pode restaurar ficheiros em qualquer máquina que tenha o mesmo (ou compatível) o SO da VM de cópia de segurança. consulte a [tabela de sistema operacional compatível](backup-azure-restore-files-from-vm.md#system-requirements).
Restaurar ficheiros em VMs clássicas | Não suportado. 
Restaurar ficheiros a partir de VMs encriptadas | Não suportado.
Restaurar ficheiros a partir de contas de armazenamento restritas por rede | Não suportado.
Restaurar ficheiros em VMs através dos espaços de armazenamento do Windows | Restauro não são suportado na mesma VM.<br/><br/> Em vez disso, restaure os ficheiros numa VM compatível.
Restaurar ficheiros na VM do Linux com matrizes LVM/raid | Restauro não são suportado na mesma VM.<br/><br/> Restaure uma VM compatível.
Restaurar ficheiros com as definições de rede especiais | Restauro não são suportado na mesma VM. <br/><br/> Restaure uma VM compatível.

## <a name="support-for-vm-management"></a>Suporte para gerenciamento de VM

A tabela resume o suporte para cópia de segurança durante as tarefas de gestão da VM, como adição ou substituição de discos VM.

**Restaurar** | **Suportado** 
--- | --- 
Restaure em subscrição/região/zona. | Não suportado 
Restaurar para uma VM existente | Utilize a opção de disco de substituição.
Restaurar o disco com a conta de armazenamento ativada para encriptação de serviço de armazenamento do Azure (SSE) | Não suportado.<br/><br/> Restaure para uma conta que não tem o SSE ativado.
Restaurar para contas de armazenamento misto | Não suportado.<br/><br/> Com base no tipo de conta de armazenamento, todos os discos restaurados será premium ou standard e não mistos. 
Restaurar para a conta de armazenamento que utilizem ZRS | Não suportado
Restaurar VM diretamente para um conjunto de disponibilidade | Para discos geridos, pode restaurar o disco e utilize a opção de conjunto de disponibilidade no modelo.<br/><br/> Não é suportada para discos não geridos. Para discos não geridos, restaure o disco e, em seguida, crie uma VM no conjunto de disponibilidade.
Restaurar a cópia de segurança de VMs não geridas depois de atualizar geridas | Suportado.<br/><br/> Pode restaurar discos e, em seguida, crie uma VM gerida.
Restaurar a VM para o ponto de restauro antes da VM foi migrada para o managed disks | Suportado.<br/><br/> Restaurar para discos não geridos (predefinição), converter de discos restaurados para o disco gerido e criar uma VM com os discos geridos.
Restaure uma VM que é eliminada. | Suportado.<br/><br/> Pode restaurar a VM a partir de um ponto de recuperação.
Restaurar um controlador de domínio (DC) VM que faça parte de uma configuração de multi-DC através do portal | Suportado se restaurar o disco e criar uma VM com o PowerShell.
Restaurar a VM na VNet diferente |  Suportado.<br/><br/> A VNet tem de ser na mesma subscrição e região.




## <a name="vm-compute-support"></a>Suporte de computação VM

**Computação** | **Suporte** 
--- | --- 
Tamanho da VM |   Qualquer tamanho de VM do Azure com, pelo menos, 2 núcleos de CPU e 1 GB de RAM.<br/><br/> [Saiba mais](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) 
Cópia de segurança de VMs no [conjuntos de disponibilidade](https://docs.microsoft.com/azure/virtual-machines/windows/regions-and-availability#availability-sets) | Suportado.<br/><br/> Não é possível restaurar uma VM num conjunto de disponibilidade com a opção para criar rapidamente uma VM. Em vez disso, ao restaurar a VM terá de restaurar o disco e utilizá-lo para implementar uma VM, ou restaurar um disco e utilizá-lo para substituir um disco existente. 
Cópia de segurança de VMs no [zonas de disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-overview) |  Não suportado.  
Cópia de segurança de VMs implementadas com [benefício de utilização híbrida (HUB)](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) | Suportado.
Cópia de segurança de VMs implementadas num [conjunto de dimensionamento](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview) |   Não suportado   
Cópia de segurança de VMs implementadas a partir do [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?filters=virtual-machine-images)<br/><br/> (Publicado pela Microsoft, de terceiros) |   Suportado.<br/><br/> A VM tem de executar um sistema operativo suportado.<br/><br/> Quando a recuperação de arquivos na VM, pode restaurar apenas a um sistema de operacional compatível (não um SO anterior ou posterior).
Fazer uma cópia de segurança de VMs implementadas a partir de uma imagem personalizada (de terceiros) |    Suportado.<br/><br/> A VM tem de executar um sistema operativo suportado.<br/><br/> Quando a recuperação de arquivos na VM, pode restaurar apenas a um sistema de operacional compatível (não um SO anterior ou posterior).
Cópia de segurança de VMs migradas para o Azure   | Suportado.<br/><br/> Para fazer backup da VM, o agente da VM deve ser instalado na máquina migrada. 



## <a name="vm-storage-support"></a>Suporte de armazenamento VM

**Componente** | **Suporte**
--- | ---
Discos de dados VM do Azure | Fazer uma cópia de segurança de uma VM com discos de dados ou inferior a 16.
Tamanho do disco de dados | Disco individual pode ser até 4095 GB.<br/><br/> Se estiver a executar a versão mais recente da cópia de segurança de VM do Azure (conhecida como restaurar instantânea), tamanhos até 4TB de disco são suportadas. [Saiba mais](backup-instant-restore-capability.md).
Tipo de armazenamento | Premium HDD, standard SSD, SSD Standard <br/><br/> Standard SSD é suportada se estiver a executar a versão mais recente da cópia de segurança de VM do Azure (conhecida como restaurar instantânea), standard SSD é suportada. [Saiba mais](backup-instant-restore-capability.md).
Managed disks | Suportadas
Discos encriptados | Suportado.<br/><br/> VMs do Azure ativadas com encriptação de disco do Azure (ADE) podem ser uma cópia de segurança (com ou sem a aplicação do Azure AD).<br/><br/> VMs encriptadas não não possível recuperar ao nível do ficheiro/pasta. Precisa recuperar toda a VM.<br/><br/> Pode ativar a encriptação em VMs que já estão protegidas pelo Azure Backup.
Discos com acelerador de escrita ativados | Não suportado.<br/><br/> Se estiver a executar a versão mais recente da cópia de segurança de VM do Azure (conhecido como [restaurar instantâneas](backup-instant-restore-capability.md)), pode excluir discos com acelerador de escrita ativado a partir de cópia de segurança.
Criar cópias de segurança com eliminação de duplicados discos | Não suportado.
Adicionar disco à VM protegida | Suportado.
Redimensionar disco numa VM protegida | Suportado.

## <a name="vm-network-support"></a>Suporte de rede VM


**Componente** | **Suporte**
--- | ---
Número de NICs | Número máximo de NICs suportado para um tamanho de VM do Azure específico.<br/><br/> NICs são criados quando a VM é criada durante o processo de restauração.<br/><br/> O número de NICs à VM restaurada Espelha o número de NICs na VM quando ativou a proteção. Remover NICs após a ativação não afeta a contagem.
Balanceador de carga externo/interno |   Suportado. <br/></br> [Saiba mais](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations) sobre o restauro de VMs com definições de rede especiais.
Vários endereços IP reservados |  Suportado. <br/></br> [Saiba mais](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations) sobre o restauro de VMs com definições de rede especiais.
VMs com várias placas de rede  | Suportado. <br/></br> [Saiba mais](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations) sobre o restauro de VMs com definições de rede especiais.
VMs com endereços IP públicos    | Suportado.<br/><br/> Tem de associar um endereço IP público existente com a NIC, ou criar um endereço e associe-ao NIC com depois de efetuar um restauro.
Grupo de segurança de rede (NSG) na sub-rede/NIC. |   Suportado.
Endereço IP reservado (estático) | Não suportado.<br/><br/> Não pode criar uma VM com um endereço IP reservado e nenhum ponto de extremidade definido.
Endereço IP dinâmico |    Suportado.<br/><br/> Se a NIC na origem da VM utiliza endereçamento IP dinâmico, por predefinição o NIC à VM restaurada também compartilharão.
Gestor de Tráfego | Suportadas<br/><br/> Se a VM de cópia de segurança estiver no Gestor de tráfego, terá de adicionar manualmente a VM restaurada para o Gestor de tráfego mesmo. 
DNS do Azure | Suportado.
DNS Personalizado |    Suportado.
Conectividade de saída através do proxy de HTTP | Suportado.<br/><br/> Não é suportado um proxy autenticado. 
Pontos finais de serviço de VNet  | Suportado.<br/><br/> Definições de conta de armazenamento de VNet e de firewall devem permitir acesso de todas as redes. 



## <a name="vm-securityencryption-support"></a>Suporte de segurança/encriptação de VM

O Azure Backup suporta a encriptação para dados em trânsito e em repouso:

Tráfego de rede para o Azure:
- Tráfego de cópia de segurança dos servidores para o Cofre dos serviços de recuperação é encriptado com Advanced Encryption Standard 256.
- Dados de cópia de segurança são enviados através de uma ligação HTTPS segura.
- Os dados de cópia de segurança são armazenados no cofre dos serviços de recuperação no formato encriptado.
- O único a ter a frase de acesso para desbloquear estes dados. Microsoft não é possível desencriptar os dados de cópia de segurança em momento algum.
    > [!WARNING]
    > Depois de configurar o cofre, apenas tem acesso à chave de encriptação. A Microsoft nunca mantém uma cópia e não tem acesso à chave. Se a chave está no local incorreto, a Microsoft não é possível recuperar os dados de cópia de segurança.
Segurança de dados:
- Ao fazer backup de VMs do Azure tem de configurar a encriptação *dentro de* a máquina virtual.
- O Azure Backup suporta o Azure Disk Encryption, que utiliza o BitLocker nas máquinas virtuais do Windows e **dm-crypt** nas máquinas virtuais do Linux.
- No back-end, o Azure Backup utiliza a [encriptação do Serviço de Armazenamento do Azure](../storage/common/storage-service-encryption.md), que protege os dados inativos.


**Machine** | **Em trânsito** | **Em repouso**
--- | --- | ---
Máquinas do Windows sem DPM/MABS no local | ![Sim][green] | ![Sim][green]
VMs do Azure | ![Sim][green] | ![Sim][green] 
VMs no local/Azure com o DPM | ![Sim][green] | ![Sim][green]
VMs no local/Azure com o MABS | ![Sim][green] | ![Sim][green]



## <a name="vm-compression-support"></a>Suporte de compressão de VM

Cópia de segurança suporta a compactação do tráfego de cópia de segurança, conforme resumido na tabela abaixo. Tenha em atenção que:

- Para VMs do Azure, a extensão da VM lê os dados diretamente a partir da conta de armazenamento do Azure através da rede de armazenamento, pelo que não é necessário comprimir este tráfego.
- Se estiver a utilizar o DPM ou MABS, é possível compactar os dados antes de ele cópia de segurança para DPM/MABS, para poupar largura de banda. 

**Machine** | **Comprimir para o MABS/DPM (TCP)** | **Comprimir (HTTPS) para o Cofre**
--- | --- | ---
Máquinas do Windows sem DPM/MABS no local | ND | Sim
VMs do Azure | ND | ND
VMs no local/Azure com o DPM | ![Sim][green] | ![Sim][green]
VMs no local/Azure com o MABS | ![Sim][green] | ![Sim][green]




## <a name="next-steps"></a>Passos Seguintes

- [Fazer cópia de segurança de VMs do Azure](backup-azure-arm-vms-prepare.md)
- [Cópia de segurança diretamente a máquinas Windows](tutorial-backup-windows-server-to-azure.md), sem um servidor de cópia de segurança.
- [Configurar o MABS](backup-azure-microsoft-azure-backup.md) para cópia de segurança para o Azure e, em seguida, de cópia de segurança de cargas de trabalho para o MABS.
- [Configurar o DPM](backup-azure-dpm-introduction.md) para cópia de segurança para o Azure e, em seguida, de cópia de segurança de cargas de trabalho para o DPM.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png


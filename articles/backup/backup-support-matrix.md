---
title: Matriz de suporte do Azure Backup
description: Fornece um resumo das definições de suporte e limitações para o serviço de backup Azure.
ms.topic: conceptual
ms.date: 02/17/2019
ms.openlocfilehash: 788da5708178cfa533d4382a4a417cef73f46a5e
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78254912"
---
# <a name="support-matrix-for-azure-backup"></a>Matriz de suporte para Backup Azure

Pode utilizar o [Azure Backup](backup-overview.md) para fazer backup de dados na plataforma cloud do Microsoft Azure. Este artigo resume as definições e limitações gerais de suporte para cenários e implementações de Backup Azure.

Estão disponíveis outras matrizes de suporte:

- Matriz de suporte para cópia de segurança da [máquina virtual Azure (VM)](backup-support-matrix-iaas.md)
- Matriz de suporte para backup utilizando [system center Data Protection Manager (DPM)/Microsoft Azure Backup Server (MABS)](backup-support-matrix-mabs-dpm.md)
- Matriz de suporte para backup utilizando o [agente Microsoft Azure Recovery Services (MARS)](backup-support-matrix-mars-agent.md)

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="vault-support"></a>Suporte do cofre

O Azure Backup usa cofres dos Serviços de Recuperação para orquestrar e gerir backups. Também usa cofres para armazenar dados de reserva.

A tabela seguinte descreve as características dos cofres dos Serviços de Recuperação:

**Funcionalidade** | **Detalhes**
--- | ---
**Cofres em assinatura** | Até 500 cofres dos Serviços de Recuperação numa única subscrição.
**Máquinas em um cofre** | Até 1.000 VMs Azure num único cofre.<br/><br/> Até 50 servidores MABS podem ser registados num único cofre.
**Fontes de dados** | O tamanho máximo de uma fonte de [dados](https://docs.microsoft.com/azure/backup/backup-azure-backup-faq#how-is-the-data-source-size-determined) individual é de 54.400 GB. Este limite não se aplica às cópias de segurança da Azure VM. Não se aplicam limites à quantidade total de dados que pode supor até ao cofre.
**Backups para o cofre** | **VMs azure:** Uma vez por dia.<br/><br/>**Máquinas protegidas por DPM/MABS:** Duas vezes por dia.<br/><br/> **As máquinas ativadas diretamente utilizando o agente MARS:** Três vezes por dia.
**Backups entre cofres** | O reforço está dentro de uma região.<br/><br/> Precisa de um cofre em todas as regiões de Azure que contenha VMs que queira apoiar. Não se pode recuar para uma região diferente.
**Mover cofres** | Pode [mover cofres](https://docs.microsoft.com/azure/backup/backup-azure-move-recovery-services-vault) através de subscrições ou entre grupos de recursos na mesma subscrição. No entanto, a movimentação de cofres em todas as regiões não é apoiada.
**Mover dados entre cofres** | Mover dados de apoio entre cofres não é suportado.
**Modificar o tipo de armazenamento do cofre** | Pode modificar o tipo de replicação de armazenamento (armazenamento geo-redundante ou armazenamento localmente redundante) para um cofre antes de armazenar backups. Depois de as cópias de segurança começarem no cofre, o tipo de replicação não pode ser modificado.

## <a name="on-premises-backup-support"></a>Apoio de apoio ao backup no local

Aqui está o que é suportado se quiser apoiar as máquinas no local:

**Máquina** | **O que está apoiado** | **Localização** | **Funcionalidades**
--- | --- | --- | ---
**Backup direto da máquina windows com agente MARS** | Ficheiros, pastas, estado do sistema | De volta ao cofre dos Serviços de Recuperação. | Recua três vezes por dia.<br/><br/> Sem backup consciente de aplicativo<br/><br/> Restaurar ficheiro, pasta, volume
**Backup direto da máquina Linux com agente MARS** | Backup não suportado
**Back up to DPM** | Ficheiros, pastas, volumes, estado do sistema, dados da aplicação | Volte para o armazém local do DPM. DPM então recua para o cofre. | Instantâneos conscientes de aplicativos<br/><br/> Granularidade total para backup e recuperação<br/><br/> Linux suportado para VMs (Hyper-V/VMware)<br/><br/> Oráculo não apoiado
**Back up to MABS** | Ficheiros, pastas, volumes, estado do sistema, dados da aplicação | Volte para o armazenamento local mABS. A MABS volta para o cofre. | Instantâneos conscientes de aplicativos<br/><br/> Granularidade total para backup e recuperação<br/><br/> Linux suportado para VMs (Hyper-V/VMware)<br/><br/> Oráculo não apoiado

## <a name="azure-vm-backup-support"></a>Suporte de backup Azure VM

### <a name="azure-vm-limits"></a>Limites de VM Azure

**Limite** | **Detalhes**
--- | ---
**Discos de dados Azure VM** | Limite de 16 <br> Para se registar para obter a pré-visualização privada de VMs com mais de 16 discos (até 32 discos), contacte-nos através de AskAzureBackupTeam@microsoft.com
**Tamanho do disco de dados Azure VM** | O tamanho do disco individual pode ser até 32 TB e um máximo de 256 TB combinado para todos os discos de um VM.

### <a name="azure-vm-backup-options"></a>Opções de backup Azure VM

Aqui está o que é suportado se quiser apoiar os VMs Azure:

**Máquina** | **O que está apoiado** | **Localização** | **Funcionalidades**
--- | --- | --- | ---
**Backup Azure VM utilizando extensão VM** | VM inteiro | De volta ao cofre. | Extensão instalada quando ativa a cópia de segurança para um VM.<br/><br/> Recua uma vez por dia.<br/><br/> Backup de aplicações para VMs do Windows; cópia de segurança consistente para VMs Linux. Pode configurar a consistência das aplicações para as máquinas Linux utilizando scripts personalizados.<br/><br/> Restaurar vM ou disco.<br/><br/> Não se pode apoiar um VM Azure para um local no local.
**Backup Azure VM utilizando agente MARS** | Ficheiros, pastas, estado do sistema | De volta ao cofre. | Recua três vezes por dia.<br/><br/> Se pretender fazer o back-up de ficheiros ou pastas específicas em vez de todo o VM, o agente MARS pode correr ao lado da extensão VM.
**Azure VM com DPM** | Ficheiros, pastas, volumes, estado do sistema, dados da aplicação | De volta ao armazenamento local de Azure VM que está a executar DPM. DPM então recua para o cofre. | Fotos conscientes de aplicativos.<br/><br/> Granularidade total para backup e recuperação.<br/><br/> Linux suportado para VMs (Hyper-V/VMware).<br/><br/> Oráculo não apoiado.
**Azure VM com MABS** | Ficheiros, pastas, volumes, estado do sistema, dados da aplicação | De volta ao armazenamento local de Azure VM que está a executar MABS. A MABS volta para o cofre. | Fotos conscientes de aplicativos.<br/><br/> Granularidade total para backup e recuperação.<br/><br/> Linux suportado para VMs (Hyper-V/VMware).<br/><br/> Oráculo não apoiado.

## <a name="linux-backup-support"></a>Suporte de apoio linux

Aqui está o que é suportado se quiser apoiar as máquinas Linux:

**Tipo de cópia de segurança** | **Linux (Azure endossado)**
--- | ---
**Backup direto da máquina no local que está a executar linux** | Não suportado. O agente MARS só pode ser instalado em máquinas Windows.
**Usando a extensão do agente para apoiar o Azure VM que está a executar o Linux** | Backup consistente com aplicativos utilizando [scripts personalizados.](backup-azure-linux-app-consistent.md)<br/><br/> Recuperação ao nível dos ficheiros.<br/><br/> Restaurar criando um VM a partir de um ponto de recuperação ou disco.
**Usando o DPM para apoiar as máquinas no local que executam o Linux** | Backup consistente com ficheiros de VMs de Hóspedes Linux em Hyper-V e VMWare.<br/><br/> Restauração VM de V-V e VMWare Linux Guest VMs.
**Usando MABS para apoiar máquinas no local que executam Linux** | Backup consistente com ficheiros de VMs de Hóspedes Linux em Hyper-V e VMWare.<br/><br/> Restauração VM de V-V e VMWare Linux convidado VMs.
**Utilização de MABS ou DPM para apoiar Os VMs Linux Azure** | Não suportado.

## <a name="daylight-saving-time-support"></a>Suporte horário de verão

O Azure Backup não suporta o ajuste automático do relógio para o horário de verão para cópias de segurança Do VM Azure. Modifique manualmente as políticas de backup conforme necessário.

## <a name="disk-deduplication-support"></a>Suporte de desduplicação de disco

O suporte de desduplicação do disco é o seguinte:

- A duplicação do disco é suportada no local quando utiliza DPM ou MABs para fazer cópias de hiper-V V V que estão a executar windows. O Windows Server realiza a desduplicação de dados (ao nível do anfitrião) em discos rígidos virtuais (VHDs) que estão ligados ao VM como armazenamento de cópia de segurança.
- A duplicação não é suportada em Azure para qualquer componente de backup. Quando o DPM e o MABS são implantados em Azure, os discos de armazenamento ligados ao VM não podem ser duplicados.

## <a name="security-and-encryption-support"></a>Suporte de segurança e encriptação

O Azure Backup suporta encriptação para dados em trânsito e em repouso.

### <a name="network-traffic-to-azure"></a>Tráfego de rede para Azure

- O tráfego de backup dos servidores para o cofre dos Serviços de Recuperação é encriptado utilizando o Advanced Encryption Standard 256.
- Os dados de cópia de segurança são enviados sobre um link HTTPS seguro.
- Os dados de backup são armazenados no cofre dos Serviços de Recuperação de forma encriptada.
- Só tem a frase-passe para desbloquear estes dados. A Microsoft não pode desencriptar os dados de cópia de segurança em nenhum momento.

    > [!WARNING]
    > Depois de montar o cofre, só você tem acesso à chave de encriptação. A Microsoft nunca mantém uma cópia e não tem acesso à chave. Se a chave estiver deslocada, a Microsoft não pode recuperar os dados de backup.

### <a name="data-security"></a>Segurança de dados

- Quando estiver estoirando Os VMs Azure, precisa de configurar a encriptação *dentro* da máquina virtual.
- O Azure Backup suporta o Azure Disk Encryption, que utiliza o BitLocker nas máquinas virtuais do Windows e **dm-crypt** nas máquinas virtuais do Linux.
- Na parte de trás, o Azure Backup utiliza a Encriptação do Serviço de [Armazenamento Azure,](../storage/common/storage-service-encryption.md)que protege os dados em repouso.

**Máquina** | **Em trânsito** | **Em repouso**
--- | --- | ---
**No local máquinas Windows sem DPM/MABS** | ![Sim][green] | ![Sim][green]
**Azure VMs** | ![Sim][green] | ![Sim][green]
**No local máquinas Windows ou VMs Azure com DPM** | ![Sim][green] | ![Sim][green]
**No local máquinas Windows ou VMs Azure com MABS** | ![Sim][green] | ![Sim][green]

## <a name="compression-support"></a>Suporte de compressão

O backup suporta a compressão do tráfego de reserva, tal como resumido na tabela seguinte.

- Para os VMs Azure, a extensão VM lê os dados diretamente da conta de armazenamento Azure sobre a rede de armazenamento, pelo que não é necessário comprimir este tráfego.
- Se estiver a utilizar DPM ou MABS, pode poupar largura de banda comprimindo os dados antes de ser apoiado.

**Máquina** | **Compressa para MABS/DPM (TCP)** | **Compressa ao cofre (HTTPS)**
--- | --- | ---
**Backup direto das máquinas Windows no local** | ND | ![Sim][green]
**Backup de VMs Azure utilizando extensão VM** | ND | ND
**Backup nas máquinas no local/Azure utilizando MABS/DPM** | ![Sim][green] | ![Sim][green]

## <a name="retention-limits"></a>Limites de retenção

**Definição** | **Limites**
--- | ---
**Pontos de recuperação máximos por instância protegida (máquina ou carga de trabalho)** | 9,999
**Tempo de validade máximo para um ponto de recuperação** | Sem limite
**Frequência máxima de backup para DPM/MABS** | A cada 15 minutos para o SQL Server<br/><br/> Uma vez por hora para outras cargas de trabalho
**Frequência máxima de reserva para o cofre** | **No local, máquinas Windows ou VMs Azure executando MARS:** Três por dia<br/><br/> **DPM/MABS:** Dois por dia<br/><br/> **Backup Azure VM:** Um por dia
**Retenção de pontos de recuperação** | Diariamente, semanalmente, mensalmente, anualmente
**Período máximo de retenção** | Depende da frequência da cópia de segurança
**Pontos de recuperação no disco DPM/MABS** | 64 para servidores de ficheiros; 448 para servidores de aplicações <br/><br/>Pontos de recuperação ilimitados de fitas para dPM no local

## <a name="cross-region-restore"></a>Restauro da região transversal

O Azure Backup adicionou a funcionalidade Cross Region Restore para reforçar a disponibilidade de dados e a capacidade de resiliência, dando aos clientes o controlo total para restaurar os dados numa região secundária. Para configurar esta funcionalidade, visite [o artigo set Cross Region Restore. .](backup-create-rs-vault.md#set-cross-region-restore). Esta funcionalidade é suportada para os seguintes tipos de gestão:

| Tipo de Gestão de Backup | Suportado                                                    | Regiões Apoiadas |
| ---------------------- | ------------------------------------------------------------ | ----------------- |
| VM do Azure               | Sim. Pré-visualização limitada pública Suportada para VMs e VMs encriptados com discos inferiores a 4 TB | E.U.A. Centro-Oeste   |
| Mars Agent/Nas instalações | Não                                                           | N/D               |
| SQL /SAP HANA          | Não                                                           | N/D               |
| AFS                    | Não                                                           | N/D               |

## <a name="next-steps"></a>Passos seguintes

- [Reveja a matriz](backup-support-matrix-iaas.md) de suporte para backup Azure VM.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png

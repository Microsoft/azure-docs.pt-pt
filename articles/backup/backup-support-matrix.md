---
title: Matriz de suporte do Azure Backup
description: Fornece um resumo das definições e limitações de suporte para o serviço de backup Azure.
ms.topic: conceptual
ms.date: 02/17/2019
ms.custom: references_regions
ms.openlocfilehash: 2877b0085f31f39849e04678d5e0ddd65e3888e2
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105564471"
---
# <a name="support-matrix-for-azure-backup"></a>Matriz de suporte para backup Azure

Pode utilizar [o Azure Backup](backup-overview.md) para fazer cópia de segurança para fazer cópias de dados na plataforma cloud do Microsoft Azure. Este artigo resume as configurações e limitações gerais de suporte para cenários e implementações de Backup Azure.

Outras matrizes de suporte estão disponíveis:

- Matriz de suporte para [a máquina virtual Azure (VM)](backup-support-matrix-iaas.md)
- Matriz de suporte para cópia de segurança utilizando [o Gestor de Proteção de Dados do Centro de Sistema (DPM)/Microsoft Azure Backup Server (MABS)](backup-support-matrix-mabs-dpm.md)
- Matriz de suporte para cópia de segurança utilizando o [agente Microsoft Azure Recovery Services (MARS)](backup-support-matrix-mars-agent.md)

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="vault-support"></a>Suporte de abóbada

A Azure Backup utiliza cofres de Serviços de Recuperação para orquestrar e gerir backups para os seguintes tipos de carga de trabalho - Azure VMs, SQL in Azure VMs, SAP HANA em VMs Azure, Azure File shares e cargas de trabalho no local usando Azure Backup Agent, Azure Backup Server e System Center DPM. Também utiliza cofres dos Serviços de Recuperação para armazenar dados de reserva para estas cargas de trabalho.

A tabela a seguir descreve as características dos cofres dos Serviços de Recuperação:

**Funcionalidade** | **Detalhes**
--- | ---
**Cofres em subscrição** | Até 500 cofres dos Serviços de Recuperação numa única subscrição.
**Máquinas em um cofre** | Até 2000 fontes de dados em todas as cargas de trabalho (como VMs Azure, SQL Server VM, MABS Servers, e assim por diante) podem ser protegidas num único cofre.<br><br>Até 1.000 VMs Azure num único cofre.<br/><br/> Até 50 servidores MABS podem ser registados num único cofre.
**Origens de dados** | O tamanho máximo de uma fonte de [dados](./backup-azure-backup-faq.md#how-is-the-data-source-size-determined) individual é de 54.400 GB. Este limite não se aplica às cópias de segurança Azure VM. Não se aplicam limites à quantidade total de dados que podes fazer até ao cofre.
**Backups para o cofre** | **VMs Azure:** Uma vez por dia.<br/><br/>**Máquinas protegidas por DPM/MABS:** Duas vezes por dia.<br/><br/> **Máquinas apoiadas diretamente utilizando o agente MARS:** Três vezes por dia.
**Backups entre cofres** | O reforço está dentro de uma região.<br/><br/> Precisa de um cofre em todas as regiões do Azure que contenham VMs que queira apoiar. Não pode voltar para outra região.
**Mover cofres** | Pode [mover cofres](./backup-azure-move-recovery-services-vault.md) através de subscrições ou entre grupos de recursos na mesma subscrição. No entanto, mover cofres através de regiões não é suportado.
**Mover dados entre abóbadas** | Mover dados de apoio entre cofres não é suportado.
**Modificar o tipo de armazenamento do cofre** | Pode modificar o tipo de replicação de armazenamento (armazenamento geo-redundante ou armazenamento localmente redundante) para um cofre antes de serem armazenadas cópias de segurança. Depois de começarem as cópias de segurança no cofre, o tipo de replicação não pode ser modificado.
**Armazenamento com redundância entre zonas (ZRS)** | Disponível nas regiões do Reino Unido Sul (UKS) e Sudeste Asiático (SEA).
**Pontos finais privados** | Consulte [esta secção](./private-endpoints.md#before-you-start) para obter requisitos para criar pontos finais privados para um cofre de serviço de recuperação.  

## <a name="on-premises-backup-support"></a>Suporte de reserva no local

Aqui está o que é suportado se você quiser apoiar as máquinas no local:

**Máquina** | **O que está apoiado** | **Localização** | **Funcionalidades**
--- | --- | --- | ---
**Backup direto da máquina do Windows com agente MARS** | Ficheiros, pastas, estado do sistema | De volta ao cofre dos Serviços de Recuperação. | Recuar três vezes por dia<br/><br/> Sem cópia de segurança consciente da aplicação<br/><br/> Restaurar ficheiro, pasta, volume
**Backup direto da máquina Linux com agente MARS** | Backup não suportado
**Voltar para o DPM** | Ficheiros, pastas, volumes, estado do sistema, dados de aplicações | Volte ao armazenamento local de DPM. DPM, em seguida, recua para o cofre. | Instantâneos conscientes de aplicativos<br/><br/> Granularidade total para apoio e recuperação<br/><br/> Linux suportado para VMs (Hiper-V/VMware)<br/><br/> Oráculo não apoiado
**Voltar para o MABS** | Ficheiros, pastas, volumes, estado do sistema, dados de aplicações | Volte para o armazenamento local da MABS. MABS, em seguida, volta para o cofre. | Instantâneos conscientes de aplicativos<br/><br/> Granularidade total para apoio e recuperação<br/><br/> Linux suportado para VMs (Hiper-V/VMware)<br/><br/> Oráculo não apoiado

## <a name="azure-vm-backup-support"></a>Suporte de backup Azure VM

### <a name="azure-vm-limits"></a>Limites Azure VM

**Limite** | **Detalhes**
--- | ---
**Discos de dados Azure VM** | Consulte a [matriz de suporte para a cópia de segurança Azure VM](./backup-support-matrix-iaas.md#vm-storage-support).
**Tamanho do disco de dados Azure VM** | O tamanho do disco individual pode ser até 32 TB e um máximo de 256 TB combinados para todos os discos num VM.

### <a name="azure-vm-backup-options"></a>Opções de backup Azure VM

Aqui está o que é suportado se você quiser apoiar VMs Azure:

**Máquina** | **O que está apoiado** | **Localização** | **Funcionalidades**
--- | --- | --- | ---
**Backup Azure VM usando extensão VM** | VM inteiro | De volta ao cofre. | Extensão instalada quando ativa a cópia de segurança para um VM.<br/><br/> Recua uma vez por dia.<br/><br/> Cópia de segurança consciente da aplicação para VMs do Windows; cópia de segurança consistente com ficheiros para Os VMs Do Linux. Pode configurar a consistência da aplicação para máquinas Linux utilizando scripts personalizados.<br/><br/> Restaurar VM ou disco.<br/><br/>[A cópia de segurança e a restauração dos controladores de domínio](active-directory-backup-restore.md) do Ative Directory são suportadas.<br><br> Não posso apoiar um Azure VM para um local no local.
**Backup Azure VM usando agente MARS** | Ficheiros, pastas, estado do sistema | De volta ao cofre. | Recua três vezes por dia.<br/><br/> Se pretender fazer o back up ficheiros ou pastas específicos em vez de todo o VM, o agente MARS pode funcionar ao lado da extensão VM.
**Azure VM com DPM** | Ficheiros, pastas, volumes, estado do sistema, dados de aplicações | Volte ao armazenamento local da Azure VM que está a executar DPM. DPM, em seguida, recua para o cofre. | Fotos conscientes da aplicação.<br/><br/> Granularidade total para apoio e recuperação.<br/><br/> Linux suportado para VMs (Hiper-V/VMware).<br/><br/> Oráculo não apoiado.
**Azure VM com MABS** | Ficheiros, pastas, volumes, estado do sistema, dados de aplicações | Volte ao armazenamento local da Azure VM que está a executar mABS. MABS, em seguida, volta para o cofre. | Fotos conscientes da aplicação.<br/><br/> Granularidade total para apoio e recuperação.<br/><br/> Linux suportado para VMs (Hiper-V/VMware).<br/><br/> Oráculo não apoiado.

## <a name="linux-backup-support"></a>Suporte de reserva Linux

Aqui está o que é suportado se quiser apoiar as máquinas Linux:

**Tipo de backup** | **Linux (Azure endossado)**
--- | ---
**Backup direto da máquina de acesso ao local que está a executar o Linux** | Não suportado. O agente MARS só pode ser instalado em máquinas Windows.
**Usando a extensão do agente para apoiar o Azure VM que está a executar o Linux** | Backup consistente com aplicações utilizando [scripts personalizados.](backup-azure-linux-app-consistent.md)<br/><br/> Recuperação ao nível do ficheiro.<br/><br/> Restaurar criando um VM a partir de um ponto de recuperação ou disco.
**Usando o DPM para apoiar máquinas no local que executam o Linux** | Cópia de segurança consistente de ficheiros de VMs de hóspedes Linux em Hyper-V e VMware.<br/><br/> Restauração VM de VMware Linux Guest VMs.
**Utilização de MABS para apoiar máquinas no local que executam o Linux** | Cópia de segurança consistente de ficheiros de VMs de hóspedes Linux em Hyper-V e VMware.<br/><br/> VM restauração de VM e VMware Linux convidados VMS.
**Utilização de MABS ou DPM para apoiar os VMs Linux Azure** | Não suportado.

## <a name="daylight-saving-time-support"></a>Suporte de horário de verão

A Azure Backup não suporta o ajuste automático do relógio para o horário de verão para as cópias de segurança Azure VM. Não muda a hora do apoio para a frente ou para trás. Para garantir que a cópia de segurança é executado no momento pretendido, modifique as políticas de backup manualmente, conforme necessário.

## <a name="disk-deduplication-support"></a>Suporte à desduplicação de disco

O suporte à desduplicação de disco é o seguinte:

- A desduplicação do disco é suportada no local quando utiliza DPM ou MABS para fazer o back up Hyper-VMs que estão a executar o Windows. O Windows Server executa a deduplicação de dados (ao nível do anfitrião) em discos rígidos virtuais (VHDs) que estão ligados ao VM como armazenamento de backup.
- A deduplica não é suportada no Azure para qualquer componente de reserva. Quando o DPM e o MABS são implantados em Azure, os discos de armazenamento ligados ao VM não podem ser desduplicados.

## <a name="security-and-encryption-support"></a>Suporte à segurança e encriptação

O Azure Backup suporta encriptação para dados em trânsito e em repouso.

### <a name="network-traffic-to-azure"></a>Tráfego de rede para Azure

- O tráfego de backup dos servidores para o cofre dos Serviços de Recuperação é encriptado utilizando o Padrão de Encriptação Avançada 256.
- Os dados de cópia de segurança são enviados por um link HTTPS seguro.

### <a name="data-security"></a>Segurança de dados

- Os dados de backup são armazenados no cofre dos Serviços de Recuperação sob forma encriptada.
- Quando os dados são cópias de segurança dos servidores no local com o agente MARS, os dados são encriptados com uma palavra-passe antes do upload para Azure Backup e desencriptados apenas depois de serem descarregados a partir do Azure Backup.
- Quando estiver a fazer backup de VMs Azure, tem de configurar encriptação *dentro* da máquina virtual.
- O Azure Backup suporta o Azure Disk Encryption, que utiliza o BitLocker nas máquinas virtuais do Windows e **dm-crypt** nas máquinas virtuais do Linux.
- Na parte traseira, o Azure Backup utiliza [encriptação do serviço de armazenamento Azure](../storage/common/storage-service-encryption.md), que protege os dados em repouso.

**Máquina** | **Em trânsito** | **Em repouso**
--- | --- | ---
**Máquinas Windows no local sem DPM/MABS** | ![Yes][green] | ![Yes][green]
**VMs do Azure** | ![Yes][green] | ![Yes][green]
**Máquinas Windows no local ou VMs Azure com DPM** | ![Yes][green] | ![Yes][green]
**Máquinas Windows no local ou VMs Azure com MABS** | ![Yes][green] | ![Yes][green]

## <a name="compression-support"></a>Suporte à compressão

A cópia de segurança suporta a compressão do tráfego de backup, tal como resumida na tabela seguinte.

- Para os VMs Azure, a extensão VM lê os dados diretamente da conta de armazenamento Azure sobre a rede de armazenamento, pelo que não é necessário comprimir este tráfego.
- Se estiver a utilizar DPM ou MABS, pode poupar largura de banda comprimindo os dados antes de serem apoiados.

**Máquina** | **Comprimir para MABS/DPM (TCP)** | **Comprimir para cofre (HTTPS)**
--- | --- | ---
**Backup direto das máquinas Windows no local** | ND | ![Sim][green]
**Backup dos VMs Azure utilizando a extensão VM** | ND | ND
**Backup nas máquinas de Azure no local utilizando MABS/DPM** | ![Yes][green] | ![Yes][green]

## <a name="retention-limits"></a>Limites de retenção

**Definição** | **Limites**
--- | ---
**Pontos de recuperação máximos por instância protegida (máquina ou carga de trabalho)** | 9,999
**Prazo máximo de validade para um ponto de recuperação** | Sem limite
**Frequência máxima de backup para DPM/MABS** | A cada 15 minutos para o SQL Server<br/><br/> Uma vez por hora para outras cargas de trabalho
**Frequência máxima de backup para o cofre** | **Máquinas Windows no local ou VMs Azure em execução MARS:** Três por dia<br/><br/> **DPM/MABS:** Dois por dia<br/><br/> **Backup Azure VM:** Um por dia
**Retenção de pontos de recuperação** | Diariamente, semanalmente, mensalmente, anualmente
**Período de retenção máximo** | Depende da frequência da cópia de segurança
**Pontos de recuperação no disco DPM/MABS** | 64 para servidores de ficheiros; 448 para servidores de aplicações <br/><br/>Pontos de recuperação de fitas ilimitadas para DPM no local

## <a name="cross-region-restore"></a>Restauro da Região Transversal

O Azure Backup adicionou a funcionalidade Cross Region Restore para reforçar a disponibilidade de dados e a capacidade de resiliência, dando-lhe total controlo para restaurar os dados numa região secundária. Para configurar esta funcionalidade, visite [o artigo set Cross Region Restore.](backup-create-rs-vault.md#set-cross-region-restore) Esta funcionalidade é suportada para os seguintes tipos de gestão:

| Tipo de Gestão de Backup | Suportado                                                    | Regiões apoiadas |
| ---------------------- | ------------------------------------------------------------ | ----------------- |
| VM do Azure               | Suportado para VMs Azure com discos geridos e não geridos. Não suportado para VMs clássicos. | Disponível em todas as regiões públicas e regiões soberanas do Azure, com exceção da França Central, Austrália Central, África do Sul Norte, Emirados Árabes Unidos, Suíça Norte, Alemanha Central Ocidental, Noruega Oriental. <br>Para obter informações sobre a utilização nessas regiões, contacte [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) |
| SQL /SAP HANA | Em pré-visualização                                                      | Disponível em todas as regiões públicas e regiões soberanas do Azure, com exceção da França Central, Austrália Central, África do Sul Norte, Emirados Árabes Unidos, Suíça Norte, Alemanha Central Ocidental, Noruega Oriental. <br>Para obter informações sobre a utilização nessas regiões, contacte [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) |
| Agente MARS/Nas instalações  | No                                                           | N/D               |
| AFS (ações de ficheiros Azure)                 | No                                                           | N/D               |

## <a name="next-steps"></a>Passos seguintes

- [Rever matriz de suporte](backup-support-matrix-iaas.md) para backup Azure VM.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
---
title: Matriz de suporte do Azure Backup
description: Fornece um resumo das definições de suporte e limitações para o serviço de cópia de segurança do Azure.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/17/2019
ms.author: raynew
ms.openlocfilehash: 51bd4b935b32bea20d3f5de0b8cda62dfdbf07b8
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57898725"
---
# <a name="azure-backup-support-matrix"></a>Matriz de suporte do Azure Backup

Pode usar [Azure Backup](backup-overview.md) para criar cópias de segurança para a plataforma de cloud do Microsoft Azure. Este artigo resume as definições de suporte gerais e limitações para cenários de cópia de segurança do Azure e implementações.

Outras matrizes de suporte estão disponíveis:

- Matriz de suporte para [cópia de segurança da máquina virtual do Azure (VM)](backup-support-matrix-iaas.md)
- Matriz de suporte para cópia de segurança utilizando [System Center Data Protection Manager (DPM) / Microsoft Azure Backup Server (MABS)](backup-support-matrix-mabs-dpm.md)
- Matriz de suporte para cópia de segurança utilizando o [agente dos serviços de recuperação do Azure (MARS) da Microsoft](backup-support-matrix-mars-agent.md)

## <a name="vault-support"></a>Suporte de cofre

O Azure Backup utiliza os cofres dos serviços de recuperação para orquestrar e gerir cópias de segurança. Ele também usa cofres para armazenar dados de cópia de segurança. 

A tabela seguinte descreve as funcionalidades de cofres dos serviços de recuperação:

**Funcionalidade** | **Detalhes**
--- | ---
**Cofres de subscrição** | Até 500 cofres de serviços de recuperação numa única subscrição.
**Máquinas num cofre** | Até 1000 VMs do Azure num único cofre.<br/><br/> Até 50 MABS servidores podem ser registados num único cofre.
**Origens de dados no armazenamento do Cofre** | GB 54,400 máximo. Não existe nenhum limite para cópias de segurança de VM do Azure.
**Cópias de segurança para o Cofre** | **VMs do Azure:** Uma vez por dia.<br/><br/>**Máquinas protegidas pelo DPM/MABS:** Duas vezes por dia.<br/><br/> **Máquinas de cópia de segurança diretamente através do agente MARS:** Três vezes por dia. 
**Cópias de segurança entre cofres** | Cópia de segurança é dentro de uma região.<br/><br/> É necessário um cofre em cada região do Azure que contém as VMs que pretende criar cópias de segurança. Não é possível criar cópias de segurança para uma região diferente. 
**Cofres de movimentação** | Pode [mover cofres](https://review.docs.microsoft.com/azure/backup/backup-azure-move-recovery-services-vault) entre subscrições ou entre grupos de recursos na mesma subscrição.
**Mover dados entre cofres** | Mover dados de cópia de segurança entre cofres não é suportada.
**Modificar o tipo de armazenamento do Cofre** | Pode modificar o tipo de replicação de armazenamento (armazenamento georredundante ou armazenamento localmente redundante) para um cofre, antes de cópias de segurança são armazenadas. Depois de iniciar as cópias de segurança no cofre, não é possível modificar o tipo de replicação.

## <a name="on-premises-backup-support"></a>Suporte de cópia de segurança no local

Eis o que é suportado se quiser fazer uma cópia de segurança de máquinas no local:

**Machine** | **O que é uma cópia de segurança** | **Localização** | **Funcionalidades**
--- | --- | --- | ---
**Cópia de segurança direta da máquina do Windows com agente MARS** | Ficheiros, pastas, estado do sistema | Criar cópias de segurança para cofre dos serviços de recuperação. | Criar cópias de segurança três vezes por dia<br/><br/> Nenhuma cópia de segurança com suporte para a aplicação<br/><br/> Restaurar o ficheiro, pasta, volume
**Cópia de segurança direta da máquina do Linux com o agente MARS** | Cópia de segurança não suportada
**Cópia de segurança para DPM** | Ficheiros, pastas, volumes, o estado do sistema, os dados da aplicação | Criar cópias de segurança para o armazenamento do DPM local. O DPM, em seguida, efetua cópias de segurança para o cofre. | Instantâneos de deteção de aplicação<br/><br/> Granularidade de completa para cópia de segurança e recuperação<br/><br/> Linux suportada para VMs (Hyper-V/VMware)<br/><br/> Oracle não suportada
**Cópia de segurança para o MABS** | Ficheiros, pastas, volumes, o estado do sistema, os dados da aplicação | Criar cópias de segurança para o armazenamento local do MABS. MABS, em seguida, efetua cópias de segurança no cofre. | Instantâneos de deteção de aplicação<br/><br/> Granularidade de completa para cópia de segurança e recuperação<br/><br/> Linux suportada para VMs (Hyper-V/VMware)<br/><br/> Oracle não suportada

## <a name="azure-vm-backup-support"></a>Suporte de cópia de segurança de VM do Azure

### <a name="azure-vm-limits"></a>Limites VM do Azure

**Limite** | **Detalhes**
--- | ---
**Discos de dados VM do Azure** | Limite de 16
**Tamanho do disco de dados VM do Azure** | Discos individuais podem ser até 4095 GB

### <a name="azure-vm-backup-options"></a>Opções de cópia de segurança de VM do Azure

Eis o que é suportado se quiser fazer uma cópia de segurança de VMs do Azure:

**Machine** | **O que é uma cópia de segurança** | **Localização** | **Funcionalidades**
--- | --- | --- | ---
**Cópia de segurança VM do Azure ao utilizar a extensão de VM** | VM inteira | Criar cópias de segurança para o cofre. | Extensão instalada quando ativar a cópia de segurança para uma VM.<br/><br/> Criar cópias de segurança uma vez por dia.<br/><br/> Aplicação com suporte para cópia de segurança para VMs do Windows; cópia de segurança consistente com ficheiros para VMs do Linux. Pode configurar a consistência de aplicação para computadores Linux ao utilizar scripts personalizados.<br/><br/> Restaure VM ou disco.<br/><br/> Não é possível fazer backup de uma VM do Azure para uma localização no local.
**Cópia de segurança VM do Azure com o agente MARS** | Ficheiros, pastas | Criar cópias de segurança para o cofre. | Criar cópias de segurança três vezes por dia.<br/><br/> Se pretender criar cópias de segurança específicos ficheiros ou pastas, em vez de toda a VM, pode executar o agente de MARS juntamente com a extensão de VM.
**VM do Azure com o DPM** | Ficheiros, pastas, volumes, o estado do sistema, os dados da aplicação | Criar cópias de segurança para o armazenamento local de VM do Azure que está a executar o DPM. O DPM, em seguida, efetua cópias de segurança para o cofre. | Instantâneos de deteção de aplicação.<br/><br/> Granularidade de completa para cópia de segurança e recuperação.<br/><br/> Linux suportada para VMs (Hyper-V/VMware).<br/><br/> Oracle não suportada.
**VM do Azure com o MABS** | Ficheiros, pastas, volumes, o estado do sistema, os dados da aplicação | Criar cópias de segurança para o armazenamento local de VM do Azure que está a executar o MABS. MABS, em seguida, efetua cópias de segurança no cofre. | Instantâneos de deteção de aplicação.<br/><br/> Granularidade de completa para cópia de segurança e recuperação.<br/><br/> Linux suportada para VMs (Hyper-V/VMware).<br/><br/> Oracle não suportada.

## <a name="linux-backup-support"></a>Suporte de cópia de segurança do Linux

Eis o que é suportado se quiser fazer uma cópia de segurança de máquinas do Linux:

**Tipo de cópia de segurança** | **Linux (aprovado pelo Azure)**
--- | ---
**Cópia de segurança direta da máquina no local que está a executar o Linux** | Não suportado. O agente de MARS pode ser instalado apenas em máquinas do Windows.
**Usando a extensão do agente para fazer uma cópia de segurança de VM do Azure que está a executar o Linux** | Cópia de segurança consistente com a aplicação utilizando [scripts personalizados](backup-azure-linux-app-consistent.md).<br/><br/> Recuperação ao nível do ficheiro.<br/><br/> Restaure ao criar uma VM a partir de um ponto de recuperação ou disco.
**Utilizar o DPM para criar cópias de segurança no local ou VM do Azure que está a executar o Linux** | Cópia de segurança consistente com ficheiros de VMs de convidado de Linux no Hyper-V e VMWare.<br/><br/> Restauro de VM de Hyper-V e VMs de convidado de Linux do VMWare.<br/><br/> Ficheiro de cópia de segurança consistente não está disponível para VM do Azure.
**Usando o MABS para criar cópias de segurança no computador local ou VM do Azure que está a executar o Linux** | Cópia de segurança consistente com ficheiros de VMs de convidado de Linux no Hyper-V e VMWare.<br/><br/> Restauro de VM de Hyper-V e VMs de convidado de Linux do VMWare.<br/><br/> Ficheiro de cópia de segurança consistente não está disponível para VMs do Azure.

## <a name="daylight-saving-time-support"></a>Suporte de horário de Verão

O Azure Backup não suporta o ajuste automático de relógio para horário de Verão para cópias de segurança de VM do Azure. Modificar as políticas de cópia de segurança manualmente conforme necessário.

## <a name="disk-deduplication-support"></a>Suporte de eliminação de duplicados do disco

Suporte de eliminação de duplicados do disco é o seguinte:

- A eliminação de duplicados do disco é suportado no local ao utilizar o DPM ou MABs para fazer uma cópia de segurança de VMs de Hyper-V que executem o Windows. Windows Server executa a eliminação de duplicados de dados (ao nível do anfitrião) em discos rígidos virtuais (VHDs) que estão ligados à VM como armazenamento de cópia de segurança.
- A eliminação de duplicados não é suportada no Azure para qualquer componente de cópia de segurança. Quando o DPM e o MABS são implementadas no Azure, os discos de armazenamento ligados à VM não é possível ter duplicados eliminados.

## <a name="security-and-encryption-support"></a>Suporte de segurança e encriptação

O Azure Backup suporta a encriptação para dados em trânsito e em repouso.

### <a name="network-traffic-to-azure"></a>Tráfego de rede para o Azure

- Tráfego de cópia de segurança dos servidores para o Cofre dos serviços de recuperação é encriptado com o Advanced Encryption Standard 256.
- Dados de cópia de segurança são enviados através de uma ligação HTTPS segura.
- Dados de cópia de segurança são armazenados no cofre dos serviços de recuperação no formato encriptado.
- O único a ter a frase de acesso para desbloquear estes dados. Microsoft não é possível desencriptar os dados de cópia de segurança em momento algum.

    > [!WARNING]
    > Depois de configurar o cofre, apenas tem acesso à chave de encriptação. A Microsoft nunca mantém uma cópia e não tem acesso à chave. Se a chave está no local incorreto, a Microsoft não é possível recuperar os dados de cópia de segurança.

### <a name="data-security"></a>Segurança de dados

- Quando estiver fazendo o backup de VMs do Azure, terá de configurar a encriptação *dentro de* a máquina virtual.
- O Azure Backup suporta o Azure Disk Encryption, que utiliza o BitLocker nas máquinas virtuais do Windows e **dm-crypt** nas máquinas virtuais do Linux.
- No back-end, o Azure Backup utiliza [do Azure Storage Service Encryption](../storage/common/storage-service-encryption.md), que protege dados em repouso.

**Machine** | **Em trânsito** | **Em repouso**
--- | --- | ---
**Máquinas do Windows sem DPM/MABS no local** | ![Sim][green] | ![Sim][green]
**VMs do Azure** | ![Sim][green] | ![Sim][green]
**Máquinas do Windows no local ou VMs do Azure com o DPM** | ![Sim][green] | ![Sim][green]
**Máquinas do Windows no local ou VMs do Azure com o MABS** | ![Sim][green] | ![Sim][green]

## <a name="compression-support"></a>Suporte de compressão

Cópia de segurança suporta a compactação do tráfego de cópia de segurança, conforme resumido na tabela seguinte.

- Para VMs do Azure, a extensão da VM lê os dados diretamente a partir da conta de armazenamento do Azure através da rede de armazenamento, pelo que não é necessário comprimir este tráfego.
- Se estiver a utilizar o DPM ou MABS, pode poupar largura de banda ao comprimir os dados antes de ele é uma cópia de segurança.

**Machine** | **Comprimir para o MABS/DPM (TCP)** | **Comprimir cofre (HTTPS)**
--- | --- | ---
**Cópia de segurança direta de máquinas do Windows no local** | ND | ![Sim][green]
**Cópia de segurança de VMs do Azure ao utilizar a extensão de VM** | ND | ND
**Cópia de segurança em máquinas no local/Azure utilizando o MABS/DPM** | ![Sim][green] | ![Sim][green]

## <a name="retention-limits"></a>Limites de retenção

**Definição** | **Limites**
--- | ---
**Máximo de pontos de recuperação por instância protegida (máquina ou carga de trabalho)** | 9,999
**Hora de expiração máximo para um ponto de recuperação** | Sem limite
**Frequência de cópia de segurança máxima para o DPM/MABS** | A cada 15 minutos para o SQL Server<br/><br/> Uma vez por hora para outras cargas de trabalho
**Frequência de cópia de segurança máxima para o Cofre** | **No local máquinas do Windows ou VMs do Azure a executar o MARS:** Três por dia<br/><br/> **O DPM/MABS:** Duas por dia<br/><br/> **Cópia de segurança VM do Azure:** Uma por dia
**Retenção do ponto de recuperação** | Diariamente, semanalmente, mensalmente, anualmente
**Período de retenção máximo** | Depende da frequência da cópia de segurança
**Pontos de recuperação no disco do DPM/MABS** | 64 para servidores de ficheiros 448 para servidores de aplicações <br/><br/>Pontos de recuperação de banda ilimitado para o DPM no local

## <a name="next-steps"></a>Passos Seguintes

- [Matriz de suporte de revisão](backup-support-matrix-iaas.md) para cópia de segurança de VM do Azure.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png

---
title: Descrição geral dos cofres dos Serviços de Recuperação
description: Uma descrição geral e comparação entre os cofres dos serviços de recuperação e os cofres de cópia de segurança do Azure.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 8/10/2018
ms.author: raynew
ms.openlocfilehash: 924b36701ecf21f6bd84938aeefbf25e47fcbaa7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60699255"
---
# <a name="recovery-services-vaults-overview"></a>Descrição geral de cofres de serviços de recuperação

Este artigo descreve as funcionalidades de um cofre dos serviços de recuperação. Um cofre dos serviços de recuperação é uma entidade de armazenamento no Azure que aloja os dados. Os dados normalmente são cópias de dados ou informações de configuração para máquinas virtuais (VMs), cargas de trabalho, servidores ou estações de trabalho. Pode utilizar cofres dos serviços de recuperação para armazenar dados de cópia de segurança para vários serviços do Azure, como VMs de IaaS (Linux ou Windows) e bases de dados SQL do Azure. Sistema de suporte de cofres de serviços de recuperação Center DPM, Windows Server, servidor de cópia de segurança do Azure e muito mais. Os cofres dos Serviços de Recuperação facilitam a organização dos dados de cópia de segurança ao minimizar os custos de gestão.

Uma subscrição do Azure, pode criar até 500 cofres de serviços de recuperação por subscrição por região.

## <a name="comparing-recovery-services-vaults-and-backup-vaults"></a>Cofres dos serviços de recuperação comparando e cofres de cópia de segurança

Se ainda terá os cofres de cópia de segurança, eles estão a ser atualizado automaticamente para cofres dos serviços de recuperação. Novembro de 2017, todos os cofres de cópia de segurança foram atualizados para cofres dos serviços de recuperação.

Os cofres dos serviços de recuperação baseiam-se no modelo do Azure Resource Manager do Azure, ao passo que os cofres de cópia de segurança foram com base no modelo do Azure Service Manager. Quando atualizar um cofre de cópia de segurança para um cofre dos serviços de recuperação, os dados de cópia de segurança permanecem intactos durante e após o processo de atualização. Os cofres dos serviços de recuperação fornecem funcionalidades não disponíveis para os cofres de cópia de segurança, tais como:

- **Recursos para ajudar a proteger os dados da cópia de segurança aprimorados**: Com cofres dos serviços de recuperação, a cópia de segurança do Azure fornece recursos de segurança para proteger cópias de segurança na cloud. Os recursos de segurança Certifique-se pode proteger as suas cópias de segurança e recuperar com segurança de dados, mesmo que os servidores de produção e cópia de segurança sejam comprometidos. [Saiba mais](backup-azure-security-feature.md)

- **Monitorização central para seu ambiente de TI híbrido**: Com os cofres dos serviços de recuperação, pode monitorizar não apenas sua [VMs IaaS do Azure](backup-azure-manage-vms.md) , mas também sua [recursos no local](backup-azure-manage-windows-server.md#manage-backup-items) partir de um portal central. [Saiba mais](https://azure.microsoft.com/blog/alerting-and-monitoring-for-azure-backup)

- **Controlo de acesso baseado em funções (RBAC)** : RBAC fornece controlo de gestão de acesso detalhado no Azure. [O Azure fornece várias funções incorporadas](../role-based-access-control/built-in-roles.md), e o Azure Backup tem três [funções incorporadas para gerir pontos de recuperação](backup-rbac-rs-vault.md). Os cofres dos serviços de recuperação são compatíveis com o RBAC, que restringe a cópia de segurança e restaurar o acesso para o conjunto definido de funções de utilizador. [Saiba mais](backup-rbac-rs-vault.md)

- **Proteger todas as configurações das máquinas virtuais Azure**: Os cofres dos serviços de recuperação protegem VMs com base no Gestor de recursos, incluindo os discos Premium, o Managed Disks e VMs encriptadas. Atualizar um cofre de cópia de segurança para um cofre dos serviços de recuperação dá-lhe a oportunidade de atualizar as VMs com base no Service Manager para VMs baseadas no Resource Manager. Durante a atualização do cofre, pode reter os pontos de recuperação VM baseada no Service Manager e configure a proteção para as VMs (Resource Manager-ativado) atualizadas. [Saiba mais](https://azure.microsoft.com/blog/azure-backup-recovery-services-vault-ga)

- **Restauro imediato para IaaS VMs**: Utilizar cofres dos serviços de recuperação, pode restaurar ficheiros e pastas a partir de uma VM de IaaS sem restaurar a VM inteira, que permite que os tempos de restauro mais rápidos. Restauro imediato para IaaS VMs está disponível para VMs do Linux e Windows. [Saiba mais](https://azure.microsoft.com/blog/instant-file-recovery-from-azure-linux-vm-backup-using-azure-backup-preview)

## <a name="managing-your-recovery-services-vaults-in-the-portal"></a>Gerir os cofres de serviços de recuperação no portal
Criação e gestão dos cofres de serviços de recuperação no portal do Azure é fácil porque o serviço de cópia de segurança integra-se noutros serviços do Azure. Esta integração significa que pode criar ou gerir um cofre dos serviços de recuperação *no contexto do serviço de destino*. Por exemplo, para ver os pontos de recuperação para uma VM, selecione a sua VM e clique em **cópia de segurança** no menu de operações.

![Detalhes do Cofre de serviços de recuperação VM](./media/backup-azure-recovery-services-vault-overview/rs-vault-in-context-vm.png)

Se a VM não tiver a cópia de segurança configurada, em seguida, ele irá solicitar-lhe para configurar a cópia de segurança. Se tiver sido configurada a cópia de segurança, verá informações de cópia de segurança sobre a VM, incluindo uma lista de pontos de restauro.  

![Detalhes do Cofre de serviços de recuperação VM](./media/backup-azure-recovery-services-vault-overview/vm-recovery-point-list.png)

No exemplo anterior, **ContosoVM** é o nome da máquina virtual. **ContosoVM-demovault** é o nome do cofre dos serviços de recuperação. Não precisa se lembrar do nome do cofre dos serviços de recuperação que armazena os pontos de recuperação, pode aceder a estas informações da máquina virtual.  

Se um cofre dos serviços de recuperação Protege os vários servidores, pode ser mais lógica para examinar o Cofre dos serviços de recuperação. Pode procurar todos os cofres de serviços de recuperação na subscrição e selecione um na lista.

As secções seguintes contêm links para artigos que explicam como usar um cofre dos serviços de recuperação em cada tipo de atividade.

> [!NOTE]
> Não é possível criar o Cofre dos serviços de recuperação com o mesmo nome se tiver sido eliminada dentro de 24 horas. Utilize um nome de recurso diferente ou escolha um grupo de recursos diferente ou repita novamente após 24 horas.

### <a name="back-up-data"></a>Criar cópias de segurança
- [Fazer cópias de segurança de uma VM do Azure](backup-azure-vms-first-look-arm.md)
- [Criar cópias de segurança do Windows Server ou estação de trabalho do Windows](backup-try-azure-backup-in-10-mins.md)
- [Fazer cópias de segurança de cargas de trabalho do DPM para o Azure](backup-azure-dpm-introduction.md)
- [Preparar a cópia de segurança de cargas de trabalho com o Azure Backup Server](backup-azure-microsoft-azure-backup.md)

### <a name="manage-recovery-points"></a>Gerir pontos de recuperação
- [Gerir cópias de segurança de VM do Azure](backup-azure-manage-vms.md)
- [Gerenciamento de arquivos e pastas](backup-azure-manage-windows-server.md)

### <a name="restore-data-from-the-vault"></a>Restaurar dados a partir do Cofre
- [Recuperar ficheiros individuais a partir de uma VM do Azure](backup-azure-restore-files-from-vm.md)
- [Restaurar uma VM do Azure](backup-azure-arm-restore-vms.md)

### <a name="secure-the-vault"></a>Proteger o Cofre
- [Proteção de dados de cópia de segurança na cloud nos cofres dos serviços de recuperação](backup-azure-security-feature.md)



## <a name="next-steps"></a>Próximos Passos
Utilize os seguintes artigos para:</br>
[Fazer uma cópia de segurança de uma VM de IaaS](backup-azure-arm-vms-prepare.md)</br>
[Fazer uma cópia de segurança de um servidor de cópia de segurança do Azure](backup-azure-microsoft-azure-backup.md)</br>
[Fazer cópias de segurança do Windows Server](backup-configure-vault.md)

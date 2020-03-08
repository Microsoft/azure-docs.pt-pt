---
title: Descrição geral dos cofres dos Serviços de Recuperação
description: Uma visão geral e comparação entre cofres de Serviços de Recuperação e cofres azure backup.
ms.topic: conceptual
ms.date: 08/10/2018
ms.openlocfilehash: 94a3e5a0865bcc8c0a9ecb866ca013f20a558e1a
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78673243"
---
# <a name="recovery-services-vaults-overview"></a>Visão geral dos serviços de recuperação

Este artigo descreve as características de um cofre dos Serviços de Recuperação. Um cofre de Serviços de Recuperação é uma entidade de armazenamento em Azure que abriga dados. Os dados são tipicamente cópias de dados, ou informações de configuração para máquinas virtuais (VMs), cargas de trabalho, servidores ou estações de trabalho. Pode utilizar cofres dos Serviços de Recuperação para guardar dados de backup para vários serviços Azure, tais como VMs IaaS (Linux ou Windows) e bases de dados Azure SQL. Os cofres dos Serviços de Recuperação suportam o System Center DPM, o Windows Server, o Azure Backup Server e muito mais. Os cofres dos Serviços de Recuperação facilitam a organização dos dados de cópia de segurança ao minimizar os custos de gestão.

Dentro de uma subscrição azure, você pode criar até 500 cofres de Serviços de Recuperação por subscrição por região.

## <a name="comparing-recovery-services-vaults-and-backup-vaults"></a>Comparando cofres de serviços de recuperação e cofres de backup

Se ainda tiver cofres de reserva, estão a ser automaticamente atualizados para cofres dos Serviços de Recuperação. Até novembro de 2017, todos os cofres de backup foram atualizados para cofres dos Serviços de Recuperação.

Os cofres dos Serviços de Recuperação baseiam-se no modelo azure Resource Manager do Azure, enquanto os cofres de backup foram baseados no modelo do Gestor de Serviços Azure. Ao atualizar um cofre de backup para um cofre dos Serviços de Recuperação, os dados de backup permanecem intactos durante e após o processo de atualização. Os cofres dos Serviços de Recuperação fornecem funcionalidades não disponíveis para cofres de backup, tais como:

- **Capacidades melhoradas para ajudar a proteger os dados de backup**: Com os cofres dos Serviços de Recuperação, o Azure Backup fornece capacidades de segurança para proteger as cópias de segurança da nuvem. As funcionalidades de segurança garantem que pode proteger as suas cópias de segurança e recuperar dados com segurança, mesmo que os servidores de produção e cópia de segurança estejam comprometidos. [Saiba mais](backup-azure-security-feature.md)

- **Monitorização central para o seu ambiente de TI híbrido**: Com cofres de Serviços de Recuperação, você pode monitorizar não só os seus [VMs Azure IaaS,](backup-azure-manage-vms.md) mas também os seus [ativos no local](backup-azure-manage-windows-server.md#manage-backup-items) a partir de um portal central. [Saiba mais](https://azure.microsoft.com/blog/alerting-and-monitoring-for-azure-backup)

- **Controlo de Acesso baseado em funções (RBAC)** : O RBAC fornece um controlo de gestão de acesso fino em Azure. [O Azure fornece várias funções incorporadas](../role-based-access-control/built-in-roles.md), e o Azure Backup tem três [funções incorporadas para gerir pontos de recuperação.](backup-rbac-rs-vault.md) Os cofres dos Serviços de Recuperação são compatíveis com o RBAC, que restringe a cópia de segurança e restaura o acesso ao conjunto definido de funções de utilizador. [Saiba mais](backup-rbac-rs-vault.md)

- **Proteja todas as configurações de Máquinas Virtuais Azure**: Os cofres dos Serviços de Recuperação protegem vMs baseados em Recursos, incluindo Discos Premium, Discos Geridos e VMs encriptados. A atualização de um cofre de backup para um cofre de Serviços de Recuperação dá-lhe a oportunidade de atualizar os seus VMs baseados em Gestor de Serviços para VMs baseados em Recursos. Ao atualizar o cofre, pode reter os seus pontos de recuperação vM baseados em Gestor de Serviço e configurar a proteção para os VMs atualizados (Resource Manager). [Saiba mais](https://azure.microsoft.com/blog/azure-backup-recovery-services-vault-ga)

- **Restauro instantâneo para VMs IaaS**: Utilizando cofres de Serviços de Recuperação, pode restaurar ficheiros e pastas de um VM IaaS sem restaurar todo o VM, o que permite tempos de restauro mais rápidos. O restauro instantâneo para VMs IaaS está disponível tanto para Windows como Para VMs Linux. [Saiba mais](backup-instant-restore-capability.md)

## <a name="managing-your-recovery-services-vaults-in-the-portal"></a>Gerir os seus cofres de Serviços de Recuperação no portal

A criação e gestão de cofres de Serviços de Recuperação no portal Azure é fácil porque o serviço de backup integra-se noutros serviços do Azure. Esta integração significa que pode criar ou gerir um cofre de Serviços de Recuperação *no contexto do serviço alvo.* Por exemplo, para visualizar os pontos de recuperação de um VM, selecione o seu VM e clique em **Backup** no menu Operações.

![Detalhes do cofre dos serviços de recuperação VM](./media/backup-azure-recovery-services-vault-overview/rs-vault-in-context-vm.png)

Se o VM não tiver cópia de segurança configurada, irá institurá-lo a configurar a cópia de segurança. Se a cópia de segurança tiver sido configurada, verá informações de backup sobre o VM, incluindo uma lista de pontos de restauro.  

![Detalhes do cofre dos serviços de recuperação VM](./media/backup-azure-recovery-services-vault-overview/vm-recovery-point-list.png)

No exemplo anterior, **ContosoVM** é o nome da máquina virtual. **ContosoVM-demovault** é o nome do cofre dos Serviços de Recuperação. Não precisa de se lembrar do nome do cofre dos Serviços de Recuperação que armazena os pontos de recuperação, pode aceder a esta informação a partir da máquina virtual.  

Se um cofre dos Serviços de Recuperação proteger vários servidores, pode ser mais lógico olhar para o cofre dos Serviços de Recuperação. Pode pesquisar todos os cofres dos Serviços de Recuperação na subscrição e escolher um da lista.

As seguintes secções contêm links para artigos que explicam como usar um cofre de Serviços de Recuperação em cada tipo de atividade.

> [!NOTE]
> O cofre dos Serviços de Recuperação não pode ser criado com o mesmo nome se tiver sido apagado dentro de 24 horas. Use um nome de recurso diferente ou escolha um grupo de recursos diferente ou volte a tentar novamente após 24 horas.

### <a name="back-up-data"></a>Dados de back up

- [Back up a Azure VM](backup-azure-vms-first-look-arm.md)
- [Back up Windows Server ou Windows workstation](backup-try-azure-backup-in-10-mins.md)
- [Recue as cargas de trabalho do DPM para o Azure](backup-azure-dpm-introduction.md)
- [Prepare-se para fazer backup de cargas de trabalho usando o Servidor de Backup Azure](backup-azure-microsoft-azure-backup.md)

### <a name="manage-recovery-points"></a>Gerir pontos de recuperação

- [Gerir backups Azure VM](backup-azure-manage-vms.md)
- [Gestão de ficheiros e pastas](backup-azure-manage-windows-server.md)

### <a name="restore-data-from-the-vault"></a>Restaurar os dados do cofre

- [Recuperar ficheiros individuais de um VM Azure](backup-azure-restore-files-from-vm.md)
- [Restaurar um VM Azure](backup-azure-arm-restore-vms.md)

### <a name="secure-the-vault"></a>Proteja o cofre.

- [Garantir dados de backup em nuvem nos cofres dos Serviços de Recuperação](backup-azure-security-feature.md)

## <a name="next-steps"></a>Passos seguintes

Utilize os seguintes artigos para:</br>
[Back up a IaaS VM](backup-azure-arm-vms-prepare.md)</br>
[Backup de um servidor de backup Azure](backup-azure-microsoft-azure-backup.md)</br>
[Back up a Windows Server](backup-windows-with-mars-agent.md)

---
title: Descrição geral dos cofres dos Serviços de Recuperação
description: Uma visão geral e comparação entre cofres dos Serviços de Recuperação e cofres Azure Backup.
ms.topic: conceptual
ms.date: 08/10/2018
ms.openlocfilehash: 798f49629ad1012e8cc9ac3ed43f5beddd6eefeb
ms.sourcegitcommit: 8017209cc9d8a825cc404df852c8dc02f74d584b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/01/2020
ms.locfileid: "84248911"
---
# <a name="recovery-services-vaults-overview"></a>Descrição geral dos Cofres dos Serviços de Recuperação

Este artigo descreve as características de um cofre dos Serviços de Recuperação. Um cofre dos Serviços de Recuperação é uma entidade de armazenamento em Azure que alberga dados. Os dados são normalmente cópias de dados, ou informações de configuração para máquinas virtuais (VMs), cargas de trabalho, servidores ou estações de trabalho. Pode utilizar cofres dos Serviços de Recuperação para conter dados de backup para vários serviços Azure, tais como IaaS VMs (Linux ou Windows) e bases de dados Azure SQL. Os cofres dos Serviços de Recuperação suportam o System Center DPM, Windows Server, Azure Backup Server e muito mais. Os cofres dos Serviços de Recuperação facilitam a organização dos dados de cópia de segurança ao minimizar os custos de gestão.

Dentro de uma subscrição Azure, você pode criar até 500 cofres de Serviços de Recuperação por subscrição por região.

## <a name="comparing-recovery-services-vaults-and-backup-vaults"></a>Comparando cofres dos Serviços de Recuperação e cofres de reserva

Se ainda tens cofres de reserva, estão a ser atualizados automaticamente para cofres dos Serviços de Recuperação. Em novembro de 2017, todos os cofres de reserva foram atualizados para cofres dos Serviços de Recuperação.

Os cofres dos Serviços de Recuperação baseiam-se no modelo Azure Resource Manager da Azure, no entanto os cofres de backup foram baseados no modelo Azure Service Manager. Quando atualiza um cofre de reserva para um cofre dos Serviços de Recuperação, os dados de cópia de segurança permanecem intactos durante e após o processo de atualização. Os cofres dos Serviços de Recuperação fornecem funcionalidades não disponíveis para cofres de backup, tais como:

- **Capacidades melhoradas para ajudar a proteger dados de backup**: Com os cofres dos Serviços de Recuperação, o Azure Backup fornece capacidades de segurança para proteger cópias de segurança na nuvem. As funcionalidades de segurança garantem que pode proteger as suas cópias de segurança e recuperar dados com segurança, mesmo que os servidores de produção e cópia de segurança estejam comprometidos. [Saiba mais](backup-azure-security-feature.md)

- **Monitorização central para o seu ambiente híbrido de TI**: Com cofres dos Serviços de Recuperação, pode monitorizar não só os seus [VMs Azure IaaS,](backup-azure-manage-vms.md) mas também [os seus ativos no local](backup-azure-manage-windows-server.md#manage-backup-items) a partir de um portal central. [Saiba mais](https://azure.microsoft.com/blog/alerting-and-monitoring-for-azure-backup)

- **Controlo de Acesso Baseado em Função (RBAC)**: O RBAC fornece um controlo de gestão de acessos finos em Azure. [O Azure oferece vários papéis incorporados,](../role-based-access-control/built-in-roles.md)e o Azure Backup tem três [funções incorporadas para gerir pontos de recuperação.](backup-rbac-rs-vault.md) Os cofres dos Serviços de Recuperação são compatíveis com o RBAC, que restringe o backup e restaura o acesso ao conjunto definido de funções de utilizador. [Saiba mais](backup-rbac-rs-vault.md)

- **Proteger todas as configurações de Máquinas Virtuais Azure**: Os cofres dos Serviços de Recuperação protegem os VMs baseados em Gestores de Recursos, incluindo Discos Premium, Discos Geridos e VMs encriptados. A atualização de um cofre de backup para um cofre dos Serviços de Recuperação dá-lhe a oportunidade de atualizar os VMs baseados no Service Manager para VMs baseados em Recursos. Durante a atualização do cofre, pode reter os pontos de recuperação VM baseados no Service Manager e configurar a proteção para os VMs atualizados (habilitados pelo Gestor de Recursos). [Saiba mais](https://azure.microsoft.com/blog/azure-backup-recovery-services-vault-ga)

- **Restauro instantâneo para IaaS VMs**: Utilizando cofres dos Serviços de Recuperação, pode restaurar ficheiros e pastas a partir de um IaaS VM sem restaurar todo o VM, o que permite tempos de restauração mais rápidos. A restauração instantânea para IaaS VMs está disponível tanto para Windows como para Linux VMs. [Saiba mais](backup-instant-restore-capability.md)

## <a name="storage-settings-in-the-recovery-services-vault"></a>Definições de armazenamento no cofre dos Serviços de Recuperação

Um cofre dos Serviços de Recuperação é uma entidade que armazena os backups e pontos de recuperação criados ao longo do tempo. O cofre dos Serviços de Recuperação também contém as políticas de backup que estão associadas às máquinas virtuais protegidas.

O Azure Backup manuseia automaticamente o armazenamento para o cofre. Veja como as [definições de armazenamento podem ser alteradas](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-storage-redundancy).

Para saber mais sobre a redundância de armazenamento, consulte estes artigos sobre [redundância geo](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs) e [local.](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs)

## <a name="managing-your-recovery-services-vaults-in-the-portal"></a>Gerir os cofres dos Serviços de Recuperação no portal

A criação e gestão de cofres dos Serviços de Recuperação no portal Azure é fácil porque o serviço de Backup integra-se em outros serviços da Azure. Esta integração significa que pode criar ou gerir um cofre dos Serviços de Recuperação *no contexto do serviço-alvo.* Por exemplo, para ver os pontos de recuperação de um VM, selecione o seu VM e clique em **Backup** no menu Operações.

![Detalhes do cofre dos Serviços de Recuperação VM](./media/backup-azure-recovery-services-vault-overview/rs-vault-in-context-vm.png)

Se o VM não tiver cópia de segurança configurada, irá pedir-lhe que configure a cópia de segurança. Se a cópia de segurança tiver sido configurada, verá informações de backup sobre o VM, incluindo uma lista de pontos de restauro.  

![Detalhes do cofre dos serviços de recuperação VM](./media/backup-azure-recovery-services-vault-overview/vm-recovery-point-list.png)

No exemplo anterior, **ContosoVM** é o nome da máquina virtual. **ContosoVM-demovault** é o nome do cofre dos Serviços de Recuperação. Não precisa de se lembrar do nome do cofre dos Serviços de Recuperação que armazena os pontos de recuperação, pode aceder a esta informação a partir da máquina virtual.  

Se um cofre dos Serviços de Recuperação proteger vários servidores, pode ser mais lógico olhar para o cofre dos Serviços de Recuperação. Pode procurar todos os cofres dos Serviços de Recuperação na subscrição e escolher um da lista.

As seguintes secções contêm ligações a artigos que explicam como usar um cofre dos Serviços de Recuperação em cada tipo de atividade.

> [!NOTE]
> O cofre dos Serviços de Recuperação não pode ser criado com o mesmo nome se tiver sido eliminado dentro de 24 horas. Use um nome de recurso diferente ou escolha um grupo de recursos diferente ou volte a tentar depois de 24 horas.

### <a name="back-up-data"></a>Fazer cópias de segurança de dados

- [Apoiar um VM Azure](backup-azure-vms-first-look-arm.md)
- [Faça o back up Windows Server ou Windows workstation](backup-try-azure-backup-in-10-mins.md)
- [Apoiar cargas de trabalho da DPM para Azure](backup-azure-dpm-introduction.md)
- [Prepare-se para fazer backup de cargas de trabalho usando o Servidor de Backup Azure](backup-azure-microsoft-azure-backup.md)

### <a name="manage-recovery-points"></a>Gerir pontos de recuperação

- [Gerir as cópias de segurança de VMs do Azure](backup-azure-manage-vms.md)
- [Gestão de ficheiros e pastas](backup-azure-manage-windows-server.md)

### <a name="restore-data-from-the-vault"></a>Restaurar dados do cofre

- [Recuperar ficheiros individuais de um Azure VM](backup-azure-restore-files-from-vm.md)
- [Restaurar um VM azul](backup-azure-arm-restore-vms.md)

### <a name="secure-the-vault"></a>Proteja o cofre.

- [Garantir dados de backup em nuvem em cofres dos Serviços de Recuperação](backup-azure-security-feature.md)

## <a name="azure-advisor"></a>Azure Advisor

[O Azure Advisor](https://docs.microsoft.com/azure/advisor/) é um consultor de nuvem personalizado que ajuda a otimizar o uso do Azure. Analisa o seu uso Azure e fornece recomendações oportunas para ajudar a otimizar e proteger as suas implementações. Fornece recomendações em quatro categorias: Alta Disponibilidade, Segurança, Desempenho e Custo.

O Azure Advisor fornece [recomendações horárias](https://docs.microsoft.com/azure/advisor/advisor-high-availability-recommendations#protect-your-virtual-machine-data-from-accidental-deletion) para VMs que não são apoiados, por isso nunca perca o apoio de VMs importantes. Também pode controlar as recomendações snoozing-las.  Pode clicar na recomendação e ativar a cópia de segurança em VMs em linha especificando o cofre (onde serão armazenadas cópias de segurança) e a política de backup (agendamento de cópias de backup e retenção de cópias de backup).

![Azure Advisor](./media/backup-azure-recovery-services-vault-overview/azure-advisor.png)

## <a name="next-steps"></a>Passos seguintes

Utilize os seguintes artigos para:</br>
[Apoiar um IaaS VM](backup-azure-arm-vms-prepare.md)</br>
[Faça backup a um servidor de backup Azure](backup-azure-microsoft-azure-backup.md)</br>
[Fazer o back up de um Servidor Windows](backup-windows-with-mars-agent.md)

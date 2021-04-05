---
title: Descrição geral dos cofres dos Serviços de Recuperação
description: Uma visão geral dos cofres dos Serviços de Recuperação.
ms.topic: conceptual
ms.date: 08/17/2020
ms.openlocfilehash: 0ed37446e1ccf0780f924143c8f063964adf0004
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98755128"
---
# <a name="recovery-services-vaults-overview"></a>Descrição geral dos Cofres dos Serviços de Recuperação

Este artigo descreve as características de um cofre dos Serviços de Recuperação. Um cofre dos Serviços de Recuperação é uma entidade de armazenamento em Azure que alberga dados. Os dados são normalmente cópias de dados, ou informações de configuração para máquinas virtuais (VMs), cargas de trabalho, servidores ou estações de trabalho. Pode utilizar cofres dos Serviços de Recuperação para conter dados de backup para vários serviços Azure, tais como IaaS VMs (Linux ou Windows) e bases de dados Azure SQL. Os cofres dos Serviços de Recuperação suportam o System Center DPM, Windows Server, Azure Backup Server e muito mais. Os cofres dos Serviços de Recuperação facilitam a organização dos dados de cópia de segurança ao minimizar os custos de gestão. Os cofres dos Serviços de Recuperação baseiam-se no modelo Azure Resource Manager da Azure, que fornece funcionalidades como:

- **Capacidades melhoradas para ajudar a proteger dados de backup**: Com os cofres dos Serviços de Recuperação, o Azure Backup fornece capacidades de segurança para proteger cópias de segurança na nuvem. As funcionalidades de segurança garantem que pode proteger as suas cópias de segurança e recuperar dados com segurança, mesmo que os servidores de produção e cópia de segurança estejam comprometidos. [Saiba mais](backup-azure-security-feature.md)

- **Monitorização central para o seu ambiente híbrido de TI**: Com cofres dos Serviços de Recuperação, pode monitorizar não só os seus [VMs Azure IaaS,](backup-azure-manage-vms.md) mas também [os seus ativos no local](backup-azure-manage-windows-server.md#manage-backup-items) a partir de um portal central. [Saiba mais](backup-azure-monitoring-built-in-monitor.md)

- **Controlo de acesso baseado em funções Azure (Azure RBAC)**: O Azure RBAC fornece um controlo de gestão de acessos finos em Azure. [O Azure oferece vários papéis incorporados,](../role-based-access-control/built-in-roles.md)e o Azure Backup tem três [funções incorporadas para gerir pontos de recuperação.](backup-rbac-rs-vault.md) Os cofres dos Serviços de Recuperação são compatíveis com o Azure RBAC, que restringe o backup e restaura o acesso ao conjunto definido de funções de utilizador. [Saiba mais](backup-rbac-rs-vault.md)

- **Soft Delete**: Com a eliminação suave, mesmo que um ator malicioso elimine uma cópia de segurança (ou os dados de cópia de segurança sejam acidentalmente eliminados), os dados de cópia de segurança são retidos por mais 14 dias, permitindo a recuperação desse item de backup sem perda de dados. Os 14 dias adicionais de retenção para dados de backup no estado de "soft delete" não incorrem em qualquer custo para si. [Saiba mais](backup-azure-security-feature-cloud.md).

- **Cross Region Restore**: Cross Region Restore (CRR) permite restaurar os VMs Azure numa região secundária, que é uma região emparelhada Azure. Ao ativar esta funcionalidade ao nível do [cofre,](backup-create-rs-vault.md#set-cross-region-restore)pode restaurar os dados replicados na região secundária a qualquer momento, quando escolher. Isto permite-lhe restaurar os dados da região secundária para a conformidade com a auditoria, e durante os cenários de paralisação, sem esperar que a Azure declare um desastre (ao contrário das configurações GRS do cofre). [Saiba mais](backup-azure-arm-restore-vms.md#cross-region-restore).

## <a name="storage-settings-in-the-recovery-services-vault"></a>Definições de armazenamento no cofre dos Serviços de Recuperação

Um cofre dos Serviços de Recuperação é uma entidade que armazena os backups e pontos de recuperação criados ao longo do tempo. O cofre dos Serviços de Recuperação também contém as políticas de backup que estão associadas às máquinas virtuais protegidas.

- O Azure Backup manuseia automaticamente o armazenamento para o cofre. Veja como as [definições de armazenamento podem ser alteradas](./backup-create-rs-vault.md#set-storage-redundancy).

- Para saber mais sobre a redundância de armazenamento, consulte estes artigos sobre [redundância geo,](../storage/common/storage-redundancy.md#geo-zone-redundant-storage) [local](../storage/common/storage-redundancy.md#locally-redundant-storage) e [zonal.](../storage/common/storage-redundancy.md#zone-redundant-storage)

## <a name="encryption-settings-in-the-recovery-services-vault"></a>Definições de encriptação no cofre dos Serviços de Recuperação

Esta secção discute as opções disponíveis para encriptar os seus dados de backup armazenados no cofre dos Serviços de Recuperação.

### <a name="encryption-of-backup-data-using-platform-managed-keys"></a>Encriptação de dados de backup usando chaves geridas pela plataforma

Por predefinição, todos os seus dados são encriptados utilizando chaves geridas pela plataforma. Não precisa de tomar nenhuma ação explícita do seu lado para ativar esta encriptação. Aplica-se a todas as cargas de trabalho que estão a ser apoiadas até ao cofre dos Serviços de Recuperação.

### <a name="encryption-of-backup-data-using-customer-managed-keys"></a>Encriptação de dados de cópias de segurança com chaves geridas pelo cliente

Pode optar por encriptar os seus dados utilizando chaves de encriptação possuídas e geridas por si. O Azure Backup permite-lhe utilizar as suas teclas RSA armazenadas no Cofre da Chave Azure para encriptar as suas cópias de segurança. A chave de encriptação utilizada para encriptar cópias de segurança pode ser diferente da utilizada para a origem. Os dados estão protegidos utilizando uma chave de encriptação de dados baseada em AES 256 (DEK), que está, por sua vez, protegida usando as suas chaves. Isto dá-lhe controlo total sobre os dados e as chaves. Para permitir a encriptação, o cofre dos Serviços de Recuperação deve ter acesso à chave de encriptação no Cofre da Chave Azure. Pode desativar a chave ou revogar o acesso sempre que necessário. No entanto, tem de ativar a encriptação utilizando as suas chaves antes de tentar proteger quaisquer itens no cofre.

Leia mais sobre como encriptar os seus dados de backup [utilizando as teclas geridas pelo cliente](encryption-at-rest-with-cmk.md).

## <a name="azure-advisor"></a>Assistente do Azure

[O Azure Advisor](../advisor/index.yml) é um consultor de nuvem personalizado que ajuda a otimizar o uso do Azure. Analisa o seu uso Azure e fornece recomendações oportunas para ajudar a otimizar e proteger as suas implementações. Fornece recomendações em quatro categorias: Alta Disponibilidade, Segurança, Desempenho e Custo.

O Azure Advisor fornece [recomendações horárias](../advisor/advisor-high-availability-recommendations.md#protect-your-virtual-machine-data-from-accidental-deletion) para VMs que não são apoiados, por isso nunca perca o apoio de VMs importantes. Também pode controlar as recomendações snoozing-las.  Pode selecionar a recomendação e ativar a cópia de segurança em VMs em linha especificando o cofre (onde serão armazenadas cópias de segurança) e a política de backup (agendamento de cópias de backup e retenção de cópias de backup).

![Assistente do Azure](./media/backup-azure-recovery-services-vault-overview/azure-advisor.png)

## <a name="additional-resources"></a>Recursos adicionais

- [Cenários suportados e não suportados pelo cofre](backup-support-matrix.md#vault-support)
- [Cofre frequentemente fez perguntas](backup-azure-backup-faq.md)

## <a name="next-steps"></a>Passos seguintes

Utilize os seguintes artigos para:

- [Apoiar um IaaS VM](backup-azure-arm-vms-prepare.md)
- [Faça backup a um servidor de backup Azure](backup-azure-microsoft-azure-backup.md)
- [Fazer o back up de um Servidor Windows](backup-windows-with-mars-agent.md)

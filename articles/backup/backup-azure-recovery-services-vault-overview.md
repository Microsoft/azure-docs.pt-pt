---
title: Descrição geral dos cofres dos Serviços de Recuperação
description: Uma visão geral e uma comparação entre os cofres dos serviços de recuperação e os cofres de backup do Azure.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 08/10/2018
ms.author: dacurwin
ms.openlocfilehash: 3c451d1bd77fa1cec85a9d42b5d4d3d819b2d19a
ms.sourcegitcommit: d470d4e295bf29a4acf7836ece2f10dabe8e6db2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/02/2019
ms.locfileid: "70210357"
---
# <a name="recovery-services-vaults-overview"></a>Visão geral dos cofres dos serviços de recuperação

Este artigo descreve os recursos de um cofre dos serviços de recuperação. Um cofre dos serviços de recuperação é uma entidade de armazenamento no Azure que hospeda dados. Os dados normalmente são cópias de dados ou informações de configuração para VMs (máquinas virtuais), cargas de trabalho, servidores ou estações de trabalho. Você pode usar os cofres dos serviços de recuperação para manter os dados de backup de vários serviços do Azure, como VMs de IaaS (Linux ou Windows) e bancos de dado SQL do Azure. Os cofres dos serviços de recuperação dão suporte ao System Center DPM, ao Windows Server, ao Servidor de Backup do Azure e muito mais. Os cofres dos Serviços de Recuperação facilitam a organização dos dados de cópia de segurança ao minimizar os custos de gestão.

Em uma assinatura do Azure, você pode criar até 500 cofres de serviços de recuperação por assinatura por região.

## <a name="comparing-recovery-services-vaults-and-backup-vaults"></a>Comparando cofres de serviços de recuperação e cofres de backup

Se você ainda tiver cofres de backup, eles serão atualizados automaticamente para os cofres dos serviços de recuperação. Em novembro de 2017, todos os cofres de backup foram atualizados para os cofres dos serviços de recuperação.

Os cofres dos serviços de recuperação são baseados no modelo de Azure Resource Manager do Azure, enquanto os cofres de backup eram baseados no modelo de Service Manager do Azure. Quando você atualiza um cofre de backup para um cofre dos serviços de recuperação, os dados de backup permanecem intactos durante e após o processo de atualização. Os cofres dos serviços de recuperação fornecem recursos não disponíveis para cofres de backup, como:

- **Recursos aprimorados para ajudar a proteger os dados de backup**: Com os cofres dos serviços de recuperação, o backup do Azure fornece recursos de segurança para proteger os backups na nuvem. Os recursos de segurança garantem que você possa proteger seus backups e recuperar dados com segurança, mesmo se os servidores de produção e de backup estiverem comprometidos. [Saiba mais](backup-azure-security-feature.md)

- **Monitoramento central para seu ambiente de ti híbrido**: Com os cofres dos serviços de recuperação, você pode monitorar não apenas suas [VMs IaaS do Azure](backup-azure-manage-vms.md) , mas também seus [ativos locais](backup-azure-manage-windows-server.md#manage-backup-items) de um portal central. [Saiba mais](https://azure.microsoft.com/blog/alerting-and-monitoring-for-azure-backup)

- **RBAC (controle de acesso baseado em função)** : O RBAC fornece controle de gerenciamento de acesso refinado no Azure. O [Azure fornece várias funções internas](../role-based-access-control/built-in-roles.md), e o backup do Azure tem três [funções internas para gerenciar pontos de recuperação](backup-rbac-rs-vault.md). Os cofres dos serviços de recuperação são compatíveis com o RBAC, que restringe o acesso de backup e restauração ao conjunto definido de funções de usuário. [Saiba mais](backup-rbac-rs-vault.md)

- **Proteger todas as configurações de máquinas virtuais do Azure**: Os cofres dos serviços de recuperação protegem VMs baseadas no Resource Manager, incluindo discos Premium, Managed Disks e VMs criptografadas. A atualização de um cofre de backup para um cofre dos serviços de recuperação oferece a oportunidade de atualizar suas VMs baseadas em Service Manager para VMs baseadas no Gerenciador de recursos. Ao atualizar o cofre, você pode manter seus pontos de recuperação de VM baseados em Service Manager e configurar a proteção para as VMs atualizadas (habilitadas para o Gerenciador de recursos). [Saiba mais](https://azure.microsoft.com/blog/azure-backup-recovery-services-vault-ga)

- **Restauração instantânea para VMs de IaaS**: Usando cofres dos serviços de recuperação, você pode restaurar arquivos e pastas de uma VM IaaS sem restaurar toda a VM, o que permite tempos de restauração mais rápidos. A restauração instantânea para VMs de IaaS está disponível para VMs Windows e Linux. [Saiba mais](https://azure.microsoft.com/blog/instant-file-recovery-from-azure-linux-vm-backup-using-azure-backup-preview)

## <a name="managing-your-recovery-services-vaults-in-the-portal"></a>Gerenciando cofres dos serviços de recuperação no portal
A criação e o gerenciamento de cofres dos serviços de recuperação na portal do Azure é fácil porque o serviço de backup se integra a outros serviços do Azure. Essa integração significa que você pode criar ou gerenciar um cofre de serviços de recuperação *no contexto do serviço de destino*. Por exemplo, para exibir os pontos de recuperação de uma VM, selecione sua VM e clique em **backup** no menu operações.

![Detalhes do cofre dos serviços de recuperação VM](./media/backup-azure-recovery-services-vault-overview/rs-vault-in-context-vm.png)

Se a VM não tiver o backup configurado, ele solicitará que você configure o backup. Se o backup tiver sido configurado, você verá informações de backup sobre a VM, incluindo uma lista de pontos de restauração.  

![Detalhes do cofre dos serviços de recuperação VM](./media/backup-azure-recovery-services-vault-overview/vm-recovery-point-list.png)

No exemplo anterior, **ContosoVM** é o nome da máquina virtual. **ContosoVM-demovault** é o nome do cofre dos serviços de recuperação. Você não precisa se lembrar do nome do cofre dos serviços de recuperação que armazena os pontos de recuperação, pode acessar essas informações da máquina virtual.  

Se um cofre dos serviços de recuperação proteger vários servidores, poderá ser mais lógico examinar o cofre dos serviços de recuperação. Você pode pesquisar todos os cofres de serviços de recuperação na assinatura e escolher um na lista.

As seções a seguir contêm links para artigos que explicam como usar um cofre de serviços de recuperação em cada tipo de atividade.

> [!NOTE]
> O cofre dos serviços de recuperação não pode ser criado com o mesmo nome, caso tenha sido excluído dentro de 24 horas. Use um nome de recurso diferente ou escolha um grupo de recursos diferente ou tente novamente após 24 horas.

### <a name="back-up-data"></a>Fazer backup de dados
- [Fazer backup de uma VM do Azure](backup-azure-vms-first-look-arm.md)
- [Fazer backup do Windows Server ou da estação de trabalho do Windows](backup-try-azure-backup-in-10-mins.md)
- [Fazer backup de cargas de trabalho do DPM no Azure](backup-azure-dpm-introduction.md)
- [Preparar para fazer backup de cargas de trabalho usando o Servidor de Backup do Azure](backup-azure-microsoft-azure-backup.md)

### <a name="manage-recovery-points"></a>Gerenciar pontos de recuperação
- [Gerenciar backups de VM do Azure](backup-azure-manage-vms.md)
- [Gerenciando arquivos e pastas](backup-azure-manage-windows-server.md)

### <a name="restore-data-from-the-vault"></a>Restaurar dados do cofre
- [Recuperar arquivos individuais de uma VM do Azure](backup-azure-restore-files-from-vm.md)
- [Restaurar uma VM do Azure](backup-azure-arm-restore-vms.md)

### <a name="secure-the-vault"></a>Proteger o cofre
- [Protegendo dados de backup na nuvem em cofres dos serviços de recuperação](backup-azure-security-feature.md)



## <a name="next-steps"></a>Próximos Passos
Use os seguintes artigos para:</br>
[Fazer backup de uma VM IaaS](backup-azure-arm-vms-prepare.md)</br>
[Fazer backup de um Servidor de Backup do Azure](backup-azure-microsoft-azure-backup.md)</br>
[Fazer backup de um Windows Server](backup-configure-vault.md)

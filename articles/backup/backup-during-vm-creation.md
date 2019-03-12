---
title: Ativar cópia de segurança de VM do Azure durante a criação
description: Como ativar a cópia de segurança da máquina virtual do Azure durante o processo de criação.
services: backup, virtual-machines
author: rayne-wiselman
manager: carmonm
tags: azure-resource-manager, virtual-machine-backup
ms.service: backup
ms.topic: conceptual
ms.date: 01/08/2018
ms.author: trinadhk
ms.openlocfilehash: fd2beaa39f03d4f2342c94bf1cd8b8aea7440e62
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57780448"
---
# <a name="enable-backup-when-you-create-an-azure-virtual-machine"></a>Ativar cópia de segurança, quando cria uma máquina virtual do Azure

Utilize o serviço de cópia de segurança do Azure para fazer cópias de segurança de máquinas virtuais do Azure (VMs). As VMs são uma cópia de segurança, de acordo com um agendamento especificado numa política de cópia de segurança e pontos de recuperação são criados a partir de cópias de segurança. Pontos de recuperação são armazenados nos cofres dos serviços de recuperação.

Este artigo fornece detalhes sobre como ativar a cópia de segurança enquanto está a criar uma máquina virtual (VM) no portal do Azure.  

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Se não tiver entrado à sua conta, inicie sessão para o [portal do Azure](https://portal.azure.com).
 
## <a name="create-a-vm-with-backup-configured"></a>Criar uma VM com a cópia de segurança configurada 

1. No canto superior esquerdo do portal do Azure, selecione **New**.

1. Selecione **computação**e, em seguida, selecione uma imagem da VM.

1. Introduza as informações para a VM. O nome de utilizador e palavra-passe que fornecer serão utilizados para iniciar sessão na VM. Quando tiver terminado, selecione **OK**. 

1. Selecione um tamanho para a VM.  

1. Sob **configurações** > **cópia de segurança**, selecione **ativado** para abrir as definições de configuração de cópia de segurança.

   - Para aceitar os valores predefinidos, selecione **OK** sobre o **definições** página. Em seguida, vamos para o **resumo** página para criar a VM. Ignore os passos 6 a 8.
   - Para alterar os valores de configuração de cópia de segurança, siga os passos seguintes.  

1. Crie ou selecione um cofre de serviços de recuperação para armazenar as cópias de segurança da VM. Se estiver a criar um cofre dos serviços de recuperação, pode escolher um grupo de recursos do cofre.  

    ![Definições de configuração de cópia de segurança na página de criação de VM](./media/backup-during-vm-creation/create-vm-backup-config.png) 

    > [!NOTE] 
    > O grupo de recursos para o Cofre dos serviços de recuperação pode ser diferente do que o grupo de recursos para a VM.  

1. Por predefinição, uma política de cópia de segurança está selecionada para proteger a VM. Uma política de cópia de segurança especifica a frequência de tirar instantâneos de cópia de segurança e durante quanto tempo para manter essas cópias de segurança. 

   - Pode aceitar a política predefinida, ou pode criar ou selecionar uma política de cópia de segurança diferente. 
   - Para editar a política de cópia de segurança, selecione **política de cópia de segurança** e altere os valores.  

1. Quando tiver terminado de definir os valores de configuração de cópia de segurança, selecione **OK** sobre o **definições** página.  

1. Sobre o **resumo** página, após a validação foi aprovada, selecione **criar** para criar uma VM que utiliza as definições de cópia de segurança configuradas. 

## <a name="start-a-backup-after-creating-the-vm"></a>Iniciar uma cópia de segurança depois de criar a VM 

Mesmo que tiver configurado uma política de cópia de segurança para a sua VM, é uma boa prática para criar uma cópia de segurança inicial. 

Após a conclusão do modelo de criação da VM, aceda a **Operations** no menu à esquerda e selecione **cópia de segurança** para ver os detalhes de cópia de segurança para a máquina virtual. Pode utilizar esta página para:

- Acione uma cópia de segurança a pedido.
- Restaure uma VM completa ou de todos os respetivos discos.
- Restaure ficheiros a partir de uma cópia de segurança VM.
- Altere a política de cópia de segurança associada à VM.  

## <a name="use-a-resource-manager-template-to-deploy-a-protected-vm"></a>Utilizar um modelo do Resource Manager para implementar uma VM protegida

Os passos anteriores explicam como utilizar o portal do Azure para criar uma máquina virtual e protegê-lo num cofre dos serviços de recuperação. Para implementar uma ou mais máquinas virtuais e protegê-los num cofre dos serviços de recuperação rapidamente, ver o modelo [implementar uma VM do Windows e ativar cópia de segurança](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/).

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes 

### <a name="which-vm-images-support-backup-configuration-during-vm-creation"></a>Quais imagens VM suportam a configuração de cópia de segurança durante a criação de VM?

As seguintes imagens de núcleos, publicadas pela Microsoft são suportadas para ativar a cópia de segurança durante a criação da VM. Para outras VMs, pode ativar a cópia de segurança após a VM é criada. Para obter mais informações, consulte [ativar cópia de segurança após a VM é criada](quick-backup-vm-portal.md).

- **Windows** -Windows Server 2016 Datacenter, o núcleo do Windows Server 2016 Datacenter, Windows Server 2012 Datacenter, Windows Server 2012 R2 Datacenter, Windows Server 2008 R2 SP1 
- **Ubuntu** -Ubuntu Server 17.10, Ubuntu Server 17.04, Ubuntu Server 16.04 (LTS), Ubuntu Server 14.04 (LTS) 
- **Red Hat** -RHEL 6.7, 6.8, 6.9, 7.2, 7.3, 7.4 
- **SUSE** -SP4 do SUSE Linux Enterprise Server 11, 12 SP2, 12 SP3 
- **Debian** -Debian 8, Debian 9 
- **CentOS** - CentOS 6.9, CentOS 7.3 
- **Oracle Linux** - Oracle Linux 6.7, 6.8, 6.9, 7.2, 7.3 
 
### <a name="is-the-backup-cost-included-in-the-vm-cost"></a>É o custo de cópia de segurança incluído no custo VM? 

Não. Os custos de cópia de segurança são separados dos custos de uma VM. Para obter mais informações sobre os preços de cópia de segurança, consulte [preços de cópia de segurança do Azure](https://azure.microsoft.com/pricing/details/backup/).
 
### <a name="which-permissions-are-required-to-enable-backup-on-a-vm"></a>Que permissões são necessárias para ativar a cópia de segurança numa VM? 

Se for um contribuinte VM, pode ativar a cópia de segurança na VM. Se estiver a utilizar uma função personalizada, terá as seguintes permissões para ativar cópia de segurança na VM: 

- Microsoft.RecoveryServices/Vaults/write 
- Microsoft.RecoveryServices/Vaults/read 
- Microsoft.RecoveryServices/locations/* 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write 
- Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write 
- Microsoft.RecoveryServices/Vaults/backupPolicies/read 
- Microsoft.RecoveryServices/Vaults/backupPolicies/write 
 
Se o Cofre de serviços de recuperação e a VM tiverem grupos de recursos diferente, certifique-se de que tem permissões de escrita no grupo de recursos para o Cofre dos serviços de recuperação.  

## <a name="next-steps"></a>Passos Seguintes 

Agora que protegeu a sua VM, consulte os seguintes artigos para saber como gerir e restaurar VMs:

- [Gerir e monitorizar as máquinas virtuais](backup-azure-manage-vms.md) 
- [Monitorizar máquinas virtuais](backup-azure-arm-restore-vms.md) 

---
title: Ativar cópia de segurança de VM do Azure durante a criação
description: Veja os passos para ativar a cópia de segurança da máquina virtual do Azure durante o processo de criação.
services: backup, virtual-machines
author: rayne-wiselman
manager: carmonm
tags: azure-resource-manager, virtual-machine-backup
ms.service: backup, virtual-machines
ms.topic: conceptual
ms.date: 01/08/2018
ms.author: trinadhk
ms.openlocfilehash: 0cb1d925d4f9c67439b3c96a85088102c7d0e05c
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56737200"
---
# <a name="enable-backup-during-azure-virtual-machine-creation"></a>Ativar cópia de segurança durante a criação da máquina virtual do Azure 

O serviço de cópia de segurança do Azure fornece uma interface para criar e configurar cópias de segurança para a cloud. Proteger os seus dados criar cópias de segurança, chamadas de pontos de recuperação, em intervalos regulares. O Azure Backup cria pontos de recuperação que podem ser armazenados em cofres de recuperação georredundantes. Este artigo fornece detalhes sobre como ativar a cópia de segurança durante a criação de uma máquina virtual (VM) no portal do Azure.  

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure 

Se não estiver já em entrar para a sua conta, inicie sessão para o [portal do Azure](http://portal.azure.com).
 
## <a name="create-virtual-machine-with-backup-configured"></a>Criar máquina virtual com cópia de segurança configurada 

1. No canto superior esquerdo do portal do Azure, clique em **New**. 

2. Selecione **computação**e, em seguida, selecione uma imagem da máquina virtual.   

3. Introduza as informações da máquina virtual. O nome de utilizador e palavra-passe fornecida é utilizado para iniciar sessão para a máquina virtual. Quando terminar, clique em **OK**. 

4. Selecione um tamanho para a VM.  

5. Sob **definições > cópia de segurança**, clique em **ativado** para abrir as definições de configuração de cópia de segurança. Pode aceitar valores predefinidos e clique em **OK** na página de definições para avançar para a página de resumo para criar a VM. Se pretender alterar os valores, siga os passos seguintes.  

6. Crie ou selecione um cofre dos serviços de recuperação, que contém as cópias de segurança da máquina virtual. Se estiver a criar um cofre dos serviços de recuperação, pode escolher um grupo de recursos do cofre.  

    ![Configuração de cópia de segurança numa vm criar página](./media/backup-during-vm-creation/create-vm-backup-config.png) 

    > [!NOTE] 
    > O grupo de recursos para o Cofre dos serviços de recuperação pode ser diferente do que o grupo de recursos para a máquina virtual.  
    > 
    > 

7. Por predefinição, uma política de cópia de segurança está selecionada para que possa proteger rapidamente a VM. Uma política de cópia de segurança especifica a frequência de tirar instantâneos de cópia de segurança e durante quanto tempo para manter essas cópias de segurança. Pode aceitar a política predefinida, ou pode criar ou selecionar uma política de cópia de segurança diferente. Para editar a política de cópia de segurança, selecione **política de cópia de segurança** e altere os valores da política.  

8. Quando estiver satisfeito com os valores de configuração de cópia de segurança, na página Definições, clique em **OK**.  

9. Na página Resumo, uma vez que a validação passou, clique em **criar** para criar uma máquina virtual que utiliza as definições de cópia de segurança configuradas. 

## <a name="initiate-a-backup-after-creating-the-vm"></a>Iniciar uma cópia de segurança depois de criar a VM 

Embora a política de cópia de segurança tiver sido criada, é boa prática para criar uma cópia de segurança inicial. Para ver os detalhes de cópia de segurança para a virtual máquina uma vez a conclusão de modelo de criação de VM, do **Operations** definir no menu do lado esquerdo, clique em **cópia de segurança**. Pode utilizar este para acionar uma cópia de segurança a pedido, restaurar uma VM completa ou de todos os discos, restaurar ficheiros a partir de cópia de segurança VM ou alterar a política de cópia de segurança associada à máquina virtual.  

## <a name="using-a-resource-manager-template-to-deploy-a-protected-vm"></a>Utilizar um modelo do Resource Manager para implementar uma VM protegida

Os passos anteriores explicam como utilizar o portal do Azure para criar uma máquina virtual e protegê-lo para um cofre dos serviços de recuperação. Se quiser rapidamente implementar uma ou mais máquinas virtuais e protegê-los para um cofre dos serviços de recuperação, consulte o modelo [implementar uma VM do Windows e ativar cópia de segurança](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/).

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes 

### <a name="which-vm-images-enable-backup-at-the-time-of-vm-creation"></a>Quais imagens VM ativar cópia de segurança no momento da criação da VM? 

A seguinte lista de imagens de núcleo publicadas pela Microsoft são suportadas para ativar a cópia de segurança durante a criação da VM. Para outras VMs, pode ativar a cópia de segurança assim que a VM é criada. Saiba mais [ativar cópia de segurança após a VM é criada](quick-backup-vm-portal.md) 

- **Windows** -Centro de dados do Windows Server 2016, o núcleo do Centro de dados do Windows Server 2016, Windows Server 2012 DataCenter, Windows Server 2012 R2 DataCenter, Windows Server 2008 R2 SP1 
- **Ubuntu** -1710 do Ubuntu Server, Ubuntu Server 1704, servidor UUbuntu 1604(LTS), 1404(LTS) Ubuntu Server 
- **Red Hat** -RHEL 6.7, 6.8, 6.9, 7.2, 7.3, 7.4 
- **SUSE** -SP4 do SUSE Linux Enterprise Server 11, 12 SP2, 12 SP3 
- **Debian** -Debian 8, Debian 9 
- **CentOS** - CentOS 6.9, CentOS 7.3 
- **Oracle Linux** - Oracle Linux 6.7, 6.8, 6.9, 7.2, 7.3 
 
### <a name="is-backup-cost-included-in-the-vm-cost"></a>É o custo de cópia de segurança incluído no custo VM? 

Não, os custos de cópia de segurança estão separadas ou diferentes, dos custos de máquinas virtuais. Para obter mais informações sobre os preços da cópia de segurança, consulte a [preços de cópia de segurança site](https://azure.microsoft.com/pricing/details/backup/).
 
### <a name="which-permissions-are-required-to-enable-backup-on-a-vm"></a>Que permissões são necessárias para ativar a cópia de segurança numa VM? 

Se for um contribuinte de máquina virtual, pode ativar a cópia de segurança na VM. Se estiver a utilizar uma função personalizada, terá as seguintes permissões para ativar com êxito a cópia de segurança na VM. 

- Microsoft.RecoveryServices/Vaults/write 
- Microsoft.RecoveryServices/Vaults/read 
- Microsoft.RecoveryServices/locations/* 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write 
- Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write 
- Microsoft.RecoveryServices/Vaults/backupPolicies/read 
- Microsoft.RecoveryServices/Vaults/backupPolicies/write 
 
Se o seu Cofre dos serviços de recuperação e a máquina virtual tiverem grupos de recursos diferente, certifique-se de que tem permissões de escrita no grupo de recursos de Cofre de serviços de recuperação.  

## <a name="next-steps"></a>Passos Seguintes 

Agora que proteger a VM, consulte os seguintes artigos para saber mais sobre tarefas de gestão da VM e como restaurar VMs. 

- [Gerir e monitorizar as máquinas virtuais](backup-azure-manage-vms.md) 
- [Monitorizar máquinas virtuais](backup-azure-arm-restore-vms.md) 

---
title: Ativar cópia de segurança, quando cria uma VM do Azure com o Azure Backup
description: Descreve como ativar a cópia de segurança, quando cria uma VM do Azure com o Azure Backup.
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: raynew
ms.openlocfilehash: d96b898c8f72abd7e4eb3522ae046e9fc926f387
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403583"
---
# <a name="enable-backup-when-you-create-an-azure-vm"></a>Ativar a cópia de segurança quando cria uma VM do Azure

Utilize o serviço de cópia de segurança do Azure para fazer cópias de segurança de máquinas virtuais do Azure (VMs). As VMs são uma cópia de segurança, de acordo com um agendamento especificado numa política de cópia de segurança e pontos de recuperação são criados a partir de cópias de segurança. Pontos de recuperação são armazenados nos cofres dos serviços de recuperação.

Este artigo fornece detalhes sobre como ativar a cópia de segurança, quando cria uma máquina virtual (VM) no portal do Azure.  

## <a name="before-you-start"></a>Antes de começar

- [Verificar](backup-support-matrix-iaas.md#supported-backup-actions) que sistemas operativos são suportados se ativar a cópia de segurança quando criar uma VM.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Se não tiver entrado à sua conta, inicie sessão para o [portal do Azure](https://portal.azure.com).
 
## <a name="create-a-vm-with-backup-configured"></a>Criar uma VM com a cópia de segurança configurada

1. No portal do Azure, clique em **criar um recurso**.

2. No Azure Marketplace, clique em **computação**e, em seguida, selecione uma imagem de VM.

3. Configurar a VM de acordo com o [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal) ou [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal) instruções.

4. Sobre o **gerenciamento** separador **ativar cópia de segurança**, clique em **no**.
5. Backups de cópia de segurança do Azure para um cofre dos serviços de recuperação. Clique em **criar novo** se não tiver um cofre existente.
6. Aceite o nome do cofre sugeridas ou especificar a sua própria.
7. Especifique ou crie um grupo de recursos em que o Cofre será localizado. O cofre do grupo de recursos pode ser diferente do grupo de recursos VM.

    ![Ativar cópia de segurança para uma VM](./media/backup-during-vm-creation/enable-backup.png) 

8. Aceite a política de cópia de segurança predefinida ou modifique as definições.
    - Uma política de cópia de segurança especifica a frequência de tirar instantâneos de cópia de segurança da VM e durante quanto tempo para manter essas cópias de segurança. 
    - A política predefinida cria uma cópia de segurança da VM uma vez por dia.
    - Pode personalizar a sua própria política de cópia de segurança para uma VM do Azure efetuar cópias de segurança diárias ou semanais.
    - [Saiba mais](backup-azure-vms-introduction.md#backup-and-restore-considerations) sobre as considerações de cópia de segurança para VMs do Azure.
    - [Saiba mais](backup-instant-restore-capability.md) sobre instante restaurar a funcionalidade.

      ![Política de cópia de segurança predefinida](./media/backup-during-vm-creation/daily-policy.png) 


## <a name="start-a-backup-after-creating-the-vm"></a>Iniciar uma cópia de segurança depois de criar a VM 

A cópia de segurança VM será executado de acordo com a política de cópia de segurança. No entanto, recomendamos que execute uma cópia de segurança inicial. 

Depois da VM é criada, faça o seguinte:

1. Nas propriedades da VM, clique em **cópia de segurança**. O estado da VM é cópia de segurança inicial pendente até que a cópia de segurança inicial é executada
2. Clique em **criar cópias de segurança agora** para executar uma cópia de segurança a pedido.

    ![Executar cópias de segurança a pedido](./media/backup-during-vm-creation/run-backup.png) 

## <a name="use-a-resource-manager-template-to-deploy-a-protected-vm"></a>Utilizar um modelo do Resource Manager para implementar uma VM protegida

Os passos anteriores explicam como utilizar o portal do Azure para criar uma máquina virtual e protegê-lo num cofre dos serviços de recuperação. Para implementar uma ou mais VMs e protegê-los num cofre dos serviços de recuperação rapidamente, ver o modelo [implementar uma VM do Windows e ativar cópia de segurança](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/).



## <a name="next-steps"></a>Passos Seguintes 

Agora que protegeu a sua VM, saiba como gerir e restaurá-los.

- [Gerir e monitorizar VMs](backup-azure-manage-vms.md) 
- [Restaurar VMs](backup-azure-arm-restore-vms.md) 

Se encontrar algum problema [rever](backup-azure-vms-troubleshoot.md) o guia de resolução de problemas.

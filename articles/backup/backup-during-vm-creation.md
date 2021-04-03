---
title: Ativar a cópia de segurança quando cria uma VM do Azure
description: Descreve como ativar o backup quando cria um Azure VM com Azure Backup.
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: ad81300545686d61f42cdd8684e502c937b4fd43
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89377340"
---
# <a name="enable-backup-when-you-create-an-azure-vm"></a>Ativar a cópia de segurança quando cria uma VM do Azure

Utilize o serviço de backup Azure para fazer backup de máquinas virtuais Azure (VMs). Os VMs são apoiados de acordo com um calendário especificado numa política de backup, e os pontos de recuperação são criados a partir de backups. Os pontos de recuperação são armazenados em cofres dos Serviços de Recuperação.

Este artigo detalha como ativar a cópia de segurança quando cria uma máquina virtual (VM) no portal Azure.  

## <a name="before-you-start"></a>Antes de começar

- [Verifique](backup-support-matrix-iaas.md#supported-backup-actions) quais os sistemas operativos suportados se ativar a cópia de segurança quando criar um VM.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Se ainda não se inscreveu na sua conta, inscreva-se no [portal Azure.](https://portal.azure.com)

## <a name="create-a-vm-with-backup-configured"></a>Criar um VM com backup configurado

1. No portal Azure, selecione **Criar um recurso**.

2. No Azure Marketplace, **selecione Compute** e, em seguida, selecione uma imagem VM.

3. Configurar o VM de acordo com as instruções [do Windows](../virtual-machines/windows/quick-create-portal.md) ou [Linux.](../virtual-machines/linux/quick-create-portal.md)

4. No **separador Gestão,** em **Ativar a cópia de segurança**, selecione **On**.
5. Backups Azure para um cofre dos Serviços de Recuperação. **Selecione Create New** se não tiver um cofre existente.
6. Aceite o nome do cofre sugerido ou especifique o seu próprio.
7. Especifique ou crie um grupo de recursos no qual o cofre será localizado. O cofre do grupo de recursos pode ser diferente do grupo de recursos VM.

    ![Ativar a cópia de segurança para um VM](./media/backup-during-vm-creation/enable-backup.png)

8. Aceite a política de backup predefinido ou modifique as definições.
    - Uma política de backup especifica a frequência com que tirar fotos de backup do VM e quanto tempo para manter essas cópias de backup.
    - A política por defeito confirma o VM uma vez por dia.
    - Pode personalizar a sua própria política de backup para um Azure VM para fazer backups diariamente ou semanalmente.
    - [Saiba mais](backup-azure-vms-introduction.md#backup-and-restore-considerations) sobre considerações de backup para Azure VMs.
    - [Saiba mais](backup-instant-restore-capability.md) sobre a funcionalidade de restauro instantâneo.

      ![Política de backup predefinido](./media/backup-during-vm-creation/daily-policy.png)

>[!NOTE]
>[SSE e PMK são os métodos de encriptação padrão](backup-encryption.md) para VMs Azure. A Azure Backup suporta cópia de segurança e restauro destes VMs Azure.

## <a name="azure-backup-resource-group-for-virtual-machines"></a>Grupo de recursos de backup Azure para máquinas virtuais

O serviço De Backup cria um grupo de recursos separado (RG), diferente do grupo de recursos do VM para armazenar a coleção de pontos de restauração (RPC). O RPC abriga os pontos de recuperação instantâneos de VMs geridos. O formato de nomeação padrão do grupo de recursos criado pelo serviço Desarmamento é: `AzureBackupRG_<Geo>_<number>` . Por exemplo: *AzureBackupRG_northeurope_1*. Agora pode personalizar o nome do grupo De recurso criado por Azure Backup.

Pontos a notar:

1. Pode utilizar o nome padrão do RG ou editá-lo de acordo com os requisitos da sua empresa.
2. Você fornece o padrão de nome RG como entrada durante a criação de política de backup VM. O nome RG deve ser do seguinte formato: `<alpha-numeric string>* n <alpha-numeric string>` . 'n' é substituído por um inteiro (a partir de 1) e é utilizado para escalonar se o primeiro RG estiver cheio. Um RG pode ter um máximo de 600 RPCs hoje.
              ![Escolha o nome ao criar política](./media/backup-during-vm-creation/create-policy.png)
3. O padrão deve seguir as regras de nomeação RG abaixo e o comprimento total não deve exceder o comprimento máximo permitido do nome RG.
    1. Os nomes do grupo de recursos só permitem caracteres alfanuméricos, períodos, sublinhados, hífens e parênteses. Não podem terminar num período.
    2. Os nomes de grupos de recursos podem conter até 74 caracteres, incluindo o nome do RG e o sufixo.
4. O primeiro `<alpha-numeric-string>` é obrigatório enquanto o segundo após 'n' é opcional. Isto só se aplica se der um nome personalizado. Se não introduzir nada em nenhuma das caixas de texto, o nome predefinido é utilizado.
5. Pode editar o nome do RG modificando a política se e quando necessário. Se o padrão de nome for alterado, novos RPs serão criados no novo RG. No entanto, os antigos RPs continuarão a residir no antigo RG e não serão movidos, uma vez que a RP Collection não suporta movimento de recursos. Eventualmente, os RPs receberão lixo recolhido à medida que os pontos expirarem.
![Alterar nome ao modificar a política](./media/backup-during-vm-creation/modify-policy.png)
6. É aconselhável não bloquear o grupo de recursos criado para ser utilizado pelo serviço de backup.

Para configurar o grupo de recursos de backup Azure para máquinas virtuais utilizando o PowerShell, consulte a [Criação do grupo de recursos backup Azure durante a retenção de instantâneos](backup-azure-vms-automation.md#creating-azure-backup-resource-group-during-snapshot-retention).

## <a name="start-a-backup-after-creating-the-vm"></a>Inicie uma cópia de segurança depois de criar o VM

A sua cópia de segurança VM será executada de acordo com a sua política de backup. No entanto, recomendamos que faça uma cópia de segurança inicial.

Após a criação do VM, faça o seguinte:

1. Nas propriedades VM, selecione **Backup**. O estado de VM é cópia de segurança inicial pendente até que a cópia de segurança inicial seja executado.
2. **Selecione Backup agora** para executar uma cópia de segurança a pedido.

    ![Executar uma cópia de segurança a pedido](./media/backup-during-vm-creation/run-backup.png)

## <a name="use-a-resource-manager-template-to-deploy-a-protected-vm"></a>Utilize um modelo de Gestor de Recursos para implementar um VM protegido

Os passos anteriores explicam como usar o portal Azure para criar uma máquina virtual e protegê-la num cofre dos Serviços de Recuperação. Para implementar rapidamente um ou mais VMs e protegê-los num cofre dos Serviços de Recuperação, consulte o modelo [Implementar um VM do Windows e ativar a cópia de segurança](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/).

## <a name="next-steps"></a>Passos seguintes

Agora que protegeste o teu VM, aprende a geri-los e a restaurá-los.

- [Gerir e monitorizar VMs](backup-azure-manage-vms.md)
- [Restaurar VMs](backup-azure-arm-restore-vms.md)

Se encontrar algum problema, [reveja](backup-azure-vms-troubleshoot.md) o guia de resolução de problemas.

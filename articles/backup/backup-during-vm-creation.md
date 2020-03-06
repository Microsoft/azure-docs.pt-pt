---
title: Ativar a cópia de segurança quando cria uma VM do Azure
description: Descreve como ativar a cópia de segurança quando cria um VM Azure com Backup Azure.
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: 0cfea6579791c4fd23c1b7acdfe722d57b5ec2fd
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78363755"
---
# <a name="enable-backup-when-you-create-an-azure-vm"></a>Ativar a cópia de segurança quando cria uma VM do Azure

Utilize o serviço de backup Azure para fazer backup de máquinas virtuais Azure (VMs). Os VMs são apoiados de acordo com um calendário especificado numa política de backup, e os pontos de recuperação são criados a partir de backups. Os pontos de recuperação estão guardados nos cofres dos Serviços de Recuperação.

Este artigo detalha como ativar a cópia de segurança quando cria uma máquina virtual (VM) no portal Azure.  

## <a name="before-you-start"></a>Antes de começar

- [Verifique](backup-support-matrix-iaas.md#supported-backup-actions) quais os sistemas operativos suportados se ativa rés em suporte quando criar um VM.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Se ainda não se inscreveu na sua conta, inscreva-se no [portal Azure](https://portal.azure.com).

## <a name="create-a-vm-with-backup-configured"></a>Criar um VM com Backup configurado

1. No portal Azure, clique em **Criar um recurso.**

2. No Mercado Azure, clique em **Compute**e, em seguida, selecione uma imagem VM.

3. Instale o VM de acordo com as instruções [do Windows](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal) ou [do Linux.](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal)

4. No separador **'Gestão',** em **'Backup',** clique **em .**
5. Reforços Azure para um cofre dos Serviços de Recuperação. Clique em **Criar Novo** se não tiver um cofre existente.
6. Aceite o nome do cofre sugerido ou especifique o seu.
7. Especifique ou crie um grupo de recursos no qual o cofre será localizado. O cofre do grupo de recursos pode ser diferente do grupo de recursos VM.

    ![Ativar backup para um VM](./media/backup-during-vm-creation/enable-backup.png)

8. Aceite a política de cópia de segurança predefinida ou modifique as definições.
    - Uma política de backup especifica com que frequência tirar fotografias de backup do VM e quanto tempo para manter essas cópias de reserva.
    - A política de incumprimento apoia o VM uma vez por dia.
    - Pode personalizar a sua própria política de backup para um VM Azure para receber backups diariamente ou semanais.
    - [Saiba mais](backup-azure-vms-introduction.md#backup-and-restore-considerations) sobre considerações de backup para VMs Azure.
    - [Saiba mais](backup-instant-restore-capability.md) sobre a funcionalidade de restauro instantâneo.

      ![Política de backup padrão](./media/backup-during-vm-creation/daily-policy.png)

## <a name="azure-backup-resource-group-for-virtual-machines"></a>Grupo de recursos de backup Azure para Máquinas Virtuais

O serviço de backup cria um grupo de recursos separado (RG), diferente do grupo de recursos do VM para armazenar a coleção de pontos de restauro (RPC). O RPC abriga os pontos de recuperação instantâneas de VMs geridos. O formato de nomeação padrão do grupo de recursos criado pelo serviço de backup é: `AzureBackupRG_<Geo>_<number>`. Por exemplo: *AzureBackupRG_northeurope_1*. Agora pode personalizar o nome do grupo Resource criado pela Azure Backup.

Pontos a serem observados:

1. Pode utilizar o nome padrão do RG ou editá-lo de acordo com os requisitos da sua empresa.
2. Você fornece o padrão de nome RG como entrada durante a criação de política de backup VM. O nome RG deve ser do seguinte formato: `<alpha-numeric string>* n <alpha-numeric string>`. 'n' é substituído por um inteiro (a partir de 1) e é utilizado para escalonar se o primeiro RG estiver cheio. Um RG pode ter um máximo de 600 RPCs hoje.
              ![Escolha o nome ao criar](./media/backup-during-vm-creation/create-policy.png) política
3. O padrão deve seguir as regras de nomeação RG abaixo e o comprimento total não deve exceder o comprimento máximo permitido do nome RG.
    1. Os nomes de grupos de recursos apenas permitem caracteres alfanuméricos, períodos, sublinhados, hífenes e parênteses. Não podem terminar num período.
    2. Os nomes de grupos de recursos podem conter até 74 caracteres, incluindo o nome do RG e o sufixo.
4. A primeira `<alpha-numeric-string>` é obrigatória, enquanto a segunda após 'n' é opcional. Isto só se aplica se der um nome personalizado. Se não introduzir nada em nenhuma das caixas de texto, o nome predefinido é utilizado.
5. Pode editar o nome do RG modificando a política se e quando necessário. Se o padrão de nome for alterado, novos RPs serão criados no novo RG. No entanto, os antigos RPs continuarão a residir no antigo RG e não serão movidos, uma vez que a RP Collection não suporta o movimento de recursos. Eventualmente, os RPs receberão lixo recolhido à medida que os pontos expirarem.
![Mudar de nome ao modificar](./media/backup-during-vm-creation/modify-policy.png) políticas
6. É aconselhável não bloquear o grupo de recursos criado para utilização pelo serviço de backup.

## <a name="start-a-backup-after-creating-the-vm"></a>Inicie uma cópia de segurança depois de criar o VM

O seu backup vm será executado de acordo com a sua política de backup. No entanto, recomendamos que faça uma cópia de segurança inicial.

Depois da criação do VM, faça o seguinte:

1. Nas propriedades VM, clique em **Backup**. O estado VM é backup inicial pendente até que a cópia de segurança inicial seja executado
2. Clique em **Back up agora** para executar uma cópia de segurança a pedido.

    ![Faça um backup a pedido](./media/backup-during-vm-creation/run-backup.png)

## <a name="use-a-resource-manager-template-to-deploy-a-protected-vm"></a>Use um modelo de Gestor de Recursos para implementar um VM protegido

Os passos anteriores explicam como usar o portal Azure para criar uma máquina virtual e protegê-la num cofre dos Serviços de Recuperação. Para implementar rapidamente um ou mais VMs e protegê-los num cofre dos Serviços de Recuperação, consulte o modelo [Implementar um VM do Windows e ativar a cópia](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/)de segurança .

## <a name="next-steps"></a>Passos seguintes

Agora que protegeu o seu VM, aprenda a geri-los e restaurá-los.

- [Gerir e monitorizar VMs](backup-azure-manage-vms.md)
- [Restaurar VMs](backup-azure-arm-restore-vms.md)

Se encontrar algum problema, [reveja](backup-azure-vms-troubleshoot.md) o guia de resolução de problemas.

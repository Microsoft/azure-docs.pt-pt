---
title: Eliminação acidental de ações de ficheiros Azure
description: Aprender a eliminar suavemente pode proteger as suas Partilhas de Ficheiros Azure de eliminação acidental.
ms.topic: conceptual
ms.date: 02/02/2020
ms.openlocfilehash: 09d74a135fc43a7758004d77af2ec4c478345a2c
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84122410"
---
# <a name="accidental-delete-protection-for-azure-file-shares-using-azure-backup"></a>Eliminação acidental de proteção para ações de ficheiros Azure utilizando backup azure

Para garantir proteção contra ciberataques ou eliminação acidental, a [eliminação suave](https://docs.microsoft.com/azure/storage/files/storage-files-prevent-file-share-deletion) está ativada para todas as ações de ficheiro saque a uma conta de armazenamento quando configura rés em reserva para qualquer participação de ficheiro na respetiva conta de armazenamento. Com a eliminação suave, mesmo que um ator malicioso apague a parte do ficheiro, o conteúdo e os pontos de recuperação da partilha de ficheiros (instantâneos) são retidos por um mínimo de 14 dias adicionais, permitindo a recuperação de ações de ficheiro sem perda de dados.  

O soft delete é suportado apenas para contas de armazenamento padrão e premium e é atualmente ativado do lado azure backup [nestas regiões](azure-file-share-support-matrix.md).

O gráfico de fluxo seguinte mostra os diferentes passos e estados de um item de backup quando a eliminação suave é ativada para ações de ficheiros numa conta de armazenamento:

 ![Soft delete flow chart](./media/soft-delete-afs/soft-delete-flow-chart.png)

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="when-will-soft-delete-be-enabled-for-file-shares-in-my-storage-account"></a>Quando será que a eliminação suave será ativada para ações de ficheiros na minha conta de armazenamento?

Quando configura a cópia de segurança pela primeira vez para qualquer participação de ficheiro numa conta de armazenamento, o serviço De backup Azure permite eliminar suavemente todas as ações de ficheiros na respetiva conta de armazenamento.

### <a name="can-i-configure-the-number-of-days-for-which-my-snapshots-and-restore-points-will-be-retained-in-soft-deleted-state-after-i-delete-the-file-share"></a>Posso configurar o número de dias para os quais os meus instantâneos e pontos de restauro serão mantidos em estado de soft-delete depois de apagar a parte do ficheiro?

Sim, pode definir o período de retenção de acordo com os seus requisitos. [Este documento](https://docs.microsoft.com/azure/storage/files/storage-files-enable-soft-delete?tabs=azure-portal) explica os passos para configurar o período de retenção. Para contas de armazenamento com ações de ficheiros apoiados, a definição mínima de retenção deve ser de 14 dias.

### <a name="does-azure-backup-reset-my-retention-setting-because-i-configured-it-to-less-than-14-days"></a>O Azure Backup repõe a minha definição de retenção porque o configurei para menos de 14 dias?

Do ponto de vista da segurança, recomendamos que tenha uma retenção mínima de 14 dias para contas de armazenamento com ações de ficheiros apoiados. Assim, em cada trabalho de backup, se o Azure Backup identificar a definição como inferior a 14 dias, repõe-o para 14 dias.

### <a name="what-is-the-cost-incurred-during-the-retention-period"></a>Qual é o custo incorrido durante o período de retenção?

Durante o período de eliminação suave, o custo de instância protegida e o custo de armazenamento instantâneo permanecerão como está.  Além disso, será cobrado pela capacidade usada à taxa normal de ações de ficheiros padrão e à taxa de armazenamento instantânea para ações de ficheiropremium.

### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-deleted-state"></a>Posso executar uma operação de restauro quando os meus dados estão em estado suave apagado?

Primeiro, é necessário eliminar a parte de ficheiro suave mente apagada para efetuar operações de restauro. A operação de exclusão trará a parte do ficheiro para o estado de back-up onde pode restaurar a qualquer momento. Para aprender a desapagar a sua parte de ficheiro, visite [este link](https://docs.microsoft.com/azure/storage/files/storage-files-enable-soft-delete?tabs=azure-portal#restore-soft-deleted-file-share) ou veja o Script de Partilha de [Ficheiros Não Excluir](./scripts/backup-powershell-script-undelete-file-share.md).

### <a name="how-can-i-purge-the-data-of-a-file-share-in-a-storage-account-that-has-at-least-one-protected-file-share"></a>Como posso expurgar os dados de uma parte de ficheiro numa conta de armazenamento que tem pelo menos uma partilha de ficheiros protegida?

Se tiver pelo menos uma parte de ficheiro protegida numa conta de armazenamento, significa que a eliminação suave está ativada para todas as ações de ficheiro nessa conta e os seus dados serão retidos durante 14 dias após a operação de eliminação. Mas se quiser expurgar os dados imediatamente e não quiser que sejam retidos, siga estes passos:

1. Se já apagou a parte do ficheiro enquanto o Soft Delete estava ativado, primeiro desapague a parte do ficheiro do [portal Ficheiros](https://docs.microsoft.com/azure/storage/files/storage-files-enable-soft-delete?tabs=azure-portal#restore-soft-deleted-file-share) ou utilizando o Script de Partilha de [Ficheiros Não excluir](./scripts/backup-powershell-script-undelete-file-share.md).
2. Desative a eliminação suave das ações de ficheiro na sua conta de armazenamento seguindo os passos mencionados [neste documento](https://docs.microsoft.com/azure/storage/files/storage-files-enable-soft-delete?tabs=azure-portal#disable-soft-delete).
3. Agora apague a parte do ficheiro cujo conteúdo pretende expurgar imediatamente.

>[!NOTE]
>Deve executar o passo 2 antes que o próximo trabalho de backup programado seja contra a parte de ficheiro protegida na sua conta de armazenamento. Porque sempre que o trabalho de backup funciona, ele reativa a eliminação suave para todas as ações de ficheiro na conta de armazenamento.

>[!WARNING]
>Depois de desativar a eliminação suave no passo 2, qualquer operação de eliminação realizada contra as ações do ficheiro é uma operação de eliminação permanente. Isto significa que se eliminar acidentalmente a parte do ficheiro backed depois de desativar a eliminação suave, perderá todas as suas fotos e não conseguirá recuperar os seus dados.

### <a name="in-the-context-of-a-file-shares-soft-delete-setting-what-changes-does-azure-backup-do-when-i-unregister-a-storage-account"></a>No contexto da definição suave de eliminação de uma parte de ficheiro, que alterações faz a Backup Azure quando não registo uma conta de armazenamento?

No momento da não inscrição, a Azure Backup verifica a definição do período de retenção para as ações de ficheiros e, se for superior a 14 dias ou menos de 14 dias, deixa a retenção tal como está. No entanto, se a retenção for de 14 dias, consideramos que está ativada pela Azure Backup e por isso desativamos a eliminação suave durante o processo de não registo. Se pretender desregistar a conta de armazenamento mantendo a definição de retenção tal como está, volte a atilá-la a partir do painel da conta de armazenamento após completar o não registo. Pode consultar [este link](https://docs.microsoft.com/azure/storage/files/storage-files-enable-soft-delete?tabs=azure-portal#restore-soft-deleted-file-share) para os passos de configuração.

## <a name="next-steps"></a>Próximos passos

Saiba como [backup Azure File Shares do portal Azure](backup-afs.md)

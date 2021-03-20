---
title: Proteção acidental de exclusão para ações de ficheiros Azure
description: Aprenda a eliminar suavemente pode proteger as suas Azure File Shares da eliminação acidental.
ms.topic: conceptual
ms.date: 02/02/2020
ms.custom: references_regions
ms.openlocfilehash: 52a116320e07c25d4ee7f00b8063ca15faeb8560
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "89179917"
---
# <a name="accidental-delete-protection-for-azure-file-shares-using-azure-backup"></a>Eliminar acidentalmente a proteção para ações de ficheiros Azure usando o Backup Azure

Para fornecer proteção contra ciberataques ou eliminação acidental, [a eliminação suave](../storage/files/storage-files-prevent-file-share-deletion.md) é ativada para todas as ações de ficheiros numa conta de armazenamento quando configurar a cópia de segurança para qualquer partilha de ficheiros na respetiva conta de armazenamento. Com a eliminação suave, mesmo que um ator malicioso elimine a partilha de ficheiros, o conteúdo e pontos de recuperação da partilha de ficheiros (instantâneos) são retidos por um mínimo de 14 dias adicionais, permitindo a recuperação de partilhas de ficheiros sem perda de dados.  A eliminação suave é suportada para contas de armazenamento standard e premium e a configuração é ativada pela Azure Backup para todas as contas de armazenamento que hospedam ações de ficheiros com suporte.

O gráfico de fluxo a seguir mostra os diferentes passos e estados de um item de backup quando a eliminação suave é ativada para ações de ficheiros numa conta de armazenamento:

 ![Gráfico de fluxo de eliminação suave](./media/soft-delete-afs/soft-delete-flow-chart.png)

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="when-will-soft-delete-be-enabled-for-file-shares-in-my-storage-account"></a>Quando é que a eliminação suave será ativada para ações de ficheiros na minha conta de armazenamento?

Quando configurar a cópia de segurança pela primeira vez para qualquer ação de ficheiro numa conta de armazenamento, o serviço Azure Backup permite a eliminação suave de todas as ações de ficheiros na respetiva conta de armazenamento.

### <a name="can-i-configure-the-number-of-days-for-which-my-snapshots-and-restore-points-will-be-retained-in-soft-deleted-state-after-i-delete-the-file-share"></a>Posso configurar o número de dias para os quais as minhas fotos e pontos de restauro serão mantidos em estado de eliminação suave após eu apagar a partilha de ficheiros?

Sim, pode definir o período de retenção de acordo com os seus requisitos. [Este documento](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal) explica os passos para configurar o período de retenção. Para contas de armazenamento com ações de ficheiros com reserva, a definição mínima de retenção deve ser de 14 dias.

### <a name="does-azure-backup-reset-my-retention-setting-because-i-configured-it-to-less-than-14-days"></a>O Azure Backup repôs a minha definição de retenção porque o configurei para menos de 14 dias?

Do ponto de vista da segurança, recomendamos a retenção mínima de 14 dias para contas de armazenamento com ações de ficheiros com apoio. Assim, em cada trabalho de backup, se o Azure Backup identificar a definição de menos de 14 dias, ele repõe-o para 14 dias.

### <a name="what-is-the-cost-incurred-during-the-retention-period"></a>Qual é o custo incorrido durante o período de retenção?

Durante o período de apagação suave, o custo de caso protegido e o custo de armazenamento instantâneo permanecerão como está.  Além disso, você será cobrado pela capacidade usada à taxa regular para ações de ficheiros padrão e a uma taxa de armazenamento instantânea para ações de ficheiros premium.

### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-deleted-state"></a>Posso realizar uma operação de restauro quando os meus dados estão em estado de apagamento suave?

Primeiro, é necessário desembolsar a partilha de ficheiros suavemente eliminada para realizar operações de restauro. A operação undelete trará a partilha de ficheiros para o estado de apoio onde pode restaurar a qualquer ponto no tempo. Para aprender a desdelear a sua partilha de ficheiros, visite [este link](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#restore-soft-deleted-file-share) ou veja o Script de Partilha [de Ficheiros Undelete](./scripts/backup-powershell-script-undelete-file-share.md).

### <a name="how-can-i-purge-the-data-of-a-file-share-in-a-storage-account-that-has-at-least-one-protected-file-share"></a>Como posso expurgar os dados de uma parte de um ficheiro numa conta de armazenamento que tem pelo menos uma partilha de ficheiros protegida?

Se tiver pelo menos uma parte de ficheiro protegida numa conta de armazenamento, significa que a eliminação suave está ativada para todas as ações de ficheiros nessa conta e os seus dados serão retidos durante 14 dias após a operação de eliminação. Mas se quiser expurgar os dados imediatamente e não quiser que sejam retidos, siga estes passos:

1. Se já eliminou a partilha de ficheiros enquanto o Soft Delete estava ativado, em seguida, desembolsar a partilha de ficheiros do [portal Ficheiros](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#restore-soft-deleted-file-share) ou utilizando o [Script de Partilha de Ficheiros Undelete](./scripts/backup-powershell-script-undelete-file-share.md).
2. Desative a exclusão suave para ações de ficheiros na sua conta de armazenamento, seguindo os passos mencionados [neste documento.](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#disable-soft-delete)
3. Agora elimine a partilha de ficheiros cujo conteúdo pretende expurgar imediatamente.

>[!NOTE]
>Deve executar o passo 2 antes que o próximo trabalho de reserva programado seja contra a parte de ficheiros protegidos na sua conta de armazenamento. Porque sempre que o trabalho de backup funciona, ele re-permite a eliminação suave para todas as ações de ficheiros na conta de armazenamento.

>[!WARNING]
>Depois de desativar a eliminação suave no passo 2, qualquer operação de eliminação realizada contra as ações do ficheiro é uma operação de eliminação permanente. Por isso, se eliminar acidentalmente a partilha de ficheiros com ressarcimento depois de desativar a eliminação suave, perderá todas as suas fotos e não conseguirá recuperar os seus dados.

### <a name="in-the-context-of-a-file-shares-soft-delete-setting-what-changes-does-azure-backup-do-when-i-unregister-a-storage-account"></a>No contexto da definição de exclusão suave de uma partilha de ficheiros, que alterações faz o Azure Backup quando não registo uma conta de armazenamento?

No momento da não inscrição, o Azure Backup verifica a definição do período de retenção para ações de ficheiros e se for superior a 14 dias ou menos de 14 dias, deixa a retenção tal como está. No entanto, se a retenção for de 14 dias, consideramos que está ativada pela Azure Backup e por isso desativamos a eliminação suave durante o processo de não registo. Se pretender desativar a conta de armazenamento mantendo a definição de retenção tal como está, volte a a capacitá-la a partir do painel de conta de armazenamento após a conclusão da não inscrição. Pode consultar [este link](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#restore-soft-deleted-file-share) para os passos de configuração.

## <a name="next-steps"></a>Passos seguintes

Saiba como [fazer backup Azure File Shares a partir do portal Azure](backup-afs.md)

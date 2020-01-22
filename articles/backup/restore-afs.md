---
title: Restaurar compartilhamentos de arquivos do Azure
description: Saiba como usar o portal do Azure para restaurar um compartilhamento de arquivos inteiro ou arquivos específicos de um ponto de restauração criado pelo serviço de backup do Azure.
ms.topic: conceptual
ms.date: 01/12/2020
ms.openlocfilehash: b16eb4120ff2d269135ae8ae6555ef4fdbdbda5d
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294399"
---
# <a name="restore-azure-file-shares"></a>Restaurar compartilhamentos de arquivos do Azure

Este artigo explica como usar o portal do Azure para restaurar um compartilhamento de arquivos inteiro ou arquivos específicos de um ponto de restauração criado pelo serviço de [backup do Azure](https://docs.microsoft.com/azure/backup/backup-overview) .

Neste guia, você aprenderá a:

* Restaurar um compartilhamento de arquivos completo do Azure
* Restaurar arquivos ou pastas individuais
* Acompanhar o status da operação de restauração

## <a name="steps-to-perform-restore"></a>Etapas para executar a restauração

### <a name="select-the-file-share-to-restore"></a>Selecione o compartilhamento de arquivos a ser restaurado

1. No [portal do Azure](https://portal.azure.com/), abra o cofre dos serviços de recuperação que você usou para configurar o backup para o compartilhamento de arquivos.

2. Clique em **itens de backup** na seção **itens protegidos** da folha **visão geral** .

    ![Clique em itens de backup](./media/restore-afs/backup-items.png)

3. Depois de clicar em **itens de backup**, uma nova folha listando todos os tipos de gerenciamento de backup é exibida ao lado da folha **visão geral** , como mostrado abaixo:

    ![Tipos de gerenciamento de backup](./media/restore-afs/backup-management.png)

4. Em **itens de backup**, em **tipo de gerenciamento de backup**, selecione **armazenamento do Azure (arquivos do Azure)** . Você verá uma lista de todos os compartilhamentos de arquivos e seu backup da conta de armazenamento correspondente usando esse cofre.

    ![Lista de todos os compartilhamentos de arquivos](./media/restore-afs/file-shares.png)

5. Na lista de compartilhamentos de arquivos do Azure, selecione o compartilhamento de arquivos desejado para o qual você deseja executar a operação de restauração.

### <a name="full-share-recovery"></a>Recuperação de compartilhamento completa

Você pode usar essa opção de restauração para restaurar o compartilhamento de arquivos completo no local original ou alternativo.

1. Selecione a opção **restaurar compartilhamento** na folha **item de backup** que aparece depois de selecionar o compartilhamento de arquivos desejado para restaurar na etapa 5 da seção [selecionar o compartilhamento de arquivos a ser restaurado](#select-the-file-share-to-restore) .

   ![Selecione restaurar compartilhamento](./media/restore-afs/restore-share.png)

2. Depois de clicar em **restaurar compartilhamento**, a folha **restaurar** é aberta com um menu de **ponto de restauração** exibindo a lista de pontos de restauração disponíveis para o compartilhamento de arquivos selecionado.

3. Selecione o ponto de restauração que você deseja usar para executar a operação de restauração e clique em **OK**.

    ![Selecionar ponto de restauração](./media/restore-afs/restore-point.png)

4. Depois de clicar em OK, o menu da folha restaurar muda para o **local de restauração**. Em **restaurar local**, especifique onde (ou como) restaurar os dados. Você pode escolher uma das duas opções a seguir:

    * **Local original**: restaure o compartilhamento de arquivos completo para o mesmo local que a origem original.
    * **Local alternativo**: restaure o compartilhamento de arquivos completo para um local alternativo e mantenha o compartilhamento de arquivos original **como está**.

#### <a name="restore-to-original-location"></a>Restaurar no local original

1. Escolha **local original** como o **destino de recuperação** e selecione se deseja ignorar ou substituir se houver conflitos. Clique em **OK** depois de fazer a seleção apropriada.

    ![Escolher local original](./media/restore-afs/original-location.png)

2. Clique em **restaurar** para iniciar a operação de restauração.

    ![Clique em restaurar para iniciar](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location"></a>Restaurar em um local alternativo

1. Escolha o **local alternativo** como o destino de recuperação.
2. Selecione a conta de armazenamento de destino, na qual você deseja restaurar o conteúdo de backup, no menu suspenso do campo **conta de armazenamento** .
3. Com base na conta de armazenamento que você selecionou na etapa 2, o menu suspenso **selecionar compartilhamento de arquivos** exibirá a lista de compartilhamentos de arquivos presentes na conta de armazenamento selecionada. Selecione o compartilhamento de arquivos no qual você deseja restaurar o conteúdo de backup.
4. No campo **nome da pasta** , especifique um nome de pasta que você deseja criar no compartilhamento de arquivos de destino com o conteúdo restaurado.
5. Selecione se deseja ignorar ou substituir se houver conflitos.
6. Clique em **OK** depois de inserir os valores apropriados em todos os campos.

    ![Selecionar local alternativo](./media/restore-afs/alternate-location.png)

7. Clique em restaurar para iniciar a operação de restauração.

    ![Clique em restaurar para iniciar](./media/restore-afs/click-restore.png)

### <a name="item-level-recovery"></a>Recuperação em nível de item

Você pode usar essa opção de restauração para restaurar arquivos ou pastas individuais no local original ou alternativo.

1. Selecione a opção **recuperação de arquivo** na folha item de **backup** que aparece depois de selecionar o compartilhamento de arquivos desejado para restaurar na etapa 5 da seção [selecionar o compartilhamento de arquivos a ser restaurado](#select-the-file-share-to-restore) .

    ![Selecionar recuperação de arquivo](./media/restore-afs/file-recovery.png)

2. Depois de clicar em **recuperação de arquivo**, a folha **restaurar** é aberta com um menu de ponto de **restauração** exibindo uma lista de pontos de restauração disponíveis para o compartilhamento de arquivos selecionado.

3. Selecione o ponto de restauração que você deseja usar para executar a operação de restauração e clique em **OK**.

    ![Selecionar ponto de restauração](./media/restore-afs/restore-point.png)

4. Depois de clicar em OK, o menu da folha restaurar muda para o **local de restauração**. Em **restaurar local**, especifique onde (ou como) restaurar os dados. Você pode escolher uma das duas opções a seguir:

    * **Local original**: restaure os arquivos/pastas selecionados para o mesmo compartilhamento de arquivos que a origem original.
    * **Local alternativo**: restaure os arquivos/pastas selecionados em um local alternativo e mantenha o conteúdo do compartilhamento de arquivos original **como está**.

#### <a name="restore-to-original-location"></a>Restaurar para a localização original;

1. Escolha **local original** como o **destino de recuperação** e selecione se deseja ignorar ou substituir se houver conflitos.

    ![Local original para recuperação em nível de item](./media/restore-afs/original-location-item-level.png)

2. Clique em **Selecionar arquivo** para escolher os arquivos/pastas que você deseja restaurar.

    ![Clique em Selecionar arquivo](./media/restore-afs/select-file.png)

3. Quando você clica em **Selecionar arquivo**, uma folha compartilhamento de arquivos, exibindo o conteúdo do ponto de recuperação de compartilhamento de arquivos selecionado para restauração, é exibida.

4. Marque a caixa correspondente ao arquivo/pasta que você deseja restaurar e clique em **selecionar**.

    ![Selecionar arquivo ou pasta](./media/restore-afs/select-file-folder.png)

5. Repita as etapas 2-4 para selecionar vários arquivos/pastas para restauração.
6. Depois de selecionar todos os itens que você deseja restaurar, clique em **OK**.

    ![Depois de selecionar todos os itens a serem restaurados, clique em OK](./media/restore-afs/after-selecting-items.png)

7. Clique em restaurar para iniciar a operação de restauração.

    ![Clique em restaurar para iniciar](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location"></a>Restaurar em um local alternativo

1. Escolha o **local alternativo** como o destino de recuperação.
2. Selecione a conta de armazenamento de destino, na qual você deseja restaurar o conteúdo de backup, no menu suspenso do campo **conta de armazenamento** .
3. Com base na conta de armazenamento que você selecionou na etapa 2, o menu suspenso **selecionar compartilhamento de arquivos** exibirá a lista dos compartilhamentos de arquivos presentes na conta de armazenamento selecionada. Selecione o compartilhamento de arquivos no qual você deseja restaurar o conteúdo de backup.
4. No campo **nome da pasta** , especifique um nome de pasta que você deseja criar no compartilhamento de arquivos de destino com o conteúdo restaurado.
5. Selecione se deseja ignorar ou substituir se houver conflitos.
6. Clique em **Selecionar arquivo** para escolher os arquivos/pastas que você deseja restaurar.

    ![Selecionar itens para restaurar para o local alternativo](./media/restore-afs/restore-to-alternate-location.png)

7. Quando você clica em **Selecionar arquivo**, uma folha compartilhamento de arquivos, exibindo o conteúdo do ponto de recuperação de compartilhamento de arquivos selecionado para restauração, é exibida.
8. Marque a caixa correspondente ao arquivo/pasta que você deseja restaurar e clique em **selecionar**.

    ![Selecionar destino de recuperação](./media/restore-afs/recovery-destination.png)

9. Repita as etapas 6-8 para selecionar vários arquivos/pastas para restauração.
10. Depois de selecionar todos os itens que você deseja restaurar, clique em **OK**.

    [Clique em OK depois de selecionar todos os arquivos](./media/restore-afs/after-selecting-all-items.png)

11. Clique em **restaurar** para iniciar a operação de restauração.

## <a name="track-restore-operation"></a>Rastrear a operação de restauração

Depois de disparar a operação de restauração, o serviço de backup cria um trabalho para acompanhamento. O backup do Azure exibe notificações sobre o trabalho no Portal. Para exibir as operações do trabalho, clique no hiperlink notificações.

![Clique no hiperlink notificações](./media/restore-afs/notifications-link.png)

Você também pode monitorar o progresso da restauração no cofre dos serviços de recuperação. Estas são as etapas para verificar o status da operação de restauração:

1. Abra o cofre dos serviços de recuperação de onde você disparou a operação de restauração.
2. Clique em **trabalhos de backup** na **seção monitoramento** da folha **visão geral** para ver o status das operações em execução em diferentes cargas de trabalho.

    ![Clique em trabalhos de backup](./media/restore-afs/backup-jobs.png)

3. Clique no nome da carga de trabalho correspondente ao seu compartilhamento de arquivos para exibir mais detalhes sobre a operação de restauração, como dados transferidos, número de arquivos restaurados etc.

    ![Consulte os detalhes restaurados](./media/restore-afs/restore-details.png)

## <a name="next-steps"></a>Passos seguintes

* Saiba como [gerenciar backups de compartilhamento de arquivos do Azure](manage-afs-backup.md)

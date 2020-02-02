---
title: Restaurar compartilhamentos de arquivos do Azure
description: Saiba como utilizar o portal Azure para restaurar uma partilha de ficheiros ou ficheiros específicos a partir de um ponto de restauro criado pela Azure Backup.
ms.topic: conceptual
ms.date: 01/12/2020
ms.openlocfilehash: 8ecb5850c90e5a92e59d17f7949bd5b6cd6205f5
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76930760"
---
# <a name="restore-azure-file-shares"></a>Restaurar compartilhamentos de arquivos do Azure

Este artigo explica como usar o portal Azure para restaurar uma partilha de ficheiros ou ficheiros específicos de um ponto de restauro criado pela [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview).

Neste artigo, você aprenderá a:

* Restaurar uma parte completa do ficheiro Azure.
* Restaurar ficheiros ou pastas individuais.
* Rastreie o estado de operação de restauro.

## <a name="steps-to-perform-a-restore-operation"></a>Passos para realizar uma operação de restauro
Para realizar uma operação de restauro, siga estes passos.

### <a name="select-the-file-share-to-restore"></a>Selecione o compartilhamento de arquivos a ser restaurado

1. No [portal Azure,](https://portal.azure.com/)abra o cofre dos Serviços de Recuperação que usou para configurar a cópia de segurança para a partilha de ficheiros.

1. No painel de visão geral, selecione **itens de backup** sob a secção de itens **protegidos.**

    ![Selecione itens de backup](./media/restore-afs/backup-items.png)

1. Depois de selecionar **itens de backup,** um novo painel que lista todos os tipos de gestão de backup abre ao lado do painel de visão geral.

    ![Tipos de gerenciamento de backup](./media/restore-afs/backup-management.png)

1. No painel de itens de **backup,** em tipo de **gestão de backup,** selecione **Armazenamento Azure (Ficheiros Azure)** . Verá uma lista de todas as ações de ficheiros e as respetivas contas de armazenamento apoiadas usando este cofre.

    ![Lista de todos os compartilhamentos de arquivos](./media/restore-afs/file-shares.png)

1. A partir da lista de ações de ficheiros Azure, selecione a parte de ficheiro para a qual pretende executar a operação de restauro.

### <a name="full-share-recovery"></a>Recuperação total de ações

Pode utilizar esta opção de restauro para restaurar a parte completa do ficheiro na localização original ou numa localização alternativa.

1. Selecione a opção **Restaurar** a partilha no painel **'Backup Item'** que aparece depois de ter selecionado a parte do ficheiro para restaurar no passo 5 da parte de [ficheiro Selea a parte](#select-the-file-share-to-restore) do ficheiro para restaurar a secção.

   ![Selecione restaurar compartilhamento](./media/restore-afs/restore-share.png)

1. Depois de selecionar restore **Share,** o painel **Restaurar** abre-se com um menu **Restore Point** que apresenta uma lista de pontos de restauro disponíveis para a partilha de ficheiros selecionada.

1. Selecione o ponto de restauro que pretende utilizar para executar a operação de restauro e selecione **OK**.

    ![Selecione ponto de restauro](./media/restore-afs/restore-point.png)

1. Depois de selecionar **OK,** o menu **restaurar** o painel para restaurar a **localização**. No **Local de Restauro,** especifique onde ou como restaurar os dados. Selecione uma das duas opções seguintes:

    * **Local original**: restaure o compartilhamento de arquivos completo para o mesmo local que a origem original.
    * **Localização Alternativa**: Restaurar a parte de ficheiro completa para um local alternativo e manter a parte original do ficheiro como está.

#### <a name="restore-to-the-original-location"></a>Restaurar a localização original

1. Selecione **Original Location** como destino de **recuperação**, e selecione se deve saltar ou substituir se houver conflitos. Depois de fazer a seleção adequada, selecione **OK**.

    ![Selecione Localização Original](./media/restore-afs/original-location.png)

1. Selecione **Restaurar** para iniciar a operação de restauro.

    ![Selecione Restaurar para começar](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location"></a>Restaurar em um local alternativo

1. Selecione **Localização Alternativa** como destino de **recuperação.**
1. Selecione a conta de armazenamento de destino onde pretende restaurar o conteúdo back-up da lista de drop-down da Conta de **Armazenamento.**
1. A lista de drop-down Select **File Share** mostra as ações de ficheiro presentes na conta de armazenamento selecionada no passo 2. Selecione o compartilhamento de arquivos no qual você deseja restaurar o conteúdo de backup.
1. Na caixa nome da **pasta,** especifique um nome de pasta que pretende criar na partilha de ficheiros de destino com o conteúdo restaurado.
1. Selecione se deseja ignorar ou substituir se houver conflitos.
1. Depois de introduzir os valores apropriados em todas as caixas, selecione **OK**.

    ![Selecionar local alternativo](./media/restore-afs/alternate-location.png)

1. Selecione **Restaurar** para iniciar a operação de restauro.

    ![Selecione Restaurar para começar](./media/restore-afs/click-restore.png)

### <a name="item-level-recovery"></a>Recuperação ao nível do item

Pode utilizar esta opção de restauro para restaurar ficheiros ou pastas individuais na localização original ou numa localização alternativa.

1. Selecione a opção **de recuperação** de ficheiros no painel **''Backup Item'** que aparece depois de ter selecionado a parte do ficheiro para restaurar no passo 5 da parte de [ficheiro Selea a parte](#select-the-file-share-to-restore) do ficheiro para restaurar a secção.

    ![Selecionar recuperação de arquivo](./media/restore-afs/file-recovery.png)

1. Depois de selecionar **a Recuperação**de Ficheiros, o painel **restaurar** abre-se com um menu de **ponto** restaurar que apresenta uma lista de pontos de restauro disponíveis para a partilha de ficheiros selecionada.

1. Selecione o ponto de restauro que pretende utilizar para executar a operação de restauro e selecione **OK**.

    ![Selecione ponto de restauro](./media/restore-afs/restore-point.png)

1. Depois de selecionar **OK,** o menu de painel de restauro muda para restaurar a **localização**. No **Local de Restauro,** especifique onde ou como restaurar os dados. Selecione uma das duas opções seguintes:

    * **Localização Original**: Restaurar ficheiros ou pastas selecionados na mesma partilha de ficheiros que a fonte original.
    * **Localização Alternativa**: Restaurar ficheiros ou pastas selecionados para um local alternativo e manter o conteúdo original da partilha de ficheiros tal como está.

#### <a name="restore-to-the-original-location"></a>Restaurar a localização original

1. Selecione **Original Location** como destino de **recuperação**, e selecione se deve saltar ou substituir se houver conflitos.

    ![Localização original para recuperação ao nível do item](./media/restore-afs/original-location-item-level.png)

2. Escolha **Selecione 'Ficheiro'** para selecionar os ficheiros ou pastas que pretende restaurar.

    ![Escolha selecione Ficheiro](./media/restore-afs/select-file.png)

1. Depois de escolher o **Select File,** um painel de partilha de ficheiros mostra o conteúdo do ponto de recuperação da partilha de ficheiros selecionado para restaurar.

1. Selecione a caixa de verificação que corresponde ao ficheiro ou pasta que pretende restaurar e escolha **Selecionar**.

    ![Selecionar arquivo ou pasta](./media/restore-afs/select-file-folder.png)

1. Repita os passos 2 a 4 para selecionar vários ficheiros ou pastas para restaurar.
1. Depois de selecionar todos os itens que pretende restaurar, selecione **OK**.

    ![Depois de selecionar todos os itens para restaurar, selecione OK](./media/restore-afs/after-selecting-items.png)

1. Selecione **Restaurar** para iniciar a operação de restauro.

    ![Selecione Restaurar para começar](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location"></a>Restaurar em um local alternativo

1. Selecione **Localização Alternativa** como destino de **recuperação.**
1. Selecione a conta de armazenamento de destino onde pretende restaurar o conteúdo back-up da lista de drop-down da Conta de **Armazenamento.**
1. A lista de drop-down Select **File Share** mostra as ações de ficheiro presentes na conta de armazenamento selecionada no passo 2. Selecione o compartilhamento de arquivos no qual você deseja restaurar o conteúdo de backup.
1. Na caixa nome da **pasta,** especifique um nome de pasta que pretende criar na partilha de ficheiros de destino com o conteúdo restaurado.
1. Selecione se deseja ignorar ou substituir se houver conflitos.
1. Escolha **Selecione 'Ficheiro'** para selecionar os ficheiros ou pastas que pretende restaurar.

    ![Selecionar itens para restaurar para o local alternativo](./media/restore-afs/restore-to-alternate-location.png)

1. Quando escolher **O Ficheiro Select**, um painel de partilha de ficheiros mostra o conteúdo do ponto de recuperação da partilha de ficheiros selecionado para restaurar.
1. Selecione a caixa de verificação que corresponde ao ficheiro ou pasta que pretende restaurar e escolha **Selecionar**.

    ![Selecionar destino de recuperação](./media/restore-afs/recovery-destination.png)

1. Repita os passos 6 a 8 para selecionar vários ficheiros ou pastas para restaurar.
1. Depois de selecionar todos os itens que pretende restaurar, selecione **OK**.

    ![Selecione OK depois de selecionar todos os ficheiros](./media/restore-afs/after-selecting-all-items.png)

1. Selecione **Restaurar** para iniciar a operação de restauro.

## <a name="track-a-restore-operation"></a>Acompanhe uma operação de restauro

Depois de disparar a operação de restauração, o serviço de backup cria um trabalho para acompanhamento. O backup do Azure exibe notificações sobre o trabalho no Portal. Para visualizar as operações para o trabalho, selecione a hiperligação das notificações.

![Selecione hiperligação de notificações](./media/restore-afs/notifications-link.png)

Também pode monitorizar o progresso do restauro do cofre dos Serviços de Recuperação:

1. Abra o cofre dos serviços de recuperação de onde você disparou a operação de restauração.
1. No painel de visão geral, selecione **Backup Jobs** no âmbito da secção **de Monitorização** para ver o estado das operações a funcionar contra diferentes cargas de trabalho.

    ![Selecione Backup Jobs](./media/restore-afs/backup-jobs.png)

1. Selecione o nome da carga de trabalho que corresponde à sua partilha de ficheiros para ver mais detalhes sobre a operação de restauro, como **Data Transfer** e Number **of Restored Files**.

    ![Consulte os detalhes restaurados](./media/restore-afs/restore-details.png)

## <a name="next-steps"></a>Passos seguintes

* Saiba como gerir cópias de [segurança de ficheiros Azure](manage-afs-backup.md).

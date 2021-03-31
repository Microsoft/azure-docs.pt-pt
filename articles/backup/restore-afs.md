---
title: Restaurar ações de ficheiros Azure
description: Saiba como utilizar o portal Azure para restaurar uma partilha de ficheiros inteira ou ficheiros específicos a partir de um ponto de restauração criado pelo Azure Backup.
ms.topic: conceptual
ms.date: 01/12/2020
ms.openlocfilehash: 35ca0936ae6efba716adb51f43326cdd5bfa2d98
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89376779"
---
# <a name="restore-azure-file-shares"></a>Restaurar ações de ficheiros Azure

Este artigo explica como usar o portal Azure para restaurar uma partilha de ficheiros inteira ou ficheiros específicos a partir de um ponto de restauração criado pela [Azure Backup](./backup-overview.md).

Neste artigo, vai aprender a:

* Restaurar uma partilha completa de ficheiros Azure.
* Restaurar ficheiros ou pastas individuais.
* Acompanhe o estado de operação de restauro.

## <a name="steps-to-perform-a-restore-operation"></a>Passos para realizar uma operação de restauro

Para realizar uma operação de restauro, siga estes passos.

### <a name="select-the-file-share-to-restore"></a>Selecione a partilha de ficheiros para restaurar

1. No [portal Azure,](https://portal.azure.com/)abra o cofre dos Serviços de Recuperação que usou para configurar a cópia de segurança para a partilha de ficheiros.

1. No painel de visão geral, selecione **itens de cópia de segurança** na secção **itens protegidos.**

    ![Selecione itens de backup](./media/restore-afs/backup-items.png)

1. Depois de selecionar **itens de backup,** um novo painel que lista todos os tipos de gestão de backup abre ao lado do painel de visão geral.

    ![Tipos de gestão de backup](./media/restore-afs/backup-management.png)

1. No painel **de itens de cópia de segurança,** sob **o Tipo de Gestão de Cópias** de Segurança, selecione **Azure Storage (Ficheiros Azure)**. Verá uma lista de todas as ações de ficheiros e as respetivas contas de armazenamento apoiadas através deste cofre.

    ![Lista de todas as ações de ficheiros](./media/restore-afs/file-shares.png)

1. A partir da lista de ações de ficheiros Azure, selecione a parte de ficheiro para a qual deseja realizar a operação de restauro.

### <a name="full-share-recovery"></a>Recuperação total de ações

Pode utilizar esta opção de restauro para restaurar a partilha completa de ficheiros na localização original ou numa localização alternativa.

1. Selecione a opção **Partilhar opção Partilhar** No painel **de artigos de reserva** que aparece depois de ter selecionado a partilha de ficheiros para restaurar no passo 5 da partilha de [ficheiros para restaurar a](#select-the-file-share-to-restore) secção.

   ![Selecione Restaurar Partilhar](./media/restore-afs/restore-share.png)

1. Depois de selecionar **Restaurar Partilhar,** o **painel De restauro** abre-se. Para selecionar o ponto de restauro que pretende utilizar para executar a operação de restauro, escolha o texto de ligação **Select** abaixo da caixa de texto **Ponto de Restauração.**

    ![Selecione o ponto de restauro escolhendo Select](./media/restore-afs/select-restore-point.png)

1. O painel de contexto **Select Restore Point** abre à direita, listando os pontos de restauro disponíveis para a partilha de ficheiros selecionada. Selecione o ponto de restauro que pretende utilizar para executar a operação de restauro e selecione **OK**.

    ![Selecione ponto de restauro](./media/restore-afs/restore-point.png)

    >[!NOTE]
    >Por predefinição, o **painel Select Restore Point** lista pontos de restauro dos últimos 30 dias. Se pretender olhar para os pontos de restauro criados durante uma duração específica, especifique o intervalo selecionando a **hora** de início e **o tempo de início** adequados e selecione o botão **Refresh.**

1. O próximo passo é escolher a **Localização De Restauro.** Na secção **Destino de Recuperação,** especifique onde ou como restaurar os dados. Selecione uma das duas opções seguintes utilizando o botão de alternar:

    * **Localização original**: Restaurar a partilha completa do ficheiro para o mesmo local que a fonte original.
    * **Localização Alternativa**: Restaurar a partilha completa do ficheiro para uma localização alternativa e manter a partilha original do ficheiro tal como está.

#### <a name="restore-to-the-original-location-full-share-recovery"></a>Restaurar a localização original (recuperação total de ações)

1. Selecione **a Localização Original** como **Destino de Recuperação**, e selecione se deve saltar ou substituir se houver conflitos, escolhendo a opção apropriada da lista de caso **de conflitos.**

1. **Selecione Restaurar** para iniciar a operação de restauro.

    ![Selecione Restaurar para iniciar](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location-full-share-recovery"></a>Restaurar para um local alternativo (recuperação total de ações)

1. Selecione **Localização Alternativa** como **Destino de Recuperação**.
1. Selecione a conta de armazenamento de destino onde pretende restaurar o conteúdo retrossondo da lista de down-down da Conta de **Armazenamento.**
1. A lista de drop-down **Select File Share** apresenta as ações de ficheiro presentes na conta de armazenamento selecionada no passo 2. Selecione a partilha de ficheiros onde pretende restaurar o conteúdo de back-up.
1. Na caixa Nome da **Pasta,** especifique um nome de pasta que pretende criar na partilha de ficheiros de destino com o conteúdo restaurado.
1. Selecione se deve saltar ou substituir se houver conflitos.
1. Depois de introduzir os valores apropriados em todas as caixas, **selecione Restaurar** para iniciar a operação de restauro.

    ![Selecione localização alternativa](./media/restore-afs/alternate-location.png)

### <a name="item-level-recovery"></a>Recuperação ao nível do item

Pode utilizar esta opção de restauro para restaurar ficheiros ou pastas individuais no local original ou numa localização alternativa.

1. Selecione a opção **de recuperação** de ficheiros no painel **de produto de cópia de segurança** que aparece depois de ter selecionado a partilha de ficheiros para restaurar no passo 5 da partilha de [ficheiros para restaurar a](#select-the-file-share-to-restore) secção.

    ![Selecione recuperação de ficheiros](./media/restore-afs/file-recovery.png)

1. Depois de selecionar **a Recuperação de Ficheiros,** o **painel De restauro** abre-se. Para selecionar o ponto de restauro que pretende utilizar para executar a operação de restauro, selecione o texto de ligação **Select** abaixo da caixa de texto **'Ponto de Vista'** ..

    ![Selecione o ponto de restauro escolhendo o link Select](./media/restore-afs/select-restore-point.png)

1. O painel de contexto **Select Restore Point** abre à direita, listando os pontos de restauro disponíveis para a partilha de ficheiros selecionada. Selecione o ponto de restauro que pretende utilizar para executar a operação de restauro e selecione **OK**.

    ![Selecione ponto de restauro](./media/restore-afs/restore-point.png)

1. O próximo passo é escolher a **Localização De Restauro.** Na secção **Destino de Recuperação,** especifique onde ou como restaurar os dados. Selecione uma das duas opções seguintes utilizando o botão de alternar:

    * **Localização Original**: Restaurar ficheiros ou pastas selecionados para a mesma partilha de ficheiros que a origem original.
    * **Localização Alternativa**: Restaurar ficheiros ou pastas selecionados para uma localização alternativa e manter o conteúdo original da partilha de ficheiros como está.

#### <a name="restore-to-the-original-location-item-level-recovery"></a>Restaurar a localização original (recuperação ao nível do item)

1. Selecione **a Localização Original** como **Destino de Recuperação** e selecione se deve saltar ou substituir se houver conflitos, escolhendo a opção apropriada da lista **em caso de conflitos.**

    ![Localização original para recuperação do nível do item](./media/restore-afs/original-location-item-level.png)

1. Para selecionar os ficheiros ou pastas que pretende restaurar, selecione o botão **'Adicionar Ficheiro'.** Isto abrirá um painel de contexto à direita, mostrando o conteúdo do ponto de recuperação da partilha de ficheiros selecionado para restauro.

    ![Escolha adicionar ficheiro](./media/restore-afs/add-file.png)

1. Selecione a caixa de verificação que corresponde ao ficheiro ou pasta que pretende restaurar e escolha **Selecione**.

    ![Selecione arquivo ou pasta](./media/restore-afs/select-file-folder.png)

1. Repita os passos 2 a 4 para selecionar vários ficheiros ou pastas para restaurar.
1. Depois de selecionar todos os itens que pretende restaurar, selecione **Restaurar** para iniciar a operação de restauro.

    ![Selecione Restaurar para iniciar](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location-item-level-recovery"></a>Restaurar para uma localização alternativa (recuperação ao nível do item)

1. Selecione **Localização Alternativa** como **Destino de Recuperação**.
1. Selecione a conta de armazenamento de destino onde pretende restaurar o conteúdo retrossondo da lista de down-down da Conta de **Armazenamento.**
1. A lista de drop-down **Select File Share** apresenta as ações de ficheiro presentes na conta de armazenamento selecionada no passo 2. Selecione a partilha de ficheiros onde pretende restaurar o conteúdo de back-up.
1. Na caixa Nome da **Pasta,** especifique um nome de pasta que pretende criar na partilha de ficheiros de destino com o conteúdo restaurado.
1. Selecione se deve saltar ou substituir se houver conflitos.
1. Para selecionar os ficheiros ou pastas que pretende restaurar, selecione o botão **'Adicionar Ficheiro'.** Isto abrirá um painel de contexto no lado direito mostrando o conteúdo do ponto de recuperação da partilha de ficheiros selecionado para restauro.

    ![Selecione itens para restaurar para localização alternativa](./media/restore-afs/restore-to-alternate-location.png)

1. Selecione a caixa de verificação que corresponde ao ficheiro ou pasta que pretende restaurar e escolha **Selecione**.

    ![Selecione destino de recuperação](./media/restore-afs/recovery-destination.png)

1. Repita os passos 6 a 8 para selecionar vários ficheiros ou pastas para restaurar.
1. Depois de selecionar todos os itens que pretende restaurar, selecione **Restaurar** para iniciar a operação de restauro.

    ![Selecione OK depois de selecionar todos os ficheiros](./media/restore-afs/after-selecting-all-items.png)

## <a name="track-a-restore-operation"></a>Acompanhe uma operação de restauro

Depois de ativar a operação de restauro, o serviço de backup cria um trabalho para rastrear. O Azure Backup apresenta notificações sobre o trabalho no portal. Para visualizar as operações para o trabalho, selecione a hiperligação de notificações.

![Selecione hiperligação de notificações](./media/restore-afs/notifications-link.png)

Também pode monitorizar o progresso da recuperação do cofre dos Serviços de Recuperação:

1. Abra o cofre dos Serviços de Recuperação de onde desencadeou a operação de restauro.
1. No painel geral, selecione **Backup Jobs** na secção **de monitorização** para ver o estado das operações em funcionamento contra diferentes cargas de trabalho.

    ![Selecione trabalhos de backup](./media/restore-afs/backup-jobs.png)

1. Selecione o nome da carga de trabalho que corresponde à sua partilha de ficheiros para ver mais detalhes sobre a operação de restauro, como **Data Transfered** e **Número de Ficheiros Restaurados**.

    ![Ver detalhes restaurados](./media/restore-afs/restore-details.png)

## <a name="next-steps"></a>Passos seguintes

* Saiba como gerir as [cópias de segurança de partilha de ficheiros Azure](manage-afs-backup.md).

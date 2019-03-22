---
title: Ver alterações de conteúdo do ficheiro com a automatização do Azure
description: Utilize a funcionalidade de alteração de conteúdo do ficheiro de controlo de alterações para ver o conteúdo de um ficheiro que foi alterado.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: georgewallace
ms.author: gwallace
ms.date: 07/03/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: dae0ea3e564a3dfafcd7f072f9b0d8854df17ed7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58074748"
---
# <a name="view-contents-of-a-file-that-is-being-tracked-with-change-tracking"></a>Ver o conteúdo de um ficheiro que está a ser controlada com controlo de alterações

Controlo de conteúdo de ficheiros permite-lhe ver os conteúdos de um ficheiro antes e depois uma alteração que está a ser controlada com controlo de alterações. Para fazer isso, ele salva o conteúdo do ficheiro para uma conta de armazenamento após a ocorrência de cada alteração.

## <a name="requirements"></a>Requisitos

* Uma conta de armazenamento standard com o modelo de implementação do Resource Manager é necessária para armazenar o conteúdo do ficheiro. Premium e contas de armazenamento de modelo de implementação clássica não devem ser utilizadas. Para obter mais informações sobre contas de armazenamento, consulte [sobre as contas de armazenamento](../storage/common/storage-create-storage-account.md)

* A conta de armazenamento utilizada só pode ter 1 conta de automatização ligada.

* [Controlo de alterações](automation-change-tracking.md) estiver ativado na sua conta de automatização.

## <a name="enable-file-content-tracking"></a>Ativar o controlo de conteúdo do ficheiro

1. No portal do Azure, abra a sua conta de automatização e, em seguida, selecione **controlo de alterações**.
2. No menu superior, selecione **editar definições de**.
3. Selecione **conteúdo do ficheiro** e clique em **ligação**. Esta ação abre o **localização de conteúdo adicionar para controlo de alterações** painel.

   ![ativar](./media/change-tracking-file-contents/enable.png)

4. Selecione a subscrição e a conta de armazenamento a utilizar para armazenar o conteúdo do ficheiro para. Se pretender ativar o controlo de conteúdo do ficheiro para todos os ficheiros de controladas existentes, selecione **nos** para **carregar conteúdo do ficheiro para todas as definições**. Pode alterar isso para cada caminho de ficheiro posteriormente.

   ![conta de armazenamento do conjunto](./media/change-tracking-file-contents/storage-account.png)

5. Uma vez ativada, a conta de armazenamento e os Uris de SAS são mostrados. Os Uris de SAS expire após a 365 dias e pode ser recriada clicando a **regenerar** botão.

   ![Listar chaves de conta](./media/change-tracking-file-contents/account-keys.png)

## <a name="add-a-file"></a>Adicione um ficheiro

Os seguintes passos guiá-lo por meio de ativar o controlo de alterações para um ficheiro:

1. Na **editar definições** página do **controlo de alterações**, selecione **ficheiros do Windows** ou **ficheiros do Linux** separador e clique em  **Adicionar**

1. Preencha as informações para o caminho do ficheiro e selecione **True** sob **carregar conteúdo do ficheiro para todas as definições**. Esta definição permite que o conteúdo do ficheiro de controlo para o caminho do arquivo apenas.

   ![Adicionar um ficheiro do linux](./media/change-tracking-file-contents/add-linux-file.png)

## <a name="viewing-the-contents-of-a-tracked-file"></a>Ver o conteúdo de um ficheiro controlado

1. Uma vez que foi detetada uma alteração para o ficheiro ou um ficheiro no caminho, mostra o portal. Selecione a alteração de ficheiro da lista de alterações. O **alterar detalhes** é apresentado o painel.

   ![alterações de lista](./media/change-tracking-file-contents/change-list.png)

1. Sobre o **alterar detalhes** página, verá o padrão antes e depois do ficheiro informações, no canto superior esquerdo, clique em **alterações de conteúdo do ficheiro de vista de** para ver o conteúdo do ficheiro.

   ![Detalhes de alteração](./media/change-tracking-file-contents/change-details.png)

1. A nova página mostra o conteúdo do arquivo numa exibição lado a lado. Também pode selecionar **Inline** para ver uma vista de inline das alterações.

   ![alterações de vista de ficheiro](./media/change-tracking-file-contents/view-file-changes.png)

## <a name="next-steps"></a>Passos Seguintes

Visite o tutorial para saber mais sobre como utilizar a solução de controlo de alterações:

> [!div class="nextstepaction"]
> [Resolver problemas relacionados com alterações no seu ambiente](automation-tutorial-troubleshoot-changes.md)

* Uso [pesquisas de registos nos registos do Azure Monitor](../log-analytics/log-analytics-log-searches.md) para ver dados de controlo de alterações detalhadas.


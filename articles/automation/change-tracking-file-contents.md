---
title: Exibir alterações de conteúdo do arquivo com a automação do Azure
description: Use o recurso alteração de conteúdo de arquivo do controle de alterações para exibir o conteúdo de um arquivo que foi alterado.
services: automation
ms.subservice: change-inventory-management
ms.date: 07/03/2018
ms.topic: conceptual
ms.openlocfilehash: 23c4f24e430d58895eb551c3e2cb62b5f0003ac0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75418815"
---
# <a name="view-contents-of-a-file-that-is-being-tracked-with-change-tracking"></a>Exibir o conteúdo de um arquivo que está sendo acompanhado com Controle de Alterações

O controle de conteúdo de arquivo permite que você exiba o conteúdo de um arquivo antes e depois de uma alteração que está sendo controlada com Controle de Alterações. Para fazer isso, ele salva o conteúdo do arquivo em uma conta de armazenamento após a ocorrência de cada alteração.

## <a name="requirements"></a>Requisitos

* Uma conta de armazenamento padrão usando o modelo de implantação do Gerenciador de recursos é necessária para armazenar o conteúdo do arquivo. As contas de armazenamento do modelo de implantação Premium e clássico não devem ser usadas. Para obter mais informações sobre contas de armazenamento, consulte [sobre contas de armazenamento do Azure](../storage/common/storage-create-storage-account.md)

* A conta de armazenamento usada pode ter apenas uma conta de automação conectada.

* O [controle de alterações](automation-change-tracking.md) está habilitado em sua conta de automação.

## <a name="enable-file-content-tracking"></a>Habilitar rastreamento de conteúdo de arquivo

1. No portal do Azure, abra sua conta de automação e, em seguida, selecione **controle de alterações**.
2. No menu superior, selecione **Editar configurações**.
3. Selecione **conteúdo do arquivo** e clique em **vincular**. Isso abre o painel **Adicionar local de conteúdo para controle de alterações** .

   ![ativar](./media/change-tracking-file-contents/enable.png)

4. Selecione a assinatura e a conta de armazenamento a serem usadas para armazenar o conteúdo do arquivo. Se você quiser habilitar o rastreamento de conteúdo de arquivo para todos os arquivos acompanhados existentes, selecione **ativado** para **carregar o conteúdo do arquivo para todas as configurações**. Você pode alterar isso para cada caminho de arquivo posteriormente.

   ![definir conta de armazenamento](./media/change-tracking-file-contents/storage-account.png)

5. Uma vez habilitado, a conta de armazenamento e os URIs de SAS são mostrados. Os URIs de SAS expiram após 365 dias e podem ser recriados clicando no botão **regenerar** .

   ![listar chaves de conta](./media/change-tracking-file-contents/account-keys.png)

## <a name="add-a-file"></a>Adicionar um arquivo

As etapas a seguir orientarão você na ativação do controle de alterações de um arquivo:

1. Na página **Editar configurações** do **controle de alterações**, selecione a guia **arquivos do Windows** ou **arquivos do Linux** e clique em **Adicionar**

1. Preencha as informações do caminho do arquivo e selecione **true** em **carregar conteúdo do arquivo para todas as configurações**. Essa configuração habilita o rastreamento de conteúdo de arquivo somente para esse caminho de arquivo.

   ![Adicionar um arquivo do Linux](./media/change-tracking-file-contents/add-linux-file.png)

## <a name="viewing-the-contents-of-a-tracked-file"></a>Exibindo o conteúdo de um arquivo rastreado

1. Depois que uma alteração for detectada para o arquivo ou um arquivo no caminho, ela será mostrada no Portal. Selecione a alteração de arquivo na lista de alterações. O painel **alterar detalhes** é exibido.

   ![listar alterações](./media/change-tracking-file-contents/change-list.png)

1. Na página **alterar detalhes** , você vê as informações padrão antes e depois do arquivo, na parte superior esquerda, clique em **Exibir alterações de conteúdo do arquivo** para ver o conteúdo do arquivo.

   ![alterar detalhes](./media/change-tracking-file-contents/change-details.png)

1. A nova página mostra o conteúdo do arquivo em uma exibição lado a lado. Você também pode selecionar **embutido** para ver uma exibição em linha das alterações.

   ![Exibir alterações de arquivo](./media/change-tracking-file-contents/view-file-changes.png)

## <a name="next-steps"></a>Passos seguintes

Visite o tutorial em Controle de Alterações para saber mais sobre como usar a solução:

> [!div class="nextstepaction"]
> [Solucionar problemas de alterações em seu ambiente](automation-tutorial-troubleshoot-changes.md)

* Use [pesquisas de log em logs de Azure monitor](../log-analytics/log-analytics-log-searches.md) para exibir dados detalhados de controle de alterações.


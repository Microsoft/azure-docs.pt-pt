---
title: Ver alterações de conteúdo de ficheiros com a Automação Azure
description: Utilize a função de alteração de conteúdo de ficheiro sintetizador de Change Tracking para visualizar o conteúdo de um ficheiro que tenha mudado.
services: automation
ms.subservice: change-inventory-management
ms.date: 07/03/2018
ms.topic: conceptual
ms.openlocfilehash: 57c3c2c7a0c923921c727ccea7839940457bc1ee
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682988"
---
# <a name="view-contents-of-a-file-that-is-being-tracked-with-change-tracking"></a>Ver conteúdo de um ficheiro que está a ser rastreado com Change Tracking

O rastreio de conteúdo de ficheiros permite-lhe visualizar o conteúdo de um ficheiro antes e depois de uma alteração que está a ser rastreada com o Change Tracking. Para tal, guarda o conteúdo do ficheiro numa conta de armazenamento após cada alteração.

## <a name="requirements"></a>Requisitos

* É necessária uma conta de armazenamento padrão utilizando o modelo de implementação do Gestor de Recursos para armazenar conteúdo de ficheiros. Não devem ser utilizadas contas de armazenamento de modelos de implantação premium e clássicas. Para mais informações sobre contas de armazenamento, consulte as contas de [armazenamento do Azure](../storage/common/storage-create-storage-account.md)

* A conta de armazenamento utilizada só pode ter 1 conta de automação ligada.

* [O Change Tracking](automation-change-tracking.md) está ativado na sua conta De automação.

## <a name="enable-file-content-tracking"></a>Ativar o rastreio de conteúdo de ficheiros

1. No portal Azure, abra a sua conta De automação e, em seguida, selecione **'Localizar ' rastreio**.
2. No menu superior, selecione **Definições de Edição**.
3. Selecione **Conteúdo de Ficheiro** e clique em **Link**. Isto abre a localização do conteúdo adicionar para alterar o painel de **rastreio.**

   ![ativar](./media/change-tracking-file-contents/enable.png)

4. Selecione a conta de subscrição e armazenamento para utilizar para armazenar o conteúdo do ficheiro. Se pretender ativar o rastreio de conteúdos de ficheiros para todos os ficheiros rastreados existentes, selecione o conteúdo do ficheiro **On** for **Upload para todas as definições**. Pode alterar isto para cada caminho de ficheiro depois.

   ![definir conta de armazenamento](./media/change-tracking-file-contents/storage-account.png)

5. Uma vez ativada, a conta de armazenamento e o SAS Uris são mostrados. O SAS Uris expira após 365 dias e pode ser recriado clicando no botão **Regenerar.**

   ![lista de chaves de conta](./media/change-tracking-file-contents/account-keys.png)

## <a name="add-a-file"></a>Adicione um ficheiro

Os seguintes passos passam por ligar o rastreio de mudança para um ficheiro:

1. Na página 'Definições de Edição' do **'Change Tracking',** selecione os **Ficheiros windows** ou o separador **Ficheiros Linux** e clique em **Adicionar**

1. Preencha as informações para o caminho do ficheiro e selecione True under Upload conteúdo de **ficheiro para todas as definições**. Esta definição permite o rastreio de conteúdo de ficheiros apenas para esse caminho de ficheiros.

   ![adicionar um ficheiro linux](./media/change-tracking-file-contents/add-linux-file.png)

## <a name="viewing-the-contents-of-a-tracked-file"></a>Visualização do conteúdo de um ficheiro rastreado

1. Uma vez detetada uma alteração para o ficheiro ou um ficheiro no caminho, mostra no portal. Selecione a alteração do ficheiro na lista de alterações. O painel de detalhes da Mudança é apresentado.

   ![alterações na lista](./media/change-tracking-file-contents/change-list.png)

1. No painel de detalhes da Alteração, vê a norma antes e depois das informações do ficheiro. Selecione **Ver Alterações de Conteúdo** de Ficheiropara ver o conteúdo do ficheiro.

   ![alterar detalhes](./media/change-tracking-file-contents/change-details.png)

1. A nova página mostra-lhe o conteúdo do ficheiro numa vista lado a lado. Também pode selecionar **Inline** para ver uma visão inline das alterações.

   ![ver alterações de ficheiros](./media/change-tracking-file-contents/view-file-changes.png)

## <a name="next-steps"></a>Passos seguintes

Visite o tutorial sobre O Rastreio de Mudanças para saber mais sobre a utilização da solução:

> [!div class="nextstepaction"]
> [Resolver problemas relacionados com alterações no seu ambiente](automation-tutorial-troubleshoot-changes.md)

* Utilize as pesquisas de [registo nos registos do Monitor Azure](../log-analytics/log-analytics-log-searches.md) para visualizar dados de rastreio de alterações detalhadas.


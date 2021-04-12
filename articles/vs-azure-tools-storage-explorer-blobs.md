---
title: Gerir os recursos de armazenamento Azure Blob com | do Storage Explorer Microsoft Docs
description: Gerir os recursos de armazenamento Azure Blob com o Storage Explorer. Crie um recipiente blob, veja o conteúdo do recipiente blob, elimine ou copie um recipiente de bolhas, e muito mais.
services: storage
documentationcenter: na
author: cawaMS
manager: paulyuk
editor: ''
ms.assetid: 2f09e545-ec94-4d89-b96c-14783cc9d7a9
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/21/2019
ms.author: cawa
ms.openlocfilehash: 48f00d964a87790b8d8c9b1d8eceaed26d15199e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "95531972"
---
# <a name="manage-azure-blob-storage-resources-with-storage-explorer"></a>Gerir os recursos de armazenamento Azure Blob com o Storage Explorer

## <a name="overview"></a>Descrição Geral

[O Azure Blob Storage](./storage/blobs/storage-quickstart-blobs-dotnet.md) é um serviço para armazenar grandes quantidades de dados não estruturados, como texto ou dados binários, que podem ser acedidos a partir de qualquer parte do mundo através de HTTP ou HTTPS.
Pode utilizar o armazenamento de Blobs para expor publicamente os dados ao mundo ou para armazenar dados da aplicação em privado. Neste artigo, você vai aprender a usar o Storage Explorer para trabalhar com recipientes e bolhas.

## <a name="prerequisites"></a>Pré-requisitos

Para executar os passos descritos neste artigo, é necessário o seguinte:

* [Transferir e instalar o Explorador de Armazenamento](https://www.storageexplorer.com)
* [Ligue-se a uma conta ou serviço de armazenamento Azure](vs-azure-tools-storage-manage-with-storage-explorer.md#connect-to-a-storage-account-or-service)

## <a name="create-a-blob-container"></a>Criar um contentor de blobs

Todas as bolhas devem residir num recipiente de bolhas, que é simplesmente um agrupamento lógico de bolhas. Uma conta pode conter um número ilimitado de contentores, e cada recipiente pode armazenar um número ilimitado de bolhas.

Os seguintes passos ilustram como criar um recipiente de bolhas dentro do Storage Explorer.

1. Abra o Explorador de Armazenamento.
2. No painel esquerdo, expanda a conta de armazenamento na qual deseja criar o recipiente blob.
3. Clique à direita em **Recipientes Blob** e - a partir do menu de contexto - selecione **Create Blob Container**.

   ![Criar menu de contexto de recipientes blob][0]
4. Aparecerá por baixo da pasta **Blob Containers** uma caixa de texto. Introduza o nome do contentor de blobs. Consulte [Criar um recipiente](storage/blobs/storage-quickstart-blobs-dotnet.md#create-a-container) para obter informações sobre regras e restrições sobre o nome de recipientes de bolhas.

   ![Criar caixa de texto de recipientes blob][1]
5. Prima **Introduza** quando for feito para criar o recipiente blob ou **Esc** para cancelar. Uma vez criado o recipiente blob com sucesso, será apresentado sob a pasta **Blob Containers** para a conta de armazenamento selecionada.

   ![Recipiente blob criado][2]

## <a name="view-a-blob-containers-contents"></a>Ver o conteúdo de um recipiente de bolhas

Os recipientes blob contêm bolhas e pastas (que também podem conter bolhas).

Os seguintes passos ilustram como ver o conteúdo de um recipiente de bolhas dentro do Explorador de Armazenamento:

1. Abra o Explorador de Armazenamento.
2. No painel esquerdo, expanda a conta de armazenamento que contém o recipiente blob que deseja ver.
3. Expandir os **recipientes blob** da conta de armazenamento.
4. Clique com o botão direito no recipiente blob que deseja visualizar e - a partir do menu de contexto - selecione **Open Blob Container Editor**.
   Também pode clicar duas vezes no recipiente blob que deseja visualizar.

   ![Menu de contexto de editor de recipiente de blob aberto][19]
5. O painel principal mostrará o conteúdo do recipiente blob.

   ![Editor de recipientes blob][3]

## <a name="delete-a-blob-container"></a>Apagar um recipiente de bolhas

Os recipientes blob podem ser facilmente criados e eliminados conforme necessário. (Para ver como eliminar as bolhas individuais, consulte a secção, [Gerir as bolhas num recipiente de bolhas](#managing-blobs-in-a-blob-container).)

Os seguintes passos ilustram como eliminar um recipiente de bolhas dentro do Explorador de Armazenamento:

1. Abra o Explorador de Armazenamento.
2. No painel esquerdo, expanda a conta de armazenamento que contém o recipiente blob que deseja ver.
3. Expandir os **recipientes blob** da conta de armazenamento.
4. Clique com o botão direito no recipiente blob que deseja eliminar e - a partir do menu de contexto - selecione **Delete**.
   Também pode **premir Eliminar** para eliminar o recipiente blob atualmente selecionado.

   ![Eliminar menu de contexto de recipiente de bolha][4]
5. Selecione **Yes (Sim)**, na caixa de diálogo de confirmação.

   ![Eliminar a confirmação do recipiente blob][5]

## <a name="copy-a-blob-container"></a>Copiar um recipiente de bolhas

O Storage Explorer permite-lhe copiar um recipiente de bolhas para a área de transferência e, em seguida, colar esse recipiente de bolhas noutra conta de armazenamento. (Para ver como copiar bolhas individuais, consulte a secção, [Gerir bolhas num recipiente de bolhas](#managing-blobs-in-a-blob-container).)

Os seguintes passos ilustram como copiar um recipiente blob de uma conta de armazenamento para outra.

1. Abra o Explorador de Armazenamento.
2. No painel esquerdo, expanda a conta de armazenamento que contém o recipiente blob que pretende copiar.
3. Expandir os **recipientes blob** da conta de armazenamento.
4. Clique com o botão direito no recipiente blob que deseja copiar e - a partir do menu de contexto - selecione **Copy Blob Container**.

   ![Menu de contexto do recipiente blob de cópia][6]
5. Clique com o botão direito na conta de armazenamento "target" desejada na qual pretende colar o recipiente blob e - a partir do menu de contexto - **selecione Pasta Blob Container**.

   ![Menu de contexto do recipiente de pasta blob][7]

## <a name="get-the-sas-for-a-blob-container"></a>Obter a SAS para um contentor de blobs

As [assinaturas de acesso partilhado (SAS)](./storage/common/storage-sas-overview.md) disponibilizam acesso delegado a recursos na sua conta de armazenamento.
Isto significa que pode conceder a um cliente permissões limitadas a objetos na sua conta de armazenamento durante um determinado período de tempo e com um conjunto de permissões especificadas, sem ter de partilhar as chaves de acesso da conta.

Os seguintes passos ilustram como criar um SAS para um recipiente de bolhas:

1. Abra o Explorador de Armazenamento.
2. No painel esquerdo, expanda a conta de armazenamento que contém o recipiente blob para o qual deseja obter um SAS.
3. Expandir os **recipientes blob** da conta de armazenamento.
4. Clique com o botão direito no recipiente de bolhas pretendido e - a partir do menu de contexto - **selecione Obter Assinatura de Acesso Partilhado**.

   ![Obtenha menu de contexto SAS][8]
5. Na caixa de diálogo **Shared Access Signature (Assinatura de Acesso Partilhado)**, especifique a política, as datas de início e de expiração, o fuso horário e os níveis de acesso que atribuir ao recurso.

   ![Obtenha opções SAS][9]
6. Quando tiver terminado de especificar as opções de SAS, selecione **Create (Criar)**.
7. Um segundo diálogo **de assinatura de acesso partilhado** irá então exibir que lista o recipiente blob juntamente com o URL e Os QueryStrings que pode utilizar para aceder ao recurso de armazenamento.
   Selecione **Copy (Copiar)**, junto ao URL que quer copiar para a área de transferências.

   ![Copiar URLs SAS][10]
8. Quando terminar, selecione **Close (Fechar)**.

## <a name="manage-access-policies-for-a-blob-container"></a>Gerir políticas de acesso para um recipiente blob

Os seguintes passos ilustram como gerir (adicionar e remover) as políticas de acesso para um recipiente blob:

1. Abra o Explorador de Armazenamento.
2. No painel esquerdo, expanda a conta de armazenamento contendo o recipiente blob cujas políticas de acesso pretende gerir.
3. Expandir os **recipientes blob** da conta de armazenamento.
4. Selecione o recipiente blob desejado e - a partir do menu de contexto - **selecione 'Gerir políticas de acesso'.**

   ![Menu de Contexto Gerir políticas de acesso][11]
5. O diálogo 'Políticas de **Acesso'** irá enumerar quaisquer políticas de acesso já criadas para o recipiente blob selecionado.

   ![Opções de Política de Acesso][12]
6. Siga estes passos consoante a tarefa de gestão de política de acesso:

   * **Adicionar uma política de acesso nova** - selecione **Add (Adicionar)**. Depois de gerada, a caixa de diálogo **Access Policies** mostrará a política de acesso acabada de adicionar (com predefinições).
   * **Editar uma política de acesso** - Faça as edições desejadas e selecione **Save**.
   * **Remover uma política de acesso** - selecione **Remove (Remover)** junto à política de acesso que pretende remover.

## <a name="set-the-public-access-level-for-a-blob-container"></a>Definir o nível de acesso público para um recipiente blob

Por predefinição, todos os recipientes blob estão definidos como "Sem acesso público".

Os seguintes passos ilustram como especificar um nível de acesso público para um recipiente de bolhas.

1. Abra o Explorador de Armazenamento.
2. No painel esquerdo, expanda a conta de armazenamento contendo o recipiente blob cujas políticas de acesso pretende gerir.
3. Expandir os **recipientes blob** da conta de armazenamento.
4. Selecione o recipiente blob desejado e - a partir do menu de contexto - selecione **Definir Nível de Acesso Público**.

   ![Definir menu de contexto de nível de acesso público][13]
5. No **diálogo de nível de acesso público** do recipiente definido, especifique o nível de acesso pretendido.

   ![Definir opções de nível de acesso público][14]
6. Selecione **Aplicar**.

## <a name="managing-blobs-in-a-blob-container"></a>Gerir bolhas em um recipiente de bolhas

Assim que criar um recipiente de bolhas, pode enviar uma bolha para o recipiente de bolhas, transferir uma bolha para o computador local, abrir uma bolha no computador local, e muito mais.

Os passos seguintes ilustram como gerir as bolhas (e pastas) dentro de um recipiente de bolhas.

1. Abra o Explorador de Armazenamento.
2. No painel esquerdo, expanda a conta de armazenamento que contém o contentor de blobs que quer gerir.
3. Expandir os **recipientes blob** da conta de armazenamento.
4. Clique duas vezes no recipiente blob que deseja ver.
5. O painel principal mostrará o conteúdo do recipiente blob.

   ![Ver recipiente blob][3]
6. O painel principal mostrará o conteúdo do recipiente blob.
7. Siga estes passos, consoante a tarefa que pretende realizar:

   * **Faça upload de ficheiros para um recipiente de bolhas**

     1. Na barra de ferramentas do painel principal, selecione **Upload (Carregar)** e, em seguida, **Upload Files (Carregar Ficheiros)**, no menu pendente.

        ![Menu Carregar ficheiros][15]
     2. Na caixa de diálogo **Upload files (Carregar ficheiros)**, selecione o botão de reticências (**...**), no lado direito da caixa de texto **Files (Ficheiros)**, para selecionar o ficheiro ou ficheiros que quer carregar.

        ![Enviar opções de ficheiros][16]
     3. Especificar o tipo de **Blob**. Consulte [Criar um recipiente](storage/blobs/storage-quickstart-blobs-dotnet.md#create-a-container) para obter mais informações.
     4. Opcionalmente, especifique uma pasta-alvo na qual os ficheiros(s) selecionados serão carregados. Se a pasta de destino não existir, será criada.
     5. Selecione **Carregar**.
   * **Faça o upload de uma pasta para um recipiente de bolhas**

     1. Na barra de ferramentas do painel principal, **selecione Carregar** e, em seguida, carregar a **pasta** a partir do menu suspenso.

        ![Menu Carregar pasta][17]
     2. Na caixa de diálogo **Upload folder (Carregar pasta)**, selecione o botão de reticências (**...**), no lado direito da caixa de texto **Folder (Pasta)**, para selecionar a pasta cujos conteúdos quer carregar.

        ![Enviar opções de pasta][18]
     3. Especificar o tipo de **Blob**. Consulte [Criar um recipiente](storage/blobs/storage-quickstart-blobs-dotnet.md#create-a-container) para obter mais informações.
     4. Opcionalmente, especifique uma pasta de destino no qual os conteúdos da pasta selecionada serão carregados. Se a pasta de destino não existir, será criada.
     5. Selecione **Carregar**.
   * **Faça o download de uma bolha para o seu computador local**

     1. Selecione a bolha que deseja baixar.
     2. Na barra de ferramentas do painel principal, selecione **Download (Transferir)**.
     3. No **Especifique onde guardar o diálogo blob descarregado,** especifique o local onde deseja que a bolha seja descarregada e o nome que deseja dar-lhe.  
     4. Selecione **Guardar**.
   * **Abra uma bolha no seu computador local**

     1. Selecione a bolha que deseja abrir.
     2. Na barra de ferramentas do painel principal, selecione **Open (Abrir)**.
     3. A bolha será descarregada e aberta utilizando a aplicação associada ao tipo de ficheiro subjacente da bolha.
   * **Copie uma bolha para a prancheta**

     1. Selecione a bolha que deseja copiar.
     2. Na barra de ferramentas do painel principal, selecione **Copy (Copiar)**.
     3. No painel esquerdo, navegue para outro recipiente de bolhas e clique duas vezes para vê-lo no painel principal.
     4. Na barra de ferramentas do painel principal, **selecione Pasta** para criar uma cópia da bolha.
   * **Eliminar um blob**

     1. Selecione a bolha que pretende eliminar.
     2. Na barra de ferramentas do painel principal, selecione **Delete (Eliminar)**.
     3. Selecione **Yes (Sim)**, na caixa de diálogo de confirmação.

## <a name="next-steps"></a>Passos seguintes

* Vejas os [mais recentes vídeos e notas de versão do Explorador de Armazenamento](https://www.storageexplorer.com).
* Saiba como [utilizar blobs, tabelas, filas e ficheiros do Azure para criar aplicações](https://azure.microsoft.com/documentation/services/storage/).

[0]: ./media/vs-azure-tools-storage-explorer-blobs/blob-containers-create-context-menu.png
[1]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-create.png
[2]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-create-done.png
[3]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-editor.png
[4]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-delete-context-menu.png
[5]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-delete-confirmation.png
[6]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-copy-context-menu.png
[7]: ./media/vs-azure-tools-storage-explorer-blobs/blob-containers-paste-context-menu.png
[8]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-context-menu.png
[9]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-options.png
[10]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-urls.png
[11]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-manage-access-policies-context-menu.png
[12]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-manage-access-policies-options.png
[13]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-set-public-access-level-context-menu.png
[14]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-set-public-access-level-options.png
[15]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-files-menu.png
[16]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-files-options.png
[17]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-folder-menu.png
[18]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-folder-options.png
[19]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-open-editor-context-menu.png
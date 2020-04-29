---
title: Gerir os recursos de armazenamento da Blob Azure com o Storage Explorer Microsoft Docs
description: Gerir recipientes e blobs azure blobs com explorador de armazenamento
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
ms.openlocfilehash: 56c20c995a95058b5039b7268c7b7b1426e900fa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79244397"
---
# <a name="manage-azure-blob-storage-resources-with-storage-explorer"></a>Gerir os recursos de armazenamento da Blob Azure com o Storage Explorer

## <a name="overview"></a>Descrição geral

[O Armazenamento Azure Blob](storage/blobs/storage-dotnet-how-to-use-blobs.md) é um serviço para armazenar grandes quantidades de dados não estruturados, como texto ou dados binários, que podem ser acedidos a partir de qualquer parte do mundo através de HTTP ou HTTPS.
Pode utilizar o armazenamento de Blobs para expor publicamente os dados ao mundo ou para armazenar dados da aplicação em privado. Neste artigo, você aprenderá a usar o Storage Explorer para trabalhar com recipientes e bolhas.

## <a name="prerequisites"></a>Pré-requisitos

Para executar os passos descritos neste artigo, é necessário o seguinte:

* [Transferir e instalar o Explorador de Armazenamento](https://www.storageexplorer.com)
* [Ligue-se a uma conta ou serviço de armazenamento Azure](vs-azure-tools-storage-manage-with-storage-explorer.md#connect-to-a-storage-account-or-service)

## <a name="create-a-blob-container"></a>Criar um contentor de blobs

Todas as bolhas devem residir num recipiente de bolhas, que é simplesmente um agrupamento lógico de bolhas. Uma conta pode conter um número ilimitado de contentores, e cada recipiente pode armazenar um número ilimitado de bolhas.

Os seguintes passos ilustram como criar um recipiente de bolha dentro do Storage Explorer.

1. Abra o Explorador de Armazenamento.
2. No painel esquerdo, expanda a conta de armazenamento dentro da qual pretende criar o recipiente de bolhas.
3. Clique direito em **Recipientes Blob**, e - a partir do menu de contexto - selecione **Create Blob Container**.

   ![Criar menu de contexto de recipientes blob][0]
4. Aparecerá uma caixa de texto abaixo da pasta **Blob Containers.** Introduza o nome do contentor de blobs. Consulte [Criar um recipiente](storage/blobs/storage-quickstart-blobs-dotnet.md#create-a-container) para obter informações sobre regras e restrições sobre a nomeação de recipientes blob.

   ![Criar caixa de texto blob containers][1]
5. Pressione **Introduza** quando estiver feito para criar o recipiente de bolha, ou **Esc** para cancelar. Uma vez criado o recipiente blob com sucesso, este será apresentado sob a pasta **Blob Containers** para a conta de armazenamento selecionada.

   ![Recipiente blob criado][2]

## <a name="view-a-blob-containers-contents"></a>Veja o conteúdo de um recipiente de bolhas

Os recipientes blob contêm bolhas e pastas (que também podem conter bolhas).

Os seguintes passos ilustram como ver o conteúdo de um recipiente de bolha dentro do Storage Explorer:

1. Abra o Explorador de Armazenamento.
2. No painel esquerdo, expanda a conta de armazenamento contendo o recipiente de bolhas que pretende ver.
3. Expandir os **recipientes blob**da conta de armazenamento.
4. Clique no recipiente blob que deseja ver e - a partir do menu de contexto - **selecione Open Blob Container Editor**.
   Também pode clicar duas vezes no recipiente de bolhas que deseja ver.

   ![Menu de contexto de editor de recipientes de blob aberto][19]
5. O painel principal mostrará o conteúdo do recipiente de bolhas.

   ![Editor de contentores blob][3]

## <a name="delete-a-blob-container"></a>Eliminar um recipiente de bolhas

Os recipientes blob podem ser facilmente criados e eliminados conforme necessário. (Para ver como eliminar as bolhas individuais, consulte a secção, [gerindo bolhas num recipiente](#managing-blobs-in-a-blob-container)de bolhas .)

Os seguintes passos ilustram como eliminar um recipiente de bolha no Storage Explorer:

1. Abra o Explorador de Armazenamento.
2. No painel esquerdo, expanda a conta de armazenamento contendo o recipiente de bolhas que pretende ver.
3. Expandir os **recipientes blob**da conta de armazenamento.
4. Clique no recipiente de bolha que pretende eliminar e - a partir do menu de contexto - **selecione Delete**.
   Também pode **premir Apagar** para eliminar o recipiente de bolhas atualmente selecionado.

   ![Eliminar menu de contexto de recipiente blob][4]
5. Selecione **Yes (Sim)**, na caixa de diálogo de confirmação.

   ![Eliminar a confirmação do recipiente blob][5]

## <a name="copy-a-blob-container"></a>Copiar um recipiente de bolhas

O Storage Explorer permite-lhe copiar um recipiente de bolha para a área de reparação e, em seguida, colar o recipiente blob em outra conta de armazenamento. (Para ver como copiar bolhas individuais, consulte a secção, [gerindo bolhas num recipiente](#managing-blobs-in-a-blob-container)de bolha .)

Os seguintes passos ilustram como copiar um recipiente de bolha de uma conta de armazenamento para outra.

1. Abra o Explorador de Armazenamento.
2. No painel esquerdo, expanda a conta de armazenamento contendo o recipiente de bolha que pretende copiar.
3. Expandir os **recipientes blob**da conta de armazenamento.
4. Clique no recipiente de blob que pretende copiar e - a partir do menu de contexto - selecione **Copy Blob Container**.

   ![Copiar menu de contexto de recipiente blob][6]
5. Clique à direita na conta de armazenamento "target" desejada na qual pretende colar o recipiente de blob e - a partir do menu de contexto - selecione **Recipiente pasta Blob**.

   ![Menu de contexto de recipiente de bolha de pasta][7]

## <a name="get-the-sas-for-a-blob-container"></a>Obter a SAS para um contentor de blobs

As [assinaturas de acesso partilhado (SAS)](storage/common/storage-dotnet-shared-access-signature-part-1.md) disponibilizam acesso delegado a recursos na sua conta de armazenamento.
Isto significa que pode conceder a um cliente permissões limitadas a objetos na sua conta de armazenamento durante um determinado período de tempo e com um conjunto de permissões especificadas, sem ter de partilhar as chaves de acesso da conta.

Os seguintes passos ilustram como criar um SAS para um recipiente de bolhas:

1. Abra o Explorador de Armazenamento.
2. No painel esquerdo, expanda a conta de armazenamento contendo o recipiente de bolhas para o qual pretende obter um SAS.
3. Expandir os **recipientes blob**da conta de armazenamento.
4. Clique no recipiente de bolhas desejado e - a partir do menu de contexto - selecione **Get Shared Access Signature**.

   ![Obtenha menu de contexto SAS][8]
5. Na caixa de diálogo **Shared Access Signature (Assinatura de Acesso Partilhado)**, especifique a política, as datas de início e de expiração, o fuso horário e os níveis de acesso que atribuir ao recurso.

   ![Obtenha opções SAS][9]
6. Quando tiver terminado de especificar as opções de SAS, selecione **Create (Criar)**.
7. Um segundo diálogo de assinatura de **acesso partilhado** irá então mostrar que lista o recipiente de bolha juntamente com o URL e as QueryStrings que pode utilizar para aceder ao recurso de armazenamento.
   Selecione **Copy (Copiar)**, junto ao URL que quer copiar para a área de transferências.

   ![Copiar URLs SAS][10]
8. Quando terminar, selecione **Close (Fechar)**.

## <a name="manage-access-policies-for-a-blob-container"></a>Gerir políticas de acesso para um recipiente de bolhas

Os seguintes passos ilustram como gerir (adicionar e remover) políticas de acesso para um recipiente de bolhas:

1. Abra o Explorador de Armazenamento.
2. No painel esquerdo, expanda a conta de armazenamento contendo o recipiente blob cujas políticas de acesso pretende gerir.
3. Expandir os **recipientes blob**da conta de armazenamento.
4. Selecione o recipiente de bolhas desejado e - a partir do menu de contexto - selecione **Manage Access Policies**.

   ![Menu de Contexto Gerir políticas de acesso][11]
5. O diálogo **De Políticas** de Acesso listará quaisquer políticas de acesso já criadas para o recipiente blob selecionado.

   ![Opções de Política de Acesso][12]
6. Siga estes passos consoante a tarefa de gestão de política de acesso:

   * **Adicionar uma política de acesso nova** - selecione **Add (Adicionar)**. Depois de gerada, a caixa de diálogo **Access Policies** mostrará a política de acesso acabada de adicionar (com predefinições).
   * **Editar uma política** de acesso - Faça as edificações desejadas e selecione **Guardar**.
   * **Remover uma política de acesso** - selecione **Remove (Remover)** junto à política de acesso que pretende remover.

## <a name="set-the-public-access-level-for-a-blob-container"></a>Definir o Nível de Acesso Público para um recipiente de bolhas

Por predefinição, cada recipiente blob está definido para "Sem acesso público".

As seguintes etapas ilustram como especificar um nível de acesso público para um recipiente de bolhas.

1. Abra o Explorador de Armazenamento.
2. No painel esquerdo, expanda a conta de armazenamento contendo o recipiente blob cujas políticas de acesso pretende gerir.
3. Expandir os **recipientes blob**da conta de armazenamento.
4. Selecione o recipiente de bolhas desejado e - a partir do menu de contexto - selecione **Definir Nível**de Acesso Público .

   ![Definir menu de contexto de nível de acesso público][13]
5. No diálogo de acesso público do **recipiente definido,** especifique o nível de acesso desejado.

   ![Definir opções de nível de acesso público][14]
6. Selecione **Aplicar**.

## <a name="managing-blobs-in-a-blob-container"></a>Gerir bolhas em um recipiente de bolhas

Depois de criar um recipiente de bolhas, pode carregar uma bolha para aquele recipiente de bolha, descarregar uma bolha para o seu computador local, abrir uma bolha no seu computador local, e muito mais.

Os seguintes passos ilustram como gerir as bolhas (e pastas) dentro de um recipiente de bolhas.

1. Abra o Explorador de Armazenamento.
2. No painel esquerdo, expanda a conta de armazenamento contendo o recipiente de bolhas que pretende gerir.
3. Expandir os **recipientes blob**da conta de armazenamento.
4. Clique duas vezes no recipiente de bolhas que deseja ver.
5. O painel principal mostrará o conteúdo do recipiente de bolhas.

   ![Ver recipiente de bolhas][3]
6. O painel principal mostrará o conteúdo do recipiente de bolhas.
7. Siga estes passos, consoante a tarefa que pretende realizar:

   * **Faça upload de ficheiros para um recipiente de bolha**

     1. Na barra de ferramentas do painel principal, selecione **Upload (Carregar)** e, em seguida, **Upload Files (Carregar Ficheiros)**, no menu pendente.

        ![Menu Carregar ficheiros][15]
     2. Na caixa de diálogo **Upload files (Carregar ficheiros)**, selecione o botão de reticências (**...**), no lado direito da caixa de texto **Files (Ficheiros)**, para selecionar o ficheiro ou ficheiros que quer carregar.

        ![Carregar opções de ficheiros][16]
     3. Especifique o tipo de **tipo Blob**. Consulte [Criar um recipiente](storage/blobs/storage-quickstart-blobs-dotnet.md#create-a-container) para obter mais informações.
     4. Opcionalmente, especifique uma pasta-alvo na qual os ficheiros selecionados serão carregados. Se a pasta de destino não existir, será criada.
     5. Selecione **Upload**.
   * **Faça upload de uma pasta para um recipiente de bolhas**

     1. Na barra de ferramentas do painel principal, **selecione Upload** **e,** em seguida, carregue a pasta a partir do menu suspenso.

        ![Menu Carregar pasta][17]
     2. Na caixa de diálogo **Upload folder (Carregar pasta)**, selecione o botão de reticências (**...**), no lado direito da caixa de texto **Folder (Pasta)**, para selecionar a pasta cujos conteúdos quer carregar.

        ![Carregar opções de pastas][18]
     3. Especifique o tipo de **tipo Blob**. Consulte [Criar um recipiente](storage/blobs/storage-quickstart-blobs-dotnet.md#create-a-container) para obter mais informações.
     4. Opcionalmente, especifique uma pasta de destino no qual os conteúdos da pasta selecionada serão carregados. Se a pasta de destino não existir, será criada.
     5. Selecione **Upload**.
   * **Faça o download de uma bolha para o seu computador local**

     1. Selecione a bolha que deseja descarregar.
     2. Na barra de ferramentas do painel principal, selecione **Download (Transferir)**.
     3. No **Especte onde guardar o diálogo blob descarregado,** especifique a localização onde pretende que a bolha seja descarregada e o nome que pretende dar.  
     4. Selecione **Guardar**.
   * **Abra uma bolha no seu computador local**

     1. Selecione a bolha que deseja abrir.
     2. Na barra de ferramentas do painel principal, selecione **Open (Abrir)**.
     3. A bolha será descarregada e aberta utilizando a aplicação associada ao tipo de ficheiro subjacente da bolha.
   * **Copiar uma bolha para a pasta**

     1. Selecione a bolha que pretende copiar.
     2. Na barra de ferramentas do painel principal, selecione **Copy (Copiar)**.
     3. No painel esquerdo, navegue para outro recipiente de bolhas e clique duas vezes nele para vê-lo no painel principal.
     4. Na barra de ferramentas do painel principal, selecione **Pasta** para criar uma cópia da bolha.
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

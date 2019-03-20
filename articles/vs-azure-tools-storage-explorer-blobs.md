---
title: Gerir recursos de armazenamento de Blobs do Azure com o Explorador de armazenamento | Documentos da Microsoft
description: Gerir Blobs com o Explorador de armazenamento e de contentores de Blobs do Azure
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
ms.date: 11/18/2016
ms.author: cawa
ms.openlocfilehash: f46467871a5ae0147b5dc60881bda4175eabac56
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57858172"
---
# <a name="manage-azure-blob-storage-resources-with-storage-explorer"></a>Gerir recursos de armazenamento de Blobs do Azure com o Explorador de armazenamento
## <a name="overview"></a>Descrição geral
[Armazenamento de Blobs do Azure](storage/blobs/storage-dotnet-how-to-use-blobs.md) é um serviço para armazenar grandes quantidades de dados não estruturados, como texto ou dados binários, que podem ser acedidos em qualquer local no mundo através de HTTP ou HTTPS.
Pode utilizar o armazenamento de Blobs para expor publicamente os dados ao mundo ou para armazenar dados da aplicação em privado. Neste artigo, aprenderá a utilizar o Explorador de armazenamento para trabalhar com blobs e contentores de Blobs.

## <a name="prerequisites"></a>Pré-requisitos
Para executar os passos descritos neste artigo, é necessário o seguinte:

* [Transferir e instalar o Explorador de Armazenamento](https://www.storageexplorer.com)
* [Ligar a uma conta de armazenamento do Azure ou serviço](vs-azure-tools-storage-manage-with-storage-explorer.md#connect-to-a-storage-account-or-service)

## <a name="create-a-blob-container"></a>Criar um contentor de blobs
Todos os blobs têm de residir num contentor de BLOBs, que é simplesmente um agrupamento lógico de blobs. Uma conta pode conter um número ilimitado de contentores, e cada contentor pode armazenar um número ilimitado de blobs.

Os passos seguintes mostram como criar um contentor de BLOBs no Explorador de armazenamento.

1. Abra o Explorador de Armazenamento.
2. No painel esquerdo, expanda a conta de armazenamento no qual pretende criar o contentor de Blobs.
3. Com o botão direito **contentores de BLOBs**e, no menu de contexto - selecione **criar contentor de BLOBs**.

   ![Criar o menu de contexto de contentores de BLOBs][0]
4. Será apresentada uma caixa de texto abaixo a **contentores de BLOBs** pasta. Introduza o nome do contentor de blobs. Consulte a [criar o contentor e definir permissões](storage/blobs/storage-quickstart-blobs-dotnet.md#create-the-container-and-set-permissions) para obter informações sobre regras e restrições à nomenclatura dos contentores de Blobs.

   ![Criar caixa de texto de contentores de BLOBs][1]
5. Prima **Enter** quando terminar de criar o contentor de BLOBs, ou **Esc** para cancelar. Quando o contentor de BLOBs tiver sido criado com êxito, será apresentado sob o **contentores de BLOBs** pasta para a conta de armazenamento selecionada.

   ![Contentor de BLOBs criado][2]

## <a name="view-a-blob-containers-contents"></a>Ver os conteúdos de um contentor de BLOBs
Contentores de BLOBs contenham blobs e pastas (que também podem conter blobs).

Os passos seguintes mostram como ver o conteúdo de um contentor de BLOBs no Explorador de armazenamento:

1. Abra o Explorador de Armazenamento.
2. No painel esquerdo, expanda a conta de armazenamento que contém o contentor de BLOBs que quer ver.
3. Expanda a conta de armazenamento **contentores de BLOBs**.
4. Com o botão direito que pretende ver e, no menu de contexto - selecione o contentor de BLOBs **Editor de contentor de BLOBs aberto**.
   Também pode clicar duas vezes o contentor de BLOBs que quer ver.

   ![Menu de contexto de editor de contentor de BLOBs aberto][19]
5. O painel principal apresenta conteúdo do contentor de Blobs.

   ![Editor de contentor de BLOBs][3]

## <a name="delete-a-blob-container"></a>Eliminar um contentor de BLOBs
Contentores de BLOBs podem ser facilmente criados e eliminados conforme necessário. (Para saber como eliminar blobs individuais, consulte a seção [gerir blobs num contentor de BLOBs](#managing-blobs-in-a-blob-container).)

Os passos seguintes mostram como eliminar um contentor de BLOBs no Explorador de armazenamento:

1. Abra o Explorador de Armazenamento.
2. No painel esquerdo, expanda a conta de armazenamento que contém o contentor de BLOBs que quer ver.
3. Expanda a conta de armazenamento **contentores de BLOBs**.
4. Com o botão direito que pretende eliminar e, no menu de contexto - selecione o contentor de BLOBs **eliminar**.
   Também pode premir **eliminar** para eliminar o contentor de BLOBs atualmente selecionada.

   ![Eliminar o menu de contexto do contentor de BLOBs][4]
5. Selecione **Yes (Sim)**, na caixa de diálogo de confirmação.

   ![Eliminar confirmação de contentor do blob][5]

## <a name="copy-a-blob-container"></a>Copiar um contentor de BLOBs
Explorador de armazenamento permite-lhe copiar um contentor de BLOBs para a área de transferência e, em seguida, cole esse contentor de BLOBs para outra conta de armazenamento. (Para saber como copiar blobs individuais, consulte a seção [gerir blobs num contentor de BLOBs](#managing-blobs-in-a-blob-container).)

Os passos seguintes mostram como copiar um contentor de BLOBs de uma conta de armazenamento para outro.

1. Abra o Explorador de Armazenamento.
2. No painel esquerdo, expanda a conta de armazenamento que contém o contentor de BLOBs que pretende copiar.
3. Expanda a conta de armazenamento **contentores de BLOBs**.
4. Com o botão direito que pretende copiar e, no menu de contexto - selecione o contentor de BLOBs **contentor de BLOBs de cópia**.

   ![Menu de contexto de contentor de blob de cópia][6]
5. Com o botão direito a conta de armazenamento de "destino" pretendida na qual pretende colar o contentor de BLOBs e, no menu de contexto - selecione **colar contentor de BLOBs**.

   ![Menu de contexto de contentor de blob de colar][7]

## <a name="get-the-sas-for-a-blob-container"></a>Obter a SAS para um contentor de blobs
As [assinaturas de acesso partilhado (SAS)](storage/common/storage-dotnet-shared-access-signature-part-1.md) disponibilizam acesso delegado a recursos na sua conta de armazenamento.
Isto significa que pode conceder a um cliente permissões limitadas a objetos na sua conta de armazenamento durante um determinado período de tempo e com um conjunto de permissões especificadas, sem ter de partilhar as chaves de acesso da conta.

Os passos seguintes mostram como criar uma SAS para um contentor de BLOBs:

1. Abra o Explorador de Armazenamento.
2. No painel esquerdo, expanda a conta de armazenamento que contém o contentor de BLOBs para o qual quer obter uma SAS.
3. Expanda a conta de armazenamento **contentores de BLOBs**.
4. O contentor de BLOBs pretendido com o botão direito e, no menu de contexto - selecione **obter assinatura de acesso partilhado**.

   ![Obter o menu de contexto SAS][8]
5. Na caixa de diálogo **Shared Access Signature (Assinatura de Acesso Partilhado)**, especifique a política, as datas de início e de expiração, o fuso horário e os níveis de acesso que atribuir ao recurso.

   ![Obter opções de SAS][9]
6. Quando tiver terminado de especificar as opções de SAS, selecione **Create (Criar)**.
7. Um segundo **assinatura de acesso partilhado** diálogo aparecerão, então, que lista o contentor de BLOBs, juntamente com o URL e QueryStrings que pode utilizar para aceder ao recurso de armazenamento.
   Selecione **Copy (Copiar)**, junto ao URL que quer copiar para a área de transferências.

   ![URLs SAS de cópia][10]
8. Quando terminar, selecione **Close (Fechar)**.

## <a name="manage-access-policies-for-a-blob-container"></a>Gerir políticas de acesso para um contentor de BLOBs
Os passos seguintes mostram como gerir (adicionar e remover) políticas de acesso de um contentor de BLOBs:

1. Abra o Explorador de Armazenamento.
2. No painel esquerdo, expanda a conta de armazenamento que contém o contentor de BLOBs cujas políticas de acesso que pretende gerir.
3. Expanda a conta de armazenamento **contentores de BLOBs**.
4. Selecione o contentor de BLOBs pretendido e, no menu de contexto - selecione **gerir políticas de acesso**.

   ![Menu de Contexto Gerir políticas de acesso][11]
5. O **políticas de acesso** caixa de diálogo listará todas as políticas de acesso já criadas para o contentor de BLOBs selecionados.

   ![Opções de política de acesso][12]        
6. Siga estes passos consoante a tarefa de gestão de política de acesso:

   * **Adicionar uma política de acesso nova** - selecione **Add (Adicionar)**. Depois de gerada, a caixa de diálogo **Access Policies** mostrará a política de acesso acabada de adicionar (com predefinições).
   * **Editar uma política de acesso** - faça eventuais alterações que pretenda e selecione **guardar**.
   * **Remover uma política de acesso** - selecione **Remove (Remover)** junto à política de acesso que pretende remover.

## <a name="set-the-public-access-level-for-a-blob-container"></a>Definir o nível de acesso público para um contentor de BLOBs
Por predefinição, todos os contentores de Blobs está definido para "Nenhum acesso público".

Os passos seguintes mostram como especificar um nível de acesso público para um contentor de Blobs.

1. Abra o Explorador de Armazenamento.
2. No painel esquerdo, expanda a conta de armazenamento que contém o contentor de BLOBs cujas políticas de acesso que pretende gerir.
3. Expanda a conta de armazenamento **contentores de BLOBs**.
4. Selecione o contentor de BLOBs pretendido e, no menu de contexto - selecione **defina o nível de acesso público**.

   ![Defina o menu de contexto de nível de acesso público][13]
5. Na **conjunto de nível de acesso público do contentor** caixa de diálogo, especifique o nível de acesso desejado.

   ![Definir opções de nível de acesso público][14]
6. Selecione **Aplicar**.

## <a name="managing-blobs-in-a-blob-container"></a>Gerir blobs num contentor de BLOBs
Depois de criar um contentor de BLOBs, pode carregar um blob para esse contentor de BLOBs, transferir um blob para o computador local, abra um blob no seu computador local e muito mais.

Os passos seguintes mostram como gerir os blobs (e pastas) dentro de um contentor de Blobs.

1. Abra o Explorador de Armazenamento.
2. No painel esquerdo, expanda a conta de armazenamento que contém o contentor de BLOBs que pretende gerir.
3. Expanda a conta de armazenamento **contentores de BLOBs**.
4. Clique duas vezes o contentor de BLOBs que quer ver.
5. O painel principal apresenta conteúdo do contentor de Blobs.

   ![Contentor de Blobs do Vista][3]
6. O painel principal apresenta conteúdo do contentor de Blobs.
7. Siga estes passos, consoante a tarefa que pretende realizar:

   * **Carregar ficheiros para um contentor de BLOBs**

     1. Na barra de ferramentas do painel principal, selecione **Upload (Carregar)** e, em seguida, **Upload Files (Carregar Ficheiros)**, no menu pendente.

        ![Menu Carregar ficheiros][15]
     2. Na caixa de diálogo **Upload files (Carregar ficheiros)**, selecione o botão de reticências (**...**), no lado direito da caixa de texto **Files (Ficheiros)**, para selecionar o ficheiro ou ficheiros que quer carregar.

        ![Opções de ficheiros de carregamento][16]
     3. Especifique o tipo de **tipo de Blob**. Ver [criar o contentor e definir permissões](storage/blobs/storage-quickstart-blobs-dotnet.md#upload-blobs-to-the-container) para obter mais informações.
     4. Opcionalmente, especifique uma pasta de destino no qual o ficheiro ou ficheiros selecionados serão carregados. Se a pasta de destino não existir, será criada.
     5. Selecione **Upload**.
   * **Carregar uma pasta para um contentor de BLOBs**

     1. Na barra de ferramentas do painel principal, selecione **Upload** e, em seguida, **Upload Folder (Carregar Pasta)**, no menu pendente.

        ![Menu Carregar pasta][17]
     2. Na caixa de diálogo **Upload folder (Carregar pasta)**, selecione o botão de reticências (**...**), no lado direito da caixa de texto **Folder (Pasta)**, para selecionar a pasta cujos conteúdos quer carregar.

        ![Opções de pasta de carregamento][18]
     3. Especifique o tipo de **tipo de Blob**. Ver [criar o contentor e definir permissões](storage/blobs/storage-quickstart-blobs-dotnet.md#upload-blobs-to-the-container) para obter mais informações.
     4. Opcionalmente, especifique uma pasta de destino no qual os conteúdos da pasta selecionada serão carregados. Se a pasta de destino não existir, será criada.
     5. Selecione **Upload**.
   * **Transferir um blob para o computador local**

     1. Selecione o blob que pretende transferir.
     2. Na barra de ferramentas do painel principal, selecione **Download (Transferir)**.
     3. Na **especifique onde pretende guardar o blob transferido** caixa de diálogo, especifique a localização onde pretende que o blob transferido e o nome que pretende dar a ele.  
     4. Selecione **Guardar**.
   * **Abra um blob no seu computador local**

     1. Selecione o blob que pretende abrir.
     2. Na barra de ferramentas do painel principal, selecione **Open (Abrir)**.
     3. O blob será transferido e aberto com a aplicação associada ao tipo de ficheiro subjacente do blob.
   * **Copiar um blob para a área de transferência**

     1. Selecione o blob que pretende copiar.
     2. Na barra de ferramentas do painel principal, selecione **Copy (Copiar)**.
     3. No painel esquerdo, navegue para outro contentor de BLOBs e faça duplo clique para vê-la no painel principal.
     4. Na barra de ferramentas do painel principal, selecione **colar** para criar uma cópia do blob.
   * **Eliminar um blob**

     1. Selecione o blob que pretende eliminar.
     2. Na barra de ferramentas do painel principal, selecione **Delete (Eliminar)**.
     3. Selecione **Yes (Sim)**, na caixa de diálogo de confirmação.

## <a name="next-steps"></a>Passos Seguintes
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

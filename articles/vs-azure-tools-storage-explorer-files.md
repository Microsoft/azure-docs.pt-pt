---
title: Utilizar o Explorador de Armazenamento com o Armazenamento de ficheiros do Azure | Microsoft Docs
description: Saiba como utilizar o Explorador de Armazenamento para trabalhar com ficheiros e partilhas de ficheiros.
services: storage
documentationcenter: na
author: cawaMS
manager: paulyuk
editor: ''
ms.assetid: ''
ms.service: storage
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/09/2017
ms.author: cawa
ms.openlocfilehash: 391c5cc3b25d58faeb2615c29a76164d4e5292f2
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57759144"
---
# <a name="using-storage-explorer-with-azure-file-storage"></a>Utilizar o Explorador de Armazenamento com o Armazenamento de Ficheiros do Azure

O Armazenamento de ficheiros do Azure é um serviço que oferece partilhas de ficheiros na cloud com o Protocolo SMB (Server Message Block) padrão. O SMB 2.1 e o SMB 3.0 são suportados. Com o Armazenamento de ficheiros do Azure, pode migrar aplicações antigas que se baseiam em partilhas de ficheiros para o Azure rapidamente e sem reescritas dispendiosas. Pode utilizar o Armazenamento de ficheiros para expor publicamente os dados ao mundo ou para armazenar dados da aplicação em privado. Neste artigo, vai aprender a utilizar o Explorador de Armazenamento para trabalhar com ficheiros e partilhas de ficheiros.

## <a name="prerequisites"></a>Pré-requisitos

Para executar os passos descritos neste artigo, é necessário o seguinte:

- [Transferir e instalar o Explorador de Armazenamento](https://www.storageexplorer.com/)

- [Ligar a um serviço ou a uma conta de armazenamento do Azure](https://docs.microsoft.com//azure/vs-azure-tools-storage-manage-with-storage-explorer#connect-to-a-storage-account-or-service)

## <a name="create-a-file-share"></a>Criar Partilhas de Ficheiros

Todos os ficheiros têm de residir numa partilha de ficheiros, que é simplesmente um agrupamento lógico de ficheiros. Uma conta pode conter um número ilimitado de partilhas de ficheiros e cada partilha pode armazenar um número ilimitado de ficheiros.

Os passos seguintes mostram como criar uma partilha de ficheiros no Explorador de Armazenamento.

1. Abra o Explorador de Armazenamento.

1. No painel da esquerda, expanda a conta de armazenamento na qual pretende criar a Partilha de Ficheiros.

1. Clique com o botão direito do rato em **File Shares (Partilhas de Ficheiros)** e, no menu de contexto, selecione **Create File Share (Criar Partilha de Ficheiros)**.

    ![Criar a Partilha de Ficheiros](media/vs-azure-tools-storage-explorer-files/image1.png)

1. É apresentada uma caixa de texto abaixo da pasta **File Shares (Partilhas de Ficheiros)**. Introduza o nome da partilha de ficheiros. Veja a secção [Regras de nomenclatura de partilhas](https://docs.microsoft.com//azure/storage/storage-dotnet-how-to-use-blobs) para obter uma lista das regras e restrições à nomenclatura das partilhas de ficheiros.

    ![Dar um nome à partilha](media/vs-azure-tools-storage-explorer-files/image2.png)

1. Quando tiver concluído, prima **Enter** para criar a partilha de ficheiros ou **Esc** para cancelar. Depois de a partilha ser criada com êxito, será apresentada na pasta **File Shares** da conta de armazenamento selecionada.

    ![A nova partilha](media/vs-azure-tools-storage-explorer-files/image3.png)

## <a name="view-a-file-shares-contents"></a>Ver os conteúdos de uma partilha de ficheiros

As partilhas de ficheiros contêm ficheiros e pastas (que também podem conter ficheiros).

Os passos seguintes mostram como ver os conteúdos de uma partilha de ficheiros no Explorador de Armazenamento:+

1. Abra o Explorador de Armazenamento.

1. No painel da esquerda, expanda a conta de armazenamento que contém a partilha de ficheiros que quer ver.

1. Expanda as **Partilhas de Ficheiros** da conta de armazenamento.

1. Clique com o botão direito do rato na partilha que quer ver e, no menu de contexto, selecione **Open (Abrir)**. Também pode fazer duplo clique na partilha de ficheiros desejada.

    ![Abrir a partilha](media/vs-azure-tools-storage-explorer-files/image4.png)

1. O painel principal apresenta os conteúdos da partilha de ficheiros.
    
    ![Os conteúdos da partilha](media/vs-azure-tools-storage-explorer-files/image5.png)

## <a name="delete-a-file-share"></a>Eliminar partilhas de ficheiros

É fácil criar e eliminar partilhas de ficheiros, conforme necessário. (Para saber como eliminar ficheiros individuais, veja a secção [Gerir ficheiros numa partilha de ficheiros](https://docs.microsoft.com//azure/vs-azure-tools-storage-explorer-blobs#managing-blobs-in-a-blob-container).)

Os passos seguintes mostram como eliminar uma partilha de ficheiros no Explorador de Armazenamento:

1. Abra o Explorador de Armazenamento.

1. No painel da esquerda, expanda a conta de armazenamento que contém a partilha de ficheiros que quer ver.

1. Expanda as **Partilhas de Ficheiros** da conta de armazenamento.

1. Clique com o botão direito do rato na partilha que quer eliminar e, no menu de contexto, selecione **Delete (Eliminar)**. Também pode premir **Delete** para eliminar a partilha de ficheiros selecionada atualmente.

    ![Eliminar](media/vs-azure-tools-storage-explorer-files/image6.png)

1. Selecione **Yes (Sim)**, na caixa de diálogo de confirmação.
    
    ![Caixa de diálogo de confirmação](media/vs-azure-tools-storage-explorer-files/image7.png)

## <a name="copy-a-file-share"></a>Copiar partilhas de ficheiros

O Explorador de Armazenamento permite-lhe copiar partilhas de ficheiros para a área de transferências e, em seguida, copiá-las para outra conta de armazenamento. (Para saber como copiar ficheiros individuais, veja a secção [Gerir ficheiros numa partilha de ficheiros](https://docs.microsoft.com//azure/vs-azure-tools-storage-explorer-blobs#managing-blobs-in-a-blob-container).)

Os passos seguintes mostram como copiar uma partilha de ficheiros de uma conta de armazenamento para outra.

1. Abra o Explorador de Armazenamento.

1. No painel da esquerda, expanda a conta de armazenamento que contém a partilha de ficheiros que quer copiar.

1. Expanda as **Partilhas de Ficheiros** da conta de armazenamento.

1. Clique com o botão direito do rato na partilha que quer copiar e, no menu de contexto, selecione **Copy File Share (Copiar Partilha de Ficheiro)**.

    ![Copiar Partilha de Ficheiros](media/vs-azure-tools-storage-explorer-files/image8.png)

1. Clique com o botão direito do rato na conta de armazenamento de “destino” pretendida na qual quer colar a partilha de ficheiros e, no menu de contexto, selecione **Paste File Share (Colar Partilha de Ficheiros)**.

    ![Criar Partilha de Ficheiros](media/vs-azure-tools-storage-explorer-files/image9.png)

## <a name="get-the-sas-for-a-file-share"></a>Obter a SAS de uma partilha de ficheiros

As [assinaturas de acesso partilhado (SAS)](https://docs.microsoft.com//azure/storage/storage-dotnet-shared-access-signature-part-1) disponibilizam acesso delegado a recursos na sua conta de armazenamento. Isto significa que pode conceder a um cliente permissões limitadas a objetos na sua conta de armazenamento durante um determinado período de tempo e com um conjunto de permissões especificadas, sem ter de partilhar as chaves de acesso da conta.

Os passos seguintes mostram como criar uma SAS para uma partilha de ficheiros:

1. Abra o Explorador de Armazenamento.

1. No painel da esquerda, expanda a conta de armazenamento que contém a partilha de ficheiros para a qual quer obter uma SAS

1. Expanda as **Partilhas de Ficheiros** da conta de armazenamento.

1. Clique com o botão direito do rato na partilha de ficheiros pretendida e, no menu de contexto, selecione **Get Shared Access Signature (Obter Assinatura de Acesso Partilhado)**.

    ![Obter Assinatura de Acesso Partilhado](media/vs-azure-tools-storage-explorer-files/image10.png)

1. Na caixa de diálogo **Shared Access Signature (Assinatura de Acesso Partilhado)**, especifique a política, as datas de início e de expiração, o fuso horário e os níveis de acesso que atribuir ao recurso.

    ![Caixa de diálogo SAS](media/vs-azure-tools-storage-explorer-files/image11.png)

1. Quando tiver terminado de especificar as opções de SAS, selecione **Create (Criar)**.

1. É, então, apresentada uma segunda caixa de diálogo **Assinatura de Acesso Partilhado**, que mostra a partilha de ficheiros juntamente com o URL e QueryStrings que pode utilizar para aceder ao recurso do armazenamento. Selecione **Copy (Copiar)**, junto ao URL que quer copiar para a área de transferências.
    
    ![Segunda caixa de diálogo SAS](media/vs-azure-tools-storage-explorer-files/image12.png)

1. Quando terminar, selecione **Close (Fechar)**.

## <a name="manage-access-policies-for-a-file-share"></a>Gerir as Políticas de Acesso de uma partilha de ficheiros

Os passos seguintes mostram como gerir (adicionar e remover) políticas de acesso de uma partilha de ficheiros: As Políticas de Acesso são utilizadas para criar URLs de SAS que as pessoas podem utilizar para aceder ao recurso do Armazenamento de ficheiros durante um período de tempo definido.

1. Abra o Explorador de Armazenamento.

1. No painel da esquerda, expanda a conta de armazenamento que contém a partilha de ficheiros cujas políticas de acesso quer gerir.

1. Expanda as **Partilhas de Ficheiros** da conta de armazenamento.

1. Selecione a partilha de ficheiros pretendida e, no menu de contexto, selecione **Manage Access Policies (Gerir Políticas de Acesso)**.

    ![Menu de Contexto Gerir políticas de acesso](media/vs-azure-tools-storage-explorer-files/image13.png)

1. A caixa de diálogo **Access Policies (Políticas de Acesso)** mostrará todas as políticas de acesso que já tenham sido criadas para a partilha de ficheiros selecionada.
    
    ![Políticas de Acesso](media/vs-azure-tools-storage-explorer-files/image14.png)

1. Siga estes passos consoante a tarefa de gestão de política de acesso:
    
    - **Adicionar uma política de acesso nova** - selecione **Add (Adicionar)**. Depois de gerada, a caixa de diálogo **Access Policies** mostrará a política de acesso acabada de adicionar (com predefinições).

    - **Editar uma política de acesso** - faça eventuais alterações que pretenda e selecione **Save (Guardar)**.

    - **Remover uma política de acesso** - selecione **Remove (Remover)** junto à política de acesso que pretende remover.

1. Utilize a Política de Acesso que criou anteriormente para criar um URL de SAS novo:
    
    ![Obter SAS](media/vs-azure-tools-storage-explorer-files/image15.png)
    
    ![Nome e propriedades de SAS](media/vs-azure-tools-storage-explorer-files/image16.png)

## <a name="managing-files-in-a-file-share"></a>Gerir ficheiros numa partilha de ficheiros

Depois de criar uma partilha de ficheiros, pode carregar um ficheiro para a mesma, transferir um ficheiro para o seu computador local, abrir um ficheiro no computador local e muito mais.

Os passos seguintes mostram como gerir ficheiros (e pastas) numa partilha de ficheiros.

1.  Abra o Explorador de Armazenamento.

1.  No painel da esquerda, expanda a conta de armazenamento que contém a partilha de ficheiros que quer gerir.

1.  Expanda as **Partilhas de Ficheiros** da conta de armazenamento.

1.  Faça duplo clique na partilha de ficheiros que quer ver.

1.  O painel principal apresenta os conteúdos da partilha de ficheiros.

    ![Os conteúdos da partilha](media/vs-azure-tools-storage-explorer-files/image17.png)

1.  O painel principal apresenta os conteúdos da partilha de ficheiros.

1.  Siga estes passos, consoante a tarefa que pretende realizar:

    - **Carregar ficheiros para uma partilha de ficheiros**

        a.  Na barra de ferramentas do painel principal, selecione **Upload (Carregar)** e, em seguida, **Upload Files (Carregar Ficheiros)**, no menu pendente.

        ![Carregar ficheiros](media/vs-azure-tools-storage-explorer-files/image18.png)
        
        b. Na caixa de diálogo **Upload files (Carregar ficheiros)**, selecione o botão de reticências (**...**), no lado direito da caixa de texto **Files (Ficheiros)**, para selecionar o ficheiro ou ficheiros que quer carregar.

        ![Adição ficheiros](media/vs-azure-tools-storage-explorer-files/image19.png)

        c. Selecione **Upload**.

    - **Carregar uma pasta para uma partilha de ficheiros**
        
        a. Na barra de ferramentas do painel principal, selecione **Upload** e, em seguida, **Upload Folder (Carregar Pasta)**, no menu pendente.

        ![Menu Carregar pasta](media/vs-azure-tools-storage-explorer-files/image20.png)

        b. Na caixa de diálogo **Upload folder (Carregar pasta)**, selecione o botão de reticências (**...**), no lado direito da caixa de texto **Folder (Pasta)**, para selecionar a pasta cujos conteúdos quer carregar.

        c. Opcionalmente, especifique uma pasta de destino no qual os conteúdos da pasta selecionada serão carregados. Se a pasta de destino não existir, será criada.

        d. Selecione **Upload**.

    - **Transferir um ficheiro para o seu computador local**
        
        a. Selecione o ficheiro que pretende transferir.
        
        b. Na barra de ferramentas do painel principal, selecione **Download (Transferir)**.
        
        c. Na caixa de diálogo **Specify where to save the downloaded file (Especificar onde guardar o ficheiro transferido)**, especifique a localização para onde quer transferir o ficheiro e o nome que lhe que dar.

        d. Selecione **Guardar**.

    - **Abrir um ficheiro no seu computador local**
        
        a.  Selecione o ficheiro que pretende abrir.
        
        b.  Na barra de ferramentas do painel principal, selecione **Open (Abrir)**.
        
        c.  O ficheiro é transferido e aberto com a aplicação associada ao respetivo tipo de ficheiro subjacente.

    - **Copiar um ficheiro para a área de transferência**

        a. Selecione o ficheiro que pretende copiar.

        b. Na barra de ferramentas do painel principal, selecione **Copy (Copiar)**.

        c. No painel da esquerda, navegue para outra partilha de ficheiros e faça duplo clique na mesma para vê-la no painel principal.

        d. Na barra de ferramentas do painel principal, selecione **Paste (Colar)**, para criar uma cópia do ficheiro.

    - **Eliminar um ficheiro**

        a. Selecione o ficheiro que pretende eliminar.

        b. Na barra de ferramentas do painel principal, selecione **Delete (Eliminar)**.

        c. Selecione **Yes (Sim)**, na caixa de diálogo de confirmação.

## <a name="next-steps"></a>Passos Seguintes

- Vejas os [mais recentes vídeos e notas de versão do Explorador de Armazenamento](https://www.storageexplorer.com/).

- Saiba como [utilizar blobs, tabelas, filas e ficheiros do Azure para criar aplicações](https://azure.microsoft.com/documentation/services/storage/).

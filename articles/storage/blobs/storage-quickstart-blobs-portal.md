---
title: Início Rápido do Azure – Criar um blob no armazenamento de objetos com o portaI do Azure | Microsoft Docs
description: Neste início rápido, irá utilizar o portal do Azure no armazenamento de objetos (Blobs). Em seguida, utilize o portal do Azure para carregar um blob para o Armazenamento do Microsoft Azure, transferir um blob e listar os blobs num contentor.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 10/22/2018
ms.author: tamram
ms.openlocfilehash: e615585fbdbef634a13a19aca4b85eef5b21815f
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2018
ms.locfileid: "50740379"
---
# <a name="quickstart-upload-download-and-list-blobs-with-the-azure-portal"></a>Início Rápido: carregar, transferir e listar blobs com o portal do Azure

Neste guia de introdução, irá aprender a utilizar o [portal do Azure](https://portal.azure.com/) para criar um contentor no Armazenamento do Azure e para carregar e transferir blobs de blocos nesse contentor.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

## <a name="create-a-container"></a>Criar um contentor

Para criar um contentor no portal do Azure, siga estes passos:

1. No portal do Azure, navegue para a sua nova conta de armazenamento.
2. No menu à esquerda para a conta de armazenamento, desloque-se para a secção **Serviço blob** e, em seguida, selecione **Blobs**.
3. Selecione o botão **+ Contentor**.
4. Escreva um nome para o novo contentor. O nome do contentor tem estar em minúsculas, tem de começar com uma letra ou um número e só pode incluir letras, números e o caráter de travessão (-). Para obter mais informações sobre os nomes dos contentores e dos blobs, veja [Nomenclatura e referência para contentores, blobs e metadados](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).
5. Defina o nível de acesso público ao contentor. O nível predefinido é **Privado (sem acesso anónimo)**.
6. Selecione **OK** para criar o contentor.

    ![A captura de ecrã mostra como criar um contentor no portal do Azure](media/storage-quickstart-blobs-portal/create-container.png)

## <a name="upload-a-block-blob"></a>Carregar um blob de blocos

Os blobs de blocos são compostos por blocos de dados reunidos para criar um blob. A maioria dos cenários com o armazenamento de Blobs emprega blobs de blocos. Os blobs de blocos são ideais para armazenar dados de texto e binários na cloud, como ficheiros, imagens e vídeos. Este guia de introdução mostra como trabalhar com blobs de blocos. 

Para carregar um blob de blocos para o novo contentor no portal do Azure, siga estes passos:

1. No portal do Azure, navegue para o contentor que criou na secção anterior.
2. Selecione o contentor para mostrar uma lista dos blobs que ele contém. Uma vez que este contentor é novo, ainda não contém blobs.
3. Selecione o botão **Carregar** para carregar um blob para o contentor.
4. Procurar o sistema de ficheiros local para encontrar um ficheiro para carregar como um blob de blocos e selecione **Carregar**.
     
    ![A captura de ecrã mostra como carregar um blob a partir do disco local](media/storage-quickstart-blobs-portal/upload-blob.png)

5. Selecione o **Tipo de autenticação**. A predefinição é **SAS**.
6. Carregue os blobs que quiser desta forma. Verá que os novos blobs estão agora listados no contentor.

## <a name="download-a-block-blob"></a>Transferir um blob de blocos

Pode transferir um blob de blocos para apresentar no browser ou guardar no seu sistema de ficheiros local. Para transferir um blob de blocos, siga estes passos:

1. Navegue para a lista de blobs que carregou na secção anterior. 
2. Clique com o botão direito do rato no blob que pretende transferir e selecione **Transferir**. 

## <a name="clean-up-resources"></a>Limpar recursos

Para remover os recursos que criou neste início rápido, pode eliminar o contentor. Todos os blobs no contentor também serão eliminados.

Para eliminar o contentor:

1. No portal do Azure, navegue para a lista de contentores na sua conta de armazenamento.
2. Selecione o contentor a eliminar.
3. Selecione o botão **Mais** (**...** ) e selecione **Eliminar**.
4. Certifique-se de que pretende eliminar o contentor.

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, aprendeu a transferir ficheiros entre um disco local e o armazenamento de Blobs do Azure com o portal do Azure. Para saber mais sobre a utilização do armazenamento de Blobs, avance para os procedimentos do armazenamento de blobs.

> [!div class="nextstepaction"]
> [Procedimentos de Operações de Armazenamento de Blobs](storage-dotnet-how-to-use-blobs.md)


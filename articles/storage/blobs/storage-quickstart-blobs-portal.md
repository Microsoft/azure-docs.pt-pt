---
title: Quickstart - Crie uma bolha com o portal Azure
titleSuffix: Azure Storage
description: Neste início rápido, irá utilizar o portal do Azure no armazenamento de objetos (Blobs). Em seguida, utilize o portal do Azure para carregar um blob para o Armazenamento do Microsoft Azure, transferir um blob e listar os blobs num contentor.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.date: 10/19/2020
ms.author: tamram
ms.openlocfilehash: 0bf9d6eb68536588b35df93e13b04841d7868d31
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92547163"
---
# <a name="quickstart-upload-download-and-list-blobs-with-the-azure-portal"></a>Início Rápido: carregar, transferir e listar blobs com o portal do Azure

Neste guia de introdução, irá aprender a utilizar o [portal do Azure](https://portal.azure.com/) para criar um contentor no Armazenamento do Azure e para carregar e transferir blobs de blocos nesse contentor.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

## <a name="create-a-container"></a>Criar um contentor

Para criar um contentor no portal do Azure, siga estes passos:

1. No portal do Azure, navegue para a sua nova conta de armazenamento.
2. No menu esquerdo para a conta de armazenamento, percorra a secção **de serviço Blob** e, em seguida, selecione **Recipientes** .
3. Selecione o botão **+ Contentor** .
4. Escreva um nome para o novo contentor. O nome do contentor tem estar em minúsculas, tem de começar com uma letra ou um número e só pode incluir letras, números e o caráter de travessão (-). Para obter mais informações sobre os nomes dos recipientes e blobs, consulte [o nome e os recipientes de referência, bolhas e metadados.](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
5. Defina o nível de acesso público ao contentor. O nível predefinido é **Privado (sem acesso anónimo)** .
6. Selecione **OK** para criar o contentor.

    :::image type="content" source="media/storage-quickstart-blobs-portal/create-container.png" alt-text="A captura de ecrã mostra como criar um contentor no portal do Azure":::

## <a name="upload-a-block-blob"></a>Carregar um blob de blocos

Os blobs de blocos são compostos por blocos de dados reunidos para criar um blob. A maioria dos cenários com o armazenamento de Blobs emprega blobs de blocos. Os blobs de blocos são ideais para armazenar dados de texto e binários na cloud, como ficheiros, imagens e vídeos. Este guia de introdução mostra como trabalhar com blobs de blocos.

Para carregar um blob de blocos para o novo contentor no portal do Azure, siga estes passos:

1. No portal do Azure, navegue para o contentor que criou na secção anterior.
1. Selecione o contentor para mostrar uma lista dos blobs que ele contém. Este contentor é novo, por isso ainda não contém bolhas.
1. Selecione o botão **Upload** para abrir a lâmina de upload e navegue no seu sistema de ficheiros local para encontrar um ficheiro para carregar como uma bolha de bloco. Pode expandir opcionalmente a secção **Advanced** para configurar outras definições para a operação de upload.

    :::image type="content" source="media/storage-quickstart-blobs-portal/upload-blob.png" alt-text="A captura de ecrã mostra como criar um contentor no portal do Azure":::

1. Selecione o botão **Upload** para carregar a bolha.
1. Carregue os blobs que quiser desta forma. Verá que os novos blobs estão agora listados no contentor.

## <a name="download-a-block-blob"></a>Transferir um blob de blocos

Pode transferir um blob de blocos para apresentar no browser ou guardar no seu sistema de ficheiros local. Para transferir um blob de blocos, siga estes passos:

1. Navegue para a lista de blobs que carregou na secção anterior.
1. Clique com o botão direito do rato no blob que pretende transferir e selecione **Transferir** .

    :::image type="content" source="media/storage-quickstart-blobs-portal/download-blob.png" alt-text="A captura de ecrã mostra como criar um contentor no portal do Azure":::

## <a name="delete-a-block-blob"></a>Apagar uma bolha de bloco

Para eliminar uma ou mais bolhas no portal Azure, siga estes passos:

1. No portal Azure, navegue para o contentor.
1. Exiba a lista de bolhas no recipiente.
1. Utilize a caixa de verificação para selecionar uma ou mais bolhas da lista.
1. Selecione o botão **Eliminar** para eliminar as bolhas selecionadas.
1. No diálogo, confirme a eliminação e indique se também pretende eliminar as imagens blob.

:::image type="content" source="media/storage-quickstart-blobs-portal/delete-blobs.png" alt-text="A captura de ecrã mostra como criar um contentor no portal do Azure":::

## <a name="clean-up-resources"></a>Limpar os recursos

Para remover todos os recursos que criou neste arranque rápido, pode simplesmente apagar o recipiente. Todos os blobs no contentor também serão eliminados.

Para eliminar o contentor:

1. No portal do Azure, navegue para a lista de contentores na sua conta de armazenamento.
1. Selecione o contentor a eliminar.
1. Selecione o botão **Mais** ( **...** ) e selecione **Eliminar** .
1. Certifique-se de que pretende eliminar o contentor.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a criar um recipiente e a carregar uma bolha com o portal Azure. Para aprender a trabalhar com o armazenamento blob a partir de uma aplicação web, continue a um tutorial que mostre como fazer upload de imagens para uma conta de armazenamento.

> [!div class="nextstepaction"]
> [Tutorial: Carregar dados de imagem na nuvem com armazenamento Azure](storage-upload-process-images.md)

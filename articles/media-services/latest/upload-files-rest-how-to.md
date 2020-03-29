---
title: Faça upload de ficheiros numa conta V3 do Azure Media Services utilizando o REST [ Microsoft Docs
description: Aprenda a obter conteúdos de mídia nos Media Services criando e carregando ativos.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 38d46978e37ead59deb17a86f643df041452e497
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76705773"
---
# <a name="upload-files-into-a-media-services-v3-account-using-rest"></a>Faça upload de ficheiros numa conta V3 dos Media Services utilizando o REST

Nos Serviços de Media, envia os seus ficheiros digitais para um contentor blob associado a um ativo. A entidade [Asset](https://docs.microsoft.com/rest/api/media/operations/asset) pode conter vídeo, áudio, imagens, coleções de miniaturas, faixas de texto e ficheiros de legendas fechados (e os metadados sobre estes ficheiros). Uma vez que os ficheiros são enviados para o recipiente do ativo, o seu conteúdo é armazenado de forma segura na nuvem para posterior processamento e streaming.

Este artigo mostra-lhe como carregar um ficheiro local usando o REST.

## <a name="prerequisites"></a>Pré-requisitos

Para completar os passos descritos neste tópico, tem de:

- Rever o [conceito de Ativo.](assets-concept.md)
- [Configure Postman para Azure Media Services REST API chamadas](media-rest-apis-with-postman.md).
    
    Certifique-se de seguir o último passo no tópico [Get Azure AD Token](media-rest-apis-with-postman.md#get-azure-ad-token). 

## <a name="create-an-asset"></a>Criar um ativo

Esta secção mostra como criar um novo Ativo.

1. Selecione **Ativos** -> **Criar ou atualizar um Ativo**.
2. Prima **Enviar**.

    ![Criar um ativo](./media/upload-files/postman-create-asset.png)

Você vê a **Resposta** com a informação sobre o ativo recém-criado.

## <a name="get-a-sas-url-with-read-write-permissions"></a>Obtenha um URL SAS com permissões de leitura 

Esta secção mostra como obter um URL SAS que foi gerado para o ativo criado. O URL SAS foi criado com permissões de leitura e pode ser usado para carregar ficheiros digitais no recipiente Asset.

1. Selecione **Ativos** -> **Listar os URLs de Ativos**.
2. Prima **Enviar**.

    ![Carregar um ficheiro](./media/upload-files/postman-create-sas-locator.png)

Você vê a **Resposta** com a informação sobre urls de ativos. Copie o primeiro URL e use-o para fazer upload do seu ficheiro.

## <a name="upload-a-file-to-blob-storage-using-the-upload-url"></a>Faça upload de um ficheiro para o armazenamento de blob usando o URL de upload

Utilize as APIs ou SDKs de armazenamento azure (por exemplo, o [Storage REST API](../../storage/common/storage-rest-api-auth.md) ou [.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md).

## <a name="next-steps"></a>Passos seguintes

[Tutorial: Codificar um ficheiro remoto com base em URL e transmitir o vídeo - REST](stream-files-tutorial-with-rest.md)

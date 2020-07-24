---
title: Faça upload de ficheiros para uma conta V3 da Azure Media Services usando REST Microsoft Docs
description: Saiba como obter conteúdo sonoro nos Media Services criando e carregando ativos.
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
ms.openlocfilehash: 57839c82af71f7cedfe1b3c77095210d273be4a1
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87091849"
---
# <a name="upload-files-into-a-media-services-v3-account-using-rest"></a>Faça upload de ficheiros para uma conta de Media Services v3 usando REST

Nos Serviços de Comunicação Social, você envia os seus ficheiros digitais para um recipiente de bolhas associado a um ativo. A entidade [Asset](/rest/api/media/operations/asset) pode conter vídeo, áudio, imagens, recolhas de miniaturas, faixas de texto e ficheiros de legendas fechados (e os metadados sobre estes ficheiros). Uma vez que os ficheiros são enviados para o recipiente do ativo, o seu conteúdo é armazenado de forma segura na nuvem para posterior processamento e streaming.

Este artigo mostra-lhe como fazer o upload de um ficheiro local utilizando REST.

## <a name="prerequisites"></a>Pré-requisitos

Para completar os passos descritos neste tópico, tem de:

- Rever [conceito de ativo.](assets-concept.md)
- [Configure Carteiro para Azure Media Services REST Chamadas API](media-rest-apis-with-postman.md).
    
    Certifique-se de seguir o último passo do tema [Get Azure AD Token](media-rest-apis-with-postman.md#get-azure-ad-token). 

## <a name="create-an-asset"></a>Criar um recurso

Esta secção mostra como criar um novo Ativo.

1. Selecione **Ativos**  ->  **Criar ou atualizar um Ativo**.
2. Prima **Enviar**.

    ![Criar um recurso](./media/upload-files/postman-create-asset.png)

Vê a **Resposta** com a informação sobre o bem recém-criado.

## <a name="get-a-sas-url-with-read-write-permissions"></a>Obtenha um URL SAS com permissões de leitura-escrita 

Esta secção mostra como obter um URL SAS que foi gerado para o ativo criado. O URL SAS foi criado com permissões de leitura-escrita e pode ser usado para enviar ficheiros digitais para o contentor Ativo.

1. Selecione A lista **de ativos**  ->  **urls**.
2. Prima **Enviar**.

    ![Carregar um ficheiro](./media/upload-files/postman-create-sas-locator.png)

Vê a **Resposta** com a informação sobre os URLs do ativo. Copie o primeiro URL e use-o para fazer o upload do seu ficheiro.

## <a name="upload-a-file-to-blob-storage-using-the-upload-url"></a>Faça o upload de um ficheiro para o armazenamento de blob usando o URL de upload

Utilize as APIs ou SDKs de armazenamento Azure (por exemplo, o [Rest API](../../storage/common/storage-rest-api-auth.md) de Armazenamento ou [.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md).

## <a name="next-steps"></a>Passos seguintes

[Tutorial: Encode a remote file based on URL and stream the video - REST](stream-files-tutorial-with-rest.md) (Codificar ficheiros remotos com base no URL e transmitir o vídeo em fluxo - REST)

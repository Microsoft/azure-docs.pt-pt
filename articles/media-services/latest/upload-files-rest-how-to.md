---
title: Carregar arquivos em uma conta dos serviços de mídia do Azure usando REST | Microsoft Docs
description: Saiba como obter conteúdo de mídia nos serviços de mídia criando e carregando ativos.
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
ms.openlocfilehash: 78c07330558241c889f284bcaf7302ce1327b5b8
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70389805"
---
# <a name="upload-files-into-a-media-services-account-using-rest"></a>Carregar arquivos em uma conta dos serviços de mídia usando REST

Nos serviços de mídia, você carrega seus arquivos digitais em um contêiner de blob associado a um ativo. A entidade [Asset](https://docs.microsoft.com/rest/api/media/operations/asset) pode conter vídeo, áudio, imagens, coleções de miniaturas, faixas de texto e arquivos de legenda codificada (e os metadados sobre esses arquivos). Depois que os arquivos são carregados no contêiner do ativo, seu conteúdo é armazenado com segurança na nuvem para processamento e streaming adicionais.

Este artigo mostra como carregar um arquivo local usando REST.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir os passos descritos neste tópico, tem de:

- Examine o [conceito de ativo](assets-concept.md).
- [Configurar o Postman para chamar a API de REST de serviços de multimédia do Azure](media-rest-apis-with-postman.md).
    
    Certifique-se de seguir a última etapa no tópico [obter token do Azure ad](media-rest-apis-with-postman.md#get-azure-ad-token). 

## <a name="create-an-asset"></a>Criar um ativo

Esta seção mostra como criar um novo ativo.

1. Selecione **ativos** -> **criar ou atualizar um ativo**.
2. Prima **Enviar**.

    ![Criar um ativo](./media/upload-files/postman-create-asset.png)

Você verá a **resposta** com as informações sobre o ativo recém-criado.

## <a name="get-a-sas-url-with-read-write-permissions"></a>Obter uma URL SAS com permissões de leitura/gravação 

Esta seção mostra como obter uma URL SAS que foi gerada para o ativo criado. A URL SAS foi criada com permissões de leitura/gravação e pode ser usada para carregar arquivos digitais no contêiner de ativos.

1. Selecione **ativos** -> **listar as URLs de ativo**.
2. Prima **Enviar**.

    ![Carregar um ficheiro](./media/upload-files/postman-create-sas-locator.png)

Você verá a **resposta** com as informações sobre URLs do ativo. Copie a primeira URL e use-a para carregar o arquivo.

## <a name="upload-a-file-to-blob-storage-using-the-upload-url"></a>Carregar um arquivo no armazenamento de BLOBs usando a URL de carregamento

Use as APIs de armazenamento do Azure ou SDKs (por exemplo, a [API REST de armazenamento](../../storage/common/storage-rest-api-auth.md) ou o [SDK do .net](../../storage/blobs/storage-quickstart-blobs-dotnet.md).

## <a name="next-steps"></a>Passos Seguintes

[Tutorial: Encode a remote file based on URL and stream the video - REST](stream-files-tutorial-with-rest.md) (Codificar ficheiros remotos com base no URL e transmitir o vídeo em fluxo - REST)

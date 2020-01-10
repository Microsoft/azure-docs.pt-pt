---
title: Carregar arquivos em uma conta dos serviços de mídia do Azure v3 usando REST | Microsoft Docs
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
ms.openlocfilehash: a9fd38b857f513ff67ae2fbe4ccd0c6c9540967e
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75779166"
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

## <a name="next-steps"></a>Passos seguintes

[Tutorial: codificar um arquivo remoto com base na URL e transmitir o vídeo-REST](stream-files-tutorial-with-rest.md)

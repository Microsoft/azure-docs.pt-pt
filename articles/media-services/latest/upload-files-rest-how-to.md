---
title: Carregar ficheiros para uma conta de Media Services do Azure com REST | Documentos da Microsoft
description: Saiba como obter o conteúdo de mídia para os serviços multimédia criando e carregando ativos.
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
ms.openlocfilehash: a6f872880b61a5bd9510abda2f15e2edea16e940
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67703883"
---
# <a name="upload-files-into-a-media-services-account-using-rest"></a>Carregar ficheiros para uma conta de serviços de multimédia com REST

Serviços de multimédia, vai carregar os ficheiros digitais são para um contêiner de blob associado a um recurso. O [Asset](https://docs.microsoft.com/rest/api/media/operations/asset) entidade pode conter vídeo, áudio, imagens, coleções de miniaturas, texto faixas e legendagem de áudio ficheiros (e os metadados relativos a esses ficheiros). Assim que os ficheiros são carregados para o contentor do recurso, o conteúdo estiver armazenado em segurança na cloud para processamento adicional e transmissão em fluxo.

Este artigo mostra-lhe como carregar um ficheiro local com o REST.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir os passos descritos neste tópico, tem de:

- Revisão [conceito de ativos](assets-concept.md).
- [Configurar o Postman para chamar a API de REST de serviços de multimédia do Azure](media-rest-apis-with-postman.md).
    
    Lembre-se de que siga o último passo no tópico [obter do Azure AD Token](media-rest-apis-with-postman.md#get-azure-ad-token). 

## <a name="create-an-asset"></a>Criar um elemento

Esta secção mostra como criar um novo elemento.

1. Selecione **ativos** -> **criar ou atualizar um recurso de**.
2. Prima **Enviar**.

    ![Criar um elemento](./media/upload-files/postman-create-asset.png)

Verá o **resposta** com as informações sobre o recurso criado recentemente.

## <a name="get-a-sas-url-with-read-write-permissions"></a>Obter um URL de SAS com permissões de leitura / escrita 

Esta secção mostra como obter um URL de SAS que foi gerado para o recurso criado. O URL de SAS foi criado com permissões de leitura / escrita e podem ser utilizado para ficheiros digitais são carregados para o contentor de elemento.

1. Selecione **ativos** -> **lista os URLs de Asset**.
2. Prima **Enviar**.

    ![Carregar um ficheiro](./media/upload-files/postman-create-sas-locator.png)

Verá o **resposta** com as informações sobre URLs de elementos. Copie o URL do primeiro e utilize-o para carregar o ficheiro.

## <a name="upload-a-file-to-blob-storage-using-the-upload-url"></a>Carregar um ficheiro para o armazenamento de Blobs com o URL de carregamento

Utilize as APIs de armazenamento do Azure ou SDKs (por exemplo, o [API do REST de armazenamento](../../storage/common/storage-rest-api-auth.md), [SDK de JAVA](../../storage/blobs/storage-quickstart-blobs-java-v10.md), ou [SDK de .NET](../../storage/blobs/storage-quickstart-blobs-dotnet.md).

## <a name="next-steps"></a>Passos seguintes

[Tutorial: Encode a remote file based on URL and stream the video - REST](stream-files-tutorial-with-rest.md) (Codificar ficheiros remotos com base no URL e transmitir o vídeo em fluxo - REST)

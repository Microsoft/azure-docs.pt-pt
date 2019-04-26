---
title: Configurar o Postman para chamadas de API de REST de serviços de multimédia do Azure
description: Saiba como configurar o Postman para chamadas de API de REST dos serviços de multimédia.
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
ms.date: 04/11/2019
ms.author: juliako
ms.openlocfilehash: a2171ff8a4354a59ec2f790f9bf38b7a687419ca
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60322554"
---
# <a name="configure-postman-for-media-services-rest-api-calls"></a>Configurar o Postman para chamadas de API de REST dos serviços de multimédia

Este artigo mostra-lhe como configurar **Postman** para que possa ser utilizada para chamar as APIs REST do Azure Media Services (AMS). O artigo mostra como importar ficheiros para o ambiente e a coleção **Postman**. A coleção contém definições agrupadas de pedidos HTTP que chamam as APIs REST do Azure Media Services (AMS). O ficheiro de ambiente contém variáveis que são utilizadas pela coleção.

Antes de começar a desenvolver, reveja [desenvolver com os serviços de multimédia v3 APIs](media-services-apis-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

- [Criar uma conta de Media Services](create-account-cli-how-to.md). Lembre-se de que não se esqueça de que o nome do grupo de recursos e o nome da conta dos serviços de multimédia. 
- Obtenha informações necessárias para [aceder a APIs](access-api-cli-how-to.md)
- Instale o cliente REST do [Postman](https://www.getpostman.com/) para executar as API REST mostradas em alguns dos tutoriais sobre AMS REST. 

    Estamos a utilizar o **Postman**, mas qualquer ferramenta REST seria adequada. Outras alternativas: **Visual Studio Code** com o plug-in do REST ou **Telerik Fiddler**. 

## <a name="download-postman-files"></a>Transferir ficheiros do Postman

Clone o repositório do GitHub que contém os ficheiros de ambiente e coleção do Postman.

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-rest-postman.git
 ```

## <a name="configure-postman"></a>Configurar o Postman

Esta secção configura o Postman.

### <a name="configure-the-environment"></a>Configurar o ambiente 

1. Abra o **Postman**.
2. À direita do ecrã, selecione a opção **Gerir ambiente**.

    ![Gerir ambiente](./media/develop-with-postman/postman-import-env.png)
4. Na caixa de diálogo **Gerir ambiente**, clique em **Importar**.
2. Navegue até ao ficheiro `Azure Media Service v3 Environment.postman_environment.json` que foi transferido quando clonou `https://github.com/Azure-Samples/media-services-v3-rest-postman.git`.
6. O ambiente **Ambiente v3 do Serviço de Multimédia do Azure** é adicionado.

    > [!Note]
    > Atualize as variáveis de acesso com valores obtidos na secção **Aceder à API dos Serviços de Multimédia** acima.

7. Faça duplo clique no ficheiro selecionado e introduza os valores que obteve ao seguir os passos de API de acesso.
8. Feche a caixa de diálogo.
9. Selecione o **Ambiente v3 do Serviço de Multimédia do Azure** na lista pendente.

    ![Escolher ambiente](./media/develop-with-postman/choose-env.png)
   
### <a name="configure-the-collection"></a>Configurar a coleção

1. Clique em **Importar** para importar o ficheiro de coleção.
1. Navegue até ao ficheiro `Media Services v3.postman_collection.json` que foi transferido quando clonou `https://github.com/Azure-Samples/media-services-v3-rest-postman.git`
3. Escolha o ficheiro **Media Services v3.postman_collection.json**.

    ![Importar um ficheiro](./media/develop-with-postman/postman-import-collection.png)

## <a name="get-azure-ad-token"></a>Obter o Token do Microsoft Azure AD 

Antes de começar a manipulação de recursos do AMS v3 terá de obter e definir o Token do Azure AD para autenticação do Principal de serviço.

1. Na janela à esquerda do Postman, selecione "passo 1: Obter autenticação do AAD token".
2. Em seguida, selecione "Obter o Token do Microsoft Azure AD para Autenticação Principal de Serviço".
3. Prima **Enviar**.

    A seguinte operação **POST** é enviada.

    ```
    https://login.microsoftonline.com/:tenantId/oauth2/token
    ```

4. A resposta volta com o token e define a variável de ambiente "AccessToken" como o valor de token.  

    ![Obter token do AAD](./media/develop-with-postman/postman-get-aad-auth-token.png)

## <a name="see-also"></a>Consulte também

- [Carregar ficheiros para uma conta de Media Services - REST](upload-files-rest-how-to.md)
- [Criar filtros com serviços de multimédia - REST](filters-dynamic-manifest-rest-howto.md)
- [API REST baseada no Azure Resource Manager](https://github.com/Azure-Samples/media-services-v3-arm-templates)

## <a name="next-steps"></a>Passos Seguintes

- [Stream ficheiros com REST](stream-files-tutorial-with-rest.md).  
- [Tutorial: Encode a remote file based on URL and stream the video - REST](stream-files-tutorial-with-rest.md) (Codificar ficheiros remotos com base no URL e transmitir o vídeo em fluxo - REST)

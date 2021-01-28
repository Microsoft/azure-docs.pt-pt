---
title: Configure Carteiro para Azure Media Services v3 REST API
description: Este artigo mostra-lhe como configurar o Carteiro para que possa ser usado para chamar APIs de REST da Azure Media Services (AMS).
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: b0a521ddeee6d93ce809e5e6e85eefa1f01a3f12
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98953976"
---
# <a name="configure-postman-for-media-services-v3-rest-api-calls"></a>Configure Carteiro para Serviços de Mídia v3 REST Chamadas API

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Este artigo mostra-lhe como configurar **o Carteiro** para que possa ser usado para chamar APIs de REST da Azure Media Services (AMS). O artigo mostra como importar ambiente e recolha de ficheiros para **o Carteiro.** A coleção contém definições agrupadas de pedidos HTTP que chamam Azure Media Services (AMS) REST APIs. O ficheiro de ambiente contém variáveis que são utilizadas pela coleção.

Antes de começar a [desenvolver,reveja o Desenvolvimento com os Serviços de Media v3 APIs.](media-services-apis-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

- [Criar uma conta de Serviços de Comunicação](./create-account-howto.md)Social. Lembre-se do nome do grupo de recursos e do nome da conta dos Serviços de Comunicação Social. 
- Obtenha informações necessárias para aceder a [APIs](./access-api-howto.md)
- Instale o cliente REST do [Postman](https://www.getpostman.com/) para executar as API REST mostradas em alguns dos tutoriais sobre AMS REST. 

    Estamos a utilizar o **Postman**, mas qualquer ferramenta REST seria adequada. Outras alternativas são: **Código de Estúdio Visual** com o plugin REST ou o **Fiddler Telerik**. 

> [!IMPORTANT]
> Rever [convenções de nomeação](media-services-apis-overview.md#naming-conventions).

## <a name="download-postman-files"></a>Transferir ficheiros do Postman

Clone o repositório do GitHub que contém os ficheiros de ambiente e coleção do Postman.

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-rest-postman.git
 ```

## <a name="configure-postman"></a>Configurar o Postman

### <a name="configure-the-environment"></a>Configurar o ambiente 

1. Abra a aplicação **do Carteiro.**
2. À direita do ecrã, selecione a opção **Gerir ambiente**.

    ![Gerir ambiente](./media/develop-with-postman/postman-import-env.png)
4. Na caixa de diálogo **Gerir ambiente**, clique em **Importar**.
2. Navegue até ao ficheiro `Azure Media Service v3 Environment.postman_environment.json` que foi transferido quando clonou `https://github.com/Azure-Samples/media-services-v3-rest-postman.git`.
6. O ambiente **Ambiente v3 do Serviço de Multimédia do Azure** é adicionado.

    > [!Note]
    > Atualize as variáveis de acesso com valores obtidos na secção **Aceder à API dos Serviços de Multimédia** acima.

7. Faça duplo clique no ficheiro selecionado e introduza os valores que obteve após seguir os passos de Acesso à API.
8. Feche a caixa de diálogo.
9. Selecione o **Ambiente v3 do Serviço de Multimédia do Azure** na lista pendente.

    ![Escolher ambiente](./media/develop-with-postman/choose-env.png)
   
### <a name="configure-the-collection"></a>Configurar a coleção

1. Clique em **Importar** para importar o ficheiro de coleção.
1. Navegue até ao ficheiro `Media Services v3.postman_collection.json` que foi transferido quando clonou `https://github.com/Azure-Samples/media-services-v3-rest-postman.git`
3. Escolha o ficheiro **Media Services v3.postman_collection.json**.

    ![Importar um ficheiro](./media/develop-with-postman/postman-import-collection.png)

## <a name="get-azure-ad-token"></a>Obter o Token do Microsoft Azure AD 

Antes de começar a manipular os recursos AMS v3, precisa de obter e definir Azure AD Token para a Autenticação Principal do Serviço.

1. Na janela esquerda da aplicação Postman, selecione "Passo 1: Obter ficha AAD Auth".
2. Em seguida, selecione "Obter o Token do Microsoft Azure AD para Autenticação Principal de Serviço".
3. Prima **Enviar**.

    A seguinte operação **POST** é enviada.

    ```
    https://login.microsoftonline.com/:tenantId/oauth2/token
    ```

4. A resposta volta com o token e define a variável de ambiente "AccessToken" como o valor de token.  

    ![Obter token do AAD](./media/develop-with-postman/postman-get-aad-auth-token.png)

## <a name="troubleshooting"></a>Resolução de problemas 

* Se a sua aplicação falhar com "HTTP 504: Gateway Timeout", certifique-se de que a variável de localização não foi explicitamente definida para outro valor que não a localização esperada da conta Serviços de Comunicação. 
* Se obtém um erro de "conta não encontrada", verifique também se a propriedade de localização na mensagem Body JSON está definida para o local onde se encontra a conta dos Serviços de Comunicação Social. 

## <a name="see-also"></a>Ver também

- [Criar filtros com Serviços de Mídia - REST](filters-dynamic-manifest-rest-howto.md)
- [API REST baseada no Azure Resource Manager](https://github.com/Azure-Samples/media-services-v3-arm-templates)

## <a name="next-steps"></a>Passos seguintes

- [Transmitir ficheiros com REST](stream-files-tutorial-with-rest.md).  
- [Tutorial: Encode a remote file based on URL and stream the video - REST](stream-files-tutorial-with-rest.md) (Codificar ficheiros remotos com base no URL e transmitir o vídeo em fluxo - REST)

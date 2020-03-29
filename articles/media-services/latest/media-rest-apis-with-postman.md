---
title: Configure Postman para Azure Media Services v3 REST API chamadas
description: Este artigo mostra-lhe como configurar o Carteiro para que possa ser usado para ligar para a Azure Media Services (AMS) REST APIs.
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
ms.date: 12/05/2019
ms.author: juliako
ms.openlocfilehash: 872dad95fc5b536c51e251612f40439da020a059
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75779642"
---
# <a name="configure-postman-for-media-services-v3-rest-api-calls"></a>Configure Postman para Serviços de Media v3 CHAMADAS REST API

Este artigo mostra-lhe como configurar **o Carteiro** para que possa ser usado para ligar para a Azure Media Services (AMS) REST APIs. O artigo mostra como importar ambiente e ficheiros de recolha para **o Carteiro.** A coleção contém definições agrupadas de pedidos HTTP que chamam Azure Media Services (AMS) REST APIs. O ficheiro de ambiente contém variáveis que são utilizadas pela coleção.

Antes de começar a desenvolver, reveja [o Desenvolvimento com Media Services v3 APIs](media-services-apis-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

- [Criar uma conta de Media Services.](create-account-cli-how-to.md) Lembre-se do nome do grupo de recursos e do nome da conta Media Services. 
- Obtenha informações necessárias para aceder a [APIs](access-api-cli-how-to.md)
- Instale o cliente REST do [Postman](https://www.getpostman.com/) para executar as API REST mostradas em alguns dos tutoriais sobre AMS REST. 

    Estamos a utilizar o **Postman**, mas qualquer ferramenta REST seria adequada. Outras alternativas são: **Visual Studio Code** com o plugin REST ou **Telerik Fiddler**. 

> [!IMPORTANT]
> Rever convenções de [nomeação.](media-services-apis-overview.md#naming-conventions)

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

Antes de começar a manipular os recursos da AMS v3, precisa de obter e definir o Token Azure AD para autenticação principal de serviço.

1. Na janela esquerda da aplicação Postman, selecione "Step 1: Get AAD Auth token".
2. Em seguida, selecione "Obter o Token do Microsoft Azure AD para Autenticação Principal de Serviço".
3. Prima **Enviar**.

    A seguinte operação **POST** é enviada.

    ```
    https://login.microsoftonline.com/:tenantId/oauth2/token
    ```

4. A resposta volta com o token e define a variável de ambiente "AccessToken" como o valor de token.  

    ![Obter token do AAD](./media/develop-with-postman/postman-get-aad-auth-token.png)

## <a name="troubleshooting"></a>Resolução de problemas 

* Se a sua aplicação falhar com "HTTP 504: Gateway Timeout", certifique-se de que a variável de localização não foi explicitamente fixada para um valor diferente da localização esperada da conta media Services. 
* Se tiver um erro de "conta não encontrada", consulte também para se certificar de que a propriedade de localização na mensagem Body JSON está definida para o local onde a conta de Media Services se encontra. 

## <a name="see-also"></a>Consulte também

- [Faça upload de ficheiros numa conta de Serviços de Media - REST](upload-files-rest-how-to.md)
- [Criar filtros com Serviços de Media - REST](filters-dynamic-manifest-rest-howto.md)
- [API REST baseada no Azure Resource Manager](https://github.com/Azure-Samples/media-services-v3-arm-templates)

## <a name="next-steps"></a>Passos seguintes

- [Transmita ficheiros com REST](stream-files-tutorial-with-rest.md).  
- [Tutorial: Codificar um ficheiro remoto com base em URL e transmitir o vídeo - REST](stream-files-tutorial-with-rest.md)

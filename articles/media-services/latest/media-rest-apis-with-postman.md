---
title: Configurar o postmaster para chamadas de API REST de Serviços de Mídia do Azure
description: Saiba como configurar o postmaster para chamadas à API REST dos serviços de mídia.
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
ms.date: 11/07/2019
ms.author: juliako
ms.openlocfilehash: 7b4e2d14e8719808db138a4f2607b19cece401a6
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839590"
---
# <a name="configure-postman-for-media-services-rest-api-calls"></a>Configurar o postmaster para chamadas da API REST dos serviços de mídia

Este artigo mostra como configurar o **postmaster** para que ele possa ser usado para chamar as APIs REST dos serviços de mídia do Azure (AMS). O artigo mostra como importar arquivos de ambiente e de coleção para o **postmaster**. A coleção contém definições agrupadas de solicitações HTTP que chamam as APIs REST dos serviços de mídia do Azure (AMS). O ficheiro de ambiente contém variáveis que são utilizadas pela coleção.

Antes de começar a desenvolver, examine o [desenvolvimento com as APIs dos serviços de mídia v3](media-services-apis-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

- [Crie uma conta dos serviços de mídia](create-account-cli-how-to.md). Lembre-se de lembrar o nome do grupo de recursos e o nome da conta dos serviços de mídia. 
- Obter as informações necessárias para [acessar as APIs](access-api-cli-how-to.md)
- Instale o cliente REST do [Postman](https://www.getpostman.com/) para executar as API REST mostradas em alguns dos tutoriais sobre AMS REST. 

    Estamos a utilizar o **Postman**, mas qualquer ferramenta REST seria adequada. Outras alternativas são: **Visual Studio Code** com o plug-in REST ou **Telerik Fiddler**. 

> [!IMPORTANT]
> Examine as [convenções de nomenclatura](media-services-apis-overview.md#naming-conventions).

## <a name="download-postman-files"></a>Transferir ficheiros do Postman

Clone o repositório do GitHub que contém os ficheiros de ambiente e coleção do Postman.

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-rest-postman.git
 ```

## <a name="configure-postman"></a>Configurar o Postman

### <a name="configure-the-environment"></a>Configurar o ambiente 

1. Abra o aplicativo do **postmaster** .
2. À direita do ecrã, selecione a opção **Gerir ambiente**.

    ![Gerir ambiente](./media/develop-with-postman/postman-import-env.png)
4. Na caixa de diálogo **Gerir ambiente**, clique em **Importar**.
2. Navegue até ao ficheiro `Azure Media Service v3 Environment.postman_environment.json` que foi transferido quando clonou `https://github.com/Azure-Samples/media-services-v3-rest-postman.git`.
6. O ambiente **Ambiente v3 do Serviço de Multimédia do Azure** é adicionado.

    > [!Note]
    > Atualize as variáveis de acesso com valores obtidos na secção **Aceder à API dos Serviços de Multimédia** acima.

7. Clique duas vezes no arquivo selecionado e insira os valores que você obteve seguindo as etapas de API de acesso.
8. Feche a caixa de diálogo.
9. Selecione o **Ambiente v3 do Serviço de Multimédia do Azure** na lista pendente.

    ![Escolher ambiente](./media/develop-with-postman/choose-env.png)
   
### <a name="configure-the-collection"></a>Configurar a coleção

1. Clique em **Importar** para importar o ficheiro de coleção.
1. Navegue até ao ficheiro `Media Services v3.postman_collection.json` que foi transferido quando clonou `https://github.com/Azure-Samples/media-services-v3-rest-postman.git`
3. Escolha o ficheiro **Media Services v3.postman_collection.json**.

    ![Importar um ficheiro](./media/develop-with-postman/postman-import-collection.png)

## <a name="get-azure-ad-token"></a>Obter o Token do Microsoft Azure AD 

Antes de começar a manipular os recursos do AMS v3, você precisa obter e definir o token do Azure AD para a autenticação de entidade de serviço.

1. Na janela à esquerda do aplicativo do postmaster, selecione "etapa 1: obter token de autenticação do AAD".
2. Em seguida, selecione "Obter o Token do Microsoft Azure AD para Autenticação Principal de Serviço".
3. Prima **Enviar**.

    A seguinte operação **POST** é enviada.

    ```
    https://login.microsoftonline.com/:tenantId/oauth2/token
    ```

4. A resposta volta com o token e define a variável de ambiente "AccessToken" como o valor de token.  

    ![Obter token do AAD](./media/develop-with-postman/postman-get-aad-auth-token.png)

## <a name="troubleshooting"></a>Resolução de problemas 

* Se seu aplicativo falhar com "HTTP 504: tempo limite do gateway", verifique se a variável de local não foi definida explicitamente com um valor diferente do local esperado da conta dos serviços de mídia. 
* Se você receber um erro "conta não encontrada", verifique também se a propriedade local na mensagem JSON do corpo está definida como o local em que a conta dos serviços de mídia está. 

## <a name="see-also"></a>Consultar também

- [Carregar arquivos em uma conta dos serviços de mídia-REST](upload-files-rest-how-to.md)
- [Criar filtros com os serviços de mídia-REST](filters-dynamic-manifest-rest-howto.md)
- [API REST baseada no Azure Resource Manager](https://github.com/Azure-Samples/media-services-v3-arm-templates)

## <a name="next-steps"></a>Passos seguintes

- [Transmita arquivos com REST](stream-files-tutorial-with-rest.md).  
- [Tutorial: codificar um arquivo remoto com base na URL e transmitir o vídeo-REST](stream-files-tutorial-with-rest.md)

---
title: 'Autenticação de utilizador final: API do REST com a geração 1 de armazenamento do Azure Data Lake com o Azure Active Directory | Documentos da Microsoft'
description: Saiba como conseguir a autenticação de utilizador final com Gen1 de armazenamento do Azure Data Lake com o Azure Active Directory, com a REST API
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: d5603bb6bbb56d1aebb719902c60de631a4f14f0
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58108193"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-rest-api"></a>Autenticação de utilizador final com Gen1 de armazenamento do Azure Data Lake com a REST API
> [!div class="op_single_selector"]
> * [Utilizar o Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Utilizar o SDK .NET](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Utilizar o Python](data-lake-store-end-user-authenticate-python.md)
> * [Utilizar a API REST](data-lake-store-end-user-authenticate-rest-api.md)
> 
>  

Neste artigo, saiba mais sobre como utilizar a API REST para fazer a autenticação de utilizador final com Gen1 de armazenamento do Azure Data Lake. Para a autenticação serviço a serviço com a geração 1 de armazenamento do Data Lake com a REST API, veja [autenticação do serviço para serviço com a geração 1 de armazenamento do Data Lake com a REST API](data-lake-store-service-to-service-authenticate-rest-api.md).

## <a name="prerequisites"></a>Pré-requisitos

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Criar uma aplicação de "Nativa" do Azure Active Directory**. Tem de ter concluído os passos em [autenticação de utilizador final com geração 1 de armazenamento do Data Lake com o Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

* **[cURL](https://curl.haxx.se/)**. Este artigo utiliza o cURL para demonstrar como fazer chamadas de REST API em relação a uma conta de geração 1 de armazenamento do Data Lake.

## <a name="end-user-authentication"></a>Autenticação de utilizador final
Autenticação de utilizador final é a abordagem recomendada, se pretender que um utilizador inicie sessão sua aplicação utilizar o Azure AD. Seu aplicativo é capaz de aceder aos recursos do Azure com o mesmo nível de acesso que o utilizador com sessão iniciada. O utilizador precisa de fornecer as credenciais periodicamente por ordem para a sua aplicação manter o acesso.

O resultado de ter o início de sessão do utilizador final é que seu aplicativo recebe um token de acesso e um token de atualização. O token de acesso anexado a cada pedido efetuado a geração 1 de armazenamento do Data Lake ou o Data Lake Analytics e é válido durante uma hora por predefinição. O token de atualização pode ser utilizado para obter um novo token de acesso e é válido para duas semanas por predefinição, se utilizadas com regularidade. Pode usar duas abordagens diferentes para início de sessão do utilizador final.

Neste cenário, a aplicação pede ao utilizador para iniciar sessão e todas as operações são efetuadas no contexto do utilizador. Execute os seguintes passos:

1. Através da sua aplicação, redirecione o utilizador para o seguinte URL:

        https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize?client_id=<APPLICATION-ID>&response_type=code&redirect_uri=<REDIRECT-URI>

   > [!NOTE]
   > \<REDIRECT-URI> tem de estar codificado para utilização num URL. Assim, para https://localhost, utilize `https%3A%2F%2Flocalhost`)

    Para o objetivo deste tutorial, pode substituir os valores de marcador de posição no URL acima e colá-lo na barra de endereço do browser. Será redirecionado para a autenticação utilizando o seu início de sessão do Azure. Depois de iniciar sessão com êxito, a resposta é apresentada na barra de endereço do browser. A resposta estará no seguinte formato:

        http://localhost/?code=<AUTHORIZATION-CODE>&session_state=<GUID>

2. Capture o código de autorização na resposta. Para este tutorial, pode copiar o código de autorização da barra de endereços do navegador da web e pass, ele a mensagem de pedido para o ponto final do token, conforme mostrado no seguinte fragmento:

        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token \
        -F redirect_uri=<REDIRECT-URI> \
        -F grant_type=authorization_code \
        -F resource=https://management.core.windows.net/ \
        -F client_id=<APPLICATION-ID> \
        -F code=<AUTHORIZATION-CODE>

   > [!NOTE]
   > Neste caso, \<REDIRECT-URI> não tem de ser codificado.
   > 
   > 

3. A resposta é um objeto JSON que contém um token de acesso (por exemplo, `"access_token": "<ACCESS_TOKEN>"`) e um token de atualização (por exemplo, `"refresh_token": "<REFRESH_TOKEN>"`). A aplicação utiliza o token de acesso ao aceder a geração 1 de armazenamento do Azure Data Lake e o token de atualização para obter outro token de acesso quando um token de acesso expira.

        {"token_type":"Bearer","scope":"user_impersonation","expires_in":"3599","expires_on":"1461865782","not_before":    "1461861882","resource":"https://management.core.windows.net/","access_token":"<REDACTED>","refresh_token":"<REDACTED>","id_token":"<REDACTED>"}

4. Quando o token de acesso expira, pode pedir um novo token de acesso com o token de atualização, conforme mostrado no seguinte fragmento:

        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
             -F grant_type=refresh_token \
             -F resource=https://management.core.windows.net/ \
             -F client_id=<APPLICATION-ID> \
             -F refresh_token=<REFRESH-TOKEN>

Para obter mais informações sobre a autenticação de utilizador interativa, veja [Fluxo de concessão de códigos de autorização](https://msdn.microsoft.com/library/azure/dn645542.aspx).

## <a name="next-steps"></a>Passos Seguintes
Neste artigo, aprendeu a utilizar a autenticação de serviço a serviço para autenticar com a geração 1 de armazenamento do Azure Data Lake com a REST API. Agora, pode ver os seguintes artigos que falam sobre como utilizar a API REST para trabalhar com a geração 1 de armazenamento do Azure Data Lake.

* [Operações de gestão de conta na geração 1 de armazenamento do Data Lake com a REST API](data-lake-store-get-started-rest-api.md)
* [Operações de dados no Data Lake Storage Gen1 com a REST API](data-lake-store-data-operations-rest-api.md)


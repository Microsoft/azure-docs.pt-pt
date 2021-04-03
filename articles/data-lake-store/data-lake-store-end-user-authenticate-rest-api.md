---
title: Autenticação de utilizador final - REST with Data Lake Storage Gen1 - Azure
description: Saiba como obter a autenticação do utilizador final com a Azure Data Lake Storage Gen1 utilizando o Azure Ative Directory usando a API REST
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 462cd06c9da3b1f0a57c293d52c59181372b709b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92103752"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-rest-api"></a>Autenticação de utilizador final com Azure Data Lake Storage Gen1 usando REST API
> [!div class="op_single_selector"]
> * [Utilizar o Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Com o .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Utilizar o Python](data-lake-store-end-user-authenticate-python.md)
> * [Utilizar a API REST](data-lake-store-end-user-authenticate-rest-api.md)
> 
>  

Neste artigo, você aprende sobre como usar a API REST para fazer a autenticação do utilizador final com Azure Data Lake Storage Gen1. Para autenticação de serviço-a-serviço com data lake storage gen1 usando REST API, consulte [a autenticação de serviço-a-serviço com data lake storage gen1 usando REST API](data-lake-store-service-to-service-authenticate-rest-api.md).

## <a name="prerequisites"></a>Pré-requisitos

* **Uma assinatura Azure**. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Criar uma aplicação "Native" do Diretório Ativo Azure**. Deve ter completado os passos na [autenticação do utilizador final com a Data Lake Storage Gen1 utilizando o Azure Ative Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

* **[cURL](https://curl.haxx.se/)**. Este artigo utiliza o cURL para demonstrar como fazer chamadas de API REST contra uma conta Gen1 de armazenamento de data lake.

## <a name="end-user-authentication"></a>Autenticação de utilizador final
A autenticação do utilizador final é a abordagem recomendada se pretender que um utilizador faça login na sua aplicação utilizando a Azure AD. A sua aplicação é capaz de aceder aos recursos do Azure com o mesmo nível de acesso que o utilizador com sessão iniciada. O utilizador necessita de fornecer as suas credenciais periodicamente para que a sua aplicação mantenha o acesso.

O resultado de ter o login do utilizador final é que a sua aplicação recebe um token de acesso e um token de atualização. O token de acesso é anexado a cada pedido feito à Data Lake Storage Gen1 ou data lake analytics, e é válido por uma hora por padrão. O token de atualização pode ser usado para obter um novo token de acesso, e é válido por padrão até duas semanas, se usado regularmente. Pode utilizar duas abordagens diferentes para o login do utilizador final.

Neste cenário, a aplicação pede ao utilizador para iniciar sessão e todas as operações são efetuadas no contexto do utilizador. Efetue os seguintes passos:

1. Através da sua aplicação, redirecione o utilizador para o seguinte URL:

    `https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize?client_id=<APPLICATION-ID>&response_type=code&redirect_uri=<REDIRECT-URI>`

   > [!NOTE]
   > \<REDIRECT-URI> precisa ser codificado para ser usado em um URL. Assim, para https://localhost , usar `https%3A%2F%2Flocalhost` )

    Para o objetivo deste tutorial, pode substituir os valores de marcador de posição no URL acima e colá-lo na barra de endereço do browser. Será redirecionado para a autenticação utilizando o seu início de sessão do Azure. Depois de iniciar sessão com êxito, a resposta é apresentada na barra de endereço do browser. A resposta estará no seguinte formato:

    `http://localhost/?code=<AUTHORIZATION-CODE>&session_state=<GUID>`

2. Capture o código de autorização na resposta. Para este tutorial, pode copiar o código de autorização da barra de endereços do navegador web e passá-lo no pedido DO POST para o ponto final simbólico, como mostra o seguinte corte:

    ```console
    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token \
    -F redirect_uri=<REDIRECT-URI> \
    -F grant_type=authorization_code \
    -F resource=https://management.core.windows.net/ \
    -F client_id=<APPLICATION-ID> \
    -F code=<AUTHORIZATION-CODE>
    ```

   > [!NOTE]
   > Neste caso, a \<REDIRECT-URI> necessidade de não ser codificada.
   > 
   > 

3. A resposta é um objeto JSON que contém um token de acesso (por exemplo, `"access_token": "<ACCESS_TOKEN>"` ) e um token refresh (por exemplo, `"refresh_token": "<REFRESH_TOKEN>"` ). A sua aplicação utiliza o token de acesso ao aceder ao Azure Data Lake Storage Gen1 e o token de atualização para obter outro token de acesso quando um token de acesso expirar.

    ```json
    {"token_type":"Bearer","scope":"user_impersonation","expires_in":"3599","expires_on":"1461865782","not_before":    "1461861882","resource":"https://management.core.windows.net/","access_token":"<REDACTED>","refresh_token":"<REDACTED>","id_token":"<REDACTED>"}
    ```

4. Quando o token de acesso expirar, pode solicitar um novo token de acesso utilizando o token refresh, como mostra o seguinte corte:

    ```console
    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
         -F grant_type=refresh_token \
         -F resource=https://management.core.windows.net/ \
         -F client_id=<APPLICATION-ID> \
         -F refresh_token=<REFRESH-TOKEN>
    ```

Para obter mais informações sobre a autenticação de utilizador interativa, veja [Fluxo de concessão de códigos de autorização](/previous-versions/azure/dn645542(v=azure.100)).

## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu a usar a autenticação de serviço-a-serviço para autenticar com a Azure Data Lake Storage Gen1 usando a REST API. Agora pode olhar para os seguintes artigos que falam sobre como usar a API REST para trabalhar com a Azure Data Lake Storage Gen1.

* [Operações de gestão de conta na Data Lake Storage Gen1 utilizando a API REST](data-lake-store-get-started-rest-api.md)
* [Operações de dados em Data Lake Storage Gen1 usando REST API](data-lake-store-data-operations-rest-api.md)
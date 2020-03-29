---
title: 'Autenticação do utilizador final: REST API com Azure Data Lake Storage Gen1 utilizando o Diretório Ativo Azure [ Microsoft Docs'
description: Saiba como obter a autenticação de utilizador final com o Azure Data Lake Storage Gen1 utilizando o Azure Ative Directory utilizando a REST API
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 0ef65c23ee1bf4f064695779b71c8616427da204
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "60877827"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-rest-api"></a>Autenticação de utilizador final com Azure Data Lake Storage Gen1 utilizando a REST API
> [!div class="op_single_selector"]
> * [Utilizar o Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Com o .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Utilizar o Python](data-lake-store-end-user-authenticate-python.md)
> * [Com a API REST](data-lake-store-end-user-authenticate-rest-api.md)
> 
>  

Neste artigo, aprende-se a usar a API REST para fazer a autenticação de utilizador final com o Azure Data Lake Storage Gen1. Para autenticação de serviço a serviço com Data Lake Storage Gen1 utilizando a REST API, consulte [a autenticação serviço-a-serviço com data Lake Storage Gen1 utilizando a REST API](data-lake-store-service-to-service-authenticate-rest-api.md).

## <a name="prerequisites"></a>Pré-requisitos

* **Uma subscrição Azure.** Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Criar uma aplicação de Diretório Ativo Azure "Nativa".** Deve ter concluído os passos de [autenticação do utilizador final com data Lake Storage Gen1 utilizando o Diretório Ativo Azure](data-lake-store-end-user-authenticate-using-active-directory.md).

* **[cURL](https://curl.haxx.se/)**. Este artigo usa cURL para demonstrar como fazer chamadas REST API contra uma conta Gen1 de Armazenamento de Data Lake.

## <a name="end-user-authentication"></a>Autenticação de utilizador final
A autenticação do utilizador final é a abordagem recomendada se pretender que um utilizador inicie sessão na sua aplicação utilizando o Azure AD. A sua aplicação é capaz de aceder aos recursos do Azure com o mesmo nível de acesso que o utilizador de entrada. O utilizador necessita de fornecer as suas credenciais periodicamente para que a sua aplicação mantenha o acesso.

O resultado de ter o login do utilizador final é que a sua aplicação recebe um token de acesso e um token de atualização. O token de acesso é anexado a cada pedido feito ao Data Lake Storage Gen1 ou Data Lake Analytics, e é válido por uma hora por padrão. O token de atualização pode ser usado para obter um novo token de acesso, e é válido por até duas semanas por padrão, se usado regularmente. Pode utilizar duas abordagens diferentes para iniciar sessão no utilizador final.

Neste cenário, a aplicação pede ao utilizador para iniciar sessão e todas as operações são efetuadas no contexto do utilizador. Execute os seguintes passos:

1. Através da sua aplicação, redirecione o utilizador para o seguinte URL:

        https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize?client_id=<APPLICATION-ID>&response_type=code&redirect_uri=<REDIRECT-URI>

   > [!NOTE]
   > \<REDIRECT-URI> tem de estar codificado para utilização num URL. Então, https://localhostpara, `https%3A%2F%2Flocalhost`usar)

    Para o objetivo deste tutorial, pode substituir os valores de marcador de posição no URL acima e colá-lo na barra de endereço do browser. Será redirecionado para a autenticação utilizando o seu início de sessão do Azure. Depois de iniciar sessão com êxito, a resposta é apresentada na barra de endereço do browser. A resposta estará no seguinte formato:

        http://localhost/?code=<AUTHORIZATION-CODE>&session_state=<GUID>

2. Capture o código de autorização na resposta. Para este tutorial, pode copiar o código de autorização a partir da barra de endereços do navegador web e passá-lo no pedido DOPOST para o ponto final simbólico, como mostra o seguinte corte:

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

3. A resposta é um objeto JSON que contém `"access_token": "<ACCESS_TOKEN>"`um símbolo de acesso (por `"refresh_token": "<REFRESH_TOKEN>"`exemplo, ) e um token de atualização (por exemplo, ). A sua aplicação utiliza o sinal de acesso ao aceder ao Azure Data Lake Storage Gen1 e ao token de atualização para obter outro sinal de acesso quando um token de acesso expirar.

        {"token_type":"Bearer","scope":"user_impersonation","expires_in":"3599","expires_on":"1461865782","not_before":    "1461861882","resource":"https://management.core.windows.net/","access_token":"<REDACTED>","refresh_token":"<REDACTED>","id_token":"<REDACTED>"}

4. Quando o token de acesso expirar, pode solicitar um novo sinal de acesso utilizando o token de atualização, como mostra o seguinte corte:

        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
             -F grant_type=refresh_token \
             -F resource=https://management.core.windows.net/ \
             -F client_id=<APPLICATION-ID> \
             -F refresh_token=<REFRESH-TOKEN>

Para obter mais informações sobre a autenticação de utilizador interativa, veja [Fluxo de concessão de códigos de autorização](https://msdn.microsoft.com/library/azure/dn645542.aspx).

## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu a usar a autenticação serviço-a-serviço para autenticar com o Azure Data Lake Storage Gen1 usando a REST API. Pode agora ver os seguintes artigos que falam sobre como usar a API REST para trabalhar com o Azure Data Lake Storage Gen1.

* [Operações de gestão de conta em Data Lake Storage Gen1 utilizando rest API](data-lake-store-get-started-rest-api.md)
* [Operações de dados sobre data lake storage Gen1 usando REST API](data-lake-store-data-operations-rest-api.md)


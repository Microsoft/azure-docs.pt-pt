---
title: Autenticação serviço a serviço-Data Lake Storage Gen1-API REST
description: Saiba como obter a autenticação serviço a serviço com Azure Data Lake Storage Gen1 e Azure Active Directory usando a API REST.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 59d0bf20b16beda47d76e6a9940ac9fa4436da3f
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904520"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-rest-api"></a>Autenticação serviço a serviço com Azure Data Lake Storage Gen1 usando a API REST
> [!div class="op_single_selector"]
> * [Utilizar o Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Com o .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Utilizar o Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Com a API REST](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
> 

Neste artigo, você aprenderá a usar a API REST para fazer a autenticação serviço a serviço com o Azure Data Lake Storage Gen1. Para autenticação do usuário final com Data Lake Storage Gen1 usando a API REST, consulte [autenticação do usuário final com data Lake Storage Gen1 usando a API REST](data-lake-store-end-user-authenticate-rest-api.md).

## <a name="prerequisites"></a>Pré-requisitos

* **Uma subscrição do Azure**. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Crie um aplicativo Azure Active Directory "Web"** . Você deve ter concluído as etapas em [Autenticação serviço a serviço com data Lake Storage Gen1 usando Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

## <a name="service-to-service-authentication"></a>Autenticação serviço a serviço

Nesse cenário, o aplicativo fornece suas próprias credenciais para executar as operações. Para isso, você deve emitir uma solicitação POST como a mostrada no trecho a seguir:

    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

A saída da solicitação inclui um token de autorização (indicado por `access-token` na saída abaixo) que você passa subsequentemente com suas chamadas à API REST. Salvar o token de autenticação em um arquivo de texto; Você precisará dela ao fazer chamadas REST para Data Lake Storage Gen1.

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

Este artigo utiliza a abordagem **não interativa**. Para obter mais informações sobre a abordagem não interativa (chamadas serviço a serviço), veja [Chamadas serviço a serviço utilizando credenciais](https://msdn.microsoft.com/library/azure/dn645543.aspx).

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu a usar a autenticação serviço a serviço para autenticar com Data Lake Storage Gen1 usando a API REST. Agora você pode examinar os seguintes artigos que falam sobre como usar a API REST para trabalhar com Data Lake Storage Gen1.

* [Operações de gerenciamento de conta no Data Lake Storage Gen1 usando a API REST](data-lake-store-get-started-rest-api.md)
* [Operações de dados em Data Lake Storage Gen1 usando a API REST](data-lake-store-data-operations-rest-api.md)

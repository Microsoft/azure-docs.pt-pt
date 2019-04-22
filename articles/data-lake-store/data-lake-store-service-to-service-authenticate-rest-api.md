---
title: 'Autenticação serviço a serviço: API do REST com a geração 1 de armazenamento do Azure Data Lake com o Azure Active Directory | Documentos da Microsoft'
description: Saiba como conseguir a autenticação do serviço para serviço com Gen1 de armazenamento do Azure Data Lake com o Azure Active Directory, com a REST API
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
ms.openlocfilehash: c48f7d7608b2b70f4ae41e2af5792cff72bb0dd2
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58885728"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-rest-api"></a>Autenticação do serviço para serviço com Gen1 de armazenamento do Azure Data Lake com a REST API
> [!div class="op_single_selector"]
> * [Utilizar o Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Utilizar o SDK .NET](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Utilizar o Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Utilizar a API REST](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
> 

Neste artigo, saiba mais sobre como utilizar a API REST para fazer a autenticação serviço a serviço com a geração 1 de armazenamento do Azure Data Lake. Para autenticação de utilizador final com geração 1 de armazenamento do Data Lake com a REST API, consulte [autenticação de utilizador final com geração 1 de armazenamento do Data Lake com a REST API](data-lake-store-end-user-authenticate-rest-api.md).

## <a name="prerequisites"></a>Pré-requisitos
* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Criar uma aplicação de "Web" do Azure Active Directory**. Tem de ter concluído os passos em [autenticação do serviço para serviço com a geração 1 de armazenamento do Data Lake com o Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

## <a name="service-to-service-authentication"></a>Autenticação serviço a serviço
Neste cenário, o aplicativo fornece suas próprias credenciais para efetuar as operações. Para tal, tem de emitir um pedido POST, como o mostrado no seguinte fragmento: 

    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

O resultado do pedido inclui um token de autorização (denotado por `access-token` no resultado abaixo) que transmitir subsequentemente com suas chamadas de REST API. Guarde o token de autenticação num arquivo de texto; irá precisar dele quando efetuar chamadas REST para a geração 1 de armazenamento do Data Lake.

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

Este artigo utiliza a abordagem **não interativa**. Para obter mais informações sobre a abordagem não interativa (chamadas serviço a serviço), veja [Chamadas serviço a serviço utilizando credenciais](https://msdn.microsoft.com/library/azure/dn645543.aspx). 

## <a name="next-steps"></a>Passos Seguintes
Neste artigo, aprendeu a utilizar a autenticação de serviço a serviço para autenticar com a geração 1 de armazenamento do Data Lake com a REST API. Agora, pode ver os seguintes artigos que falam sobre como utilizar a API REST para trabalhar com a geração 1 de armazenamento do Data Lake.

* [Operações de gestão de conta na geração 1 de armazenamento do Data Lake com a REST API](data-lake-store-get-started-rest-api.md)
* [Operações de dados no Data Lake Storage Gen1 com a REST API](data-lake-store-data-operations-rest-api.md)


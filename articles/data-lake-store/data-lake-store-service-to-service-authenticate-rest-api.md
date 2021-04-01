---
title: REST - Autenticação de serviço-a-serviço - Data Lake Storage Gen1 - Azure
description: Saiba como obter a autenticação de serviço-a-serviço com a Azure Data Lake Storage Gen1 e o Azure Ative Directory utilizando a API REST.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 4947bab58a6d23bcc3e3212a9e3e7dc0c4fa8578
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92106710"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-rest-api"></a>Autenticação de serviço-a-serviço com Azure Data Lake Storage Gen1 usando REST API
> [!div class="op_single_selector"]
> * [Utilizar o Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Com o .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Utilizar o Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Utilizar a API REST](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
> 

Neste artigo, você aprende a usar a API REST para fazer autenticação de serviço-a-serviço com Azure Data Lake Storage Gen1. Para autenticação do utilizador final com data lake storage gen1 usando REST API, consulte [a autenticação do utilizador final com a Data Lake Storage Gen1 utilizando REST API](data-lake-store-end-user-authenticate-rest-api.md).

## <a name="prerequisites"></a>Pré-requisitos

* **Uma assinatura Azure**. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Criar uma aplicação "Web" do Diretório Ativo Azure**. Deve ter completado os passos na [autenticação de serviço-a-serviço com a Data Lake Storage Gen1 utilizando o Azure Ative Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

## <a name="service-to-service-authentication"></a>Autenticação serviço a serviço

Neste cenário, a aplicação fornece as suas próprias credenciais para a realização das operações. Para isso, deve emitir um pedido DE POST como o mostrado no seguinte corte:

```console
curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
  -F grant_type=client_credentials \
  -F resource=https://management.core.windows.net/ \
  -F client_id=<CLIENT-ID> \
  -F client_secret=<AUTH-KEY>
```

A saída do pedido inclui um token de autorização (denotado pela `access-token` saída abaixo) que posteriormente passa com as suas chamadas REST API. Guarde o símbolo de autenticação num ficheiro de texto; você vai precisar dele ao fazer chamadas REST para Data Lake Storage Gen1.

```output
{"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}
```

Este artigo utiliza a abordagem **não interativa**. Para obter mais informações sobre a abordagem não interativa (chamadas serviço a serviço), veja [Chamadas serviço a serviço utilizando credenciais](/previous-versions/azure/dn645543(v=azure.100)).

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a usar a autenticação de serviço-a-serviço para autenticar com a Data Lake Storage Gen1 utilizando a API REST. Agora pode olhar para os seguintes artigos que falam sobre como usar a API REST para trabalhar com a Data Lake Storage Gen1.

* [Operações de gestão de conta na Data Lake Storage Gen1 utilizando a API REST](data-lake-store-get-started-rest-api.md)
* [Operações de dados em Data Lake Storage Gen1 usando REST API](data-lake-store-data-operations-rest-api.md)
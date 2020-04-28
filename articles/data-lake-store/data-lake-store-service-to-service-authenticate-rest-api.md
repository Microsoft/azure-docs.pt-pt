---
title: Autenticação serviço-a-serviço - Data Lake Storage Gen1 - REST API
description: Saiba como obter a autenticação de serviço-a-serviço com o Azure Data Lake Storage Gen1 e o Azure Ative Directory utilizando a API REST.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 59d0bf20b16beda47d76e6a9940ac9fa4436da3f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "73904520"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-rest-api"></a>Autenticação serviço-a-serviço com Azure Data Lake Storage Gen1 usando REST API
> [!div class="op_single_selector"]
> * [Utilizar o Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Com o .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Utilizar o Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Com a API REST](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
> 

Neste artigo, aprende a usar a API REST para fazer a autenticação de serviço a serviço com o Azure Data Lake Storage Gen1. Para autenticação de utilizador final com Data Lake Storage Gen1 utilizando a REST API, consulte a [autenticação do utilizador final com data Lake Storage Gen1 utilizando a REST API](data-lake-store-end-user-authenticate-rest-api.md).

## <a name="prerequisites"></a>Pré-requisitos

* **Uma subscrição Azure.** Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Criar uma aplicação de Diretório Ativo Azure .** Deve ter concluído os passos de [autenticação serviço-a-serviço com data Lake Storage Gen1 utilizando o Diretório Ativo Azure](data-lake-store-service-to-service-authenticate-using-active-directory.md).

## <a name="service-to-service-authentication"></a>Autenticação serviço a serviço

Neste cenário, a aplicação fornece as suas próprias credenciais para a realização das operações. Para isso, deve emitir um pedido de CORREIO como o mostrado no seguinte corte:

    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

A saída do pedido inclui um símbolo de `access-token` autorização (denotado na saída abaixo) que posteriormente passa com as suas chamadas REST API. Guarde o símbolo de autenticação num ficheiro de texto; você precisará dele ao fazer chamadas REST para Data Lake Storage Gen1.

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

Este artigo utiliza a abordagem **não interativa**. Para obter mais informações sobre a abordagem não interativa (chamadas serviço a serviço), veja [Chamadas serviço a serviço utilizando credenciais](https://msdn.microsoft.com/library/azure/dn645543.aspx).

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a usar a autenticação serviço-a-serviço para autenticar com data Lake Storage Gen1 usando a REST API. Pode agora ver os seguintes artigos que falam sobre como usar a API REST para trabalhar com data Lake Storage Gen1.

* [Operações de gestão de conta em Data Lake Storage Gen1 utilizando rest API](data-lake-store-get-started-rest-api.md)
* [Operações de dados sobre data lake storage Gen1 usando REST API](data-lake-store-data-operations-rest-api.md)

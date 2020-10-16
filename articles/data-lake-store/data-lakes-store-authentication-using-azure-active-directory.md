---
title: Autenticação - Data Lake Storage Gen1 com Azure AD
description: Saiba como autenticar com a Azure Data Lake Storage Gen1 utilizando o Azure Ative Directory.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 6fc09f9145b7a1652b621ed38a8bf9af7c4c82a8
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92106574"
---
# <a name="authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Autenticação com Azure Data Lake Storage Gen1 usando diretório ativo Azure

Azure Data Lake Storage Gen1 utiliza o Azure Ative Directory para autenticação. Antes de autorizar uma aplicação que funcione com a Data Lake Storage Gen1, deve decidir como autenticar a sua aplicação com o Azure Ative Directory (Azure AD).

## <a name="authentication-options"></a>Opções de autenticação

* **Autenticação do utilizador final** - As credenciais Azure de um utilizador final são usadas para autenticar com a Data Lake Storage Gen1. A aplicação que cria para trabalhar com pedidos de Data Lake Storage Gen1 para estas credenciais de utilizador. Como resultado, este mecanismo de autenticação é *interativo* e a aplicação é operada no contexto do utilizador. Para obter mais informações e instruções, consulte [a autenticação do utilizador final para data lake storage gen1](data-lake-store-end-user-authenticate-using-active-directory.md).

* **Autenticação de serviço-a-serviço** - Utilize esta opção se pretender que uma aplicação se autente com a Data Lake Storage Gen1. Nesses casos, cria uma aplicação Azure Ative Directory (AD) e utiliza a chave da aplicação AZure AD para autenticar com a Data Lake Storage Gen1. Como resultado, este mecanismo de autenticação não é *interativo.* Para obter mais informações e instruções, consulte [a autenticação de serviço-a-serviço para data lake storage gen1](data-lake-store-service-to-service-authenticate-using-active-directory.md).

A tabela a seguir ilustra como os mecanismos de autenticação de utilizador final e serviço-a-serviço são suportados para a Data Lake Storage Gen1. É assim que se lê a mesa.

* O símbolo ✔* denota que a opção de autenticação é suportada e liga-se a um artigo que demonstra como utilizar a opção de autenticação. 
* O símbolo ✔ indica que a opção de autenticação é suportada. 
* As células vazias denotam que a opção de autenticação não é suportada.


|Utilize esta opção de autenticação com...                   |.NET         |Java     |PowerShell |CLI do Azure | Python   |REST     |
|:---------------------------------------------|:------------|:--------|:----------|:-------------|:---------|:--------|
|Utilizador final (sem MFA**)                        |   ✔ |    ✔    |    ✔      |       ✔      |    **[✔*](data-lake-store-end-user-authenticate-python.md#end-user-authentication-without-multi-factor-authentication)**(precotado)     |    **[✔*](data-lake-store-end-user-authenticate-rest-api.md)**    |
|Utilizador final (com MFA)                           |    **[✔*](data-lake-store-end-user-authenticate-net-sdk.md)**        |    **[✔*](data-lake-store-end-user-authenticate-java-sdk.md)**     |    ✔      |       **[✔*](data-lake-store-get-started-cli-2.0.md)**      |    **[✔*](data-lake-store-end-user-authenticate-python.md#end-user-authentication-with-multi-factor-authentication)**     |    ✔    |
|Serviço-a-serviço (utilizando a chave do cliente)         |    **[✔*](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-client-secret)** |    **[✔*](data-lake-store-service-to-service-authenticate-java.md)**    |    ✔      |       ✔      |    **[✔*](data-lake-store-service-to-service-authenticate-python.md#service-to-service-authentication-with-client-secret-for-account-management)**     |    **[✔*](data-lake-store-service-to-service-authenticate-rest-api.md)**    |
|Serviço-a-serviço (com certificado de cliente) |    **[✔*](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-certificate)**        |    ✔    |    ✔      |       ✔      |    ✔     |    ✔    |

<i>* Clique no símbolo <b>✔. \* </b> É uma ligação.</i><br>
<i>** MFA significa autenticação multi-factor</i>

Consulte  [Os Cenários de Autenticação do Diretório Ativo Azure](../active-directory/develop/authentication-vs-authorization.md) para obter mais informações sobre como utilizar o Azure Ative Directory para autenticação.

## <a name="next-steps"></a>Passos seguintes

* [Autenticação de utilizador final](data-lake-store-end-user-authenticate-using-active-directory.md)
* [Autenticação serviço a serviço](data-lake-store-service-to-service-authenticate-using-active-directory.md)
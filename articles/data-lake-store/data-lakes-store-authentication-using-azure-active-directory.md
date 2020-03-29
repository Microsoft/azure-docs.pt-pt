---
title: Autenticação em Azure Data Lake Storage Gen1 utilizando o Diretório Ativo Azure [ Microsoft Docs
description: Saiba como autenticar com azure Data Lake Storage Gen1 usando o Diretório Ativo Azure
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
ms.openlocfilehash: f83cf183bee930dd07c707b0eb49125cecd70b84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "60193599"
---
# <a name="authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Autenticação com Azure Data Lake Storage Gen1 usando o Diretório Ativo Azure

O Azure Data Lake Storage Gen1 utiliza o Diretório Ativo Azure para autenticação. Antes de ser autor de uma aplicação que trabalhe com data lake storage Gen1, você deve decidir como autenticar a sua aplicação com o Azure Ative Directory (Azure AD).

## <a name="authentication-options"></a>Opções de autenticação

* **Autenticação do utilizador final** - As credenciais Azure de um utilizador final são usadas para autenticar com data Lake Storage Gen1. A aplicação que cria para trabalhar com os pedidos de Data Lake Storage Gen1 para estas credenciais de utilizador. Como resultado, este mecanismo de autenticação é *interativo* e a aplicação funciona no contexto do utilizador. Para mais informações e instruções, consulte a [autenticação do utilizador final para data Lake Storage Gen1](data-lake-store-end-user-authenticate-using-active-directory.md).

* **Autenticação serviço-a-serviço** - Utilize esta opção se quiser uma aplicação para se autenticar com data Lake Storage Gen1. Nesses casos, cria-se uma aplicação azure Ative Directory (AD) e utiliza a chave da aplicação Azure AD para autenticar com data Lake Storage Gen1. Como resultado, este mecanismo de autenticação não é *interativo.* Para mais informações e instruções, consulte a [autenticação serviço-a-serviço para data Lake Storage Gen1](data-lake-store-service-to-service-authenticate-using-active-directory.md).

A tabela que se segue ilustra como os mecanismos de autenticação de utilizador final e serviço ao serviço são suportados para data lake storage Gen1. É assim que se lê a mesa.

* O símbolo ✔* denota que a opção de autenticação é suportada e liga-se a um artigo que demonstra como usar a opção de autenticação. 
* O símbolo ✔ denota que a opção de autenticação é suportada. 
* As células vazias denotam que a opção de autenticação não é suportada.


|Utilize esta opção de autenticação com...                   |.NET         |Java     |PowerShell |CLI do Azure | Python   |REST     |
|:---------------------------------------------|:------------|:--------|:----------|:-------------|:---------|:--------|
|Utilizador final (sem MFA**)                        |   ✔ |    ✔    |    ✔      |       ✔      |    **[✔*](data-lake-store-end-user-authenticate-python.md#end-user-authentication-without-multi-factor-authentication)**(depreciado)     |    **[✔*](data-lake-store-end-user-authenticate-rest-api.md)**    |
|Utilizador final (com MFA)                           |    **[✔*](data-lake-store-end-user-authenticate-net-sdk.md)**        |    **[✔*](data-lake-store-end-user-authenticate-java-sdk.md)**     |    ✔      |       **[✔*](data-lake-store-get-started-cli-2.0.md)**      |    **[✔*](data-lake-store-end-user-authenticate-python.md#end-user-authentication-with-multi-factor-authentication)**     |    ✔    |
|Serviço ao serviço (utilizando a chave do cliente)         |    **[✔*](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-client-secret)** |    **[✔*](data-lake-store-service-to-service-authenticate-java.md)**    |    ✔      |       ✔      |    **[✔*](data-lake-store-service-to-service-authenticate-python.md#service-to-service-authentication-with-client-secret-for-account-management)**     |    **[✔*](data-lake-store-service-to-service-authenticate-rest-api.md)**    |
|Serviço ao serviço (utilizando certificado de cliente) |    **[✔*](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-certificate)**        |    ✔    |    ✔      |       ✔      |    ✔     |    ✔    |

<i>* Clique no símbolo <b>✔.\* </b> É uma ligação.</i><br>
<i>** MFA significa autenticação multifactor</i>

Consulte cenários de [autenticação para o Diretório Ativo Azure](../active-directory/develop/authentication-scenarios.md) para obter mais informações sobre como utilizar o Diretório Ativo Azure para autenticação.

## <a name="next-steps"></a>Passos seguintes

* [Autenticação de utilizador final](data-lake-store-end-user-authenticate-using-active-directory.md)
* [Autenticação serviço a serviço](data-lake-store-service-to-service-authenticate-using-active-directory.md)



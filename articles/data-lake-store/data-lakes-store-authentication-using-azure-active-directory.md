---
title: Autenticação na geração 1 de armazenamento do Azure Data Lake com o Azure Active Directory | Documentos da Microsoft
description: Saiba como autenticar com a geração 1 de armazenamento do Azure Data Lake com o Azure Active Directory
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
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60193599"
---
# <a name="authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Autenticação com a geração 1 de armazenamento do Azure Data Lake com o Azure Active Directory

Geração de armazenamento 1 do Azure Data Lake utiliza o Azure Active Directory para autenticação. Antes de criar um aplicativo que funciona com o Data Lake Storage Gen1, deve decidir como autenticar a aplicação no Azure Active Directory (Azure AD).

## <a name="authentication-options"></a>Opções de autenticação

* **Autenticação de utilizador final** -credenciais do Azure de um utilizador final são utilizadas para autenticar com a geração 1 de armazenamento do Data Lake. Solicita a estas credenciais de utilizador da aplicação criada para trabalhar com a geração 1 de armazenamento do Data Lake. Como resultado, esse mecanismo de autenticação é *interativo* e a aplicação é executada no contexto do utilizador com sessão iniciada. Para obter mais informações e instruções, consulte [autenticação de utilizador final para geração 1 de armazenamento do Data Lake](data-lake-store-end-user-authenticate-using-active-directory.md).

* **Autenticação serviço a serviço** -Utilize esta opção se pretender que um aplicativo para se autenticar no Data Lake Storage Gen1. Nesses casos, criar uma aplicação do Azure Active Directory (AD) e utilizar a chave da aplicação do Azure AD para autenticar com a geração 1 de armazenamento do Data Lake. Como resultado, esse mecanismo de autenticação é *não interativa*. Para obter mais informações e instruções, consulte [autenticação de serviço a serviço para geração 1 de armazenamento do Data Lake](data-lake-store-service-to-service-authenticate-using-active-directory.md).

A tabela a seguir ilustra como o utilizador final e mecanismos de autenticação de serviço para serviço são suportados para geração 1 de armazenamento do Data Lake. Aqui está como ler a tabela.

* O símbolo de ✔ * indica que a opção de autenticação é suportada e liga a um artigo que demonstra como utilizar a opção de autenticação. 
* O símbolo ✔ indica que a opção de autenticação é suportada. 
* As células vazias indicam que a opção de autenticação não é suportada.


|Utilize esta opção de autenticação com...                   |.NET         |Java     |PowerShell |CLI do Azure | Python   |REST     |
|:---------------------------------------------|:------------|:--------|:----------|:-------------|:---------|:--------|
|Utilizador final (sem MFA * *)                        |   ✔ |    ✔    |    ✔      |       ✔      |    **[✔ *](data-lake-store-end-user-authenticate-python.md#end-user-authentication-without-multi-factor-authentication)** (preterido)     |    **[✔*](data-lake-store-end-user-authenticate-rest-api.md)**    |
|Utilizador final (com a MFA)                           |    **[✔*](data-lake-store-end-user-authenticate-net-sdk.md)**        |    **[✔*](data-lake-store-end-user-authenticate-java-sdk.md)**     |    ✔      |       **[✔*](data-lake-store-get-started-cli-2.0.md)**      |    **[✔*](data-lake-store-end-user-authenticate-python.md#end-user-authentication-with-multi-factor-authentication)**     |    ✔    |
|Para serviços (utilizando a chave de cliente)         |    **[✔*](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-client-secret)** |    **[✔*](data-lake-store-service-to-service-authenticate-java.md)**    |    ✔      |       ✔      |    **[✔*](data-lake-store-service-to-service-authenticate-python.md#service-to-service-authentication-with-client-secret-for-account-management)**     |    **[✔*](data-lake-store-service-to-service-authenticate-rest-api.md)**    |
|Para serviços (usando o certificado de cliente) |    **[✔*](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-certificate)**        |    ✔    |    ✔      |       ✔      |    ✔     |    ✔    |

<i>* Clique a <b>✔\* </b> símbolo. É uma ligação.</i><br>
<i>* * MFA significa para a autenticação multifator</i>

Ver [cenários de autenticação do Azure Active Directory](../active-directory/develop/authentication-scenarios.md) para obter mais informações sobre como utilizar o Azure Active Directory para autenticação.

## <a name="next-steps"></a>Passos Seguintes

* [Autenticação de utilizador final](data-lake-store-end-user-authenticate-using-active-directory.md)
* [Autenticação serviço a serviço](data-lake-store-service-to-service-authenticate-using-active-directory.md)



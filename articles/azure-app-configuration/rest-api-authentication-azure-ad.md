---
title: Azure Ative Directory REST API - autenticação
description: Utilize o Azure Ative Directory para autenticar para a Configuração de Aplicações Azure utilizando a API REST
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: cbf05245768a663e324e9bb6e1ad422eeee3ab1a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96930522"
---
# <a name="azure-active-directory-authentication"></a>Autenticação do Azure Active Directory

Pode autenticar pedidos HTTP utilizando o `Bearer` esquema de autenticação com um símbolo adquirido à Azure Ative Directory (Azure AD). Deve transmitir estes pedidos através da Segurança da Camada de Transporte (TLS).

## <a name="prerequisites"></a>Pré-requisitos

Você deve atribuir o principal que é usado para solicitar um token AD Azure para uma das funções de Configuração de [Aplicação de Aplicação Azure](./rest-api-authorization-azure-ad.md)aplicável .

Forneça a cada pedido todos os cabeçalhos HTTP necessários para a autenticação. Aqui está o requisito mínimo:

|  Cabeçalho do pedido | Descrição  |
| --------------- | ------------ |
| `Authorization` | Informação de autenticação requerida pelo `Bearer` esquema. |

**Exemplo:**

```http
Host: {myconfig}.azconfig.io
Authorization: Bearer {{AadToken}}
```

## <a name="azure-ad-token-acquisition"></a>Aquisição de fichas Azure AD

Antes de adquirir um token AD Azure, deve identificar qual o utilizador que pretende autenticar como, para que público está a pedir o símbolo, e o que o ponto final da AZure AD (autoridade) usar.

### <a name="audience"></a>Audiência

Solicite o sinal de Ad Azure com um público adequado. Para configuração de aplicativos Azure, utilize um dos seguintes públicos. O público também pode ser referido como o *recurso* que o símbolo está sendo solicitado.

- {configuraçãoStoreName}.azconfig.io
- *.azconfig.io

> [!IMPORTANT]
> Quando o público solicitado `{configurationStoreName}.azconfig.io` é, deve corresponder exatamente ao cabeçalho de `Host` pedido (sensível a caso) utilizado para enviar o pedido.

### <a name="azure-ad-authority"></a>Autoridade AD de Azure

A autoridade AZure AD é o ponto final que usa para adquirir um token AD Azure. Está na forma `https://login.microsoftonline.com/{tenantId}` de. O `{tenantId}` segmento refere-se ao ID do inquilino Azure AD a que pertence o utilizador ou aplicação que está a tentar autenticar.

### <a name="authentication-libraries"></a>Bibliotecas de autenticação

O Azure fornece um conjunto de bibliotecas, chamadas Bibliotecas de Autenticação de Diretório Ativo Azure, para simplificar o processo de aquisição de um token AD Azure. Azure constrói estas bibliotecas para várias línguas. Para obter mais informações, consulte a [documentação](../active-directory/azuread-dev/active-directory-authentication-libraries.md).

## <a name="errors"></a>Erros

Pode encontrar os seguintes erros.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer
```

**Razão:** Não forneceu o pedido de autorização com o `Bearer` esquema.

**Solução:** Forneça um `Authorization` cabeçalho de pedido HTTP válido.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer error="invalid_token", error_description="Authorization token failed validation"
```

**Razão:** O sinal de Azure não é válido.

**Solução:** Adquira um sinal AD AZure da autoridade AZure AD, e certifique-se de que usou o público adequado.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer error="invalid_token", error_description="The access token is from the wrong issuer. It must match the AD tenant associated with the subscription to which the configuration store belongs. If you just transferred your subscription and see this error message, please try back later."
```

**Razão:** O sinal de Azure não é válido.

**Solução:** Adquira um token AD Azure da autoridade Azure AD. Certifique-se de que o inquilino AZURE AD é o associado à subscrição a que pertence a loja de configuração. Este erro pode aparecer se o principal pertencer a mais de um inquilino da AD Azure.
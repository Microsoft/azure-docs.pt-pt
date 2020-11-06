---
title: Azure Ative Directory REST API - Autenticação
description: Utilize o Azure Ative Directory para autenticar para a Configuração de Aplicações Azure utilizando a API REST
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: fb3d00fb79c55e29d578f5e068e4ae025414a935
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424196"
---
# <a name="azure-active-directory-authentication"></a>Autenticação do Azure Active Directory

Os pedidos HTTP podem ser autenticados utilizando o sistema de autenticação **do Portador** com um símbolo adquirido à Azure Ative Directory (Azure AD). Estes pedidos devem ser transmitidos através do TLS.

## <a name="prerequisites"></a>Pré-requisitos

O principal que será usado para solicitar um token AD Azure deve ser atribuído a uma das funções de Configuração de [Aplicação aplicáveis](./rest-api-authorization-azure-ad.md)

Forneça a cada pedido todos os cabeçalhos HTTP necessários para autenticação. O mínimo exigido é:

|  Cabeçalho do Pedido | Description  |
| --------------- | ------------ |
| **Autorização** | Informações de autenticação requeridas pelo esquema **Do Portador.** O formato e os detalhes são explicados abaixo. |

**Exemplo:**

```http
Host: {myconfig}.azconfig.io
Authorization: Bearer {{AadToken}}
```

## <a name="azure-active-directory-token-acquisition"></a>Aquisição de fichas do Azure Ative Directory

Antes de adquirir um token AD Azure deve-se identificar qual o utilizador que querem autenticar como, para que público estão a pedir o símbolo, e para que ponto final (autoridade) Azure AD devem utilizar.

### <a name="audience"></a>Audiência

O sinal AD Azure deve ser solicitado com um público adequado. Para a Configuração da Aplicação Azure, um dos seguintes públicos deve ser especificado ao solicitar um token. O público também pode ser referido como o "recurso" que o símbolo está a ser solicitado.

- {configuraçãoStoreName}.azconfig.io
- *.azconfig.io

> [!IMPORTANT]
> Quando o público solicitado é {configuraçãoStoreName}.azconfig.io, deve corresponder exatamente ao cabeçalho de pedido "Host" (sensível a caso) utilizado para enviar o pedido.

### <a name="azure-ad-authority"></a>Autoridade AD de Azure

A autoridade AZure AD é o ponto final que é usado para adquirir um token AD Azure. Está na forma `https://login.microsoftonline.com/{tenantId}` de. O `{tenantId}` segmento refere-se ao ID do inquilino do Azure Ative Directory ao qual pertence o utilizador/aplicação que está a tentar autenticar.

### <a name="authentication-libraries"></a>Bibliotecas de autenticação

O Azure fornece um conjunto de bibliotecas chamadas Azure Ative Directory Authentication Libraries (ADAL) para simplificar o processo de aquisição de um token AD Azure. Estas bibliotecas são construídas para várias línguas. A documentação pode ser [consultada aqui.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)

## <a name="errors"></a>**Erros**

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer
```

**Razão:** Não é fornecido o cabeçalho do pedido de autorização com o regime do Portador.
**Solução:** Fornecer ```Authorization``` cabeçalho de pedido HTTP válido

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer error="invalid_token", error_description="Authorization token failed validation"
```

**Razão:** O sinal AD Azure não é válido.
**Solução:** Adquira um token AD Azure da Autoridade AD Azure e certifique-se de que o público adequado é usado.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer error="invalid_token", error_description="The access token is from the wrong issuer. It must match the AD tenant associated with the subscription to which the configuration store belongs. If you just transferred your subscription and see this error message, please try back later."
```

**Razão:** O sinal AD Azure não é válido.
**Solução:** Adquirir um token AD Azure da Autoridade AD Azure e garantir que o Azure AD Tenant é o associado à subscrição, a que pertence a loja de configuração. Este erro pode aparecer se o principal pertencer a mais de um inquilino da AD Azure.

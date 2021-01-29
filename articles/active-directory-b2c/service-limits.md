---
title: Limites e restrições de serviço Azure AD B2C
titleSuffix: Azure AD B2C
description: Referência para limites de serviço e restrições para o serviço Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/29/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 866af8b992374492286f47357f108a01f35e560b
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99051046"
---
# <a name="azure-active-directory-b2c-service-limits-and-restrictions"></a>Limites e restrições de serviço Azure Ative Directory B2C

Este artigo contém as restrições de utilização e outros limites de serviço para o serviço Azure Ative Directory B2C (Azure AD B2C).

## <a name="end-userconsumption-related-limits"></a>Limites relacionados com o utilizador final/consumo

Os seguintes limites de serviço relacionados com o utilizador final aplicam-se a todos os protocolos de autenticação e autorização suportados pela Azure AD B2C, incluindo SAML, Open ID Connect, OAuth2 e ROPC.

|Categoria |Limite    |
|---------|---------|
|Número de pedidos por endereço IP por inquilino Azure AD B2C       |6.000/5min          |
|Número total de pedidos por inquilino Azure AD B2C     |12.000/min          |

O número de pedidos pode variar dependendo do número de leituras e escritos de diretório que ocorrem durante a viagem do utilizador Azure AD B2C. Por exemplo, uma simples viagem de inscrição que lê do diretório consiste em 1 pedido. Se a viagem de inscrição também tiver de atualizar o diretório, esta operação é contada como um pedido adicional.

## <a name="azure-ad-b2c-configuration-limits"></a>Limites de configuração AD B2C Azure

O quadro que se segue lista os limites de configuração administrativa no serviço Azure AD B2C.

|Categoria  |Limite  |
|---------|---------|
|Número de pedidos por inquilino Azure AD B2C   |250           |
|Número de âmbitos por aplicação        |1000          |
|Número de [atributos personalizados](user-profile-attributes.md#extension-attributes)   por utilizador <sup>1</sup>       |100         |
|Número de URLs de redirecionamento por aplicação       |100         |
|Número de URLs por aplicação        |1          |
|Limite de corda por atributo      |250 Chars          |
|Número de inquilinos B2C por subscrição      |20         |
|Níveis de [herança](custom-policy-overview.md#inheritance-model) em políticas personalizadas     |10         |
|Número de políticas por inquilino Azure AD B2C      |200          |
|Tamanho máximo do ficheiro de política      |400 KB          |

<sup>1</sup> Consulte também [os limites e restrições de serviço AZure AD](../active-directory/enterprise-users/directory-service-limits-restrictions.md).

## <a name="next-steps"></a>Passos seguintes

- Conheça a [orientação de estrangulamento do Microsoft Graph](/graph/throttling.md) 
- Conheça as [diferenças de validação para aplicações Azure AD B2C](../active-directory/develop/supported-accounts-validation.md)














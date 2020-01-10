---
title: Armazenamento de dados de identidade para clientes australianos-Azure AD
description: Saiba mais sobre onde o Azure Active Directory armazena dados relacionados à identidade para seus clientes australianos.
services: active-directory
author: msaburnley
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2019
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: cbe4befa824990c2f8b2627e7709913f564c0486
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75751422"
---
# <a name="identity-data-storage-for-australian-customers-in-azure-active-directory"></a>Armazenamento de dados de identidade para clientes australianos no Azure Active Directory

Os dados de identidade são armazenados pelo Azure AD em uma localização geográfica com base no endereço fornecido pela sua organização ao assinar um serviço online da Microsoft, como o Office 365 e o Azure. Para obter informações sobre onde os dados do cliente de identidade são armazenados, você pode usar a seção [onde os dados estão localizados?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) da central de confiabilidade da Microsoft.

> [!NOTE]
> Os serviços e aplicativos que se integram ao Azure AD têm acesso aos dados do cliente de identidade. Avalie cada serviço e aplicativo que você usa para determinar como os dados de identidade do cliente são processados por esse serviço e aplicativo específicos, e se eles atendem aos requisitos de armazenamento de dados da sua empresa. Para obter mais informações sobre a residência de dados dos serviços da Microsoft, consulte onde estão localizados seus dados? da central de confiabilidade da Microsoft.

Para os clientes que forneceram um endereço na Austrália, o Azure AD mantém os dados de identidade para esses serviços em data centers australianos: 
- Gerenciamento de diretório do Azure AD 
- Autenticação

Todos os outros serviços do Azure AD armazenam dados do cliente em data centers globais. Para localizar o datacenter de um serviço, consulte [Azure Active Directory – onde os dados estão localizados?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Microsoft Azure a autenticação multifator (MFA)

A MFA armazena dados de clientes de identidade em datacenters globais. Para saber mais sobre as informações do usuário coletadas e armazenadas pela MFA do Azure baseada em nuvem e pelo servidor do Azure MFA, confira [coleta de dados do usuário da autenticação multifator do Azure](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-data-residency).

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre qualquer um dos recursos e funcionalidades descritos acima, consulte estes artigos:
- [What is Multi-Factor Authentication?](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication) (O que é o Multi-Factor Authentication)

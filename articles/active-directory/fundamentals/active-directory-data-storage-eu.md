---
title: Armazenamento de dados de identidade para clientes europeus – Azure AD
description: Saiba mais sobre onde o Azure Active Directory armazena dados relacionados com a identidade para seus clientes europeus.
services: active-directory
author: msaburnley
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 03/04/2019
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7360b11f41cc08c2beb3ffa227e1658881798502
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423005"
---
# <a name="identity-data-storage-for-european-customers-in-azure-active-directory"></a>Armazenamento de dados de identidade para os clientes europeus no Azure Active Directory
Os dados de identidade são armazenados pelo Azure AD em uma localização geográfica com base no endereço fornecido pela sua organização ao assinar um serviço online da Microsoft, como o Office 365 e o Azure. Para obter informações sobre onde os dados de identidade são armazenados, você pode usar a seção [onde os dados estão localizados?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) da central de confiabilidade da Microsoft.

Para clientes que forneceram um endereço na Europa, o Azure AD mantém a maioria dos dados de identidade em datacenters europeus. Este documento fornece informações sobre todos os dados armazenados fora da Europa pelos serviços do Azure AD.

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Microsoft Azure a autenticação multifator (MFA)
    
- Toda a autenticação de dois fatores que usa chamadas telefônicas ou o SMS origina-se de data centers dos EUA e também é roteada por provedores globais.
- As notificações por push usando o aplicativo Microsoft Authenticator originam-se de datacenters dos EUA. Além disso, os serviços específicos de fornecedores de dispositivos também podem entrar em jogo e esses serviços talvez fora da Europa.
- Os códigos OATH são sempre validados nos E.U.A. 

Para obter mais informações sobre quais informações do usuário são coletadas pelo Azure Servidor de Autenticação Multifator (servidor MFA) e pela MFA do Azure baseada em nuvem, consulte [coleta de dados do usuário da autenticação multifator do Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-reporting-datacollection).

## <a name="microsoft-azure-active-directory-b2c-azure-ad-b2c"></a>Microsoft Azure Active Directory B2C (Azure AD B2C)

Os dados de configuração da política de Azure AD B2C e os contêiners de chave são armazenados nos datacenters dos EUA. Eles não contêm nenhum dado pessoal do usuário. Para obter mais informações sobre as configurações de políticas, veja o artigo [Azure Active Directory B2C: Built-in policies](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies) (Azure Active Directory B2C: Políticas incorporadas).

## <a name="microsoft-azure-active-directory-b2b-azure-ad-b2b"></a>Microsoft Azure Active Directory B2B (Azure AD B2B) 
    
O Azure AD B2B armazena convites com as informações de enviar link e redirecionar URL nos data centers dos EUA. Além disso, o endereço de email dos usuários que não se inscrevem no recebimento de convites B2B também são armazenados em data centers dos EUA.

## <a name="microsoft-azure-active-directory-domain-services-azure-ad-ds"></a>Serviços de domínio Microsoft Azure Active Directory (AD DS do Azure)

O Azure AD DS armazena os dados de utilizador na mesma localização da rede virtual do Azure que os utilizadores selecionam. Assim, se a rede estiver fora da Europa, os dados são replicados e armazenados fora deste continente.

## <a name="federation-in-microsoft-exchange-server-2013"></a>Federação no Microsoft Exchange Server 2013
    
- Identificador de aplicativo (AppID) – um número exclusivo gerado pelo sistema de autenticação de Azure Active Directory para identificar organizações do Exchange.
- Lista de domínios federados aprovados para o aplicativo
- Chave pública de assinatura de token do aplicativo 

Para obter mais informações sobre Federação no Microsoft Exchange Server, consulte o artigo de ajuda sobre a [Federação: Exchange 2013](https://docs.microsoft.com/exchange/federation-exchange-2013-help) .


## <a name="other-considerations"></a>Outras considerações

Serviços e aplicativos que se integram ao Azure AD têm acesso aos dados de identidade. Avalie cada serviço e aplicativo que você usa para determinar como os dados de identidade são processados por esse serviço e aplicativo específicos, e se eles atendem aos requisitos de armazenamento de dados da sua empresa.

Para obter mais informações sobre a residência de dados dos serviços Microsoft, veja a secção [Where is your data located?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) (Onde estão localizados os seus dados) do Microsoft Trust Center.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre qualquer um dos recursos e funcionalidades descritos acima, consulte estes artigos:
- [What is Multi-Factor Authentication?](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication) (O que é o Multi-Factor Authentication)

- [Azure AD self-service password reset](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-overview) (Reposição personalizada de palavras-passe do Azure AD)

- [O que é o Azure Active Directory B2C?](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)

- [O que é a colaboração B2B do Azure AD?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)

- [Azure Active Directory (AD) Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview)

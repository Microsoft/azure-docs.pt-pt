---
title: Armazenamento de dados de identidade para clientes europeus - Azure AD
description: Saiba onde o Azure Ative Directory armazena dados relacionados com a identidade para os seus clientes europeus.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75423005"
---
# <a name="identity-data-storage-for-european-customers-in-azure-active-directory"></a>Armazenamento de dados de identidade para clientes europeus no Diretório Ativo do Azure
Os dados de identidade são armazenados pela Azure AD numa localização geográfica com base no endereço fornecido pela sua organização ao subscrever um serviço Microsoft Online como o Office 365 e o Azure. Para obter informações sobre onde os seus dados de identidade são armazenados, pode utilizar a secção [Onde estão os seus dados localizados?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)

Para os clientes que forneceram um endereço na Europa, a Azure AD mantém a maioria dos dados de identidade dentro dos datacenters europeus. Este documento fornece informações sobre quaisquer dados que sejam armazenados fora da Europa pelos serviços da Azure AD.

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Autenticação multifactor do Microsoft Azure (MFA)
    
- Toda a autenticação de dois fatores usando chamadas telefónicas ou SMS tem origem em datacenters dos EUA e também são encaminhados por fornecedores globais.
- As notificações push utilizando a aplicação Microsoft Authenticator têm origem em datacenters dos EUA. Além disso, os serviços específicos do fornecedor de dispositivos também podem entrar em jogo e estes serviços talvez fora da Europa.
- Os códigos OATH são sempre validados nos E.U.A. 

Para obter mais informações sobre as informações do utilizador recolhidas pelo Azure Multi-Factor Authentication Server (MFA Server) e pelo Azure MFA baseado na nuvem, consulte a recolha de dados dos utilizadores de [autenticação multi-factor Azure.](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-reporting-datacollection)

## <a name="microsoft-azure-active-directory-b2c-azure-ad-b2c"></a>Microsoft Azure Active Directory B2C (Azure AD B2C)

Os dados de configuração da política Azure AD B2C e os porta-chaves são armazenados em centros de dados dos EUA. Estes não contêm quaisquer dados pessoais do utilizador. Para obter mais informações sobre as configurações de políticas, veja o artigo [Azure Active Directory B2C: Built-in policies](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies) (Azure Active Directory B2C: Políticas incorporadas).

## <a name="microsoft-azure-active-directory-b2b-azure-ad-b2b"></a>Microsoft Azure Active Directory B2B (Azure AD B2B) 
    
O Azure AD B2B armazena convites com link de resgate e redirecionamento de informações de URL nos centros de dados dos EUA. Além disso, o endereço de e-mail dos utilizadores que desinscrevam de receber convites B2B também são armazenados em datacenters dos EUA.

## <a name="microsoft-azure-active-directory-domain-services-azure-ad-ds"></a>Microsoft Azure Active Directory Domain Services (Azure AD DS)

O Azure AD DS armazena os dados de utilizador na mesma localização da rede virtual do Azure que os utilizadores selecionam. Assim, se a rede estiver fora da Europa, os dados são replicados e armazenados fora deste continente.

## <a name="federation-in-microsoft-exchange-server-2013"></a>Federação no Microsoft Exchange Server 2013
    
- Identificador de aplicações (AppID) - Um número único gerado pelo sistema de autenticação de Diretório Ativo Azure para identificar organizações de Intercâmbio.
- Lista de domínios federados aprovados para aplicação
- Assinatura simbólica da aplicação Chave Pública 

Para mais informações sobre a federação no servidor da Microsoft Exchange, consulte o artigo [de Ajuda da Federação: Exchange 2013.](https://docs.microsoft.com/exchange/federation-exchange-2013-help)


## <a name="other-considerations"></a>Outras considerações

Os serviços e aplicações que se integram com a Azure AD têm acesso a dados de identidade. Avalie cada serviço e aplicação que utiliza para determinar como os dados de identidade são tratados por esse serviço e aplicação específicos, e se cumprem os requisitos de armazenamento de dados da sua empresa.

Para obter mais informações sobre a residência de dados dos serviços Microsoft, veja a secção [Where is your data located?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) (Onde estão localizados os seus dados) do Microsoft Trust Center.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre qualquer uma das funcionalidades e funcionalidades acima descritas, consulte estes artigos:
- [What is Multi-Factor Authentication?](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication) (O que é o Multi-Factor Authentication)

- [Reposição personalizada de palavra-passe do Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-overview)

- [O que é o Azure Active Directory B2C?](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)

- [O que é a colaboração B2B do Azure AD?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)

- [Azure Active Directory (AD) Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview)

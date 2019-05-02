---
title: Armazenamento de dados de identidade para os clientes europeus - Azure Active Directory | Documentos da Microsoft
description: Saiba mais sobre onde o Azure Active Directory armazena dados relacionados com a identidade para seus clientes europeus.
services: active-directory
author: eross-msft
manager: daveba
ms.author: lizross
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 03/04/2019
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 93ac5ef5f03f800a8f90259db3e382b3bc5c5e2c
ms.sourcegitcommit: 2c09af866f6cc3b2169e84100daea0aac9fc7fd0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2019
ms.locfileid: "64875646"
---
# <a name="identity-data-storage-for-european-customers-in-azure-active-directory"></a>Armazenamento de dados de identidade para os clientes europeus no Azure Active Directory
Dados de identidade são armazenados pelo Azure AD numa localização geográfica com base no endereço fornecido pela sua organização ao subscrever um serviço Online da Microsoft, como o Office 365 e Azure. Para obter informações sobre onde estão armazenados os dados de identidade, pode utilizar o [é onde os dados localizados?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) secção da Microsoft Trust Center.

Para os clientes que forneceu um endereço na Europa, do Azure AD mantém a maioria dos dados de identidade entre os datacenters europeus. Este documento fornece informações sobre todos os dados que estão armazenados fora do Europa por serviços do Azure AD.

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Autenticação multifator do Microsoft Azure (MFA)
    
- Autenticação de todos os dois fatores através de chamadas de telefone ou SMS têm origem nos datacenters nos EUA e também é roteado pelo fornecedores mundiais.
- Enviar notificações push com o Microsoft Authenticator app provêm de datacenters nos EUA. Além disso, os serviços específicos do fornecedor de dispositivo também pode ter no play e estes serviços talvez fora da Europa.
- Os códigos OATH são sempre validados nos E.U.A. 

## <a name="microsoft-azure-active-directory-b2c-azure-ad-b2c"></a>Microsoft Azure Active Directory B2C (Azure AD B2C)

Dados de configuração de política do AD B2C e contentores de chaves do Azure são armazenados em datacenters nos EUA. Estes não contêm dados pessoais do utilizador. Para mais informações sobre as configurações da política, veja o [Azure Active Directory B2C: Políticas incorporadas](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies) artigo.

## <a name="microsoft-azure-active-directory-b2b-azure-ad-b2b"></a>Microsoft Azure Active Directory B2B (Azure AD B2B) 
    
Convites de lojas AD B2B do Azure com resgatar associar e redirecionar informações de URL em datacenters nos EUA. Além disso, o endereço de correio eletrónico de utilizadores que anular a subscrição de receber convites de B2B também são armazenadas em datacenters nos EUA.

## <a name="microsoft-azure-active-directory-domain-services-azure-ad-ds"></a>Serviços de domínio do Microsoft Azure Active Directory (Azure AD DS)

O Azure AD DS armazena os dados de utilizador na mesma localização da rede virtual do Azure que os utilizadores selecionam. Assim, se a rede estiver fora da Europa, os dados são replicados e armazenados fora deste continente.

## <a name="other-considerations"></a>Outras considerações

Serviços e aplicações que se integram com o Azure AD têm acesso a dados de identidade. Avalie cada serviço e o que usa para determinar como os dados de identidade são processados por esse serviço específico e a aplicação e, se cumprirem os requisitos de armazenamento de dados da sua empresa.

Para obter mais informações sobre a residência de dados dos serviços Microsoft, veja a secção [Where is your data located?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) (Onde estão localizados os seus dados) do Microsoft Trust Center.

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre qualquer um dos recursos e funcionalidade descrita acima, veja estes artigos:
- [What is Multi-Factor Authentication?](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication) (O que é o Multi-Factor Authentication)

- [Azure AD self-service password reset](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-overview) (Reposição personalizada de palavras-passe do Azure AD)

- [O que é o Azure Active Directory B2C?](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)

- [O que é a colaboração B2B do Azure AD?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)

- [Azure Active Directory (AD) Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview)

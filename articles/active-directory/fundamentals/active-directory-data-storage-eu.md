---
title: Armazenamento de dados de identidade para clientes europeus - Azure AD
description: Saiba onde o Azure Ative Directory armazena dados relacionados com a identidade para os seus clientes europeus.
services: active-directory
author: ajburnle
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/15/2020
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7a8b013723707c4a3a087a90674227c3d41c5108
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94836942"
---
# <a name="identity-data-storage-for-european-customers-in-azure-active-directory"></a>Armazenamento de dados de identidade para clientes europeus no Azure Ative Directory
Os dados de identidade são armazenados pela Azure AD numa localização geográfica baseada no endereço fornecido pela sua organização ao subscrever um serviço Microsoft Online como o Microsoft 365 e o Azure. Para obter informações sobre onde os seus dados de identidade são armazenados, pode utilizar a secção [Onde os seus dados estão localizados?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)

Para os clientes que forneceram uma morada na Europa, a Azure AD mantém a maioria dos dados de identidade dentro dos datacenters europeus. Este documento fornece informações sobre quaisquer dados armazenados fora da Europa pelos serviços Azure AD.

## <a name="microsoft-azure-ad-multi-factor-authentication"></a>Autenticação multi-factor do Microsoft Azure

Para a autenticação multi-factor Azure AD baseada na nuvem, a autenticação é completa no centro de dados mais próximo do utilizador. Os datacenters para a autenticação multi-factor Azure AD existem na América do Norte, Europa e Ásia-Pacífico.

* A autenticação multi-factor usando chamadas telefónicas tem origem em datacenters dos EUA e é encaminhada por fornecedores globais.
* A autenticação multi-factor utilizando SMS é encaminhada por fornecedores globais.
* Os pedidos de autenticação de vários fatores que utilizam as notificações push da aplicação Microsoft Authenticator originárias de datacenters da UE são processados em datacenters da UE.
    * Os serviços específicos do fornecedor de dispositivos, como as notificações do Apple Push, podem estar fora da Europa.
* Os pedidos de autenticação de vários fatores que utilizam códigos OATH originários de centros de dados da UE são validados na UE.

Para obter mais informações sobre as informações do utilizador recolhidas pelo Azure Multi-Factor Authentication Server (MFA Server) e pelo Azure AD MFA baseado na nuvem, consulte [a recolha de dados do utilizador de autenticação multi-factor Azure](../authentication/howto-mfa-reporting-datacollection.md).

## <a name="password-based-single-sign-on-for-enterprise-applications"></a>Sign-On única baseada em palavra-passe para aplicações empresariais
 
Se um cliente criar uma nova aplicação empresarial (seja através da Galeria AD AZure ou não da Galeria) e permitir sSO baseado em palavra-passe, o sinal de aplicação em URL e o sinal de captura personalizado nos campos são armazenados nos Estados Unidos. Para obter mais informações sobre esta funcionalidade, consulte o [Configure o único sinal baseado em palavra-passe](../manage-apps/configure-password-single-sign-on-non-gallery-applications.md)

## <a name="microsoft-azure-active-directory-b2c-azure-ad-b2c"></a>Microsoft Azure Active Directory B2C (Azure AD B2C)

Os dados de configuração da política Azure AD B2C e os principais contentores são armazenados em centros de dados dos EUA. Estes não contêm quaisquer dados pessoais do utilizador. Para obter mais informações sobre as configurações de políticas, veja o artigo [Azure Active Directory B2C: Built-in policies](../../active-directory-b2c/user-flow-overview.md) (Azure Active Directory B2C: Políticas incorporadas).

## <a name="microsoft-azure-active-directory-b2b-azure-ad-b2b"></a>Microsoft Azure Active Directory B2B (Azure AD B2B) 
    
Azure AD B2B armazena convites com link de resgate e redireciona informações de URL em datacenters dos EUA. Além disso, o endereço de e-mail dos utilizadores que não subscrevem a receção de convites B2B também são armazenados em centros de dados dos EUA.

## <a name="microsoft-azure-active-directory-domain-services-azure-ad-ds"></a>Microsoft Azure Active Directory Domain Services (Azure AD DS)

O Azure AD DS armazena os dados de utilizador na mesma localização da rede virtual do Azure que os utilizadores selecionam. Assim, se a rede estiver fora da Europa, os dados são replicados e armazenados fora deste continente.

## <a name="federation-in-microsoft-exchange-server-2013"></a>Federação no Microsoft Exchange Server 2013
    
- Identificador de aplicações (AppID) - Um número único gerado pelo sistema de autenticação do Azure Ative Directory para identificar organizações de Intercâmbio.
- Lista de domínios federados aprovados para aplicação
- Ficha de inscrição assinando Chave Pública 

Para obter mais informações sobre a federação no servidor Microsoft Exchange, consulte a [Federação: Artigo de ajuda de 2013.](/exchange/federation-exchange-2013-help)


## <a name="other-considerations"></a>Outras considerações

Os serviços e aplicações que integram a Azure AD têm acesso a dados de identidade. Avalie cada serviço e aplicação que utiliza para determinar como os dados de identidade são tratados por esse serviço e aplicação específicos, e se cumprem os requisitos de armazenamento de dados da sua empresa.

Para obter mais informações sobre a residência de dados dos serviços Microsoft, veja a secção [Where is your data located?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) (Onde estão localizados os seus dados) do Microsoft Trust Center.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre qualquer uma das funcionalidades e funcionalidades acima descritas, consulte estes artigos:
- [What is Multi-Factor Authentication?](../authentication/concept-mfa-howitworks.md) (O que é o Multi-Factor Authentication)

- [Reposição personalizada de palavra-passe do Azure AD](../authentication/concept-sspr-howitworks.md)

- [O que é o Azure Active Directory B2C?](../../active-directory-b2c/overview.md)

- [O que é a colaboração B2B do Azure AD?](../external-identities/what-is-b2b.md)

- [Azure Active Directory (AD) Domain Services](../../active-directory-domain-services/overview.md)
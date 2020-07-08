---
title: Armazenamento de dados de identidade para clientes australianos - Azure AD
description: Saiba onde o Azure Ative Directory armazena dados relacionados com a identidade para os seus clientes australianos.
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
ms.openlocfilehash: 0dc659f3d580646c1e86e5e97940268311e5546f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "79460539"
---
# <a name="identity-data-storage-for-australian-and-new-zealand-customers-in-azure-active-directory"></a>Armazenamento de dados de identidade para clientes australianos e neozelandeses em Azure Ative Directory

Os dados de identidade são armazenados pela Azure AD numa localização geográfica com base no endereço fornecido pela sua organização ao subscrever um serviço Microsoft Online como o Office 365 e o Azure. Para obter informações sobre onde os seus Dados de Cliente de Identidade estão armazenados, pode utilizar a secção [Onde os seus dados estão localizados?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)

> [!NOTE]
> Os serviços e aplicações que se integram com a Azure AD têm acesso aos Dados do Cliente de Identidade. Avalie cada serviço e aplicação que utiliza para determinar como os Dados do Cliente de Identidade são tratados por esse serviço e aplicação específicos e se cumprem os requisitos de armazenamento de dados da sua empresa. Para obter mais informações sobre a residência de dados dos serviços Microsoft, veja a secção Where is your data located? (Onde estão localizados os seus dados) do Microsoft Trust Center.

Para clientes que forneceram uma morada na Austrália e nova Zelândia e utilizam edição gratuita do Azure AD, a Azure AD mantém os dados pii em repouso dentro dos datacenters australianos. 

Todos os outros serviços premium Azure armazenam dados de clientes em datacenters globais. Para localizar o datacenter para um serviço, consulte [o Diretório Ativo Azure – Onde estão os seus dados localizados?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Autenticação multi-factor microsoft Azure (MFA)

O serviço MFA em Azure AD armazena Dados de Clientes de Identidade em centros de dados globais em repouso. Para saber mais sobre as informações do utilizador recolhidas e armazenadas pelo Azure MFA e pelo Azure MFA Server, consulte [a recolha de dados do utilizador Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-data-residency). Se os clientes utilizarem MFA, os seus dados serão armazenados fora dos datacenters da Austrália em repouso. 

## <a name="next-steps"></a>Próximos passos
Para obter mais informações sobre qualquer uma das funcionalidades e funcionalidades acima descritas, consulte estes artigos:
- [What is Multi-Factor Authentication?](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication) (O que é o Multi-Factor Authentication)

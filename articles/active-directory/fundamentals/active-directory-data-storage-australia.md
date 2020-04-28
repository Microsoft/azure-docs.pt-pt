---
title: Armazenamento de dados de identidade para clientes australianos e neozelandeses - Azure AD
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
ms.openlocfilehash: 850298719d5636e964b0c338d7a2a4cc9bb8aece
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77370288"
---
# <a name="identity-data-storage-for-australian-and-new-zealand-customers-in-azure-active-directory"></a>Armazenamento de dados de identidade para clientes australianos e neozelandeses no Diretório Ativo Azure

Os dados de identidade são armazenados pela Azure AD numa localização geográfica com base no endereço fornecido pela sua organização ao subscrever um serviço Microsoft Online como o Office 365 e o Azure. Para obter informações sobre onde os seus Dados de Cliente de Identidade estão armazenados, pode utilizar a secção [Onde estão os seus dados localizados?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)

> [!NOTE]
> Os serviços e aplicações que integram com a Azure AD têm acesso aos Dados do Cliente De Identidade. Avalie cada serviço e aplicação que utiliza para determinar como os Dados do Cliente de Identidade são tratados por esse serviço e aplicação específicos, e se cumprem os requisitos de armazenamento de dados da sua empresa. Para obter mais informações sobre a residência de dados dos serviços Microsoft, veja a secção Where is your data located? (Onde estão localizados os seus dados) do Microsoft Trust Center.

Para os clientes que forneceram um endereço na Austrália ou na Nova Zelândia, a Azure AD guarda dados de identidade para estes serviços dentro de datacenters australianos: 
- Gestão de Diretórios azure AD 
- Autenticação

Todos os outros serviços da Azure AD armazenam dados de clientes em centros de dados globais. Para localizar o datacenter para um serviço, consulte [o Azure Ative Directory – Onde estão os seus dados localizados?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Autenticação multifactor do Microsoft Azure (MFA)

MFA armazena Dados de Clientes De Identidade em datacenters globais. Para saber mais sobre as informações recolhidas e armazenadas pelo Azure MFA e pelo Servidor Azure MFA baseados na nuvem, consulte a recolha de dados dos utilizadores da [Autenticação Multi-Factor Azure.](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-data-residency)

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre qualquer uma das funcionalidades e funcionalidades acima descritas, consulte estes artigos:
- [What is Multi-Factor Authentication?](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication) (O que é o Multi-Factor Authentication)

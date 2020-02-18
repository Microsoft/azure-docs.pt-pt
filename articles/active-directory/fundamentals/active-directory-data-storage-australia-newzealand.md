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
ms.openlocfilehash: cbe4befa824990c2f8b2627e7709913f564c0486
ms.sourcegitcommit: f255f869c1dc451fd71e0cab340af629a1b5fb6b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/16/2020
ms.locfileid: "77371530"
---
# <a name="identity-data-storage-for-australian-customers-in-azure-active-directory"></a>Armazenamento de dados de identidade para clientes australianos no Diretório Ativo azure

Os dados de identidade são armazenados pela Azure AD numa localização geográfica com base no endereço fornecido pela sua organização ao subscrever um serviço Microsoft Online como o Office 365 e o Azure. Para obter informações sobre onde os seus Dados de Cliente de Identidade estão armazenados, pode utilizar a secção [Onde estão os seus dados localizados?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)

> [!NOTE]
> Os serviços e aplicações que integram com a Azure AD têm acesso aos Dados do Cliente De Identidade. Avalie cada serviço e aplicação que utiliza para determinar como os Dados do Cliente de Identidade são tratados por esse serviço e aplicação específicos, e se cumprem os requisitos de armazenamento de dados da sua empresa. Para obter mais informações sobre a residência de dados dos serviços da Microsoft, consulte o Local onde estão localizados os seus dados? secção do Microsoft Trust Center.

Para os clientes que forneceram um endereço na Austrália, a Azure AD guarda dados de identidade para estes serviços dentro de datacenters australianos: 
- Gestão de Diretórios azure AD 
- Autenticação

Todos os outros serviços da Azure AD armazenam dados de clientes em centros de dados globais. Para localizar o datacenter para um serviço, consulte [o Azure Ative Directory – Onde estão os seus dados localizados?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Autenticação multifactor do Microsoft Azure (MFA)

MFA armazena Dados de Clientes De Identidade em datacenters globais. Para saber mais sobre as informações recolhidas e armazenadas pelo Azure MFA e pelo Servidor Azure MFA baseados na nuvem, consulte a recolha de dados dos utilizadores da [Autenticação Multi-Factor Azure.](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-data-residency)

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre qualquer uma das funcionalidades e funcionalidades acima descritas, consulte estes artigos:
- [What is Multi-Factor Authentication?](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication) (O que é o Multi-Factor Authentication)

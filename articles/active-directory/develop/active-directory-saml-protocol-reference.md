---
title: Como o Azure AD usa o protocolo SAML | Microsoft Docs
description: Este artigo fornece uma visão geral dos perfis SAML de logon único e logout único no Azure Active Directory.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 88125cfc-45c1-448b-9903-a629d8f31b01
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/05/2018
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: 57c5f21e04b2d3adad975be0368d9435583b4f72
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74844674"
---
# <a name="how-azure-ad-uses-the-saml-protocol"></a>Como o Azure AD utiliza o protocolo SAML

O Azure Active Directory (AD do Azure) usa o protocolo SAML 2,0 para permitir que os aplicativos forneçam uma experiência de logon único para seus usuários. Os perfis SAML de [logon único](single-sign-on-saml-protocol.md) e [logout único](single-sign-out-saml-protocol.md) do Azure ad explicam como as asserções, os protocolos e as associações SAML são usados no serviço de provedor de identidade.

O protocolo SAML requer que o provedor de identidade (Azure AD) e o provedor de serviços (o aplicativo) troquem informações sobre eles mesmos.

Quando um aplicativo é registrado no Azure AD, o desenvolvedor do aplicativo registra informações relacionadas à Federação com o Azure AD. Essas informações incluem o **URI de redirecionamento** e o **URI de metadados** do aplicativo.

O Azure AD usa o URI de **metadados** do serviço de nuvem para recuperar a chave de assinatura e o URI de logout. O cliente pode abrir o aplicativo no **Azure ad – > registro de aplicativo** e, em seguida, em **Configurações-> Propriedades**, eles podem atualizar a URL de logout. Dessa forma, o Azure AD pode enviar a resposta para a URL correta. 

O Azure Active Directory expõe pontos de extremidade de logon único e de saída único de locatários e comuns (independentes de locatário). Essas URLs representam locais endereçáveis – elas não são apenas identificadores, portanto, você pode ir para o ponto de extremidade para ler os metadados.

* O ponto de extremidade específico do locatário está localizado em `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`. O espaço reservado *\<TenantDomainName >* representa um nome de domínio registrado ou um GUID tenantid de um locatário do Azure AD. Por exemplo, os metadados de Federação do locatário contoso.com estão em: https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

* O ponto de extremidade independente de locatário está localizado em `https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml`. Nesse endereço de ponto de extremidade, **Common** é exibido em vez de um nome de domínio de locatário ou ID.

Para obter informações sobre os documentos de metadados de Federação que o Azure AD publica, consulte [metadados de Federação](azure-ad-federation-metadata.md).

---
title: Como o Azure AD utiliza o protocolo SAML | Documentos da Microsoft
description: Este artigo fornece uma descrição geral dos perfis de início de sessão único e SAML de fim de sessão único no Azure Active Directory.
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 88125cfc-45c1-448b-9903-a629d8f31b01
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: 35c231f043261b65baa40eff32535eeab014bee9
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58437965"
---
# <a name="how-azure-ad-uses-the-saml-protocol"></a>Como o Azure AD utiliza o protocolo SAML

Utiliza o SAML 2.0 protocolo de Active Directory (Azure AD) do Azure para ativar aplicações para proporcionar uma experiência de início de sessão único para seus usuários. O [início de sessão único](single-sign-on-saml-protocol.md) e [fim de sessão único](single-sign-out-saml-protocol.md) perfis SAML do Azure AD explicam como asserções SAML, protocolos e ligações são utilizadas no serviço do fornecedor de identidade.

Protocolo SAML requer o fornecedor de identidade (Azure AD) e o fornecedor de serviços (o aplicativo) para trocar informações sobre si.

Quando um aplicativo está registrado com o Azure AD, o programador da aplicação regista informações relacionadas com a Federação com o Azure AD. Estas informações incluem o **URI de redirecionamento** e **URI de metadados** do aplicativo.

Azure AD utiliza o serviço em nuvem **URI de metadados** para obter a chave de assinatura e a fim de sessão URI. Cliente pode abrir a aplicação no **do Azure AD -> registo de aplicações** e, em seguida, no **definições -> propriedades**, podem atualizar o URL de fim de sessão. Desta forma do Azure AD pode enviar a resposta para o URL correto. 

O Azure Active Directory expõe comuns e específico de inquilino (inquilino independente) únicos início de sessão únicos fim de sessão pontos de extremidade e. Estes URLs representam os locais endereçáveis--não são apenas identificadores, para que pode ir para o ponto final para ler os metadados.

* O ponto de extremidade específico de inquilino está localizado em `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`. O  *\<TenantDomainName >* marcador de posição representa um nome de domínio registado ou o GUID de TenantID do inquilino do Azure AD. Por exemplo, os metadados de Federação do inquilino contoso.com são em: https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

* O ponto de extremidade independente de inquilino está localizado em `https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml`. Este endereço de ponto final **comuns** aparece em vez de um nome de domínio do inquilino ou ID.

Para obter informações sobre os documentos de metadados de federação que publica do Azure AD, consulte [metadados de Federação](azure-ad-federation-metadata.md).

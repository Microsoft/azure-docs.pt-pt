---
title: Como a plataforma de identidade da Microsoft utiliza o protocolo SAML
description: Este artigo fornece uma visão geral dos perfis de Sign-On e SAML single Sign-Out no Diretório Ativo Azure.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/05/2018
ms.author: kenwith
ms.custom: aaddev
ms.reviewer: paulgarn
ms.openlocfilehash: 4e9d63b8fe7fc281a87deb27ddadd794e14fa04d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98755629"
---
# <a name="how-the-microsoft-identity-platform-uses-the-saml-protocol"></a>Como a plataforma de identidade da Microsoft utiliza o protocolo SAML

A plataforma de identidade da Microsoft utiliza o protocolo SAML 2.0 para permitir que as aplicações forneçam uma única experiência de início de súp à sua utilizadores. Os perfis SAML [de assinatura única](single-sign-on-saml-protocol.md) e único [sign-out](single-sign-out-saml-protocol.md) da Azure AD explicam como as afirmações, protocolos e encadernações da SAML são utilizadas no serviço de fornecedor de identidade.

O Protocolo SAML exige que o fornecedor de identidade (plataforma de identidade da Microsoft) e o prestador de serviços (a aplicação) troquem informações sobre si mesmos.

Quando uma aplicação é registada no Azure AD, o desenvolvedor de aplicações regista informações relacionadas com a federação com a Azure AD. Esta informação inclui o **Redirect URI** e **Metadata URI** da aplicação.

A plataforma de identidade da Microsoft utiliza o **Metadadata URI** do serviço de nuvem para recuperar a chave de assinatura e o logout URI. O cliente pode abrir a aplicação em **Azure AD -> Registo de Aplicações** e, em seguida, em **Definições -> Propriedades,** podem atualizar o URL do Logout. Desta forma, a plataforma de identidade da Microsoft pode enviar a resposta para o URL correto. 

O Azure Ative Directory expõe pontos finais únicos de assinatura e assinatura únicos específicos e comuns (independentes do inquilino). Estes URLs representam locais endereçados, não são apenas identificadores, por isso podes ir ao ponto final ler os metadados.

* O ponto final específico do inquilino está localizado em `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml` . O espaço reservado representa um nome de *\<TenantDomainName>* domínio registado ou TenantID GUID de um inquilino AZure AD. Por exemplo, os metadados da federação do inquilino contoso.com estão em: https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

* O ponto final independente do inquilino está localizado em `https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml` . Neste endereço de ponto final, **o comum** aparece em vez de um nome de domínio de inquilino ou ID.

Para obter informações sobre os documentos de metadados da federação que a Azure AD publica, consulte [os Metadados da Federação](../azuread-dev/azure-ad-federation-metadata.md).

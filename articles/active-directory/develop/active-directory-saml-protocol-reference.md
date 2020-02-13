---
title: Como a Azure AD utiliza o protocolo SAML  Microsoft Docs
description: Este artigo fornece uma visão geral dos perfis SAML de entrada única e de inscrição única no Diretório Ativo Azure.
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
ms.openlocfilehash: dc7771f29fb5d00aedfe5162a98f5f0c14544a7b
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77161176"
---
# <a name="how-azure-ad-uses-the-saml-protocol"></a>Como o Azure AD utiliza o protocolo SAML

O Azure Ative Directory (Azure AD) utiliza o protocolo SAML 2.0 para permitir que as aplicações forneçam uma única experiência de início de sessão aos seus utilizadores. Os perfis SAML de [Entrada](single-sign-on-saml-protocol.md) Única e Único De [saml](single-sign-out-saml-protocol.md) da Azure AD explicam como as afirmações, protocolos e encadernações da SAML são utilizados no serviço de fornecedor de identidade.

O Protocolo SAML exige que o fornecedor de identidade (Azure AD) e o prestador de serviços (o pedido) troquem informações sobre si mesmos.

Quando uma aplicação é registada na Azure AD, o desenvolvedor de aplicações regista informações relacionadas com a federação com a Azure AD. Estas informações incluem o **Redirect URI** e **metadata URI** da aplicação.

A Azure AD utiliza o **Metadata URI** do serviço na nuvem para recuperar a chave de assinatura e o logout URI. O cliente pode abrir a aplicação em **Azure AD -> Registo** de Aplicações e, em seguida, em **Definições -> Propriedades,** podem atualizar o URL de Logout. Desta forma, o Azure AD pode enviar a resposta para o URL correto. 

O Azure Ative Directory expõe os pontos finais de inscrição simples e únicos (independentes de inquilinos) e de entrada única. Estes URLs representam localizações endereçáveis, não são apenas identificadores, por isso pode supor que pode ir ao ponto final para ler os metadados.

* O ponto final específico do inquilino está localizado em `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`. O *\<TenantDomainName>* o espaço reservado representa um nome de domínio registado ou O TenantID GUID de um inquilino da AD Azure. Por exemplo, os metadados da federação do inquilino contoso.com estão em: https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

* O ponto final independente do inquilino está localizado em `https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml`. Neste endereço final, aparece **comum** em vez de um nome de domínio ou ID do inquilino.

Para obter informações sobre os documentos de metadados da federação que a Azure AD publica, consulte [metadados da Federação](../azuread-dev/azure-ad-federation-metadata.md).

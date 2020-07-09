---
title: Como o Azure AD utiliza o protocolo SAML
description: Este artigo fornece uma visão geral dos perfis SAML de assinatura única e única sinalização no Diretório Ativo Azure.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/05/2018
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.openlocfilehash: 44d06030d8015d2df9499ce903eb9cb06e1ef27a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80885655"
---
# <a name="how-azure-ad-uses-the-saml-protocol"></a>Como o Azure AD utiliza o protocolo SAML

O Azure Ative Directory (Azure AD) utiliza o protocolo SAML 2.0 para permitir que as aplicações proporcionem uma única experiência de início de sação aos seus utilizadores. Os perfis SAML [de assinatura única](single-sign-on-saml-protocol.md) e único [sign-out](single-sign-out-saml-protocol.md) da Azure AD explicam como as afirmações, protocolos e encadernações da SAML são utilizadas no serviço de fornecedor de identidade.

O Protocolo SAML exige que o fornecedor de identidade (Azure AD) e o prestador de serviços (a aplicação) troquem informações sobre si mesmos.

Quando uma aplicação é registada no Azure AD, o desenvolvedor de aplicações regista informações relacionadas com a federação com a Azure AD. Esta informação inclui o **Redirect URI** e **Metadata URI** da aplicação.

O Azure AD utiliza o **Metadadata URI** do serviço de nuvem para recuperar a chave de assinatura e o logout URI. O cliente pode abrir a aplicação em **Azure AD -> Registo de Aplicações** e, em seguida, em **Definições -> Propriedades,** podem atualizar o URL do Logout. Desta forma, a Azure AD pode enviar a resposta para o URL correto. 

O Azure Ative Directory expõe pontos finais únicos de assinatura e assinatura únicos específicos e comuns (independentes do inquilino). Estes URLs representam locais endereçados, não são apenas identificadores, por isso podes ir ao ponto final ler os metadados.

* O ponto final específico do inquilino está localizado em `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml` . O espaço reservado representa um nome de *\<TenantDomainName>* domínio registado ou TenantID GUID de um inquilino AZure AD. Por exemplo, os metadados da federação do inquilino contoso.com estão em:https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

* O ponto final independente do inquilino está localizado em `https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml` . Neste endereço de ponto final, **o comum** aparece em vez de um nome de domínio de inquilino ou ID.

Para obter informações sobre os documentos de metadados da federação que a Azure AD publica, consulte [os Metadados da Federação](../azuread-dev/azure-ad-federation-metadata.md).

---
title: Como o Azure AD utiliza o protocolo SAML
description: Este artigo fornece uma visão geral dos perfis SAML de assinatura única e única sinalização no Diretório Ativo Azure.
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
ms.openlocfilehash: 54d8278a93bfd2d6009422a5c105be14a7bc3c80
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87274328"
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

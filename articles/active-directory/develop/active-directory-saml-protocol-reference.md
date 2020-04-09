---
title: Como o Azure AD utiliza o protocolo SAML
description: Este artigo fornece uma visão geral dos perfis SAML de entrada única e de inscrição única no Diretório Ativo Azure.
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
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885655"
---
# <a name="how-azure-ad-uses-the-saml-protocol"></a>Como o Azure AD utiliza o protocolo SAML

O Azure Ative Directory (Azure AD) utiliza o protocolo SAML 2.0 para permitir que as aplicações forneçam uma única experiência de início de sessão aos seus utilizadores. Os perfis SAML de [Entrada](single-sign-on-saml-protocol.md) Única e Único De [saml](single-sign-out-saml-protocol.md) da Azure AD explicam como as afirmações, protocolos e encadernações da SAML são utilizados no serviço de fornecedor de identidade.

O Protocolo SAML exige que o fornecedor de identidade (Azure AD) e o prestador de serviços (o pedido) troquem informações sobre si mesmos.

Quando uma aplicação é registada na Azure AD, o desenvolvedor de aplicações regista informações relacionadas com a federação com a Azure AD. Estas informações incluem o **Redirect URI** e **metadata URI** da aplicação.

A Azure AD utiliza o **Metadata URI** do serviço na nuvem para recuperar a chave de assinatura e o logout URI. O cliente pode abrir a aplicação no Registo de **Aplicações > Azure E,** em seguida, em **Definições -> Propriedades,** pode atualizar o URL de Logout. Desta forma, o Azure AD pode enviar a resposta para o URL correto. 

O Azure Ative Directory expõe os pontos finais de inscrição simples e únicos (independentes de inquilinos) e de entrada única. Estes URLs representam localizações endereçáveis, não são apenas identificadores, por isso pode supor que pode ir ao ponto final para ler os metadados.

* O ponto final específico do `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`inquilino está localizado em . O * \<TenantDomainName>* espaço reservado representa um nome de domínio registado ou 'TenantID GUID' de um inquilino da AD Azure. Por exemplo, os metadados da federação do contoso.com inquilino está em:https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

* O ponto final independente do `https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml`inquilino está localizado em . Neste endereço final, aparece **comum** em vez de um nome de domínio ou ID do inquilino.

Para obter informações sobre os documentos de metadados da federação que a Azure AD publica, consulte [metadados da Federação](../azuread-dev/azure-ad-federation-metadata.md).

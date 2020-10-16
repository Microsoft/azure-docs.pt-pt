---
title: Diferenças de validação por tipos de conta suportados - plataforma de identidade da Microsoft Rio Azure
description: Conheça as diferenças de validação de várias propriedades para diferentes tipos de conta suportadas ao registar a sua aplicação na plataforma de identidade microsoft.
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 07/21/2020
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.openlocfilehash: e794e277f6731c7b6e57a4710eea437f65be0340
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87336349"
---
# <a name="validation-differences-by-supported-account-types-signinaudience"></a>Diferenças de validação por tipos de conta suportados (signInAudience)

Ao registar uma aplicação na plataforma de identidade da Microsoft para programadores, é-lhe pedido que selecione quais os tipos de conta que a sua aplicação suporta. No objeto de aplicação e manifesto, esta propriedade é `signInAudience` .

As opções incluem:

- *AzureADMyOrg*: Apenas contas no diretório organizacional onde a aplicação está registada (inquilino único)
- *AzureADMultipleOrgs*: Contas em qualquer diretório organizacional (multi-inquilino)
- *AzureADandPersonalMicrosoftAccount*: Contas em qualquer diretório organizacional (multi-inquilino) e contas pessoais da Microsoft (por exemplo, Skype, Xbox e Outlook.com)

Para aplicações registadas, pode encontrar o valor para tipos de conta suportados na secção **autenticação** de uma aplicação. Você também pode encontrá-lo sob a `signInAudience` propriedade no **Manifesto.**

O valor que seleciona para esta propriedade tem implicações em outras propriedades de objetos de aplicação. Como resultado, se alterar esta propriedade poderá ter de alterar outros imóveis primeiro.

Consulte a tabela seguinte para as diferenças de validação de vários imóveis para diferentes tipos de conta suportados.

| Propriedade | `AzureADMyOrg` | `AzureADMultipleOrgs` | `AzureADandPersonalMicrosoftAccount` e `PersonalMicrosoftAccount` |
|--------------|---------------|----------------|----------------|
| ID URI de aplicação ( `identifierURIs` )  | Deve ser único no inquilino <br><br> urn:// regimes são apoiados <br><br> Wildcards não são suportados <br><br> As cordas e fragmentos de consulta são suportados <br><br> Comprimento máximo de 255 caracteres <br><br> Sem limite* no número de identifierURIs  | Deve ser globalmente único <br><br> urn:// regimes são apoiados <br><br> Wildcards não são suportados <br><br> As cordas e fragmentos de consulta são suportados <br><br> Comprimento máximo de 255 caracteres <br><br> Sem limite* no número de identifierURIs | Deve ser globalmente único <br><br> urn:// os regimes não são apoiados <br><br> Wildcards, fragmentos e cordas de consulta não são suportados <br><br> Comprimento máximo de 120 caracteres <br><br> Máximo de 50 identifierURIs |
| Certificados ( `keyCredentials` ) | Chave de assinatura simétrica | Chave de assinatura simétrica | Chave de encriptação e assinatura assimétrica | 
| Segredos do cliente `passwordCredentials` () | Sem limite* | Sem limite* | Se o liveSDK estiver ativado: Máximo de 2 segredos de cliente | 
| Redirecionamento URIs `replyURLs` () | Consulte [as restrições e limitações de URL de redirecionamento URI/resposta](reply-url.md) para mais informações. | | | 
| Permissões API `requiredResourceAccess` () | Sem limite* | Sem limite* | Máximo de 50 recursos por aplicação e 30 permissões por recurso (por exemplo, Microsoft Graph). Limite total de 200 por pedido (recursos x permissões). | 
| Âmbitos definidos por esta API ( `oauth2Permissions` ) | Comprimento máximo do nome do âmbito de 120 caracteres <br><br> Sem limite* no número de âmbitos definidos | Comprimento máximo do nome do âmbito de 120 caracteres <br><br> Sem limite* no número de âmbitos definidos |  Comprimento máximo do nome do âmbito de 40 caracteres <br><br> Máximo de 100 âmbitos definidos | 
| Pedidos de cliente autorizados `preAuthorizedApplications` () | Sem limite* | Sem limite* | Máximo total de 500 <br><br> Máximo de 100 aplicações de cliente definidas <br><br> Máximo de 30 âmbitos definidos por cliente | 
| appRoles | Suportado <br> Sem limite* | Suportado <br> Sem limite* | Não suportado | 
| Logout URL | http://localhost é permitido <br><br> Comprimento máximo de 255 caracteres | http://localhost é permitido <br><br> Comprimento máximo de 255 caracteres | <br><br> https://localhost é permitido, http://localhost falha para MSA <br><br> Comprimento máximo de 255 caracteres <br><br> O regime HTTP não é permitido <br><br> Wildcards não são suportados | 

*Existe um limite global de cerca de 1000 itens em todas as propriedades de recolha no objeto da aplicação

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [o registo de candidaturas](app-objects-and-service-principals.md)
- Conheça o [manifesto da Candidatura](reference-app-manifest.md)

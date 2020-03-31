---
title: Diferenças de validação por tipos de conta suportados - plataforma de identidade da Microsoft / Azure
description: Conheça as diferenças de validação de várias propriedades para diferentes tipos de conta suportadas ao registar a sua aplicação com a plataforma de identidade microsoft.
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 10/12/2019
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.openlocfilehash: 2a1507b008903085886f9392f3f4e5461997b6e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128864"
---
# <a name="validation-differences-by-supported-account-types-signinaudience"></a>Diferenças de validação por tipos de conta suportados (signInAudience)

Ao registar uma aplicação com a plataforma de identidade da Microsoft para programadores, é-lhe pedido que selecione quais os tipos de conta que a sua aplicação suporta. No objeto de aplicação e `signInAudience`manifesto, este imóvel é .

As opções incluem:

- *AzureADMyOrg*: Apenas contas no diretório organizacional onde a app está registada (inquilino único)
- *AzureADMultipleOrgs*: Contas em qualquer diretório organizacional (multi-inquilino)
- *AzureADandPersonalMicrosoftAccount*: Contas em qualquer diretório organizacional (multi-inquilino) e contas pessoais da Microsoft (por exemplo, Skype, Xbox e Outlook.com)

Para aplicações registadas, pode encontrar o valor para tipos de conta suportados na secção **Autenticação** de uma aplicação. Você também pode encontrá-lo sob a `signInAudience` propriedade no **Manifesto.**

O valor que seleciona para esta propriedade tem implicações em outras propriedades de objetos de aplicação. Como resultado, se você mudar este imóvel você pode precisar de alterar outras propriedades primeiro.

Consulte a tabela seguinte para as diferenças de validação de vários imóveis para diferentes tipos de conta suportada.

| Propriedade | `AzureADMyOrg` | `AzureADMultipleOrgs` | `AzureADandPersonalMicrosoftAccount` e `PersonalMicrosoftAccount` |
|--------------|---------------|----------------|----------------|
| ID de`identifierURIs`aplicação URI ()  | Deve ser único no inquilino <br><br> urn:// esquemas são apoiados <br><br> Wildcards não são suportados <br><br> Cordas de consulta e fragmentos são suportados <br><br> Comprimento máximo de 255 caracteres <br><br> Sem limite* no número de identificadores  | Deve ser globalmente único <br><br> urn:// esquemas são apoiados <br><br> Wildcards não são suportados <br><br> Cordas de consulta e fragmentos são suportados <br><br> Comprimento máximo de 255 caracteres <br><br> Sem limite* no número de identificadores | Deve ser globalmente único <br><br> urn:// esquemas não são apoiados <br><br> Wildcards, fragmentos e cordas de consulta não são suportados <br><br> Comprimento máximo de 120 caracteres <br><br> Máximo de 50 identificadores |
| Certificados`keyCredentials`() | Chave de assinatura simétrica | Chave de assinatura simétrica | Chave de assinatura de encriptação e assimétrica | 
| Segredos`passwordCredentials`do cliente () | Sem limite* | Sem limite* | Se liveSDK estiver ativado: Máximo de 2 segredos de cliente | 
| Redirecionamento`replyURLs`de URIs () | Consulte [Redirecionamento de restrições e limitações de URL DE URI/resposta](reply-url.md) para mais informações. | | | 
| Permissões API ()`requiredResourceAccess` | Sem limite* | Sem limite* | Máximo de 30 permissões por recurso permitidas (por exemplo, Microsoft Graph) | 
| Âmbitos definidos por`oauth2Permissions`esta API ( ) | Comprimento máximo de nome de âmbito de 120 caracteres <br><br> Sem limite* no número de âmbitos definidos | Comprimento máximo de nome de âmbito de 120 caracteres <br><br> Sem limite* no número de âmbitos definidos |  Comprimento máximo de nome de âmbito de 40 caracteres <br><br> Máximo de 100 âmbitos definidos | 
| Pedidos de cliente`preautorizedApplications`autorizados () | Sem limite* | Sem limite* | Máximo total de 500 <br><br> Máximo de 100 aplicações de clientes definidas <br><br> Máximo de 30 âmbitos definidos por cliente | 
| appRoles | Suportado <br> Sem limite* | Suportado <br> Sem limite* | Não suportado | 
| Logout URL | http://localhosté permitido <br><br> Comprimento máximo de 255 caracteres | http://localhosté permitido <br><br> Comprimento máximo de 255 caracteres | <br><br> https://localhosté permitido, http://localhost falha para MSA <br><br> Comprimento máximo de 255 caracteres <br><br> Esquema HTTP não é permitido <br><br> Wildcards não são suportados | 

*Existe um limite global de cerca de 1000 itens em todas as propriedades de coleção no objeto da aplicação

## <a name="next-steps"></a>Passos seguintes

- Conheça o [registo de candidaturas](app-objects-and-service-principals.md)
- Conheça o manifesto de [Candidatura](reference-app-manifest.md)

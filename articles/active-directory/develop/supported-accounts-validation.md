---
title: Diferenças de validação por tipos de conta com suporte (SignInAudience)-plataforma de identidade da Microsoft/Azure Active Directory
description: Saiba mais sobre as diferenças de validação de várias propriedades para diferentes tipos de conta com suporte ao registrar seu aplicativo com a plataforma de identidade da Microsoft.
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 10/12/2019
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3bea6e3ea93e8b630970a8d86fc246eddaf3f56c
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72392608"
---
# <a name="validation-differences-by-supported-account-types-signinaudience"></a>Diferenças de validação por tipos de conta com suporte (signInAudience)

Ao registrar um aplicativo com a plataforma de identidade da Microsoft para desenvolvedores, você será solicitado a selecionar a quais tipos de conta o aplicativo dá suporte. No objeto de aplicativo e no manifesto, essa propriedade é `signInAudience`.

As opções incluem o seguinte:

- *AzureADMyOrg*: somente as contas no diretório organizacional em que o aplicativo está registrado (locatário único)
- *AzureADMultipleOrgs*: contas em qualquer diretório organizacional (multilocatário)
- *AzureADandPersonalMicrosoftAccount*: contas em qualquer diretório organizacional (multilocatário) e contas pessoais da Microsoft (por exemplo, Skype, Xbox e Outlook.com)

Para aplicativos registrados, você pode encontrar o valor dos tipos de conta com suporte na seção de **autenticação** de um aplicativo. Você também pode encontrá-lo na propriedade `signInAudience` no **manifesto**.

O valor selecionado para essa propriedade tem implicações em outras propriedades de objeto de aplicativo. Como resultado, se você alterar essa propriedade, talvez seja necessário alterar as outras propriedades primeiro.

Consulte a tabela a seguir para obter as diferenças de validação de várias propriedades para diferentes tipos de conta com suporte.

| Propriedade | `AzureADMyOrg` | `AzureADMultipleOrgs`  | `AzureADandPersonalMicrosoftAccount` |
|--------------|---------------|----------------|----------------|
| URI da ID do aplicativo (`identifierURIs`)  | Deve ser exclusivo no locatário <br><br> Há suporte para esquemas urn:// <br><br> Não há suporte para curingas <br><br> Há suporte para cadeias de caracteres de consulta e fragmentos <br><br> Comprimento máximo de 255 caracteres <br><br> Sem limite * no número de identifierURIs  | Deve ser globalmente exclusivo <br><br> Há suporte para esquemas urn:// <br><br> Não há suporte para curingas <br><br> Há suporte para cadeias de caracteres de consulta e fragmentos <br><br> Comprimento máximo de 255 caracteres <br><br> Sem limite * no número de identifierURIs | Deve ser globalmente exclusivo <br><br> Não há suporte para esquemas urn:// <br><br> Não há suporte para curingas, fragmentos e cadeias de consulta <br><br> Comprimento máximo de 120 caracteres <br><br> Máximo de 50 identifierURIs |
| Certificados (`keyCredentials`) | Chave de assinatura simétrica | Chave de assinatura simétrica | Criptografia e chave de assinatura assimétrica | 
| Segredos do cliente (`passwordCredentials`) | Sem limite * | Sem limite * | Se liveSDK estiver habilitado: máximo de 2 segredos de cliente | 
| Redirecionar URIs (`replyURLs`) | Confira [restrições de redirecionamento de URI/URL de resposta e limitações](reply-url.md) para obter mais informações. | | | 
| Permissões de API (`requiredResourceAccess`) | Sem limite * | Sem limite * | Máximo de 30 permissões por recurso permitido (por exemplo, Microsoft Graph) | 
| Escopos definidos por esta API (`oauth2Permissions`) | Comprimento máximo do nome de escopo de 120 caracteres <br><br> Sem limite * no número de escopos definidos | Comprimento máximo do nome de escopo de 120 caracteres <br><br> Sem limite * no número de escopos definidos |  Comprimento máximo do nome de escopo de 40 caracteres <br><br> Máximo de 100 escopos definidos | 
| Aplicativos cliente autorizados (`preautorizedApplications`) | Sem limite * | Sem limite * | Máximo total de 500 <br><br> Máximo de 100 aplicativos cliente definidos <br><br> Máximo de 30 escopos definidos por cliente | 
| appRoles | Suportadas <br> Sem limite * | Suportadas <br> Sem limite * | Não suportado | 
| URL de logout | http://localhost é permitido <br><br> Comprimento máximo de 255 caracteres | http://localhost é permitido <br><br> Comprimento máximo de 255 caracteres | <br><br> https://localhost é permitido, http://localhost falha para MSA <br><br> Comprimento máximo de 255 caracteres <br><br> O esquema HTTP não é permitido <br><br> Não há suporte para curingas | 

\* Há um limite global de cerca de 1000 itens em todas as propriedades de coleção no objeto de aplicativo

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o [registro do aplicativo](app-objects-and-service-principals.md)
- Saiba mais sobre o [manifesto do aplicativo](reference-app-manifest.md)

---
title: incluir ficheiro
description: incluir ficheiros para o cenário de cliente confidencial páginas (daemon, aplicação Web, Web API) de destino
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/18/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 9ee7422b372993d60c629524eb036b9678e5776c
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67184441"
---
## <a name="registration-of-secrets-or-certificates"></a>Registo de segredos ou certificados

Como para qualquer aplicativo de cliente confidencial, terá de registar um certificado ou um segredo. Pode registar os segredos da aplicação através da experiência interativa no [portal do Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview), ou utilizando as ferramentas da linha de comandos (como o PowerShell)

### <a name="registering-client-secrets-using-the-application-registration-portal"></a>Registar os segredos de cliente com o portal de registo de aplicação

A gestão de credenciais de cliente ocorre no **certificados e segredos** página para uma aplicação:

![image](../articles/active-directory/develop/media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets-expanded.png)

- o segredo de aplicação (também o segredo de cliente com nome) é gerado pelo Azure AD, durante o registo da aplicação cliente confidencial. Esta geração acontece quando seleciona **novo segredo do cliente**. Nessa altura, tem de copiar a cadeia de caracteres secreta na área de transferência para utilização na sua aplicação, antes de selecionar **guardar**. Esta cadeia não ser apresentada mais.
- o certificado é carregado no aplicativo registo com o **carregar certificado** botão

Para obter detalhes, consulte [início rápido: Configurar uma aplicação de cliente para aceder a web APIs | Adicionar credenciais ao seu aplicativo](../articles/active-directory/develop/quickstart-configure-app-access-web-apis.md#add-credentials-to-your-web-application)

### <a name="registering-client-secrets-using-powershell"></a>Registar os segredos de cliente com o PowerShell

Em alternativa, pode registar a sua aplicação com o Azure AD com as ferramentas da linha de comandos. O [active-directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) exemplo mostra como registar um segredo de aplicação ou um certificado com uma aplicação do Azure AD:

- Para obter detalhes sobre como registar um segredo de aplicação, consulte [AppCreationScripts/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190)
- Para obter detalhes sobre como registar um certificado com a aplicação, consulte [AppCreationScripts-withCert/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178)
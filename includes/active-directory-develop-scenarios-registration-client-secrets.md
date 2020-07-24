---
title: incluir ficheiro
description: incluir ficheiros para páginas confidenciais de aterragem de cenário de cliente (daemon, web app, web API)
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
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: a3acdbb93dd20f0b89e4f99d64f5f7a30ce40623
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87102915"
---
## <a name="register-secrets-or-certificates"></a>Registar segredos ou certificados

Quanto a qualquer pedido de cliente confidencial, tem de registar um segredo ou certificado. Pode registar os seus segredos de aplicação através da experiência interativa no [portal Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) ou utilizando ferramentas de linha de comando (como o PowerShell).

### <a name="register-client-secrets-by-using-the-application-registration-portal"></a>Registar segredos do cliente utilizando o portal de registo de candidaturas

A gestão das credenciais de clientes ocorre na página **de segredos & certificados** para uma aplicação:

![Certificados & página de segredos](../articles/active-directory/develop/media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets.png)

- Cria um *segredo de cliente* selecionando novo segredo de **cliente** no registo da app no portal Azure. Quando cria um segredo de cliente, _deve_ gravar a corda do segredo antes de navegar para longe do **painel de segredos & Certificados.** A corda do segredo nunca mais é exibida.
- Durante o registo de pedidos, utilize o **botão de certificado upload** para carregar o certificado. A Azure AD suporta apenas certificados que estão diretamente registados no pedido e não seguem cadeias de certificados.

Para mais detalhes, consulte [Quickstart: Configurar uma aplicação do cliente para aceder a APIs web Adicione credenciais à sua aplicação.](../articles/active-directory/develop/quickstart-configure-app-access-web-apis.md#add-credentials-to-your-web-application)

### <a name="register-client-secrets-by-using-powershell"></a>Registar segredos do cliente usando o PowerShell

Em alternativa, pode registar a sua aplicação com Azure AD utilizando ferramentas de linha de comando. A amostra [ativa dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) mostra como registar um segredo de aplicação ou certificado com uma aplicação AD Azure:

- Para obter detalhes sobre como registar um segredo de [aplicação, consulte AppCreationScripts/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190).
- Para obter mais informações sobre como registar um certificado com uma aplicação, consulte [AppCreationScripts-withCert/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178).

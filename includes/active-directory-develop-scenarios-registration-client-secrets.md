---
title: ficheiro de inclusão
description: incluir ficheiros para páginas confidenciais de aterragem de cenário de cliente (daemon, web app, web API)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/14/2020
ms.author: jmprieur
ms.openlocfilehash: 42102f38959911388cefcc141d949e59f24a2c31
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89436477"
---
## <a name="add-a-client-secret-or-certificate"></a>Adicionar um segredo ou certificado ao cliente

Tal como acontece com qualquer aplicação confidencial do cliente, é necessário adicionar um segredo ou certificado para agir como *credenciais* dessa aplicação para que possa autenticar-se como ele próprio, sem interação do utilizador.

Pode adicionar credenciais ao registo da sua aplicação cliente utilizando o [portal Azure](#add-client-credentials-by-using-the-azure-portal) ou utilizando uma ferramenta de linha de comando como [o PowerShell.](#add-client-credentials-by-using-powershell)

### <a name="add-client-credentials-by-using-the-azure-portal"></a>Adicione credenciais de cliente usando o portal Azure

Para adicionar credenciais ao registo de aplicações da sua aplicação de clientes confidenciais, siga os passos em [Quickstart: Registe uma aplicação com a plataforma de identidade da Microsoft](../articles/active-directory/develop/quickstart-register-app.md) para o tipo de credencial que pretende adicionar:

* [Adicione um segredo de cliente](../articles/active-directory/develop/quickstart-register-app.md#add-a-client-secret)
* [Adicione um certificado](../articles/active-directory/develop/quickstart-register-app.md#add-a-certificate)

### <a name="add-client-credentials-by-using-powershell"></a>Adicione credenciais de cliente usando PowerShell

Em alternativa, pode adicionar credenciais quando registar a sua aplicação na plataforma de identidade da Microsoft utilizando o PowerShell.

A amostra de código [activo-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) no GitHub mostra como adicionar um segredo de aplicação ou certificado ao registar uma aplicação:

- Para obter detalhes sobre como adicionar um segredo de **cliente** com PowerShell, consulte [AppCreationScripts/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190).
- Para obter mais informações sobre como adicionar um **certificado** com o PowerShell, consulte [AppCreationScripts-withCert/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178).

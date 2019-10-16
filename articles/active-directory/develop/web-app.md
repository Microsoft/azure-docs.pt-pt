---
title: Aplicativos Web no Azure Active Directory
description: Descreve o que são os aplicativos Web e as noções básicas sobre o fluxo de protocolo, o registro e a expiração do token para esse tipo de aplicativo.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d41cd23d551e4834bf6b94f513e36ff46c1cd45
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72373649"
---
# <a name="web-apps"></a>Aplicações Web

Aplicativos Web são aplicativos que autenticam um usuário em um navegador da Web para um aplicativo Web. Nesse cenário, o aplicativo Web direciona o navegador do usuário para conectá-lo ao Azure AD. O Azure AD retorna uma resposta de entrada por meio do navegador do usuário, que contém declarações sobre o usuário em um token de segurança. Esse cenário oferece suporte a logon usando os protocolos OpenID Connect, SAML 2,0 e WS-Federation.

## <a name="diagram"></a>Diagrama

![Fluxo de autenticação do navegador para o aplicativo Web](./media/authentication-scenarios/web_browser_to_web_api.png)

## <a name="protocol-flow"></a>Fluxo de protocolo

1. Quando um usuário visita o aplicativo e precisa entrar, ele é redirecionado por meio de uma solicitação de entrada para o ponto de extremidade de autenticação no Azure AD.
1. O usuário entra na página de entrada.
1. Se a autenticação for bem-sucedida, o Azure AD criará um token de autenticação e retornará uma resposta de entrada para a URL de resposta do aplicativo que foi configurada no portal do Azure. Para um aplicativo de produção, essa URL de resposta deve ser HTTPS. O token retornado inclui declarações sobre o usuário e o Azure AD exigidos pelo aplicativo para validar o token.
1. O aplicativo valida o token usando uma chave de assinatura pública e informações do emissor disponíveis no documento de metadados de Federação para o Azure AD. Depois que o aplicativo valida o token, ele inicia uma nova sessão com o usuário. Esta sessão permite que o usuário acesse o aplicativo até que ele expire.

## <a name="code-samples"></a>Exemplos de código

Consulte os exemplos de código para cenários de navegador da Web para aplicativos Web. E volte com frequência à medida que novos exemplos são adicionados com frequência.

## <a name="app-registration"></a>Registo da aplicação

Para registrar um aplicativo Web, consulte [registrar um aplicativo](quickstart-register-app.md).

* Locatário único – se você estiver criando um aplicativo apenas para sua organização, ele deverá ser registrado no diretório da sua empresa usando o portal do Azure.
* Multilocatário – se você estiver criando um aplicativo que pode ser usado por usuários fora da sua organização, ele deve ser registrado no diretório da sua empresa, mas também deve ser registrado no diretório de cada organização que usará o aplicativo. Para disponibilizar seu aplicativo em seu diretório, você pode incluir um processo de inscrição para seus clientes que lhes permite consentir com seu aplicativo. Quando eles se inscreverem em seu aplicativo, receberão uma caixa de diálogo que mostra as permissões exigidas pelo aplicativo e, em seguida, a opção de consentimento. Dependendo das permissões necessárias, um administrador na outra organização pode ser solicitado a dar consentimento. Quando o usuário ou administrador consentir, o aplicativo será registrado em seu diretório.

## <a name="token-expiration"></a>Expiração do token

A sessão do usuário expira quando o tempo de vida do token emitido pelo AD do Azure expira. Seu aplicativo pode reduzir esse período de tempo, se desejado, por exemplo, desconectando usuários com base em um período de inatividade. Quando a sessão expirar, o usuário será solicitado a entrar novamente.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [tipos de aplicativos e cenários](app-types.md)
* Saiba mais sobre as [noções básicas de autenticação](v1-authentication-scenarios.md) do Azure AD

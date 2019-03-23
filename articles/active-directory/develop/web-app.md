---
title: Aplicações Web no Azure Active Directory
description: Descreve quais são as aplicações web e as noções básicas sobre fluxo de protocolo, registo e expiração do token para este tipo de aplicação.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0337f3de40e75ada86c0a5265fc99b34f1c2ecae
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58367921"
---
# <a name="web-apps"></a>Web Apps

Aplicações Web são aplicativos que autenticar um usuário num navegador da web a uma aplicação web. Neste cenário, o aplicativo web direciona o navegador do usuário para iniciar sessão-las Azure AD. O Azure AD devolve uma resposta de início de sessão por meio de navegador do usuário, que contém as afirmações sobre o utilizador num token de segurança. Este cenário suporta início de sessão utilizando os protocolos de OpenID Connect, SAML 2.0 e WS-Federation.

## <a name="diagram"></a>Diagrama

![Fluxo de autenticação para o browser para a aplicação web](./media/authentication-scenarios/web_browser_to_web_api.png)

## <a name="protocol-flow"></a>Fluxo de protocolo

1. Quando um usuário visita o aplicativo e tem de iniciar sessão, é redirecionado através de um pedido de início de sessão para o ponto final de autenticação no Azure AD.
1. O utilizador inicia sessão na página de início de sessão.
1. Se a autenticação for bem-sucedida, o Azure AD cria um token de autenticação e devolve uma resposta de início de sessão para o URL de resposta do aplicativo que foi configurada no portal do Azure. Para uma aplicação de produção, este URL de resposta deve ser HTTPS. O token devolvido inclui declarações sobre o utilizador e o Azure AD, que são necessários pelo aplicativo para validar o token.
1. A aplicação valida o token com uma chave pública de assinatura e informações de emissor disponíveis no documento de metadados de Federação para o Azure AD. Depois da aplicação valida o token, inicia uma nova sessão com o utilizador. Esta sessão permite ao utilizador aceder à aplicação até expirar.

## <a name="code-samples"></a>Exemplos de código

Veja os exemplos de código para o navegador da web para cenários de aplicativos web. E, novamente, com frequência, como novos exemplos são adicionados com frequência de verificação.

## <a name="app-registration"></a>Registo de aplicações

Para registar uma aplicação web, consulte [registar uma aplicação com o ponto de final do Azure AD v1.0](quickstart-v1-add-azure-ad-app.md).

* Inquilino único, se estiver a criar uma aplicação apenas para a sua organização, tem de ser registado no diretório da sua empresa com o portal do Azure.
* Multi-inquilino - se estiver criando um aplicativo que pode ser utilizado por utilizadores fora da sua organização, ela deve ser registrada no diretório da sua empresa, mas também tem de estar registrada no diretório de cada organização que irá utilizar a aplicação. Para disponibilizar a aplicação no seu diretório, pode incluir um processo de inscrição para os seus clientes, que permite autorizar a aplicação. Quando se inscrevem no seu aplicativo, serão apresentadas com uma caixa de diálogo que mostra as permissões que a aplicação requer e, em seguida, a opção para dar consentimento. Consoante as permissões necessárias, um administrador da outra organização poderá ser necessário para dar consentimento. Quando o utilizador ou administrador autorizar, o aplicativo está registado no seu diretório.

## <a name="token-expiration"></a>Expiração do token

A sessão do utilizador expira quando expira o tempo de vida do token emitido pelo Azure AD. Seu aplicativo pode reduzir este período de tempo, se assim o desejar, por exemplo, terminar sessão dos utilizadores com base num período de inatividade. Quando a sessão expira, será pedido ao utilizador iniciar sessão novamente.

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre outras [tipos de aplicativos e cenários](app-types.md)
* Saiba mais sobre o Azure AD [Noções básicas de autenticação](authentication-scenarios.md)

---
title: Aplicativos web em Diretório Ativo Azure
description: Descreve quais são as aplicações web e os fundamentos do fluxo de protocolo, registo e expiração simbólica para este tipo de aplicação.
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
ms.openlocfilehash: fe9fb67331340314ebeea3c9e8d34436b9088856
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76699755"
---
# <a name="web-apps"></a>Aplicações Web

As aplicações web são aplicações que autenticam um utilizador num navegador web para uma aplicação web. Neste cenário, a aplicação web direciona o navegador do utilizador para inscrevê-los no Azure AD. A Azure AD devolve uma resposta de entrada através do navegador do utilizador, que contém reclamações sobre o utilizador num token de segurança. Este cenário suporta o início de inscrição utilizando os protocolos OpenID Connect, SAML 2.0 e WS-Federation.

## <a name="diagram"></a>Diagrama

![Fluxo de autenticação para navegador para aplicação web](./media/authentication-scenarios/web_browser_to_web_api.png)

## <a name="protocol-flow"></a>Fluxo de protocolo

1. Quando um utilizador visita a aplicação e precisa de iniciar sessão, são redirecionados através de um pedido de inscrição para o ponto final de autenticação em Azure AD.
1. O utilizador entra na página de inscrição.
1. Se a autenticação for bem sucedida, a Azure AD cria um símbolo de autenticação e devolve uma resposta de entrada ao URL de resposta da aplicação que foi configurado no portal Azure. Para um pedido de produção, este URL de resposta deve ser HTTPS. O token devolvido inclui reclamações sobre o utilizador e a AD Azure que são necessárias pela aplicação para validar o token.
1. A aplicação valida o símbolo utilizando uma chave de assinatura pública e informações emitentes disponíveis no documento de metadados da federação para a AD Azure. Depois de a aplicação validar o token, inicia uma nova sessão com o utilizador. Esta sessão permite ao utilizador aceder à aplicação até ao seu termo.

## <a name="code-samples"></a>Exemplos de código

Consulte as amostras de código para o navegador web para cenários de aplicação web. E, volte frequentemente à medida que novas amostras são adicionadas frequentemente.

## <a name="app-registration"></a>Registo da aplicação

Para registar uma aplicação web, consulte [Registar uma aplicação](quickstart-register-app.md).

* Inquilino único - Se estiver a construir uma candidatura apenas para a sua organização, deve estar registado no diretório da sua empresa utilizando o portal Azure.
* Multi-inquilino - Se estiver a construir uma aplicação que possa ser utilizada por utilizadores fora da sua organização, deve estar registada no diretório da sua empresa, mas também deve ser registada no diretório de cada organização que estará a usar a aplicação. Para disponibilizar a sua candidatura no seu diretório, pode incluir um processo de inscrição para os seus clientes que lhes permita consentir com a sua candidatura. Quando se inscreverem para a sua candidatura, serão apresentados com um diálogo que mostra as permissões que o pedido necessita e, em seguida, a opção de consentir. Dependendo das permissões necessárias, um administrador da outra organização pode ser obrigado a dar o seu consentimento. Quando o utilizador ou administrador consentir, o pedido está registado no seu diretório.

## <a name="token-expiration"></a>Expiração do token

A sessão do utilizador expira quando a vida útil do token emitido pela Azure AD expirar. A sua aplicação pode encurtar este período de tempo, se desejar, como a contratação de utilizadores com base num período de inatividade. Quando a sessão expirar, o utilizador será solicitado a iniciar sessão novamente.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [tipos e cenários](app-types.md) de aplicação
* Conheça os [fundamentos básicos](v1-authentication-scenarios.md) de autenticação da AD Azure

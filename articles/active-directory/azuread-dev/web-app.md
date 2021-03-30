---
title: Aplicativos web em Azure Ative Directory
description: Descreve quais são as aplicações web e o básico no fluxo de protocolo, registo e expiração simbólica para este tipo de aplicação.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: e4a7fb72d40f5db65e8e30264e9d68b2727749e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "80154411"
---
# <a name="web-apps"></a>Web Apps

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

As aplicações web são aplicações que autenticam um utilizador num navegador web para uma aplicação web. Neste cenário, a aplicação web direciona o navegador do utilizador para os inscrever no AZure AD. O Azure AD devolve uma resposta de súmis através do browser do utilizador, que contém reclamações sobre o utilizador num sinal de segurança. Este cenário suporta o início de súps utilizando os protocolos OpenID Connect, SAML 2.0 e WS-Federation.

## <a name="diagram"></a>Diagrama

![Fluxo de autenticação para navegador para aplicação web](./media/authentication-scenarios/web-browser-to-web-api.png)

## <a name="protocol-flow"></a>Fluxo de protocolo

1. Quando um utilizador visita a aplicação e precisa de iniciar sação, é redirecionado através de um pedido de inscrição para o ponto final de autenticação em Azure AD.
1. O utilizador assina na página de inscrição.
1. Se a autenticação for bem sucedida, a Azure AD cria um token de autenticação e devolve uma resposta de inscrição ao URL de Resposta da aplicação que foi configurado no portal Azure. Para um pedido de produção, este URL de resposta deve ser HTTPS. O token devolvido inclui reclamações sobre o utilizador e a AD Azure que são necessárias pela aplicação para validar o token.
1. A aplicação valida o token utilizando uma chave de assinatura pública e informações de emitentes disponíveis no documento de metadados da federação para Azure AD. Depois de a aplicação validar o token, inicia-se uma nova sessão com o utilizador. Esta sessão permite ao utilizador aceder à aplicação até que expire.

## <a name="code-samples"></a>Exemplos de código

Consulte as amostras de código para o navegador web para cenários de aplicações web. E, verifique frequentemente à medida que novas amostras são adicionadas frequentemente.

## <a name="app-registration"></a>Registo de aplicações

Para registar uma aplicação web, consulte [registar uma aplicação.](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)

* Inquilino único - Se estiver a construir uma aplicação apenas para a sua organização, deve estar registada no diretório da sua empresa utilizando o portal Azure.
* Multi-inquilino - Se estiver a construir uma aplicação que pode ser usada por utilizadores fora da sua organização, deve estar registada no diretório da sua empresa, mas também deve estar registada no diretório de cada organização que utilizará a aplicação. Para disponibilizar a sua candidatura no seu diretório, pode incluir um processo de inscrição para os seus clientes que lhes permite consentir a sua aplicação. Quando se inscreverem para a sua aplicação, serão apresentados com um diálogo que mostra as permissões que a aplicação requer e, em seguida, a opção de consentimento. Dependendo das permissões necessárias, um administrador da outra organização pode ser obrigado a dar o seu consentimento. Quando o utilizador ou administrador consente, o pedido é registado no seu diretório.

## <a name="token-expiration"></a>Expiração simbólica

A sessão do utilizador expira quando o tempo de vida útil do símbolo emitido pela Azure AD expirar. A sua aplicação pode encurtar este período de tempo, se desejar, como por exemplo a contratação de utilizadores com base num período de inatividade. Quando a sessão expirar, o utilizador será solicitado a iniciar novamente o sessão.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [tipos e cenários de aplicações](app-types.md)
* Conheça os [fundamentos básicos](v1-authentication-scenarios.md) de autenticação AD do Azure

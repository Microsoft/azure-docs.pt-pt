---
title: incluir ficheiro
description: incluir ficheiro
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: ios
ms.workload: identity
ms.date: 03/20/2019
ms.author: dadobali
ms.custom: include file
ms.openlocfilehash: 971ae8cd44f1b345d3a71b8fa4f256c8f25ef961
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60250466"
---
# <a name="call-the-microsoft-graph-api-from-an-ios-application"></a>Chamar a API do Microsoft Graph a partir de uma aplicação iOS

Este guia mostra como uma aplicação nativa no iOS (Swift) pode chamar as APIs que precisam de tokens de acesso do ponto de extremidade do Microsoft identity platform. O guia explica como obter tokens de acesso e utilizá-los em chamadas para o Microsoft Graph API e outras APIs.

Depois de concluir os exercícios deste guia, seu aplicativo pode chamar uma API protegida a partir de qualquer empresa ou organização que tenha o Azure AD. Seu aplicativo pode fazer chamadas à API protegidas através da utilização de contas pessoais, como o outlook.com, live.com e outros, bem como contas escolares ou profissionais.

## <a name="prerequisites"></a>Pré-requisitos

- XCode versão 10.x é necessária para o exemplo que é criado neste guia. Pode baixar o XCode do [Web site do iTunes](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "URL de transferência do XCode").
- O [Carthage](https://github.com/Carthage/Carthage) Gestor de dependências é necessário para a gestão de pacotes.

## <a name="how-this-guide-works"></a>Como funciona este guia

![Mostra como a aplicação de exemplo gerado por este funciona de tutoriais](media/active-directory-develop-guidedsetup-ios-introduction/iosintro.svg)

Neste guia, o aplicativo de exemplo permite que as aplicações iOS, para consultar o Graph API da Microsoft ou uma API web que aceita tokens do ponto de extremidade de plataforma de identidade Microsoft. Para este cenário, um token é adicionado a pedidos de HTTP utilizando o **autorização** cabeçalho. Aquisição do token e a renovação são processadas pelo Microsoft Authentication Library (MSAL).

### <a name="handle-token-acquisition-for-access-to-protected-web-apis"></a>Lidar com a aquisição do token de acesso a APIs da web protegidos

Depois do utilizador é autenticado, o aplicativo de exemplo recebe um token. O token é utilizado para consultar o Graph API da Microsoft ou uma API web que está protegida pelo ponto de final de plataforma de identidade do Microsoft.

As APIs, como o Microsoft Graph, exigem um token de acesso para permitir o acesso a recursos específicos. Tokens são necessários para ler o perfil de um utilizador, aceder ao calendário do usuário, enviar um e-mail e assim por diante. Seu aplicativo pode solicitar um token de acesso através de MSAL e especificando a âmbitos da API. O token de acesso é adicionado ao HTTP **autorização** cabeçalho para cada chamada feita em relação ao recurso protegido.

A MSAL gerencia a colocação em cache e atualizar os tokens de acesso para si, para que seu aplicativo não precisa.

## <a name="libraries"></a>Bibliotecas

Este guia utiliza a biblioteca do seguinte:

|Biblioteca|Descrição|
|---|---|
|[MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|Pré-visualização biblioteca de autenticação de Microsoft para iOS|

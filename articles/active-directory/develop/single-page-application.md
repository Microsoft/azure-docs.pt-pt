---
title: Aplicativos de página única no Azure Active Directory
description: Descreve o que são aplicativos de página única (SPAs) e os conceitos básicos sobre o fluxo de protocolo, o registro e a expiração do token para esse tipo de aplicativo.
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
ms.openlocfilehash: 8afb226406c02f395c7112d485d4616bfbec140e
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72373855"
---
# <a name="single-page-applications"></a>Aplicativos de página única

Aplicativos de página única (SPAs) normalmente são estruturados como uma camada de apresentação JavaScript (front-end) que é executada no navegador e um back-end de API da Web que é executado em um servidor e implementa a lógica de negócios do aplicativo. Para saber mais sobre a concessão de autorização implícita e ajudá-lo a decidir se ela é adequada para seu cenário de aplicativo, consulte [noções básicas sobre o fluxo de concessão implícita OAuth2 no Azure Active Directory](v1-oauth2-implicit-grant-flow.md).

Nesse cenário, quando o usuário entra, o front-end do JavaScript usa [biblioteca de autenticação do Active Directory para JavaScript (Adal. JS)](https://github.com/AzureAD/azure-activedirectory-library-for-js) e a concessão de autorização implícita para obter um token de ID (id_token) do Azure AD. O token é armazenado em cache e o cliente o anexa à solicitação como o token de portador ao fazer chamadas para seu back-end de API da Web, que é protegido usando o middleware OWIN.

## <a name="diagram"></a>Diagrama

![Diagrama de aplicativo de página única](./media/authentication-scenarios/single_page_app.png)

## <a name="protocol-flow"></a>Fluxo de protocolo

1. O usuário navega para o aplicativo Web.
1. O aplicativo retorna o front-end do JavaScript (camada de apresentação) para o navegador.
1. O usuário inicia a entrada, por exemplo, clicando em um link de entrada. O navegador envia um GET para o ponto de extremidade de autorização do Azure AD para solicitar um token de ID. Essa solicitação inclui a ID do aplicativo e a URL de resposta nos parâmetros de consulta.
1. O Azure AD valida a URL de resposta em relação à URL de resposta registrada que foi configurada no portal do Azure.
1. O usuário entra na página de entrada.
1. Se a autenticação for bem-sucedida, o Azure AD criará um token de ID e o retornará como um fragmento de URL (#) para a URL de resposta do aplicativo. Para um aplicativo de produção, essa URL de resposta deve ser HTTPS. O token retornado inclui declarações sobre o usuário e o Azure AD exigidos pelo aplicativo para validar o token.
1. O código de cliente JavaScript em execução no navegador extrai o token da resposta a ser usada na proteção de chamadas para o back-end da API Web do aplicativo.
1. O navegador chama o back-end da API Web do aplicativo com o token de ID no cabeçalho de autorização. O serviço de autenticação do Azure AD emite um token de ID que pode ser usado como um token de portador se o recurso for o mesmo que a ID do cliente (nesse caso, isso é verdadeiro, pois a API da Web é o próprio back-end do aplicativo).

## <a name="code-samples"></a>Exemplos de código

Consulte os [exemplos de código para cenários de aplicativo de página única](sample-v1-code.md#single-page-applications). Lembre-se de verificar com frequência conforme novas amostras são adicionadas com frequência.

## <a name="app-registration"></a>Registo da aplicação

* Locatário único – se você estiver criando um aplicativo apenas para sua organização, ele deverá ser registrado no diretório da sua empresa usando o portal do Azure.
* Multilocatário – se você estiver criando um aplicativo que pode ser usado por usuários fora da sua organização, ele deve ser registrado no diretório da sua empresa, mas também deve ser registrado no diretório de cada organização que usará o aplicativo. Para disponibilizar seu aplicativo em seu diretório, você pode incluir um processo de inscrição para seus clientes que lhes permite consentir com seu aplicativo. Quando eles se inscreverem em seu aplicativo, receberão uma caixa de diálogo que mostra as permissões exigidas pelo aplicativo e, em seguida, a opção de consentimento. Dependendo das permissões necessárias, um administrador na outra organização pode ser solicitado a dar consentimento. Quando o usuário ou administrador consentir, o aplicativo será registrado em seu diretório.

Depois de registrar o aplicativo, ele deve ser configurado para usar o protocolo de concessão implícita do OAuth 2,0. Por padrão, esse protocolo está desabilitado para aplicativos. Para habilitar o protocolo de concessão implícita OAuth2 para seu aplicativo, edite seu manifesto do aplicativo no portal do Azure e defina o valor "oauth2AllowImplicitFlow" como true. Para obter mais informações, consulte [manifesto do aplicativo](reference-app-manifest.md).

## <a name="token-expiration"></a>Expiração do token

O uso do ADAL. js ajuda com:

* Atualizando um token expirado
* Solicitando um token de acesso para chamar um recurso da API Web

Após uma autenticação bem-sucedida, o Azure AD grava um cookie no navegador do usuário para estabelecer uma sessão. Observe que a sessão existe entre o usuário e o Azure AD (não entre o usuário e o aplicativo Web). Quando um token expira, o ADAL. js usa essa sessão para obter silenciosamente outro token. O ADAL. js usa um iFrame oculto para enviar e receber a solicitação usando o protocolo de concessão implícita OAuth. O ADAL. js também pode usar esse mesmo mecanismo para obter silenciosamente tokens de acesso para outros recursos da API Web que o aplicativo chama, contanto que esses recursos ofereçam suporte ao compartilhamento de recursos entre origens (CORS), sejam registrados no diretório do usuário e qualquer consentimento necessário foi fornecido pelo usuário durante a entrada.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [tipos de aplicativos e cenários](app-types.md)
* Saiba mais sobre as [noções básicas de autenticação](v1-authentication-scenarios.md) do Azure AD

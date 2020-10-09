---
title: Configuração de aplicação do cliente (MSAL) Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre opções de configuração para aplicações de cliente público e confidencial usando a Microsoft Authentication Library (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/27/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: b4595a63613afa3c6fef2fa2a85647d8b70b1388
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "81534470"
---
# <a name="application-configuration-options"></a>Opções de configuração de aplicações

No seu código, inicializa uma nova aplicação de cliente público ou confidencial (ou user-agent para MSAL.js) para autenticar e adquirir fichas. Pode definir uma série de opções de configuração quando rubricar a aplicação do cliente na Microsoft Authentication Library (MSAL). Estas opções enquadram-se em dois grupos:

- Opções de inscrição, incluindo:
    - [Autoridade](#authority) (composta pela [instância](#cloud-instance) do fornecedor de identidade e [público](#application-audience) de inscrição para a app, e possivelmente a identificação do inquilino).
    - [Identificação do cliente.](#client-id)
    - [Redirecionar URI](#redirect-uri).
    - [Segredo do cliente](#client-secret) (para aplicações confidenciais de clientes).
- [Opções de registo](#logging), incluindo o nível de registo, o controlo de dados pessoais e o nome do componente utilizando a biblioteca.

## <a name="authority"></a>Autoridade

A autoridade é uma URL que indica um diretório do qual a MSAL pode solicitar fichas. As autoridades comuns são:

- https \: //login.microsoftonline.com/ \<tenant\> /, onde &lt; o inquilino é o inquilino &gt; ID do inquilino Azure Ative Directory (Azure AD) ou um domínio associado a este inquilino AD Azure. Usado apenas para assinar em utilizadores de uma organização específica.
- https \: //login.microsoftonline.com/common/. Costumava assinar em utilizadores com contas de trabalho e escola ou contas pessoais da Microsoft.
- https \: //login.microsoftonline.com/organizations/. Costumava assinar em utilizadores com contas de trabalho e escola.
- https \: //login.microsoftonline.com/consumers/. Usado para assinar em utilizadores apenas com contas pessoais da Microsoft (anteriormente conhecidas como contas de ID do Windows Live).

A definição de autoridade tem de ser consistente com o que está declarado no portal de registo de candidaturas.

A URL da autoridade é composta pelo exemplo e pelo público.

A autoridade pode ser:
- Uma autoridade de nuvem Azure.
- Uma autoridade Azure AD B2C. Consulte [as especificidades do B2C](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics).
- Uma autoridade de Serviços da Federação de Diretórios Ativos (AD FS). Consulte [o suporte da AD FS](https://aka.ms/msal-net-adfs-support).

As autoridades da nuvem AZure AD têm duas partes:
- A instância do fornecedor *de* identidade
- O *público* de inscrição para a app

O caso e o público podem ser concatenados e fornecidos como URL de autoridade. Em versões de MSAL.NET mais cedo que o MSAL 3. *x,* você tinha que compor a autoridade por si mesmo, com base na nuvem que queria atingir e o público de inscrição.  Este diagrama mostra como o URL da autoridade é composto:

![Como a URL de autoridade é composta](media/msal-client-application-configuration/authority.png)

## <a name="cloud-instance"></a>Caso de nuvem

O *caso* é usado para especificar se a sua aplicação está a assinar utilizadores da nuvem pública Azure ou a partir de nuvens nacionais. Utilizando o MSAL no seu código, pode definir a instância da nuvem Azure usando uma enumeração ou passando o URL para a [instância de nuvem nacional](authentication-national-cloud.md#azure-ad-authentication-endpoints) como membro `Instance` (se você sabe).

MSAL.NET lançará uma exceção explícita se ambos `Instance` e `AzureCloudInstance` forem especificados.

Se não especificar um caso, a sua aplicação terá como alvo a instância de nuvem pública Azure (a instância de `https://login.onmicrosoftonline.com` URL).

## <a name="application-audience"></a>Público de candidaturas

O público de inscrição depende das necessidades do negócio para a sua aplicação:
- Se você é um desenvolvedor de linha de negócios (LOB), você provavelmente irá produzir uma aplicação de inquilino único que será usada apenas na sua organização. Nesse caso, você precisa especificar a organização, quer pelo seu ID de inquilino (o ID do seu exemplo AD Azure) ou por um nome de domínio associado à instância AD Azure.
- Se você é um ISV, você pode querer assinar em utilizadores com o seu trabalho e contas escolares em qualquer organização ou em algumas organizações (app multitenant). Mas também pode querer que os utilizadores entrem com as suas contas pessoais da Microsoft.

### <a name="how-to-specify-the-audience-in-your-codeconfiguration"></a>Como especificar o público no seu código/configuração

Utilizando o MSAL no seu código, especifique o público utilizando um dos seguintes valores:
- A enumeração do público da autoridade AD do Azure
- A iD do inquilino, que pode ser:
  - Um GUID (o ID do seu exemplo AD Azure), para aplicações de inquilino único
  - Um nome de domínio associado à sua instância AD Azure (também para aplicações de inquilino único)
- Um desses espaços reservados como um inquilino ID no lugar da lista de audiências da autoridade AD Azure:
    - `organizations` para uma aplicação multitenant
    - `consumers` para assinar nos utilizadores apenas com as suas contas pessoais
    - `common` para assinar nos utilizadores com o seu trabalho e contas escolares ou as suas contas pessoais da Microsoft

A MSAL lançará uma exceção significativa se especificar tanto o público da autoridade AD AZure como o ID do inquilino.

Se não especificar um público, a sua aplicação terá como alvo a Azure AD e as contas pessoais da Microsoft como público. (Ou seja, vai comportar-se como se `common` fosse especificado.)

### <a name="effective-audience"></a>Público eficaz

O público eficaz para a sua aplicação será o mínimo (se houver um cruzamento) do público que definiu na sua app e do público especificado no registo da aplicação. De facto, a experiência de [registos](https://aka.ms/appregistrations) da App permite especificar o público (os tipos de conta suportados) para a aplicação. Para obter mais informações, consulte [Quickstart: Registe uma aplicação com a plataforma de identidade microsoft](quickstart-register-app.md).

Atualmente, a única maneira de obter uma aplicação para assinar em utilizadores apenas com contas pessoais da Microsoft é configurar ambas as definições:
- Desaprote o público de registo de aplicações para `Work and school accounts and personal accounts` .
- Desa estade o público no seu código/configuração para `AadAuthorityAudience.PersonalMicrosoftAccount` (ou `TenantID` ="consumidores").

## <a name="client-id"></a>ID de Cliente

O ID do cliente é o ID de aplicação único (cliente) atribuído à sua aplicação pela Azure AD quando a aplicação foi registada.

## <a name="redirect-uri"></a>URI de Redirecionamento

O URI redirecionado é o URI para o qual o fornecedor de identidade enviará os tokens de segurança de volta.

### <a name="redirect-uri-for-public-client-apps"></a>Redirecionar URI para aplicações de clientes públicos

Se você é um desenvolvedor de aplicativos de cliente público que está usando MSAL:
- Você gostaria de usar `.WithDefaultRedirectUri()` em aplicações de desktop ou UWP (MSAL.NET 4.1+). Este método definirá a propriedade uri redirecionado da aplicação do cliente público para o uri recomendado padrão para aplicações de cliente público.

  Plataforma  | URI de Redirecionamento
  ---------  | --------------
  Aplicativo de ambiente de trabalho (.NET FW) | `https://login.microsoftonline.com/common/oauth2/nativeclient`
  UWP | valor de `WebAuthenticationBroker.GetCurrentApplicationCallbackUri()` . Isto permite ao SSO com o navegador, definindo o valor para o resultado do WebAuthenticationBroker.GetCurrentApplicationCallbackUri() que precisa de registar
  .NET Core | `https://localhost`. Isto permite ao utilizador utilizar o navegador do sistema para autenticação interativa uma vez que .NET Core não tem um UI para a vista web incorporada no momento.

- Não precisa de adicionar um URI de redirecionamento se estiver a construir uma aplicação Xamarin Android e iOS que não suporta corretor (o URI de redirecionamento está automaticamente definido `msal{ClientId}://auth` para Xamarin Android e iOS

- Você precisa configurar o URI redirecionamento em [registos de aplicações:](https://aka.ms/appregistrations)

   ![Redirecionar URI em registos de aplicações](media/msal-client-application-configuration/redirect-uri.png)

Pode anular o URI de redirecionamento utilizando a `RedirectUri` propriedade (por exemplo, se utilizar corretores). Aqui estão alguns exemplos de URIs redirecionados para este cenário:

- `RedirectUriOnAndroid` = "msauth-5a434691-ccb2-4fd1-b97b-b64bcfbc03fc://com.microsoft.identity.client.sample";
- `RedirectUriOnIos` = "msauth". {Bundle.ID}://auth";

Para mais detalhes sobre iOS, consulte [aplicações do iOS migrando que utilizam o Microsoft Authenticator de ADAL.NET para MSAL.NET](msal-net-migration-ios-broker.md) e [alavancando o corretor no iOS](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS).
Para mais detalhes do Android, consulte [o auth Brokered no Android.](brokered-auth.md)

### <a name="redirect-uri-for-confidential-client-apps"></a>Redirecionar URI para aplicações confidenciais de clientes

Para aplicações web, o URI de redirecionamento (ou resposta URI) é o URI que a Azure AD utilizará para enviar o símbolo de volta para a aplicação. Este URI pode ser o URL da aplicação web/web API se a aplicação confidencial for uma delas. O URI de redirecionamento precisa de ser registado no registo de aplicações. Este registo é especialmente importante quando implementa uma aplicação que inicialmente testou localmente. Em seguida, é necessário adicionar o URL de resposta da aplicação implementada no portal de registo de aplicações.

Para aplicações daemon, você não precisa especificar um URI redirecionado.

## <a name="client-secret"></a>Segredo do cliente

Esta opção especifica o segredo do cliente para a aplicação confidencial do cliente. Este segredo (palavra-passe da aplicação) é fornecido pelo portal de registo de aplicações ou fornecido ao Azure AD durante o registo de aplicações com o PowerShell AzureAD, PowerShell AzureRM ou Azure CLI.

## <a name="logging"></a>Registo

As outras opções de configuração permitem registar e resolver problemas. Consulte o artigo ['Registar'](msal-logging.md) para obter mais informações sobre como usá-los.

## <a name="next-steps"></a>Passos seguintes

Saiba como [instantânear as aplicações do cliente utilizando MSAL.NET](msal-net-initializing-client-applications.md).
Saiba como [instantânear as aplicações do cliente utilizando MSAL.js](msal-js-initializing-client-applications.md).

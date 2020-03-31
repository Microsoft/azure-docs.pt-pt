---
title: Configuração de aplicação do cliente (MSAL) / Azure
titleSuffix: Microsoft identity platform
description: Conheça as opções de configuração para aplicações de clientes públicos e clientes confidenciais utilizando a Microsoft Authentication Library (MSAL).
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
ms.openlocfilehash: 0262d22ae00456ce06cb8efbf995f1a093b20043
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262896"
---
# <a name="application-configuration-options"></a>Opções de configuração de aplicação

No seu código, ininicia uma nova aplicação de cliente pública ou confidencial (ou utilizador-agente da MSAL.js) para autenticar e adquirir fichas. Pode definir várias opções de configuração quando rubrica rindo a aplicação do cliente na Microsoft Authentication Library (MSAL). Estas opções enquadram-se em dois grupos:

- Opções de registo, incluindo:
    - [Autoridade](#authority) (composta pela [instância](#cloud-instance) do fornecedor de identidade e [audiência](#application-audience) de inscrição para a app, e possivelmente o ID do inquilino).
    - [ID do cliente](#client-id).
    - [Redirecione o URI](#redirect-uri).
    - [Segredo do cliente](#client-secret) (para aplicações confidenciais de clientes).
- [Opções](#logging)de registo , incluindo nível de registo, controlo de dados pessoais e o nome do componente utilizando a biblioteca.

## <a name="authority"></a>Autoridade

A autoridade é um URL que indica um diretório que a MSAL pode solicitar fichas. As autoridades comuns são:

- https/login.microsoftonline.com/\:\<inquilino\>/, &lt;&gt; onde o inquilino é o inquilino iD do inquilino Azure Ative Directory (Azure AD) inquilino ou um domínio associado a este inquilino da AD Azure. Usado apenas para assinar em utilizadores de uma organização específica.
- https\:/login.microsoftonline.com/common/. Usado para assinar em utilizadores com contas de trabalho e escola ou contas pessoais da Microsoft.
- https\:/login.microsoftonline.com/organizations/. Costumava inscrever utentes com contas de trabalho e escola.
- https\:/login.microsoftonline.com/consumers/. Usado para iniciar sessão em utilizadores com apenas contas pessoais da Microsoft (anteriormente conhecidas como contas de ID do Windows Live).

A definição de autoridade tem de ser consistente com o que está declarado no portal de registo de candidaturas.

O URL da autoridade é composto pelo caso e pelo público.

A autoridade pode ser:
- Uma autoridade de nuvem azure.
- Uma autoridade Azure AD B2C. Consulte [as especificidades do B2C](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics).
- Uma autoridade de Serviços da Federação de Diretórios Ativos (AD FS). Consulte [o suporte AD FS](https://aka.ms/msal-net-adfs-support).

As autoridades da nuvem Azure AD têm duas partes:
- A *instância* do fornecedor de identidade
- O *público* de inscrição para a app

O exemplo e o público podem ser concatenados e fornecidos como URL de autoridade. Em versões de MSAL.NET mais cedo do que mSAL 3. *x,* você tinha que compor a autoridade por si mesmo, com base na nuvem que queria atingir e no público de inscrição.  Este diagrama mostra como o URL da autoridade é composto:

![Como o URL de autoridade é composto](media/msal-client-application-configuration/authority.png)

## <a name="cloud-instance"></a>Instância de nuvem

A *instância* é usada para especificar se a sua aplicação está a contratar utilizadores a partir da nuvem pública do Azure ou de nuvens nacionais. Utilizando o MSAL no seu código, pode definir a instância de nuvem Azure utilizando uma `Instance` enumeração ou passando o URL para a instância de nuvem [nacional](authentication-national-cloud.md#azure-ad-authentication-endpoints) como membro (se o souber).

MSAL.NET lançará uma exceção explícita se ambos `Instance` e `AzureCloudInstance` forem especificados.

Se não especificar uma instância, a sua aplicação terá como alvo `https://login.onmicrosoftonline.com`a instância de nuvem pública Azure (a instância de URL).

## <a name="application-audience"></a>Audiência de candidatura

O público que entra depende das necessidades do negócio para a sua aplicação:
- Se você é um desenvolvedor de uma linha de negócios (LOB), você provavelmente produzirá uma aplicação de inquilino único que será usada apenas na sua organização. Nesse caso, é necessário especificar a organização, quer pelo seu ID de inquilino (a identificação da sua instância AD Azure) quer por um nome de domínio associado à instância Azure AD.
- Se você é um ISV, você pode querer assinar em utilizadores com seu trabalho e contas escolares em qualquer organização ou em algumas organizações (app multitenant). Mas também pode querer que os utilizadores inscrevam-se nas suas contas pessoais da Microsoft.

### <a name="how-to-specify-the-audience-in-your-codeconfiguration"></a>Como especificar o público no seu código/configuração

Utilizando o MSAL no seu código, especifice o público utilizando um dos seguintes valores:
- A enumeração do público da autoridade da AD Azure
- A identificação do inquilino, que pode ser:
  - Um GUID (a identificação do seu AD Azure), para pedidos de inquilino único
  - Um nome de domínio associado à sua instância Azure AD (também para pedidos de inquilino único)
- Um destes espaços reservados como identificação de inquilino no lugar da lista de audiências da autoridade da AD Azure:
    - `organizations`para uma aplicação multiarrendatária
    - `consumers`para assinar em utilizadores apenas com as suas contas pessoais
    - `common`para assinar em utilizadores com as suas contas de trabalho e escola ou as suas contas pessoais da Microsoft

A MSAL lançará uma exceção significativa se especificar tanto o público da autoridade da AD Azure como a identificação do inquilino.

Se não especificar um público, a sua aplicação terá como alvo contas Azure AD e pessoal da Microsoft como um público. (Isto é, comportar-se-á como se `common` tivesse sido especificado.)

### <a name="effective-audience"></a>Público eficaz

O público eficaz para a sua aplicação será o mínimo (se houver uma intersecção) do público que você definir na sua app e o público especificado no registo da aplicação. De facto, a experiência de [registos](https://aka.ms/appregistrations) da App permite especificar o público (os tipos de conta suportados) para a aplicação. Para mais informações, consulte [Quickstart: Registe uma aplicação com a plataforma de identidade microsoft](quickstart-register-app.md).

Atualmente, a única maneira de obter uma app para iniciar sessão em utilizadores com apenas contas pessoais da Microsoft é configurar ambas as definições:
- Detete o `Work and school accounts and personal accounts`público de inscrição da aplicação para .
- Desloque o público `AadAuthorityAudience.PersonalMicrosoftAccount` no `TenantID` seu código/configuração para (ou ="consumidores").

## <a name="client-id"></a>ID de Cliente

O ID do cliente é o ID de aplicação única (cliente) atribuído à sua app pela Azure AD quando a aplicação foi registada.

## <a name="redirect-uri"></a>URI de Redirecionamento

O URI redirecionado é o URI para o que o fornecedor de identidade enviará as fichas de segurança de volta.

### <a name="redirect-uri-for-public-client-apps"></a>Redirecione o URI para aplicações de clientes públicos

Se é um desenvolvedor de aplicativos de cliente público que está a usar mSAL:
- Desejaria utilizar `.WithDefaultRedirectUri()` em aplicações de desktop ou UWP (MSAL.NET 4.1+). Este método irá definir a propriedade uri redirecionamento da aplicação do cliente público para o redirecionamento recomendado por predefinição para aplicações de clientes públicos. 

  Plataforma  | URI de Redirecionamento  
  ---------  | --------------
  Aplicativo de ambiente de trabalho (.NET FW) | `https://login.microsoftonline.com/common/oauth2/nativeclient` 
  UWP | valor `WebAuthenticationBroker.GetCurrentApplicationCallbackUri()`de . Isto permite o SSO com o navegador, definindo o valor para o resultado do WebAuthenticationBroker.GetCurrentApplicationCallbackUri() que precisa registar
  .NET Core | `https://localhost`. Isto permite ao utilizador utilizar o navegador do sistema para autenticação interativa, uma vez que o .NET Core não tem um UI para a visualização web incorporada neste momento.

- Você não precisa adicionar um URI redirecionado se você está construindo uma aplicação Xamarin Android e iOS `msal{ClientId}://auth` que não suporta corretor (o URI redirecionado está automaticamente definido para Xamarin Android e iOS

- É necessário configurar o URI redirecionamento nos [registos da App:](https://aka.ms/appregistrations)

   ![Redirecione o URI nos registos da App](media/msal-client-application-configuration/redirect-uri.png)

Pode anular o URI redirecionamento utilizando a `RedirectUri` propriedade (por exemplo, se utilizar corretores). Aqui estão alguns exemplos de URIs redirecionados para este cenário:

- `RedirectUriOnAndroid`= "msauth-5a434691-ccb2-4fd1-b97b-b64bcfbc03fc://com.microsoft.identity.client.sample";
- `RedirectUriOnIos`= "msauth. {Bundle.ID}://auth";

Para mais detalhes do iOS, consulte [aplicações iOS migrate que utilizam](msal-net-migration-ios-broker.md) o Microsoft Authenticator de ADAL.NET para MSAL.NET e [alavancar o corretor no iOS](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS).
Para mais detalhes android, consulte [Brokered auth no Android](brokered-auth.md).

### <a name="redirect-uri-for-confidential-client-apps"></a>Redirecione o URI para aplicações confidenciais de clientes

Para aplicações web, o redirecionamento URI (ou resposta URI) é o URI que o Azure AD utilizará para enviar o token de volta para a aplicação. Este URI pode ser o URL da aplicação web/Web API se a aplicação confidencial for uma delas. O URI redirecionado tem de ser registado no registo da aplicação. Este registo é especialmente importante quando implementa uma aplicação que inicialmente testou localmente. Em seguida, é necessário adicionar o URL de resposta da aplicação implementada no portal de registo de aplicações.

Para aplicações daemon, você não precisa especificar um URI redirecionado.

## <a name="client-secret"></a>Segredo do cliente

Esta opção especifica o segredo do cliente para a aplicação de cliente confidencial. Este segredo (palavra-passe de aplicação) é fornecido pelo portal de registo de aplicações ou fornecido ao Azure AD durante o registo da aplicação com powerShell AzureAD, PowerShell AzureRM ou Azure CLI.

## <a name="logging"></a>Registo

As outras opções de configuração permitem a exploração madeireira e resolução de problemas. Consulte o artigo [da Madeira](msal-logging.md) para obter mais detalhes sobre como usá-los.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [instantaneamente aplicações de clientes utilizando MSAL.NET](msal-net-initializing-client-applications.md).
Saiba mais sobre [aplicações instantâneas de clientes utilizando MSAL.js](msal-js-initializing-client-applications.md).

---
title: Configuração de aplicação de cliente (biblioteca de autenticação da Microsoft) | Azure
description: Saiba mais sobre as opções de configuração de cliente público e confidencial cliente aplicações no Microsoft Authentication Library (MSAL).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/12/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a48eea9fedd2d82f44693d58b31ee0d5c8c288d
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138540"
---
# <a name="application-configuration-options"></a>Opções de configuração de aplicação

Em seu código, inicializar um novo cliente público ou confidencial (ou o agente do usuário para msal) a aplicação para autenticar e adquirir tokens.  Existem várias opções de configuração diferentes que podem ser definidos ao inicializar a aplicação de cliente no MSAL. Estas opções podem ser divididas em dois grupos:

- Opções de registo, incluindo:
    - [Autoridade](#authority) (composto pelo fornecedor de identidade [instância](#cloud-instance) e início de sessão [público-alvo](#application-audience) do aplicativo, e, possivelmente, o ID de inquilino).
    - [ID de cliente](#client-id)
    - [URI de redirecionamento](#redirect-uri)
    - [segredo do cliente](#client-secret) (para aplicações de cliente confidencial).
- [Opções de registo](#logging), incluindo o nível de registo e o nome do componente com a biblioteca de controle de dados pessoais.

## <a name="authority"></a>Autoridade
A autoridade é um URL que indica um diretório que MSAL pode pedir tokens a partir de. Autoridades de comuns são:

- https://login.microsoftonline.com/&lt; inquilino&gt;/, onde &lt;inquilino&gt; é o ID de inquilino do inquilino do Azure AD ou a um domínio associado a este inquilino do Azure AD.  Utilizado apenas para iniciar sessão dos utilizadores de uma organização específica.
- https://login.microsoftonline.com/common/. Utilizado para iniciar sessão dos utilizadores com uma conta pessoal da Microsoft e contas escolar ou profissional.
- https://login.microsoftonline.com/organizations/. Utilizado para iniciar sessão dos utilizadores com contas profissionais e escolares.
- https://login.microsoftonline.com/consumers/. Utilizado para iniciar sessão dos utilizadores com apenas pessoal conta Microsoft (live).

A definição de autoridade tem de ser consistente com o que é declarado no portal de registo de aplicação.

O URL da autoridade é composto pela instância e o público-alvo.

Pode ser a autoridade de:
- uma autoridade de Cloud do Azure Active directory
- uma autoridade do Azure AD B2C. Ver [especificações de B2C](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics).
- uma autoridade ADFS. Ver [ADFS suportar](https://aka.ms/msal-net-adfs-support).

Autoridades de cloud do Azure AD tem duas partes:
- o fornecedor de identidade **instância**
- o início de sessão **público-alvo** para a aplicação

A instância e o público-alvo podem ser concatenados e fornecidos como o URL da autoridade. Nas versões do MSAL.NET antes da MSAL 3.x, tinha que compõem a autoridade por conta própria dependendo da cloud que gostaria de destino e o público-alvo início de sessão.  O diagrama seguinte mostra como o URL da autoridade é composto.

![Autoridade](media/msal-client-application-configuration/authority.png)

## <a name="cloud-instance"></a>Instância de cloud
O **instância** é utilizado para especificar se a aplicação está a iniciar os utilizadores de nuvem pública do Microsoft Azure ou de clouds nacionais. Através de MSAL em seu código, a instância de cloud do Azure pode ser definida com uma enumeração ou ao passar o URL para o [instância de cloud nacional](authentication-national-cloud.md#azure-ad-authentication-endpoints) como o `Instance` membro (se souber).

MSAL.NET lançará uma exceção de explícita, se os dois `Instance` e `AzureCloudInstance` são especificados. 

Se não especificar uma instância, a aplicação destina-se a instância de cloud pública do Azure (a instância do URL `https://login.onmicrosoftonline.com`).

## <a name="application-audience"></a>Público-alvo de aplicação

O público-alvo início de sessão depende das necessidades comerciais para a sua aplicação:
- Se for uma linha de desenvolvedor de negócio (LOB), provavelmente irá produzir um aplicativo de inquilino único, que será utilizado apenas na sua organização. Nesse caso, terá de especificar esta organização What ' s, ao seu ID de inquilino (ID do seu Azure Active Directory) ou um nome de domínio associado a este Azure Active Directory.
- Se for um ISV, pode querer iniciar sessão dos utilizadores com o seu trabalho e contas escolares em qualquer organização, ou para algumas organizações (aplicação multi-inquilino), mas também poderão querer que os utilizadores iniciar sessão com a conta pessoal Microsoft.

### <a name="how-to-specify-the-audience-in-your-codeconfiguration"></a>Como especificar o público-alvo no seu código/configuração
Através de MSAL em seu código, especifique o público-alvo com:
- ambos do Azure AD autoridade público-alvo enumeração. 
- ou o ID de inquilino, que pode ser:
  - um GUID (de acordo com o ID do seu Azure Active Directory), para aplicações de inquilino único
  - um nome de domínio associado com o Azure Active Directory (também para aplicações de inquilino único)
- ou um destes marcadores de posição como um ID de inquilino em vez da enumeração de público-alvo de autoridade do Azure AD:
    - `organizations` para uma aplicação de inquilino de múltiplas
    - `consumers` iniciar sessão dos utilizadores apenas com as suas contas pessoais
    - `common` iniciar sessão dos utilizadores com o seu trabalho e a conta escolar ou a conta pessoal da Microsoft

A MSAL lançará uma exceção de significativa se especificar audiência de autoridade do Azure AD e o ID do inquilino. 

Se não especificar um público-alvo a aplicação destina-se do Azure AD e as contas Microsoft pessoais como um público-alvo (ou seja `common`).

### <a name="effective-audience"></a>Público-alvo em vigor
O público-alvo eficaz para a sua aplicação será o valor mínimo (se existir uma interseção) do público-alvo definido em seu aplicativo e o público-alvo especificado no registo de aplicação. Na verdade, a experiência de registo de aplicação ([registo de aplicações](https://aka.ms/appregistrations)) permite-lhe especificar o público-alvo (tipos de conta suportado) para a aplicação. Consulte [início rápido: Registar uma aplicação com a plataforma de identidade do Microsoft](quickstart-register-app.md) para obter mais informações.

Atualmente, a única forma de obter uma aplicação para iniciar sessão dos utilizadores com apenas contas pessoais da Microsoft é definido:
- definir o público-alvo de registo de aplicação "Trabalho e contas escolares e pessoais contas" e,
- e, defina o público-alvo no seu código / configuração para `AadAuthorityAudience.PersonalMicrosoftAccount` (ou `TenantID `= "os consumidores")

## <a name="client-id"></a>ID de Cliente
O ID de aplicação exclusivo (cliente) atribuído à sua aplicação pelo Azure AD quando a aplicação foi registrada.

## <a name="redirect-uri"></a>URI de Redirecionamento
O URI de redirecionamento é o URI onde o fornecedor de identidade irá enviar os tokens de segurança novamente. 

### <a name="redirect-uri-for-public-client-applications"></a>URI de redirecionamento para aplicativos cliente público
Para desenvolvedores de aplicativos de cliente pública através de MSAL:
- Não é necessário passar o ``RedirectUri`` como é calculada automaticamente pelo MSAL. Este redirecionamento URI é definido para os seguintes valores dependendo da plataforma:

- ``urn:ietf:wg:oauth:2.0:oob`` para todas as plataformas do Windows
- ``msal{ClientId}://auth`` para Xamarin Android e iOS

No entanto, o redirecionamento URI tem de ser configurado no [registo de aplicações](https://aka.ms/appregistrations).

![URI de redirecionamento no portal](media/msal-client-application-configuration/redirect-uri.png)

É possível substituir o URI de redirecionamento utilizando o `RedirectUri` propriedade, por exemplo se usar mediadores. Nesse caso, eis alguns exemplos de URIs de redirecionamento:

- `RedirectUriOnAndroid` = "msauth-5a434691-ccb2-4fd1-b97b-b64bcfbc03fc://com.microsoft.identity.client.sample";
- `RedirectUriOnIos` = $"msauth.{Bundle.ID}://auth";

Para obter detalhes, consulte informações específicas de Android e [informações específicas do iOS](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS)

### <a name="redirect-uri-for-confidential-client-applications"></a>URI de redirecionamento para aplicativos cliente confidencial
Para aplicações web, o URI redirecionamento (ou a URI de resposta) é o URI no qual do Azure AD irá contactar novamente a aplicação com o token. Isso pode ser o URL da aplicação Web / API Web se a confidenciais é um deles.  Este URI de redirecionamento tem de ser registado no registo de aplicações. Isto é especialmente importante quando implanta um aplicativo que inicialmente testado localmente. Em seguida, terá de adicionar o URL de resposta do aplicativo implementado no portal de registo de aplicação.

Para aplicações de daemon, não tem de especificar um URI de redirecionamento.

## <a name="client-secret"></a>Segredo do cliente
O segredo do cliente para a aplicação de cliente confidencial. Este segredo (palavra-passe de aplicação) é fornecido pelo portal de registo de aplicação, ou fornecido para o Azure AD durante o registo de aplicação com o PowerShell AzureAD, PowerShell AzureRM ou da CLI do Azure.

## <a name="logging"></a>Registo
As outras opções de ativar o registo e resolução de problemas. Para obter mais informações, consulte a [registo](msal-logging.md) para obter mais informações sobre como usá-los.

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre [instanciar os aplicativos de cliente com MSAL.NET](msal-net-initializing-client-applications.md).

Saiba mais sobre [instanciar a aplicativos cliente usando a msal](msal-js-initializing-client-applications.md).

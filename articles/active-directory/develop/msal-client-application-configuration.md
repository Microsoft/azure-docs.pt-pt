---
title: Configuração de aplicação de cliente (biblioteca de autenticação da Microsoft) | Azure
description: Saiba mais sobre as opções de configuração de cliente público e confidencial cliente aplicações no Microsoft Authentication Library (MSAL).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
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
ms.openlocfilehash: b5e175a8cdd1622add90bd80df63303fe914ab9c
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/31/2019
ms.locfileid: "66430801"
---
# <a name="application-configuration-options"></a>Opções de configuração de aplicação

Em seu código, inicializar um novo cliente público ou confidencial (ou o agente do usuário para msal) a aplicação para autenticar e adquirir tokens. Pode definir um número de opções de configuração quando inicializa o aplicativo de cliente no Microsoft Authentication Library (MSAL). Estas opções enquadram-se em dois grupos:

- Opções de registo, incluindo:
    - [Autoridade](#authority) (composto pelo fornecedor de identidade [instância](#cloud-instance) e início de sessão [público-alvo](#application-audience) para a aplicação e, possivelmente, o ID de inquilino).
    - [ID de cliente](#client-id).
    - [URI de redirecionamento](#redirect-uri).
    - [segredo do cliente](#client-secret) (para aplicações de cliente confidencial).
- [Opções de registo](#logging), incluindo o nível de registo e o nome do componente com a biblioteca de controle de dados pessoais.

## <a name="authority"></a>autoridade
A autoridade é um URL que indica um diretório que MSAL pode pedir tokens a partir de. Autoridades de comuns são:

- https://login.microsoftonline.com/&lt; inquilino&gt;/, onde &lt;inquilino&gt; é o ID de inquilino do inquilino do Azure Active Directory (Azure AD) ou um domínio associado a este inquilino do Azure AD. Utilizado apenas para iniciar sessão dos utilizadores de uma organização específica.
- https://login.microsoftonline.com/common/. Utilizado para iniciar sessão dos utilizadores com contas Microsoft pessoais e contas escolar ou profissional.
- https://login.microsoftonline.com/organizations/. Utilizado para iniciar sessão dos utilizadores com contas profissionais e escolares.
- https://login.microsoftonline.com/consumers/. Utilizado para iniciar sessão dos utilizadores com apenas contas pessoais da Microsoft (anteriormente conhecidas como contas do Windows Live ID).

A definição de autoridade tem de ser consistente com o que é declarado no portal de registo de aplicação.

O URL da autoridade é composto pela instância e o público-alvo.

Pode ser a autoridade de:
- Uma autoridade de cloud do Azure AD.
- uma autoridade do Azure AD B2C. Ver [especificações de B2C](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics).
- Uma autoridade de serviços de Federação do Active Directory (ADFS). Ver [ADFS suportar](https://aka.ms/msal-net-adfs-support).

Autoridades de cloud do Azure AD tem duas partes:
- o fornecedor de identidade *instância*
- O início de sessão *público-alvo* para a aplicação

A instância e o público-alvo podem ser concatenados e fornecidos como o URL da autoridade. Em versões do MSAL.NET anteriores a MSAL 3. *x*, tinha que compõem a autoridade por conta própria, com base na cloud que gostaria de destino e o público-alvo início de sessão.  Este diagrama mostra como o URL da autoridade é composta por:

![Como o URL da autoridade é composta por](media/msal-client-application-configuration/authority.png)

## <a name="cloud-instance"></a>Instância de cloud
O *instância* é utilizado para especificar se a aplicação está a iniciar os utilizadores de cloud pública do Azure ou de clouds nacionais. Através de MSAL em seu código, pode definir a instância de cloud do Azure com uma enumeração ou ao passar o URL para o [instância de cloud nacional](authentication-national-cloud.md#azure-ad-authentication-endpoints) como o `Instance` membro (se souber).

MSAL.NET lançará uma exceção de explícita, se os dois `Instance` e `AzureCloudInstance` são especificados.

Se não especificar uma instância, a aplicação destina-se a instância de cloud pública do Azure (a instância do URL `https://login.onmicrosoftonline.com`).

## <a name="application-audience"></a>Público-alvo de aplicação

O público-alvo início de sessão depende das necessidades comerciais para a sua aplicação:
- Se tiver uma linha de desenvolvedor de negócio (LOB), provavelmente irá produzir uma aplicação de inquilino único que será utilizada apenas na sua organização. Nesse caso, terá de especificar a organização, através da sua ID do inquilino (ID da sua instância do Azure AD) ou por um nome de domínio associados à instância do Azure AD.
- Se for um ISV, pode querer iniciar sessão dos utilizadores com as suas contas profissionais e escolares em qualquer organização ou em algumas organizações (aplicações multi-inquilino). Mas pode também querer ter utilizadores iniciar sessão com contas pessoais da Microsoft.

### <a name="how-to-specify-the-audience-in-your-codeconfiguration"></a>Como especificar o público-alvo no seu código/configuração
Através de MSAL em seu código, especifique o público-alvo utilizando um dos seguintes valores:
- A enumeração de público-alvo de autoridade do Azure AD
- O ID de inquilino, que pode ser:
  - Um GUID (de acordo com o ID da sua instância do Azure AD), para aplicações de inquilino único
  - Um nome de domínio associado à sua instância do Azure AD (também para aplicações de inquilino único)
- Um destes marcadores de posição como um ID de inquilino em vez da enumeração de público-alvo de autoridade do Azure AD:
    - `organizations` para uma aplicação multi-inquilino
    - `consumers` iniciar sessão dos utilizadores apenas com as suas contas pessoais
    - `common` iniciar sessão dos utilizadores com o seu trabalho e contas escolares ou contas pessoais da Microsoft

A MSAL lançará uma exceção de significativa se especificar o público-alvo de autoridade do Azure AD e o ID do inquilino.

Se não especificar um público-alvo, a aplicação destina-se do Azure AD e as contas Microsoft pessoais como um público-alvo. (Ou seja, ela se comportará como se `common` foram especificadas.)

### <a name="effective-audience"></a>Público-alvo em vigor
O público-alvo eficaz para a sua aplicação será o valor mínimo (se existir uma interseção) do público-alvo definido na sua aplicação e o público-alvo é especificado no registo de aplicações. Na verdade, o [registos das aplicações](https://aka.ms/appregistrations) experiência permite-lhe especificar o público-alvo (os tipos de conta suportado) para a aplicação. Para obter mais informações, consulte [início rápido: Registar uma aplicação com a plataforma de identidade do Microsoft](quickstart-register-app.md).

Atualmente, a única forma de obter uma aplicação para iniciar sessão dos utilizadores com contas Microsoft pessoais é configurar ambas estas definições:
- Definir o público-alvo de registo de aplicação `Work and school accounts and personal accounts`.
- Definir o público-alvo seu código/configuração até `AadAuthorityAudience.PersonalMicrosoftAccount` (ou `TenantID` = "os consumidores").

## <a name="client-id"></a>ID de Cliente
O ID de cliente é o ID de aplicação exclusivo (cliente) atribuído à sua aplicação pelo Azure AD quando a aplicação foi registrada.

## <a name="redirect-uri"></a>URI de redirecionamento
O redirecionamento URI é o URI que o fornecedor de identidade enviará de volta para os tokens de segurança.

### <a name="redirect-uri-for-public-client-apps"></a>URI de redirecionamento para aplicações de cliente público
Se for um programador de aplicações de cliente pública que está a utilizar a MSAL:
- Não precisa passar `RedirectUri` porque ele é calculado automaticamente pelo MSAL. Este redirecionamento URI é definido como um destes valores, dependendo da plataforma:
   - `urn:ietf:wg:oauth:2.0:oob` para todas as plataformas do Windows
   - `msal{ClientId}://auth` para Xamarin Android e iOS

- Tem de configurar o redirecionamento URI no [registos das aplicações](https://aka.ms/appregistrations):

   ![URI de redirecionamento em registos de aplicações](media/msal-client-application-configuration/redirect-uri.png)

Pode substituir o URI de redirecionamento utilizando o `RedirectUri` propriedade (por exemplo, se usar mediadores). Aqui estão alguns exemplos de URIs de redirecionamento para esse cenário:

- `RedirectUriOnAndroid` = "msauth-5a434691-ccb2-4fd1-b97b-b64bcfbc03fc://com.microsoft.identity.client.sample";
- `RedirectUriOnIos` = $"msauth.{Bundle.ID}://auth";

Para obter detalhes, consulte a [documentação para Android e iOS](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS).

### <a name="redirect-uri-for-confidential-client-apps"></a>URI de redirecionamento para aplicações de cliente confidencial
Para aplicações web, o URI de redirecionamento (ou URI de resposta) é o URI que o Azure AD irá utilizar para enviar o token de volta para a aplicação. Isso pode ser o URL da web aplicação/API Web se a aplicação confidencial é um deles. O redirecionamento URI tem de ser registado no registo de aplicações. Este registo é especialmente importante ao implementar uma aplicação que inicialmente tenha testado localmente. Em seguida, terá de adicionar o URL de resposta da aplicação implementada no portal de registo de aplicação.

Para aplicações de daemon, não tem de especificar um URI de redirecionamento.

## <a name="client-secret"></a>Segredo do cliente
Esta opção especifica o segredo do cliente para a aplicação de cliente confidencial. Este segredo (palavra-passe de aplicação) é fornecido pelo portal de registo de aplicação ou fornecido para o Azure AD durante o registo de aplicação com o PowerShell AzureAD, PowerShell AzureRM ou da CLI do Azure.

## <a name="logging"></a>Registo
As outras opções de configuração ativar o registo e resolução de problemas. Consulte a [registo](msal-logging.md) artigo para obter detalhes sobre como usá-los.

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre [instanciar aplicativos cliente usando MSAL.NET](msal-net-initializing-client-applications.md).

Saiba mais sobre [instanciar os aplicativos de cliente através de msal](msal-js-initializing-client-applications.md).

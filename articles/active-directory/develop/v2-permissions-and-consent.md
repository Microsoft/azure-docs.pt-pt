---
title: Escopos, permissões e consentimento da plataforma Microsoft Identity | Microsoft Docs
description: Uma descrição da autorização no ponto de extremidade da plataforma Microsoft Identity, incluindo escopos, permissões e consentimento.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 8f98cbf0-a71d-4e34-babf-e644ad9ff423
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: ryanwi
ms.reviewer: hirsin, jesakowi, jmprieur
ms.custom: fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: edb6d96dfdca63f1bacf45ab0af01d18aafcf302
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73667883"
---
# <a name="permissions-and-consent-in-the-microsoft-identity-platform-endpoint"></a>Permissões e consentimento no ponto de extremidade da plataforma Microsoft Identity

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Os aplicativos que se integram à plataforma Microsoft Identity seguem um modelo de autorização que oferece aos usuários e administradores o controle sobre como os dados podem ser acessados. A implementação do modelo de autorização foi atualizada no ponto de extremidade da plataforma de identidade da Microsoft e altera como um aplicativo deve interagir com a plataforma de identidade da Microsoft. Este artigo aborda os conceitos básicos desse modelo de autorização, incluindo escopos, permissões e consentimento.

> [!NOTE]
> O ponto de extremidade da plataforma Microsoft Identity não oferece suporte a todos os cenários e recursos. Para determinar se você deve usar o ponto de extremidade da plataforma de identidade da Microsoft, leia sobre as [limitações da plataforma de identidade da Microsoft](active-directory-v2-limitations.md).

## <a name="scopes-and-permissions"></a>Escopos e permissões

A plataforma de identidade da Microsoft implementa o protocolo de autorização [OAuth 2,0](active-directory-v2-protocols.md) . O OAuth 2,0 é um método pelo qual um aplicativo de terceiros pode acessar recursos hospedados na Web em nome de um usuário. Qualquer recurso hospedado na Web que se integre com a plataforma de identidade da Microsoft tem um identificador de recurso ou um *URI de ID do aplicativo*. Por exemplo, alguns dos recursos hospedados na Web da Microsoft incluem:

* Microsoft Graph: `https://graph.microsoft.com`
* API de email do Office 365: `https://outlook.office.com`
* Azure AD Graph: `https://graph.windows.net`

> [!NOTE]
> É altamente recomendável que você use Microsoft Graph em vez do Azure AD Graph, a API de email do Office 365, etc.

O mesmo acontece com todos os recursos de terceiros que se integraram à plataforma Microsoft Identity. Qualquer um desses recursos também pode definir um conjunto de permissões que podem ser usadas para dividir a funcionalidade desse recurso em partes menores. Por exemplo, [Microsoft Graph](https://graph.microsoft.com) definiu permissões para realizar as seguintes tarefas, entre outras:

* Ler o calendário de um usuário
* Gravar no calendário de um usuário
* Enviar email como um usuário

Ao definir esses tipos de permissões, o recurso tem um controle refinado sobre seus dados e como a funcionalidade da API é exposta. Um aplicativo de terceiros pode solicitar essas permissões de usuários e administradores, que devem aprovar a solicitação antes que o aplicativo possa acessar dados ou agir em nome de um usuário. Ao dividir a funcionalidade do recurso em conjuntos de permissões menores, aplicativos de terceiros podem ser criados para solicitar apenas as permissões específicas necessárias para executar sua função. Os usuários e administradores podem saber exatamente quais dados o aplicativo tem acesso e podem ter mais certeza de que ele não está se comportando com más intenções. Os desenvolvedores devem sempre obedecer ao conceito de privilégios mínimos, solicitando apenas as permissões necessárias para que seus aplicativos funcionem.

No OAuth 2,0, esses tipos de permissões são chamados de *escopos*. Eles também são chamados de *permissões*. Uma permissão é representada na plataforma de identidade da Microsoft como um valor de cadeia de caracteres. Continuando com o exemplo de Microsoft Graph, o valor da cadeia de caracteres para cada permissão é:

* Ler o calendário de um usuário usando `Calendars.Read`
* Gravar no calendário de um usuário usando `Calendars.ReadWrite`
* Enviar email como um usuário usando o por `Mail.Send`

Um aplicativo geralmente solicita essas permissões especificando os escopos em solicitações para o ponto de extremidade de autorização da plataforma de identidade da Microsoft. No entanto, certas permissões de alto privilégio só podem ser concedidas por meio do consentimento do administrador e solicitadas/concedidas usando o [ponto de extremidade de consentimento](v2-permissions-and-consent.md#admin-restricted-permissions)do Continue a ler para obter mais informações.

## <a name="permission-types"></a>Tipos de permissão

A plataforma de identidade da Microsoft dá suporte a dois tipos de permissões: **permissões delegadas** e **permissões de aplicativo**.

* **As permissões delegadas** são usadas por aplicativos que têm um usuário conectado presente. Para esses aplicativos, o usuário ou um administrador consentirá com as permissões solicitadas pelo aplicativo e o aplicativo será delegado a permissão para atuar como o usuário conectado ao fazer chamadas para o recurso de destino. Algumas permissões delegadas podem ser consentidas por usuários não administrativos, mas algumas permissões com privilégios superiores exigem o [consentimento do administrador](v2-permissions-and-consent.md#admin-restricted-permissions). Para saber quais funções de administrador podem consentir as permissões delegadas, consulte [permissões de função de administrador no Azure ad](../users-groups-roles/directory-assign-admin-roles.md).

* **As permissões de aplicativo** são usadas por aplicativos que são executados sem um usuário conectado presente; por exemplo, aplicativos executados como serviços em segundo plano ou daemons.  As permissões de aplicativo só podem ser [consentidas por um administrador](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

_Permissões efetivas_ são as permissões que seu aplicativo terá ao fazer solicitações para o recurso de destino. É importante entender a diferença entre as permissões delegadas e de aplicativo que seu aplicativo recebe e suas permissões efetivas ao fazer chamadas para o recurso de destino.

- Para permissões delegadas, as _permissões efetivas_ de seu aplicativo serão a interseção menos privilegiada das permissões delegadas que o aplicativo recebeu (via consentimento) e os privilégios do usuário conectado no momento. A aplicação nunca pode ter mais privilégios do que o utilizador com sessão iniciada. Nas organizações, os privilégios do utilizador com sessão iniciada podem ser determinados por uma política ou por associação a uma ou mais funções de administrador. Para saber quais funções de administrador podem consentir as permissões delegadas, consulte [permissões de função de administrador no Azure ad](../users-groups-roles/directory-assign-admin-roles.md).

   Por exemplo, suponha que seu aplicativo tenha recebido a permissão _User. ReadWrite. All_ delegated. Esta permissão concede nominalmente permissão à aplicação para ler e atualizar o perfil de todos os utilizadores de uma organização. Se o utilizador com sessão iniciada for administrador global, a aplicação poderá atualizar o perfil de todos os utilizadores da organização. No entanto, se o usuário conectado não estiver em uma função de administrador, seu aplicativo poderá atualizar somente o perfil do usuário conectado. Não poderá atualizar os perfis dos outros utilizadores da organização porque o utilizador em cujo nome tem permissão para agir não tem esses privilégios.
  
- Para permissões de aplicativo, as _permissões efetivas_ do seu aplicativo serão o nível completo de privilégios implícitos pela permissão. Por exemplo, um aplicativo que tem a permissão _User. ReadWrite. All_ Application pode atualizar o perfil de cada usuário na organização. 

## <a name="openid-connect-scopes"></a>Escopos do OpenID Connect

A implementação da plataforma Microsoft Identity do OpenID Connect tem alguns escopos bem definidos que não se aplicam a um recurso específico: `openid`, `email`, `profile`e `offline_access`. Não há suporte para os escopos `address` e `phone` OpenID Connect.

### <a name="openid"></a>OpenID

Se um aplicativo executar a entrada usando o [OpenID Connect](active-directory-v2-protocols.md), ele deverá solicitar o escopo de `openid`. O escopo de `openid` é mostrado na página de consentimento da conta corporativa como a permissão "conectar você" e na página de consentimento conta Microsoft pessoal como a permissão "exibir seu perfil e conectar-se a aplicativos e serviços usando sua conta Microsoft". Com essa permissão, um aplicativo pode receber um identificador exclusivo para o usuário na forma de declaração de `sub`. Ele também dá ao aplicativo acesso ao ponto de extremidade de UserInfo. O escopo de `openid` pode ser usado no ponto de extremidade de token da plataforma de identidade da Microsoft para adquirir tokens de ID, que podem ser usados pelo aplicativo para autenticação.

### <a name="email"></a>e-mail

O escopo de `email` pode ser usado com o escopo de `openid` e quaisquer outros. Ele dá ao aplicativo acesso ao endereço de email principal do usuário na forma da declaração de `email`. A declaração de `email` será incluída em um token somente se um endereço de email estiver associado à conta de usuário, que nem sempre é o caso. Se ele usar o escopo de `email`, seu aplicativo deverá estar preparado para lidar com um caso no qual a declaração de `email` não existe no token.

### <a name="profile"></a>criar

O escopo de `profile` pode ser usado com o escopo de `openid` e quaisquer outros. Ele dá ao aplicativo acesso a uma quantidade significativa de informações sobre o usuário. As informações que ele pode acessar incluem, mas não se limitam a, o nome do usuário, o sobrenome, o nome de usuário preferencial e a ID de objeto. Para obter uma lista completa das declarações de perfil disponíveis no parâmetro id_tokens para um usuário específico, consulte a [referência`id_tokens`](id-tokens.md).

### <a name="offline_access"></a>offline_access

O [escopo de`offline_access`](https://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) dá ao aplicativo acesso a recursos em nome do usuário por um longo tempo. Na página de consentimento, esse escopo aparece como a permissão "manter o acesso aos dados aos quais você concedeu acesso". Quando um usuário aprova o escopo de `offline_access`, seu aplicativo pode receber tokens de atualização do ponto de extremidade de token da plataforma de identidade da Microsoft. Os tokens de atualização têm vida longa. Seu aplicativo pode obter novos tokens de acesso à medida que os antigos expiram.

Se seu aplicativo não solicitar explicitamente o escopo de `offline_access`, ele não receberá tokens de atualização. Isso significa que, ao resgatar um código de autorização no [fluxo de código de autorização do OAuth 2,0](active-directory-v2-protocols.md), você receberá apenas um token de acesso do ponto de extremidade `/token`. O token de acesso é válido por um curto período de tempo. O token de acesso geralmente expira em uma hora. Nesse ponto, seu aplicativo precisa redirecionar o usuário de volta para o ponto de extremidade `/authorize` para obter um novo código de autorização. Durante esse redirecionamento, dependendo do tipo de aplicativo, o usuário pode precisar inserir suas credenciais novamente ou consentir novamente as permissões. Embora o escopo de `offline_access` seja solicitado automaticamente pelo servidor, o cliente ainda deve solicitá-lo para receber os tokens de atualização.

Para obter mais informações sobre como obter e usar tokens de atualização, consulte a [referência de protocolo de plataforma de identidade da Microsoft](active-directory-v2-protocols.md).

## <a name="requesting-individual-user-consent"></a>Solicitando consentimento de usuário individual

Em uma solicitação de autorização do [OpenID Connect ou do OAuth 2,0](active-directory-v2-protocols.md) , um aplicativo pode solicitar as permissões necessárias usando o parâmetro de consulta `scope`. Por exemplo, quando um usuário entra em um aplicativo, o aplicativo envia uma solicitação como o exemplo a seguir (com quebras de linha adicionadas para legibilidade):

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=
https%3A%2F%2Fgraph.microsoft.com%2Fcalendars.read%20
https%3A%2F%2Fgraph.microsoft.com%2Fmail.send
&state=12345
```

O parâmetro `scope` é uma lista separada por espaços de permissões delegadas que o aplicativo está solicitando. Cada permissão é indicada acrescentando o valor de permissão ao identificador do recurso (o URI da ID do aplicativo). No exemplo de solicitação, o aplicativo precisa de permissão para ler o calendário do usuário e enviar email como o usuário.

Depois que o usuário inserir suas credenciais, o ponto de extremidade da plataforma de identidade da Microsoft verificará se há um registro correspondente de *consentimento do usuário*. Se o usuário não consentiu em nenhuma das permissões solicitadas no passado, nem tem um administrador que consentiu essas permissões em nome de toda a organização, o ponto de extremidade da plataforma de identidade da Microsoft solicita que o usuário conceda as permissões solicitadas.

> [!NOTE]
> Neste momento, o `offline_access` ("manter o acesso aos dados aos quais você concedeu acesso") e `user.read` ("entrar e ler seu perfil") as permissões são incluídas automaticamente no consentimento inicial para um aplicativo.  Essas permissões geralmente são necessárias para a funcionalidade de aplicativo adequada – `offline_access` dá ao aplicativo acesso a tokens de atualização, crítico para aplicativos Web e nativos, enquanto `user.read` dá acesso à declaração de `sub`, permitindo que o cliente ou aplicativo identifique corretamente o usuário ao longo do tempo e acesso a informações rudimentares do usuário.  

![Captura de tela de exemplo que mostra o consentimento da conta corporativa](./media/v2-permissions-and-consent/work_account_consent.png)

Quando o usuário aprova a solicitação de permissão, o consentimento é registrado e o usuário não precisa consentir novamente em entradas subsequentes para o aplicativo.

## <a name="requesting-consent-for-an-entire-tenant"></a>Solicitando consentimento para um locatário inteiro

Geralmente, quando uma organização adquire uma licença ou assinatura para um aplicativo, a organização deseja configurar proativamente o aplicativo para uso por todos os membros da organização. Como parte desse processo, um administrador pode conceder consentimento para o aplicativo agir em nome de qualquer usuário no locatário. Se o administrador conceder consentimento para o locatário inteiro, os usuários da organização não verão uma página de consentimento para o aplicativo.

Para solicitar consentimento para permissões delegadas para todos os usuários em um locatário, seu aplicativo pode usar o ponto de extremidade de consentimento do administrador.

Além disso, os aplicativos devem usar o ponto de extremidade de consentimento do administrador para solicitar permissões de aplicativo.

## <a name="admin-restricted-permissions"></a>Permissões restritas ao administrador

Algumas permissões de alto privilégio no ecossistema da Microsoft podem ser definidas como *restritas ao administrador*. Exemplos desses tipos de permissões incluem o seguinte:

* Ler todos os perfis completos do usuário usando `User.Read.All`
* Gravar dados no diretório de uma organização usando `Directory.ReadWrite.All`
* Ler todos os grupos no diretório de uma organização usando `Groups.Read.All`

Embora um usuário consumidor possa conceder a um aplicativo acesso a esse tipo de dados, os usuários organizacionais estão restritos a conceder acesso ao mesmo conjunto de dados confidenciais da empresa. Se seu aplicativo solicitar acesso a uma dessas permissões de um usuário da organização, o usuário receberá uma mensagem de erro informando que elas não estão autorizadas a dar consentimento às permissões do seu aplicativo.

Se seu aplicativo exigir acesso a escopos restritos ao administrador para organizações, você deverá solicitá-los diretamente de um administrador da empresa, também usando o ponto de extremidade de consentimento do administrador, descrito a seguir.

Se o aplicativo estiver solicitando permissões delegadas de alto privilégio e um administrador conceder essas permissões por meio do ponto de extremidade de consentimento do administrador, o consentimento será concedido para todos os usuários no locatário.

Se o aplicativo estiver solicitando permissões de aplicativo e um administrador conceder essas permissões por meio do ponto de extremidade de consentimento do administrador, essa concessão não será feita em nome de qualquer usuário específico. Em vez disso, o aplicativo cliente recebe permissões *diretamente*. Esses tipos de permissões são usados apenas por serviços de daemon e outros aplicativos não interativos que são executados em segundo plano.

## <a name="using-the-admin-consent-endpoint"></a>Usando o ponto de extremidade de consentimento do administrador

> [!NOTE] 
> Observe que, depois de conceder o consentimento do administrador usando o ponto de extremidade de consentimento do administrador, você terminou de conceder o consentimento do administrador e os usuários não precisam executar outras ações adicionais. Depois de conceder o consentimento do administrador, os usuários poderão obter um token de acesso por meio de um fluxo de autenticação típico e o token de acesso resultante terá as permissões consentidas. 

Quando um administrador da empresa usa seu aplicativo e é direcionado para o ponto de extremidade de autorização, a plataforma de identidade da Microsoft detectará a função do usuário e perguntará se deseja consentir em nome do locatário inteiro para as permissões que você solicitou. No entanto, também há um ponto de extremidade de consentimento de administrador dedicado que você pode usar se quiser solicitar proativamente que um administrador Conceda permissão em nome de todo o locatário. O uso desse ponto de extremidade também é necessário para solicitar permissões de aplicativo (que não podem ser solicitadas usando o ponto de extremidade de autorização).

Se você seguir essas etapas, seu aplicativo poderá solicitar permissões para todos os usuários em um locatário, incluindo escopos restritos ao administrador. Essa é uma operação de alto privilégio e só deve ser feita se necessário para seu cenário.

Para ver um exemplo de código que implementa as etapas, consulte o [exemplo de escopos restritos ao administrador](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2).

### <a name="request-the-permissions-in-the-app-registration-portal"></a>Solicitar as permissões no portal de registro de aplicativo

O consentimento do administrador não aceita um parâmetro de escopo, portanto, todas as permissões solicitadas devem ser definidas estaticamente no registro do aplicativo. Em geral, é recomendável garantir que as permissões definidas estaticamente para um determinado aplicativo sejam um superconjunto das permissões que serão solicitadas dinamicamente/incrementalmente.

#### <a name="to-configure-the-list-of-statically-requested-permissions-for-an-application"></a>Para configurar a lista de permissões solicitadas estaticamente para um aplicativo

1. Vá para seu aplicativo na experiência de [portal do Azure – registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) ou [crie um aplicativo](quickstart-register-app.md) , caso ainda não tenha feito isso.
2. Localize a seção **permissões de API** e, dentro das permissões de API, clique em adicionar uma permissão.
3. Selecione **Microsoft Graph** na lista de APIs disponíveis e, em seguida, adicione as permissões que seu aplicativo requer.
3. **Salve** o registro do aplicativo.

### <a name="recommended-sign-the-user-into-your-app"></a>Recomendado: conectar o usuário ao seu aplicativo

Normalmente, quando você cria um aplicativo que usa o ponto de extremidade de consentimento do administrador, o aplicativo precisa de uma página ou exibição na qual o administrador possa aprovar as permissões do aplicativo. Essa página pode fazer parte do fluxo de inscrição do aplicativo, parte das configurações do aplicativo ou pode ser um fluxo de "conexão" dedicado. Em muitos casos, faz sentido que o aplicativo mostre esse modo de exibição "conectar" somente depois que um usuário tiver entrado com uma conta Microsoft corporativa ou de estudante.

Ao conectar o usuário ao seu aplicativo, você pode identificar a organização à qual o administrador pertence antes de pedir que eles aprovem as permissões necessárias. Embora não seja estritamente necessário, ele pode ajudá-lo a criar uma experiência mais intuitiva para seus usuários organizacionais. Para conectar o usuário, siga nossos [tutoriais de protocolo de plataforma de identidade da Microsoft](active-directory-v2-protocols.md).

### <a name="request-the-permissions-from-a-directory-admin"></a>Solicitar as permissões de um administrador de diretório

Quando estiver pronto para solicitar permissões do administrador da sua organização, você poderá redirecionar o usuário para o ponto de *extremidade de consentimento do administrador*da plataforma de identidade da Microsoft.

```
// Line breaks are for legibility only.
    GET https://login.microsoftonline.com/{tenant}/v2.0/adminconsent?
  client_id=6731de76-14a6-49ae-97bc-6eba6914391e
  &state=12345
  &redirect_uri=http://localhost/myapp/permissions
    &scope=
    https://graph.microsoft.com/calendars.read 
    https://graph.microsoft.com/mail.send
```


| Parâmetro     | Condição     | Descrição                                                                               |
|--------------:|--------------:|:-----------------------------------------------------------------------------------------:|
| `tenant` | Necessário | O locatário do diretório do qual você deseja solicitar permissão. Pode ser fornecido em formato de nome amigável ou GUID ou referenciado genericamente com `common` como visto no exemplo. |
| `client_id` | Necessário | A **ID do aplicativo (cliente)** que a [portal do Azure – registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) experiência atribuída ao seu aplicativo. |
| `redirect_uri` | Necessário |O URI de redirecionamento no qual você deseja que a resposta seja enviada para que seu aplicativo manipule. Ele deve corresponder exatamente a um dos URIs de redirecionamento que você registrou no portal de registro de aplicativo. |
| `state` | Recomendado | Um valor incluído na solicitação que também será retornado na resposta do token. Pode ser uma cadeia de caracteres de qualquer conteúdo desejado. Use o estado para codificar informações sobre o estado do usuário no aplicativo antes que a solicitação de autenticação ocorra, como a página ou a exibição em que eles estavam. |
|`scope`        | Necessário      | Define o conjunto de permissões que estão sendo solicitadas pelo aplicativo. Pode ser estático (usando/.default) ou escopos dinâmicos.  Isso pode incluir os escopos OIDC (`openid`, `profile`, `email`). | 


Neste ponto, o Azure AD requer que um administrador de locatários entre para concluir a solicitação. O administrador é solicitado a aprovar todas as permissões que você solicitou no parâmetro `scope`.  Se você usou um valor estático (`/.default`), ele funcionará como o ponto de extremidade de consentimento do administrador v 1.0 e solicitará o consentimento para todos os escopos encontrados nas permissões necessárias para o aplicativo.

#### <a name="successful-response"></a>Resposta bem-sucedida

Se o administrador aprovar as permissões para seu aplicativo, a resposta bem-sucedida terá esta aparência:

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parâmetro | Descrição |
| --- | --- |
| `tenant` | O locatário de diretório que concedeu ao aplicativo as permissões solicitadas, no formato GUID. |
| `state` | Um valor incluído na solicitação que também será retornado na resposta do token. Pode ser uma cadeia de caracteres de qualquer conteúdo desejado. O estado é usado para codificar informações sobre o estado do usuário no aplicativo antes que a solicitação de autenticação ocorra, como a página ou a exibição em que eles estavam. |
| `admin_consent` | Será definido como `True`. |

#### <a name="error-response"></a>Resposta de erro

Se o administrador não aprovar as permissões para seu aplicativo, a resposta com falha terá esta aparência:

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parâmetro | Descrição |
| --- | --- |
| `error` | Uma cadeia de caracteres de código de erro que pode ser usada para classificar tipos de erros que ocorrem e pode ser usada para reagir a erros. |
| `error_description` | Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro. |

Depois de receber uma resposta bem-sucedida do ponto de extremidade de consentimento do administrador, seu aplicativo ganhou as permissões solicitadas. Em seguida, você pode solicitar um token para o recurso desejado.

## <a name="using-permissions"></a>Usando permissões

Depois que o usuário consentir permissões para seu aplicativo, seu aplicativo poderá adquirir tokens de acesso que representem a permissão do seu aplicativo para acessar um recurso em alguma capacidade. Um token de acesso pode ser usado apenas para um único recurso, mas codificado dentro do token de acesso é cada permissão que seu aplicativo recebeu para esse recurso. Para adquirir um token de acesso, seu aplicativo pode fazer uma solicitação para o ponto de extremidade de token da plataforma de identidade da Microsoft, desta forma:

```
POST common/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
    "grant_type": "authorization_code",
    "client_id": "6731de76-14a6-49ae-97bc-6eba6914391e",
    "scope": "https://outlook.office.com/mail.read https://outlook.office.com/mail.send",
    "code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq..."
    "redirect_uri": "https://localhost/myapp",
    "client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

Você pode usar o token de acesso resultante em solicitações HTTP para o recurso. Ele indica de maneira confiável o recurso de que seu aplicativo tem a permissão apropriada para executar uma tarefa específica. 

Para obter mais informações sobre o protocolo OAuth 2,0 e como obter tokens de acesso, consulte a [referência do protocolo de ponto de extremidade da plataforma de identidade da Microsoft](active-directory-v2-protocols.md).

## <a name="the-default-scope"></a>O escopo/.Default

Você pode usar o escopo de `/.default` para ajudar a migrar seus aplicativos do ponto de extremidade v 1.0 para o ponto de extremidade da plataforma Microsoft Identity. Esse é um escopo interno para cada aplicativo que se refere à lista estática de permissões configuradas no registro do aplicativo. Um valor `scope` de `https://graph.microsoft.com/.default` é funcionalmente o mesmo que os pontos de extremidade v 1.0 `resource=https://graph.microsoft.com`-or, ele solicita um token com os escopos no Microsoft Graph em que o aplicativo se registrou na portal do Azure.

O escopo/.default pode ser usado em qualquer fluxo OAuth 2,0, mas é necessário no [fluxo em nome de](v2-oauth2-on-behalf-of-flow.md) e no fluxo de [credenciais do cliente](v2-oauth2-client-creds-grant-flow.md).  

> [!NOTE]
> Os clientes não podem combinar o consentimento estático (`/.default`) e dinâmico em uma única solicitação. Assim, `scope=https://graph.microsoft.com/.default+mail.read` resultará em um erro devido à combinação de tipos de escopo.

### <a name="default-and-consent"></a>/.Default e consentimento

O escopo de `/.default` aciona o comportamento de ponto de extremidade v 1.0 para `prompt=consent` também. Ele solicita consentimento para todas as permissões registradas pelo aplicativo, independentemente do recurso. Se incluído como parte da solicitação, o escopo de `/.default` retorna um token que contém os escopos para o recurso solicitado.

### <a name="default-when-the-user-has-already-given-consent"></a>/.default quando o usuário já tiver dado consentimento

Como `/.default` é funcionalmente idêntica ao comportamento do ponto de extremidade v 1.0 centrado em `resource`, ele também traz o comportamento de consentimento do ponto de extremidade v 1.0. Ou seja, `/.default` apenas acionará um prompt de consentimento se nenhuma permissão tiver sido concedida entre o cliente e o recurso pelo usuário. Se esse tipo de consentimento existir, um token será retornado contendo todos os escopos concedidos pelo usuário para esse recurso. No entanto, se nenhuma permissão tiver sido concedida ou o parâmetro `prompt=consent` tiver sido fornecido, uma solicitação de consentimento será mostrada para todos os escopos registrados pelo aplicativo cliente.

#### <a name="example-1-the-user-or-tenant-admin-has-granted-permissions"></a>Exemplo 1: o usuário, ou administrador de locatário, concedeu permissões

O usuário (ou um administrador de locatários) concedeu ao cliente as permissões de Microsoft Graph `mail.read` e `user.read`. Se o cliente fizer uma solicitação para `scope=https://graph.microsoft.com/.default`, nenhum prompt de consentimento será mostrado, independentemente do conteúdo das permissões registradas de aplicativos cliente para Microsoft Graph. Um token seria retornado contendo os escopos `mail.read` e `user.read`.

#### <a name="example-2-the-user-hasnt-granted-permissions-between-the-client-and-the-resource"></a>Exemplo 2: o usuário não concedeu permissões entre o cliente e o recurso

Não existe consentimento para o usuário entre o cliente e o Microsoft Graph. O cliente se registrou para as permissões de `user.read` e `contacts.read`, bem como o `https://vault.azure.net/user_impersonation`de escopo de Azure Key Vault. Quando o cliente solicitar um token para `scope=https://graph.microsoft.com/.default`, o usuário verá uma tela de consentimento para o `user.read`, `contacts.read`e os escopos de `user_impersonation` de Key Vault. O token retornado terá apenas os escopos `user.read` e `contacts.read`.

#### <a name="example-3-the-user-has-consented-and-the-client-requests-additional-scopes"></a>Exemplo 3: o usuário consentiu e o cliente solicita escopos adicionais

O usuário já consentiu `mail.read` para o cliente. O cliente foi registrado para o escopo de `contacts.read` em seu registro. Quando o cliente faz uma solicitação para um token usando `scope=https://graph.microsoft.com/.default` e solicita o consentimento por meio de `prompt=consent`, o usuário verá uma tela de consentimento somente para e todas as permissões registradas pelo aplicativo. `contacts.read` estará presente na tela de consentimento, mas o `mail.read` não fará isso. O token retornado será para Microsoft Graph e conterá `mail.read` e `contacts.read`.

### <a name="using-the-default-scope-with-the-client"></a>Usando o escopo/.Default com o cliente

Um caso especial do escopo de `/.default` existe onde um cliente solicita seu próprio escopo de `/.default`. O exemplo a seguir demonstra esse cenário.

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
response_type=token            //code or a hybrid flow is also possible here
&client_id=9ada6f8a-6d83-41bc-b169-a306c21527a5
&scope=9ada6f8a-6d83-41bc-b169-a306c21527a5/.default
&redirect_uri=https%3A%2F%2Flocalhost
&state=1234
```

Isso produz uma tela de consentimento para todas as permissões registradas (se aplicável com base nas descrições acima de consentimento e `/.default`) e, em seguida, retorna um id_token, em vez de um token de acesso.  Esse comportamento existe para determinados clientes herdados que se movem de ADAL para MSAL e não devem ser usados por novos clientes direcionados ao ponto de extremidade da plataforma Microsoft Identity.  

## <a name="troubleshooting-permissions-and-consent"></a>Permissões de solução de problemas e consentimento

Se você ou os usuários do aplicativo estiverem vendo erros inesperados durante o processo de consentimento, consulte este artigo para obter as etapas de solução de problemas: [erro inesperado ao executar o consentimento para um aplicativo](../manage-apps/application-sign-in-unexpected-user-consent-error.md).

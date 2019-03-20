---
title: Âmbitos de v2.0 do Active Directory, permissões e consentimento do Azure | Documentos da Microsoft
description: Uma descrição de autorização, o Azure AD v2.0 ponto final, incluindo âmbitos, permissões e consentimento.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 8f98cbf0-a71d-4e34-babf-e644ad9ff423
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: celested
ms.reviewer: hirsin, jesakowi, jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 225065e35e40c06d324bee89fa65a765a2727233
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58123764"
---
# <a name="permissions-and-consent-in-the-azure-active-directory-v20-endpoint"></a>Permissões e consentimento no ponto de final de v2.0 do Azure Active Directory

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Aplicativos que se integram com plataforma de identidade do Microsoft seguem um modelo de autorização que oferece controle de utilizadores e administradores sobre como os dados podem ser acedidos. A implementação do modelo de autorização foi atualizada no ponto final v2.0, e ele altera a forma como uma aplicação tem de interagir com a plataforma de identidade do Microsoft. Este artigo abrange os conceitos básicos deste modelo de autorização, incluindo âmbitos, permissões e consentimento.

> [!NOTE]
> O ponto final v2.0 não suporta todos os cenários e funcionalidades. Para determinar se deve utilizar o ponto final v2.0, leia sobre [v2.0 limitações](active-directory-v2-limitations.md).

## <a name="scopes-and-permissions"></a>Âmbitos e permissões

A implementa de plataforma de identidade do Microsoft da [OAuth 2.0](active-directory-v2-protocols.md) protocolo de autorização. OAuth 2.0 é um método por meio do qual uma aplicação de terceiros pode acessar recursos hospedados na web em nome de um utilizador. Qualquer recurso hospedado na web que integra-se com a plataforma de identidade da Microsoft tem um identificador de recurso, ou *URI de ID de aplicação*. Por exemplo, alguns dos recursos de hospedado na web da Microsoft incluem:

* Microsoft Graph: `https://graph.microsoft.com`
* API de correio do Office 365: `https://outlook.office.com`
* Azure AD Graph: `https://graph.windows.net`

> [!NOTE]
> Recomendamos vivamente que utilize o Microsoft Graph em vez do Azure AD Graph, API de correio do Office 365, etc.

O mesmo acontece com todos os recursos de terceiros que integraram com a plataforma de identidade da Microsoft. Qualquer um desses recursos também pode definir um conjunto de permissões que pode ser utilizado para dividir a funcionalidade desse recurso em segmentos mais pequenos. Por exemplo, [Microsoft Graph](https://graph.microsoft.com) definiu permissões para efetuar as seguintes tarefas, entre outras:

* Leia o calendário de um utilizador
* Escrever para o calendário de um utilizador
* Enviar e-mails como se fosse um utilizador

Ao definir esses tipos de permissões, o recurso tem um controle refinado sobre os seus dados e como as funcionalidades de API é exposta. Uma aplicação de terceiros pode pedir estas permissões de utilizadores e administradores, que têm de aprovar o pedido antes da aplicação pode aceder a dados ou agir em nome de um utilizador. Por segmentar a funcionalidade do recurso em conjuntos menores de permissão, aplicações de terceiros podem ser criadas para solicitar apenas as permissões específicas que precisam para executar a respetiva função. Utilizadores e administradores podem saber exatamente os dados que a aplicação tem acesso aos e podem ser mais seguro de que ele está não se comportando com más intenções. Os desenvolvedores sempre devem concordar com o conceito de privilégio mínimo, pedindo apenas as permissões necessárias para as aplicações a funcionar.

OAuth 2.0, esses tipos de permissões são chamados *âmbitos*. Eles também, muitas vezes, simplesmente denominados *permissões*. Uma permissão é representada na plataforma de identidades da Microsoft como um valor de cadeia de caracteres. Continuando com o exemplo do Microsoft Graph, o valor de cadeia de caracteres para cada permissão é:

* Leia o calendário de um utilizador através da utilização `Calendars.Read`
* Escrever para o calendário de um utilizador ao utilizar `Calendars.ReadWrite`
* Envie e-mails como um utilizador a utilizar por `Mail.Send`

Uma aplicação mais frequentemente pedidos de ponto final de autorizar estas permissões, especificando os âmbitos em pedidos para a versão 2.0. No entanto, determinadas permissões de privilégio alto só podem ser concedidas através de consentimento de administrador e geralmente solicitado/concedido a utilizar o [ponto final de consentimento de administrador](v2-permissions-and-consent.md#admin-restricted-permissions). Continue a ler para obter mais informações.

## <a name="permission-types"></a>Tipos de permissão

Plataforma de identidade da Microsoft suporta dois tipos de permissões: **permissões delegadas** e **permissões de aplicação**.

* **Permissões delegadas** são utilizados por aplicações que têm um utilizador com sessão iniciada presente. Para estas aplicações, o utilizador ou administrador autorizar as permissões que os pedidos de aplicação e a aplicação é uma permissão delegada para atuar como o utilizador com sessão iniciada ao efetuar chamadas para o recurso de destino. Algumas permissões delegadas podem ser permitidas por utilizadores não administrativos, mas exigem algumas permissões com mais privilégios [consentimento de administrador](v2-permissions-and-consent.md#admin-restricted-permissions). Para saber que funções podem dar consentimento a permissões delegadas de administrador, veja [permissões da função de administrador no Azure AD](../users-groups-roles/directory-assign-admin-roles.md).

* **Permissões de aplicação** são utilizados por aplicações que executam o sem um utilizador com sessão iniciada presente; por exemplo, aplicativos que são executados como serviços em segundo plano ou daemons.  Permissões de aplicação só é possível [deu consentimento por um administrador](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

_Permissões efetivas_ são as permissões que a aplicação terá quando são efetuados pedidos para o recurso de destino. É importante compreender a diferença entre o delegado e as permissões de aplicação que é concedida a sua aplicação e as respetivas permissões em vigor quando fazer chamadas para o recurso de destino.

- Para obter permissões delegadas, o _permissões efetivas_ da sua aplicação será a interseção com menos privilégios das permissões delegadas a aplicação recebeu (através de consentimento) e os privilégios do utilizador atualmente com sessão iniciada. A aplicação nunca pode ter mais privilégios do que o utilizador com sessão iniciada. Nas organizações, os privilégios do utilizador com sessão iniciada podem ser determinados por uma política ou por associação a uma ou mais funções de administrador. Para saber que funções podem dar consentimento a permissões delegadas de administrador, veja [permissões da função de administrador no Azure AD](../users-groups-roles/directory-assign-admin-roles.md).
  Por exemplo, suponha a sua aplicação tenha sido concedida a _User.ReadWrite.All_ delegado permissão. Esta permissão concede nominalmente permissão à aplicação para ler e atualizar o perfil de todos os utilizadores de uma organização. Se o utilizador com sessão iniciada for administrador global, a aplicação poderá atualizar o perfil de todos os utilizadores da organização. No entanto, se o utilizador com sessão iniciada não tiver uma função de administrador, a aplicação só poderá atualizar o perfil desse utilizador. Não poderá atualizar os perfis dos outros utilizadores da organização porque o utilizador em cujo nome tem permissão para agir não tem esses privilégios.
  
- Para obter permissões de aplicação, o _permissões efetivas_ da sua aplicação será o nível completo de privilégios implícitas pela permissão. Por exemplo, uma aplicação com o _User.ReadWrite.All_ permissão da aplicação pode atualizar o perfil de cada utilizador na organização. 

## <a name="openid-connect-scopes"></a>Âmbitos de OpenID Connect

A implementação de v2.0 do OpenID Connect tem alguns âmbitos bem definidos que não se aplicam a um recurso específico: `openid`, `email`, `profile`, e `offline_access`. O `address` e `phone` OpenID Connect âmbitos não são suportados.

### <a name="openid"></a>openid

Se uma aplicação realiza início de sessão usando [OpenID Connect](active-directory-v2-protocols.md), ele deve solicitar o `openid` âmbito. O `openid` âmbito mostra a página de consentimento da conta de trabalho como a permissão "Iniciar sessão" e a página de consentimento de conta pessoal Microsoft como a permissão "Visualizar seu perfil e ligar a aplicações e serviços com a sua conta Microsoft". Com esta permissão, uma aplicação pode receber um identificador exclusivo para o usuário na forma do `sub` de afirmação. Isso também permite o acesso da aplicação para o ponto final de UserInfo. O `openid` âmbito pode ser utilizado no ponto de final do token de v2.0 para adquirir os tokens de ID, que podem ser utilizados para proteger as chamadas HTTP entre os diferentes componentes de uma aplicação.

### <a name="email"></a>e-mail

O `email` âmbito pode ser utilizado com o `openid` escopo e dos outros. Fornece o acesso da aplicação para o endereço de e-mail primário do utilizador na forma do `email` de afirmação. O `email` afirmação está incluída num token apenas se um endereço de e-mail está associado com a conta de utilizador, que nem sempre é o caso. Se utiliza a `email` âmbito, a sua aplicação deve estar preparada para processar um caso em que o `email` afirmação não existe no token.

### <a name="profile"></a>perfil

O `profile` âmbito pode ser utilizado com o `openid` escopo e dos outros. Possibilita o acesso de aplicação a uma quantidade substancial de informações sobre o utilizador. As informações que ele pode acessar incluem, mas não está limitadas a, o nome de utilizador nome próprio, apelido, preferencial e ID de objeto. Para obter uma lista completa das afirmações de perfil disponíveis no parâmetro id_tokens para um utilizador específico, consulte a [ `id_tokens` referência](id-tokens.md).

### <a name="offlineaccess"></a>offline_access

O [ `offline_access` âmbito](https://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) oferece o acesso a aplicações para recursos em nome do utilizador para um período de tempo alargado. Na página de consentimento, este âmbito é apresentado como a permissão "Manter o acesso aos dados que tenha dado acesso". Quando um utilizador aprova o `offline_access` âmbito, a aplicação pode receber tokens de atualização do ponto final do token da versão 2.0. Tokens de atualização são vida longa. A aplicação pode obter novos tokens de acesso, como o antigo expirar.

Se a sua aplicação não solicita explicitamente o `offline_access` escopo, ele não irão receber tokens de atualização. Isso significa que, quando resgatar um código de autorização no [fluxo de código de autorização de OAuth 2.0](active-directory-v2-protocols.md), irá receber um token de acesso do `/token` ponto final. O token de acesso é válido para um curto período de tempo. O token de acesso, normalmente, expira dentro de uma hora. AT que ponto, o aplicativo precisa redirecionar o usuário de volta para o `/authorize` ponto final para obter um novo código de autorização. Durante este redirecionamento, dependendo do tipo de aplicação, o utilizador poderá ter de introduzir as respetivas credenciais novamente ou novamente o consentimento a permissões.  Tenha em atenção que, embora o `offline_access` âmbito automaticamente é solicitada pelo servidor, o cliente tem ainda solicitá-la para receber os tokens de atualização. 

Para obter mais informações sobre como obter e utilizar tokens de atualização, consulte a [referência do protocolo v2.0](active-directory-v2-protocols.md).

## <a name="requesting-individual-user-consent"></a>Pedir consentimento do utilizador individuais

Num [OpenID Connect ou do OAuth 2.0](active-directory-v2-protocols.md) pedido de autorização, uma aplicação pode pedir as permissões necessárias ao utilizar o `scope` parâmetro de consulta. Por exemplo, quando um utilizador inicia sessão a uma aplicação, a aplicação envia um pedido, como o exemplo seguinte (com quebras de linha adicionadas para melhorar a legibilidade):

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

O `scope` parâmetro é uma lista separada de espaço de permissões delegadas que a aplicação está a pedir. Cada permissão é indicada ao acrescentar o valor de permissão ao identificador do recurso (o URI de ID de aplicação). O exemplo de pedido, a aplicação tem permissão para ler o calendário do usuário e enviar correio como o utilizador.

Depois do utilizador introduz as respetivas credenciais, o ponto final v2.0 verifica a existência de um registo correspondente *consentimento do utilizador*. Se o utilizador não consentiu para qualquer um das permissões pedidas no passado, nem um administrador já autorizou para estas permissões em nome de toda a organização, o ponto final v2.0 pede ao utilizador para conceder as permissões pedidas.

> [!NOTE]
> Neste momento, o `offline_access` ("manter o acesso aos dados que tenha dado acesso") e `user.read` ("o início de sessão e ler o seu perfil") permissões estão incluídas automaticamente no consentimento inicial para uma aplicação.  Estas permissões normalmente são necessárias para a funcionalidade de aplicação corretas - `offline_access` permite o acesso de aplicação para críticos, tokens de atualização nativos e aplicações web, enquanto `user.read` dá acesso para o `sub` afirmação, permitindo que o cliente ou a aplicação corretamente identifica o utilizador ao longo do tempo e acessem informações de utilizador rudimentar.  

![Consentimento de conta de trabalho](./media/v2-permissions-and-consent/work_account_consent.png)

Quando o usuário aprova a solicitação de permissão, consentimento é registrado e o utilizador não tem consentimento novamente em inícios de sessão subsequentes para o aplicativo.

## <a name="requesting-consent-for-an-entire-tenant"></a>Pedir consentimento para um inquilino completo

Muitas vezes, quando uma organização compra uma subscrição para uma aplicação ou de licença, a organização pretende proativamente configurar a aplicação para utilização por todos os membros da organização. Como parte deste processo, um administrador pode conceder o consentimento para a aplicação para agir em nome de qualquer utilizador no inquilino. Se o administrador conceder consentimento para todo o inquilino, os utilizadores da organização não verão uma página de consentimento para a aplicação.

Para pedir consentimento para permissões delegadas para todos os utilizadores num inquilino, a sua aplicação pode utilizar o ponto final de consentimento de administrador.

Além disso, aplicativos tem de utilizar o ponto final de consentimento de administrador para solicitar permissões de aplicação.

## <a name="admin-restricted-permissions"></a>Permissões de administrador restrito

Algumas permissões de privilégio elevado no ecossistema da Microsoft podem ser definidos como *restritas por administrador*. Seguem-se exemplos desses tipos de permissões:

* Ler perfis completos de todos os utilizadores ao utilizar `User.Read.All`
* Escrever dados para o diretório da organização através da utilização `Directory.ReadWrite.All`
* Ler todos os grupos no diretório da organização ao utilizar `Groups.Read.All`

Embora um utilizador de consumidor pode conceder uma aplicação de acesso para este tipo de dados, os utilizadores organizacionais são impedidos de conceder acesso para o mesmo conjunto de dados confidenciais da empresa. Se a aplicação solicite o acesso a uma destas permissões de um utilizador organizacional, o utilizador recebe uma mensagem de erro que diz que não tem autorização para dar consentimento a permissões da sua aplicação.

Se a sua aplicação necessita de acesso a âmbitos de administração restrito de mensagens em fila para as organizações, deve pedi-los também diretamente a partir de um administrador de empresa, utilizando o consentimento do ponto final de administração, descrito a seguir.

Se a aplicação está a pedir permissões delegadas de privilégio alto e um administrador conceder estas permissões por meio do ponto final de consentimento de administrador, o consentimento é concedido para todos os utilizadores no inquilino.

Se a aplicação está a solicitar permissões de aplicação e um administrador conceder que estas permissões por meio do administrador de consentimento de ponto final, esta concessão não é feita em nome de qualquer utilizador específico. Em vez disso, a aplicação de cliente é concedida permissões *diretamente*. Em geral, esses tipos de permissões só são utilizados por serviços de daemon e outras aplicações não interativas que são executados em segundo plano.

## <a name="using-the-admin-consent-endpoint"></a>Utilizar o ponto final de consentimento de administrador

Quando um administrador de empresa utiliza o seu aplicativo e é direcionado para o ponto final de autorização, plataforma de identidade do Microsoft detectará a função do utilizador e peça-lhe se eles gostariam de consentir em nome de todo o inquilino para as permissões pedidas. No entanto, também é um ponto de consentimento de administrador dedicada final que pode utilizar, se gostaria de forma proativa solicitar que um administrador conceda a permissão em nome de todo o inquilino. Também é necessário para pedir permissões de aplicativo (que não é possível pedir a utilizar o ponto final de autorização) utilizar este ponto final.

Se seguir estes passos, seu aplicativo pode solicitar permissões para todos os utilizadores num inquilino, incluindo os âmbitos de administração restrito. Esta é uma operação de alto privilégio e deve ser feita apenas se for necessário para o seu cenário.

Para ver um exemplo de código que implementa os passos, consulte a [exemplo de âmbitos de administração restrito](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2).

### <a name="request-the-permissions-in-the-app-registration-portal"></a>Solicitar as permissões no portal de registo de aplicação

O consentimento de administrador não aceita um parâmetro de âmbito, para que todas as permissões que está a ser solicitada é necessário definir estaticamente no registo da aplicação. Em geral é melhor prática para garantir que as permissões definidas estaticamente para um determinado aplicativo são um conjunto mais amplo das permissões que irá solicitar dinamicamente/forma incremental.

Para configurar a lista de permissões estaticamente pedidas para um aplicativo: 
1. Aceda ao seu aplicativo no [Portal de registo de aplicação](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), ou [criar uma aplicação](quickstart-v2-register-an-app.md) se ainda não o fez.
2. Localize a **permissões do Microsoft Graph** secção e, em seguida, adicione as permissões que a sua aplicação necessita.
3. **Guardar** o registo de aplicações.

### <a name="recommended-sign-the-user-into-your-app"></a>Recomendado: Iniciar sessão do utilizador na sua aplicação

Normalmente, quando cria uma aplicação que utiliza o ponto final de consentimento de administrador, a aplicação tem uma vista em que o administrador pode aprovar as permissões da aplicação ou página. Esta página pode ser parte do fluxo de inscrição da aplicação, parte integrante das definições da aplicação, ou pode ser um fluxo de "ligar" dedicada. Em muitos casos, faz sentido para a aplicação mostre isso "ligar" modo de exibição apenas depois de um utilizador ter iniciado sessão com uma empresa ou escola conta Microsoft.

Quando o utilizador inicia sessão na sua aplicação, pode identificar a organização à qual o administrador pertence antes de solicitar a aprovação as permissões necessárias. Embora não seja estritamente necessário, ele pode ajudá-lo a criar uma experiência mais intuitiva para os seus utilizadores organizacionais. Para iniciar a sessão do utilizador no, siga nosso [tutoriais de protocolo de v2.0](active-directory-v2-protocols.md).

### <a name="request-the-permissions-from-a-directory-admin"></a>Solicitar as permissões de um administrador do diretório

Quando estiver pronto para solicitar permissões de administrador da sua organização, pode redirecionar o utilizador para a versão 2.0 *ponto final de consentimento de administração*.

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

```
// Pro tip: Try pasting the below request in a browser!
```

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| Parâmetro | Condição | Descrição |
| --- | --- | --- |
| `tenant` | Necessário | O inquilino do diretório que pretende pedir permissão do. Podem ser fornecidos no formato de nome amigável ou de GUID ou genericamente referenciado com `common` como visto no exemplo. |
| `client_id` | Necessário | ID de aplicação (cliente) que o [Portal de registo de aplicação](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) ou [novo portal de registos (pré-visualização) de aplicação](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) tem atribuído à sua aplicação. |
| `redirect_uri` | Necessário |O URI de redirecionamento do qual pretende que a resposta a serem enviados para a sua aplicação processar. Ele deve corresponder exatamente um dos URIs que registou no portal de registo de aplicação de redirecionamento. |
| `state` | Recomendado | Um valor incluído no pedido que também vai ser devolvido na resposta de token. Pode ser uma cadeia de caracteres de qualquer conteúdo que quer. Utilize o estado para codificar as informações sobre o estado do utilizador na aplicação antes do pedido de autenticação ocorreu, como a página ou a vista estivessem na. |

Neste momento, o Azure AD requer um administrador de inquilino iniciar sessão concluir o pedido. O administrador é-lhe pedido para aprovar todas as permissões que solicitou para a sua aplicação no portal de registo de aplicação.

#### <a name="successful-response"></a>Resposta com êxito

Se o administrador aprova as permissões para a sua aplicação, a resposta com êxito esta aparência:

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parâmetro | Descrição |
| --- | --- |
| `tenant` | O inquilino do diretório que concedidas as permissões solicitada, no formato GUID de seu aplicativo. |
| `state` | Um valor incluído no pedido que também vai ser devolvido na resposta de token. Pode ser uma cadeia de caracteres de qualquer conteúdo que quer. O estado é usado para codificar as informações sobre o estado do utilizador na aplicação antes do pedido de autenticação ocorreu, como a página ou a vista estivessem na. |
| `admin_consent` | Será definido como `True`. |

#### <a name="error-response"></a>Resposta de erro

Se o administrador não aprova as permissões para a sua aplicação, a resposta com falhas é semelhante a isto:

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parâmetro | Descrição |
| --- | --- |
| `error` | Uma cadeia de código de erro que pode ser utilizada para classificar tipos de erros que ocorrem e pode ser utilizada para reagir a erros. |
| `error_description` | Uma mensagem de erro específicas que pode ajudar a um desenvolvedor de identificar a causa de raiz de um erro. |

Depois que recebeu uma resposta com êxito a partir do ponto final de consentimento de administrador, a aplicação obteve as permissões que solicitou. Em seguida, pode solicitar um token para o recurso que pretende.

## <a name="using-permissions"></a>Utilizando as permissões

Depois do utilizador dá consentimento a permissões para a sua aplicação, a sua aplicação pode adquirir os tokens de acesso que representam a permissão da sua aplicação para aceder a um recurso em alguma capacidade. Um token de acesso pode ser utilizado apenas para um único recurso, mas codificado dentro do token de acesso é cada permissão que tenha sido concedida a sua aplicação para esse recurso. Para adquirir um token de acesso, a aplicação pode fazer um pedido para o token ponto final v2.0, como este:

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

Pode usar o token de acesso resultante em pedidos de HTTP para o recurso. Ele fiável indica ao recurso que a aplicação tem as permissões adequadas para executar uma tarefa específica. 

Para obter mais informações sobre o protocolo OAuth 2.0 e como obter os tokens de acesso, consulte a [referência de protocolo do ponto final v2.0](active-directory-v2-protocols.md).

## <a name="the-default-scope"></a>O âmbito de /.default

Pode utilizar o `/.default` âmbito para o ajudar a migrar as suas aplicações a partir do ponto final v1.0 para o ponto final v2.0. Este é um âmbito integrado para cada aplicativo que se refere à lista estática de permissões configuradas no registo de aplicação. R `scope` valor de `https://graph.microsoft.com/.default` é funcionalmente o mesmo que os pontos finais v1.0 `resource=https://graph.microsoft.com` -ou seja, ele solicita um token com os âmbitos no Microsoft Graph que o aplicativo registrou no portal do Azure.

O âmbito de /.default pode ser utilizado em qualquer fluxo de OAuth 2.0, mas é especialmente necessário no [](v2-oauth2-on-behalf-of-flow.md) em-nome-de fluxo e [fluxo de credenciais de cliente](v2-oauth2-client-creds-grant-flow.md).  

> [!NOTE]
> Os clientes não é possível combinar estática (`/.default`) e consentimento dinâmico numa única solicitação. Portanto, `scope=https://graph.microsoft.com/.default+mail.read` resultará num erro devido à combinação de tipos de âmbito.

### <a name="default-and-consent"></a>/.default e consentimento

O `/.default` âmbito aciona o comportamento de ponto final de v1.0 para `prompt=consent` também. Ele solicita o consentimento para todas as permissões registado pela aplicação, independentemente do recurso. Se incluído como parte da solicitação, o `/.default` âmbito devolve um token que contém os âmbitos para o recurso pedido especificamente.

### <a name="default-when-the-user-has-already-given-consent"></a>/.default quando o utilizador já deu consentimento

Porque `/.default` funcionalidade é idêntica do `resource`-comportamento v1.0 direcionadas ao ponto de extremidade, ele traz o comportamento de consentimento do ponto de extremidade v1.0. Ou seja, `/.default` apenas aciona uma solicitação de consentimento, se não tem permissão foi concedida entre o cliente e o recurso pelo utilizador. Se existir qualquer consentimento desse tipo, em seguida, um token será retornado que contém todos os âmbitos concedidos pelo utilizador para esse recurso. No entanto, se não tem permissão foi concedida, ou o `prompt=consent` parâmetro foi fornecido, será apresentado um pedido de consentimento de todos os âmbitos registados pela aplicação de cliente. 

#### <a name="example-1-the-user-or-tenant-admin-has-granted-permissions"></a>Exemplo 1: O utilizador ou administrador de inquilinos, concedeu permissões

O utilizador (ou um administrador inquilino) concedeu o cliente as permissões do Microsoft Graph `mail.read` e `user.read`. Se o cliente faz um pedido `scope=https://graph.microsoft.com/.default`, em seguida, nenhuma solicitação de consentimento será mostrada, independentemente do conteúdo das aplicações cliente permissões registadas para o Microsoft Graph. Seria retornado um token que contém os âmbitos `mail.read` e `user.read`.

#### <a name="example-2-the-user-hasnt-granted-permissions-between-the-client-and-the-resource"></a>Exemplo 2: Não foi concedido ao utilizador permissões entre o cliente e o recurso

Sem consentimento do utilizador existe entre o cliente e o Microsoft Graph. O cliente tenha registado para o `user.read` e `contacts.read` permissões, bem como o âmbito do Azure Key Vault `https://vault.azure.net/user_impersonation`. Quando o cliente solicita um token para `scope=https://graph.microsoft.com/.default`, o utilizador será apresentado um ecrã de consentimento para o `user.read`, `contacts.read`e o Key Vault `user_impersonation` âmbitos. O token retornado terá apenas o `user.read` e `contacts.read` âmbitos no mesmo.

#### <a name="example-3-the-user-has-consented-and-the-client-requests-additional-scopes"></a>Exemplo 3: O utilizador consentiu e o cliente solicita âmbitos adicionais

O utilizador já deu consentimento ao `mail.read` para o cliente. O cliente tenha registado para o `contacts.read` âmbito no seu registo. Quando o cliente faz um pedido para um token através de `scope=https://graph.microsoft.com/.default` e o consentimento de pedidos por meio de `prompt=consent`, em seguida, o utilizador será apresentado um ecrã de consentimento para apenas e todas as permissões registado pela aplicação. `contacts.read` será apresentado no ecrã de consentimento, mas `mail.read` será não. O token retornado será para o Microsoft Graph e conterá `mail.read` e `contacts.read`.

### <a name="using-the-default-scope-with-the-client"></a>Utilizar o âmbito de /.default com o cliente

Um caso especial do `/.default` âmbito existe em que um cliente solicita a sua própria `/.default` âmbito. O exemplo seguinte demonstra este cenário.

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
response_type=token            //code or a hybrid flow is also possible here
&client_id=9ada6f8a-6d83-41bc-b169-a306c21527a5
&scope=9ada6f8a-6d83-41bc-b169-a306c21527a5/.default
&redirect_uri=https%3A%2F%2Flocalhost
&state=1234
```

Isso produz um ecrã de consentimento para todas as permissões registrados (se aplicáveis com base nas descrições apresentadas acima de consentimento e `/.default`), em seguida, devolve uma id_token, em vez de um token de acesso.  Esse comportamento existe para determinados clientes legados de mover da ADAL para MSAL e não deve ser usado pelos novos clientes visando o ponto final v2.0.  

## <a name="troubleshooting-permissions-and-consent"></a>Resolução de problemas de permissões e consentimento

Se ou os utilizadores da sua aplicação estão a ver erros inesperados durante o processo de consentimento, consulte este artigo para passos de resolução de problemas: [Erro inesperado ao consentir uma aplicação](../manage-apps/application-sign-in-unexpected-user-consent-error.md).

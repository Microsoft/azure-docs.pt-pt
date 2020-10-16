---
title: Âmbitos, permissões e consentimento da plataforma de identidade da Microsoft
description: Saiba mais sobre a autorização no ponto final da plataforma de identidade da Microsoft, incluindo âmbitos, permissões e consentimento.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: ryanwi
ms.reviewer: hirsin, jesakowi, jmprieur, marsma
ms.custom: aaddev, fasttrack-edit, contperfq1, identityplatformtop40
ms.openlocfilehash: 79475414f6785474596beae208fefae81a673dea
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91842687"
---
# <a name="permissions-and-consent-in-the-microsoft-identity-platform-endpoint"></a>Permissões e consentimento no ponto final da plataforma de identidades da Microsoft

As aplicações que se integram com a plataforma de identidade da Microsoft seguem um modelo de autorização que dá aos utilizadores e administradores o controlo sobre a forma como os dados podem ser acedidos. A implementação do modelo de autorização foi atualizada no ponto final da plataforma de identidade da Microsoft, e altera a forma como uma aplicação deve interagir com a plataforma de identidade da Microsoft. Este artigo abrange os conceitos básicos deste modelo de autorização, incluindo âmbitos, permissões e consentimento.

## <a name="scopes-and-permissions"></a>Âmbitos e permissões

A plataforma de identidade da Microsoft implementa o protocolo de autorização [OAuth 2.0.](active-directory-v2-protocols.md) O OAuth 2.0 é um método através do qual uma aplicação de terceiros pode aceder a recursos hospedados na Web em nome de um utilizador. Qualquer recurso hospedado na Web que se integre com a plataforma de identidade da Microsoft tem um identificador de recursos, ou *ID URI de aplicação.* Por exemplo, alguns dos recursos hospedados na Microsoft incluem:

* Microsoft Graph: `https://graph.microsoft.com`
* Microsoft 365 Mail API: `https://outlook.office.com`
* Cofre da Chave Azure: `https://vault.azure.net`

> [!NOTE]
> Recomendamos vivamente que utilize o Microsoft Graph em vez do Microsoft 365 Mail API, etc.

O mesmo acontece com os recursos de terceiros que se integraram na plataforma de identidade da Microsoft. Qualquer um destes recursos também pode definir um conjunto de permissões que podem ser usadas para dividir a funcionalidade desse recurso em pedaços menores. Como exemplo, [o Microsoft Graph](https://graph.microsoft.com) definiu permissões para fazer as seguintes tarefas, entre outras:

* Leia o calendário de um utilizador
* Escreva para o calendário de um utilizador
* Enviar correio como utilizador

Ao definir este tipo de permissões, o recurso tem um controlo fino sobre os seus dados e como a funcionalidade API está exposta. Uma aplicação de terceiros pode solicitar estas permissões aos utilizadores e administradores, que devem aprovar o pedido antes de a aplicação poder aceder aos dados ou agir em nome de um utilizador. Ao aumentar a funcionalidade do recurso em conjuntos de permissões mais pequenos, as aplicações de terceiros podem ser construídas para solicitar apenas as permissões específicas de que necessitam para desempenhar a sua função. Os utilizadores e administradores podem saber exatamente a que dados a aplicação tem acesso, e podem estar mais confiantes de que não se está a comportar com intenção maliciosa. Os desenvolvedores devem sempre respeitar o conceito de menor privilégio, pedindo apenas as permissões de que necessitam para que as suas aplicações funcionem.

Em OAuth 2.0, estes tipos de permissões são *chamados de âmbitos*. Também são frequentemente *referidas como permissões.* Uma permissão é representada na plataforma de identidade da Microsoft como um valor de cadeia. Continuando com o exemplo do Microsoft Graph, o valor de cadeia para cada permissão é:

* Leia o calendário de um utilizador usando `Calendars.Read`
* Escreva para o calendário de um utilizador usando `Calendars.ReadWrite`
* Enviar correio como um utilizador que utiliza por `Mail.Send`

Uma aplicação solicita mais frequentemente estas permissões especificando os âmbitos de aplicação em pedidos para a plataforma de identidade da Microsoft autorizar o ponto final. No entanto, determinadas permissões de elevado privilégio só podem ser concedidas através do consentimento do administrador e solicitadas/concedidas utilizando o [ponto final](#admin-restricted-permissions)de consentimento do administrador . Leia mais para saber mais.

## <a name="permission-types"></a>Tipos de permissão

A plataforma de identidade da Microsoft suporta dois tipos de permissões: **permissões delegadas** e **permissões de aplicação.**

* **As permissões delegadas** são utilizadas por apps que tenham um utilizador inscrito presente. Para estas aplicações, o utilizador ou um administrador consente com as permissões que a aplicação solicita, e a aplicação é delegada permissão para agir como utilizador inscrito ao então fazer chamadas para o recurso-alvo. Algumas permissões delegadas podem ser consentidas por utilizadores não administrativos, mas algumas permissões privilegiadas mais elevadas requerem [o consentimento do administrador](#admin-restricted-permissions). Para saber quais as funções de administrador que podem consentir com permissões delegadas, consulte [permissões de função do Administrador em Azure AD](../users-groups-roles/directory-assign-admin-roles.md).

* **As permissões de aplicação** são utilizadas por apps que funcionam sem a presença de um utilizador inscrito; por exemplo, aplicativos que funcionam como serviços de fundo ou daemons.  As permissões de pedido só podem ser [consentidas por um administrador.](#requesting-consent-for-an-entire-tenant)

_Permissões eficazes_ são as permissões que a sua aplicação terá ao efetivo pedidos para o recurso-alvo. É importante entender a diferença entre as permissões delegadas e as permissões de aplicação que a sua aplicação é concedida e as suas permissões efetivas ao fazer chamadas para o recurso-alvo.

- Para permissões delegadas, as _permissões efetivas_ da sua app serão a intersecção menos privilegiada das permissões delegadas que a app foi concedida (por consentimento) e os privilégios do utilizador atualmente inscrito. A aplicação nunca pode ter mais privilégios do que o utilizador com sessão iniciada. Nas organizações, os privilégios do utilizador com sessão iniciada podem ser determinados por uma política ou por associação a uma ou mais funções de administrador. Para saber quais as funções de administrador que podem consentir com permissões delegadas, consulte [permissões de função do Administrador em Azure AD](../users-groups-roles/directory-assign-admin-roles.md).

   Por exemplo, assuma que a sua aplicação foi concedida ao _Utilizador.ReadWrite.Toda a_ permissão delegada. Esta permissão concede nominalmente permissão à aplicação para ler e atualizar o perfil de todos os utilizadores de uma organização. Se o utilizador com sessão iniciada for administrador global, a aplicação poderá atualizar o perfil de todos os utilizadores da organização. No entanto, se o utilizador inscrito não estiver numa função de administrador, a sua aplicação poderá atualizar apenas o perfil do utilizador inscrito. Não poderá atualizar os perfis dos outros utilizadores da organização porque o utilizador em cujo nome tem permissão para agir não tem esses privilégios.

- Para permissões de aplicação, as _permissões efetivas_ da sua app serão o nível completo de privilégios implícitos na permissão. Por exemplo, uma aplicação que tenha o _User.ReadWrite.All_ a permissão de aplicação pode atualizar o perfil de cada utilizador na organização.

## <a name="openid-connect-scopes"></a>Âmbitos de ligação OpenID

A implementação da plataforma de identidade da Microsoft do OpenID Connect tem alguns âmbitos bem definidos que também estão hospedados no Microsoft Graph: `openid` `email` , e `profile` `offline_access` . Os `address` `phone` âmbitos de ligação e OpenID não são suportados.

Solicitar os âmbitos OIDC e um token dar-lhe-á um símbolo para ligar para o [ponto final do UserInfo](userinfo.md).

### <a name="openid"></a>openid

Se uma aplicação realizar o sômtindo através do [OpenID Connect,](active-directory-v2-protocols.md)deve solicitar o `openid` âmbito. O `openid` âmbito mostra na página de consentimento da conta de trabalho como a permissão "Iniciar-te" e na página pessoal de consentimento da conta da Microsoft como a permissão "Ver o seu perfil e ligar-se a aplicações e serviços utilizando a sua conta Microsoft". Com esta permissão, uma aplicação pode receber um identificador único para o utilizador sob a forma da `sub` reclamação. Também dá acesso à aplicação ao ponto final do UserInfo. O `openid` âmbito pode ser usado no ponto final da plataforma de identidade da Microsoft para adquirir fichas de ID, que podem ser usadas pela app para autenticação.

### <a name="email"></a>e-mail

O `email` âmbito pode ser usado com o âmbito e `openid` quaisquer outros. Dá acesso à aplicação ao principal endereço de e-mail do utilizador sob a forma da `email` reclamação. A `email` reclamação só está incluída num token se um endereço de e-mail estiver associado à conta de utilizador, o que nem sempre acontece. Se utilizar o `email` âmbito, a sua aplicação deve estar preparada para lidar com um caso em que a `email` reclamação não exista no token.

### <a name="profile"></a>perfil

O `profile` âmbito pode ser usado com o âmbito e `openid` quaisquer outros. Dá à aplicação acesso a uma quantidade substancial de informação sobre o utilizador. A informação a que pode aceder inclui, mas não se limita a, o nome próprio do utilizador, apelido, nome de utilizador preferido e ID de objeto. Para obter uma lista completa das reclamações de perfil disponíveis no parâmetro id_tokens para um utilizador específico, consulte a [ `id_tokens` referência](id-tokens.md).

### <a name="offline_access"></a>offline_access

O [ `offline_access` âmbito](https://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) dá à sua aplicação acesso a recursos em nome do utilizador por um longo período de tempo. Na página de consentimento, este âmbito aparece como a permissão "Manter o acesso aos dados a que lhe deu acesso". Quando um utilizador aprova o âmbito, a `offline_access` sua aplicação pode receber fichas de atualização a partir do ponto final da plataforma de identidade da Microsoft. As fichas de atualização são de longa duração. A sua aplicação pode obter novos tokens de acesso à medida que os mais velhos expiram.

> [!NOTE]
> Esta permissão aparece hoje em todos os ecrãs de consentimento, mesmo para fluxos que não fornecem um token de atualização (o [fluxo implícito).](v2-oauth2-implicit-grant-flow.md)  Isto é para cobrir cenários em que um cliente pode começar dentro do fluxo implícito, e depois passar para o fluxo de código onde é esperado um token de atualização.

Na plataforma de identidade da Microsoft (pedidos feitos para o ponto final v2.0), a sua aplicação deve solicitar explicitamente o `offline_access` âmbito, para receber fichas de atualização. Isto significa que quando resgatar um código de autorização no fluxo de código de [autorização OAuth 2.0,](active-directory-v2-protocols.md)receberá apenas um sinal de acesso a partir do `/token` ponto final. O token de acesso é válido por um curto período de tempo. O sinal de acesso geralmente expira em uma hora. Nessa altura, a sua aplicação precisa de redirecionar o utilizador de volta para o `/authorize` ponto final para obter um novo código de autorização. Durante este redirecionamento, dependendo do tipo de aplicação, o utilizador poderá ter de introduzir novamente as suas credenciais ou voltar a consentir com permissões.

Para obter mais informações sobre como obter e utilizar fichas de atualização, consulte a referência do protocolo da [plataforma de identidade](active-directory-v2-protocols.md)da Microsoft .

## <a name="requesting-individual-user-consent"></a>Solicitando consentimento individual do utilizador

Num pedido de autorização [OpenID Connect ou OAuth 2.0,](active-directory-v2-protocols.md) uma aplicação pode solicitar as permissões de que necessita utilizando o `scope` parâmetro de consulta. Por exemplo, quando um utilizador assina uma aplicação, a aplicação envia um pedido como o seguinte exemplo (com quebras de linha adicionadas para legibilidade):

```HTTP
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

O `scope` parâmetro é uma lista separada do espaço de permissões delegadas que a aplicação está a solicitar. Cada permissão é indicada através da anexação do valor de permissão ao identificador do recurso (o ID URI da aplicação). No exemplo do pedido, a aplicação necessita de permissão para ler o calendário do utilizador e enviar o correio como utilizador.

Depois de o utilizador introduzir as suas credenciais, o ponto final da plataforma de identidade da Microsoft verifica se há um registo correspondente do consentimento do *utilizador*. Se o utilizador não tiver consentido em nenhuma das permissões solicitadas no passado, nem um administrador consentiu nestas permissões em nome de toda a organização, o ponto final da plataforma de identidade da Microsoft pede ao utilizador que conceda as permissões solicitadas.

> [!NOTE]
>Neste momento, as `offline_access` permissões ("Manter o acesso aos dados a que lhe deu acesso") e `user.read` ("Iniciar sessão e ler o seu perfil") são automaticamente incluídas no consentimento inicial para uma aplicação.  Estas permissões são geralmente necessárias para uma funcionalidade adequada da aplicação - `offline_access` dá à app acesso a tokens de atualização, crítico para aplicações nativas e web, ao mesmo tempo que dá acesso à `user.read` `sub` reivindicação, permitindo ao cliente ou app identificar corretamente o utilizador ao longo do tempo e aceder a informações rudimentares do utilizador.

![Imagem de exemplo que mostra o consentimento da conta de trabalho](./media/v2-permissions-and-consent/work_account_consent.png)

Quando o utilizador aprova o pedido de permissão, o consentimento é registado e o utilizador não tem de voltar a consentir em inscrições subsequentes à aplicação.

## <a name="requesting-consent-for-an-entire-tenant"></a>Solicitando consentimento para um inquilino inteiro

Muitas vezes, quando uma organização compra uma licença ou subscrição para uma aplicação, a organização quer configurar proativamente o pedido de utilização por todos os membros da organização. Como parte deste processo, um administrador pode conceder consentimento para que o pedido aja em nome de qualquer utilizador no arrendatário. Se o administrador conceder o consentimento para todo o inquilino, os utilizadores da organização não verão uma página de consentimento para o pedido.

Para solicitar o consentimento para permissões delegadas para todos os utilizadores de um inquilino, a sua aplicação pode usar o ponto final de consentimento administrativo.

Além disso, as aplicações devem usar o ponto final de consentimento administrativo para solicitar permissões de aplicação.

## <a name="admin-restricted-permissions"></a>Permissões restritas a administradores

Algumas permissões de alto privilégio no ecossistema da Microsoft podem ser definidas como restritas à *administração.* Exemplos deste tipo de permissões incluem:

* Leia todos os perfis completos do utilizador usando `User.Read.All`
* Escreva dados para o diretório de uma organização usando `Directory.ReadWrite.All`
* Leia todos os grupos no diretório de uma organização usando `Groups.Read.All`

Apesar de um utilizador de consumo poder conceder acesso a este tipo de dados, os utilizadores organizacionais estão impedidos de conceder acesso ao mesmo conjunto de dados sensíveis da empresa. Se a sua aplicação solicitar o acesso a uma destas permissões de um utilizador organizacional, o utilizador recebe uma mensagem de erro que diz não estar autorizado a consentir com as permissões da sua aplicação.

Se a sua aplicação necessitar de acesso a âmbitos restritos de administração para organizações, deverá solicitá-los diretamente a um administrador da empresa, também utilizando o ponto final de consentimento de administração, descrito em seguida.

Se o pedido estiver a solicitar permissões delegadas de alto privilégio e um administrador conceder essas permissões através do ponto final de consentimento administrativo, o consentimento é concedido a todos os utilizadores do arrendatário.

Se o pedido estiver a solicitar permissões de pedido e um administrador conceder essas permissões através do ponto final de consentimento administrativo, esta subvenção não é feita em nome de nenhum utilizador específico. Em vez disso, o pedido do cliente é concedido *permissões diretamente*. Este tipo de permissões são utilizados apenas por serviços da Daemon e outras aplicações não interativas que funcionam em segundo plano.

## <a name="using-the-admin-consent-endpoint"></a>Usando o ponto final de consentimento administrativo

> [!NOTE]
> Por favor, note depois de conceder o consentimento administrativo usando o ponto final de consentimento administrativo, terminou de conceder o consentimento administrativo e os utilizadores não precisam de realizar mais ações adicionais. Após a concessão do consentimento administrativo, os utilizadores podem obter um token de acesso através de um fluxo típico de auth e o token de acesso resultante terá as permissões consentidas.

Quando um Administrador da Empresa utiliza a sua aplicação e é direcionado para o ponto final autorizado, a plataforma de identidade da Microsoft irá detetar a função do utilizador e perguntar-lhes se pretende consentir em nome de todo o arrendatário as permissões que solicitou. No entanto, existe também um ponto final de consentimento de administração dedicado que pode usar se quiser solicitar proactivamente que um administrador conceda permissão em nome de todo o inquilino. A utilização deste ponto final também é necessária para solicitar permissões de aplicação (que não podem ser solicitadas usando o ponto final autorizado).

Se seguir estes passos, a sua aplicação pode solicitar permissões para todos os utilizadores de um inquilino, incluindo âmbitos restritos a administradores. Esta é uma operação de alto privilégio e só deve ser feita se necessário para o seu cenário.

Para ver uma amostra de código que implemente os passos, consulte a [amostra de âmbitos restritos a administração](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2).

### <a name="request-the-permissions-in-the-app-registration-portal"></a>Solicite as permissões no portal de registo de aplicações

As aplicações podem observar quais as permissões que necessitam (delegadas e aplicações) no portal de registo de aplicações.  Isto permite a utilização do `/.default` âmbito e a opção "Grant admin consent" do portal Azure.  Em geral, é melhor garantir que as permissões estáticamente definidas para uma determinada aplicação são um superconjunto das permissões que irá solicitar dinamicamente/incrementalmente.

> [!NOTE]
>As permissões de aplicação só podem ser solicitadas através da utilização de [`/.default`](#the-default-scope) - por isso, se a sua aplicação precisar de permissões de aplicação, certifique-se de que estão listadas no portal de registo de aplicações.

#### <a name="to-configure-the-list-of-statically-requested-permissions-for-an-application"></a>Para configurar a lista de permissões estáticais solicitadas para um pedido

1. Aceda à sua aplicação no [portal Azure – Experiência de registos de aplicações,](https://go.microsoft.com/fwlink/?linkid=2083908) ou [crie uma app](quickstart-register-app.md) se ainda não o fez.
2. Localizar a secção **permissões API** e dentro das permissões API clique em Adicionar uma permissão.
3. Selecione o **Microsoft Graph** a partir da lista de APIs disponíveis e, em seguida, adicione as permissões que a sua aplicação necessita.
3. **Guarde** o registo da aplicação.

### <a name="recommended-sign-the-user-into-your-app"></a>Recomendado: Inscreva o utilizador na sua aplicação

Normalmente, quando constrói uma aplicação que utiliza o ponto final de consentimento administrativo, a aplicação precisa de uma página ou vista na qual o administrador pode aprovar as permissões da app. Esta página pode fazer parte do fluxo de inscrição da aplicação, parte das definições da aplicação, ou pode ser um fluxo dedicado de "connect". Em muitos casos, faz sentido que a app mostre esta vista de "conectar" apenas depois de um utilizador ter assinado com uma conta microsoft de trabalho ou escola.

Quando assinar o utilizador na sua aplicação, pode identificar a organização a que o administrador pertence antes de pedir-lhes que aprovem as permissões necessárias. Embora não seja estritamente necessário, pode ajudá-lo a criar uma experiência mais intuitiva para os seus utilizadores organizacionais. Para iniciar sômsim do utilizador, siga os [nossos tutoriais de protocolo de plataforma de identidade da Microsoft.](active-directory-v2-protocols.md)

### <a name="request-the-permissions-from-a-directory-admin"></a>Solicite as permissões a um administrador de diretório

Quando estiver pronto para solicitar permissões do administrador da sua organização, pode redirecionar o utilizador para o *ponto final de consentimento da*plataforma de identidade da Microsoft.

```HTTP
// Line breaks are for legibility only.
GET https://login.microsoftonline.com/{tenant}/v2.0/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
&scope=
https://graph.microsoft.com/calendars.read
https://graph.microsoft.com/mail.send
```


| Parâmetro        | Condição        | Descrição                                                                                |
|:--------------|:--------------|:-----------------------------------------------------------------------------------------|
| `tenant` | Obrigatório | O inquilino do diretório que quer pedir autorização. Pode ser fornecido em formato guiado ou de nome amigável ou genericamente referenciado com organizações como visto no exemplo. Não utilize 'comum', uma vez que as contas pessoais não podem fornecer consentimento administrativo, exceto no contexto de um inquilino. Para garantir a melhor compatibilidade com contas pessoais que gerem os inquilinos, utilize o ID do inquilino sempre que possível. |
| `client_id` | Necessário | O **ID da Aplicação (cliente)** que o [portal Azure – Experiência de registos de aplicações](https://go.microsoft.com/fwlink/?linkid=2083908) atribuído à sua app. |
| `redirect_uri` | Necessário |O URI de redirecionamento onde deseja que a resposta seja enviada para que a sua aplicação seja tratada. Deve corresponder exatamente a um dos URIs redirecionados que registou no portal de registo de aplicações. |
| `state` | Recomendado | Um valor incluído no pedido que também será devolvido na resposta simbólica. Pode ser uma série de conteúdos que quiser. Utilize o estado para codificar informações sobre o estado do utilizador na aplicação antes do pedido de autenticação ocorrer, como a página ou a visualização em que se encontravam. |
|`scope`        | Necessário        | Define o conjunto de permissões solicitadas pela aplicação. Isto pode ser estático [`/.default`](#the-default-scope) (utilizando) ou âmbitos dinâmicos.  Isto pode incluir os âmbitos OIDC `openid` (, `profile` , . `email` . Se precisar de permissões de aplicação, deve usar `/.default` para solicitar a lista de permissões estáticamente configuradas.  |


Neste momento, a Azure AD exige que um administrador de inquilino assine para completar o pedido. Pede-se ao administrador que aprove todas as permissões que solicitou no `scope` parâmetro.  Se tiver usado um valor estático `/.default` () funcionará como o ponto final de consentimento de administração v1.0 e solicitará o consentimento para todos os âmbitos encontrados nas permissões necessárias para a aplicação.

#### <a name="successful-response"></a>Resposta bem sucedida

Se o administrador aprovar as permissões para a sua aplicação, a resposta bem sucedida é a seguinte:

```HTTP
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parâmetro | Descrição |
| --- | --- |
| `tenant` | O inquilino do diretório que concedeu ao seu pedido as permissões que solicitou, em formato GUID. |
| `state` | Um valor incluído no pedido que também será devolvido na resposta simbólica. Pode ser uma série de conteúdos que quiser. O Estado é utilizado para codificar informações sobre o estado do utilizador na aplicação antes do pedido de autenticação ocorrer, como a página ou a visualização em que se encontravam. |
| `admin_consent` | Será definido para `True` . |

#### <a name="error-response"></a>Resposta de erro

Se o administrador não aprovar as permissões para a sua aplicação, a resposta falhada é a seguinte:

```HTTP
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parâmetro | Descrição |
| --- | --- |
| `error` | Uma cadeia de código de erro que pode ser usada para classificar tipos de erros que ocorrem e pode ser usado para reagir a erros. |
| `error_description` | Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa principal de um erro. |

Depois de ter recebido uma resposta bem sucedida do ponto final de consentimento administrativo, a sua aplicação obteve as permissões que solicitou. Em seguida, pode pedir um sinal para o recurso que quiser.

## <a name="using-permissions"></a>Usando permissões

Depois de o utilizador consentir com permissões para a sua aplicação, a sua aplicação pode adquirir fichas de acesso que representem a permissão da sua app para aceder a um recurso em alguma capacidade. Um token de acesso pode ser usado apenas para um único recurso, mas codificado dentro do token de acesso é cada permissão que a sua app foi concedida para esse recurso. Para adquirir um token de acesso, a sua aplicação pode fazer um pedido para o ponto final da plataforma de identidade da Microsoft, como este:

```HTTP
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

Pode utilizar o token de acesso resultante em pedidos HTTP ao recurso. Indica de forma fiável o recurso que a sua aplicação tem a permissão adequada para executar uma tarefa específica.

Para obter mais informações sobre o protocolo OAuth 2.0 e como obter tokens de acesso, consulte a referência do [protocolo de ponto final](active-directory-v2-protocols.md)da plataforma de identidade da Microsoft .

## <a name="the-default-scope"></a>O âmbito /.predefinido

Pode utilizar o `/.default` âmbito para ajudar a migrar as suas aplicações do ponto final v1.0 para o ponto final da plataforma de identidade da Microsoft. Este é um âmbito incorporado para cada aplicação que se refere à lista estática de permissões configuradas no registo de pedidos. Um `scope` valor de é funcionalmente o mesmo que os `https://graph.microsoft.com/.default` pontos finais v1.0 `resource=https://graph.microsoft.com` - ou seja, solicita um token com os âmbitos no Microsoft Graph para os que a aplicação se registou no portal Azure.  É construído utilizando o recurso URI + `/.default` (por exemplo, se o recurso URI `https://contosoApp.com` for, então o âmbito solicitado será `https://contosoApp.com/.default` ).  Consulte a secção sobre cortes de rasto para [casos](#trailing-slash-and-default) em que deve incluir um segundo corte para solicitar corretamente o token.

O âmbito /.predefinido pode ser utilizado em qualquer fluxo OAuth 2.0, mas é necessário no fluxo de fluxo e credenciais [de](v2-oauth2-on-behalf-of-flow.md) [cliente,](v2-oauth2-client-creds-grant-flow.md)bem como ao utilizar o ponto final de consentimento de administração v2 para solicitar permissões de pedido.

> [!NOTE]
> Os clientes não podem combinar estática `/.default` () e consentimento dinâmico num único pedido. Assim, `scope=https://graph.microsoft.com/.default+mail.read` resultará num erro devido à combinação de tipos de âmbito.

### <a name="default-and-consent"></a>/.predefinição e consentimento

O `/.default` âmbito também despoleta o comportamento do ponto final v1.0. `prompt=consent` Solicita o consentimento de todas as permissões registadas pelo pedido, independentemente do recurso. Se incluído como parte do pedido, o `/.default` âmbito devolve um token que contém os âmbitos para o recurso solicitado.

### <a name="default-when-the-user-has-already-given-consent"></a>/.predefinição quando o utilizador já deu consentimento

Porque `/.default` é funcionalmente idêntico ao `resource` comportamento do ponto final de v1.0 centrado, traz consigo o comportamento de consentimento do ponto final v1.0 também. Ou seja, `/.default` apenas aciona um pedido de consentimento se não tiver sido concedida qualquer autorização entre o cliente e o recurso pelo utilizador. Se existir tal consentimento, então um sinal será devolvido contendo todos os âmbitos concedidos pelo utilizador para esse recurso. No entanto, se não tiver sido concedida qualquer autorização, ou se o `prompt=consent` parâmetro tiver sido fornecido, será mostrado um pedido de consentimento para todos os âmbitos registados pelo pedido do cliente.

#### <a name="example-1-the-user-or-tenant-admin-has-granted-permissions"></a>Exemplo 1: O utilizador, ou administrador inquilino, concedeu permissões

Neste exemplo, o utilizador (ou administrador de inquilino) concedeu ao cliente as permissões do Microsoft Graph `mail.read` e `user.read` . Se o cliente fizer um pedido de `scope=https://graph.microsoft.com/.default` , então não será mostrado qualquer pedido de consentimento independentemente do conteúdo das permissões registadas pelas aplicações do cliente para o Microsoft Graph. Seria devolvido um símbolo contendo os âmbitos `mail.read` e `user.read` .

#### <a name="example-2-the-user-hasnt-granted-permissions-between-the-client-and-the-resource"></a>Exemplo 2: O utilizador não concedeu permissões entre o cliente e o recurso

Neste exemplo, não existe qualquer consentimento para o utilizador entre o cliente e o Microsoft Graph. O cliente registou-se para as `user.read` permissões e `contacts.read` permissões, bem como o âmbito Azure Key Vault `https://vault.azure.net/user_impersonation` . Quando o cliente solicitar um token `scope=https://graph.microsoft.com/.default` para, o utilizador verá um ecrã de consentimento para os `user.read` `contacts.read` mirais de , e o teclado do Cofre `user_impersonation` de Chaves. O token devolvido terá apenas os `user.read` `contacts.read` e âmbitos nele e só será utilizável contra o Microsoft Graph.

#### <a name="example-3-the-user-has-consented-and-the-client-requests-additional-scopes"></a>Exemplo 3: O utilizador consentiu e o cliente solicita âmbitos adicionais

Neste exemplo, o utilizador já consentiu `mail.read` com o cliente. O cliente registou-se para o `contacts.read` âmbito de aplicação da sua inscrição. Quando o cliente fizer um pedido de um token usando `scope=https://graph.microsoft.com/.default` e solicita o consentimento através , `prompt=consent` então o utilizador verá um ecrã de consentimento para todos (e apenas) as permissões registadas pela aplicação. `contacts.read` estará presente no ecrã de consentimento, mas `mail.read` não estará. O token devolvido será para o Microsoft Graph e irá conter `mail.read` e `contacts.read` .

### <a name="using-the-default-scope-with-the-client"></a>Utilizando o âmbito /.predefinido com o cliente

Existe um caso especial do `/.default` âmbito em que um cliente solicita o seu próprio `/.default` âmbito. O exemplo que se segue demonstra este cenário.

```HTTP
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
response_type=token            //code or a hybrid flow is also possible here
&client_id=9ada6f8a-6d83-41bc-b169-a306c21527a5
&scope=9ada6f8a-6d83-41bc-b169-a306c21527a5/.default
&redirect_uri=https%3A%2F%2Flocalhost
&state=1234
```

Isto produz um ecrã de consentimento para todas as permissões registadas (se aplicável com base nas descrições acima de consentimento `/.default` e), em seguida, devolve um id_token, em vez de um token de acesso.  Este comportamento existe para certos clientes legados que se deslocam da ADAL para a MSAL, e **não devem** ser utilizados por novos clientes direcionados para o ponto final da plataforma de identidade da Microsoft.

### <a name="client-credentials-grant-flow-and-default"></a>Fluxo de concessão de credenciais de cliente e /.default

Outra utilização `./default` é quando se solicita permissões de aplicação (ou *funções)* numa aplicação não interativa como uma app daemon que utiliza o fluxo de concessão de [credenciais](v2-oauth2-client-creds-grant-flow.md) do cliente para chamar uma API web.

Para criar permissões de aplicação (funções) para uma API web, consulte [Como: Adicionar funções de aplicação na sua aplicação.](howto-add-app-roles-in-azure-ad-apps.md)

Os pedidos de credenciais de cliente na sua aplicação **de clientes devem** `scope={resource}/.default` incluir, onde está a `{resource}` API web que a sua aplicação pretende ligar. A emissão de um pedido de credenciais de cliente com permissões individuais de aplicação (funções) **não** é suportada. Todas as permissões de aplicação (funções) que tenham sido concedidas para essa API web serão incluídas no token de acesso devolvido.

Para conceder acesso às permissões de candidatura que define, incluindo a concessão de consentimento administrativo para a aplicação, consulte [Quickstart: Configure uma aplicação do cliente para aceder a uma API web.](quickstart-configure-app-access-web-apis.md)

### <a name="trailing-slash-and-default"></a>Corte de fuga e /.padrão

Alguns URIs de recursos têm um corte de fuga ( `https://contoso.com/` ao contrário `https://contoso.com` de), que pode causar problemas com a validação simbólica.  Isto pode ocorrer principalmente quando se solicita um símbolo para a Azure Resource Management ( `https://management.azure.com/` ), que tem um corte de fuga no seu recurso URI e exige que esteja presente quando o token é solicitado.  Assim, ao solicitar um token para `https://management.azure.com/` e utilizar , deve solicitar - tome nota do duplo `/.default` `https://management.azure.com//.default` corte!

Em geral - se você validou que o token está sendo emitido, e o token está sendo rejeitado pela API que deve aceitá-lo, considere adicionar um segundo corte e tentar novamente. Isto acontece porque o servidor de login emite um token com o público que corresponde aos URIs no `scope` parâmetro - com removido a partir do `/.default` final.  Se isto remover o corte de fuga, o servidor de login ainda processa o pedido e valida-o contra o recurso URI, mesmo que já não correspondam - este não é padrão e não deve ser invocado pela sua aplicação.

## <a name="troubleshooting-permissions-and-consent"></a>Permissões e consentimento para resolução de problemas

Se você ou os utilizadores da sua aplicação estiverem a ver erros inesperados durante o processo de consentimento, consulte este artigo para etapas de resolução de problemas: [Erro inesperado ao efetuar o consentimento de uma aplicação](../manage-apps/application-sign-in-unexpected-user-consent-error.md).

## <a name="next-steps"></a>Passos seguintes

* [Fichas de ID Plataforma de identidade da Microsoft](id-tokens.md)
* [Fichas de acesso Plataforma de identidade da Microsoft](access-tokens.md)

---
title: Âmbitos, permissões, & consentimento da plataforma de identidade da Microsoft
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
ms.custom: aaddev, fasttrack-edit, contperf-fy21q1, identityplatformtop40
ms.openlocfilehash: bdcfb0adf2c92fa6e084c2efbc2e5c066a3e3ede
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305859"
---
# <a name="permissions-and-consent-in-the-microsoft-identity-platform"></a>Permissões e consentimento na plataforma de identidade da Microsoft

As aplicações que se integram com a plataforma de identidade da Microsoft seguem um modelo de autorização que dá aos utilizadores e administradores o controlo sobre a forma como os dados podem ser acedidos. A implementação do modelo de autorização foi atualizada na plataforma de identidade da Microsoft. Altera a forma como uma aplicação deve interagir com a plataforma de identidade da Microsoft. Este artigo abrange os conceitos básicos deste modelo de autorização, incluindo âmbitos, permissões e consentimento.

## <a name="scopes-and-permissions"></a>Âmbitos e permissões

A plataforma de identidade da Microsoft implementa o protocolo de autorização [OAuth 2.0.](active-directory-v2-protocols.md) O OAuth 2.0 é um método através do qual uma aplicação de terceiros pode aceder a recursos hospedados na Web em nome de um utilizador. Qualquer recurso hospedado na Web que se integre com a plataforma de identidade da Microsoft tem um identificador de recursos, ou *ID URI de aplicação.* 

Aqui estão alguns exemplos de recursos hospedados na Microsoft na Web:

* Microsoft Graph: `https://graph.microsoft.com`
* Microsoft 365 Mail API: `https://outlook.office.com`
* Cofre da Chave Azure: `https://vault.azure.net`

O mesmo acontece com os recursos de terceiros que se integraram na plataforma de identidade da Microsoft. Qualquer um destes recursos também pode definir um conjunto de permissões que podem ser usadas para dividir a funcionalidade desse recurso em pedaços menores. Como exemplo, [o Microsoft Graph](https://graph.microsoft.com) definiu permissões para fazer as seguintes tarefas, entre outras:

* Leia o calendário de um utilizador
* Escreva para o calendário de um utilizador
* Enviar correio como utilizador

Devido a este tipo de definições de permissão, o recurso tem um controlo fino sobre os seus dados e como a funcionalidade API está exposta. Uma aplicação de terceiros pode solicitar estas permissões aos utilizadores e administradores, que devem aprovar o pedido antes de a aplicação poder aceder aos dados ou agir em nome de um utilizador. 

Quando a funcionalidade de um recurso é dividida em pequenos conjuntos de permissões, as aplicações de terceiros podem ser construídas para solicitar apenas as permissões que precisam para executar a sua função. Os utilizadores e administradores podem saber a que dados a aplicação pode aceder. E podem estar mais confiantes de que a aplicação não se está a comportar com intenções maliciosas. Os desenvolvedores devem sempre respeitar o princípio do menor privilégio, pedindo apenas as permissões de que necessitam para que as suas aplicações funcionem.

Em OAuth 2.0, estes tipos de conjuntos de permissões são *chamados de âmbitos*. Também são frequentemente referidos como *permissões.* Na plataforma de identidade da Microsoft, uma permissão é representada como um valor de cadeia. Para o exemplo do Microsoft Graph, aqui está o valor de cadeia para cada permissão:

* Leia o calendário de um utilizador usando `Calendars.Read`
* Escreva para o calendário de um utilizador usando `Calendars.ReadWrite`
* Enviar correio como um utilizador que utiliza por `Mail.Send`

Uma aplicação solicita mais frequentemente estas permissões especificando os âmbitos de aplicação em pedidos para a plataforma de identidade da Microsoft autorizar o ponto final. No entanto, algumas permissões de alto privilégio só podem ser concedidas através do consentimento do administrador. Podem ser solicitados ou concedidos utilizando o [ponto final](#admin-restricted-permissions)de consentimento do administrador . Continua a ler para saber mais.

## <a name="permission-types"></a>Tipos de permissão

A plataforma de identidade da Microsoft suporta dois tipos de permissões: *permissões delegadas* e *permissões de aplicação.*

* **As permissões delegadas** são utilizadas por apps que tenham um utilizador inscrito presente. Para estas aplicações, o utilizador ou um administrador consente com as permissões que a aplicação solicita. A aplicação é delegada permissão para agir como o utilizador inscrito quando faz chamadas para o recurso alvo. 

    Algumas permissões delegadas podem ser consentidas por nãoministradores. Mas algumas permissões privilegiadas requerem [o consentimento do administrador.](#admin-restricted-permissions) Para saber quais as funções de administrador que podem consentir com permissões delegadas, consulte [permissões de função de administrador no Diretório Ativo Azure (Azure AD)](../roles/permissions-reference.md).

* **As permissões de aplicação** são utilizadas por apps que funcionam sem um utilizador inscrito presente, por exemplo, aplicações que funcionam como serviços de fundo ou daemons. Apenas [um administrador pode consentir com permissões de](#requesting-consent-for-an-entire-tenant) candidatura.

_Permissões eficazes_ são as permissões que a sua aplicação tem quando faz pedidos ao recurso-alvo. É importante entender a diferença entre as permissões delegadas e as permissões de aplicação que a sua aplicação é concedida, e as permissões efetivas que a sua app é concedida quando faz chamadas para o recurso alvo.

- Para permissões delegadas, as _permissões efetivas_ da sua app são a intersecção menos privilegiada das permissões delegadas que a app foi concedida (por consentimento) e os privilégios do utilizador atualmente inscrito. A aplicação nunca pode ter mais privilégios do que o utilizador com sessão iniciada. 

   Dentro das organizações, os privilégios do utilizador inscrito podem ser determinados por política ou por adesão a uma ou mais funções de administrador. Para saber quais as funções de administrador que podem consentir com permissões delegadas, consulte [permissões de função do Administrador em Azure AD](../roles/permissions-reference.md).

   Por exemplo, assuma que a sua aplicação foi concedida ao _Utilizador.ReadWrite.Toda a_ permissão delegada. Esta permissão concede nominalmente permissão à aplicação para ler e atualizar o perfil de todos os utilizadores de uma organização. Se o utilizador inscrito for um administrador global, a sua aplicação pode atualizar o perfil de cada utilizador da organização. No entanto, se o utilizador inscrito não tiver uma função de administrador, a sua aplicação pode atualizar apenas o perfil do utilizador inscrito. Não é possível atualizar os perfis de outros utilizadores da organização porque o utilizador que tem permissão para agir em nome de não tem esses privilégios.

- Para permissões de aplicação, as _permissões efetivas_ da sua app são o nível completo de privilégios implícitos na permissão. Por exemplo, uma aplicação que tenha o _User.ReadWrite.All_ a permissão de aplicação pode atualizar o perfil de cada utilizador na organização.

## <a name="openid-connect-scopes"></a>Âmbitos de ligação OpenID

A implementação da plataforma de identidade da Microsoft do OpenID Connect tem alguns âmbitos bem definidos que também estão hospedados no Microsoft Graph: `openid` `email` . . `profile` . `offline_access` Os `address` `phone` âmbitos de ligação e OpenID não são suportados.

Se solicitar os âmbitos de ligação OpenID e um token, receberá um token para ligar para o [ponto final do UserInfo.](userinfo.md)

### <a name="openid"></a>openid

Se uma aplicação se apresentar utilizando [o OpenID Connect,](active-directory-v2-protocols.md)deve solicitar o `openid` âmbito. O `openid` âmbito aparece na página de consentimento da conta de trabalho como a permissão **do Signo.o.** Na página pessoal de consentimento da conta da Microsoft, aparece como o perfil de Ver o **seu perfil e conectar-se a aplicações e serviços usando a** permissão da sua conta Microsoft. 

Ao utilizar esta permissão, uma aplicação pode receber um identificador único para o utilizador sob a forma da `sub` reclamação. A permissão também dá acesso à aplicação ao ponto final do UserInfo. O `openid` âmbito pode ser usado no ponto final da plataforma de identidade da Microsoft para adquirir fichas de ID. A aplicação pode usar estes tokens para autenticação.

### <a name="email"></a>e-mail

O `email` âmbito pode ser utilizado com o âmbito e `openid` quaisquer outros âmbitos. Dá acesso à aplicação ao principal endereço de e-mail do utilizador sob a forma da `email` reclamação. 

A `email` reclamação só está incluída num token se um endereço de e-mail estiver associado à conta de utilizador, o que nem sempre acontece. Se a sua aplicação utilizar o `email` âmbito, a aplicação precisa de ser capaz de lidar com um caso em que não exista qualquer `email` reclamação no token.

### <a name="profile"></a>perfil

O `profile` âmbito pode ser utilizado com o âmbito e qualquer outro `openid` âmbito. Dá à aplicação acesso a uma grande quantidade de informação sobre o utilizador. A informação a que pode aceder inclui, mas não se limita a, o nome próprio do utilizador, apelido, nome de utilizador preferido e ID de objeto. 

Para obter uma lista completa das `profile` reclamações disponíveis no `id_tokens` parâmetro para um utilizador específico, consulte a [ `id_tokens` referência](id-tokens.md).

### <a name="offline_access"></a>offline_access

O [ `offline_access` âmbito](https://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) dá à sua aplicação acesso a recursos em nome do utilizador por um longo período de tempo. Na página de consentimento, este âmbito aparece como o **acesso manter aos dados que lhe deu acesso à** permissão. 

Quando um utilizador aprova o âmbito, a `offline_access` sua aplicação pode receber fichas de atualização a partir do ponto final da plataforma de identidade da Microsoft. As fichas de atualização são de longa duração. A sua aplicação pode obter novos tokens de acesso à medida que os mais velhos expiram.

> [!NOTE]
> Esta permissão aparece atualmente em todas as páginas de consentimento, mesmo para fluxos que não fornecem um token de atualização (como o [fluxo implícito).](v2-oauth2-implicit-grant-flow.md) Esta configuração aborda cenários em que um cliente pode começar dentro do fluxo implícito e, em seguida, mover-se para o fluxo de código onde é esperado um token de atualização.

Na plataforma de identidade da Microsoft (pedidos feitos para o ponto final v2.0), a sua aplicação deve solicitar explicitamente o `offline_access` âmbito, para receber fichas de atualização. Assim, quando resgatar um código de autorização no fluxo de código de [autorização OAuth 2.0,](active-directory-v2-protocols.md)receberá apenas um sinal de acesso a partir do `/token` ponto final. 

O token de acesso é válido por um curto período de tempo. Normalmente expira em uma hora. Nessa altura, a sua aplicação precisa de redirecionar o utilizador de volta para o `/authorize` ponto final para obter um novo código de autorização. Durante este redirecionamento, dependendo do tipo de aplicação, o utilizador poderá ter de introduzir novamente as suas credenciais ou voltar a consentir com permissões.

Para obter mais informações sobre como obter e utilizar fichas de atualização, consulte a referência do protocolo da [plataforma de identidade](active-directory-v2-protocols.md)da Microsoft .

## <a name="requesting-individual-user-consent"></a>Solicitando consentimento individual do utilizador

Num pedido de autorização [OpenID Connect ou OAuth 2.0,](active-directory-v2-protocols.md) uma aplicação pode solicitar as permissões de que necessita utilizando o `scope` parâmetro de consulta. Por exemplo, quando um utilizador assina uma aplicação, a aplicação envia um pedido como o seguinte exemplo. (As quebras de linha são adicionadas para a legibilidade.)

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

O `scope` parâmetro é uma lista separada do espaço de permissões delegadas que a aplicação está a solicitar. Cada permissão é indicada através da anexação do valor de permissão ao identificador do recurso (o ID URI de aplicação). No exemplo do pedido, a aplicação necessita de permissão para ler o calendário do utilizador e enviar o correio como utilizador.

Depois de o utilizador introduzir as suas credenciais, a plataforma de identidade da Microsoft verifica se há um registo correspondente do consentimento do *utilizador.* Se o utilizador não tiver consentido nenhuma das permissões solicitadas no passado, e se o administrador não tiver consentido estas permissões em nome de toda a organização, a plataforma de identidade da Microsoft pede ao utilizador que conceda as permissões solicitadas.

Neste momento, a `offline_access` permissão ("Manter o acesso aos dados a que lhe deu acesso") e `user.read` ("Iniciar e ler o seu perfil") são automaticamente incluídas no consentimento inicial de uma aplicação.  Estas permissões são geralmente necessárias para uma funcionalidade adequada da aplicação. A `offline_access` permissão dá à aplicação acesso a tokens de atualização que são fundamentais para aplicações nativas e aplicações web. A `user.read` permissão dá acesso à `sub` reclamação. Permite ao cliente ou app identificar corretamente o utilizador ao longo do tempo e aceder a informações rudimentares do utilizador.

![Imagem de exemplo que mostra o consentimento da conta de trabalho.](./media/v2-permissions-and-consent/work_account_consent.png)

Quando o utilizador aprova o pedido de permissão, o consentimento é registado. O utilizador não tem de voltar a consentir quando mais tarde iniciar a sua inscrição.

## <a name="requesting-consent-for-an-entire-tenant"></a>Solicitando consentimento para um inquilino inteiro

Quando uma organização compra uma licença ou subscrição para uma aplicação, a organização muitas vezes quer configurar proativamente o pedido de uso por todos os membros da organização. Como parte deste processo, um administrador pode conceder consentimento para que o pedido aja em nome de qualquer utilizador no arrendatário. Se o administrador conceder o consentimento para todo o inquilino, os utilizadores da organização não vêem uma página de consentimento para o pedido.

Para solicitar o consentimento para permissões delegadas para todos os utilizadores de um inquilino, a sua aplicação pode usar o ponto final de consentimento administrativo.

Além disso, os pedidos devem usar o ponto final de consentimento administrativo para solicitar permissões de pedido.

## <a name="admin-restricted-permissions"></a>Permissões restritas a administradores

Algumas permissões de alto privilégio nos recursos da Microsoft podem ser definidas como restritas a *administradores.* Aqui estão alguns exemplos deste tipo de permissões:

* Leia todos os perfis completos do utilizador usando `User.Read.All`
* Escreva dados para o diretório de uma organização usando `Directory.ReadWrite.All`
* Leia todos os grupos no diretório de uma organização usando `Groups.Read.All`

Apesar de um utilizador de consumo poder conceder acesso a este tipo de dados, os utilizadores organizacionais não podem conceder acesso ao mesmo conjunto de dados sensíveis da empresa. Se a sua aplicação solicitar o acesso a uma destas permissões de um utilizador organizacional, o utilizador recebe uma mensagem de erro que diz não estar autorizado a consentir com as permissões da sua aplicação.

Se a sua aplicação necessitar de âmbitos para permissões restritas a administradores, o administrador da organização deve consentir esses âmbitos em nome dos utilizadores da organização. Para evitar visualizar solicitações aos utilizadores que solicitam o consentimento para permissões que não podem conceder, a sua aplicação pode usar o ponto final de consentimento de administração. O ponto final de consentimento administrativo está coberto na secção seguinte.

Se o pedido solicitar permissões delegadas de alto privilégio e um administrador conceder essas permissões através do ponto final de consentimento administrativo, o consentimento é concedido a todos os utilizadores do arrendatário.

Se o pedido solicitar permissões de pedido e um administrador conceder estas permissões através do ponto final de consentimento administrativo, esta concessão não é feita em nome de qualquer utilizador específico. Em vez disso, o pedido do cliente é concedido *permissões diretamente*. Este tipo de permissões são utilizados apenas por serviços daemon e outras aplicações nãointerativas que funcionam em segundo plano.

## <a name="using-the-admin-consent-endpoint"></a>Usando o ponto final de consentimento administrativo

Depois de usar o ponto final de consentimento administrativo para conceder o consentimento administrativo, está acabado. Os utilizadores não precisam de tomar mais medidas. Após o consentimento administrativo ser concedido, os utilizadores podem obter um token de acesso através de um fluxo típico de auth. O token de acesso resultante tem as permissões consentidas.

Quando um Administrador Global utiliza a sua aplicação e é direcionado para o ponto final autorizado, a plataforma de identidade da Microsoft deteta o papel do utilizador. Pergunta se o Administrador Global quer consentir em nome de todo o inquilino para as permissões que pediu. Em vez disso, você poderia usar um ponto final de consentimento de administração dedicado para solicitar proativamente a um administrador para conceder permissão em nome de todo o inquilino. Este ponto final também é necessário para solicitar permissões de pedido. As permissões de inscrição não podem ser solicitadas utilizando o ponto final autorizado.

Se seguir estes passos, a sua aplicação pode solicitar permissões para todos os utilizadores de um inquilino, incluindo âmbitos restritos a administradores. Esta operação é altamente privilegiada. Utilize a operação apenas se necessário para o seu cenário.

Para ver uma amostra de código que implementa os passos, consulte a [amostra de âmbitos restritos a administração](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2) no GitHub.

### <a name="request-the-permissions-in-the-app-registration-portal"></a>Solicite as permissões no portal de registo de aplicações

No portal de registo de aplicações, as aplicações podem listar as permissões que necessitam, incluindo permissões delegadas e permissões de aplicação. Esta configuração permite a utilização do `/.default` âmbito e a opção de consentimento de **administração** do portal Azure.  

Em geral, as permissões devem ser definidas estáticamente para uma determinada aplicação. Devem ser um superconjunto das permissões que a app irá solicitar de forma dinâmica ou incremental.

> [!NOTE]
>As permissões de candidatura só podem ser solicitadas através da utilização de [`/.default`](#the-default-scope) . Portanto, se a sua aplicação precisa de permissões de aplicação, certifique-se de que estão listadas no portal de registo de aplicações.

Para configurar a lista de permissões estáticais solicitadas para um pedido:

1. Aceda à sua aplicação no <a href="https://go.microsoft.com/fwlink/?linkid=2083908" target="_blank">portal Azure - App registra</a> experiência de arranque rápido.
1. Selecione uma aplicação ou [crie uma aplicação](quickstart-register-app.md) se ainda não o fez.
1. Na **página** geral da aplicação, em **Gestão,** selecione **Permissões API**  >  **Adicione uma permissão**.
1. Selecione **o Microsoft Graph** na lista de APIs disponíveis. Em seguida, adicione as permissões que a sua aplicação requer.
1. Selecione **Permissões de Adicionar**.

### <a name="recommended-sign-the-user-in-to-your-app"></a>Recomendado: Inscreva o utilizador na sua aplicação

Normalmente, quando constrói uma aplicação que utiliza o ponto final de consentimento administrativo, a aplicação precisa de uma página ou vista na qual o administrador pode aprovar as permissões da app. Esta página pode ser:

* Parte do fluxo de inscrição da aplicação.
* Parte das definições da aplicação.
* Um fluxo dedicado de "ligar". 

Em muitos casos, faz sentido que a app mostre esta vista de "conectar" apenas depois de um utilizador ter assinado com uma conta microsoft de trabalho ou uma conta microsoft escolar.

Ao iniciar a sômissão do utilizador na sua aplicação, pode identificar a organização a que o administrador pertence antes de lhes pedir que aprovem as permissões necessárias. Embora este passo não seja estritamente necessário, pode ajudá-lo a criar uma experiência mais intuitiva para os seus utilizadores organizacionais. 

Para iniciar a sômin, siga os [tutoriais](active-directory-v2-protocols.md)do protocolo da plataforma de identidade da Microsoft .

### <a name="request-the-permissions-from-a-directory-admin"></a>Solicite as permissões a um administrador de diretório

Quando estiver pronto para solicitar permissões do administrador da sua organização, pode redirecionar o utilizador para o ponto final de consentimento da plataforma de identidade da Microsoft.

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
| `tenant` | Obrigatório | O inquilino do diretório que quer pedir autorização. Pode ser fornecido num formato DE NOME GUID ou amigável. Ou pode ser genericamente referenciado com organizações, como se pode ver no exemplo. Não use "comum", porque as contas pessoais não podem fornecer consentimento administrativo a não ser no contexto de um inquilino. Para garantir a melhor compatibilidade com contas pessoais que gerem os inquilinos, utilize o ID do inquilino sempre que possível. |
| `client_id` | Necessário | A aplicação (cliente) ID que o [portal Azure – Experiência de registos de aplicações](https://go.microsoft.com/fwlink/?linkid=2083908) atribuída à sua app. |
| `redirect_uri` | Necessário |O URI de redirecionamento onde deseja que a resposta seja enviada para que a sua aplicação seja tratada. Deve corresponder exatamente a um dos URIs redirecionados que registou no portal de registo de aplicações. |
| `state` | Recomendado | Um valor incluído no pedido que também será devolvido na resposta simbólica. Pode ser uma série de conteúdos que quiser. Utilize o estado para codificar informações sobre o estado do utilizador na aplicação antes do pedido de autenticação ocorrer, como a página ou a visualização em que se encontravam. |
|`scope`        | Necessário        | Define o conjunto de permissões solicitadas pela aplicação. Os âmbitos podem ser estáticos [`/.default`](#the-default-scope) (utilizando) ou dinâmicos.  Este conjunto pode incluir os âmbitos openID connect `openid` (, `profile` , . `email` . Se precisar de permissões de aplicação, deve usar `/.default` para solicitar a lista de permissões estáticamente configuradas.  |


Neste momento, a Azure AD exige que um administrador de inquilino assine para completar o pedido. Pede-se ao administrador que aprove todas as permissões que solicitou no `scope` parâmetro.  Se utilizar um valor estático `/.default` () funcionará como o ponto final de consentimento de administração v1.0 e solicitará o consentimento para todos os âmbitos encontrados nas permissões necessárias para a aplicação.

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
| `error` | Uma cadeia de código de erro que pode ser usada para classificar tipos de erros que ocorrem. Também pode ser usado para reagir a erros. |
| `error_description` | Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa principal de um erro. |

Depois de ter recebido uma resposta bem sucedida do ponto final de consentimento administrativo, a sua aplicação obteve as permissões que solicitou. Em seguida, pode pedir um sinal para o recurso que quiser.

## <a name="using-permissions"></a>Usando permissões

Depois de o utilizador consentir com permissões para a sua aplicação, a sua aplicação pode adquirir fichas de acesso que representem a permissão da app para aceder a um recurso em alguma capacidade. Um token de acesso só pode ser usado para um único recurso. Mas codificado dentro do token de acesso está todas as permissões que a sua app foi concedida para esse recurso. Para adquirir um token de acesso, a sua aplicação pode fazer um pedido para o ponto final da plataforma de identidade da Microsoft, como este:

```HTTP
POST common/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
    "grant_type": "authorization_code",
    "client_id": "6731de76-14a6-49ae-97bc-6eba6914391e",
    "scope": "https://outlook.office.com/mail.read https://outlook.office.com/mail.send",
    "code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...",
    "redirect_uri": "https://localhost/myapp",
    "client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

Pode utilizar o token de acesso resultante em pedidos HTTP ao recurso. Indica de forma fiável o recurso que a sua aplicação tem a permissão adequada para fazer uma tarefa específica.

Para obter mais informações sobre o protocolo OAuth 2.0 e como obter tokens de acesso, consulte a referência do [protocolo de ponto final](active-directory-v2-protocols.md)da plataforma de identidade da Microsoft .

## <a name="the-default-scope"></a>O âmbito /.predefinido

Pode utilizar o `/.default` âmbito para ajudar a migrar as suas aplicações do ponto final v1.0 para o ponto final da plataforma de identidade da Microsoft. O `/.default` âmbito é incorporado para cada aplicação que se refere à lista estática de permissões configuradas no registo de pedidos. 

Um `scope` valor de é `https://graph.microsoft.com/.default` funcionalmente o mesmo que no ponto final `resource=https://graph.microsoft.com` v1.0. Ao especificar o `https://graph.microsoft.com/.default` âmbito do seu pedido, a sua aplicação está a solicitar um token de acesso que inclui âmbitos para cada permissão do Microsoft Graph que selecionou para a aplicação no portal de registo de aplicações. O âmbito é construído utilizando o recurso URI e `/.default` . Assim, se o recurso URI `https://contosoApp.com` for, o âmbito solicitado é `https://contosoApp.com/.default` .  Para os casos em que deve incluir um segundo corte para solicitar corretamente o token, consulte a [secção sobre os cortes de rasto](#trailing-slash-and-default).

O `/.default` âmbito pode ser utilizado em qualquer fluxo OAuth 2.0. Mas é necessário no [fluxo on-behalf-of](v2-oauth2-on-behalf-of-flow.md) e credenciais de [clientes fluir.](v2-oauth2-client-creds-grant-flow.md) Também precisa quando utiliza o ponto final de consentimento de administração v2 para solicitar permissões de pedido.

Os clientes não podem combinar consentimento estático `/.default` () e consentimento dinâmico num único pedido. Assim, `scope=https://graph.microsoft.com/.default+mail.read` resulta num erro porque combina tipos de âmbito.

### <a name="default-and-consent"></a>/.predefinição e consentimento

O `/.default` âmbito também despoleta o comportamento do ponto final v1.0. `prompt=consent` Solicita o consentimento de todas as permissões que o pedido registou, independentemente do recurso. Se for incluído como parte do pedido, o `/.default` âmbito devolve um token que contém os âmbitos para o recurso solicitado.

### <a name="default-when-the-user-has-already-given-consent"></a>/.predefinição quando o utilizador já deu consentimento

O `/.default` âmbito é funcionalmente idêntico ao comportamento do `resource` ponto final -centrado v1.0. Também tem o comportamento de consentimento do ponto final v1.0. Ou seja, `/.default` só aciona uma solicitação de consentimento se o utilizador não tiver dado qualquer autorização entre o cliente e o recurso. 

Se existir tal consentimento, o token devolvido contém todos os âmbitos que o utilizador concedeu para esse recurso. No entanto, se não tiver sido concedida qualquer autorização ou se o `prompt=consent` parâmetro tiver sido fornecido, é mostrado um pedido de consentimento para todos os âmbitos que o pedido do cliente registou.

#### <a name="example-1-the-user-or-tenant-admin-has-granted-permissions"></a>Exemplo 1: O utilizador, ou administrador inquilino, concedeu permissões

Neste exemplo, o utilizador ou administrador de um inquilino concedeu as `mail.read` permissões e `user.read` o Microsoft Graph ao cliente. 

Se o cliente `scope=https://graph.microsoft.com/.default` solicitar, não é apresentado qualquer pedido de consentimento, independentemente do conteúdo das permissões registadas da aplicação do cliente para o Microsoft Graph. O símbolo devolvido contém os âmbitos `mail.read` e `user.read` .

#### <a name="example-2-the-user-hasnt-granted-permissions-between-the-client-and-the-resource"></a>Exemplo 2: O utilizador não concedeu permissões entre o cliente e o recurso

Neste exemplo, o utilizador não concedeu o consentimento entre o cliente e o Microsoft Graph. O cliente registou-se para as permissões `user.read` `contacts.read` e. Também registou o âmbito Azure Key Vault `https://vault.azure.net/user_impersonation` . 

Quando o cliente solicita um token `scope=https://graph.microsoft.com/.default` para, o utilizador vê uma página de consentimento para o `user.read` âmbito, o `contacts.read` âmbito e os miraisos do Cofre `user_impersonation` chave. O símbolo devolvido contém apenas os `user.read` `contacts.read` e miras. Só pode ser utilizado contra o Microsoft Graph.

#### <a name="example-3-the-user-has-consented-and-the-client-requests-more-scopes"></a>Exemplo 3: O utilizador consentiu e o cliente solicita mais âmbitos

Neste exemplo, o utilizador já consentiu `mail.read` com o cliente. O cliente registou-se para o `contacts.read` âmbito. 

Quando o cliente solicita um token utilizando `scope=https://graph.microsoft.com/.default` e solicita o consentimento `prompt=consent` através, o utilizador vê uma página de consentimento para todos (e apenas) as permissões que a aplicação registou. O `contacts.read` âmbito está na página de consentimento, mas não `mail.read` está. O token devolvido é para o Microsoft Graph. Contém `mail.read` `contacts.read` e.

### <a name="using-the-default-scope-with-the-client"></a>Utilizando o âmbito /.predefinido com o cliente

Em alguns casos, um cliente pode solicitar o seu próprio `/.default` âmbito. O exemplo que se segue demonstra este cenário.

```HTTP
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
response_type=token            //Code or a hybrid flow is also possible here
&client_id=9ada6f8a-6d83-41bc-b169-a306c21527a5
&scope=9ada6f8a-6d83-41bc-b169-a306c21527a5/.default
&redirect_uri=https%3A%2F%2Flocalhost
&state=1234
```

Este exemplo de código produz uma página de consentimento para todas as permissões registadas se as descrições anteriores de consentimento e `/.default` se aplicarem ao cenário. Em seguida, o código devolve um `id_token` , em vez de um token de acesso.  

Este comportamento acomoda alguns clientes legados que estão a mudar-se da Azure AD Authentication Library (ADAL) para a Microsoft Authentication Library (MSAL). Esta configuração *não deve* ser usada por novos clientes que visam a plataforma de identidade da Microsoft.

### <a name="client-credentials-grant-flow-and-default"></a>Fluxo de concessão de credenciais de cliente e /.default  

Outro uso `/.default` é solicitar permissões de aplicação (ou *funções)* numa aplicação nãointerativa como uma app daemon que usa o fluxo de concessão de [credenciais](v2-oauth2-client-creds-grant-flow.md) do cliente para chamar uma API web.

Para criar permissões de aplicação (funções) para uma API web, consulte [as funções da aplicação Adicionar na sua aplicação.](howto-add-app-roles-in-azure-ad-apps.md)

Os pedidos de credenciais de cliente na aplicação do seu cliente *devem* `scope={resource}/.default` incluir. Aqui, `{resource}` é a API web que a sua aplicação pretende ligar. A emissão de um pedido de credenciais de cliente utilizando permissões individuais de aplicação (funções) *não* é suportada. Todas as permissões de aplicação (funções) que foram concedidas para essa API web estão incluídas no token de acesso devolvido.

Para conceder acesso às permissões de candidatura que define, incluindo a concessão de consentimento administrativo para a aplicação, consulte [configurar uma aplicação do cliente para aceder a uma API web.](quickstart-configure-app-access-web-apis.md)

### <a name="trailing-slash-and-default"></a>Corte de fuga e /.padrão

Alguns URIs de recursos têm um corte para a frente, por exemplo, `https://contoso.com/` em oposição a `https://contoso.com` . O corte de fuga pode causar problemas com a validação simbólica. Os problemas ocorrem principalmente quando um símbolo é solicitado para Azure Resource Manager ( `https://management.azure.com/` ). Neste caso, um corte de fuga no recurso URI significa que o corte deve estar presente quando o sinal é solicitado.  Assim, quando você pede um token para `https://management.azure.com/` e `/.default` usar, você deve solicitar `https://management.azure.com//.default` (note o corte duplo!). Em geral, se verificar que o token está a ser emitido, e se o sinal for rejeitado pela API que o aceite, considere adicionar um segundo corte avançado e tentar novamente. 

## <a name="troubleshooting-permissions-and-consent"></a>Permissões e consentimento para resolução de problemas

Para obter etapas de resolução de problemas, consulte [erro inesperado ao efetuar o consentimento de uma aplicação](../manage-apps/application-sign-in-unexpected-user-consent-error.md).

## <a name="next-steps"></a>Passos seguintes

* [Fichas de ID na plataforma de identidade da Microsoft](id-tokens.md)
* [Fichas de acesso na plataforma de identidade da Microsoft](access-tokens.md)

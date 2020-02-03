---
title: Porquê atualizar para a plataforma de identidade da Microsoft (v2.0) / Azure
description: Conheça as diferenças entre a plataforma de identidade da Microsoft (v2.0) endpoint e o Azure Ative Directory (Azure AD) v1.0 endpoint, e saiba os benefícios da atualização para v2.0.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: ryanwi
ms.reviewer: saeeda, hirsin, jmprieur, sureshja, jesakowi, lenalepa, kkrishna, negoe
ms.custom: aaddev
ms.openlocfilehash: a05db11f48811f5ad6163447fae1dbb4ea31842c
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76712437"
---
# <a name="why-update-to-microsoft-identity-platform-v20"></a>Porquê atualizar para a plataforma de identidade da Microsoft (v2.0)?

Ao desenvolver uma nova aplicação, é importante conhecer as diferenças entre a plataforma de identidade da Microsoft (v2.0) e os pontos finais do Azure Ative Directory (v1.0). Este artigo aborda as principais diferenças entre os pontos finais e algumas limitações existentes para a plataforma de identidade da Microsoft.

> [!NOTE]
> O ponto de extremidade da plataforma de identidade da Microsoft não dá suporte a todos os cenários e recursos do Azure AD. Para determinar se deve utilizar o ponto final da plataforma de identidade da Microsoft, leia sobre [as limitações](#limitations)da plataforma de identidade da Microsoft .

## <a name="who-can-sign-in"></a>Quem pode entrar

![Quem pode inscrever-se com pontos finais v1.0 e v2.0](media/azure-ad-endpoint-comparison/who-can-sign-in.svg)

* O ponto final v1.0 permite que apenas as contas de trabalho e escola sintetizam a sua candidatura (Azure AD)
* O ponto final da plataforma de identidade da Microsoft permite que contas de trabalho e escola a partir de contas Da AD E pessoais da Microsoft (MSA), como hotmail.com, outlook.com e msn.com, se inscrevam.
* Ambos os pontos finais também aceitam inscrições de *[utilizadores convidados](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b)* de um diretório Azure AD para aplicações configuradas como *[inquilino único](single-and-multi-tenant-apps.md)* ou para aplicações *multi-arrendatárias* configuradas para apontar para o ponto final específico do inquilino (`https://login.microsoftonline.com/{TenantId_or_Name}`).

O ponto final da plataforma de identidade da Microsoft permite-lhe escrever apps que aceitam inscrições a partir de contas pessoais da Microsoft e contas de trabalho e escola. Isto dá-lhe a capacidade de escrever a sua app completamente agnóstica. Por exemplo, se a sua aplicação ligar para o [Microsoft Graph,](https://graph.microsoft.io)alguma funcionalidade adicional e dados estarão disponíveis para contas de trabalho, como os seus sites do SharePoint ou dados de diretório. Mas para muitas ações, como ler o [correio de um utilizador,](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_messages)o mesmo código pode aceder ao e-mail para contas pessoais e laborais e escolares.

Para o ponto final da plataforma de identidade microsoft, você pode usar a Microsoft Authentication Library (MSAL) para ter acesso aos mundos do consumidor, educacional e empresarial. O ponto final da Azure AD v1.0 aceita inscrições apenas a partir de contas de trabalho e escolares.

## <a name="incremental-and-dynamic-consent"></a>Consentimento incremental e dinâmico

As aplicações que utilizem o ponto final da AD Azure v1.0 são obrigadas a especificar antecipadamente as suas permissões OAuth 2.0 necessárias:

![Exemplo mostrando o UI de Registo de Permissões](./media/azure-ad-endpoint-comparison/app_reg_permissions.png)

As permissões definidas diretamente no registo da candidatura são **estáticas.** Embora as permissões estáticas da app definidano portal Azure mantenham o código agradável e simples, apresenta alguns problemas possíveis para os desenvolvedores:

* A aplicação precisa de solicitar todas as permissões que alguma vez necessitaria no primeiro início de sessão do utilizador. Isto pode levar a uma longa lista de permissões que desencoraja os utilizadores finais de aprovarem o acesso da app no início do registo.

* A aplicação precisa de conhecer todos os recursos a que alguma vez acederia com antecedência. Foi difícil criar apps que pudessem aceder a um número arbitrário de recursos.

Com o ponto final da plataforma de identidade da Microsoft, pode ignorar as permissões estáticas definidas na informação de registo da aplicação no portal Azure e solicitar permissões incrementalmente, o que significa pedir um conjunto mínimo de permissões antecipadamente e crescendo mais com o tempo à medida que o cliente usa funcionalidades adicionais de aplicação. Para isso, pode especificar os âmbitos de que a sua aplicação necessita a qualquer momento, incluindo os novos âmbitos do parâmetro `scope` ao solicitar um sinal de acesso - sem a necessidade de os definir predefinindo nas informações de registo da aplicação. Se o utilizador ainda não tiver consentido com novos âmbitos adicionados ao pedido, serão solicitados a consentir apenas com as novas permissões. Para saber mais, consulte [permissões, consentimento e âmbitos.](v2-permissions-and-consent.md)

Permitir que uma aplicação solicite permissões dinamicamente através do parâmetro `scope` dá aos desenvolvedores o controlo total sobre a experiência do utilizador. Também pode carregar frontalmente a sua experiência de consentimento e pedir todas as permissões num pedido de autorização inicial. Se a sua aplicação necessitar de um grande número de permissões, pode recolher essas permissões do utilizador de forma incremental, uma vez que tenta utilizar determinadas funcionalidades da aplicação ao longo do tempo.

O consentimento da Administração feita em nome de uma organização ainda requer as permissões estáticas registadas para a app, pelo que deverá definir essas permissões para aplicações no portal de registo de aplicações se precisar de um administrador para dar consentimento em nome de toda a organização. Isto reduz os ciclos exigidos pelo administrador da organização para a configuração da aplicação.

## <a name="scopes-not-resources"></a>Âmbitos, não recursos

Para aplicações que utilizem o ponto final v1.0, uma aplicação pode comportar-se como um **recurso**, ou um destinatário de fichas. Um recurso pode definir uma série de **âmbitos** ou **oAuth2Permissions** que entende, permitindo que as aplicações de clientes solicitem fichas desse recurso para um determinado conjunto de âmbitos. Considere a Microsoft Graph API como um exemplo de um recurso:

* Identificador de recursos, ou `AppID URI`: `https://graph.microsoft.com/`
* Âmbitos, ou `oAuth2Permissions`: `Directory.Read`, `Directory.Write`, e assim por diante.

Isto mantém-se fiel ao ponto final da plataforma de identidade da Microsoft. Uma aplicação ainda pode comportar-se como um recurso, definir âmbitos e ser identificada por um URI. As aplicações de clientes ainda podem solicitar acesso a esses âmbitos. No entanto, a forma como um cliente solicita essas permissões mudou.

Para o ponto final v1.0, um pedido de autorização oAuth 2.0 para Azure AD poderia ter parecido:

```text
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https://graph.windows.net/
...
```

Aqui, o parâmetro de **recurso** indicou qual o recurso que a aplicação do cliente está a solicitar autorização. A Azure AD computou as permissões exigidas pela app com base na configuração estática no portal Azure, e emitiu tokens em conformidade.

Para aplicações que utilizem o ponto final da plataforma de identidade da Microsoft, o mesmo pedido de autorização OAuth 2.0 parece:

```text
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https://graph.windows.net/directory.read%20https://graph.windows.net/directory.write
...
```

Aqui, o parâmetro de **âmbito** indica que recurso e permissões a app está a solicitar autorização. O recurso pretendido ainda está presente no pedido - é englobado em cada um dos valores do parâmetro de âmbito. A utilização do parâmetro de âmbito desta forma permite que o ponto final da plataforma de identidade da Microsoft seja mais conforme com a especificação OAuth 2.0, e alinha-se mais de perto com as práticas comuns da indústria. Também permite que as aplicações façam [o consentimento incremental](#incremental-and-dynamic-consent) - apenas solicitando permissões quando a aplicação as requer em oposição à frente.

## <a name="well-known-scopes"></a>Âmbitos bem conhecidos

### <a name="offline-access"></a>Acesso offline

As aplicações que utilizam o ponto final da plataforma de identidade da Microsoft podem exigir o uso de uma nova permissão bem conhecida para apps - o âmbito `offline_access`. Todas as aplicações terão de solicitar esta permissão se necessitarem de aceder a recursos em nome de um utilizador por um período prolongado de tempo, mesmo quando o utilizador pode não estar a utilizar ativamente a aplicação. O âmbito `offline_access` aparecerá ao utilizador em diálogos de consentimento como Aceder aos **seus dados a qualquer momento**, que o utilizador deve concordar. Pedir a autorização `offline_access` permitirá que a sua aplicação web receba o OAuth 2.0 refresh_tokens do ponto final da plataforma de identidade da Microsoft. As fichas de atualização são de longa duração e podem ser trocadas por novos tokens de acesso OAuth 2.0 por longos períodos de acesso.

Se a sua aplicação não solicitar o âmbito `offline_access`, não receberá fichas de atualização. Isto significa que quando resgatar um código de autorização no fluxo de código de autorização OAuth 2.0, só receberá um sinal de acesso do ponto final `/token`. Esse token de acesso permanece válido por um curto período de tempo (normalmente uma hora), mas acabará por expirar. Nessa altura, a sua aplicação terá de redirecionar o utilizador para o ponto final `/authorize` para recuperar um novo código de autorização. Durante este redirecionamento, o utilizador pode ou não precisar de introduzir as suas credenciais novamente ou reconsentir-se com permissões, dependendo do tipo de app.

Para saber mais sobre o OAuth 2.0, `refresh_tokens`, e `access_tokens`, consulte a referência do protocolo da [plataforma de identidade microsoft](active-directory-v2-protocols.md).

### <a name="openid-profile-and-email"></a>OpenID, perfil e e-mail

Historicamente, o fluxo de entrada de entrada mais básico do OpenID Connect com a plataforma de identidade da Microsoft forneceria muita informação sobre o utilizador na *id_token*resultante . As reclamações num id_token podem incluir o nome do utilizador, nome de utilizador preferido, endereço de e-mail, ID do objeto, e muito mais.

A informação a que o âmbito `openid` dá acesso à sua aplicação está agora restrita. O âmbito `openid` só permitirá que a sua aplicação assine o utilizador e receba um identificador específico da aplicação para o utilizador. Se pretender obter dados pessoais sobre o utilizador na sua aplicação, a sua aplicação necessita de solicitar permissões adicionais ao utilizador. Dois novos âmbitos, `email` e `profile`, permitir-lhe-ão solicitar permissões adicionais.

* O âmbito `email` permite o acesso da sua aplicação ao endereço de e-mail principal do utilizador através da `email` reclamação no id_token, assumindo que o utilizador tem um endereço de e-mail endereçado.
* O âmbito `profile` permite à sua aplicação o acesso a todas as outras informações básicas sobre o utilizador, tais como o seu nome de utilizador, nome de utilizador preferido, ID de objeto, e assim por diante, no id_token.

Estes âmbitos permitem codificar a sua aplicação de forma mínima para que só possa pedir ao utilizador o conjunto de informações que a sua aplicação precisa para fazer o seu trabalho. Para obter mais informações sobre estes âmbitos, consulte [a referência de âmbito da plataforma de identidade da Microsoft](v2-permissions-and-consent.md).

## <a name="token-claims"></a>Alegações simbólicas

O ponto final da plataforma de identidade da Microsoft emite um conjunto menor de reclamações nos seus tokens por padrão para manter as cargas pequenas. Se tiver aplicações e serviços que tenham uma dependência de uma reclamação particular num token v1.0 que já não seja fornecido por padrão numa plataforma de identidade da Microsoft, considere utilizar a funcionalidade de [reclamações opcionais](active-directory-optional-claims.md) para incluir essa alegação.

> [!IMPORTANT]
> v1.0 e v2.0 tokens podem ser emitidos pelos pontos finais v1.0 e v2.0! id_tokens *sempre* corresponda ao ponto final a que são solicitados, e os tokens de acesso correspondem *sempre* ao formato esperado pela Web API que o seu cliente irá ligar usando esse token.  Assim, se a sua aplicação utilizar o ponto final v2.0 para obter um sinal para ligar para o Microsoft Graph, que espera fichas de acesso ao formato v1.0, a sua aplicação receberá um token no formato v1.0.  

## <a name="limitations"></a>Limitações

Existem algumas restrições a ter em conta ao utilizar a plataforma de identidade da Microsoft.

Quando constrói aplicações que se integram com a plataforma de identidade da Microsoft, precisa de decidir se os protocolos de ponto final da plataforma de identidade da Microsoft e os protocolos de autenticação satisfazem as suas necessidades. O ponto final v1.0 e plataforma ainda é totalmente suportado e, em alguns aspetos, é mais rico em funcionalidades do que a plataforma de identidade da Microsoft. No entanto, a plataforma de identidade da Microsoft [introduz benefícios significativos](azure-ad-endpoint-comparison.md) para os desenvolvedores.

Aqui está uma recomendação simplificada para os desenvolvedores agora:

* Se quiser ou precisar de suportar contas pessoais da Microsoft na sua aplicação, ou estiver a escrever uma nova aplicação, utilize a plataforma de identidade da Microsoft. Mas antes disso, certifique-se de compreender as limitações discutidas neste artigo.
* Se estiver a migrar ou a atualizar uma aplicação que dependa da SAML, não pode utilizar a plataforma de identidade da Microsoft. Em vez disso, consulte o [guia Azure AD v1.0](v1-overview.md).

O ponto final da plataforma de identidade da Microsoft evoluirá para eliminar as restrições listadas aqui, para que só precise de utilizar o ponto final da plataforma de identidade da Microsoft. Entretanto, utilize este artigo para determinar se o ponto final da plataforma de identidade da Microsoft é o certo para si. Continuaremos a atualizar este artigo para refletir o estado atual do ponto final da plataforma de identidade da Microsoft. Volte a verificar para reavaliar os seus requisitos em relação às capacidades da plataforma de identidade da Microsoft.

### <a name="restrictions-on-app-registrations"></a>Restrições aos registos de aplicações

Para cada aplicação que pretende integrar com o ponto final da plataforma de identidade da Microsoft, pode criar um registo de aplicações na experiência de [ **registos** ](https://aka.ms/appregistrations) da nova App no portal Azure. As aplicações de conta microsoft existentes não são compatíveis com o portal, mas todas as aplicações da AD Azure são, independentemente de onde ou quando foram registadas.

As inscrições de aplicativos que suportam o trabalho e as contas escolares e as contas pessoais têm as seguintes ressalvas:

* Apenas dois segredos de aplicativo são permitidos por id de aplicação.
* Um pedido que não estava registado num inquilino só pode ser gerido pela conta que o registou. Não pode ser partilhado com outros desenvolvedores. É o caso da maioria das aplicações que foram registadas através de uma conta pessoal da Microsoft no Portal de Registo de Aplicações. Se quiser partilhar o registo da sua aplicação com vários desenvolvedores, registe a aplicação num inquilino utilizando a nova secção de **registos** da App do portal Azure.
* Existem várias restrições no formato do URL de redirecionamento que é permitido. Para mais informações sobre o URL redirecionamento, consulte a secção seguinte.

### <a name="restrictions-on-redirect-urls"></a>Restrições aos URLs de redirecionamento

As aplicações que estão registadas na plataforma de identidade da Microsoft estão restritas a um conjunto limitado de valores de URL redirecionados. O URL de redirecionamento para aplicações e serviços web deve começar com o esquema `https`, e todos os valores de URL redirecionam um único domínio DNS.  O sistema de registo compara todo o nome DNS do URL de redirecionamento existente ao nome DNS do URL de redirecionamento que está a adicionar. `http://localhost` também é suportado como um URL de redirecionamento.  

O pedido para adicionar o nome DNS irá falhar, se uma das seguintes condições for verdadeira:  

* Todo o nome DNS do novo URL de redirecionamento não corresponde ao nome DNS do URL de redirecionamento existente.
* Todo o nome DNS do novo URL de redirecionamento não é um subdomínio do URL de redirecionamento existente.

#### <a name="example-1"></a>Exemplo 1

Se a aplicação tiver um URL redirecionado de `https://login.contoso.com`, pode adicionar um URL de redirecionamento onde o nome DNS corresponde exatamente, como mostra o seguinte exemplo:

`https://login.contoso.com/new`

Ou, pode consultar um subdomínio DNS de login.contoso.com, como mostra o seguinte exemplo:

`https://new.login.contoso.com`

#### <a name="example-2"></a>Exemplo 2

Se quiser ter uma aplicação que tenha `login-east.contoso.com` e `login-west.contoso.com` como URLs redireccionadores, deve adicionar esses URLs redirecionais na seguinte ordem:

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

Pode adicionar os dois últimos porque são subdomínios do primeiro URL redirecionado, contoso.com.

Pode ter apenas 20 URLs de resposta para uma determinada aplicação - este limite aplica-se em todos os tipos de aplicações que o registo suporta (aplicação de página única (SPA), cliente nativo, aplicação web e serviço).  

Para aprender a registar uma aplicação para utilização com a plataforma de identidade microsoft, consulte [O Registo de uma aplicação utilizando a experiência de registos da nova App.](quickstart-register-app.md)

### <a name="restrictions-on-libraries-and-sdks"></a>Restrições às bibliotecas e SDKs

Atualmente, o suporte à biblioteca para o ponto final da plataforma de identidade da Microsoft é limitado. Se pretender utilizar o ponto final da plataforma de identidade da Microsoft numa aplicação de produção, tem estas opções:

* Se estiver a construir uma aplicação web, pode utilizar com segurança o middleware do lado do servidor geralmente disponível para fazer a validação de entrada e ficha. Estes incluem o middleware OWIN OpenID Connect para ASP.NET e o plug-in Node.js Passport. Para amostras de código que utilizam o middleware da Microsoft, consulte a secção de identidade da [Microsoft.](v2-overview.md#getting-started)
* Se estiver a construir um ambiente de trabalho ou uma aplicação móvel, pode utilizar uma das Bibliotecas de Autenticação da Microsoft (MSAL). Estas bibliotecas estão geralmente disponíveis ou numa pré-visualização apoiada pela produção, pelo que é seguro utilizá-las em aplicações de produção. Pode ler mais sobre os termos da pré-visualização e as bibliotecas disponíveis na referência das bibliotecas de [autenticação.](reference-v2-libraries.md)
* Para plataformas não abrangidas pelas bibliotecas da Microsoft, pode integrar-se com o ponto final da plataforma de identidade da Microsoft enviando e recebendo diretamente mensagens protocolares no seu código de aplicação. Os protocolos OpenID Connect e OAuth [estão explicitamente documentados](active-directory-v2-protocols.md) para ajudá-lo a fazer tal integração.
* Por fim, pode utilizar bibliotecas OpenID Connect e OAuth de código aberto para integrar com o ponto final da plataforma de identidade da Microsoft. O ponto final da plataforma de identidade da Microsoft deve ser compatível com muitas bibliotecas de protocolos de código aberto sem alterações. A disponibilidade deste tipo de bibliotecas varia em termos de linguagem e plataforma. Os websites [OpenID Connect](https://openid.net/connect/) e [OAuth 2.0](https://oauth.net/2/) mantêm uma lista de implementações populares. Para obter mais informações, consulte a [plataforma de identidade da Microsoft e bibliotecas](reference-v2-libraries.md)de autenticação , e a lista de bibliotecas e amostras de clientes de código aberto que foram testadas com o ponto final da plataforma de identidade da Microsoft.
* Para referência, o ponto final `.well-known` para o ponto final comum da plataforma de identidade da Microsoft é `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration`. Substitua `common` com a sua identificação do inquilino para obter dados específicos do seu inquilino.  

### <a name="protocol-changes"></a>Alterações de protocolo

O ponto final da plataforma de identidade da Microsoft não suporta a SAML ou a WS-Federation; suporta apenas o OpenID Connect e o OAuth 2.0.  As notáveis alterações aos protocolos OAuth 2.0 do ponto final v1.0 são: 

* A `email` reclamação é devolvida se uma reclamação opcional estiver configurada **ou for** especificada no pedido o scope=email. 
* O parâmetro `scope` é agora suportado no lugar do parâmetro `resource`.  
* Muitas respostas foram modificadas para torná-las mais compatíveis com a especificação OAuth 2.0, por exemplo, devolvendo corretamente `expires_in` como um int em vez de uma cadeia.  

Para melhor compreender o âmbito da funcionalidade do protocolo suportado no ponto final da plataforma de identidade da Microsoft, consulte openID Connect e referência de [protocolo OAuth 2.0](active-directory-v2-protocols.md).

#### <a name="saml-restrictions"></a>Restrições SAML

Se utilizou a Biblioteca de Autenticação de Diretórios Ativo (ADAL) em aplicações Windows, poderá ter aproveitado a autenticação integrada do Windows, que utiliza a bolsa de afirmação de marcação de afirmação de afirmação de afirmação de afirmação (SAML) de afirmação de afirmação de afirmação de afirmação de afirmação de afirmação de afirmação de afirmação de afirmação de afirmação de afirmação de afirmação de afirmação de afirmação de afirmação de afirmação de afirmação de afirmação de afirmação de afirmação de afirmação de afirmação de afirmação de afirmação de afirmação de afirmação de afirmação de afirmação de afirmação de afirmação Com esta subvenção, os utilizadores de inquilinos federados da AD Azure podem autenticar silenciosamente com a sua instância de Diretório Ativo no local sem introduzir credenciais. A bolsa de afirmação SAML não é suportada no ponto final da plataforma de identidade da Microsoft.

---
title: Porquê atualizar para a plataforma de identidade da Microsoft (v2.0) Rio Azure
description: Conheça as diferenças entre a plataforma de identidade da Microsoft (v2.0) e o Azure Ative Directory (Azure AD) v1.0 e aprenda os benefícios da atualização para v2.0.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 07/17/2020
ms.author: ryanwi
ms.reviewer: saeeda, hirsin, jmprieur, sureshja, jesakowi, lenalepa, kkrishna, negoe
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 8f6170de65ae5e1ca8ecb5f7cc8a78f4f194ac41
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92055295"
---
# <a name="why-update-to-microsoft-identity-platform-v20"></a>Porquê atualizar a plataforma de identidade da Microsoft (v2.0)?

Ao desenvolver uma nova aplicação, é importante saber as diferenças entre a plataforma de identidade da Microsoft (v2.0) e o Azure Ative Directory (v1.0). Este artigo cobre as principais diferenças entre os pontos finais e algumas limitações existentes para a plataforma de identidade da Microsoft.

## <a name="who-can-sign-in"></a>Quem pode entrar

![Quem pode assinar com pontos finais v1.0 e v2.0](media/azure-ad-endpoint-comparison/who-can-signin.svg)

* O ponto final v1.0 permite apenas que o trabalho e as contas escolares se inscrevam na sua candidatura (Azure AD)
* O ponto final da plataforma de identidade da Microsoft permite que contas de trabalho e escola a partir de Azure AD e contas pessoais da Microsoft (MSA), como hotmail.com, outlook.com e msn.com, se inscrevam.
* Ambos os pontos finais também aceitam inscrições de *[utilizadores convidados](../external-identities/what-is-b2b.md)* de um diretório AD Azure para aplicações configuradas como *[inquilino único](../develop/single-and-multi-tenant-apps.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)* ou para aplicações *multi-arrendatários* configuradas para apontar para o ponto final específico do inquilino ( `https://login.microsoftonline.com/{TenantId_or_Name}` ).

O ponto final da plataforma de identidade da Microsoft permite-lhe escrever apps que aceitam ins-ins a partir de contas pessoais da Microsoft, e contas de trabalho e escola. Isto dá-lhe a capacidade de escrever a sua aplicação completamente agnóstico de conta. Por exemplo, se a sua aplicação ligar para o [Microsoft Graph,](https://graph.microsoft.io)algumas funcionalidades e dados adicionais estarão disponíveis para contas de trabalho, tais como os seus sites SharePoint ou dados de diretório. Mas para muitas ações, como [ler o correio de um utilizador,](/graph/api/user-list-messages)o mesmo código pode aceder ao e-mail tanto para contas pessoais como de trabalho e escolas.

Para o ponto final da plataforma de identidade da Microsoft, pode utilizar a Microsoft Authentication Library (MSAL) para ter acesso aos mundos do consumidor, educacional e empresarial. O ponto final Azure AD v1.0 aceita inscrições apenas do trabalho e das contas escolares.

## <a name="incremental-and-dynamic-consent"></a>Consentimento incremental e dinâmico

As aplicações que utilizam o ponto final Azure AD v1.0 são necessárias para especificar antecipadamente as permissões OAuth 2.0 exigidas, por exemplo:

![Exemplo mostrando as permissões Registo UI](./media/azure-ad-endpoint-comparison/app-reg-permissions.png)

As permissões definidas diretamente no registo do pedido são **estáticas.** Embora as permissões estáticas da aplicação definidas no portal Azure mantenham o código agradável e simples, apresenta alguns problemas possíveis para os desenvolvedores:

* A aplicação precisa de solicitar todas as permissões que alguma vez necessitaria no primeiro início de sção do utilizador. Isto pode levar a uma longa lista de permissões que desencoraja os utilizadores finais de aprovarem o acesso da app ao início da sindes.

* A aplicação precisa de saber todos os recursos a que alguma vez acederia com antecedência. Era difícil criar apps que pudessem aceder a um número arbitrário de recursos.

Com o ponto final da plataforma de identidade da Microsoft, pode ignorar as permissões estáticas definidas nas informações de registo de aplicações no portal Azure e solicitar permissões incrementalmente, o que significa pedir um conjunto mínimo de permissões inicialmente e crescer mais ao longo do tempo à medida que o cliente utiliza funcionalidades adicionais da aplicação. Para tal, pode especificar os âmbitos de que a sua aplicação necessita a qualquer momento, incluindo os novos âmbitos no `scope` parâmetro ao solicitar um token de acesso – sem a necessidade de os definir pré-definidos nas informações de registo de aplicações. Se o utilizador ainda não tiver consentido com novos âmbitos adicionados ao pedido, serão solicitados a consentir apenas com as novas permissões. Para saber mais, consulte [permissões, consentimento e âmbitos.](../develop/v2-permissions-and-consent.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)

Permitir que uma aplicação solicite permissões dinamicamente através do `scope` parâmetro dá aos desenvolvedores total controlo sobre a experiência do seu utilizador. Também pode fazer a primeira carga da sua experiência de consentimento e pedir todas as permissões num pedido inicial de autorização. Se a sua aplicação necessitar de um grande número de permissões, pode recolher essas permissões do utilizador de forma incremental, uma vez que tentam utilizar certas funcionalidades da aplicação ao longo do tempo.

O consentimento da administração feito em nome de uma organização ainda requer as permissões estáticas registadas para a app, pelo que deve definir essas permissões para aplicações no portal de registo de aplicações se precisar de um administrador para dar consentimento em nome de toda a organização. Isto reduz os ciclos exigidos pela administração da organização para configurar a aplicação.

## <a name="scopes-not-resources"></a>Âmbitos, não recursos

Para aplicações que utilizem o ponto final v1.0, uma aplicação pode comportar-se como um **recurso**, ou um destinatário de fichas. Um recurso pode definir uma série de **âmbitos** ou **oAuth2Permissions** que entende, permitindo que as aplicações do cliente solicitem fichas desse recurso para um determinado conjunto de âmbitos. Considere a API do Gráfico da Microsoft como um exemplo de um recurso:

* Identificador de recursos, `AppID URI` ou: `https://graph.microsoft.com/`
* Âmbitos, ou `oAuth2Permissions` : , , e assim por `Directory.Read` `Directory.Write` diante.

Isto é válido para o ponto final da plataforma de identidade da Microsoft. Uma aplicação ainda pode comportar-se como um recurso, definir âmbitos e ser identificado por um URI. As aplicações do cliente ainda podem solicitar o acesso a esses âmbitos. No entanto, a forma como um cliente solicita essas permissões mudou.

Para o ponto final v1.0, um pedido autorizado da OAuth 2.0 à AZure AD poderia ter parecido:

```text
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https://graph.microsoft.com/
...
```

Aqui, o parâmetro **de recurso** indicava qual o recurso que a app do cliente está a solicitar autorização. O Azure AD computação as permissões exigidas pela app com base na configuração estática no portal Azure, e emitiu fichas em conformidade.

Para aplicações que utilizam o ponto final da plataforma de identidade da Microsoft, o mesmo pedido autorizado OAuth 2.0 parece:

```text
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https://graph.microsoft.com/directory.read%20https://graph.microsoft.com/directory.write
...
```

Aqui, o parâmetro de **âmbito** indica qual o recurso e permissões que a app está a solicitar autorização. O recurso desejado ainda está presente no pedido - está incluído em cada um dos valores do parâmetro de âmbito. A utilização do parâmetro de âmbito desta forma permite que o ponto final da plataforma de identidade da Microsoft seja mais compatível com a especificação OAuth 2.0 e alinha-se mais estreitamente com as práticas comuns da indústria. Também permite que as aplicações façam [o consentimento incremental](#incremental-and-dynamic-consent) - apenas solicitando permissões quando a aplicação as requer em oposição à frontalidade.

## <a name="well-known-scopes"></a>Âmbitos bem conhecidos

### <a name="offline-access"></a>Acesso offline

As aplicações que utilizam o ponto final da plataforma de identidade da Microsoft podem exigir o uso de uma nova permissão bem conhecida para apps - o `offline_access` âmbito. Todas as aplicações terão de solicitar esta permissão caso necessitem de aceder a recursos em nome de um utilizador por um período prolongado de tempo, mesmo quando o utilizador possa não estar a utilizar ativamente a app. O `offline_access` âmbito de aplicação aparecerá para o utilizador em diálogos de consentimento como **Access os seus dados**a qualquer momento , o que o utilizador deve concordar. O pedido de `offline_access` permissão permitirá que a sua aplicação web receba refresh_tokens OAuth 2.0 do ponto final da plataforma de identidade da Microsoft. Os tokens de atualização são de longa duração, e podem ser trocados por novos tokens de acesso OAuth 2.0 por longos períodos de acesso.

Se a sua aplicação não solicitar o `offline_access` âmbito, não receberá fichas de atualização. Isto significa que quando resgatar um código de autorização no fluxo de código de autorização OAuth 2.0, só receberá um sinal de acesso a partir do `/token` ponto final. Esse sinal de acesso permanece válido por um curto período de tempo (normalmente uma hora), mas acabará por expirar. Nessa altura, a sua aplicação terá de redirecionar o utilizador para o `/authorize` ponto final para recuperar um novo código de autorização. Durante este redirecionamento, o utilizador pode ou não precisar de introduzir as suas credenciais novamente ou reconsenciar as permissões, dependendo do tipo de aplicação.

Para saber mais sobre o OAuth 2.0, `refresh_tokens` `access_tokens` e, consulte a referência do protocolo da [plataforma de identidade da Microsoft](../develop/active-directory-v2-protocols.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

### <a name="openid-profile-and-email"></a>OpenID, perfil e e-mail

Historicamente, o fluxo de entrada de acesso OpenID Connect mais básico com a plataforma de identidade da Microsoft forneceria muita informação sobre o utilizador na *id_token*resultante . As reclamações num id_token podem incluir o nome do utilizador, nome de utilizador preferido, endereço de e-mail, ID do objeto, entre outros.

A informação a que o `openid` âmbito dá acesso à sua app é agora restrita. O `openid` âmbito apenas permitirá que a sua aplicação assine no utilizador e receba um identificador específico da aplicação para o utilizador. Se pretender obter dados pessoais sobre o utilizador na sua aplicação, a sua aplicação necessita de solicitar permissões adicionais ao utilizador. Dois novos `email` âmbitos, `profile` e, permitir-lhe-á solicitar permissões adicionais.

* O `email` âmbito permite o acesso da sua aplicação ao principal endereço de e-mail do utilizador através da `email` reclamação na id_token, assumindo que o utilizador tem um endereço de e-mail endereçada.
* O `profile` âmbito permite à sua aplicação aceder a todas as outras informações básicas sobre o utilizador, como o seu nome, nome de utilizador preferido, ID de objeto, e assim por diante, no id_token.

Estes âmbitos permitem-lhe codificar a sua aplicação de forma de divulgação mínima para que só possa pedir ao utilizador o conjunto de informações que a sua aplicação necessita para fazer o seu trabalho. Para obter mais informações sobre estes âmbitos, consulte [a referência de âmbito da plataforma de identidade da Microsoft](../develop/v2-permissions-and-consent.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

## <a name="token-claims"></a>Reivindicações simbólicas

O ponto final da plataforma de identidade da Microsoft emite um conjunto menor de reclamações nos seus tokens por padrão para manter as cargas pequenas. Se tiver aplicações e serviços que tenham uma dependência de uma determinada reclamação num token v1.0 que já não é fornecido por padrão num token da plataforma de identidade da Microsoft, considere usar a funcionalidade [de reclamações opcionais](../develop/active-directory-optional-claims.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) para incluir essa alegação.

> [!IMPORTANT]
> os tokens v1.0 e v2.0 podem ser emitidos tanto pelos pontos finais v1.0 como v2.0! id_tokens *sempre* correspondem ao ponto final de onde são solicitados, e os tokens de acesso *correspondem sempre* ao formato esperado pela API Web que o seu cliente irá chamar usando esse token.  Assim, se a sua aplicação usar o ponto final v2.0 para obter um token para ligar para o Microsoft Graph, que espera tokens de acesso ao formato V1.0, a sua aplicação receberá um símbolo no formato v1.0.

## <a name="limitations"></a>Limitações

Existem algumas restrições a ter em conta ao utilizar a plataforma de identidade da Microsoft.

Ao construir aplicações que se integram na plataforma de identidade da Microsoft, tem de decidir se o ponto final da plataforma de identidade da Microsoft e os protocolos de autenticação satisfazem as suas necessidades. O ponto final e plataforma v1.0 ainda está totalmente suportado e, em alguns aspetos, é mais rico em funcionalidades do que a plataforma de identidade da Microsoft. No entanto, a plataforma de identidade da Microsoft [introduz benefícios significativos](azure-ad-endpoint-comparison.md) para os desenvolvedores.

Aqui está uma recomendação simplificada para os desenvolvedores agora:

* Se quiser ou precisar de suportar contas pessoais da Microsoft na sua aplicação, ou estiver a escrever uma nova aplicação, utilize a plataforma de identidade da Microsoft. Mas antes de o fazer, certifique-se de compreender as limitações discutidas neste artigo.
* Se estiver a migrar ou a atualizar uma aplicação que se baseie na SAML, não pode utilizar a plataforma de identidade da Microsoft. Em vez disso, consulte o [guia Azure AD v1.0](v1-overview.md).

O ponto final da plataforma de identidade da Microsoft irá evoluir para eliminar as restrições listadas aqui, de modo que apenas necessitará de utilizar o ponto final da plataforma de identidade da Microsoft. Entretanto, utilize este artigo para determinar se o ponto final da plataforma de identidade da Microsoft é adequado para si. Continuaremos a atualizar este artigo para refletir o estado atual do ponto final da plataforma de identidade da Microsoft. Volte a reavaliar os seus requisitos contra as capacidades da plataforma de identidade da Microsoft.

### <a name="restrictions-on-app-registrations"></a>Restrições aos registos de aplicações

Para cada aplicação que pretende integrar com o ponto final da plataforma de identidade da Microsoft, pode criar um registo de aplicações na experiência de [ **registos** ](https://aka.ms/appregistrations) de novas Apps no portal Azure. As aplicações de conta microsoft existentes não são compatíveis com o portal, mas todas as aplicações AD do Azure são, independentemente de onde ou quando foram registadas.

As inscrições de aplicativos que suportam o trabalho e as contas escolares e contas pessoais têm as seguintes ressalvas:

* Apenas dois segredos de aplicações são permitidos por ID de aplicação.
* Um pedido que não tenha sido registado num inquilino só pode ser gerido pela conta que o registou. Não pode ser partilhado com outros desenvolvedores. Este é o caso da maioria das aplicações que foram registadas usando uma conta pessoal da Microsoft no Portal de Registo de Aplicações. Se quiser partilhar o registo da sua aplicação com vários programadores, registe a aplicação num inquilino utilizando a nova secção de **registos** da App do portal Azure.
* Existem várias restrições no formato do URL de redirecionamento que é permitido. Para obter mais informações sobre o URL de redirecionamento, consulte a secção seguinte.

### <a name="restrictions-on-redirect-urls"></a>Restrições aos URLs de redirecionamento

Para obter as informações mais atualizadas sobre restrições em URLs de redirecionamento para aplicações registadas para plataforma de identidade da Microsoft, consulte [as restrições e limitações de URL de redirecionamento uri/resposta](../develop/reply-url.md) na documentação da plataforma de identidade da Microsoft.

Para saber como registar uma aplicação para uso com a plataforma de identidade da Microsoft, consulte [Registar uma aplicação utilizando a experiência de registos da nova App.](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)

### <a name="restrictions-on-libraries-and-sdks"></a>Restrições às bibliotecas e aos ODS

Atualmente, o suporte à biblioteca para o ponto final da plataforma de identidade da Microsoft é limitado. Se quiser utilizar o ponto final da plataforma de identidade da Microsoft numa aplicação de produção, tem estas opções:

* Se estiver a construir uma aplicação web, pode utilizar com segurança o middleware do lado do servidor geralmente disponível para fazer a validação de inscrição e token. Estes incluem o middleware OWIN OpenID Connect para ASP.NET e o plug-in Node.js Passport. Para obter amostras de código que utilizem middleware da Microsoft, consulte a plataforma de identidade da [Microsoft a iniciar a](../develop/v2-overview.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json#getting-started) secção.
* Se estiver a construir um ambiente de trabalho ou uma aplicação móvel, pode utilizar uma das Bibliotecas de Autenticação da Microsoft (MSAL). Estas bibliotecas estão geralmente disponíveis ou numa pré-visualização apoiada pela produção, pelo que é seguro usá-las em aplicações de produção. Pode ler mais sobre os termos da pré-visualização e as bibliotecas disponíveis em [referência de bibliotecas de autenticação.](../develop/reference-v2-libraries.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)
* Para plataformas não abrangidas pelas bibliotecas da Microsoft, pode integrar-se com o ponto final da plataforma de identidade da Microsoft, enviando e recebendo diretamente mensagens de protocolo no seu código de aplicação. Os protocolos OpenID Connect e OAuth [estão explicitamente documentados](../develop/active-directory-v2-protocols.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) para ajudá-lo a fazer tal integração.
* Por fim, pode utilizar bibliotecas OpenID Connect e OAuth de código aberto para integrar-se no ponto final da plataforma de identidade da Microsoft. O ponto final da plataforma de identidade da Microsoft deve ser compatível com muitas bibliotecas de protocolos de código aberto sem alterações. A disponibilidade deste tipo de bibliotecas varia consoam em termos linguísticos e plataformas. Os websites [OpenID Connect](https://openid.net/connect/) e [OAuth 2.0](https://oauth.net/2/) mantêm uma lista de implementações populares. Para mais informações, consulte [a plataforma de identidade da Microsoft e as bibliotecas de autenticação](../develop/reference-v2-libraries.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json), e a lista de bibliotecas de clientes de código aberto e amostras que foram testadas com o ponto final da plataforma de identidade da Microsoft.
* Para referência, o `.well-known` ponto final para a plataforma de identidade da Microsoft é . `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration` `common`Substitua-se pela sua identificação de inquilino para obter dados específicos do seu inquilino.

### <a name="protocol-changes"></a>Alterações de protocolo

O ponto final da plataforma de identidade da Microsoft não suporta SAML ou WS-Federation; suporta apenas OpenID Connect e OAuth 2.0.  As notáveis alterações aos protocolos OAuth 2.0 do ponto final v1.0 são:

* A `email` reclamação é devolvida se uma reclamação opcional for configurada **ou** o scope=email tiver sido especificado no pedido.
* O `scope` parâmetro é agora suportado no lugar do `resource` parâmetro.
* Muitas respostas foram modificadas para torná-las mais compatíveis com a especificação OAuth 2.0, por exemplo, regressando corretamente `expires_in` como um int em vez de uma corda.

Para melhor compreender o âmbito da funcionalidade do protocolo suportado no ponto final da plataforma de identidade da Microsoft, consulte a [referência do protocolo OpenID Connect e OAuth 2.0](../develop/active-directory-v2-protocols.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

#### <a name="saml-usage"></a>Utilização de SAML

Se utilizou a Ative Directory Authentication Library (ADAL) em aplicações windows, pode ter aproveitado a autenticação integrada do Windows, que utiliza o subsídio de afirmação de marcação de afirmação de segurança (SAML). Com esta subvenção, os utilizadores de inquilinos federados da Azure AD podem autenticar silenciosamente com a sua instância ative do Diretório no local sem introduzir credenciais. Embora [a SAML ainda seja um protocolo suportado](../develop/active-directory-saml-protocol-reference.md) para utilização com utilizadores empresariais, o ponto final v2.0 é apenas para utilização com aplicações OAuth 2.0.

## <a name="next-steps"></a>Passos seguintes

Saiba mais na documentação da plataforma de identidade da [Microsoft.](../develop/index.yml)

---
title: Por que atualizar à plataforma de identidades da Microsoft (v2.0) | Azure
description: Saber as diferenças entre o ponto final do Microsoft identity platform (v2.0) e o ponto de extremidade de versão 1.0 do Azure Active Directory (Azure AD) e conheça as vantagens de atualizar para a versão 2.0.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: ryanwi
ms.reviewer: saeeda, hirsin, jmprieur, sureshja, jesakowi, lenalepa, kkrishna, negoe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ccac719c78ce2844a8dd37a80445e11baa4a488
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65962873"
---
# <a name="why-update-to-microsoft-identity-platform-v20"></a>Por que atualizar à plataforma de identidades da Microsoft (v2.0)?

Ao desenvolver um novo aplicativo, é importante saber as diferenças entre a plataforma de identidade da Microsoft (v2.0) e os pontos finais do Azure Active Directory (v1.0). Este artigo aborda as principais diferenças entre os pontos finais e algumas limitações existentes para a plataforma de identidade da Microsoft.

> [!NOTE]
> O ponto de extremidade de plataforma de identidade Microsoft não suporta todos os cenários do Azure AD e funcionalidades. Para determinar se deve utilizar o ponto de extremidade de plataforma do Microsoft identity, leia sobre [limitações de plataforma de identidade do Microsoft](#limitations).

## <a name="who-can-sign-in"></a>Quem pode iniciar sessão

![Quem pode iniciar sessão com pontos finais v1.0 e v2.0](media/azure-ad-endpoint-comparison/who-can-sign-in.svg)

* O ponto de extremidade v1.0 permite apenas contas profissionais e escolares iniciar sessão na sua aplicação (Azure AD)
* O ponto de extremidade de plataforma de identidade Microsoft permite que trabalho e contas escolares do Azure AD e contas pessoais da Microsoft (MSA), como hotmail.com, outlook.com e msn.com, para iniciar sessão.
* Ambos os pontos de extremidade também aceitam inícios de sessão do *[os utilizadores convidados](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b)* de um diretório do Azure AD para aplicações configuradas como *[inquilino único](single-and-multi-tenant-apps.md)* ou para *multi-inquilino* aplicações configuradas para apontar para o ponto final específico de inquilino (`https://login.microsoftonline.com/{TenantId_or_Name}`).

O ponto final do Microsoft identity platform permite-lhe escrever aplicações que aceitam inícios de sessão de contas Microsoft pessoais e contas profissionais e escolares. Isto dá-lhe a capacidade de escrever a sua aplicação completamente independente de conta. Por exemplo, se a aplicação chama o [Microsoft Graph](https://graph.microsoft.io), algumas funcionalidades adicionais e os dados estarão disponíveis para contas, como seus sites do SharePoint ou dados do diretório de trabalho. Mas para muitas ações, como [ler correio de um utilizador](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_messages), o mesmo código pode aceder ao e-mail para tanto pessoais e contas profissionais e escolares.

Para o ponto final de plataforma do Microsoft identity, pode utilizar o Microsoft Authentication Library (MSAL) para obter acesso para o consumidor, instituições de ensino e os mundos da empresa. O ponto de final do Azure AD v1.0 aceita inícios de sessão de contas profissionais e escolares.

## <a name="incremental-and-dynamic-consent"></a>Consentimento de incremental e dinâmico

Aplicações com o ponto de extremidade de versão 1.0 do Azure AD são necessárias para especificar as respetivas necessárias permissões de OAuth 2.0 com antecedência, por exemplo:

![Registo de permissões da interface do Usuário](./media/azure-ad-endpoint-comparison/app_reg_permissions.png)

As permissões definidas diretamente no registo de aplicação são **estático**. Enquanto estáticas permissões da aplicação definida no portal do Azure manter o código interessante e simples, ele apresenta alguns possíveis problemas para os desenvolvedores:

* A aplicação precisa solicitar todas as permissões nunca seriam necessárias após primeiro início de sessão o utilizador. Isso pode levar a uma longa lista de permissões que desestimula os utilizadores finais de aprovação de acesso da aplicação no início de sessão inicial.

* A aplicação precisa de saber todos os recursos acessaria nunca antes do tempo. Era difícil criar aplicações que podem aceder a um número arbitrário de recursos.

Com o Microsoft identity platform ponto final, pode ignorar as permissões estáticas definidas as informações de registo de aplicação nas permissões do Azure portal e o pedido de forma incremental em vez disso, que significa a solicitar a um conjunto mínimo bare de antemão as permissões e a crescer mais ao longo do tempo, como o cliente utiliza funcionalidades adicionais de aplicações. Para fazer isso, pode especificar os âmbitos a sua aplicação precisa num dado momento, incluindo novos âmbitos no `scope` parâmetro ao pedir um token de acesso - sem a necessidade de previamente defini-los em informações de registo de aplicação. Se o utilizador ainda não tiver dado consentimento novos âmbitos adicionados ao pedido, serão solicitados para dar consentimento apenas para as novas permissões. Para obter mais informações, consulte [permissões e consentimento e âmbitos](v2-permissions-and-consent.md).

Permitir que uma aplicação para solicitar permissões dinamicamente por meio do `scope` parâmetro dá aos programadores controlo total sobre a experiência dos seus utilizadores. Também pode front carga seu consentimento experiência e peça para todas as permissões numa solicitação de autorização inicial. Se a sua aplicação requer um grande número de permissões, pode recolher essas permissões do usuário incrementalmente à medida que tentam utilizar determinadas funcionalidades da aplicação ao longo do tempo.

Consentimento de administrador realizado em nome de uma organização ainda exige as permissões estáticas registadas para a aplicação, pelo que deve definir essas permissões para aplicações no portal de registo de aplicação se precisar de um administrador para dar consentimento em nome de toda a organização. Isso reduz os ciclos necessários pelo administrador da organização para configurar a aplicação.

## <a name="scopes-not-resources"></a>Âmbitos, não a recursos

Para aplicações que utilizam o ponto de extremidade v1.0, uma aplicação pode se comportar como um **recursos**, ou um destinatário de tokens. Um recurso pode definir um número de **âmbitos** ou **oAuth2Permissions** que ela compreende, permitindo que o cliente aplicações solicitar tokens desse recurso para um determinado conjunto de âmbitos. Considere o Azure AD Graph API como um exemplo de um recurso:

* Identificador de recurso, ou `AppID URI`: `https://graph.windows.net/`
* Âmbitos, ou `oAuth2Permissions`: `Directory.Read`, `Directory.Write`e assim por diante.

Isso se aplica para o ponto de final de plataforma de identidade Microsoft. Uma aplicação ainda pode se comportar como um recurso, definir escopos e ser identificada por um URI. Aplicações de cliente ainda podem solicitar acesso a esses âmbitos. No entanto, a forma que um cliente solicita essas permissões foram alteradas.

Para autorizar o ponto de extremidade v1.0, um OAuth 2.0 a pedido para o Azure AD poderão ter aparência:

```text
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https://graph.windows.net/
...
```

Aqui, o **recursos** parâmetro indicado qual o recurso que a aplicação de cliente está a solicitar autorização. O Azure AD calculadas as permissões necessárias pela aplicação com base na configuração estática no portal do Azure e emitidos tokens em conformidade.

Para aplicações utilizando o ponto de extremidade de plataforma de identidade Microsoft, o OAuth 2.0 mesmo autorizar parece do pedido:

```text
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https://graph.windows.net/directory.read%20https://graph.windows.net/directory.write
...
```

Aqui, o **âmbito** parâmetro indica quais recursos e as permissões a aplicação está a solicitar autorização. O recurso pretendido ainda está presente no pedido – ele é incluído em cada um dos valores do parâmetro de âmbito. Utilizar o parâmetro de âmbito desta forma permite que o ponto de extremidade da plataforma de identidade Microsoft ser mais compatível com a especificação de OAuth 2.0 e se alinha mais de perto com práticas comuns da indústria. Ele também permite que aplicativos para realizar [consentimento incremental](#incremental-and-dynamic-consent) – só pedir permissões quando o aplicativo requê-las, em vez de com antecedência.

## <a name="well-known-scopes"></a>Âmbitos conhecidos

### <a name="offline-access"></a>Acesso offline

Aplicações com o ponto de extremidade de plataforma de identidade Microsoft podem exigir a utilização de uma nova permissão bem conhecida para aplicações - o `offline_access` âmbito. Todas as aplicações terá de pedir esta permissão se precisarem de aceder a recursos em nome de um utilizador durante um período prolongado de tempo, mesmo quando o utilizador poderá não estar ativamente a utilizar a aplicação. O `offline_access` âmbito vai aparecer ao utilizador nas caixas de diálogo de consentimento como **aceder aos seus dados em qualquer altura**, que o utilizador tem de aceitar. Pedir o `offline_access` permissão irá permitir que a aplicação web receber o OAuth 2.0 refresh_tokens do ponto de extremidade do Microsoft identity platform. Atualizar tokens são vida longa e podem ser trocadas por longos períodos de acesso para novos tokens de acesso de OAuth 2.0.

Se a sua aplicação não pedir o `offline_access` escopo, ele não irão receber tokens de atualização. Isso significa que quando resgatar um código de autorização no fluxo de código de autorização de OAuth 2.0, apenas receberá novamente um token de acesso a partir do `/token` ponto final. Que token permanece válido durante um curto período de tempo (normalmente de uma hora) de acesso, mas, por fim, irá expirar. Nessa altura, a aplicação tem de redirecionar o utilizador de volta para o `/authorize` ponto final para obter um novo código de autorização. Durante este redirecionamento, o utilizador pode ou não poderá ter de introduzir as respetivas credenciais novamente ou reconsent de permissões, dependendo do tipo de aplicação.

Para saber mais sobre o OAuth 2.0, `refresh_tokens`, e `access_tokens`, confira o [referência do protocolo de plataforma do Microsoft identity](active-directory-v2-protocols.md).

### <a name="openid-profile-and-email"></a>E-mail, perfil e OpenID

Historicamente, o mais básico início de sessão no fluxo de OpenID Connect com a plataforma de identidade do Microsoft forneceria muitas informações sobre o utilizador no resultante *id_token*. As declarações num id_token podem incluir o utilizador nome, nome de utilizador preferencial, endereço de e-mail, ID de objeto e muito mais.

As informações que o `openid` âmbito propicia a sua aplicação o acesso ao agora é restrito. O `openid` âmbito apenas irá permitir que a sua aplicação iniciar a sessão do utilizador e receber um identificador específico de aplicação para o utilizador. Se pretender obter os dados pessoais sobre o utilizador na sua aplicação, a sua aplicação precisa solicitar permissões adicionais do usuário. Dois novos âmbitos `email` e `profile`, permite-lhe solicitar permissões adicionais.

* O `email` âmbito permite o acesso a aplicações para o endereço de e-mail primário do utilizador a `email` id_token, partindo do princípio de que o utilizador tem um endereço de e-mail endereçável de afirmação.
* O `profile` âmbito permite o acesso a aplicações para todas as outras informações básicas sobre o utilizador, como o respetivo nome, nome de utilizador preferencial, objeto ID e assim por diante, no id_token.

Nestes âmbitos permitem-lhe a aplicação de uma forma de divulgação mínima de código para que apenas pode pedir ao utilizador para o conjunto de informações que a sua aplicação precisa para fazer seu trabalho. Para obter mais informações sobre estes âmbitos, consulte [a referência de âmbito de plataforma de identidade do Microsoft](v2-permissions-and-consent.md).

## <a name="token-claims"></a>Afirmações de token

O ponto de extremidade de plataforma de identidade Microsoft emite um conjunto menor de declarações em seus tokens por predefinição, para manter cargas pequeno. Se tiver aplicações e serviços que têm uma dependência numa declaração específica num token de v1.0 que já não está incluído por padrão num token de plataforma de identidade do Microsoft, considere utilizar o [afirmações opcionais](active-directory-optional-claims.md) funcionalidade para incluir essa declaração.

## <a name="limitations"></a>Limitações

Existem algumas restrições estar atento ao utilizar a plataforma de identidade da Microsoft.

Quando criar aplicativos que se integram com a plataforma de identidade da Microsoft, precisa decidir se o Microsoft identity plataforma ponto final de autenticação protocolos e atender às suas necessidades. A plataforma e o ponto final de v1.0 é ainda totalmente suportado e, em alguns aspetos, é mais rico que plataforma de identidade da Microsoft. No entanto, plataforma de identidade da Microsoft [apresenta vantagens consideráveis](azure-ad-endpoint-comparison.md) para desenvolvedores.

Aqui está uma recomendação simplificada para os desenvolvedores agora:

* Se quer ou precisa para oferecer suporte a contas Microsoft pessoais em seu aplicativo, ou se estiver escrevendo uma nova aplicação, utilize a plataforma de identidade da Microsoft. Mas antes de o fazer, certifique-se de que compreende as limitações discutidas neste artigo.
* Se estiver a migrar ou atualizar um aplicativo que se baseia em SAML, é possível utilizar a plataforma de identidade da Microsoft. Em vez disso, consulte a [guia de versão 1.0 do Azure AD](v1-overview.md).

O ponto de extremidade de plataforma de identidade Microsoft irá evoluir para eliminar as restrições listadas aqui, para que apenas terá de utilizar o ponto de final de plataforma de identidade Microsoft. Entretanto, utilize este artigo para determinar se o ponto de extremidade de plataforma do Microsoft identity é adequado para si. Vamos continuar a atualizar este artigo para refletir o estado atual do ponto de extremidade de plataforma de identidade Microsoft. Verifique novamente para reavaliar os requisitos de recursos de plataforma de identidade Microsoft.

### <a name="restrictions-on-app-registrations"></a>Restrições em registos de aplicações

Para cada aplicação que pretende integrar com o ponto de final de plataforma de identidade Microsoft, pode criar um registo de aplicações no novo [ **registos das aplicações** experiência](https://aka.ms/appregistrations) no portal do Azure. Aplicações de conta Microsoft existentes não são compatíveis com o portal, mas todas as aplicações do Azure AD são, independentemente de onde ou quando eles foram registados.

Registos de aplicações que suportam o trabalho e contas escolares e pessoais contas têm os seguintes avisos:

* Apenas dois segredos de aplicação são permitidos por ID de aplicação.
* Um aplicativo que não foi registado num inquilino só pode ser gerido pela conta que registou. Não podem ser partilhado com outros desenvolvedores. Esse é o caso para a maioria das aplicações que foram registados com uma conta Microsoft pessoal no Portal de registo de aplicação. Se gostaria de partilhar o seu registo de aplicações com vários desenvolvedores, registar a aplicação num inquilino utilizando o novo **registos das aplicações** seção do portal do Azure.
* Existem várias restrições sobre o formato do URL de redirecionamento que é permitido. Para obter mais informações sobre o URL de redirecionamento, veja a secção seguinte.

### <a name="restrictions-on-redirect-urls"></a>Restrições em URLs de redirecionamento

Aplicações que estão registadas para plataforma de identidade da Microsoft estão limitadas a um conjunto limitado de valores de URL de redirecionamento. O URL para aplicações e serviços web tem de começar com o esquema de redirecionamento `https`, e todos os valores de URL de redirecionamento têm de partilhar um único domínio DNS.  O sistema de registo compara o nome DNS completo do URL de redirecionamento existente para o nome DNS do URL de redirecionamento que estiver a adicionar. `http://localhost` Também é suportado como um URL de redirecionamento.  

O pedido para adicionar o nome DNS irá falhar, se uma das seguintes condições for verdadeira:  

* O nome DNS completo do novo URL de redirecionamento não corresponde ao nome DNS do URL de redirecionamento existente.
* O nome DNS completo do novo URL de redirecionamento não é um subdomínio do URL de redirecionamento existente.

#### <a name="example-1"></a>Exemplo 1

Se a aplicação tiver um URL de redirecionamento de `https://login.contoso.com`, pode adicionar um URL de redirecionamento em que o nome DNS corresponde exatamente, conforme mostrado no exemplo a seguir:

`https://login.contoso.com/new`

Em alternativa, pode consultar um subdomínio DNS de login.contoso.com, conforme mostrado no exemplo a seguir:

`https://new.login.contoso.com`

#### <a name="example-2"></a>Exemplo 2

Se quiser ter uma aplicação que tenha `login-east.contoso.com` e `login-west.contoso.com` como URLs de redirecionamento, tem de adicionar os URLs de redirecionamento na seguinte ordem:

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

Pode adicionar os dois últimos porque são subdomínios do primeiro URL de redirecionamento, contoso.com.

Pode ter apenas 20 URLs de resposta para uma aplicação específica – este limite aplica-se em todos os tipos de aplicação que suporta o registo (aplicação de página única (SPA), cliente nativo, aplicação web e serviço).  

Para saber como registar uma aplicação para utilização com a plataforma de identidade da Microsoft, consulte [registar uma aplicação utilizar a nova experiência de registos de aplicação](quickstart-register-app.md).

### <a name="restrictions-on-libraries-and-sdks"></a>Restrições de bibliotecas e SDKs

Atualmente, o suporte de bibliotecas para o ponto final do Microsoft identity platform é limitado. Se quiser usar o ponto de extremidade de plataforma de identidade Microsoft num aplicativo de produção, tem estas opções:

* Se estiver criando um aplicativo web, pode utilizar com segurança o middleware de servidor em disponibilidade geral para início de sessão e validação do token. Estes incluem o middleware da OWIN OpenID Connect para ASP.NET e o plug-in de passaporte de node. js. Para exemplos de código que utilizam o middleware da Microsoft, consulte a [introdução à plataforma de identidade do Microsoft](v2-overview.md#getting-started) secção.
* Se estiver criando um aplicativo de desktop ou mobile, pode utilizar uma das bibliotecas de autenticação do Microsoft (MSAL). Essas bibliotecas estão disponíveis em geral ou numa pré-visualização do suporte de produção, por isso, é seguro para usá-los em aplicações de produção. Pode ler mais sobre os termos de pré-visualização e as bibliotecas disponíveis no [referência de bibliotecas de autenticação](reference-v2-libraries.md).
* Para plataformas não abrangidas por bibliotecas da Microsoft, pode integrar com o ponto de final de plataforma de identidade Microsoft diretamente enviar e receber mensagens de protocolo no código da aplicação. Os protocolos de OpenID Connect e OAuth [estão documentados explicitamente](active-directory-v2-protocols.md) para o ajudar a fazer uma integração desse tipo.
* Por fim, pode utilizar bibliotecas de código-fonte aberto OpenID Connect e OAuth para integrar com o ponto de extremidade de plataforma de identidade Microsoft. O ponto de extremidade de plataforma de identidade Microsoft deve ser compatível com muitas bibliotecas de protocolo aberto sem alterações. A disponibilidade desses tipos de bibliotecas varia consoante o idioma e plataforma. O [OpenID Connect](https://openid.net/connect/) e [OAuth 2.0](https://oauth.net/2/) Web sites manter uma lista de implementações populares. Para obter mais informações, consulte [bibliotecas de autenticação e a plataforma de identidade do Microsoft](reference-v2-libraries.md)e a lista de bibliotecas de cliente de código-fonte aberto e exemplos que foram testados com o ponto de extremidade de plataforma de identidade Microsoft.
* Para referência, o `.well-known` ponto final para o Microsoft identity platform comuns ponto final está `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration`. Substitua `common` com o seu ID de inquilino para obter os dados específicos ao seu inquilino.  

### <a name="protocol-changes"></a>Alterações de protocolo

O ponto de extremidade de plataforma de identidade Microsoft não suporta SAML ou WS-Federation; ela oferece suporte apenas OpenID Connect e OAuth 2.0.  Alterações importantes para os protocolos de OAuth 2.0, do ponto de extremidade de v1.0 são: 

* O `email` afirmação é devolvida se estiver configurada uma afirmação opcional **ou** âmbito = correio eletrónico foi especificado no pedido. 
* O `scope` parâmetro é agora suportado em vez do `resource` parâmetro.  
* Respostas muitos foram modificadas para torná-los mais compatível com a especificação de OAuth 2.0, por exemplo, retornando corretamente `expires_in` como um inteiro em vez de uma cadeia de caracteres.  

Para compreender melhor o âmbito da funcionalidade de protocolo suportado o ponto de final de plataforma de identidade Microsoft, consulte [referência de protocolo do OpenID Connect e OAuth 2.0](active-directory-v2-protocols.md).

#### <a name="saml-restrictions"></a>Restrições de SAML

Se já usou o Active Directory Authentication Library (ADAL) em aplicativos do Windows, que pode tomar partido da autenticação integrada do Windows, que usa a concessão de asserção de Security Assertion Markup Language (SAML). Com esta concessão, os utilizadores de federado do Azure AD inquilinos podem autenticar silenciosamente com sua instância do Active Directory no local sem introduzir as credenciais. A concessão de asserção de SAML não é suportada no ponto de final de plataforma de identidade do Microsoft.

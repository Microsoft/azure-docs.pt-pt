---
title: Por que atualizar para a plataforma Microsoft Identity (v 2.0) | Azure
description: Conheça as diferenças entre o ponto de extremidade da plataforma Microsoft Identity (v 2.0) e o ponto de extremidade do Azure Active Directory (Azure AD) v 1.0 e Aprenda os benefícios da atualização para o v 2.0.
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
ms.date: 08/01/2019
ms.author: ryanwi
ms.reviewer: saeeda, hirsin, jmprieur, sureshja, jesakowi, lenalepa, kkrishna, negoe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 815ff980abdde7ab91861d8550030476312fb6d3
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68835157"
---
# <a name="why-update-to-microsoft-identity-platform-v20"></a>Por que atualizar para a plataforma Microsoft Identity (v 2.0)?

Ao desenvolver um novo aplicativo, é importante conhecer as diferenças entre os pontos de extremidade da plataforma de identidade da Microsoft (v 2.0) e do Azure Active Directory (v 1.0). Este artigo aborda as principais diferenças entre os pontos de extremidade e algumas limitações existentes para a plataforma Microsoft Identity.

> [!NOTE]
> O ponto de extremidade da plataforma de identidade da Microsoft não dá suporte a todos os cenários e recursos do Azure AD. Para determinar se você deve usar o ponto de extremidade da plataforma de identidade da Microsoft, leia sobre as [limitações da plataforma de identidade da Microsoft](#limitations).

## <a name="who-can-sign-in"></a>Quem pode entrar

![Quem pode entrar com pontos de extremidade v 1.0 e v 2.0](media/azure-ad-endpoint-comparison/who-can-sign-in.svg)

* O ponto de extremidade v 1.0 permite que apenas contas corporativas e de estudante entrem no seu aplicativo (Azure AD)
* O ponto de extremidade da plataforma de identidade da Microsoft permite contas corporativas e de estudante do Azure AD e do MSA (contas pessoais da Microsoft), como hotmail.com, outlook.com e msn.com, para entrar.
* Os dois pontos de extremidade também aceitam entradas de *[usuários convidados](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b)* de um diretório do Azure ad para aplicativos configurados como *[locatário único](single-and-multi-tenant-apps.md)* ou para aplicativos multilocatários configurados para apontarem para o`https://login.microsoftonline.com/{TenantId_or_Name}`ponto de extremidade específico do locatário ().

O ponto de extremidade da plataforma de identidade da Microsoft permite que você escreva aplicativos que aceitem entradas de contas pessoais da Microsoft e contas corporativas e de estudante. Isso lhe dá a capacidade de escrever seu aplicativo completamente independente de conta. Por exemplo, se seu aplicativo chama o [Microsoft Graph](https://graph.microsoft.io), alguns dados e funcionalidade adicionais estarão disponíveis para contas de trabalho, como seus sites do SharePoint ou dados de diretório. Mas, para muitas ações, como [ler o email de um usuário](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_messages), o mesmo código pode acessar o email para contas pessoais e corporativas e de estudante.

Para o ponto de extremidade da plataforma Microsoft Identity, você pode usar a MSAL (biblioteca de autenticação da Microsoft) para obter acesso aos mundos do consumidor, da educação e da empresa. O ponto de extremidade v 1.0 do Azure AD aceita entradas somente de contas corporativas e de estudante.

## <a name="incremental-and-dynamic-consent"></a>Consentimento incremental e dinâmico

Os aplicativos que usam o ponto de extremidade v 1.0 do Azure AD são necessários para especificar suas permissões OAuth 2,0 necessárias com antecedência, por exemplo:

![Exemplo mostrando a interface do usuário de registro de permissões](./media/azure-ad-endpoint-comparison/app_reg_permissions.png)

As permissões definidas diretamente no registro do aplicativo são **estáticas**. Embora as permissões estáticas do aplicativo definidas no portal do Azure manter o código interessante e simples, ele apresenta alguns problemas possíveis para os desenvolvedores:

* O aplicativo precisa solicitar todas as permissões que jamais precisariam na primeira entrada do usuário. Isso pode levar a uma longa lista de permissões que desencoraja os usuários finais a aprovar o acesso do aplicativo na entrada inicial.

* O aplicativo precisa saber todos os recursos que ele nunca acessaria antes do tempo. Era difícil criar aplicativos que podiam acessar um número arbitrário de recursos.

Com o ponto de extremidade da plataforma Microsoft Identity, você pode ignorar as permissões estáticas definidas nas informações de registro do aplicativo no portal do Azure e solicitar permissões incrementalmente, o que significa solicitar um conjunto mínimo de permissões antecipadamente e crescendo mais ao longo do tempo à medida que o cliente usa recursos de aplicativo adicionais. Para fazer isso, você pode especificar os escopos de que seu aplicativo precisa a qualquer momento, incluindo os novos escopos no `scope` parâmetro ao solicitar um token de acesso-sem a necessidade de defini-los nas informações de registro do aplicativo. Se o usuário ainda não tiver consentido com novos escopos adicionados à solicitação, ele será solicitado a dar consentimento somente às novas permissões. Para saber mais, confira [permissões, consentimento e](v2-permissions-and-consent.md)escopos.

Permitir que um aplicativo solicite permissões dinamicamente por `scope` meio do parâmetro dá aos desenvolvedores controle total sobre a experiência do usuário. Você também pode carregar sua experiência de consentimento e solicitar todas as permissões em uma solicitação de autorização inicial. Se seu aplicativo exigir um grande número de permissões, você poderá reunir essas permissões do usuário de forma incremental à medida que tentarem usar determinados recursos do aplicativo ao longo do tempo.

O consentimento do administrador feito em nome de uma organização ainda requer as permissões estáticas registradas para o aplicativo, portanto, você deve definir essas permissões para aplicativos no portal de registro de aplicativo se precisar de um administrador para dar consentimento em nome de toda a organização. Isso reduz os ciclos exigidos pelo administrador da organização para configurar o aplicativo.

## <a name="scopes-not-resources"></a>Escopos, não recursos

Para aplicativos que usam o ponto de extremidade v 1.0, um aplicativo pode se comportar como um **recurso**ou um destinatário de tokens. Um recurso pode definir um número de escopos ou **oAuth2Permissions** que ele entende, permitindo que aplicativos cliente solicitem tokens desse recurso para um determinado conjunto de escopos. Considere o API do Graph do Azure AD como um exemplo de um recurso:

* Identificador de recurso ou `AppID URI`:`https://graph.windows.net/`
* Escopos, `oAuth2Permissions`ou `Directory.Read`: `Directory.Write`, e assim por diante.

Isso se aplica ao ponto de extremidade da plataforma Microsoft Identity. Um aplicativo ainda pode se comportar como um recurso, definir escopos e ser identificado por um URI. Os aplicativos cliente ainda podem solicitar acesso a esses escopos. No entanto, a maneira como um cliente solicita essas permissões foi alterada.

Para o ponto de extremidade v 1.0, uma solicitação de autorização OAuth 2,0 para o Azure AD pode ter esta aparência:

```text
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https://graph.windows.net/
...
```

Aqui, o parâmetro de **recurso** indicou qual recurso o aplicativo cliente está solicitando autorização. O Azure AD computau as permissões exigidas pelo aplicativo com base na configuração estática no portal do Azure e emitiu os tokens de acordo.

Para aplicativos que usam o ponto de extremidade da plataforma Microsoft Identity, a mesma solicitação de autorização OAuth 2,0 é semelhante a:

```text
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https://graph.windows.net/directory.read%20https://graph.windows.net/directory.write
...
```

Aqui, o parâmetro de **escopo** indica qual recurso e as permissões o aplicativo está solicitando autorização. O recurso desejado ainda está presente na solicitação – ele é englobado em cada um dos valores do parâmetro de escopo. Usar o parâmetro scope dessa maneira permite que o ponto de extremidade da plataforma de identidade da Microsoft seja mais compatível com a especificação OAuth 2,0 e se alinhe mais de acordo com as práticas comuns do setor. Ele também permite que os aplicativos façam o [consentimento incremental](#incremental-and-dynamic-consent) , solicitando permissões somente quando o aplicativo os exige, em oposição à frente.

## <a name="well-known-scopes"></a>Escopos bem conhecidos

### <a name="offline-access"></a>Acesso offline

Aplicativos que usam o ponto de extremidade da plataforma Microsoft Identity podem exigir o uso de uma nova permissão conhecida para aplicativos `offline_access` – o escopo. Todos os aplicativos precisarão solicitar essa permissão se precisarem acessar recursos em nome de um usuário por um período de tempo prolongado, mesmo quando o usuário pode não estar usando o aplicativo ativamente. O `offline_access` escopo será exibido para o usuário em caixas de diálogo de consentimento como **acessar seus dados a qualquer momento**, para o qual o usuário deve concordar. Solicitar a `offline_access` permissão permitirá que seu aplicativo Web receba o OAuth 2,0 refresh_tokens do ponto de extremidade da plataforma Microsoft Identity. Os tokens de atualização são de longa duração e podem ser trocados por novos tokens de acesso OAuth 2,0 por longos períodos de acesso.

Se seu aplicativo não solicitar o `offline_access` escopo, ele não receberá tokens de atualização. Isso significa que, ao resgatar um código de autorização no fluxo de código de autorização do OAuth 2,0, você receberá apenas um token de `/token` acesso do ponto de extremidade. Esse token de acesso permanece válido por um curto período de tempo (normalmente uma hora), mas eventualmente expirará. Nesse momento, seu aplicativo precisará redirecionar o usuário de volta para o `/authorize` ponto de extremidade para recuperar um novo código de autorização. Durante esse redirecionamento, o usuário pode ou não precisar inserir suas credenciais novamente ou consentir novamente as permissões, dependendo do tipo de aplicativo.

Para saber mais sobre o OAuth 2,0 `refresh_tokens`, o `access_tokens`e o, confira a [referência do protocolo de plataforma de identidade da Microsoft](active-directory-v2-protocols.md).

### <a name="openid-profile-and-email"></a>OpenID, perfil e email

Historicamente, o fluxo de entrada mais básico do OpenID Connect com a plataforma de identidade da Microsoft forneceria muitas informações sobre o usuário no *id_token*resultante. As declarações em um id_token podem incluir o nome do usuário, nome de usuário preferencial, endereço de email, ID de objeto e muito mais.

As informações às quais `openid` o escopo dá acesso ao aplicativo agora estão restritas. O `openid` escopo só permitirá que seu aplicativo Conecte o usuário e receba um identificador específico do aplicativo para o usuário. Se você quiser obter dados pessoais sobre o usuário em seu aplicativo, seu aplicativo precisa solicitar permissões adicionais do usuário. Dois novos escopos `email` , `profile`e, permitirão que você solicite permissões adicionais.

* O `email` escopo permite que o aplicativo acesse o endereço de email principal do usuário `email` por meio da declaração no id_token, supondo que o usuário tenha um endereço de email endereçável.
* O `profile` escopo dá ao seu aplicativo acesso a todas as outras informações básicas sobre o usuário, como nome, nome de usuário preferencial, ID de objeto e assim por diante, no id_token.

Esses escopos permitem codificar seu aplicativo em um modo de divulgação mínima para que você possa solicitar ao usuário o conjunto de informações de que seu aplicativo precisa para realizar seu trabalho. Para obter mais informações sobre esses escopos, consulte [a referência de escopo da plataforma de identidade da Microsoft](v2-permissions-and-consent.md).

## <a name="token-claims"></a>Declarações de token

O ponto de extremidade da plataforma de identidade da Microsoft emite um conjunto menor de declarações em seus tokens por padrão para manter as cargas pequenas. Se você tiver aplicativos e serviços que têm uma dependência em uma declaração específica em um token v 1.0 que não é mais fornecido por padrão em um token da plataforma de identidade da Microsoft, considere usar o recurso de [declarações opcionais](active-directory-optional-claims.md) para incluir essa declaração.

> [!IMPORTANT]
> os tokens v 1.0 e v 2.0 podem ser emitidos pelos pontos de extremidade v 1.0 e v 2.0! id_tokens *sempre* correspondem ao ponto de extremidade do qual eles são solicitados e os tokens de acesso *sempre* correspondem ao formato esperado pela API Web que seu cliente chamará usando esse token.  Portanto, se seu aplicativo usar o v 2.0 igual ao ponto para obter um token para chamar Microsoft Graph, que espera tokens de acesso de formato v 1.0, seu aplicativo receberá um token no formato v 1.0.  

## <a name="limitations"></a>Limitações

Há algumas restrições a serem consideradas ao usar a plataforma de identidade da Microsoft.

Ao criar aplicativos que se integram com a plataforma de identidade da Microsoft, você precisa decidir se os protocolos de autenticação e o ponto de extremidade da plataforma de identidade da Microsoft atendem às suas necessidades. O ponto de extremidade v 1.0 e a plataforma ainda têm suporte total e, em alguns aspectos, é mais rico em recursos do que a plataforma de identidade da Microsoft. No entanto, a plataforma de identidade da Microsoft [apresenta benefícios significativos](azure-ad-endpoint-comparison.md) para os desenvolvedores.

Aqui está uma recomendação simplificada para os desenvolvedores agora:

* Se você quiser ou precisar dar suporte a contas pessoais da Microsoft em seu aplicativo ou se estiver escrevendo um novo aplicativo, use a plataforma de identidade da Microsoft. Mas antes de fazer isso, certifique-se de entender as limitações discutidas neste artigo.
* Se você estiver migrando ou atualizando um aplicativo que dependa do SAML, não poderá usar a plataforma de identidade da Microsoft. Em vez disso, consulte o [Guia do Azure ad v 1.0](v1-overview.md).

O ponto de extremidade da plataforma de identidade da Microsoft evoluirá para eliminar as restrições listadas aqui, para que você nunca precise usar o ponto de extremidade da plataforma de identidade da Microsoft. Enquanto isso, use este artigo para determinar se o ponto de extremidade da plataforma de identidade da Microsoft é ideal para você. Continuaremos a atualizar este artigo para refletir o estado atual do ponto de extremidade da plataforma Microsoft Identity. Verifique novamente para reavaliar seus requisitos em relação aos recursos da plataforma de identidade da Microsoft.

### <a name="restrictions-on-app-registrations"></a>Restrições nos registros do aplicativo

Para cada aplicativo que você deseja integrar com o ponto de extremidade da plataforma de identidade da Microsoft, você pode criar um registro de aplicativo na nova experiência de [ **Registros de aplicativo** ](https://aka.ms/appregistrations) no portal do Azure. Os aplicativos conta Microsoft existentes não são compatíveis com o portal, mas todos os aplicativos do Azure AD são, independentemente de onde ou quando eles foram registrados.

Registros de aplicativo que dão suporte a contas corporativas e de estudante e contas pessoais têm as seguintes advertências:

* Somente dois segredos de aplicativo são permitidos por ID de aplicativo.
* Um aplicativo que não foi registrado em um locatário só pode ser gerenciado pela conta que o registrou. Ele não pode ser compartilhado com outros desenvolvedores. Esse é o caso para a maioria dos aplicativos que foram registrados usando um conta Microsoft pessoal no portal de registro de aplicativo. Se você quiser compartilhar seu registro de aplicativo com vários desenvolvedores, registre o aplicativo em um locatário usando a nova seção **registros de aplicativo** da portal do Azure.
* Há várias restrições no formato da URL de redirecionamento permitido. Para obter mais informações sobre a URL de redirecionamento, consulte a próxima seção.

### <a name="restrictions-on-redirect-urls"></a>Restrições em URLs de redirecionamento

Os aplicativos registrados para a plataforma Microsoft Identity são restritos a um conjunto limitado de valores de URL de redirecionamento. A URL de redirecionamento para aplicativos e serviços Web deve começar `https`com o esquema, e todos os valores de URL de redirecionamento devem compartilhar um único domínio DNS.  O sistema de registro compara o nome DNS completo da URL de redirecionamento existente com o nome DNS da URL de redirecionamento que você está adicionando. `http://localhost`também tem suporte como uma URL de redirecionamento.  

O pedido para adicionar o nome DNS irá falhar, se uma das seguintes condições for verdadeira:  

* O nome DNS completo da nova URL de redirecionamento não corresponde ao nome DNS da URL de redirecionamento existente.
* O nome DNS completo da nova URL de redirecionamento não é um subdomínio da URL de redirecionamento existente.

#### <a name="example-1"></a>Exemplo 1

Se o aplicativo tiver uma URL de redirecionamento de, você poderá adicionar uma URL de `https://login.contoso.com`redirecionamento na qual o nome DNS corresponde exatamente, conforme mostrado no exemplo a seguir:

`https://login.contoso.com/new`

Ou, você pode se referir a um subdomínio DNS de login.contoso.com, conforme mostrado no exemplo a seguir:

`https://new.login.contoso.com`

#### <a name="example-2"></a>Exemplo 2

Se você quiser ter um aplicativo que tenha `login-east.contoso.com` e `login-west.contoso.com` como URLs de redirecionamento, você deve adicionar essas URLs de redirecionamento na seguinte ordem:

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

Você pode adicionar os dois últimos porque eles são subdomínios da primeira URL de redirecionamento, contoso.com.

Você pode ter apenas 20 URLs de resposta para um aplicativo específico – esse limite se aplica a todos os tipos de aplicativo que o registro dá suporte (SPA (aplicativo de página única), cliente nativo, aplicativo Web e serviço).  

Para saber como registrar um aplicativo para uso com a plataforma de identidade da Microsoft, consulte [registrar um aplicativo usando a nova experiência de registros de aplicativo](quickstart-register-app.md).

### <a name="restrictions-on-libraries-and-sdks"></a>Restrições em bibliotecas e SDKs

Atualmente, o suporte à biblioteca para o ponto de extremidade da plataforma Microsoft Identity é limitado. Se você quiser usar o ponto de extremidade da plataforma de identidade da Microsoft em um aplicativo de produção, terá estas opções:

* Se você estiver criando um aplicativo Web, você pode usar com segurança o middleware do lado do servidor disponível para fazer logon e validação de token. Isso inclui o middleware OWIN OpenID Connect para ASP.NET e o plug-in do Passport do node. js. Para obter exemplos de código que usam o middleware da Microsoft, consulte a seção [introdução à plataforma de identidade da Microsoft](v2-overview.md#getting-started) .
* Se você estiver criando um aplicativo móvel ou desktop, poderá usar uma das MSAL (bibliotecas de autenticação da Microsoft). Essas bibliotecas estão geralmente disponíveis ou em uma versão prévia com suporte de produção, portanto, é seguro usá-las em aplicativos de produção. Você pode ler mais sobre os termos da visualização e as bibliotecas disponíveis na [referência de bibliotecas de autenticação](reference-v2-libraries.md).
* Para plataformas não cobertas pelas bibliotecas da Microsoft, você pode integrar-se ao ponto de extremidade da plataforma de identidade da Microsoft enviando e recebendo diretamente mensagens de protocolo no código do aplicativo. Os protocolos do OpenID Connect e do OAuth [são documentados explicitamente](active-directory-v2-protocols.md) para ajudá-lo a fazer essa integração.
* Por fim, você pode usar o OpenID Connect e as bibliotecas do OAuth de software livre para integrar com o ponto de extremidade da plataforma de identidade da Microsoft. O ponto de extremidade da plataforma de identidade da Microsoft deve ser compatível com muitas bibliotecas de protocolo de código aberto sem alterações. A disponibilidade desses tipos de bibliotecas varia de acordo com a linguagem e a plataforma. Os sites do [OpenID Connect](https://openid.net/connect/) e do [OAuth 2,0](https://oauth.net/2/) mantêm uma lista de implementações populares. Para obter mais informações, consulte [plataforma de identidade da Microsoft e bibliotecas de autenticação](reference-v2-libraries.md)e a lista de bibliotecas de cliente de software livre e exemplos que foram testados com o ponto de extremidade da plataforma Microsoft Identity.
* Para referência, o `.well-known` ponto de extremidade para o ponto de extremidade comum `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration`da plataforma de identidade da Microsoft é. Substitua `common` pela sua ID de locatário para obter dados específicos ao seu locatário.  

### <a name="protocol-changes"></a>Alterações de protocolo

O ponto de extremidade da plataforma de identidade da Microsoft não oferece suporte a SAML ou WS-Federation; Ele só dá suporte a OpenID Connect e a OAuth 2,0.  As alterações notáveis nos protocolos OAuth 2,0 do ponto de extremidade v 1.0 são: 

* A `email` declaração será retornada se uma declaração opcional for configurada **ou** Scope = email tiver sido especificado na solicitação. 
* Agora `scope` há suporte para o parâmetro no lugar `resource` do parâmetro.  
* Muitas respostas foram modificadas para torná-las mais compatíveis com a especificação do OAuth 2,0, por exemplo, `expires_in` retornando corretamente como um int em vez de uma cadeia de caracteres.  

Para entender melhor o escopo da funcionalidade de protocolo com suporte no ponto de extremidade da plataforma de identidade da Microsoft, consulte [OpenID Connect e referência de protocolo OAuth 2,0](active-directory-v2-protocols.md).

#### <a name="saml-restrictions"></a>Restrições SAML

Se você usou o Biblioteca de Autenticação do Active Directory (ADAL) em aplicativos do Windows, você pode ter aproveitado a autenticação integrada do Windows, que usa a concessão de asserção do Security Assertion Markup Language (SAML). Com essa concessão, os usuários de locatários federados do Azure AD podem se autenticar silenciosamente com sua instância de Active Directory local sem inserir credenciais. A concessão de Asserção SAML não tem suporte no ponto de extremidade da plataforma de identidade da Microsoft.

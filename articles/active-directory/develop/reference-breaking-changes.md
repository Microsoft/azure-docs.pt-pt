---
title: Azure Ative Directory quebrando referência de alterações
description: Saiba mais sobre as alterações feitas aos protocolos AZure AD que possam ter impacto na sua aplicação.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: reference
ms.date: 2/22/2021
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: c5e7f556f37a1d6d53e0a938490f1099a7be776a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101647426"
---
# <a name="whats-new-for-authentication"></a>O que há de novo para a autenticação?

> Seja notificado das atualizações desta página colando este URL no seu leitor de feed RSS:<br/>`https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20for%20authentication%22&locale=en-us`

O sistema de autenticação altera e adiciona funcionalidades de forma contínua para melhorar a conformidade com a segurança e as normas. Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece-lhe informações sobre os seguintes detalhes:

- Funcionalidades mais recentes
- Problemas conhecidos
- Alterações de protocolo
- Funcionalidade preterida

> [!TIP]
> Esta página é atualizada regularmente, por isso visite com frequência. Salvo indicação em contrário, estas alterações só são implementadas para os novos pedidos registados.

## <a name="upcoming-changes"></a>Próximas alterações

### <a name="conditional-access-will-only-trigger-for-explicitly-requested-scopes"></a>O Acesso Condicional só irá desencadear para âmbitos explicitamente solicitados

**Data efetiva**: março 2021

**Pontos finais impactados:** v2.0

**Protocolo impactado**: Todos os fluxos usando [consentimento dinâmico](v2-permissions-and-consent.md#requesting-individual-user-consent)

As aplicações que utilizam o consentimento dinâmico hoje em dia recebem todas as permissões para as quais têm consentimento, mesmo que não tenham sido solicitadas no `scope` parâmetro pelo nome.  Isto pode fazer com que uma aplicação que solicite `user.read` apenas, mas com consentimento para , seja forçada a passar o Acesso Condicional atribuído para a `files.read` `files.read` permissão. 

Para reduzir o número de pedidos de acesso condicional desnecessários, o Azure AD está a alterar a forma como os âmbitos não solicitados são fornecidos às aplicações de modo que apenas os âmbitos explicitamente solicitados desencadeiam o Acesso Condicional. Esta alteração pode fazer com que as aplicações dependentes do comportamento anterior da AZure AD (nomeadamente, fornecendo todas as permissões mesmo quando não foram solicitadas) para quebrar, uma vez que os tokens que solicitam estarão a faltar permissões.

As aplicações passarão a receber tokens de acesso com um misto de permissões neste caso - as solicitadas, bem como aquelas que tenham consentimento para que não necessitem de solicitações de Acesso Condicional.  Os âmbitos do token de acesso refletem-se no parâmetro da resposta `scope` simbólica. 

**Exemplos**

Uma aplicação tem consentimento para `user.read` `files.readwrite` , e `tasks.read` . `files.readwrite` tem políticas de acesso condicional aplicadas a ele, enquanto as outras duas não. Se uma aplicação fizer um pedido simbólico para `scope=user.read` , e o atualmente assinado no utilizador não tiver aprovado nenhuma política de Acesso Condicional, então o token resultante será para as `user.read` `tasks.read` permissões e permissões. `tasks.read` está incluído porque a aplicação tem consentimento para isso, e não requer que seja aplicada uma política de Acesso Condicional. 

Se a aplicação solicitar `scope=files.readwrite` então , o Acesso Condicional exigido pelo arrendatário irá desencadear, forçando a app a mostrar uma auth prompt interativa onde a política de Acesso Condicional pode ser satisfeita.  O símbolo devolvido terá os três âmbitos. 

Se a aplicação fizer então um último pedido para qualquer um dos três âmbitos (por exemplo, `scope=tasks.read` ), a Azure AD verá que o utilizador já completou as políticas de acesso condicional necessárias para `files.readwrite` , e novamente emitir um token com as três permissões na mesma. 


## <a name="may-2020"></a>Maio de 2020

### <a name="azure-government-endpoints-are-changing"></a>Os pontos finais do Governo de Azure estão a mudar

**Data de edição**: 5 de maio (terminando junho de 2020) 

**Pontos finais impactados:** Todos os pontos

**Protocolo impactado**: Todos os fluxos

Em 1 de junho de 2018, a Autoridade Oficial do Azure Ative Directory (AAD) para o Governo Azure passou de `https://login-us.microsoftonline.com` `https://login.microsoftonline.us` . Esta alteração também se aplicava ao Microsoft 365 GCC High e DoD, que o Azure Government AAD também presta serviços. Se possuir uma aplicação dentro de um inquilino do Governo dos EUA, deve atualizar a sua aplicação para iniciar sômsião no `.us` ponto final.  

A partir de 5 de maio, a Azure AD começará a impor a mudança de ponto final, impedindo os utilizadores do governo de assinarem em aplicações hospedadas em inquilinos do governo dos EUA usando o ponto final público `microsoftonline.com` .  As aplicações impactadas começarão a ver um `AADSTS900439`  -  `USGClientNotSupportedOnPublicEndpoint` erro. Este erro indica que a aplicação está a tentar assinar num utilizador do Governo dos EUA no ponto final da nuvem pública. Se a sua aplicação estiver num inquilino de nuvem pública e tiver a intenção de apoiar utilizadores do Governo dos EUA, terá de [atualizar a sua app para as suportar explicitamente.](./authentication-national-cloud.md) Isto pode exigir a criação de um novo registo de aplicações na nuvem do Governo dos EUA. 

A aplicação desta mudança será feita utilizando um lançamento gradual baseado na frequência com que os utilizadores do Governo dos EUA assinam a sua assinatura na aplicação - as aplicações que assinam no Governo dos EUA raramente verão a aplicação em primeiro lugar, e as aplicações frequentemente utilizadas pelos utilizadores do Governo dos EUA serão as últimas a ter aplicação da aplicação. Esperamos que a aplicação esteja concluída em todas as aplicações em junho de 2020. 

Para mais detalhes, consulte o post de blog do [Governo Azure sobre esta migração.](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/) 

## <a name="march-2020"></a>Março de 2020

### <a name="user-passwords-will-be-restricted-to-256-characters"></a>As palavras-passe do utilizador serão limitadas a 256 caracteres.

**Data efetiva**: 13 de março de 2020

**Pontos finais impactados:** Todos os pontos

**Protocolo impactado**: Todos os fluxos de utilizador.

Os utilizadores com senhas superiores a 256 caracteres que assinem diretamente com a AZure AD (em oposição a um IDP federado como o ADFS) não poderão assinar a partir de 13 de março de 2020 e ser-lhes-á pedido que repor a sua palavra-passe.  Os administradores podem receber pedidos para ajudar a redefinir a palavra-passe dos utilizadores.

O erro no sinal nos registos será AADSTS 50052: InvalidPasswordExceedsMaxLength

Mensagem: `The password entered exceeds the maximum length of 256. Please reach out to your admin to reset the password.`

Remediação:

O utilizador não consegue iniciar sessão porque a sua palavra-passe excede o comprimento máximo permitido. Devem contactar o administrador para redefinir a senha. Se a SSPR estiver ativada para o seu inquilino, podem redefinir a sua palavra-passe seguindo o link "Esqueci-me da sua palavra-passe".



## <a name="february-2020"></a>Fevereiro de 2020

### <a name="empty-fragments-will-be-appended-to-every-http-redirect-from-the-login-endpoint"></a>Os fragmentos vazios serão anexados a todos os redirecionamentos HTTP do ponto final de início de sessão.

**Data efetiva**: 8 de fevereiro de 2020

**Pontos finais impactados**: V1.0 e v2.0

**Protocolo impactado**: Fluxos Oauth e OIDC que utilizam response_type=consulta - isto abrange o fluxo do código de [autorização](v2-oauth2-auth-code-flow.md) em alguns casos, e o [fluxo implícito](v2-oauth2-implicit-grant-flow.md).

Quando uma resposta de autenticação é enviada de login.microsoftonline.com para uma aplicação através de redirecionamento HTTP, o serviço anexará um fragmento vazio ao URL de resposta.  Isto impede uma classe de ataques de redirecionamento, garantindo que o navegador elimina qualquer fragmento existente no pedido de autenticação.  Nenhuma aplicação deve ter uma dependência deste comportamento.


## <a name="august-2019"></a>Agosto de 2019

### <a name="post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored"></a>Semântica de formulário post será aplicada mais rigorosamente - espaços e citações serão ignorados

**Data efetiva**: 2 de setembro de 2019

**Pontos finais impactados**: V1.0 e v2.0

**Protocolo impactado**: Qualquer posto de correio é utilizado [(credenciais de cliente,](./v2-oauth2-client-creds-grant-flow.md) [resgate de código de autorização,](./v2-oauth2-auth-code-flow.md) [ROPC,](./v2-oauth-ropc.md) [OBO,](./v2-oauth2-on-behalf-of-flow.md)e [resgate de fichas de atualização)](./v2-oauth2-auth-code-flow.md#refresh-the-access-token)

A partir da semana de 9/2, os pedidos de autenticação que utilizem o método POST serão validados com normas HTTP mais rigorosas.  Especificamente, os espaços e as cotações duplas (") deixarão de ser removidos dos valores do formulário de pedido. Não se espera que estas alterações quebrem os clientes existentes, e garantirão que os pedidos enviados para a Azure AD são manuseados de forma fiável todas as vezes. No futuro (ver acima) planeamos rejeitar adicionalmente duplicar parâmetros e ignorar o BOM dentro dos pedidos.

Exemplo:

Hoje, `?e=    "f"&g=h` é analisado de forma idêntica como - assim `?e=f&g=h` `e`  ==  `f` .  Com esta alteração, seria agora analisado de modo a que `e`  ==  `    "f"` - este é improvável que seja um argumento válido, e o pedido iria agora falhar.


## <a name="july-2019"></a>Julho de 2019

### <a name="app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant"></a>Os tokens apenas para aplicações de inquilino único só são emitidos se a app do cliente existir no inquilino de recursos

**Data de efetivo**: 26 de julho de 2019

**Pontos finais impactados**: [V1.0](../azuread-dev/v1-oauth2-client-creds-grant-flow.md) e [v2.0](./v2-oauth2-client-creds-grant-flow.md)

**Protocolo impactado**: [Credenciais de Cliente (fichas só para aplicações)](../azuread-dev/v1-oauth2-client-creds-grant-flow.md)

Uma mudança de segurança entrou em direto a 26 de julho que altera a forma como são emitidas fichas só de aplicações (através da concessão de credenciais de cliente). Anteriormente, as aplicações eram autorizadas a obter fichas para ligar para qualquer outra app, independentemente da presença no arrendatário ou funções consentidas para essa aplicação.  Este comportamento foi atualizado de modo que para os recursos (por vezes chamados APIs web) definidos para ser um único inquilino (o padrão), a aplicação do cliente deve existir dentro do inquilino de recursos.  Note que o consentimento existente entre o cliente e a API ainda não é necessário, e as aplicações devem ainda estar a fazer os seus próprios controlos de autorização para garantir que uma `roles` reclamação está presente e contém o valor esperado para a API.

A mensagem de erro para este cenário indica atualmente:

`The service principal named <appName> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.`

Para remediar este problema, utilize a experiência Admin Consent para criar o principal do serviço de aplicação do cliente no seu inquilino ou criá-lo manualmente.  Este requisito garante que o arrendatário tenha dado autorização ao pedido para operar dentro do arrendatário.

#### <a name="example-request"></a>Pedido de exemplo

`https://login.microsoftonline.com/contoso.com/oauth2/authorize?resource=https://gateway.contoso.com/api&response_type=token&client_id=14c88eee-b3e2-4bb0-9233-f5e3053b3a28&...` Neste exemplo, o inquilino de recursos (autoridade) é contoso.com, a app de recursos é uma app de inquilino único chamada `gateway.contoso.com/api` para o inquilino Contoso, e a app do cliente é `14c88eee-b3e2-4bb0-9233-f5e3053b3a28` .  Se a aplicação do cliente tiver um principal de serviço dentro de Contoso.com, este pedido pode continuar.  No entanto, se não o fizer, o pedido falhará com o erro acima.

No entanto, se a aplicação Contoso Gateway fosse uma aplicação multi-arrendatário, então o pedido continuaria independentemente da aplicação do cliente ter um principal de serviço dentro de Contoso.com.

### <a name="redirect-uris-can-now-contain-query-string-parameters"></a>Redirecionar URIs pode agora conter parâmetros de cadeia de consulta

**Data de efetivo**: 22 de julho de 2019

**Pontos finais impactados**: V1.0 e v2.0

**Protocolo impactado**: Todos os fluxos

Por [RFC 6749,](https://tools.ietf.org/html/rfc6749#section-3.1.2)as aplicações AD do Azure podem agora registar e utilizar URIs de redirecionamento (resposta) com parâmetros de consulta estática (tais `https://contoso.com/oauth2?idp=microsoft` como) para pedidos de OAuth 2.0.  Os URIs de redirecionamento dinâmico continuam a ser proibidos, uma vez que representam um risco de segurança, o que não pode ser utilizado para reter informações estatais através de um pedido de autenticação - para isso, utilize o `state` parâmetro.

O parâmetro de consulta estática está sujeito a combinação de cordas para uris de redirecionamento como qualquer outra parte do URI de redirecionamento - se não for registada nenhuma cadeia que corresponda ao redirect_uri descodificado uri, então o pedido será rejeitado.  Se o URI for encontrado no registo da aplicação, então toda a cadeia será usada para redirecionar o utilizador, incluindo o parâmetro de consulta estática.

Note-se que neste momento (final de julho de 2019), o registo de aplicações UX no portal Azure ainda bloqueia parâmetros de consulta.  No entanto, pode editar manualmente o manifesto da aplicação para adicionar parâmetros de consulta e testá-lo na sua aplicação.


## <a name="march-2019"></a>Março de 2019

### <a name="looping-clients-will-be-interrupted"></a>Os clientes em loop serão interrompidos

**Data efetiva**: 25 de março de 2019

**Pontos finais impactados**: V1.0 e v2.0

**Protocolo impactado**: Todos os fluxos

As aplicações do cliente podem por vezes portar-se mal, emitindo centenas do mesmo pedido de login durante um curto período de tempo.  Estes pedidos podem ou não ser bem sucedidos, mas todos contribuem para uma má experiência do utilizador e para o aumento da carga de trabalho para o IDP, aumentando a latência para todos os utilizadores e reduzindo a disponibilidade do IDP.  Estas aplicações estão a funcionar fora dos limites de utilização normal, e devem ser atualizadas para se comportarem corretamente.

Os clientes que emitem pedidos duplicados serão enviados um `invalid_grant` erro: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` .

A maioria dos clientes não precisará de alterar o comportamento para evitar este erro.  Apenas os clientes mal configurados (aqueles que não têm caching simbólico ou aqueles que já exibem loops rápidos) serão impactados por este erro.  Os clientes são rastreados por instância localmente (via cookie) sobre os seguintes fatores:

* Dica do utilizador, se houver

* Âmbitos ou recursos solicitados

* ID de Cliente

* URI de Redirecionamento

* Tipo e modo de resposta

As aplicações que fizerem vários pedidos (15+) num curto espaço de tempo (5 minutos) receberão um `invalid_grant` erro explicando que estão em loop.  As fichas solicitadas têm vidas suficientemente longas (10 minutos no mínimo, 60 minutos por defeito), pelo que os pedidos repetidos ao longo deste período de tempo são desnecessários.

Todas as aplicações devem ser `invalid_grant` tratadas mostrando uma solicitação interativa, em vez de solicitar silenciosamente um token.  Para evitar este erro, os clientes devem garantir que estão a caching corretamente as fichas que recebem.


## <a name="october-2018"></a>Outubro de 2018

### <a name="authorization-codes-can-no-longer-be-reused"></a>Os códigos de autorização já não podem ser reutilizados

**Data efetiva**: 15 de novembro de 2018

**Pontos finais impactados**: V1.0 e v2.0

**Protocolo impactado**: [Fluxo de código](v2-oauth2-auth-code-flow.md)

A partir de 15 de novembro de 2018, o Azure AD deixará de aceitar códigos de autenticação usados anteriormente para apps. Esta mudança de segurança ajuda a alinhar o Azure AD com a especificação OAuth e será aplicada tanto nos pontos finais v1 como v2.

Se a sua aplicação reutilizar códigos de autorização para obter fichas para vários recursos, recomendamos que use o código para obter um token de atualização e, em seguida, use esse token de atualização para adquirir fichas adicionais para outros recursos. Os códigos de autorização só podem ser usados uma vez, mas os tokens de atualização podem ser usados várias vezes em vários recursos. Qualquer nova aplicação que tente reutilizar um código de autenticação durante o fluxo de código OAuth terá um erro invalid_grant.

Para obter mais informações sobre tokens de atualização, consulte [Refresh the access tokens](v2-oauth2-auth-code-flow.md#refresh-the-access-token).  Se utilizar a ADAL ou a MSAL, esta é tratada por si pela biblioteca - substitua a segunda instância de 'AcquireTokenByAuthorizationCodeAsync' por 'AcquireTokenSilentAsync'.

## <a name="may-2018"></a>Maio de 2018

### <a name="id-tokens-cannot-be-used-for-the-obo-flow"></a>Fichas de ID não podem ser usadas para o fluxo OBO

**Data**: 1 de maio de 2018

**Pontos finais impactados**: V1.0 e v2.0

**Protocolos impactados**: Fluxo implícito e [em nome do fluxo](v2-oauth2-on-behalf-of-flow.md)

Depois de 1 de maio de 2018, id_tokens não podem ser usadas como afirmação num fluxo OBO para novas aplicações. Os tokens de acesso devem ser usados em vez de garantir APIs, mesmo entre um cliente e o nível médio da mesma aplicação. As aplicações registadas antes de 1 de maio de 2018 continuarão a funcionar e poderão trocar id_tokens por um token de acesso; no entanto, este padrão não é considerado uma boa prática.

Para contornar esta mudança, pode fazer o seguinte:

1. Crie uma API web para a sua aplicação, com um ou mais âmbitos. Este ponto de entrada explícito permitirá um controlo e segurança mais finos.
1. No manifesto da sua aplicação, no [portal Azure](https://portal.azure.com) ou no portal de registo de [aplicações,](https://apps.dev.microsoft.com)garante que a aplicação é permitida a emissão de tokens de acesso através do fluxo implícito. Isto é controlado através da `oauth2AllowImplicitFlow` chave.
1. Quando a sua aplicação ao cliente solicita uma id_token `response_type=id_token` via, solicite também um token de acesso `response_type=token` para a API web criada acima. Assim, ao utilizar o ponto final v2.0, o `scope` parâmetro deve parecer semelhante a `api://GUID/SCOPE` . No ponto final v1.0, o `resource` parâmetro deve ser a aplicação URI da API web.
1. Passe este símbolo de acesso para o nível médio no lugar do id_token.

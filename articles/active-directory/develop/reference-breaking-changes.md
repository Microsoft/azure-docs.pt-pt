---
title: Quebra do Diretório Ativo Azure altera referência Microsoft Docs
description: Conheça as alterações feitas aos protocolos DaD Azure que podem afetar a sua aplicação.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 68517c83-1279-4cc7-a7c1-c7ccc3dbe146
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 3/13/2020
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 1248063c1b4c1b1e124ff671797450dd5c1b8727
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050173"
---
# <a name="whats-new-for-authentication"></a>O que há de novo para autenticação? 

>Seja notificado sobre as atualizações para esta página. Basta adicionar [este URL](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20for%20authentication%22&locale=en-us) ao leitor de feed RSS.

O sistema de autenticação altera e adiciona funcionalidades de forma contínua para melhorar a conformidade com a segurança e as normas. Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece-lhe informações sobre os seguintes detalhes:

- Funcionalidades mais recentes
- Problemas conhecidos
- Alterações de protocolo
- Funcionalidade preterida

> [!TIP] 
> Esta página é atualizada regularmente, por isso visite com frequência. Salvo indicação em contrário, estas alterações só são implementadas para pedidos recém-registados.  

## <a name="upcoming-changes"></a>Próximas alterações

Nenhum agendado neste momento.  Veja abaixo as alterações que estão dentro ou estão a chegar à produção. 

## <a name="march-2020"></a>março de 2020 

### <a name="user-passwords-will-be-restricted-to-256-characters"></a>As palavras-passe dos utilizadores serão restritas a 256 caracteres.

**Data de funcionação**: 13 de março de 2020

**Pontos finais impactados**: V1.0 e v2.0

**Protocolo impactado**: Todos os fluxos de utilizador. 

Os utilizadores com senhas superiores a 256 caracteres que acedam diretamente ao Azure AD (em oposição a um IDP federado como o ADFS) não poderão assinar a partir de 13 de março de 2020, e serão convidados a redefinir a sua palavra-passe em vez disso.  Os administradores podem receber pedidos para ajudar a redefinir a palavra-passe dos utilizadores. 

O erro no registo será AADSTS 50052: InvalidPasswordExceedsMaxLength

Mensagem:`The password entered exceeds the maximum length of 256. Please reach out to your admin to reset the password.`

Reparação:

O utilizador não pode fazer login porque a sua palavra-passe excede o comprimento máximo permitido. Devem contactar o administrador para redefinir a senha. Se o SSPR estiver ativado para o seu inquilino, podem redefinir a sua palavra-passe seguindo o link "Esqueci-me da palavra-passe".



## <a name="february-2020"></a>Fevereiro de 2020 

### <a name="empty-fragments-will-be-appended-to-every-http-redirect-from-the-login-endpoint"></a>Fragmentos vazios serão anexados a cada redirecionamento HTTP a partir do ponto final de login. 

**Data de funcionação**: 8 de fevereiro de 2020

**Pontos finais impactados**: V1.0 e v2.0

**Protocolo impactado**: Os fluxos OAuth e OIDC que utilizam response_type=consulta - isto cobre o fluxo do código de [autorização](v2-oauth2-auth-code-flow.md) em alguns casos, e o [fluxo implícito](v2-oauth2-implicit-grant-flow.md). 

Quando uma resposta de autenticação for enviada de login.microsoftonline.com para uma aplicação através de redirecionamento HTTP, o serviço anexará um fragmento vazio ao URL de resposta.  Isto impede uma classe de ataques redirecionados, garantindo que o navegador elimina qualquer fragmento existente no pedido de autenticação.  Nenhuma aplicação deve ter uma dependência deste comportamento. 


## <a name="august-2019"></a>Agosto de 2019

### <a name="post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored"></a>Semântica de formulário post será aplicada mais estritamente - espaços e citações serão ignorados

**Data de funcionação**: 2 de setembro de 2019

**Pontos finais impactados**: V1.0 e v2.0

**Protocolo impactado**: Em qualquer lugar o POST é usado ([credenciais de cliente,](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow)resgate de [código de autorização,](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow) [ROPC,](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth-ropc) [OBO,](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)e [refrescamento de resgate simbólico](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow#refresh-the-access-token))

A partir da semana de 9/2, os pedidos de autenticação que utilizem o método POST serão validados utilizando normas HTTP mais rigorosas.  Especificamente, os espaços e as pasções duplas (") deixarão de ser removidos dos valores do formulário de pedido. Estas alterações não deverão quebrar quaisquer clientes existentes, e garantirão que os pedidos enviados para a Azure AD sejam tratados de forma fiável cada vez. No futuro (ver acima) planeamos rejeitar adicionalmente os parâmetros duplicados e ignorar o BOM dentro dos pedidos. 

Exemplo:

Hoje, `?e=    "f"&g=h` é analisado da `?e=f&g=h` mesma forma `e`  ==  `f`que - assim .  Com esta alteração, seria agora analisado de `e`  ==  `    "f"` modo a que - é pouco provável que este seja um argumento válido, e o pedido agora falharia. 


## <a name="july-2019"></a>Julho de 2019

### <a name="app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant"></a>Tokens apenas para aplicações de inquilino único só são emitidos se a aplicação do cliente existir no inquilino de recursos

**Data de funcionação**: 26 de julho de 2019

**Pontos finais impactados**: [V1.0](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow) e [v2.0](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow)

**Protocolo impactado**: [Credenciais de cliente (fichas apenas de aplicações)](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)

Uma mudança de segurança foi transmitida em direto a 26 de julho que altera a forma como são emitidas fichas apenas de aplicações (através da concessão de credenciais de cliente). Anteriormente, as aplicações eram autorizadas a obter fichas para ligar para qualquer outra app, independentemente da presença no inquilino ou funções consentidos para essa aplicação.  Este comportamento foi atualizado de modo que para os recursos (por vezes chamados APIs Web) definidos como um único inquilino (o padrão), a aplicação do cliente deve existir dentro do inquilino de recursos.  Note que o consentimento existente entre o cliente e a API ainda não é necessário, `roles` e as aplicações devem ainda estar a fazer os seus próprios controlos de autorização para garantir que uma reclamação está presente e contém o valor esperado para a API.

A mensagem de erro para este cenário indica atualmente: 

`The service principal named <appName> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.`

Para resolver este problema, utilize a experiência Admin Consent para criar o principal do serviço de aplicação do cliente no seu inquilino, ou criá-lo manualmente.  Este requisito garante que o inquilino autorizou o pedido de funcionamento dentro do arrendatário.  

#### <a name="example-request"></a>Pedido de exemplo

`https://login.microsoftonline.com/contoso.com/oauth2/authorize?resource=https://gateway.contoso.com/api&response_type=token&client_id=14c88eee-b3e2-4bb0-9233-f5e3053b3a28&...`Neste exemplo, o inquilino de recursos (autoridade) é contoso.com, a `gateway.contoso.com/api` app de recursos é uma aplicação de inquilino único chamada para o inquilino Contoso, e a app do cliente é `14c88eee-b3e2-4bb0-9233-f5e3053b3a28`.  Se a aplicação do cliente tiver um diretor de serviço dentro Contoso.com, este pedido pode continuar.  Se não o fizer, no entanto, o pedido falhará com o erro acima.  

Se a aplicação Contoso gateway fosse uma aplicação multi-inquilino, no entanto, o pedido continuaria independentemente da aplicação do cliente ter um diretor de serviço dentro de Contoso.com.  

### <a name="redirect-uris-can-now-contain-query-string-parameters"></a>Redirecionamento de URIs pode agora conter parâmetros de cadeia de consulta

**Data de funcionação**: 22 de julho de 2019

**Pontos finais impactados**: V1.0 e v2.0

**Protocolo impactado**: Todos os fluxos

Por [RFC 6749,](https://tools.ietf.org/html/rfc6749#section-3.1.2)as aplicações Azure AD podem agora registar e utilizar URIs `https://contoso.com/oauth2?idp=microsoft`redirecionais (resposta) com parâmetros de consulta estática (como) para pedidos oAuth 2.0.  As URIs de redirecionamento dinâmico continuam proibidas, uma vez que representam um risco de segurança, e isso não pode ser utilizado para reter informações estatais através de um pedido de autenticação - para isso, utilizar o `state` parâmetro.

O parâmetro de consulta estática está sujeito a correspondência de cordas para URIs redirecionados como qualquer outra parte do URI redirecionado - se não for registada nenhuma cadeia que corresponda ao redirect_uri descodificado pela URI, então o pedido será rejeitado.  Se o URI for encontrado no registo da aplicação, então toda a cadeia será usada para redirecionar o utilizador, incluindo o parâmetro de consulta estática. 

Note que nesta altura (final de julho de 2019), o registo de aplicações UX no portal Azure ainda bloqueia parâmetros de consulta.  No entanto, pode editar manualmente o manifesto da aplicação para adicionar parâmetros de consulta e testá-lo na sua aplicação.  


## <a name="march-2019"></a>Março de 2019

### <a name="looping-clients-will-be-interrupted"></a>Clientes em loop serão interrompidos

**Data de funcionação**: 25 de março de 2019

**Pontos finais impactados**: V1.0 e v2.0

**Protocolo impactado**: Todos os fluxos

As aplicações dos clientes podem por vezes portar-se mal, emitindo centenas do mesmo pedido de login durante um curto período de tempo.  Estes pedidos podem ou não ser bem sucedidos, mas todos contribuem para a má experiência do utilizador e para o aumento da carga horária para o IDP, aumentando a latência para todos os utilizadores e reduzindo a disponibilidade do IDP.  Estas aplicações estão a funcionar fora dos limites da utilização normal e devem ser atualizadas para se comportarem corretamente.  

Os clientes que emitirem pedidos duplicados `invalid_grant` várias vezes serão enviados um erro: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request`. 

A maioria dos clientes não precisará de mudar de comportamento para evitar este erro.  Apenas os clientes mal configurados (aqueles sem cache de fichaou ou aqueles que já exibem loops rápidos) serão impactados por este erro.  Os clientes são rastreados por exemplo localmente (via cookie) nos seguintes fatores:

* Dica do utilizador, se houver

* Âmbitos ou recursos solicitados

* ID de Cliente

* URI de Redirecionamento

* Tipo e modo de resposta

As aplicações que fizerem vários pedidos (15+) num `invalid_grant` curto espaço de tempo (5 minutos) receberão um erro explicando que estão a fazer looping.  Os símbolos que estão a ser solicitados têm vida útil suficiente (10 minutos mínimos, 60 minutos por defeito), pelo que os pedidos repetidos durante este período são desnecessários.  

Todas as `invalid_grant` aplicações devem manusear mostrando um pedido interativo, em vez de pedir silenciosamente um símbolo.  Para evitar este erro, os clientes devem certificar-se de que estão a cortar corretamente as fichas que recebem.


## <a name="october-2018"></a>Outubro de 2018

### <a name="authorization-codes-can-no-longer-be-reused"></a>Os códigos de autorização já não podem ser reutilizados

**Data de funcionação**: 15 de novembro de 2018

**Pontos finais impactados**: V1.0 e v2.0

**Protocolo impactado**: [Fluxo de código](v2-oauth2-auth-code-flow.md)

A partir de 15 de novembro de 2018, a Azure AD deixará de aceitar códigos de autenticação usados anteriormente para apps. Esta mudança de segurança ajuda a alinhar a AD Azure com a especificação OAuth e será aplicada tanto nos pontos finais v1 como v2.

Se a sua aplicação reutilizar códigos de autorização para obter fichas para vários recursos, recomendamos que use o código para obter um token de atualização e, em seguida, use esse token de atualização para adquirir tokens adicionais para outros recursos. Os códigos de autorização só podem ser usados uma vez, mas tokens de atualização podem ser usados várias vezes em vários recursos. Qualquer nova aplicação que tente reutilizar um código de autenticação durante o fluxo de código OAuth terá um erro invalid_grant.

Para mais informações sobre fichas de atualização, consulte [Refreshing as fichas](v2-oauth2-auth-code-flow.md#refresh-the-access-token)de acesso .  Se utilizar a ADAL ou a MSAL, esta é tratada pela biblioteca - substitua a segunda instância de 'AcquireTokenByAuthorizationCodeAsync' por 'AcquireTokenSilentAsync'. 

## <a name="may-2018"></a>Maio de 2018

### <a name="id-tokens-cannot-be-used-for-the-obo-flow"></a>Fichas de identificação não podem ser usadas para o fluxo obo

**Data**: 1 de maio de 2018

**Pontos finais impactados**: V1.0 e v2.0

**Protocolos impactados**: Fluxo implícito e [em nome do fluxo](v2-oauth2-on-behalf-of-flow.md)

Depois de 1 de maio de 2018, id_tokens não pode ser usado como afirmação num fluxo obo para novas aplicações. Em vez disso, devem ser utilizados fichas de acesso para garantir APIs, mesmo entre um cliente e um nível médio da mesma aplicação. As aplicações registadas antes de 1 de maio de 2018 continuarão a funcionar e poderão trocar id_tokens por um sinal de acesso; no entanto, este padrão não é considerado uma boa prática.

Para contornar esta mudança, pode fazer o seguinte:

1. Crie uma API Web para a sua aplicação, com um ou mais âmbitos. Este ponto de entrada explícito permitirá um controlo e segurança mais finos.
1. No manifesto da sua aplicação, no [portal Azure](https://portal.azure.com) ou no portal de registo de [aplicações,](https://apps.dev.microsoft.com)certifique-se de que a aplicação está autorizada a emitir fichas de acesso através do fluxo implícito. Isto é controlado `oauth2AllowImplicitFlow` através da chave.
1. Quando a sua aplicação `response_type=id_token`de cliente solicita uma`response_type=token`id_token via, também solicite um sinal de acesso ( ) para a Web API criada acima. Assim, ao utilizar o ponto final `scope` v2.0, `api://GUID/SCOPE`o parâmetro deve ser semelhante a . No ponto final v1.0, o `resource` parâmetro deve ser a aplicação URI da Web API.
1. Passe este sinal de acesso para o nível médio no lugar do id_token.  

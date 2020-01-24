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
ms.date: 08/28/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: b3d5aa74705d858349eaca543a7fd86e315a63e6
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76703002"
---
# <a name="whats-new-for-authentication"></a>O que há de novo para autenticação? 

>Seja notificado sobre as atualizações para esta página. Basta adicionar [este URL](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20for%20authentication%22&locale=en-us) ao leitor de feed RSS.

O sistema de autenticação altera e adiciona funcionalidades de forma contínua para melhorar a conformidade com a segurança e as normas. Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece-lhe informações sobre os seguintes detalhes:

- Funcionalidades mais recentes
- Problemas conhecidos
- Alterações de protocolo
- Funcionalidades preteridas

> [!TIP] 
> Esta página é atualizada regularmente, por isso visite com frequência. Salvo indicação em contrário, estas alterações só são implementadas para pedidos recém-registados.  

## <a name="upcoming-changes"></a>Alterações futuras

Setembro de 2019: Aplicação adicional da semântica post de acordo com as regras de análise de URL - os parâmetros duplicados desencadearão um erro e [a BOM](https://www.w3.org/International/questions/qa-byte-order-mark) ignorada.

## <a name="august-2019"></a>Agosto de 2019

### <a name="post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored"></a>Semântica de formulário post será aplicada mais estritamente - espaços e citações serão ignorados

**Data de funcionação**: 2 de setembro de 2019

**Pontos finais impactados**: V1.0 e v2.0

**Protocolo impactado**: Em qualquer lugar o POST é usado ([credenciais de cliente,](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow)resgate de [código de autorização,](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow) [ROPC,](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth-ropc) [OBO,](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)e [refrescamento de resgate simbólico](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow#refresh-the-access-token))

A partir da semana de 9/2, os pedidos de autenticação que utilizem o método POST serão validados utilizando normas HTTP mais rigorosas.  Especificamente, os espaços e as pasções duplas (") deixarão de ser removidos dos valores do formulário de pedido. Estas alterações não deverão quebrar quaisquer clientes existentes, e garantirão que os pedidos enviados para a Azure AD sejam tratados de forma fiável cada vez. No futuro (ver acima) planeamos rejeitar adicionalmente os parâmetros duplicados e ignorar o BOM dentro dos pedidos. 

Exemplo:

Hoje, `?e=    "f"&g=h` é analisado da mesma forma que `?e=f&g=h` - por isso `e` == `f`.  Com esta alteração, seria agora analisada de modo a que `e` == `    "f"` - é pouco provável que este seja um argumento válido, e o pedido falharia agora. 


## <a name="july-2019"></a>Julho de 2019

### <a name="app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant"></a>Tokens apenas para aplicações de inquilino único só são emitidos se a aplicação do cliente existir no inquilino de recursos

**Data de funcionação**: 26 de julho de 2019

**Pontos finais impactados**: [V1.0](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow) e [v2.0](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow)

**Protocolo impactado**: [Credenciais de cliente (fichas apenas de aplicações)](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)

Uma mudança de segurança foi transmitida em direto a 26 de julho que altera a forma como são emitidas fichas apenas de aplicações (através da concessão de credenciais de cliente). Anteriormente, as aplicações eram autorizadas a obter fichas para ligar para qualquer outra app, independentemente da presença no inquilino ou funções consentidos para essa aplicação.  Este comportamento foi atualizado de modo que para os recursos (por vezes chamados APIs Web) definidos como um único inquilino (o padrão), a aplicação do cliente deve existir dentro do inquilino de recursos.  Note que o consentimento existente entre o cliente e a API ainda não é necessário, e as aplicações devem ainda estar a fazer os seus próprios controlos de autorização para garantir que uma `roles` reclamação está presente e contém o valor esperado para a API.

A mensagem de erro para este cenário indica atualmente: 

`The service principal named <appName> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.`

Para resolver este problema, utilize a experiência Admin Consent para criar o principal do serviço de aplicação do cliente no seu inquilino, ou criá-lo manualmente.  Este requisito garante que o inquilino autorizou o pedido de funcionamento dentro do arrendatário.  

#### <a name="example-request"></a>Pedido de exemplo

`https://login.microsoftonline.com/contoso.com/oauth2/authorize?resource=https://gateway.contoso.com/api&response_type=token&client_id=14c88eee-b3e2-4bb0-9233-f5e3053b3a28&...` Neste exemplo, o inquilino de recursos (autoridade) é contoso.com, a app de recursos é uma aplicação de um único inquilino chamada `gateway.contoso.com/api` para o inquilino Contoso, e a aplicação de clientes é `14c88eee-b3e2-4bb0-9233-f5e3053b3a28`.  Se a aplicação do cliente tiver um diretor de serviço dentro Contoso.com, este pedido pode continuar.  Se não o fizer, no entanto, o pedido falhará com o erro acima.  

Se a aplicação Contoso gateway fosse uma aplicação multi-inquilino, no entanto, o pedido continuaria independentemente da aplicação do cliente ter um diretor de serviço dentro de Contoso.com.  

### <a name="redirect-uris-can-now-contain-query-string-parameters"></a>Redirecionamento de URIs pode agora conter parâmetros de cadeia de consulta

**Data de funcionação**: 22 de julho de 2019

**Pontos finais impactados**: V1.0 e v2.0

**Protocolo impactado**: Todos os fluxos

Por [RFC 6749,](https://tools.ietf.org/html/rfc6749#section-3.1.2)as aplicações Azure AD podem agora registar e utilizar URIs redirecionais (resposta) com parâmetros de consulta estática (como https://contoso.com/oauth2?idp=microsoft) pedidos de OAuth 2.0.  As URIs de redirecionamento dinâmico continuam proibidas, uma vez que representam um risco de segurança, o que não pode ser utilizado para reter informações estatais através de um pedido de autenticação - para isso, utilizar o parâmetro `state`.

O parâmetro de consulta estática está sujeito a correspondência de cordas para URIs redirecionados como qualquer outra parte do URI redirecionado - se não for registada nenhuma cadeia que corresponda ao redirect_uri descodificado pela URI, então o pedido será rejeitado.  Se o URI for encontrado no registo da aplicação, então toda a cadeia será usada para redirecionar o utilizador, incluindo o parâmetro de consulta estática. 

Note que nesta altura (final de julho de 2019), o registo de aplicações UX no portal Azure ainda bloqueia parâmetros de consulta.  No entanto, pode editar manualmente o manifesto da aplicação para adicionar parâmetros de consulta e testá-lo na sua aplicação.  


## <a name="march-2019"></a>Março de 2019

### <a name="looping-clients-will-be-interrupted"></a>Clientes em loop serão interrompidos

**Data de funcionação**: 25 de março de 2019

**Pontos finais impactados**: V1.0 e v2.0

**Protocolo impactado**: Todos os fluxos

As aplicações dos clientes podem por vezes portar-se mal, emitindo centenas do mesmo pedido de login durante um curto período de tempo.  Estes pedidos podem ou não ser bem sucedidos, mas todos contribuem para a má experiência do utilizador e para o aumento da carga horária para o IDP, aumentando a latência para todos os utilizadores e reduzindo a disponibilidade do IDP.  Estas aplicações estão a funcionar fora dos limites da utilização normal e devem ser atualizadas para se comportarem corretamente.  

Os clientes que emitirem pedidos duplicados várias vezes serão enviados um erro `invalid_grant`: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request`. 

A maioria dos clientes não precisará de mudar de comportamento para evitar este erro.  Apenas os clientes mal configurados (aqueles sem cache de fichaou ou aqueles que já exibem loops rápidos) serão impactados por este erro.  Os clientes são rastreados por exemplo localmente (via cookie) nos seguintes fatores:

* Dica do utilizador, se houver

* Âmbitos ou recursos solicitados

* ID de Cliente

* URI de Redirecionamento

* Tipo e modo de resposta

As aplicações que fizerem vários pedidos (15+) num curto espaço de tempo (5 minutos) receberão um erro `invalid_grant` explicando que estão em loop.  Os símbolos que estão a ser solicitados têm vida útil suficiente (10 minutos mínimos, 60 minutos por defeito), pelo que os pedidos repetidos durante este período são desnecessários.  

Todas as aplicações devem lidar com `invalid_grant` mostrando um pedido interativo, em vez de pedir silenciosamente um símbolo.  Para evitar este erro, os clientes devem certificar-se de que estão a cortar corretamente as fichas que recebem.


## <a name="october-2018"></a>Outubro de 2018

### <a name="authorization-codes-can-no-longer-be-reused"></a>Os códigos de autorização já não podem ser reutilizados

**Data de funcionação**: 15 de novembro de 2018

**Pontos finais impactados**: V1.0 e v2.0

**Protocolo impactado**: [Fluxo de código](v2-oauth2-auth-code-flow.md)

A partir de 15 de Novembro de 2018, do Azure AD irá parar abertos ao recebimento de códigos de autenticação utilizados anteriormente para aplicações. Esta alteração de segurança ajuda a trazer do Azure AD em conformidade com a especificação de OAuth e será imposta em pontos finais de ambos o v1 e v2.

Se a sua aplicação reutiliza os códigos de autorização para obter os tokens de vários recursos, recomendamos que utilize o código para obter um token de atualização e, em seguida, utilizar esse token de atualização para adquirir tokens adicionais para outros recursos. Códigos de autorização só podem ser utilizados uma vez, mas os tokens de atualização podem ser utilizadas várias vezes em vários recursos. Qualquer nova aplicação que tente reutilizar um código de autenticação durante o fluxo de código OAuth terá um erro invalid_grant.

Para mais informações sobre fichas de atualização, consulte [Refreshing as fichas](v1-protocols-oauth-code.md#refreshing-the-access-tokens)de acesso .  Se utilizar a ADAL ou a MSAL, esta é tratada pela biblioteca - substitua a segunda instância de 'AcquireTokenByAuthorizationCodeAsync' por 'AcquireTokenSilentAsync'. 

## <a name="may-2018"></a>Maio de 2018

### <a name="id-tokens-cannot-be-used-for-the-obo-flow"></a>Fichas de identificação não podem ser usadas para o fluxo obo

**Data**: 1 de maio de 2018

**Pontos finais impactados**: V1.0 e v2.0

**Protocolos impactados**: Fluxo implícito e [fluxo de OBO](v1-oauth2-on-behalf-of-flow.md)

Depois de 1 de maio de 2018, id_tokens não pode ser usado como afirmação num fluxo obo para novas aplicações. Em vez disso, devem ser utilizados fichas de acesso para garantir APIs, mesmo entre um cliente e um nível médio da mesma aplicação. As aplicações registadas antes de 1 de maio de 2018 continuarão a funcionar e poderão trocar id_tokens por um sinal de acesso; no entanto, este padrão não é considerado uma boa prática.

Para contornar esta mudança, pode fazer o seguinte:

1. Crie uma API Web para a sua aplicação, com um ou mais âmbitos. Este ponto de entrada explícito permitirá um controlo e segurança mais finos.
1. No manifesto da sua aplicação, no [portal Azure](https://portal.azure.com) ou no portal de registo de [aplicações,](https://apps.dev.microsoft.com)certifique-se de que a aplicação está autorizada a emitir fichas de acesso através do fluxo implícito. Isto é controlado através da chave `oauth2AllowImplicitFlow`.
1. Quando a sua aplicação de cliente solicita um id_token via `response_type=id_token`, também solicite um sinal de acesso (`response_type=token`) para a Web API acima criada. Assim, ao utilizar o ponto final v2.0, o parâmetro `scope` deve ser semelhante ao `api://GUID/SCOPE`. No ponto final v1.0, o parâmetro `resource` deve ser a aplicação URI da Web API.
1. Passe este sinal de acesso para o nível médio no lugar do id_token.  

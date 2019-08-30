---
title: Referência de alterações recentes de Azure Active Directory | Microsoft Docs
description: Saiba mais sobre as alterações feitas nos protocolos do AD do Azure que podem afetar seu aplicativo.
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6dd50aa00368469a9c5b42c41826da28566268d4
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70125423"
---
# <a name="whats-new-for-authentication"></a>O que há de novo para autenticação? 

>Seja notificado sobre atualizações desta página. Basta adicionar [essa URL](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20for%20authentication%22&locale=en-us) ao leitor de RSS feed.

O sistema de autenticação altera e adiciona recursos de forma contínua para melhorar a conformidade com a segurança e os padrões. Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece informações sobre os seguintes detalhes:

- Funcionalidades mais recentes
- Problemas conhecidos
- Alterações de protocolo
- Funcionalidades preteridas

> [!TIP] 
> Esta página é atualizada regularmente, portanto, acesse com frequência. Salvo indicação em contrário, essas alterações só são colocadas em vigor para aplicativos recentemente registrados.  

## <a name="upcoming-changes"></a>Alterações futuras

Setembro de 2019: Imposição adicional de semântica de POST de acordo com as regras de análise de URL-parâmetros duplicados dispararão um erro e uma [bom](https://www.w3.org/International/questions/qa-byte-order-mark) ignorada.

## <a name="august-2019"></a>Agosto de 2019

### <a name="post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored"></a>A semântica do formulário de POSTAgem será imposta mais estritamente espaços e aspas será ignorada

**Data**de efetivação: 2 de setembro de 2019

**Pontos de extremidade afetados**: V 1.0 e v 2.0

**Protocolo afetado**: Em qualquer lugar, a POSTAgem é usada ([credenciais do cliente](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow), [resgate de código de autorização](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow), [ROPC](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth-ropc), [obo](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)e resgate de [token de atualização](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow#refresh-the-access-token))

A partir da semana de 9/2, as solicitações de autenticação que usam o método POST serão validadas usando padrões de HTTP mais estritos.  Especificamente, espaços e aspas duplas (") não serão mais removidos dos valores do formulário de solicitação. Não se espera que essas alterações interrompam os clientes existentes e garantirão que as solicitações enviadas ao Azure AD sejam manipuladas de forma confiável a cada vez. No futuro (veja acima), planejamos rejeitar ainda mais parâmetros duplicados e ignorar a BOM em solicitações. 

Exemplo:

Hoje, `?e=    "f"&g=h` `?e=f&g=h` o é analisado de forma `e`  ==  `f`idêntica.  Com essa alteração, agora ela seria analisada para que `e`  ==  `    "f"` , provavelmente, isso não seja um argumento válido, e a solicitação agora falharia. 


## <a name="july-2019"></a>Julho de 2019

### <a name="app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant"></a>Tokens somente de aplicativo para aplicativos de locatário único só serão emitidos se o aplicativo cliente existir no locatário de recursos

**Data**de efetivação: 26 de julho de 2019

**Pontos de extremidade afetados**: [V 1.0](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow) e [v 2.0](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow)

**Protocolo afetado**: [Credenciais do cliente (tokens somente de aplicativo)](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)

Uma alteração de segurança entrou em 26 de julho que altera a maneira como os tokens somente de aplicativo (por meio da concessão de credenciais de cliente) são emitidos. Anteriormente, os aplicativos eram autorizados a obter tokens para chamar qualquer outro aplicativo, independentemente da presença no locatário ou das funções consentidas para esse aplicativo.  Esse comportamento foi atualizado para que, para recursos (às vezes chamados de APIs da Web) definido como um único locatário (o padrão), o aplicativo cliente deve existir dentro do locatário do recurso.  Observe que o consentimento existente entre o cliente e a API ainda não é necessário, e os aplicativos ainda devem estar fazendo suas próprias verificações de autorização para `roles` garantir que uma declaração esteja presente e contenha o valor esperado para a API.

A mensagem de erro para este cenário declara atualmente: 

`The service principal named <appName> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.`

Para corrigir esse problema, use a experiência de consentimento do administrador para criar a entidade de serviço do aplicativo cliente em seu locatário ou crie-a manualmente.  Esse requisito garante que o locatário tenha concedido a permissão do aplicativo para operar dentro do locatário.  

#### <a name="example-request"></a>Pedido de exemplo

`https://login.microsoftonline.com/contoso.com/oauth2/authorize?resource=https://gateway.contoso.com/api&response_type=token&client_id=14c88eee-b3e2-4bb0-9233-f5e3053b3a28&...`Neste exemplo, o locatário (autoridade) do recurso é contoso.com, o aplicativo de recurso é um aplicativo de locatário único `gateway.contoso.com/api` chamado para o locatário da Contoso e o aplicativo cliente `14c88eee-b3e2-4bb0-9233-f5e3053b3a28`é.  Se o aplicativo cliente tiver uma entidade de serviço dentro de Contoso.com, essa solicitação poderá continuar.  No entanto, se isso não acontecer, a solicitação falhará com o erro acima.  

No entanto, se o aplicativo de gateway da Contoso fosse de vários locatários, a solicitação continuaria independentemente do aplicativo cliente ter uma entidade de serviço em Contoso.com.  

### <a name="redirect-uris-can-now-contain-query-string-parameters"></a>URIs de redirecionamento agora podem conter parâmetros de cadeia de caracteres de consulta

**Data**de efetivação: 22 de julho de 2019

**Pontos de extremidade afetados**: V 1.0 e v 2.0

**Protocolo afetado**: Todos os fluxos

Por [RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2), os aplicativos do Azure ad agora podem registrar e usar URIs de redirecionamento (resposta) com parâmetros https://contoso.com/oauth2?idp=microsoft) de consulta estáticos (como para solicitações do OAuth 2,0.  URIs de redirecionamento dinâmico ainda são proibidos, pois representam um risco de segurança, e isso não pode ser usado para reter informações de estado em uma solicitação `state` de autenticação-para isso, use o parâmetro.

O parâmetro de consulta estática está sujeito à correspondência de cadeia de caracteres para URIs de redirecionamento como qualquer outra parte do URI de redirecionamento-se nenhuma cadeia de caracteres estiver registrada que corresponda ao redirect_uri decodificado por URI, a solicitação será rejeitada.  Se o URI for encontrado no registro do aplicativo, a cadeia de caracteres inteira será usada para redirecionar o usuário, incluindo o parâmetro de consulta estática. 

Observe que, neste momento (fim de julho de 2019), o UX de registro de aplicativo no portal do Azure ainda bloqueia parâmetros de consulta.  No entanto, você pode editar o manifesto do aplicativo manualmente para adicionar parâmetros de consulta e testá-lo em seu aplicativo.  


## <a name="march-2019"></a>Março de 2019

### <a name="looping-clients-will-be-interrupted"></a>Os clientes de loop serão interrompidos

**Data**de efetivação: 25 de março de 2019

**Pontos de extremidade afetados**: V 1.0 e v 2.0

**Protocolo afetado**: Todos os fluxos

Às vezes, os aplicativos cliente podem se comportar, emitindo centenas da mesma solicitação de logon em um curto período de tempo.  Essas solicitações podem ou não ser bem-sucedidas, mas todas contribuem para a má experiência do usuário e cargas de trabalho aumentadas para o IDP, aumentando a latência para todos os usuários e reduzindo a disponibilidade do IDP.  Esses aplicativos estão operando fora dos limites de uso normal e devem ser atualizados para se comportarem corretamente.  

Os clientes que emitirem solicitações duplicadas várias vezes receberão `invalid_grant` um `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request`erro:. 

A maioria dos clientes não precisará alterar o comportamento para evitar esse erro.  Somente clientes mal configurados (aqueles sem cache de token ou aqueles que exibem loops de prompt já) serão afetados por esse erro.  Os clientes são acompanhados em uma base por instância localmente (via cookie) nos seguintes fatores:

* Dica de usuário, se houver

* Escopos ou recursos sendo solicitados

* ID de Cliente

* URI de Redirecionamento

* Tipo e modo de resposta

Os aplicativos que fazem várias solicitações (15 +) em um curto período de tempo (5 minutos) receberão um `invalid_grant` erro explicando que eles estão em loop.  Os tokens que estão sendo solicitados têm tempos de vida de vida longa suficiente (10 minutos no mínimo, 60 minutos por padrão), portanto, solicitações repetidas nesse período de tempo são desnecessárias.  

Todos os aplicativos devem `invalid_grant` lidar com a exibição de um prompt interativo, em vez de solicitar silenciosamente um token.  Para evitar esse erro, os clientes devem garantir que eles estejam armazenando em cache corretamente os tokens recebidos.


## <a name="october-2018"></a>Outubro de 2018

### <a name="authorization-codes-can-no-longer-be-reused"></a>Os códigos de autorização não podem mais ser reutilizados

**Data**de efetivação: 15 de novembro de 2018

**Pontos de extremidade afetados**: V 1.0 e v 2.0

**Protocolo afetado**: [Fluxo de código](v2-oauth2-auth-code-flow.md)

A partir de 15 de Novembro de 2018, do Azure AD irá parar abertos ao recebimento de códigos de autenticação utilizados anteriormente para aplicações. Esta alteração de segurança ajuda a trazer do Azure AD em conformidade com a especificação de OAuth e será imposta em pontos finais de ambos o v1 e v2.

Se a sua aplicação reutiliza os códigos de autorização para obter os tokens de vários recursos, recomendamos que utilize o código para obter um token de atualização e, em seguida, utilizar esse token de atualização para adquirir tokens adicionais para outros recursos. Códigos de autorização só podem ser utilizados uma vez, mas os tokens de atualização podem ser utilizadas várias vezes em vários recursos. Qualquer novo aplicativo que tentar reutilizar um código de autenticação durante o fluxo de código OAuth receberá um erro invalid_grant.

Para obter mais informações sobre tokens de atualização, consulte [atualizando os tokens de acesso](v1-protocols-oauth-code.md#refreshing-the-access-tokens).  Se estiver usando a ADAL ou MSAL, isso será tratado para você pela biblioteca – substitua a segunda instância de "AcquireTokenByAuthorizationCodeAsync" por "AcquireTokenSilentAsync". 

## <a name="may-2018"></a>Maio de 2018

### <a name="id-tokens-cannot-be-used-for-the-obo-flow"></a>Tokens de ID não podem ser usados para o fluxo OBO

**Data**: 1 de maio de 2018

**Pontos de extremidade afetados**: V 1.0 e v 2.0

**Protocolos afetados**: Fluxo implícito e [fluxo obo](v1-oauth2-on-behalf-of-flow.md)

Após 1º de maio de 2018, id_tokens não pode ser usado como a asserção em um fluxo OBO para novos aplicativos. Tokens de acesso devem ser usados para proteger APIs, mesmo entre um cliente e uma camada intermediária do mesmo aplicativo. Os aplicativos registrados antes de 1º de maio de 2018 continuarão a funcionar e poderão trocar id_tokens por um token de acesso; no entanto, esse padrão não é considerado uma prática recomendada.

Para solucionar essa alteração, você pode fazer o seguinte:

1. Crie uma API da Web para seu aplicativo, com um ou mais escopos. Esse ponto de entrada explícito permitirá controle e segurança mais refinados.
1. No manifesto do aplicativo, no [portal do Azure](https://portal.azure.com) ou no portal de [registro de aplicativo](https://apps.dev.microsoft.com), verifique se o aplicativo tem permissão para emitir tokens de acesso por meio do fluxo implícito. Isso é controlado por meio `oauth2AllowImplicitFlow` da chave.
1. Quando o aplicativo cliente solicita um id_token via `response_type=id_token`, solicite também um token de`response_type=token`acesso () para a API Web criada acima. Portanto, ao usar o ponto de extremidade v `scope` 2.0, o parâmetro deve `api://GUID/SCOPE`ser semelhante a. No ponto de extremidade v 1.0, `resource` o parâmetro deve ser o URI do aplicativo da API Web.
1. Passe esse token de acesso para a camada intermediária no lugar do id_token.  

---
title: Como utilizar a Avaliação de Acesso Contínuo viabilizou APIs nas suas aplicações Rio Azure
titleSuffix: Microsoft identity platform
description: Como aumentar a segurança e a resiliência das aplicações adicionando suporte para avaliação contínua de acesso, permitindo o acesso de longa duração a tokens que podem ser revogados com base em eventos críticos e avaliação de políticas.
services: active-directory
author: knicholasa
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/06/2020
ms.author: nichola
ms.reviewer: ''
ms.openlocfilehash: 86c379316737b7718b62165a6feb93ca3a0e9954
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96484044"
---
# <a name="how-to-use-continuous-access-evaluation-enabled-apis-in-your-applications"></a>Como utilizar a Avaliação contínua de Acessos permitiu APIs nas suas aplicações

[A Avaliação contínua de Acesso](../conditional-access/concept-continuous-access-evaluation.md) (CAE) é uma norma emergente da indústria que permite revogar os tokens de acesso com base em [eventos críticos](../conditional-access/concept-continuous-access-evaluation.md#critical-event-evaluation) e [avaliação de políticas](../conditional-access/concept-continuous-access-evaluation.md#conditional-access-policy-evaluation-preview) em vez de depender da expiração simbólica com base na vida útil. Para algumas APIs de recursos, porque o risco e a política são avaliados em tempo real, isso pode aumentar a vida útil simbólica até 28 horas. Estes tokens de longa duração serão proativamente atualizados pela Microsoft Authentication Library (MSAL), aumentando a resiliência das suas aplicações.

Este artigo mostra-lhe como utilizar APIs ativados por CAE nas suas aplicações.

## <a name="implementation-considerations"></a>Considerações de implementação

Para utilizar a Avaliação de Acesso Contínuo, tanto a sua aplicação como a API de recursos a que está a aceder devem estar ativadas pelo CAE. No entanto, preparar o seu código para utilizar um recurso ativado por CAE não o impedirá de utilizar APIs que não estejam ativados por CAE.

Se uma API de recurso implementar o CAE e a sua aplicação declarar que pode lidar com CAE, a sua aplicação receberá fichas CAE para esse recurso. Por esta razão, se declarar a sua app CAE pronta, a sua aplicação deve lidar com o desafio de reivindicação cae para todas as APIs de recursos que aceitem tokens de acesso da Microsoft Identity. Se não lidar com as respostas cae nestas chamadas API, a sua aplicação pode acabar num ciclo redando uma chamada da API com um token que ainda está no tempo de vida devolvido do token, mas que foi revogado devido ao CAE.

## <a name="the-code"></a>O código

O primeiro passo é adicionar código para lidar com uma resposta da API de recurso rejeitando a chamada devido à CAE. Com o CAE, as APIs devolverão um estado 401 e um cabeçalho WWW-Authenticate quando o token de acesso tiver sido revogado ou a API detetar uma alteração no endereço IP utilizado. O cabeçalho WWW-Authenticate contém um Desafio de Reclamações que a aplicação pode usar para adquirir um novo token de acesso.

Por exemplo:

```console
HTTP 401; Unauthorized
WWW-Authenticate=Bearer
 authorization_uri="https://login.windows.net/common/oauth2/authorize",
 error="insufficient_claims",
 claims="eyJhY2Nlc3NfdG9rZW4iOnsibmJmIjp7ImVzc2VudGlhbCI6dHJ1ZSwgInZhbHVlIjoiMTYwNDEwNjY1MSJ9fX0="
```

A sua aplicação verificaria:

- a chamada da API devolvendo o estado 401
- Existência de um cabeçalho WWW-Authenticate que contenha:
  - um parâmetro de "erro" com o valor "insufficient_claims"
  - um parâmetro de "reivindicações"

Quando estas condições são satisfeitas, a aplicação pode extrair e descodificar o desafio de reclamações.

```csharp
if (APIresponse.IsSuccessStatusCode)
{
    // ...
}
else
{
    if (APIresponse.StatusCode == System.Net.HttpStatusCode.Unauthorized
        && APIresponse.Headers.WwwAuthenticate.Any())
    {
        AuthenticationHeaderValue bearer = APIresponse.Headers.WwwAuthenticate.First
            (v => v.Scheme == "Bearer");
        IEnumerable<string> parameters = bearer.Parameter.Split(',').Select(v => v.Trim()).ToList();
        var error = GetParameter(parameters, "error");

        if (null != error && "insufficient_claims" == error)
        {
            var claimChallengeParameter = GetParameter(parameters, "claims");
            if (null != claimChallengeParameter)
            {
                var claimChallengebase64Bytes = System.Convert.FromBase64String(claimChallengeParameter);
                var claimChallenge = System.Text.Encoding.UTF8.GetString(claimChallengebase64Bytes);
                var newAccessToken = await GetAccessTokenWithClaimChallenge(scopes, claimChallenge);
```

A sua aplicação usaria então o desafio de sinistros para adquirir um novo token de acesso para o recurso.

```csharp
try
{
    authResult = await _clientApp.AcquireTokenSilent(scopes, firstAccount)
        .WithClaims(claimChallenge)
        .ExecuteAsync()
        .ConfigureAwait(false);
}
catch (MsalUiRequiredException)
{
    try
    {
        authResult = await _clientApp.AcquireTokenInteractive(scopes)
            .WithClaims(claimChallenge)
            .WithAccount(firstAccount)
            .ExecuteAsync()
            .ConfigureAwait(false);
    }
    // ...
```

Uma vez que a sua aplicação esteja pronta para lidar com o desafio de reclamação devolvido por um recurso ativado pela CAE, pode dizer ao Microsoft Identity que a sua aplicação está pronta para CAE. Para isso na sua aplicação MSAL, construa o seu Cliente Público utilizando as Capacidades do Cliente de "cp1".

```csharp
_clientApp = PublicClientApplicationBuilder.Create(App.ClientId)
    .WithDefaultRedirectUri()
    .WithAuthority(authority)
    .WithClientCapabilities(new [] {"cp1"})
    .Build();
```

Pode testar a sua aplicação assinando um utilizador para a aplicação e, em seguida, utilizar o portal Azure para revogar as sessões do utilizador. Da próxima vez que a aplicação ligar para a API ativada pela CAE, o utilizador será solicitado a reauthenticar.

## <a name="next-steps"></a>Passos seguintes

Para saber mais, consulte [a avaliação de acesso contínuo.](../conditional-access/concept-continuous-access-evaluation.md)

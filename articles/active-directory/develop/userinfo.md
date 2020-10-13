---
title: Plataforma de identidade da Microsoft UserInfo ponto final Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre o ponto final do UserInfo na plataforma de identidade da Microsoft.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 09/21/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 8f3fd462a52b035cd5b5447560e5472b41f237fa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91653235"
---
# <a name="microsoft-identity-platform-userinfo-endpoint"></a>Ponto final da plataforma de identidade da Microsoft UserInfo

O ponto final do UserInfo faz parte da [norma OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html#UserInfo) (OIDC), concebida para devolver reclamações sobre o utilizador que autenticou.  Para a plataforma de identidade da Microsoft, o ponto final do UserInfo está hospedado no Microsoft Graph ( https://graph.microsoft.com/oidc/userinfo) . 

## <a name="find-the-well-known-configuration-endpoint"></a>Encontre o ponto final de configuração .bem conhecido

Pode descobrir programáticamente o ponto final do UserInfo utilizando o documento de descoberta OpenID Connect, em `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration` . Está listado no `userinfo_endpoint` campo, e este padrão pode ser usado através das nuvens para ajudar a apontar para o ponto final certo.  Não recomendamos a codificação dura do ponto final do UserInfo na sua aplicação – utilize o documento de descoberta OIDC para encontrar este ponto final em tempo de execução.

Como parte da especificação OpenID Connect, o ponto final do UserInfo é frequentemente chamado automaticamente por [bibliotecas compatíveis com o OIDC](https://openid.net/developers/certified/)  para obter informações sobre o utilizador.  Sem hospedar tal ponto final, a plataforma de identidade da Microsoft não seria compatível com padrões e algumas bibliotecas falhariam.  Da [lista de reclamações identificadas na norma OIDC](https://openid.net/specs/openid-connect-core-1_0.html#StandardClaims) produzimos as reclamações de nome, reclamação de assunto e e-mail quando disponíveis e consentidas.  

## <a name="consider-use-an-id-token-instead"></a>Considere: Use um token de ID em vez

A informação disponível no token de ID que a sua aplicação pode receber é um superconjunto da informação que pode obter do ponto final do UserInfo.  Como pode obter um token de ID ao mesmo tempo que obtém um token para ligar para o ponto final do UserInfo, sugerimos que utilize esse token de ID para obter informações sobre o utilizador em vez de ligar para o ponto final do UserInfo.  A utilização do token de ID eliminará um a dois pedidos de rede do lançamento da sua aplicação, reduzindo a latência na sua aplicação.

Se necessitar de mais detalhes sobre o utilizador, deverá ligar para a [ `/user` API](/graph/api/user-get) do Microsoft Graph para obter informações como o número de escritório ou o título de emprego.   Também pode utilizar [pedidos opcionais](active-directory-optional-claims.md) para incluir informações adicionais do utilizador no seu ID e fichas de acesso.

## <a name="calling-the-userinfo-endpoint"></a>Chamando o ponto final do UserInfo

UserInfo é uma API de token padrão do Portador de OAuth, chamada como qualquer outra API do Microsoft Graph usando o token de acesso recebido quando recebe um token para o Microsoft Graph. Devolve uma resposta JSON contendo alegações sobre o utilizador.

### <a name="permissions"></a>Permissões

Utilize as [seguintes permissões OIDC](v2-permissions-and-consent.md#openid-connect-scopes) para ligar para a API Do UtilizadorInfo. `openid` é necessário, e os `profile` `email` e os âmbitos asseguram que informações adicionais são fornecidas na resposta.

|Tipo de permissão      | Permissões    |
|:--------------------|:---------------------------------------------------------|
|Delegado (trabalho ou conta escolar) | openid (obrigatório), perfil, e-mail |
|Delegado (conta pessoal da Microsoft) | openid (obrigatório), perfil, e-mail |
|Aplicação | Não aplicável |

> [!TIP]
> Copie este URL no seu browser para obter um token para o ponto final do UserInfo, bem como um [token de ID](id-tokens.md) e substitua o ID do cliente e redirecione o URI pelo seu próprio. Note que apenas solicita miras para âmbitos OpenID ou Graph, e nada mais.  Isto é necessário, uma vez que não pode solicitar permissões para dois recursos diferentes no mesmo pedido simbólico.
>
> `https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=<yourClientID>&response_type=token+id_token&redirect_uri=<YourRedirectUri>&scope=user.read+openid+profile+email&response_mode=fragment&state=12345&nonce=678910`
>
> Pode utilizar este token de acesso na secção seguinte.

Como qualquer outro token Microsoft Graph, o token que recebe aqui pode não ser um JWT. Se assinou numa conta da Microsoft, será um formato de token encriptado. Isto porque o Microsoft Graph tem um padrão especial de emissão de fichas. Isto não afeta a sua capacidade de usar o token de acesso para ligar para o ponto final do UserInfo.

### <a name="calling-the-api"></a>Chamando a API

A API do UserInfo suporta tanto o GET como o POST, de acordo com a especificação OIDC.

```http
GET or POST /oidc/userinfo HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJub25jZSI6Il…
```

### <a name="userinfo-response"></a>Resposta UserInfo

```jsonc
{
    "sub": "OLu859SGc2Sr9ZsqbkG-QbeLgJlb41KcdiPoLYNpSFA",
    "name": "Mikah Ollenburg", // names all require the “profile” scope.
    "family_name": " Ollenburg",
    "given_name": "Mikah",
    "email": "mikoll@contoso.com" //requires the “email” scope.
}
```

As reclamações aqui listadas são todas as alegações que o ponto final do UserInfo pode devolver.  Estes são os mesmos valores que a app veria no [token de ID](id-tokens.md) emitido para a app.  

## <a name="notes-and-caveats-on-the-userinfo-endpoint"></a>Notas e ressalvas no ponto final do UserInfo

* Se quiser chamar este ponto final Do UtilizadorInfo, tem de utilizar o ponto de terminação v2.0.  Se utilizar o ponto final v1.0, receberá um token para o ponto final do UserInfo v1.0, alojado no login.microsoftonline.com.  Recomendamos que todas as aplicações e bibliotecas compatíveis com o OIDC utilizem o ponto final v2.0 para garantir a compatibilidade.
* A resposta do ponto final do UserInfo não pode ser personalizada.  Se quiser personalizar reclamações, por favor use [mapeamento de reclamações]( active-directory-claims-mapping.md) para editar as informações devolvidas nos tokens.
* A resposta do ponto final do UserInfo não pode ser adicionada.  Se quiser obter reclamações adicionais sobre o utilizador, por favor use [pedidos opcionais]( active-directory-optional-claims.md) para adicionar novas reclamações aos tokens.

## <a name="next-steps"></a>Passos Seguintes

* [Reveja o conteúdo dos tokens de ID](id-tokens.md)
* [Personalize o conteúdo de um token de ID usando pedidos opcionais](active-directory-optional-claims.md)
* [Solicite um token de acesso e ficha de ID usando o protocolo OAuth2](v2-protocols-oidc.md)

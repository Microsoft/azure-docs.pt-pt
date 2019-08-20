---
title: Configurar a inscrição e a entrada com o OpenID Connect-Azure Active Directory B2C | Microsoft Docs
description: Configure a inscrição e a entrada com o OpenID Connect usando Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: fe49a57e74822c0b4349b2919ea8aa89cbfb458d
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69622300"
---
# <a name="set-up-sign-up-and-sign-in-with-openid-connect-using-azure-active-directory-b2c"></a>Configurar a inscrição e a entrada com o OpenID Connect usando o Azure Active Directory B2C

O [OpenID Connect](active-directory-b2c-reference-oidc.md) é um protocolo de autenticação criado com base no OAuth 2,0 que pode ser usado para a entrada segura do usuário. A maioria dos provedores de identidade que usam esse protocolo tem suporte no Azure AD B2C. Este artigo explica como você pode adicionar provedores de identidade do OpenID Connect personalizados em seus fluxos de usuário.

## <a name="add-the-identity-provider"></a>Adicionar o provedor de identidade

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador global do inquilino do Azure AD B2C.
1. Verifique se você está usando o diretório que contém seu locatário de Azure AD B2C clicando no filtro **diretório + assinatura** no menu superior e escolhendo o diretório que contém seu locatário.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **provedores de identidade**e, em seguida, selecione **novo provedor do OpenID Connect**.

## <a name="configure-the-identity-provider"></a>Configurar o provedor de identidade

Cada provedor de identidade do OpenID Connect descreve um documento de metadados que contém a maioria das informações necessárias para executar a entrada. Isso inclui informações como as URLs a serem usadas e o local das chaves de assinatura públicas do serviço. O documento de metadados do OpenID Connect sempre está localizado em um ponto de `.well-known\openid-configuration`extremidade que termina em. Para o provedor de identidade do OpenID Connect que você está procurando adicionar, insira sua URL de metadados.

## <a name="client-id-and-secret"></a>ID do cliente e segredo

Para permitir que os usuários entrem, o provedor de identidade exige que os desenvolvedores registrem um aplicativo em seu serviço. Esse aplicativo tem uma ID que é conhecida como a **ID do cliente** e um **segredo do cliente**. Copie esses valores do provedor de identidade e insira-os nos campos correspondentes.

> [!NOTE]
> O segredo do cliente é opcional. No entanto, você deve inserir um segredo do cliente se desejar usar o [fluxo do código de autorização](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth), que usa o segredo para trocar o código do token.

## <a name="scope"></a>Scope

Escopo define as informações e as permissões que você pretende coletar do seu provedor de identidade personalizado. As solicitações do OpenID Connect devem `openid` conter o valor do escopo para receber o token de ID do provedor de identidade. Sem o token de ID, os usuários não conseguem entrar no Azure AD B2C usando o provedor de identidade personalizado. Outros escopos podem ser anexados separados por espaço. Consulte a documentação do provedor de identidade personalizado para ver quais outros escopos podem estar disponíveis.

## <a name="response-type"></a>Tipo de resposta

O tipo de resposta descreve que tipo de informação é enviada de volta na chamada inicial para `authorization_endpoint` o do provedor de identidade personalizado. Os seguintes tipos de resposta podem ser usados:

* `code`: De acordo com o [fluxo do código de autorização](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth), um código será retornado para Azure ad B2C. Azure ad B2C prossegue para chamar o `token_endpoint` para trocar o código do token.
* `token`: Um token de acesso é retornado de volta para Azure AD B2C do provedor de identidade personalizado.
* `id_token`: Um token de ID é retornado de volta para Azure AD B2C do provedor de identidade personalizado.

## <a name="response-mode"></a>Modo de resposta

O modo de resposta define o método que deve ser usado para enviar os dados de volta do provedor de identidade personalizado para Azure AD B2C. Os seguintes modos de resposta podem ser usados:

* `form_post`: Esse modo de resposta é recomendado para obter a melhor segurança. A resposta é transmitida por meio `POST` do método http, com o código ou token que está sendo codificado no `application/x-www-form-urlencoded` corpo usando o formato.
* `query`: O código ou token é retornado como um parâmetro de consulta.

## <a name="domain-hint"></a>Sugestão de domínio

A dica de domínio pode ser usada para pular diretamente para a página de entrada do provedor de identidade especificado, em vez de fazer com que o usuário faça uma seleção entre a lista de provedores de identidade disponíveis. Para permitir esse tipo de comportamento, insira um valor para a dica de domínio. Para saltar para o provedor de identidade personalizado, acrescente o `domain_hint=<domain hint value>` parâmetro ao final da solicitação ao chamar Azure ad B2C para entrar.

## <a name="claims-mapping"></a>Mapeamento de declarações

Depois que o provedor de identidade personalizado envia um token de ID de volta para Azure AD B2C, Azure AD B2C precisa ser capaz de mapear as declarações do token recebido para as declarações que Azure AD B2C reconhece e usa. Para cada um dos seguintes mapeamentos, consulte a documentação do provedor de identidade personalizado para entender as declarações retornadas de volta nos tokens do provedor de identidade:

* **ID de usuário**: Insira a declaração que fornece o *identificador exclusivo* para o usuário conectado.
* **Nome para exibição**: Insira a declaração que fornece o *nome de exibição* ou o *nome completo* do usuário.
* **Nome fornecido**: Insira a declaração que fornece o *nome* do usuário.
* **Sobrenome**: Insira a declaração que fornece o *último nome* do usuário.
* **Email**: Insira a declaração que fornece o *endereço de email* do usuário.

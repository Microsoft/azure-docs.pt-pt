---
title: Configurar a inscrição e a entrada com o OpenID Connect-Azure Active Directory B2C
description: Configure a inscrição e a entrada com o OpenID Connect usando Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 720deb28ce124af23035337ac88cfb1d37fc7c53
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69509693"
---
# <a name="set-up-sign-up-and-sign-in-with-openid-connect-using-azure-active-directory-b2c"></a>Configurar a inscrição e a entrada com o OpenID Connect usando o Azure Active Directory B2C

O [OpenID Connect](active-directory-b2c-reference-oidc.md) é um protocolo de autenticação criado com base no OAuth 2,0 que pode ser usado para a entrada segura do usuário. A maioria dos provedores de identidade que usam esse protocolo tem suporte no Azure AD B2C. Este artigo explica como você pode adicionar provedores de identidade do OpenID Connect personalizados em seus fluxos de usuário.

## <a name="add-the-identity-provider"></a>Adicionar o provedor de identidade

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador global do inquilino do Azure AD B2C.
2. Certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD B2C, clicando no **filtro de diretório e subscrição** no menu superior e escolher o diretório que contém o seu inquilino.
3. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
4. Selecione **provedores de identidade**e clique em **Adicionar**.
5. Para o **tipo de provedor de identidade**, selecione **OpenID Connect**.

## <a name="configure-the-identity-provider"></a>Configurar o provedor de identidade

Todos os provedores de identidade do OpenID Connect descrevem um documento de metadados que contém a maioria das informações necessárias para executar a entrada. Isso inclui informações como as URLs a serem usadas e o local das chaves de assinatura públicas do serviço. O documento de metadados do OpenID Connect sempre está localizado em um ponto de `.well-known\openid-configuration`extremidade que termina em. Para o provedor de identidade do OpenID Connect que você está procurando adicionar, insira sua URL de metadados.

Para permitir que os usuários entrem, o provedor de identidade exige que os desenvolvedores registrem um aplicativo em seu serviço. Esse aplicativo tem uma ID que é conhecida como a **ID do cliente** e um **segredo do cliente**. Copie esses valores do provedor de identidade e insira-os nos campos correspondentes.

> [!NOTE]
> O segredo do cliente é opcional. No entanto, você deve inserir um segredo do cliente se desejar usar o [fluxo do código de autorização](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth), que usa o segredo para trocar o código do token.

Escopo define as informações e as permissões que você pretende coletar do seu provedor de identidade personalizado. As solicitações do OpenID Connect devem `openid` conter o valor do escopo para receber o token de ID do provedor de identidade. Sem o token de ID, os usuários não conseguem entrar no Azure AD B2C usando o provedor de identidade personalizado. Outros escopos podem ser anexados separados por espaço. Consulte a documentação do provedor de identidade personalizado para ver quais outros escopos podem estar disponíveis.

O tipo de resposta descreve que tipo de informação é enviada de volta na chamada inicial para `authorization_endpoint` o do provedor de identidade personalizado. Os seguintes tipos de resposta podem ser usados:

- `code`: De acordo com o [fluxo do código de autorização](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth), um código será retornado para Azure ad B2C. Azure ad B2C prossegue para chamar o `token_endpoint` para trocar o código do token.
- `token`: Um token de acesso é retornado de volta para Azure AD B2C do provedor de identidade personalizado.
- `id_token`: Um token de ID é retornado de volta para Azure AD B2C do provedor de identidade personalizado.

O modo de resposta define o método que deve ser usado para enviar os dados de volta do provedor de identidade personalizado para Azure AD B2C. Os seguintes modos de resposta podem ser usados:

- `form_post`: Esse modo de resposta é recomendado para obter a melhor segurança. A resposta é transmitida por meio `POST` do método http, com o código ou token que está sendo codificado no `application/x-www-form-urlencoded` corpo usando o formato.
- `query`: O código ou token é retornado como um parâmetro de consulta.

A dica de domínio pode ser usada para pular diretamente para a página de entrada do provedor de identidade especificado, em vez de fazer com que o usuário faça uma seleção entre a lista de provedores de identidade disponíveis. Para permitir esse tipo de comportamento, insira um valor para a dica de domínio. Para saltar para o provedor de identidade personalizado, acrescente o `domain_hint=<domain hint value>` parâmetro ao final da solicitação ao chamar Azure ad B2C para entrar.

Depois que o provedor de identidade personalizado envia um token de ID de volta para Azure AD B2C, Azure AD B2C precisa ser capaz de mapear as declarações do token recebido para as declarações que Azure AD B2C reconhece e usa. Para cada um dos seguintes mapeamentos, consulte a documentação do provedor de identidade personalizado para entender as declarações retornadas de volta nos tokens do provedor de identidade:

- `User ID`: Insira a declaração que fornece o identificador exclusivo para o usuário conectado.
- `Display Name`: Insira a declaração que fornece o nome de exibição ou o nome completo do usuário.
- `Given Name`: Insira a declaração que fornece o nome do usuário.
- `Surname`: Insira a declaração que fornece o último nome do usuário.
- `Email`: Insira a declaração que fornece o endereço de email do usuário.


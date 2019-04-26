---
title: Configurar a inscrição e início de sessão com OpenID Connect - Azure Active Directory B2C | Documentos da Microsoft
description: Configure a inscrição e início de sessão com OpenID Connect com o Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: de0c866889f15f096bea26915f58cd4e268dfed3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60316328"
---
# <a name="set-up-sign-up-and-sign-in-with-openid-connect-using-azure-active-directory-b2c"></a>Configurar a inscrição e início de sessão com a ligação do OpenID com o Azure Active Directory B2C

>[!NOTE]
> Esta funcionalidade está em pré-visualização pública. Não utilize a funcionalidade em ambientes de produção.


[OpenID Connect](active-directory-b2c-reference-oidc.md) é um protocolo de autenticação, criado com base no OAuth 2.0, que pode ser utilizado para assinar com segurança os utilizadores. A maioria dos fornecedores de identidade que utilizam esse protocolo, são suportadas no Azure AD B2C. Este artigo explica como pode adicionar fornecedores de identidade OpenID Connect personalizados em seus fluxos de utilizador.

## <a name="add-the-identity-provider"></a>Adicionar o fornecedor de identidade

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador global do inquilino do Azure AD B2C.
2. Certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD B2C, clicando no **filtro de diretório e subscrição** no menu superior e escolher o diretório que contém o seu inquilino.
3. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
4. Selecione **fornecedores de identidade**e, em seguida, clique em **Add**.
5. Para o **tipo de fornecedor de identidade**, selecione **OpenID Connect (pré-visualização)**.

## <a name="configure-the-identity-provider"></a>Configurar o fornecedor de identidade

Todos os fornecedores de identidade OpenID Connect descreve um documento de metadados que contém a maior parte das informações necessárias para executar o início de sessão. Isto inclui informações como os URLs para utilizar e a localização das chaves de assinatura pública do serviço. O documento de metadados OpenID Connect sempre está localizado num ponto final que termina em `.well-known\openid-configuration`. Para o fornecedor de identidade OpenID Connect que deseja adicionar, introduza o URL de metadados.

Para permitir que os utilizadores iniciem sessão, o fornecedor de identidade exige que os desenvolvedores registar uma aplicação no seu serviço. Esta aplicação tem um ID que é conhecido como o **ID de cliente** e uma **segredo do cliente**. Copie estes valores a partir do fornecedor de identidade e insira-os nos campos correspondentes.

> [!NOTE]
> O segredo do cliente é opcional. No entanto, tem de introduzir um segredo do cliente se pretender utilizar o [fluxo de código de autorização](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth), que utiliza o segredo para trocar o código para o token.

Âmbito define as informações e as permissões que quer para recolher através do seu fornecedor de identidade personalizada. Pedidos de ligação OpenID tem de conter o `openid` analisar o valor para receber o token de ID do fornecedor de identidade. Sem o token de ID, os utilizadores não são capazes de iniciar sessão no Azure AD B2C usando o provedor de identidade personalizada. Outros âmbitos podem ser anexados separados por espaço. Consulte a documentação do fornecedor de identidade personalizado para ver o que podem ser outros âmbitos disponíveis.

O tipo de resposta descreve que tipo de informações é enviado de volta na chamada inicial para o `authorization_endpoint` do fornecedor de identidade personalizada. Podem ser utilizados os seguintes tipos de resposta:

- `code`: De acordo a [fluxo de código de autorização](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth), será devolvido um código de volta para o Azure AD B2C. O Azure AD B2C prossegue, chamando o `token_endpoint` para trocar o código para o token.
- `token`: Um token de acesso é retornado novamente ao Azure AD B2C do fornecedor de identidade personalizada.
- `id_token`: Um token de ID é retornado novamente ao Azure AD B2C do fornecedor de identidade personalizada.

O modo de resposta define o método que deve ser utilizado para enviar os dados de volta do fornecedor de identidade personalizada para o Azure AD B2C. Podem ser utilizados os seguintes modos de resposta:

- `form_post`: Para melhor segurança, recomenda-se neste modo de resposta. A resposta é transmitida via HTTP `POST` método, com o código ou um token a ser codificada no corpo com o `application/x-www-form-urlencoded` formato.
- `query`: O código ou o token é retornado como um parâmetro de consulta.

A sugestão de domínio pode ser utilizada para avançar diretamente para o início de sessão na página do fornecedor de identidade especificada, em vez de ter a marca de usuário uma seleção na lista de fornecedores de identidade disponíveis. Para permitir que esse tipo de comportamento, introduza um valor para a sugestão de domínio. Para ir para o fornecedor de identidade personalizada, acrescentar o parâmetro `domain_hint=<domain hint value>` ao final do seu pedido ao chamar o Azure AD B2C para início de sessão.

Depois de identidade personalizada fornecedor envia um token de ID para o Azure AD B2C, do Azure AD B2C tem de ser capaz de mapear as declarações do token recebido para as afirmações que o Azure AD B2C reconhece e utiliza. Para cada um dos seguintes mapeamentos, consulte a documentação do fornecedor de identidade personalizada para compreender as afirmações que são devolvidas em tokens do fornecedor de identidade:

- `User ID`: Introduza a afirmação que fornece o identificador exclusivo para o utilizador com sessão iniciada.
- `Display Name`: Introduza a afirmação que fornece o nome a apresentar ou o nome completo do utilizador.
- `Given Name`: Introduza a afirmação que fornece o nome do utilizador.
- `Surname`: Introduza a afirmação que fornece o apelido do utilizador.
- `Email`: Introduza a afirmação que fornece o endereço de e-mail do utilizador.


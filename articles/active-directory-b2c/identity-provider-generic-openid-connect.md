---
title: Configurar o início e iniciar sessão com o OpenID Connect
titleSuffix: Azure AD B2C
description: Instale o início e o início de sessão com qualquer fornecedor de identidade OpenID Connect (IdP) no Diretório Ativo Azure B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c72abf79f0a420309ebe229673be9439fd99b74c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188261"
---
# <a name="set-up-sign-up-and-sign-in-with-openid-connect-using-azure-active-directory-b2c"></a>Configurar o sign-up e iniciar sessão com o OpenID Connect utilizando o Diretório Ativo Azure B2C

[OpenID Connect](openid-connect.md) é um protocolo de autenticação construído em cima do OAuth 2.0 que pode ser usado para iniciar sessão de utilizador segura. A maioria dos fornecedores de identidade que utilizam este protocolo são suportados no Azure AD B2C. Este artigo explica como pode adicionar fornecedores de identidade OpenID Connect personalizados aos fluxos do utilizador.

## <a name="add-the-identity-provider"></a>Adicione o fornecedor de identidade

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador global do inquilino do Azure AD B2C.
1. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD B2C clicando no filtro de **subscrição Do Diretório +** + no menu superior e escolhendo o diretório que contém o seu inquilino.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **fornecedores de identidade**e, em seguida, selecione **o fornecedor New OpenID Connect**.

## <a name="configure-the-identity-provider"></a>Configure o fornecedor de identidade

Todos os fornecedores de identidade OpenID Connect descrevem um documento de metadados que contém a maioria das informações necessárias para realizar o sessão. Isto inclui informações como os URLs a utilizar e a localização das chaves de assinatura pública do serviço. O documento de metadados OpenID Connect está sempre `.well-known\openid-configuration`localizado num ponto final que termina em . Para o fornecedor de identidade OpenID Connect que procura adicionar, introduza o seu URL de metadados.

## <a name="client-id-and-secret"></a>ID do cliente e segredo

Para permitir que os utilizadores assinem, o fornecedor de identidade exige que os desenvolvedores registem uma aplicação no seu serviço. Esta aplicação tem um ID que é referido como o ID do **cliente** e um segredo do **cliente**. Copie estes valores do fornecedor de identidade e insira-os nos campos correspondentes.

> [!NOTE]
> O segredo do cliente é opcional. No entanto, deve introduzir um segredo de cliente se quiser utilizar o fluxo de código de [autorização](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth), que usa o segredo para trocar o código pelo símbolo.

## <a name="scope"></a>Âmbito

O Âmbito define as informações e permissões que pretende recolher do seu fornecedor de identidade personalizado. Os pedidos openID Connect `openid` devem conter o valor de âmbito para receber o símbolo de identificação do fornecedor de identidade. Sem o token ID, os utilizadores não podem iniciar sessão no Azure AD B2C utilizando o fornecedor de identidade personalizado. Outros âmbitos podem ser anexados separados pelo espaço. Consulte a documentação do fornecedor de identidade personalizada para ver que outros âmbitos podem estar disponíveis.

## <a name="response-type"></a>Tipo de resposta

O tipo de resposta descreve que tipo de informação é enviada na chamada inicial para o `authorization_endpoint` fornecedor de identidade personalizada. Podem ser utilizados os seguintes tipos de resposta:

* `code`: De acordo com o fluxo de código de [autorização,](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth)um código será devolvido ao Azure AD B2C. O Azure AD B2C `token_endpoint` continua a ligar para trocar o código pelo símbolo.
* `id_token`: Um token de identificação é devolvido ao Azure AD B2C do fornecedor de identidade personalizada.

## <a name="response-mode"></a>Modo de resposta

O modo de resposta define o método que deve ser usado para enviar os dados de volta do fornecedor de identidade personalizado para O Azure AD B2C. Podem ser utilizados os seguintes modos de resposta:

* `form_post`: Este modo de resposta é recomendado para uma melhor segurança. A resposta é transmitida `POST` através do método HTTP, com o código ou `application/x-www-form-urlencoded` token a ser codificado no organismo utilizando o formato.
* `query`: O código ou ficha é devolvido como parâmetro de consulta.

## <a name="domain-hint"></a>Sugestão de domínio

A dica de domínio pode ser usada para saltar diretamente para o sinal na página do fornecedor de identidade especificado, em vez de o utilizador fazer uma seleção entre a lista de fornecedores de identidade disponíveis. Para permitir este tipo de comportamento, insira um valor para a dica de domínio. Para saltar para o fornecedor de identidade `domain_hint=<domain hint value>` personalizado, acomode o parâmetro até ao final do seu pedido ao ligar para o Azure AD B2C para iniciar sessão.

## <a name="claims-mapping"></a>Mapeamento de reclamações

Depois de o fornecedor de identidade personalizado enviar um token de identidade para o Azure AD B2C, o Azure AD B2C precisa de ser capaz de mapear as reclamações do token recebido para as alegações que o Azure AD B2C reconhece e utiliza. Para cada um dos seguintes mapeamentos, consulte a documentação do fornecedor de identidade personalizada para compreender as alegações devolvidas nas fichas do fornecedor de identidade:

* **ID do utilizador**: Introduza a reclamação que fornece o *identificador único* para o utilizador inscrito.
* **Nome**do ecrã : Introduza a alegação que fornece o nome do *visor* ou *o nome completo* para o utilizador.
* **Nome :** Insira a reclamação que fornece o *primeiro nome* do utilizador.
* **Sobrenome**: Introduza a reclamação que fornece o *último nome* do utilizador.
* **Email**: Insira a reclamação que fornece o endereço de *e-mail* do utilizador.

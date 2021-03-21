---
title: Configurar inscrição e iniciar sôm-in com o OpenID Connect
titleSuffix: Azure AD B2C
description: Confiúde a inscrição e inscrição com qualquer fornecedor de identidade OpenID Connect (IdP) no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/08/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9434bd4042798dc05a33401e1884e11a73774936
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102448341"
---
# <a name="set-up-sign-up-and-sign-in-with-openid-connect-using-azure-active-directory-b2c"></a>Configurar inscrição e inscrição com OpenID Connect utilizando o Azure Ative Directory B2C

[OpenID Connect](openid-connect.md) é um protocolo de autenticação construído em cima do OAuth 2.0 que pode ser usado para iniciar sent-in seguro do utilizador. A maioria dos fornecedores de identidade que utilizam este protocolo são suportados no Azure AD B2C. Este artigo explica como pode adicionar fornecedores de identidade OpenID Connect personalizados aos fluxos do utilizador.

## <a name="add-the-identity-provider"></a>Adicione o fornecedor de identidade

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador global do inquilino do Azure AD B2C.
1. Certifique-se de que está a utilizar o diretório que contém o seu inquilino Azure AD B2C clicando no filtro **de subscrição Do Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **os fornecedores de identidade** e, em seguida, selecione novo fornecedor **OpenID Connect**.
1. Insira um **nome**. Por exemplo, *insira Contoso.*

## <a name="configure-the-identity-provider"></a>Configure o fornecedor de identidade

Cada fornecedor de identidade OpenID Connect descreve um documento de metadados que contém a maioria das informações necessárias para realizar o sismo. Isto inclui informações como os URLs a utilizar e a localização das chaves de assinatura pública do serviço. O documento de metadados OpenID Connect está sempre localizado num ponto final que termina em `.well-known/openid-configuration` . Para o fornecedor de identidade OpenID Connect que procura adicionar, introduza o URL de metadados.

## <a name="client-id-and-secret"></a>ID do cliente e segredo

Para permitir que os utilizadores se inscrevam, o fornecedor de identidade exige que os desenvolvedores registem uma aplicação no seu serviço. Esta aplicação tem um ID que é referido como o **ID** do cliente e um **segredo de cliente.** Copie estes valores do fornecedor de identidade e introduza-os nos campos correspondentes.

> [!NOTE]
> O segredo do cliente é opcional. No entanto, deve inserir um segredo de cliente se quiser utilizar o fluxo de código de [autorização](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth), que utiliza o segredo para trocar o código pelo token.

## <a name="scope"></a>Âmbito

O Scope define as informações e permissões que procura recolher do seu fornecedor de identidade personalizado. Os pedidos do OpenID Connect devem conter o valor de `openid` âmbito para receber o token de ID do fornecedor de identidade. Sem o token de ID, os utilizadores não podem entrar no Azure AD B2C usando o fornecedor de identidade personalizado. Outros âmbitos podem ser anexados separados pelo espaço. Consulte a documentação do fornecedor de identidade personalizado para ver que outros âmbitos podem estar disponíveis.

## <a name="response-type"></a>Tipo de resposta

O tipo de resposta descreve que tipo de informação é enviada na chamada inicial para o `authorization_endpoint` fornecedor de identidade personalizado. Podem ser utilizados os seguintes tipos de resposta:

* `code`: De acordo com o fluxo de código de [autorização,](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth)um código será devolvido ao Azure AD B2C. A Azure AD B2C procede à chamada `token_endpoint` para a troca do código pelo token.
* `id_token`: Um token de ID é devolvido ao Azure AD B2C do fornecedor de identidade personalizado.

## <a name="response-mode"></a>Modo de resposta

O modo de resposta define o método que deve ser utilizado para enviar os dados de volta do fornecedor de identidade personalizado para Azure AD B2C. Podem ser utilizados os seguintes modos de resposta:

* `form_post`: Este modo de resposta é recomendado para uma melhor segurança. A resposta é transmitida através do `POST` método HTTP, com o código ou ficha a ser codificado no corpo utilizando o `application/x-www-form-urlencoded` formato.
* `query`: O código ou ficha é devolvido como parâmetro de consulta.

## <a name="domain-hint"></a>Dica de domínio

A dica de domínio pode ser usada para saltar diretamente para o sinal na página do fornecedor de identidade especificado, em vez de ter o utilizador a fazer uma seleção entre a lista de fornecedores de identidade disponíveis. Para permitir este tipo de comportamento, insira um valor para a sugestão de domínio. Para saltar para o fornecedor de identidade personalizado, apromte o parâmetro `domain_hint=<domain hint value>` para o fim do seu pedido ao ligar para Azure AD B2C para iniciar sação.

## <a name="claims-mapping"></a>Mapeamento de reclamações

Depois de o fornecedor de identidade personalizado enviar um símbolo de identificação para Azure AD B2C, o Azure AD B2C precisa de ser capaz de mapear as reclamações do token recebido para as alegações que a Azure AD B2C reconhece e utiliza. Para cada um dos seguintes mapeamentos, consulte a documentação do fornecedor de identidade personalizado para compreender as alegações que são devolvidas nos tokens do fornecedor de identidade:

* **ID do utilizador**: Introduza a alegação que fornece o *identificador único* para o utilizador inscrito.
* **Nome do visor**: Introduza a alegação que fornece o nome de *exibição* ou *nome completo* para o utilizador.
* **Nome dado**: Introduza a reclamação que fornece o *primeiro nome* do utilizador.
* **Apelido**: Introduza a reclamação que fornece o *apelido* do utilizador.
* **Email**: Introduza a reclamação que fornece o endereço de *e-mail* do utilizador.

## <a name="add-the-identity-provider-to-a-user-flow"></a>Adicione o fornecedor de identidade a um fluxo de utilizador 

1. No seu inquilino Azure AD B2C, selecione **fluxos de utilizador**.
1. Clique no fluxo do utilizador que pretende adicionar ao fornecedor de identidade. 
1. Nos **prestadores de identidade Social,** selecione o fornecedor de identidade que adicionou. Por exemplo, *Contoso.*
1. Selecione **Guardar**.
1. Para testar a sua política, selecione **Executar o fluxo do utilizador**.
1. Para **Aplicação**, selecione a aplicação web chamada *testapp1* que registou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms` .
1. Selecione o botão **de fluxo do utilizador Executar.**
1. A partir da página de inscrição ou de inscrição, selecione o fornecedor de identidade que pretende iniciar s-in. Por exemplo, *Contoso.*

Se o processo de inscrição for bem sucedido, o seu navegador é redirecionado para `https://jwt.ms` , que exibe o conteúdo do token devolvido pelo Azure AD B2C.

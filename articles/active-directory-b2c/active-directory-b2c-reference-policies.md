---
title: Fluxos de usuário em Azure Active Directory B2C | Microsoft Docs
description: Saiba mais sobre a estrutura de política extensível de Azure Active Directory B2C e como criar vários fluxos de usuário.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 08da04a8bf167c99ef2384a9714034ae1865bec1
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065376"
---
# <a name="user-flows-in-azure-active-directory-b2c"></a>Fluxos de usuário em Azure Active Directory B2C

A estrutura de política extensível da Azure Active Directory B2C (Azure AD B2C) é a força principal do serviço. As políticas descrevem totalmente as experiências de identidade, como inscrição, entrada ou edição de perfil. Para ajudá-lo a configurar as tarefas de identidade mais comuns, o portal de Azure AD B2C inclui políticas configuráveis, predefinidas, chamadas de **fluxos de usuário**.

## <a name="what-are-user-flows"></a>O que são fluxos de usuário?

Um fluxo de usuário permite que você controle comportamentos em seus aplicativos definindo as seguintes configurações:

- Tipos de conta usados para entrar, como contas sociais, como um Facebook ou contas locais
- Atributos a serem coletados do consumidor, como primeiro nome, CEP e tamanho do calçado
- Multi-Factor Authentication do Azure
- Personalização da interface do usuário
- Informações que o aplicativo recebe como declarações em um token

Você pode criar vários fluxos de usuário de diferentes tipos em seu locatário e usá-los em seus aplicativos, conforme necessário. Os fluxos de usuário podem ser reutilizados em todos os aplicativos. Essa flexibilidade permite que você defina e modifique experiências de identidade com mínimo ou nenhuma alteração no seu código. Seu aplicativo dispara um fluxo de usuário usando uma solicitação de autenticação HTTP padrão que inclui um parâmetro de fluxo de usuário. Um [token](active-directory-b2c-reference-tokens.md) personalizado é recebido como uma resposta.

Os exemplos a seguir mostram o parâmetro de cadeia de caracteres de consulta "p" que especifica o fluxo de usuário a ser usado:

```
https://contosob2c.b2clogin.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siup                                       // Your sign-up user flow
```

```
https://contosob2c.b2clogin.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siin                                       // Your sign-in user flow
```

## <a name="user-flow-versions"></a>Versões de fluxo de utilizador

Na portal do Azure, novas [versões de fluxos de usuário](user-flow-versions.md) estão sendo adicionadas o tempo todo. Quando você começar a usar o Azure AD B2C, os fluxos de usuário testados serão recomendados para você. Ao criar um novo fluxo de usuário, você escolhe o fluxo de usuário que precisa na guia **recomendado** .

Os fluxos de usuário a seguir são recomendados no momento:

- **Inscrever-se e entrar** – lida com as duas experiências de inscrição e entrada com uma única configuração. Os usuários são orientados no caminho certo, dependendo do contexto. É recomendável que você use esse fluxo de usuário em um fluxo de usuário de **inscrição** ou um fluxo de usuário de **entrada** .
- **Edição de perfil** – permite que os usuários editem suas informações de perfil.
- **Redefinição de senha** – permite que você configure se e como os usuários podem redefinir sua senha.

## <a name="linking-user-flows"></a>Vinculando fluxos de usuário

Um fluxo de usuário de **inscrição ou entrada** com contas locais inclui um link **esqueceu a senha?** na primeira página da experiência. Clicar nesse link não disparará automaticamente um fluxo de usuário de redefinição de senha.

Em vez disso, o `AADB2C90118` código de erro é retornado para seu aplicativo. Seu aplicativo precisa manipular esse código de erro executando um fluxo de usuário específico que redefine a senha. Para ver um exemplo, dê uma olhada em um exemplo [simples de ASP.net](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI) que demonstra a vinculação de fluxos de usuário.

## <a name="email-address-storage"></a>Armazenamento de endereço de email

Um endereço de email pode ser necessário como parte de um fluxo de usuário. Se o usuário autenticar com um provedor de identidade social, o endereço de email será armazenado na propriedade **otherMails** . Se uma conta local for baseada em um nome de usuário, o endereço de email será armazenado em uma propriedade de detalhes de autenticação forte. Se uma conta local for baseada em um endereço de email, o endereço de email será armazenado na propriedade **signInNames** .

Não há garantia de que o endereço de email seja verificado em nenhum desses casos. Um administrador de locatários pode desabilitar a verificação de email nas políticas básicas para contas locais. Mesmo se a verificação de endereço de email estiver habilitada, os endereços não serão verificados se vierem de um provedor de identidade social e não tiverem sido alterados.

Somente as propriedades **otherMails** e **signInNames** são expostas por meio do Active Directory API do Graph. O endereço de email na propriedade de detalhes de autenticação forte não está disponível.

## <a name="next-steps"></a>Passos Seguintes

Para criar os fluxos de usuário recomendados, siga as instruções [em Tutorial: Criar um fluxo](tutorial-create-user-flows.md)de usuário.



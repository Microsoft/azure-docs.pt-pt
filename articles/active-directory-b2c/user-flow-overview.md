---
title: Fluxos de utilizadores em Azure Ative Directory B2C  Microsoft Docs
description: Saiba mais sobre o quadro de política extensível do Azure Ative Directory B2C e como criar vários fluxos de utilizadores.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c11bc48742c398d2048a236c7d00af044971f845
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78185615"
---
# <a name="user-flows-in-azure-active-directory-b2c"></a>Fluxos de utilizadores em Diretório Ativo Azure B2C

O quadro de política extensível do Azure Ative Directory B2C (Azure AD B2C) é a força central do serviço. As políticas descrevem totalmente experiências de identidade como inscrição, inscrição ou edição de perfil. Para ajudá-lo a configurar as tarefas de identidade mais comuns, o portal Azure AD B2C inclui políticas pré-definidas e configuráveis chamadas **fluxos de utilizadores**.

## <a name="what-are-user-flows"></a>O que são fluxos de utilizador?

Um fluxo de utilizador permite controlar comportamentos nas suas aplicações configurando as seguintes definições:

- Tipos de conta utilizados para iniciar sessão, tais como contas sociais como um Facebook ou contas locais
- Atributos a recolher junto do consumidor, tais como primeiro nome, código postal e tamanho do sapato
- Multi-Factor Authentication do Azure
- Personalização da interface do utilizador
- Informação que o pedido recebe como reclamações num símbolo

Pode criar muitos fluxos de utilizadores de diferentes tipos no seu inquilino e usá-los nas suas aplicações conforme necessário. Os fluxos de utilizadores podem ser reutilizados através de aplicações. Esta flexibilidade permite-lhe definir e modificar experiências de identidade com alterações mínimas ou sem alterações ao seu código. A sua aplicação aciona um fluxo de utilizador utilizando um pedido de autenticação HTTP padrão que inclui um parâmetro de fluxo do utilizador. Um [token](tokens-overview.md) personalizado é recebido como resposta.

Os seguintes exemplos mostram o parâmetro de corda de consulta "p" que especifica o fluxo do utilizador a utilizar:

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

No portal Azure, estão sempre a ser adicionadas novas [versões de fluxos de utilizadores.](user-flow-versions.md) Quando começar com o Azure AD B2C, recomenda-se a utilização de fluxos de utilizadores testados. Quando cria um novo fluxo de utilizador, escolhe o fluxo de utilizador de que necessita a partir do **separador Recomendado.**

Os seguintes fluxos de utilizadores são atualmente recomendados:

- **Inscreva-se e inscreva-se** - Manuseie ambas as experiências de inscrição e inscrição com uma única configuração. Os utilizadores são conduzidos pelo caminho certo, dependendo do contexto. Recomenda-se que utilize este fluxo de utilizador sobre um fluxo de utilizador **de inscrição** ou um fluxo de utilizador **de entrada.**
- **Edição de perfis** - Permite aos utilizadores editar em informação sobre o seu perfil.
- **Redefinição da palavra-passe** - Permite-lhe configurar se e como os utilizadores podem redefinir a sua palavra-passe.

## <a name="linking-user-flows"></a>Ligação dos fluxos de utilizadores

Um fluxo de utilizador de **inscrição ou de entrada** com contas locais inclui uma **senha esquecida na** primeira página da experiência. Clicar neste link não despoleta automaticamente um fluxo de utilizador de redefinição de palavra-passe.

Em vez disso, o código de erro `AADB2C90118` é devolvido à sua aplicação. A sua aplicação precisa de lidar com este código de erro executando um fluxo específico do utilizador que redefine a palavra-passe. Para ver um exemplo, dê uma olhada numa [amostra simples ASP.NET](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI) que demonstre a ligação dos fluxos de utilizador.

## <a name="email-address-storage"></a>Armazenamento de endereço de e-mail

Um endereço de e-mail pode ser exigido como parte de um fluxo de utilizador. Se o utilizador autenticar com um fornecedor de identidade social, o endereço de e-mail é armazenado na propriedade **de outros Mails.** Se uma conta local se basear num nome de utilizador, o endereço de e-mail é armazenado numa propriedade de detalhes de autenticação forte. Se uma conta local se baseia num endereço de e-mail, então o endereço de e-mail é armazenado na propriedade **signInNames.**

O endereço de e-mail não é garantido para ser verificado em nenhum destes casos. Um administrador de inquilino pode desativar a verificação de e-mail nas políticas básicas para contas locais. Mesmo que a verificação do endereço de e-mail esteja ativada, os endereços não são verificados se provêm de um fornecedor de identidade social e não foram alterados.

Apenas os **outros Mails** e **propriedades signInNames** são expostos através da API do Microsoft Graph. O endereço de e-mail na propriedade de detalhes de autenticação forte não está disponível.

## <a name="next-steps"></a>Passos seguintes

Para criar os fluxos recomendados do utilizador, siga as instruções no [Tutorial: Criar um fluxo de utilizador](tutorial-create-user-flows.md).

---
title: Fluxos de utilizador no Azure Ative Directory B2C Microsoft Docs
description: Saiba mais sobre o quadro de política extensível do Azure Ative Directory B2C e como criar vários fluxos de utilizador.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "78185615"
---
# <a name="user-flows-in-azure-active-directory-b2c"></a>Fluxos de utilizadores no Azure Ative Directory B2C

O quadro político extensível do Azure Ative Directory B2C (Azure AD B2C) é a força central do serviço. As políticas descrevem totalmente experiências de identidade como inscrição, inscrição ou edição de perfis. Para ajudá-lo a configurar as tarefas de identidade mais comuns, o portal Azure AD B2C inclui políticas predefinidas e configuráveis chamadas **fluxos de utilizador.**

## <a name="what-are-user-flows"></a>O que são fluxos de utilizador?

Um fluxo de utilizador permite-lhe controlar comportamentos nas suas aplicações configurando as seguintes definições:

- Tipos de conta utilizados para o s-in, tais como contas sociais como um Facebook ou contas locais
- Atributos a serem recolhidos junto do consumidor, como o primeiro nome, código postal e tamanho do sapato
- Multi-Factor Authentication do Azure
- Personalização da interface de utilizador
- Informação que o pedido recebe como reclamações num token

Pode criar muitos fluxos de utilizadores de diferentes tipos no seu inquilino e usá-los nas suas aplicações conforme necessário. Os fluxos dos utilizadores podem ser reutilizados através das aplicações. Esta flexibilidade permite-lhe definir e modificar experiências de identidade com alterações mínimas ou nenhumas ao seu código. A sua aplicação aciona um fluxo de utilizador utilizando um pedido de autenticação HTTP padrão que inclui um parâmetro de fluxo do utilizador. Um [token](tokens-overview.md) personalizado é recebido como resposta.

Os exemplos a seguir mostram o parâmetro de cadeia de consulta "p" que especifica o fluxo do utilizador a utilizar:

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

No portal Azure, estão sempre a ser adicionadas novas [versões de fluxos de utilizadores.](user-flow-versions.md) Quando começa com o Azure AD B2C, recomenda-se que os fluxos de utilizador testados sejam utilizados. Quando criar um novo fluxo de utilizador, escolha o fluxo de utilizador de que necessita a partir do separador **Recomendado.**

Recomenda-se atualmente os seguintes fluxos de utilizador:

- **Inscreva-se e inscreva-se** - Trata de ambas as experiências de inscrição e de inscrição com uma única configuração. Os utilizadores são conduzidos pelo caminho certo, dependendo do contexto. Recomenda-se que utilize este fluxo de utilizador sobre um fluxo de utilizador **de inscrição** ou um fluxo de utilizador **de entrada.**
- **Edição de perfis** - Permite que os utilizadores editem as suas informações de perfil.
- **Reset da palavra-passe** - Permite-lhe configurar se e como os utilizadores podem redefinir a sua palavra-passe.

## <a name="linking-user-flows"></a>Ligação dos fluxos dos utilizadores

Um fluxo de utilizador **de inscrição ou de inscrição** com contas locais inclui uma **palavra-passe esquecida na** primeira página da experiência. Clicar neste link não aciona automaticamente o fluxo do utilizador de redefinição de palavra-passe.

Em vez disso, o código de erro `AADB2C90118` é devolvido à sua aplicação. A sua aplicação precisa de lidar com este código de erro executando um fluxo de utilizador específico que reinicia a palavra-passe. Para ver um exemplo, veja uma [simples amostra de ASP.NET](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI) que demonstre a ligação dos fluxos dos utilizadores.

## <a name="email-address-storage"></a>Armazenamento de endereço de e-mail

Um endereço de e-mail pode ser necessário como parte de um fluxo de utilizador. Se o utilizador autenticar com um fornecedor de identidade social, o endereço de e-mail é armazenado na **propriedade de outrosMails.** Se uma conta local se basear num nome de utilizador, então o endereço de e-mail é armazenado numa propriedade de dados de autenticação forte. Se uma conta local se basear num endereço de e-mail, então o endereço de e-mail é armazenado na propriedade **SignInNames.**

O endereço de e-mail não é garantido para ser verificado em nenhum destes casos. Um administrador de inquilino pode desativar a verificação de e-mail nas políticas básicas para contas locais. Mesmo que a verificação do endereço de e-mail esteja ativada, os endereços não são verificados se são provenientes de um fornecedor de identidade social e não foram alterados.

Apenas as outras propriedades **DeMails** e **signInNames** são expostas através da API do Gráfico microsoft. O endereço de e-mail na propriedade de detalhes de autenticação forte não está disponível.

## <a name="next-steps"></a>Próximos passos

Para criar os fluxos recomendados do utilizador, siga as instruções em [Tutorial: Crie um fluxo de utilizador](tutorial-create-user-flows.md).

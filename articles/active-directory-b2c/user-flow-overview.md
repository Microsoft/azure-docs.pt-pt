---
title: Fluxos de utilizador no Azure Ative Directory B2C Microsoft Docs
titleSuffix: Azure AD B2C
description: Saiba mais sobre o quadro de política extensível do Azure Ative Directory B2C e como criar vários fluxos de utilizador.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/30/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7a7736602fafb740d1d76fa09fd26da25e4ff9f5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87481602"
---
# <a name="user-flows-in-azure-active-directory-b2c"></a>Fluxos de utilizadores no Azure Ative Directory B2C

Para ajudá-lo a configurar as tarefas de identidade mais comuns para as suas aplicações, o portal Azure AD B2C inclui políticas predefinidas e configuráveis chamadas **fluxos de utilizador.** Um fluxo de utilizador permite-lhe determinar como os utilizadores interagem com a sua aplicação quando fazem coisas como iniciar sessão, iniciar sessão, editar um perfil ou redefinir uma palavra-passe. Com os fluxos do utilizador, pode controlar as seguintes capacidades:

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

O Azure AD B2C inclui vários tipos de fluxos de utilizador:

- **Inscreva-se e inscreva-se** - Trata de ambas as experiências de inscrição e de inscrição com uma única configuração. Os utilizadores são conduzidos pelo caminho certo, dependendo do contexto. Também estão incluídos fluxos **de inscrição** ou **de entrada de** utilizadores separados. Mas geralmente recomendamos a inscrição combinada e o sinal no fluxo do utilizador.
- **Edição de perfis** - Permite que os utilizadores editem as suas informações de perfil.
- **Reset da palavra-passe** - Permite-lhe configurar se e como os utilizadores podem redefinir a sua palavra-passe.

A maioria dos tipos de fluxo de utilizador tem uma versão **recomendada** e uma versão **Standard.** Para mais informações, consulte [as versões de fluxo do utilizador](user-flow-versions.md).

> [!IMPORTANT]
> Se já trabalhou com fluxos de utilizadores em Azure AD B2C antes, irá notar que mudamos a forma como referimos as versões de fluxo do utilizador. Anteriormente, oferecemos versões V1 (prontas para produção) e versões V1.1 e V2 (pré-visualização). Agora, consolidamos fluxos de utilizadores em duas versões:
>
>- Os fluxos **recomendados** para o utilizador são as novas versões de pré-visualização dos fluxos dos utilizadores. São completamente testados e combinam todas as características das versões **legados V2** e **V1.1.** Daqui para a frente, os novos fluxos recomendados de utilizador serão mantidos e atualizados. Assim que se deslocar para estes novos fluxos recomendados de utilizador, terá acesso a novas funcionalidades à medida que forem lançadas.
>- Os fluxos **padrão** do utilizador, anteriormente conhecidos como **V1,** estão geralmente disponíveis, fluxos de utilizador prontos para a produção. Se os fluxos dos seus utilizadores forem críticos da missão e dependerem de versões altamente estáveis, pode continuar a utilizar fluxos padrão de utilizador, percebendo que estas versões não serão mantidas e atualizadas.
>
>Todos os fluxos de pré-visualização legado (V1.1 e V2) estão em vias de depreciação até 1 de agosto de **2021**. Sempre que possível, recomendamos vivamente que [mude para os novos fluxos **recomendados** para](user-flow-versions.md#how-to-switch-to-a-new-recommended-user-flow) que possa sempre tirar partido das funcionalidades e atualizações mais recentes.

## <a name="linking-user-flows"></a>Ligação dos fluxos dos utilizadores

Um fluxo de utilizador **de inscrição ou de inscrição** com contas locais inclui uma **palavra-passe esquecida na** primeira página da experiência. Clicar neste link não aciona automaticamente o fluxo do utilizador de redefinição de palavra-passe.

Em vez disso, o código de erro `AADB2C90118` é devolvido à sua aplicação. A sua aplicação precisa de lidar com este código de erro executando um fluxo de utilizador específico que reinicia a palavra-passe. Para ver um exemplo, veja uma [simples amostra de ASP.NET](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI) que demonstre a ligação dos fluxos dos utilizadores.

## <a name="email-address-storage"></a>Armazenamento de endereço de e-mail

Um endereço de e-mail pode ser necessário como parte de um fluxo de utilizador. Se o utilizador autenticar com um fornecedor de identidade social, o endereço de e-mail é armazenado na **propriedade de outrosMails.** Se uma conta local se basear num nome de utilizador, então o endereço de e-mail é armazenado numa propriedade de dados de autenticação forte. Se uma conta local se basear num endereço de e-mail, então o endereço de e-mail é armazenado na propriedade **SignInNames.**

O endereço de e-mail não é garantido para ser verificado em nenhum destes casos. Um administrador de inquilino pode desativar a verificação de e-mail nas políticas básicas para contas locais. Mesmo que a verificação do endereço de e-mail esteja ativada, os endereços não são verificados se são provenientes de um fornecedor de identidade social e não foram alterados.

Apenas as outras propriedades **DeMails** e **signInNames** são expostas através da API do Gráfico microsoft. O endereço de e-mail na propriedade de detalhes de autenticação forte não está disponível.

## <a name="next-steps"></a>Passos seguintes

Para criar os fluxos recomendados do utilizador, siga as instruções em [Tutorial: Crie um fluxo de utilizador](tutorial-create-user-flows.md).

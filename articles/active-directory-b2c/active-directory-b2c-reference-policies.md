---
title: Fluxos de utilizador no Azure Active Directory B2C | Documentos da Microsoft
description: Saiba mais sobre a estrutura de política extensível do Azure Active Directory B2C e como criar vários fluxos de utilizador.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 67db13c8a36977f2614ba7b0e263919bd0405bc7
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56585598"
---
# <a name="user-flows-in-azure-active-directory-b2c"></a>Fluxos de utilizador no Azure Active Directory B2C

A estrutura de política extensível do Azure Active Directory (Azure AD) B2C é a força do principal do serviço. Políticas de descrevem completamente experiências de identidade como inscrição, início de sessão ou edição de perfis. Para ajudá-lo a configurar as tarefas mais comuns de identidade, o portal do Azure AD B2C inclui políticas predefinidas e configuráveis chamadas **fluxos de utilizador**. 

## <a name="what-are-user-flows"></a>Quais são os fluxos de utilizador?

Um fluxo de utilizador permite-lhe controlar os comportamentos em seus aplicativos ao configurar as seguintes definições:

- Conta de tipos utilizado para iniciar sessão, por exemplo, contas de redes sociais, como um Facebook ou contas locais
- Atributos para recolher do consumidor, como o nome próprio, código postal e o tamanho do calçado
- Multi-Factor Authentication do Azure
- Personalização da interface do usuário
- Informações de que a aplicação recebe como afirmações num token 

Pode criar fluxos de utilizador de muitos tipos diferentes no seu inquilino e utilizá-los em seus aplicativos, conforme necessário. Fluxos de utilizador podem ser reutilizados em todas as aplicações. Essa flexibilidade permite-lhe definir e modificar as experiências de identidade com mínima ou sem alterações ao seu código. O aplicativo aciona um fluxo de utilizador, utilizando uma solicitação de autenticação HTTP padrão, que inclui um parâmetro de fluxo de utilizador. Um personalizado [token](active-directory-b2c-reference-tokens.md) é recebida como uma resposta. 

Os exemplos seguintes mostram o parâmetro de cadeia de caracteres de consulta de "p" que especifica o fluxo de utilizador a utilizar:

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

No portal do Azure, novos [versões de fluxos de utilizador](user-flow-versions.md) estão a ser adicionadas o tempo todo. Quando começar a utilizar com o Azure AD B2C, testado fluxos são recomendados para a utilização do utilizador. Quando cria um novo fluxo de utilizador, selecione o fluxo de utilizador que precisam a partir da **recomendado** separador.

Os seguintes fluxos de utilizador atualmente são recomendados:

- **Inscreva-se e iniciar sessão** -lida com ambas as experiências de inscrição e início de sessão com uma configuração única. Os utilizadores são liderados para baixo o caminho certo dependendo do contexto. É recomendado que utilize este fluxo de utilizador através de um **Inscreva-se** fluxo de utilizador ou uma **início de sessão** fluxo de utilizador.
- **Edição de perfil** -permite aos utilizadores editar as informações do perfil.
- **Reposição de palavra-passe** -permite-lhe configurar se e como os utilizadores podem repor a palavra-passe.

## <a name="linking-user-flows"></a>Ligação de fluxos de utilizador

Uma **inscrição ou início de sessão** inclui o fluxo de utilizador com as contas locais um **palavra-passe de Forgot?** link na primeira página da experiência. Ao clicar nesta ligação não automaticamente acionador uma palavra-passe de reposição de fluxo de utilizador. 

Em vez disso, o código de erro `AADB2C90118` é devolvido ao seu aplicativo. Seu aplicativo precisa lidar com este código de erro ao executar um fluxo de utilizador específico que redefine a palavra-passe. Para ver um exemplo, dê uma olhada num [exemplo ASP.NET simples](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI) que demonstra a vinculação de fluxos de utilizador.

## <a name="email-address-storage"></a>Armazenamento de endereço de e-mail

Um endereço de e-mail pode ser necessário como parte de um fluxo de utilizador. Se o utilizador é autenticado com um fornecedor de identidade social, o endereço de e-mail é armazenado na **otherMails** propriedade. Se uma conta local se basear num nome de utilizador, o endereço de e-mail é armazenado numa propriedade de detalhes de autenticação forte. Se uma conta local é baseada num endereço de e-mail, em seguida, o endereço de e-mail é armazenado na **signInNames** propriedade.
 
O endereço de e-mail não é garantido sejam verificadas em qualquer um desses casos. Um administrador inquilino pode desativar a verificação de e-mail nas políticas básicas para contas locais. Mesmo se a verificação do endereço de e-mail estiver ativada, os endereços não são verificados se eles vêm de um fornecedor de identidade de redes sociais e ainda não foi alterados.
 
Apenas os **otherMails** e **signInNames** propriedades são expostas por meio da API de gráfico de diretório Active Directory. O endereço de e-mail na propriedade de detalhes de autenticação forte não está disponível.

## <a name="next-steps"></a>Passos Seguintes

Para criar os fluxos de utilizador recomendada, siga as instruções em [Tutorial: Criar um fluxo de utilizador](tutorial-create-user-flows.md).



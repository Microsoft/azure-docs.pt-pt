---
title: Fluxos de utilizadores e políticas personalizadas no Azure Ative Directory B2C | Microsoft Docs
titleSuffix: Azure AD B2C
description: Saiba mais sobre os fluxos de utilizadores incorporados e sobre o quadro de política extensível de política personalizada do Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/08/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2e4dbc5178bec3a5b1f0931267465879f604f36f
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107226013"
---
# <a name="user-flows-and-custom-policies-overview"></a>Fluxos de utilizador e visão geral das políticas personalizadas

No Azure AD B2C, pode definir a lógica de negócio que os utilizadores seguem para ter acesso à sua aplicação. Por exemplo, pode determinar a sequência de passos que os utilizadores seguem quando fazem sessão, se inscrevem, editam um perfil ou reiniciam uma palavra-passe. Após completar a sequência, o utilizador adquire um token e ganha acesso à sua aplicação. 

Em Azure AD B2C, existem duas formas de proporcionar experiências de utilizador de identidade:

* **Os fluxos de utilizador** são políticas pré-indefinidas, incorporadas e configuráveis que fornecemos para que possa criar experiências de inscrição, inscrição e edição de políticas em minutos.

* **As políticas personalizadas** permitem-lhe criar as suas próprias viagens de utilizador para cenários complexos de experiência de identidade.

A imagem que se segue mostra as definições de fluxo do utilizador UI, versus ficheiros de configuração de política personalizados.

![A screenshot mostra as definições de fluxo do utilizador UI, versus ficheiros de configuração de política personalizados.](media/user-flow-overview/user-flow-vs-custom-policy.png)

Este artigo fornece uma breve visão geral dos fluxos dos utilizadores e das políticas personalizadas, e ajuda-o a decidir qual o método que funcionará melhor para as necessidades do seu negócio.

## <a name="user-flows"></a>Fluxos do utilizador

Para configurar as tarefas de identidade mais comuns, o portal Azure inclui várias políticas predefinidas e configuráveis chamadas *fluxos de utilizador.*

Pode configurar definições de fluxo de utilizador como estas para controlar comportamentos de experiência de identidade nas suas aplicações:

* Tipos de conta utilizados para o s-in, como contas sociais como um Facebook, ou contas locais que usam um endereço de e-mail e senha para iniciar sação
* Atributos a recolher junto do consumidor, tais como o primeiro nome, código postal ou país/região de residência
* Autenticação de vários fatores Azure Ad (MFA)
* Personalização da interface de utilizador
* Conjunto de reclamações num token que a sua aplicação recebe depois de o utilizador completar o fluxo do utilizador
* Gestão de sessões
* ... e mais

A maioria dos cenários de identidade comuns para apps podem ser definidos e implementados eficazmente com fluxos de utilizadores. Recomendamos que utilize os fluxos de utilizador incorporados, a menos que tenha cenários complexos de viagem de utilizador que exijam a flexibilidade total das políticas personalizadas.

## <a name="custom-policies"></a>Políticas personalizadas

As políticas personalizadas são ficheiros de configuração que definem o comportamento da sua experiência de utilizador do inquilino Azure AD B2C. Embora os fluxos de utilizador estejam predefinidos no portal Azure AD B2C para as tarefas de identidade mais comuns, as políticas personalizadas podem ser totalmente editadas por um desenvolvedor de identidade para completar muitas tarefas diferentes.

Uma política personalizada é totalmente configurável e orientada pela política. Orquestra a confiança entre entidades em protocolos padrão. Por exemplo, OpenID Connect, OAuth, SAML e alguns não padrão, por exemplo, permutas de sistema-sistema baseadas em API. A estrutura cria experiências fáceis de utilizar e etiquetas brancas.

A política personalizada dá-lhe a capacidade de construir viagens de utilizador com qualquer combinação de passos. Por exemplo:

* Federate com outros fornecedores de identidade
* Desafios da autenticação de múltiplos fatores de primeira e de terceiros (MFA)
* Recolher qualquer entrada de utilizador
* Integre-se com sistemas externos utilizando a comunicação REST API

Cada viagem de utilizador é definida por uma política. Pode construir quantas ou poucas políticas necessita para permitir a melhor experiência do utilizador para a sua organização.

![Diagrama mostrando um exemplo de uma viagem complexa de utilizador possibilitada pelo IEF](media/user-flow-overview/custom-policy-diagram.png)

Uma política personalizada é definida por vários ficheiros XML que se referem uns aos outros numa cadeia hierárquica. Os elementos XML definem o esquema de reclamações, as transformações de reclamações, as definições de conteúdo, os fornecedores de sinistros, os perfis técnicos, as etapas de orquestração de jornadas de utilizador e outros aspetos da experiência de identidade.

A poderosa flexibilidade das políticas personalizadas é mais adequada para quando é necessário construir cenários de identidade complexos. Os desenvolvedores que configuram políticas personalizadas devem definir as relações fidedignas com cuidado para incluir pontos finais de metadados, definições exatas de troca de alegações e configurar segredos, chaves e certificados, conforme necessário por cada fornecedor de identidade.

Saiba mais sobre políticas [personalizadas em políticas personalizadas no Azure Ative Directory B2C](custom-policy-overview.md).

## <a name="comparing-user-flows-and-custom-policies"></a>Comparar fluxos de utilizadores e políticas personalizadas

A tabela seguinte dá uma comparação detalhada dos cenários que pode com os fluxos de utilizador AD B2C do Azure E.

| Contexto | Fluxos do utilizador | Políticas personalizadas |
|-|-------------------|-----------------|
| Utilizadores-alvo | Todos os desenvolvedores de aplicações com ou sem conhecimento de identidade. | Profissionais de identidade, integradores de sistemas, consultores e equipas de identidade interna. São confortáveis com os fluxos OpenID Connect e compreendem os fornecedores de identidade e a autenticação baseada em sinistros. |
| Método de configuração | Portal Azure com uma interface de utilizador fácil de utilizar (UI). | Editar diretamente ficheiros XML e, em seguida, fazer o upload para o portal Azure. |
| Personalização de UI | [Personalização completa da UI](customize-ui-with-html.md) incluindo HTML, CSS [e, JavaScript](javascript-and-page-layout.md).<br><br>[Suporte multilanguage](language-customization.md) com cordas personalizadas. | Mesma |
| Personalização de atributos | Atributos padrão e personalizados. | Mesma |
| Gestão de token e sessão | [Personalize o comportamento de tokens](configure-tokens.md) e [sessões.](session-behavior.md) | Mesma |
| Fornecedores de Identidade | [Predefinido](identity-provider-local.md) provedor local ou [social,](add-identity-provider.md)como federação com inquilinos do Azure Ative Directory. | OIDC, OAUTH e SAML baseados em padrões.  A autenticação também é possível utilizando a integração com AS REST. |
| Tarefas identitárias | [Inscreva-se ou inscreva-se](add-sign-up-and-sign-in-policy.md) com contas locais ou muitas contas sociais.<br><br>[Redefinição da palavra-passe de autosserviço](add-password-reset-policy.md).<br><br>[Edição de perfil.](add-profile-editing-policy.md)<br><br>Autenticação multi-factor.<br><br>O acesso aos fluxos de fichas. | Complete as mesmas tarefas que os fluxos do utilizador utilizando fornecedores de identidade personalizados ou utilize âmbitos personalizados.<br><br>Disponibilização de uma conta de utilizador noutro sistema no momento do registo.<br><br>Envie um e-mail de boas-vindas usando o seu próprio fornecedor de serviços de e-mail.<br><br>Utilize uma loja de utilizadores fora do Azure AD B2C.<br><br>Validar o utilizador forneceu informações com um sistema fidedigno utilizando uma API. |

## <a name="application-integration"></a>Integração de aplicações

Pode criar muitos fluxos de utilizador, ou políticas personalizadas de diferentes tipos no seu inquilino e usá-las nas suas aplicações conforme necessário. Tanto os fluxos de utilizador como as políticas personalizadas podem ser reutilizadas através das aplicações. Esta flexibilidade permite-lhe definir e modificar experiências de identidade com alterações mínimas ou nenhumas ao seu código. 

Quando um utilizador quer iniciar sação na sua aplicação, a aplicação inicia um pedido de autorização a um ponto final fornecido por políticas de fluxo ou personalizados. O fluxo do utilizador ou a política personalizada define e controla a experiência do utilizador. Quando completam um fluxo de utilizador, o Azure AD B2C gera um token e, em seguida, redireciona o utilizador de volta para a sua aplicação.

![Aplicativo móvel com setas mostrando fluxo entre a página de entrada de Azure AD B2C](media/user-flow-overview/app-integration.png)

Várias aplicações podem usar o mesmo fluxo de utilizador ou política personalizada. Uma única aplicação pode utilizar vários fluxos de utilizador ou políticas personalizadas.

Por exemplo, para iniciar sôm numa aplicação, a aplicação utiliza a *inscrição ou o sinal no* fluxo do utilizador. Depois de o utilizador ter assinado, podem querer editar o seu perfil. Para editar o perfil, a aplicação inicia outro pedido de autorização, desta vez utilizando o fluxo do utilizador *de edição de perfil.*

A sua aplicação aciona um fluxo de utilizador utilizando um pedido de autenticação HTTP padrão que inclui o fluxo do utilizador ou o nome de política personalizado. Um [token](tokens-overview.md) personalizado é recebido como resposta.


## <a name="next-steps"></a>Passos seguintes

- Para criar os fluxos recomendados do utilizador, siga as instruções em [Tutorial: Crie um fluxo de utilizador](tutorial-create-user-flows.md).
- Saiba mais sobre as [versões de fluxo do utilizador em Azure AD B2C](user-flow-versions.md).
- Saiba mais sobre [as políticas personalizadas Azure AD B2C](custom-policy-overview.md).

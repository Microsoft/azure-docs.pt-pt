---
title: Inscrição de self-service para identidades externas - Azure AD
description: Saiba como permitir que utilizadores externos se inscrevam nas suas próprias aplicações, permitindo o autosserviço de inscrição. Crie uma experiência de inscrição personalizada personalizando o fluxo de utilizador de inscrição de autosserviço.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 98456f26fbc7ca3955883eb283b54084bd86d503
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83737763"
---
# <a name="self-service-sign-up-preview"></a>Inscrição personalizada (Pré-visualização)
|     |
| --- |
| A inscrição em self-service é uma funcionalidade de pré-visualização pública do Azure Ative Directory. Para obter mais informações sobre pré-visualizações, veja [Termos de Utilização Suplementares do Microsoft Azure para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

Ao partilhar uma aplicação com utilizadores externos, pode nem sempre saber com antecedência quem necessitará de acesso à aplicação. Como alternativa ao envio de convites diretamente para indivíduos, pode permitir que utilizadores externos se inscrevam para aplicações específicas, permitindo o autosserviço de inscrição. Pode criar uma experiência de inscrição personalizada personalizando o fluxo de utilizador de inscrição de autosserviço. Por exemplo, pode fornecer opções para se inscrever com a Azure AD ou fornecedores de identidade social e recolher informações sobre o utilizador durante o processo de inscrição.

> [!NOTE]
> Pode associar fluxos de utilizadores a aplicações construídas pela sua organização. Os fluxos de utilizadores não podem ser utilizados para aplicações da Microsoft, como o SharePoint ou Teams.

## <a name="user-flow-for-self-service-sign-up"></a>Fluxo de utilizador para inscrição de autosserviço

Um fluxo de utilizador de inscrição de autosserviço cria uma experiência de inscrição para os seus utilizadores externos através da aplicação que pretende partilhar. O fluxo do utilizador pode ser associado a uma ou mais das suas aplicações. Primeiro, permitirá a inscrição de self-service para o seu inquilino e federará com os fornecedores de identidade que pretende permitir que utilizadores externos utilizem para iniciar sessão. Em seguida, criará e personalizará o fluxo de utilizador de inscrição e atribuirá-lhe as suas aplicações.
Pode configurar as definições de fluxo do utilizador para controlar a forma como o utilizador se inscreve na aplicação:

- Tipos de conta utilizados para iniciar sessão, tais como contas sociais como facebook ou contas AD Azure
- Atributos a recolher do utilizador que se inscreva, tais como primeiro nome, código postal ou país/região de residência

Quando um utilizador quer iniciar sessão na sua aplicação, seja uma aplicação web, móvel, de secretária ou de uma única página (SPA), a aplicação inicia um pedido de autorização para o ponto final fornecido pelo utilizador. O fluxo do utilizador define e controla a experiência do utilizador. Quando o utilizador completa o fluxo de utilizador de inscrição, o Azure AD gera um token e redireciona o utilizador de volta para a sua aplicação. Após a conclusão da inscrição, é prevista uma conta de hóspedes para o utilizador no diretório. Várias aplicações podem utilizar o mesmo fluxo de utilizador.

## <a name="example-of-self-service-sign-up"></a>Exemplo de inscrição de autosserviço

O exemplo que se segue ilustra como estamos a levar fornecedores de identidade social para a Azure AD com capacidades de inscrição de autosserviço para os utilizadores convidados.  
Um parceiro da Woodgrove abre a aplicação Woodgrove. Decidem que querem inscrever-se numa conta de fornecedor, por isso selecionam a sua conta de fornecedor, que inicia o fluxo de inscrição de autosserviço.

![Exemplo de página inicial de inscrição de autosserviço](media/self-service-sign-up-overview/example-start-sign-up-flow.png)

Usam o e-mail da sua escolha para se inscreverem.

![Exemplo mostrando seleção do Facebook para iniciar sessão](media/self-service-sign-up-overview/example-sign-in-with-facebook.png)

A Azure AD cria uma relação com a Woodgrove utilizando a conta de Facebook do parceiro e cria uma nova conta de hóspedes para o utilizador depois de se inscreverem.

A Woodgrove quer saber mais sobre o utilizador, como nome, nome comercial, código de registo de negócios, número de telefone.

![Exemplo mostrando atributos de inscrição do utilizador](media/self-service-sign-up-overview/example-enter-user-attributes.png)

O utilizador introduz a informação, continua o fluxo de inscrição e tem acesso aos recursos de que necessita.

![Exemplo mostrando o utilizador instávido](media/self-service-sign-up-overview/example-signed-in.png)

## <a name="next-steps"></a>Passos seguintes

 Para mais detalhes, consulte como adicionar a [inscrição de self-service a uma aplicação](self-service-sign-up-user-flow.md).
---
title: Inscrição de self-service para identidades externas - Azure AD
description: Saiba como permitir que os utilizadores externos se inscrevam nas suas aplicações, permitindo a inscrição de autosserviço. Crie uma experiência de inscrição personalizada personalizando o fluxo de utilizador de inscrição de autosserviço.
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
ms.openlocfilehash: 34b08e2e530843dd98c87e424812706247388228
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87909055"
---
# <a name="self-service-sign-up-preview"></a>Inscrição personalizada (Pré-visualização)

> [!NOTE]
> A inscrição de self-service é uma funcionalidade de pré-visualização pública do Azure Ative Directory. Para obter mais informações sobre pré-visualizações, veja [Termos de Utilização Suplementares do Microsoft Azure para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ao partilhar uma aplicação com utilizadores externos, pode nem sempre saber com antecedência quem irá precisar de acesso à aplicação. Como alternativa ao envio de convites diretamente para indivíduos, você pode permitir que utilizadores externos se inscrevam para aplicações específicas, permitindo a inscrição de self-service. Pode criar uma experiência de inscrição personalizada personalizando o fluxo de utilizador de inscrição de autosserviço. Por exemplo, pode fornecer opções para se inscrever com Azure AD ou fornecedores de identidade social e recolher informações sobre o utilizador durante o processo de inscrição.

> [!NOTE]
> Pode associar fluxos de utilizadores a aplicações construídas pela sua organização. Os fluxos de utilizadores não podem ser utilizados para aplicações da Microsoft, como o SharePoint ou o Teams.

## <a name="user-flow-for-self-service-sign-up"></a>Fluxo do utilizador para inscrição de autosserviço

Um fluxo de utilizador de inscrição de autosserviço cria uma experiência de inscrição para os seus utilizadores externos através da aplicação que pretende partilhar. O fluxo de utilizador pode ser associado a uma ou mais das suas aplicações. Primeiro, ativará a inscrição de self-service para o seu inquilino e federará com os fornecedores de identidade que pretende permitir que os utilizadores externos utilizem para iniciar sôm. Em seguida, irá criar e personalizar o fluxo de utilizador de inscrição e atribuir as suas aplicações ao mesmo.
Pode configurar as definições de fluxo do utilizador para controlar a forma como o utilizador se inscreve para a aplicação:

- Tipos de conta utilizados para o s-in, como contas sociais como o Facebook ou contas AD do Azure
- Atributos a serem recolhidos do utilizador que se inscreve, como o primeiro nome, código postal ou país/região de residência

Quando um utilizador quer iniciar sação na sua aplicação, seja uma aplicação web, móvel, desktop ou de uma página única (SPA), a aplicação inicia um pedido de autorização para o ponto final fornecido pelo utilizador. O fluxo do utilizador define e controla a experiência do utilizador. Quando o utilizador completa o fluxo do utilizador de inscrição, o Azure AD gera um token e redireciona o utilizador de volta para a sua aplicação. Após a conclusão da inscrição, é disponibilizada uma conta de hóspedes para o utilizador no diretório. Várias aplicações podem utilizar o mesmo fluxo de utilizador.

## <a name="example-of-self-service-sign-up"></a>Exemplo de inscrição de autosserviço

O exemplo a seguir ilustra como estamos a levar os fornecedores de identidade social para a Azure AD com capacidades de inscrição de self-service para os utilizadores convidados.  
Um parceiro da Woodgrove abre a app Woodgrove. Decidem inscrever-se numa conta de fornecedor, por isso selecionam Pedir a sua conta de fornecedor, que inicia o fluxo de inscrição de self-service.

![Exemplo da página inicial de inscrição de autosserviço](media/self-service-sign-up-overview/example-start-sign-up-flow.png)

Usam o e-mail da sua escolha para se inscreverem.

![Exemplo mostrando seleção do Facebook para iniciar sposição](media/self-service-sign-up-overview/example-sign-in-with-facebook.png)

A Azure AD cria uma relação com a Woodgrove usando a conta de Facebook do parceiro, e cria uma nova conta de hóspedes para o utilizador depois de se inscreverem.

Woodgrove quer saber mais sobre o utilizador, nome, nome comercial, código de registo comercial, número de telefone.

![Exemplo mostrando atributos de inscrição do utilizador](media/self-service-sign-up-overview/example-enter-user-attributes.png)

O utilizador introduz a informação, continua o fluxo de inscrição e obtém acesso aos recursos de que necessita.

![Exemplo mostrando o utilizador assinado em](media/self-service-sign-up-overview/example-signed-in.png)

## <a name="next-steps"></a>Passos seguintes

 Para mais detalhes, consulte como [adicionar inscrição de self-service a uma aplicação.](self-service-sign-up-user-flow.md)
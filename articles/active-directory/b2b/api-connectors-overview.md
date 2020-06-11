---
title: Gerir conectores API em fluxos de inscrição de autosserviço
description: Utilize conectores API para personalizar e alargar os fluxos de utilizador de inscrição de autosserviço
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45bb22f5f6c15e326bed2524fbc541cbdec26a70
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/10/2020
ms.locfileid: "84680162"
---
# <a name="use-api-connectors-to-customize-and-extend-self-service-sign-up"></a>Utilize conectores API para personalizar e prolongar o autosserviço de inscrição 

## <a name="overview"></a>Descrição geral 
Como desenvolvedor ou administrador de TI, pode utilizar conectores API para integrar os [fluxos de utilizador de inscrição de autosserviço](self-service-sign-up-overview.md) com sistemas externos, alavancando as APIs web. Por exemplo, pode utilizar conectores API para:

- [**Integre-se com fluxos de trabalho de aprovação personalizados.**](self-service-sign-up-add-approvals.md) Conecte-se a um sistema de aprovação personalizado para gerir a criação de conta.
<!-- - [**Perform identity proofing**](code-samples-self-service-sign-up.md#identity-proofing). Use an identity proofing and verification service to add an extra level of security to account creation decisions. -->
- **Realizar a prova de identidade**. Utilize um serviço de verificação e verificação de identidade para adicionar um nível extra de segurança às decisões de criação de conta.
- **Validar os dados de entrada do utilizador.** Validar contra dados de utilizador mal formados ou inválidos. Por exemplo, pode validar os dados fornecidos pelo utilizador com os dados existentes numa loja de dados externos ou lista de valores permitidos. Se for inválido, pode solicitar a um utilizador que forneça dados válidos ou impeça o utilizador de continuar o fluxo de inscrição.
- **Sobrepor os atributos do utilizador**. Reformat ou atribuir um valor a um atributo recolhido do utilizador. Por exemplo, se um utilizador introduzir o primeiro nome em todas as letras maiúsculas ou maiúsculas, pode formatar o nome apenas com a primeira letra capitalizada. 
<!-- - **Enrich user data**. Integrate with your external cloud systems that store user information to integrate them with the sign-up flow. For example, your API can receive the user's email address, query a CRM system, and return the user's loyalty number. Returned claims can be used to pre-fill form fields or return additional data in the application token.  -->
- **Executar lógica de negócio personalizada**. Pode desencadear eventos a jusante nos seus sistemas de nuvem para enviar notificações push, atualizar bases de dados corporativas, gerir permissões, auditar bases de dados e realizar outras ações personalizadas.

Um conector API representa um contrato entre a Azure AD e um ponto final da API, definindo o ponto final HTTP, autenticação, pedido e resposta esperada. Uma vez configurado um conector API, pode ative-lo para um passo específico no fluxo do utilizador. Quando um utilizador atinge esse passo no fluxo de inscrição, o conector API é invocado e materializa-se como um pedido HTTP POST, enviando reclamações selecionadas como pares de valor-chave num corpo JSON. A resposta da API pode afetar a execução do fluxo do utilizador. Por exemplo, a resposta da API pode bloquear a inscrição de um utilizador, pedir ao utilizador para reintrodutar informações, ou substituir e apendicar os atributos do utilizador.

## <a name="where-you-can-enable-an-api-connector-in-a-user-flow"></a>Onde pode ativar um conector API num fluxo de utilizador

Existem dois lugares num fluxo de utilizador onde pode ativar um conector API:

- Depois de iniciar sessão com um fornecedor de identidade
- Antes de criar o utilizador

Em ambos os casos, os conectores API são invocados durante a inscrição, não para iniciar sedutor.

### <a name="after-signing-in-with-an-identity-provider"></a>Depois de iniciar sessão com um fornecedor de identidade

Um conector API neste passo no processo de inscrição é invocado imediatamente após o utilizador autenticar com um fornecedor de identidade (Google, Facebook, Azure AD). Este passo antecede a ***página de recolha de atributos***, que é o formulário apresentado ao utilizador para recolher os atributos do utilizador. Seguem-se exemplos de cenários de conector API que poderá permitir neste passo:

- Utilize o e-mail ou identidade federada que o utilizador forneceu para procurar reclamações num sistema existente. Devolva estas reclamações do sistema existente, preencha a página de recolha de atributos e disponibilize-as para devolver no token.
- Validar se o utilizador está incluído numa lista de admissão ou negação e controlar se pode continuar com o fluxo de inscrição.

### <a name="before-creating-the-user"></a>Antes de criar o utilizador

Um conector API neste passo no processo de inscrição é invocado após a página de recolha do atributo, se um estiver incluído. Este passo é sempre invocado antes de uma conta de utilizador ser criada em Azure AD. Seguem-se exemplos de cenários que poderá permitir neste momento durante a inscrição:

- Valide os dados de entrada do utilizador e peça a um utilizador que reenvia os dados.
- Bloqueie uma inscrição de utilizador com base nos dados introduzidos pelo utilizador.
- Faça a prova de identidade.
- Consultar os sistemas externos para que os dados existentes sobre o utilizador o devolvam no token da aplicação ou os armazenem em Azure AD.

<!-- > [!IMPORTANT]
> If an invalid response is returned or another error occurs (for example, a network error), the user will be redirected to the app with the error re -->

## <a name="frequently-asked-questions-faq"></a>Perguntas Mais Frequentes (FAQ)

### <a name="how-do-i-integrate-with-an-existing-api-endpoint"></a>Como me integro com um ponto final da API existente?
Pode utilizar um [gatilho HTTP em Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook-trigger?tabs=csharp) como uma forma simples de ligar e invocar outras APIs web.

## <a name="next-steps"></a>Próximos passos
- Saiba como [adicionar um conector API a um fluxo de utilizador](self-service-sign-up-add-api-connector.md)
- Saiba como [adicionar um sistema de aprovação personalizado à inscrição de self-service](self-service-sign-up-add-approvals.md)
<!--#TODO: Make doc, link.-->
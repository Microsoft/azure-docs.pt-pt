---
title: Validar XML para integração empresarial B2B
description: Validar XML utilizando esquemas em Azure Logic Apps com Pacote de Integração Empresarial
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 10/21/2019
ms.openlocfilehash: ff21b059e712489c1914b2d12c6aa6a3d78d66d3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "74792159"
---
# <a name="validate-xml-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Validar o XML para a integração empresarial B2B no Azure Logic Apps com o Enterprise Integration Pack

Muitas vezes em cenários B2B, os parceiros comerciais num acordo precisam de se certificar de que as mensagens que trocam são válidas antes de qualquer processamento de dados poder começar. Pode validar documentos contra um esquema predefinido utilizando a ação de validação XML, que está disponível com o Pack de Integração Empresarial.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se ainda não tiver uma subscrição, [inscreva-se para uma conta Azure gratuita.](https://azure.microsoft.com/free/)

* Uma aplicação lógica em branco ou existente onde pretende utilizar a ação de validação XML. Se é novo em aplicações lógicas, [reveja o que é Azure Logic Apps](../logic-apps/logic-apps-overview.md) e [Quickstart: Crie a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Uma conta de [integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) associada à sua subscrição Azure, está ligada à aplicação lógica onde planeia utilizar a ação de validação XML, e contém o esquema que pretende utilizar para validar o conteúdo XML. Tanto a sua aplicação lógica como a sua conta de integração devem existir no mesmo local ou na região de Azure.

## <a name="add-xml-validation-action"></a>Adicionar ação de validação XML

1. No [portal Azure,](https://portal.azure.com)abra a sua aplicação lógica no Logic App Designer.

1. Se tiver uma aplicação lógica em branco, no Logic App Designer, na caixa de pesquisa, introduza `HTTP request` como filtro e selecione o gatilho **Quando um pedido HTTP é recebido.** Caso contrário, avance para o passo seguinte.

1. Sob o último passo do seu fluxo de trabalho, selecione **Novo passo**.

   Para adicionar uma ação entre os passos existentes, mova o ponteiro sobre a seta que liga esses passos de modo a que apareça o sinal de mais **+** ( ). Selecione o sinal mais e, em seguida, **selecione Adicione uma ação**.

1. Em **Escolha uma ação**, selecione **Built-in**. Na caixa de pesquisa, introduza `xml validation` como filtro. Na lista de ações, selecione **Validação XML.**

   ![Localizar e selecionar ação "Validação XML"](./media/logic-apps-enterprise-integration-xml-validation/select-xml-validation-action.png)

1. Para especificar o conteúdo XML que pretende validar, clique dentro da caixa **de Conteúdo** para que a lista de conteúdos dinâmicos apareça.

   ![Abrir lista de conteúdos dinâmicos](./media/logic-apps-enterprise-integration-xml-validation/open-dynamic-content-list.png)

   A lista de conteúdos dinâmicos mostra fichas de propriedade que representam as saídas dos passos anteriores no fluxo de trabalho. Se a lista não mostrar uma propriedade esperada, verifique o gatilho ou o título de ação se pode selecionar **Ver mais**.

1. A partir da lista de conteúdos dinâmicos, selecione o imóvel que tem o conteúdo que pretende validar.

   Este exemplo seleciona a saída do **Corpo** a partir do gatilho.

   ![Selecione conteúdo para validar](./media/logic-apps-enterprise-integration-xml-validation/select-content-to-validate.png)

1. Para especificar o esquema que pretende utilizar para validação, abra a lista **De Nome de Schema** e selecione o esquema de validação que adicionou à sua conta de integração ligada.

   ![Selecione esquema para usar para validação](./media/logic-apps-enterprise-integration-xml-validation/select-validation-schema.png)

1. Guarde a sua aplicação lógica.

   Já acabou de preparar a sua validação. Numa aplicação do mundo real, é possível que queira armazenar os dados validados numa aplicação de linha de negócios (LOB), como a SalesForce. Para enviar a saída validada para a Salesforce, adicione uma ação.

1. Para testar a sua ação de validação, pode enviar um pedido para desencadear o fluxo de trabalho da sua aplicação lógica.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre o [Pacote de Integração Empresarial](../logic-apps/logic-apps-enterprise-integration-overview.md)
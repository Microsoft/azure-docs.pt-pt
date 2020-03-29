---
title: Validar xML para integração empresarial B2B
description: Valide xML usando schemas em Aplicações lógicas Azure com pacote de integração empresarial
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 10/21/2019
ms.openlocfilehash: ff21b059e712489c1914b2d12c6aa6a3d78d66d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792159"
---
# <a name="validate-xml-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Valide xML para integração empresarial B2B em Aplicações Lógicas Azure com Pacote de Integração Empresarial

Muitas vezes, em cenários B2B, os parceiros comerciais num acordo precisam de se certificar de que as mensagens que trocam são válidas antes de qualquer processamento de dados poder começar. Pode validar documentos contra um esquema pré-definido utilizando a ação de validação XML, que está disponível com o Enterprise Integration Pack.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se ainda não tiver uma subscrição, [inscreva-se numa conta Azure gratuita.](https://azure.microsoft.com/free/)

* Uma aplicação lógica em branco ou existente onde pretende utilizar a ação de validação XML. Se você é novo em aplicações lógicas, reveja [o que são As Aplicações Lógicas Azure](../logic-apps/logic-apps-overview.md) e [Quickstart: Crie a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Uma conta de [integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) que está associada à sua subscrição Do Azure, está ligada à aplicação lógica onde planeia usar a ação de validação XML, e contém o esquema que pretende utilizar para validar conteúdos XML. Tanto a sua aplicação lógica como a sua conta de integração devem existir no mesmo local ou na região de Azure.

## <a name="add-xml-validation-action"></a>Adicionar ação de validação XML

1. No [portal Azure,](https://portal.azure.com)abra a sua aplicação lógica no Logic App Designer.

1. Se tiver uma aplicação lógica em branco, no Logic `HTTP request` App Designer, na caixa de pesquisa, introduza como filtro e selecione o **gatilho quando um pedido HTTP é recebido.** Caso contrário, continue para o próximo passo.

1. Sob o último passo do seu fluxo de trabalho, selecione **Novo passo**.

   Para adicionar uma ação entre os passos existentes, mova o ponteiro sobre**+** a seta que liga esses passos para que o sinal de mais () apareça. Selecione-o mais sinal e, em seguida, selecione **Adicionar uma ação**.

1. Em **'Escolha uma ação**', selecione **Incorporado**' . Na caixa de `xml validation` pesquisa, introduza como filtro. Na lista de ações, selecione **Validação XML**.

   ![Localizar e selecionar ação "Validação XML"](./media/logic-apps-enterprise-integration-xml-validation/select-xml-validation-action.png)

1. Para especificar o conteúdo XML que pretende validar, clique no interior da caixa **de conteúdo** para que a lista de conteúdos dinâmicos apareça.

   ![Lista de conteúdos dinâmicos abertos](./media/logic-apps-enterprise-integration-xml-validation/open-dynamic-content-list.png)

   A lista de conteúdos dinâmicos mostra fichas de propriedade que representam as saídas dos passos anteriores no fluxo de trabalho. Se a lista não mostrar uma propriedade esperada, verifique o gatilho ou a direção de ação se pode selecionar **Ver mais**.

1. A partir da lista de conteúdos dinâmicos, selecione a propriedade que tem o conteúdo que pretende validar.

   Este exemplo seleciona a saída **do Corpo** a partir do gatilho.

   ![Selecione conteúdo para validar](./media/logic-apps-enterprise-integration-xml-validation/select-content-to-validate.png)

1. Para especificar o esquema que pretende utilizar para validação, abra a lista **de Nomes Schema** e selecione o esquema de validação que adicionou à sua conta de integração ligada.

   ![Selecione esquema para usar para validação](./media/logic-apps-enterprise-integration-xml-validation/select-validation-schema.png)

1. Guarde a aplicação lógica.

   Já acabou de preparar a sua validação. Numa aplicação do mundo real, é possível que queira armazenar os dados validados numa aplicação de linha de negócio (LOB), como salesForce. Para enviar a saída validada para a Salesforce, adicione uma ação.

1. Para testar a sua ação de validação, pode enviar um pedido para desencadear o fluxo de trabalho da sua aplicação lógica.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre o [Pacote de Integração Empresarial](../logic-apps/logic-apps-enterprise-integration-overview.md)
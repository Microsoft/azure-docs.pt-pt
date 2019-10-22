---
title: Validar XML para integração de empresas B2B – aplicativos lógicos do Azure
description: Validar XML usando esquemas em aplicativos lógicos do Azure com Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/21/2019
ms.openlocfilehash: 7813dcb375ff4a123b1314f8f9db453b1f0b187e
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72680257"
---
# <a name="validate-xml-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Validar XML para integração de empresas B2B em aplicativos lógicos do Azure com Enterprise Integration Pack

Geralmente em cenários B2B, os parceiros comerciais em um contrato precisam garantir que as mensagens trocadas sejam válidas antes que qualquer processamento de dados possa ser iniciado. Você pode validar documentos em um esquema predefinido usando a ação validação de XML, que está disponível com o Enterprise Integration Pack.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se você ainda não tiver uma assinatura, [Inscreva-se para obter uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Um aplicativo lógico em branco ou existente no qual você deseja usar a ação de validação de XML. Se você for novo em aplicativos lógicos, examine [o que é o início rápido e aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md) [: Crie seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) associada à sua assinatura do Azure é vinculada ao aplicativo lógico no qual você planeja usar a ação de validação de XML e contém o esquema que você deseja usar para validar o conteúdo XML. O aplicativo lógico e a conta de integração devem existir no mesmo local ou na região do Azure.

## <a name="add-xml-validation-action"></a>Adicionar ação de validação de XML

1. No [portal do Azure](https://portal.azure.com), abra seu aplicativo lógico no designer do aplicativo lógico.

1. Se você tiver um aplicativo lógico em branco, no designer do aplicativo lógico, na caixa de pesquisa, insira `HTTP request` como filtro e selecione o gatilho **quando uma solicitação HTTP é recebida** . Caso contrário, prossiga para a próxima etapa.

1. Na última etapa do fluxo de trabalho, selecione **nova etapa**.

   Para adicionar uma ação entre as etapas existentes, mova o ponteiro sobre a seta que conecta essas etapas para que o sinal de adição ( **+** ) seja exibido. Selecione esse sinal de adição e, em seguida, selecione **Adicionar uma ação**.

1. Em **escolher uma ação**, selecione **interno**. Na caixa de pesquisa, insira `xml validation` como seu filtro. Na lista ações, selecione **validação de XML**.

   ![Localizar e selecionar a ação "validação de XML"](./media/logic-apps-enterprise-integration-xml-validation/select-xml-validation-action.png)

1. Para especificar o conteúdo XML que você deseja validar, clique dentro da caixa de **conteúdo** para que a lista de conteúdo dinâmico seja exibida.

   ![Abrir lista de conteúdo dinâmico](./media/logic-apps-enterprise-integration-xml-validation/open-dynamic-content-list.png)

   A lista de conteúdo dinâmico mostra os tokens de propriedade que representam as saídas das etapas anteriores no fluxo de trabalho. Se a lista não mostrar uma propriedade esperada, verifique o gatilho ou o cabeçalho da ação se você puder selecionar **Ver mais**.

1. Na lista conteúdo dinâmico, selecione a propriedade que tem o conteúdo que você deseja validar.

   Este exemplo seleciona a saída do **corpo** do gatilho.

   ![Selecione o conteúdo a ser validado](./media/logic-apps-enterprise-integration-xml-validation/select-content-to-validate.png)

1. Para especificar o esquema que você deseja usar para validação, abra a lista **nome do esquema** e selecione o esquema de validação que você adicionou à sua conta de integração vinculada.

   ![Selecione o esquema a ser usado para validação](./media/logic-apps-enterprise-integration-xml-validation/select-validation-schema.png)

1. Guarde a aplicação lógica.

   Agora você concluiu a configuração de sua validação. Em um aplicativo do mundo real, talvez você queira armazenar os dados validados em um aplicativo de linha de negócios (LOB), como o SalesForce. Para enviar a saída validada para o Salesforce, adicione uma ação.

1. Para testar sua ação de validação, você pode enviar uma solicitação para disparar o fluxo de trabalho do aplicativo lógico.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre o [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
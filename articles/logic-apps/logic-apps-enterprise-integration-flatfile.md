---
title: Codificar ou descodificar ficheiros planos
description: Codificar ou descodificar ficheiros planos para integração empresarial em Azure Logic Apps utilizando o Pacote de Integração Empresarial
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 05/09/2020
ms.openlocfilehash: aebce8f284ed4bb21d99efffc8dd6d0c51b39533
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87001490"
---
# <a name="encode-and-decode-flat-files-in-azure-logic-apps-by-using-the-enterprise-integration-pack"></a>Codificar e descodificar ficheiros simples no Azure Logic Apps através do Enterprise Integration Pack

Antes de enviar conteúdo XML a um parceiro de negócios num cenário de negócio-a-negócio (B2B), talvez queira codificar esse conteúdo primeiro. Ao construir uma aplicação lógica, pode codificar e descodificar ficheiros planos utilizando o conector **Ficheiro Plano.** A sua aplicação lógica pode obter este conteúdo XML de várias fontes, tais como o gatilho Request, outra app ou [outros conectores suportados por Azure Logic Apps](../connectors/apis-list.md). Para mais informações, consulte [O que é Azure Logic Apps?](logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, [inscreva-se numa conta do Azure gratuita](https://azure.microsoft.com/free/).

* A aplicação lógica onde pretende utilizar o conector **De Ficheiro Plano** e um gatilho que inicia o fluxo de trabalho da sua aplicação lógica. O conector **De Ficheiro Plano** fornece apenas ações, não gatilhos. Pode utilizar o gatilho ou outra ação para alimentar o conteúdo XML na sua aplicação lógica para codificar ou descodimentar. Se é novo em aplicações lógicas, reveja [Quickstart: Crie a sua primeira aplicação lógica.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Uma conta de [integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) associada à sua subscrição Azure e [ligada à aplicação lógica](./logic-apps-enterprise-integration-create-integration-account.md#link-account) onde planeia utilizar o conector De Ficheiro **Plano.** Tanto a sua aplicação lógica como a sua conta de integração devem existir no mesmo local ou na região de Azure.

* Um [esquema de](logic-apps-enterprise-integration-schemas.md) ficheiro plano que carregou na sua conta de integração para codificar ou descodibilar o conteúdo do XML

* Pelo menos dois [parceiros comerciais](logic-apps-enterprise-integration-partners.md) que já definiu na sua conta de integração

## <a name="add-flat-file-encode-action"></a>Adicionar a ação de codificação de ficheiro simples

1. No [portal Azure,](https://portal.azure.com)abra a sua aplicação lógica no Logic App Designer.

1. Sob o gatilho ou ação na sua aplicação lógica, selecione **Novo passo** Adicione  >  **uma ação**. Este exemplo utiliza o gatilho 'Pedido', que é denominado **Quando um pedido HTTP é recebido**, e trata de pedidos de entrada de fora da aplicação lógica.

   > [!TIP]
   > Desde que um esquema JSON seja opcional. Se tiver uma carga útil de amostra a partir do pedido de entrada, selecione Utilize a carga útil da **amostra para gerar esquema,** introduza a carga útil da amostra e selecione 'Fazer ' **'Fazer'.** O esquema aparece na caixa **de esquema do Corpo de Pedido JSON.**

1. Em **Escolha uma ação,** insira `flat file` . A partir da lista de ações, selecione esta ação: **Codificação de ficheiros planos**

   ![Selecione ação "Codificação de ficheiros planos"](./media/logic-apps-enterprise-integration-flatfile/select-flat-file-encoding.png)

1. Clique dentro da caixa **de conteúdo** para que a lista de conteúdos dinâmicos apareça. A partir da lista, na secção **Quando é recebido um pedido HTTP,** selecione a propriedade **Body,** que contém a saída do corpo de pedido a partir do gatilho e do conteúdo para codificar.

   ![Selecione conteúdo para codificar a partir da lista de conteúdos dinâmicos](./media/logic-apps-enterprise-integration-flatfile/select-content-to-encode.png)

   > [!TIP]
   > Se não vir a propriedade **Body** na lista de conteúdos dinâmicos, selecione **Ver mais** ao lado da etiqueta de secção Quando receber um **pedido HTTP.**
   > Também pode introduzir diretamente o conteúdo para descodificar na caixa **de Conteúdo.**

1. Na lista **de Nomes De Schema,** selecione o esquema que está na sua conta de integração ligada para usar para codificação, por exemplo:

   ![Selecione esquema para usar para codificar](./media/logic-apps-enterprise-integration-flatfile/select-schema-for-encoding.png)

   > [!NOTE]
   > Se não aparecer nenhum esquema na lista, a sua conta de integração não contém nenhum ficheiro de esquema para usar para codificação. Faça o upload do esquema que pretende utilizar na sua conta de integração.

1. Guarde a sua aplicação lógica. Para testar o seu conector, faça um pedido ao ponto final HTTPS, que aparece na propriedade **HTTP POST URL** do pedido, e inclua o conteúdo XML que pretende codificar no organismo de pedido.

Já acabou de configurar a sua ação de codificação de ficheiros planos. Numa aplicação do mundo real, é possível que queira armazenar os dados codificados numa aplicação de linha de negócios (LOB), como a Salesforce. Ou pode enviar os dados codificados para um parceiro comercial. Para enviar a saída da ação de codificação para a Salesforce ou para o seu parceiro comercial, utilize os [outros conectores disponíveis nas Azure Logic Apps](../connectors/apis-list.md).

## <a name="add-flat-file-decode-action"></a>Adicionar a ação de descodificação de ficheiros simples

1. No [portal Azure,](https://portal.azure.com)abra a sua aplicação lógica no Logic App Designer.

1. Sob o gatilho ou ação na sua aplicação lógica, selecione **Novo passo** Adicione  >  **uma ação**. Este exemplo utiliza o gatilho 'Pedido', que é denominado **Quando um pedido HTTP é recebido**, e trata de pedidos de entrada de fora da aplicação lógica.

   > [!TIP]
   > Desde que um esquema JSON seja opcional. Se tiver uma carga útil de amostra a partir do pedido de entrada, selecione Utilize a carga útil da **amostra para gerar esquema,** introduza a carga útil da amostra e selecione 'Fazer ' **'Fazer'.** O esquema aparece na caixa **de esquema do Corpo de Pedido JSON.**

1. Em **Escolha uma ação,** insira `flat file` . A partir da lista de ações, selecione esta ação: **Descodamento de ficheiros planos**

   ![Selecione ação "Flat File Decoding"](./media/logic-apps-enterprise-integration-flatfile/select-flat-file-decoding.png)

1. Clique dentro da caixa **de conteúdo** para que a lista de conteúdos dinâmicos apareça. A partir da lista, na secção **Quando é recebido um pedido HTTP,** selecione a propriedade **Body,** que contém a saída do corpo de pedido do gatilho e o conteúdo para descodificar.

   ![Selecione conteúdo para descodificar a partir da lista de conteúdos dinâmicos](./media/logic-apps-enterprise-integration-flatfile/select-content-to-decode.png)

   > [!TIP]
   > Se não vir a propriedade **Body** na lista de conteúdos dinâmicos, selecione **Ver mais** ao lado da etiqueta de secção Quando receber um **pedido HTTP.** Também pode introduzir diretamente o conteúdo para descodificar na caixa **de Conteúdo.**

1. Na lista **de Nomes De Schema,** selecione o esquema que está na sua conta de integração ligada para usar para descodição, por exemplo:

   ![Selecione esquema para usar para descodição](./media/logic-apps-enterprise-integration-flatfile/select-schema-for-decoding.png)

   > [!NOTE]
   > Se não aparecer nenhum esquema na lista, a sua conta de integração não contém nenhum ficheiro de esquema para descodição. Faça o upload do esquema que pretende utilizar na sua conta de integração.

1. Guarde a sua aplicação lógica. Para testar o seu conector, faça um pedido ao ponto final HTTPS, que aparece na propriedade **HTTP POST URL** do pedido, e inclua o conteúdo XML que pretende descodificar no organismo de pedido.

Já acabou de configurar a sua ação de descodição de ficheiros planos. Numa aplicação do mundo real, é possível que queira armazenar os dados descodificados numa aplicação de linha de negócios (LOB), como a Salesforce. Ou pode enviar os dados descodificados a um parceiro comercial. Para enviar a saída da ação de descodição para a Salesforce ou para o seu parceiro comercial, utilize os [outros conectores disponíveis nas Azure Logic Apps](../connectors/apis-list.md).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre o [Pacote de Integração Empresarial](logic-apps-enterprise-integration-overview.md)

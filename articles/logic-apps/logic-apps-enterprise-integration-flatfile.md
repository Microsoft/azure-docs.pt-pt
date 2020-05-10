---
title: Codificar ou descodificar ficheiros planos
description: Codificar ou descodificar ficheiros planos para integração empresarial em Aplicações Lógicas Azure utilizando o Pacote de Integração Empresarial
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 05/09/2020
ms.openlocfilehash: 81c1c95e2af7b537a12c8c86245b009005aa0aa2
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/10/2020
ms.locfileid: "83005290"
---
# <a name="encode-and-decode-flat-files-in-azure-logic-apps-by-using-the-enterprise-integration-pack"></a>Codificar e descodificar ficheiros planos em Aplicações Lógicas Azure utilizando o Pacote de Integração Empresarial

Antes de enviar conteúdo XML a um parceiro de negócios num cenário de negócio-a-negócio (B2B), talvez queira codificar esse conteúdo primeiro. Ao criar uma aplicação lógica, pode codificar e descodificar ficheiros planos utilizando o conector **Flat File.** A sua aplicação lógica pode obter este conteúdo XML de várias fontes, como o gatilho do Pedido, outra aplicação ou outros [conectores suportados por Aplicações Lógicas Azure](../connectors/apis-list.md). Para mais informações, consulte [O que são As Aplicações Lógicas Azure?](logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, [inscreva-se numa conta do Azure gratuita](https://azure.microsoft.com/free/).

* A aplicação lógica onde pretende utilizar o conector **Flat File** e um gatilho que inicia o fluxo de trabalho da sua aplicação lógica. O conector **De Ficheiro Plano** fornece apenas ações, não gatilhos. Pode utilizar o gatilho ou outra ação para alimentar o conteúdo xml na sua aplicação lógica para codificar ou descodificar. Se é novo em aplicações lógicas, reveja [Quickstart: Crie a sua primeira aplicação lógica.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Uma conta de [integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) que está associada à sua subscrição Azure e [ligada à aplicação lógica](logic-apps-enterprise-integration-accounts.md#link-account) onde planeia utilizar o conector Flat **File.** Tanto a sua aplicação lógica como a sua conta de integração devem existir no mesmo local ou na região de Azure.

* Um [esquema](logic-apps-enterprise-integration-schemas.md) de ficheiro plano que fez o upload para a sua conta de integração para codificar ou descodificar o conteúdo XML

* Pelo menos dois [parceiros comerciais](logic-apps-enterprise-integration-partners.md) que já definiu na sua conta de integração

## <a name="add-flat-file-encode-action"></a>Adicionar ação de codificação de ficheiro plano

1. No [portal Azure,](https://portal.azure.com)abra a sua aplicação lógica no Logic App Designer.

1. Sob o gatilho ou ação na sua aplicação lógica, selecione **Novo passo** > **Adicione uma ação**. Este exemplo utiliza o gatilho Request, que se chama **Quando um pedido HTTP é recebido**, e lida com pedidos de entrada de fora da aplicação lógica.

   > [!TIP]
   > Fornecer um esquema JSON é opcional. Se tiver uma carga útil da amostra a partir do pedido de entrada, selecione Utilize a carga útil da **amostra para gerar esquema,** introduza a carga útil da amostra e selecione **Done**. O esquema aparece na caixa **do Corpo de Pedidos JSON Schema.**

1. Em **'Escolha uma ação,'** entre . `flat file` Da lista de ações, selecione esta ação: **Flat File Encoding**

   ![Selecione ação "Codificação de ficheiros planos"](./media/logic-apps-enterprise-integration-flatfile/select-flat-file-encoding.png)

1. Clique na caixa **de conteúdo** para que a lista de conteúdos dinâmicos apareça. A partir da lista, na secção **Quando um pedido HTTP é recebido,** selecione a propriedade Do **Corpo,** que contém a saída do corpo de pedido a partir do gatilho e o conteúdo para codificar.

   ![Selecione conteúdo para codificar a partir da lista de conteúdos dinâmicos](./media/logic-apps-enterprise-integration-flatfile/select-content-to-encode.png)

   > [!TIP]
   > Se não vir a propriedade **Do Corpo** na lista de conteúdos dinâmicos, selecione Ver **mais ao** lado do pedido Quando um pedido HTTP **é recebido** etiqueta de secção.
   > Também pode introduzir diretamente o conteúdo para descodificar na caixa **de Conteúdo.**

1. A partir da lista **de nomes schema,** selecione o esquema que está na sua conta de integração ligada para usar para codificar, por exemplo:

   ![Selecione esquema para usar para codificar](./media/logic-apps-enterprise-integration-flatfile/select-schema-for-encoding.png)

   > [!NOTE]
   > Se não aparecer nenhum esquema na lista, a sua conta de integração não contém quaisquer ficheiros de esquemas para codificar. Faça upload do esquema que pretende utilizar na sua conta de integração.

1. Guarde a aplicação lógica. Para testar o seu conector, faça um pedido ao ponto final HTTPS, que aparece na propriedade **HTTP POST URL** do gatilho do Pedido, e inclua o conteúdo XML que pretende codificar no organismo de pedido.

Já acabou de montar a sua ação de codificação de ficheiros planos. Numa aplicação do mundo real, é possível que queira armazenar os dados codificados numa aplicação de linha de negócios (LOB), como a Salesforce. Ou pode enviar os dados codificados a um parceiro comercial. Para enviar a saída da ação de codificação para a Salesforce ou para o seu parceiro comercial, utilize os outros [conectores disponíveis nas Aplicações lógicas do Azure](../connectors/apis-list.md).

## <a name="add-flat-file-decode-action"></a>Adicionar ação de descodificação de ficheiro plano

1. No [portal Azure,](https://portal.azure.com)abra a sua aplicação lógica no Logic App Designer.

1. Sob o gatilho ou ação na sua aplicação lógica, selecione **Novo passo** > **Adicione uma ação**. Este exemplo utiliza o gatilho Request, que se chama **Quando um pedido HTTP é recebido**, e lida com pedidos de entrada de fora da aplicação lógica.

   > [!TIP]
   > Fornecer um esquema JSON é opcional. Se tiver uma carga útil da amostra a partir do pedido de entrada, selecione Utilize a carga útil da **amostra para gerar esquema,** introduza a carga útil da amostra e selecione **Done**. O esquema aparece na caixa **do Corpo de Pedidos JSON Schema.**

1. Em **'Escolha uma ação,'** entre . `flat file` Da lista de ações, selecione esta ação: **Descodificação de Ficheiros Planos**

   ![Selecione ação "Descodificação de Ficheiros Planos"](./media/logic-apps-enterprise-integration-flatfile/select-flat-file-decoding.png)

1. Clique na caixa **de conteúdo** para que a lista de conteúdos dinâmicos apareça. A partir da lista, na secção **Quando um pedido HTTP é recebido,** selecione a propriedade Do **Corpo,** que contém a saída do corpo de pedido do gatilho e o conteúdo para descodificar.

   ![Selecione conteúdo para descodificar da lista de conteúdos dinâmicos](./media/logic-apps-enterprise-integration-flatfile/select-content-to-decode.png)

   > [!TIP]
   > Se não vir a propriedade **Do Corpo** na lista de conteúdos dinâmicos, selecione Ver **mais ao** lado do pedido Quando um pedido HTTP **é recebido** etiqueta de secção. Também pode introduzir diretamente o conteúdo para descodificar na caixa **de Conteúdo.**

1. A partir da lista **de nomes schema,** selecione o esquema que está na sua conta de integração ligada para usar para descodificar, por exemplo:

   ![Selecione esquema para usar para descodificar](./media/logic-apps-enterprise-integration-flatfile/select-schema-for-decoding.png)

   > [!NOTE]
   > Se não aparecer nenhum esquema na lista, a sua conta de integração não contém ficheiros esquemas para descodificar. Faça upload do esquema que pretende utilizar na sua conta de integração.

1. Guarde a aplicação lógica. Para testar o seu conector, faça um pedido ao ponto final HTTPS, que aparece na propriedade **HTTP POST URL** do gatilho do Pedido, e inclua o conteúdo XML que pretende descodificar no organismo de pedido.

Já acabou de montar a sua ação de descodificação de ficheiros planos. Numa aplicação do mundo real, é possível que queira armazenar os dados descodificados numa aplicação de linha de negócios (LOB), como a Salesforce. Ou pode enviar os dados descodificados a um parceiro comercial. Para enviar a saída da ação de descodificação para a Salesforce ou para o seu parceiro de negociação, utilize os outros [conectores disponíveis nas Aplicações lógicas do Azure](../connectors/apis-list.md).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre o [Pacote de Integração Empresarial](logic-apps-enterprise-integration-overview.md)
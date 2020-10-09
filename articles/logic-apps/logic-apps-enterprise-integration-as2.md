---
title: Enviar e receber mensagens AS2 para B2B
description: Trocar mensagens AS2 para cenários de integração empresarial B2B utilizando apps Azure Logic com Pacote de Integração Empresarial
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 10/08/2020
ms.openlocfilehash: 1845de8332287f85e96b4e29c40caa66cb8704d9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2020
ms.locfileid: "91856675"
---
# <a name="exchange-as2-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Trocar mensagens AS2 para a integração empresarial B2B no Azure Logic Apps com o Enterprise Integration Pack

> [!IMPORTANT]
> O conector AS2 original está a ser depreciado, por isso, a menos que precise de capacidades de rastreio, utilize o conector **AS2 (v2).** Com exceção do rastreio, o conector V2 fornece as mesmas capacidades que a versão original, é nativo do tempo de funcionaamento das Aplicações Lógicas, e tem melhorias significativas no desempenho no tamanho, produção e latência da mensagem. Além disso, o conector V2 não requer que crie uma ligação à sua conta de integração. Em vez disso, conforme descrito nos pré-requisitos, certifique-se de que liga a sua conta de integração à aplicação lógica onde planeia utilizar o conector.

Para trabalhar com mensagens AS2 em Azure Logic Apps, pode utilizar o conector AS2, que fornece gatilhos e ações que suportam e gerem a comunicação AS2 (versão 1.2). Por exemplo, para estabelecer segurança e fiabilidade ao transmitir mensagens, pode utilizar estas ações:

* [ **As2 Codificam** a ação](#encode) para fornecer encriptação, assinatura digital e reconhecimentos através de Notificações de Disposição de Mensagens (MDN), que ajudam a suportar o não-repúdio. Por exemplo, esta ação aplica cabeçalhos AS2/HTTP e executa estas tarefas quando configuradas:

  * Assina mensagens de saída.
  * Encripta mensagens de saída.
  * Comprime a mensagem.
  * Transmite o nome do ficheiro no cabeçalho MIME.

* [ **As2 Descodificar** ação](#decode) para fornecer desencriptação, assinatura digital e reconhecimentos através de Notificações de Disposição de Mensagens (MDN). Por exemplo, esta ação executa estas tarefas:

  * Processos cabeçalhos AS2/HTTP.
  * A Reconciles recebeu MDNs com as mensagens originais de saída.
  * Atualizações e correlações de registos na base de dados de não-repúdio.
  * Escreve registos para relatórios de estado as2.
  * Outputs conteúdo de carga útil como base64 codificado.
  * Determina se os MDNs são necessários. Com base no acordo AS2, determina se as MDNs devem ser sincronizadas ou assíncronas.
  * Gera MDNs sincronizados ou assíncronos com base no acordo AS2.
  * Define os tokens de correlação e propriedades em MDNs.

  Esta ação também executa estas tarefas quando configurada:

  * Verifica a assinatura.
  * Desencripta as mensagens.
  * Descomprimia a mensagem.
  * Verifique e desesprova duplicar o ID da mensagem.

Este artigo mostra como adicionar as ações de codificação e descodamento do AS2 a uma aplicação lógica existente.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, [inscreva-se para uma conta Azure gratuita.](https://azure.microsoft.com/free/)

* A aplicação lógica de onde pretende utilizar o conector AS2 e um gatilho que inicia o fluxo de trabalho da sua aplicação lógica. O conector AS2 fornece apenas ações, não gatilhos. Se é novo em aplicações lógicas, [reveja o que é Azure Logic Apps](../logic-apps/logic-apps-overview.md) e [Quickstart: Crie a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Uma conta de [integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) associada à sua subscrição Azure e ligada à aplicação lógica onde planeia utilizar o conector AS2. Tanto a sua aplicação lógica como a sua conta de integração devem existir no mesmo local ou na região de Azure.

* Pelo menos dois [parceiros comerciais](../logic-apps/logic-apps-enterprise-integration-partners.md) que já definiu na sua conta de integração usando o qualificador de identidade AS2.

* Antes de poder utilizar o conector AS2, tem de criar um [acordo](../logic-apps/logic-apps-enterprise-integration-agreements.md) AS2 entre os seus parceiros comerciais e armazenar esse acordo na sua conta de integração.

* Se utilizar [o Cofre da Chave Azure](../key-vault/general/overview.md) para a gestão do certificado, verifique se as chaves do cofre permitem as operações de **Encriptação** e **Desencriptação.** Caso contrário, as ações de codificação e descodão falham.

  No portal Azure, vá à chave do cofre, reveja as **operações permitidas**da sua chave e confirme que as operações **de Encriptação** e **Desencriptação** estão selecionadas, por exemplo:

  ![Verifique as operações da chave do cofre](media/logic-apps-enterprise-integration-as2/key-vault-permitted-operations.png)

<a name="encode"></a>

## <a name="encode-as2-messages"></a>Codificar mensagens AS2

1. Se ainda não o fez, no [portal Azure,](https://portal.azure.com)abra a sua aplicação lógica no Logic App Designer.

1. No designer, adicione uma nova ação à sua aplicação lógica.

1. Em **Escolha uma ação** e a caixa de pesquisa, selecione **All**. Na caixa de pesquisa, insira "código as2", e certifique-se de que seleciona a ação AS2 (v2): **AS2 Codificação**

   ![Selecione "AS2 Encode"](./media/logic-apps-enterprise-integration-as2/select-as2-encode.png)

1. Agora forneça informações sobre estas propriedades:

   | Propriedade | Descrição |
   |----------|-------------|
   | **Mensagem para codificar** | A carga da mensagem |
   | **AS2 de** | O identificador para o remetente de mensagens conforme especificado pelo seu acordo AS2 |
   | **AS2 para** | O identificador do recetor de mensagens especificado no seu acordo AS2 |
   |||

   Por exemplo:

   ![Propriedades de codificação de mensagens](./media/logic-apps-enterprise-integration-as2/as2-message-encoding-details.png)

> [!TIP]
> Se tiver problemas ao enviar mensagens assinadas ou encriptadas, considere experimentar diferentes formatos de algoritmo SHA256. A especificação AS2 não fornece nenhuma informação sobre os formatos SHA256, pelo que cada fornecedor utiliza a sua própria implementação ou formato.

<a name="decode"></a>

## <a name="decode-as2-messages"></a>Descodificar mensagens AS2

1. Se ainda não o fez, no [portal Azure,](https://portal.azure.com)abra a sua aplicação lógica no Logic App Designer.

1. No designer, adicione uma nova ação à sua aplicação lógica.

1. Em **Escolha uma ação** e a caixa de pesquisa, selecione **All**. Na caixa de pesquisa, introduza "descodificar as2", e certifique-se de que seleciona a ação AS2 (v2): **AS2 Descodificar**

   ![Selecione "DEscodificar AS2"](media/logic-apps-enterprise-integration-as2/select-as2-decode.png)

1. Para que a **Mensagem codifica** e as propriedades dos **cabeçalhos de mensagem,** selecione estes valores a partir de saídas anteriores do gatilho ou de ação.

   Por exemplo, suponha que a sua aplicação lógica receba mensagens através de um gatilho Do Pedido. Pode selecionar as saídas a partir do gatilho.

   ![Selecione Corpo e Cabeçalhos a partir de saídas de pedido](media/logic-apps-enterprise-integration-as2/as2-message-decoding-details.png)

## <a name="sample"></a>Sample

Para tentar implementar uma aplicação lógica totalmente operacional e experimentar o cenário AS2, consulte o [modelo e cenário da aplicação lógica AS2.](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/)

## <a name="connector-reference"></a>Referência do conector

Para obter mais detalhes técnicos sobre este conector, tais como ações e limites descritos pelo ficheiro Swagger do conector, consulte a [página de referência do conector](/connectors/as2/). 

> [!NOTE]
> Para aplicações lógicas num ambiente de serviço de [integração (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)esta versão com rótulo ISE deste conector utiliza os limites de [mensagem B2B para ISE](../logic-apps/logic-apps-limits-and-config.md#b2b-protocol-limits).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [outros conectores de Apps Lógicas](../connectors/apis-list.md)

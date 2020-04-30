---
title: Enviar e receber mensagens AS2 para B2B
description: Trocar mensagens AS2 para cenários de integração empresarial B2B utilizando aplicações da Azure Logic com pacote de integração empresarial
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/27/2020
ms.openlocfilehash: 545c1720ef379ec74bd2e7c0bc68f6a2fcbba789
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82115504"
---
# <a name="exchange-as2-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Trocar mensagens AS2 para integração empresarial B2B em Aplicações Lógicas Azure com Pacote de Integração Empresarial

> [!IMPORTANT]
> O conector AS2 original está a ser depreciado, por isso certifique-se de que utiliza o conector **AS2 (v2).** Esta versão fornece as mesmas capacidades que a versão original, é nativa do tempo de execução das Aplicações Lógicas, e fornece melhorias significativas de desempenho em termos de tamanho de entrada e mensagem. Além disso, o conector v2 nativo não requer que crie uma ligação com a sua conta de integração. Em vez disso, conforme descrito nos pré-requisitos, certifique-se de que liga a sua conta de integração à aplicação lógica onde planeia utilizar o conector.

Para trabalhar com mensagens AS2 em Aplicações Lógicas Azure, pode utilizar o conector AS2, que fornece gatilhos e ações para gerir a comunicação AS2. Por exemplo, para estabelecer segurança e fiabilidade ao transmitir mensagens, pode utilizar estas ações:

* [ **As2 Encode** ação](#encode) para fornecer encriptação, assinatura digital e reconhecimentos através de Notificações de Disposição de Mensagens (MDN), que ajudam a apoiar o não-repúdio. Por exemplo, esta ação aplica cabeçalhos AS2/HTTP e executa estas tarefas quando configuradas:

  * Assina mensagens de saída.
  * Encripta mensagens de saída.
  * Comprime a mensagem.
  * Transmite o nome de ficheiro no cabeçalho MIME.

* [ **Ação AS2 Descodificação** ](#decode) para fornecer desencriptação, assinatura digital e reconhecimentoatravés de Notificações de Disposição de Mensagens (MDN). Por exemplo, esta ação executa estas tarefas:

  * Processa cabeçalhos AS2/HTTP.
  * Reconciliações receberam MDNs com as mensagens de saída originais.
  * Atualizações e correlacionar registos na base de dados de não-repússe.
  * Escreve registos de relatórios de estado AS2.
  * Saídas de carga útil como base64 codificada.
  * Determina se os MDNs são necessários. Com base no acordo AS2, determina se os MDNs devem ser sincronizados ou assíncronos.
  * Gera MDNs sincronizados ou assíncronos com base no acordo AS2.
  * Define as fichas e propriedades da correlação em MDNs.

  Esta ação também executa estas tarefas quando configuradas:

  * Verifica a assinatura.
  * Desencripta as mensagens.
  * Descomprime a mensagem.
  * Verifique e não permita duplicados de id de mensagem.

Este artigo mostra como adicionar as ações de codificação e descodificação as as2 a uma aplicação lógica existente.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se ainda não tiver uma assinatura Azure, [inscreva-se numa conta Azure gratuita.](https://azure.microsoft.com/free/)

* A aplicação lógica de onde pretende utilizar o conector AS2 e um gatilho que inicia o fluxo de trabalho da sua aplicação lógica. O conector AS2 fornece apenas ações, não gatilhos. Se você é novo em aplicações lógicas, reveja [o que são As Aplicações Lógicas Azure](../logic-apps/logic-apps-overview.md) e [Quickstart: Crie a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Uma conta de [integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) que está associada à sua subscrição Azure e ligada à aplicação lógica onde planeia utilizar o conector AS2. Tanto a sua aplicação lógica como a sua conta de integração devem existir no mesmo local ou na região de Azure.

* Pelo menos dois [parceiros comerciais](../logic-apps/logic-apps-enterprise-integration-partners.md) que já definiu na sua conta de integração utilizando o qualificador de identidade AS2.

* Antes de poder utilizar o conector AS2, deve criar um [acordo](../logic-apps/logic-apps-enterprise-integration-agreements.md) AS2 entre os seus parceiros comerciais e armazenar esse acordo na sua conta de integração.

* Se utilizar o [Cofre de Chaves Azure](../key-vault/general/overview.md) para gestão de certificados, verifique se as chaves do cofre permitem as operações **de Encriptação** e **Desencriptação.** Caso contrário, as ações de codificação e dedescodificação falham.

  No portal Azure, vá à chave do seu cofre chave, reveja as **operações permitidas**da sua chave, e confirme que as operações **de Encriptação** e **Desencriptação** são selecionadas, por exemplo:

  ![Verifique as operações chave do cofre](media/logic-apps-enterprise-integration-as2/key-vault-permitted-operations.png)

<a name="encode"></a>

## <a name="encode-as2-messages"></a>Codificar mensagens AS2

1. Se ainda não o fez, no [portal Azure,](https://portal.azure.com)abra a sua aplicação lógica no Logic App Designer.

1. No designer, adicione uma nova ação à sua aplicação lógica.

1. Em **'Escolha uma ação'** e a caixa de pesquisa, selecione **All**. Na caixa de pesquisa, introduza "as2 codificação", e certifique-se de que seleciona a ação AS2 (v2): **AS2 Encode**

   ![Selecione "AS2 Encode"](./media/logic-apps-enterprise-integration-as2/select-as2-encode.png)

1. Agora forneça informações sobre estas propriedades:

   | Propriedade | Descrição |
   |----------|-------------|
   | **Mensagem para codificar** | A carga da mensagem |
   | **AS2 de** | O identificador para o remetente de mensagem especificado pelo seu acordo AS2 |
   | **AS2 para** | O identificador para o recetor de mensagens especificado pelo seu acordo AS2 |
   |||

   Por exemplo:

   ![Propriedades de codificação de mensagens](./media/logic-apps-enterprise-integration-as2/as2-message-encoding-details.png)

> [!TIP]
> Se tiver problemas ao enviar mensagens assinadas ou encriptadas, considere experimentar diferentes formatos de algoritmoSHA256. A especificação AS2 não fornece qualquer informação sobre os formatos SHA256, pelo que cada fornecedor utiliza a sua própria implementação ou formato.

<a name="decode"></a>

## <a name="decode-as2-messages"></a>Descodificar mensagens AS2

1. Se ainda não o fez, no [portal Azure,](https://portal.azure.com)abra a sua aplicação lógica no Logic App Designer.

1. No designer, adicione uma nova ação à sua aplicação lógica.

1. Em **'Escolha uma ação'** e a caixa de pesquisa, selecione **All**. Na caixa de pesquisa, introduza "as2 descodificar", e certifique-se de que seleciona a ação AS2 (v2): **AS2 Decode**

   ![Selecione "AS2 Decode"](media/logic-apps-enterprise-integration-as2/select-as2-decode.png)

1. Para que a **Mensagem codificasse** e as propriedades dos **cabeçalhos da Mensagem,** selecione estes valores a partir de saídas de gatilho ou de ação anteriores.

   Por exemplo, suponha que a sua aplicação lógica receba mensagens através de um gatilho de Pedido. Pode selecionar as saídas a partir desse gatilho.

   ![Selecione Corpo e Cabeçalhos a partir de saídas de Pedido](media/logic-apps-enterprise-integration-as2/as2-message-decoding-details.png)

## <a name="sample"></a>Sample

Para tentar implementar uma aplicação lógica totalmente operacional e experimentar o cenário AS2, consulte o modelo e cenário da [aplicação lógica AS2](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/).

## <a name="connector-reference"></a>Referência do conector

Para obter mais detalhes técnicos sobre este conector, tais como ações e limites descritos pelo ficheiro Swagger do conector, consulte a [página de referência do conector](https://docs.microsoft.com/connectors/as2/). 

> [!NOTE]
> Para aplicações lógicas num ambiente de serviço de [integração (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)a versão com o rótulo ISE do conector utiliza os limites de [mensagem B2B para](../logic-apps/logic-apps-limits-and-config.md#b2b-protocol-limits)o ISE .

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [conectores de Aplicações Lógicas](../connectors/apis-list.md)

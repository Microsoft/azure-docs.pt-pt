---
title: Nas mensagens AS2 para a integração empresarial de B2B - Azure Logic Apps
description: Trocar mensagens AS2 no Azure Logic Apps com o Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 04/22/2019
ms.openlocfilehash: b494f6524e5105a95bc8a24a6fa2521abcca3f7b
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64729391"
---
# <a name="exchange-as2-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Trocar mensagens AS2 para enterprise integração B2B no Azure Logic Apps Enterprise Integration Pack

Para trabalhar com mensagens AS2 no Azure Logic Apps, pode utilizar o conector AS2, que fornece os acionadores e ações para a gestão de comunicação de AS2. Por exemplo, para estabelecer a segurança e fiabilidade ao transmitir mensagens, pode utilizar estas ações:

* [**Codificar em mensagem AS2** ação](#encode) para fornecer encriptação, assinatura digital e as confirmações por meio de notificações de disposição de mensagem (MDN), que ajudar a suportar não-repúdio. Por exemplo, esta ação aplica-se os cabeçalhos de AS2/HTTP e executa estas tarefas quando configurado:

  * Sinais de mensagens de saída.
  * Encripta as mensagens de saída.
  * Compacta a mensagem.
  * Transmite o nome do ficheiro no cabeçalho de MIME.

* [**Descodificar mensagem AS2** ação](#decode) para fornecer a descriptografia, assinatura digital e as confirmações por meio de notificações de disposição de mensagem (MDN). Por exemplo, esta ação executa estas tarefas: 

  * Processa cabeçalhos de AS2/HTTP.
  * Reconcilia MDNs recebidos com as mensagens de saída originais.
  * Atualizações e correlaciona os registos na base de dados não-repúdio.
  * Grava os registros para relatórios de estado de AS2.
  * Conteúdo do payload de saídas como codificada em base64.
  * Determina se MDNs são necessárias. Com base em AS2 contrato, determina se o devem ser MDNs síncronos ou assíncronos.
  * Gera MDNs síncronos ou assíncronos com base no contrato de AS2.
  * Define as propriedades e os tokens de correlação de MDNs.

  Esta ação também executa estas tarefas quando configurado:

  * Verifica a assinatura.
  * Desencripta as mensagens.
  * Descomprime a mensagem. 
  * Verifique e não permitir duplicados de ID de mensagem.

Este artigo mostra como adicionar a codificação de AS2 e decodificação de ações a uma aplicação lógica existente.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [Inscreva-se uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* A aplicação de lógica de em que pretende utilizar o conector AS2 e um acionador que inicia o fluxo de trabalho da sua aplicação lógica. O conector AS2 fornece apenas ações, acionadores não. Se estiver familiarizado com aplicações lógicas, reveja [o que é o Azure Logic Apps](../logic-apps/logic-apps-overview.md) e [início rápido: Criar a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) que tem associados à subscrição do Azure e associado à aplicação lógica que planeia utilizar o conector AS2. Ambos os sua lógica aplicação e a integração conta tem de existir na mesma localização ou região do Azure.

* Pelo menos dois [parceiros comerciais](../logic-apps/logic-apps-enterprise-integration-partners.md) que já definiu na sua conta de integração usando o qualificador de identidade de AS2.

* Antes de poder utilizar o conector AS2, tem de criar um AS2 [contrato](../logic-apps/logic-apps-enterprise-integration-agreements.md) entre seus parceiros comerciais e um arquivo desse contrato na sua conta de integração.

* Se usar [do Azure Key Vault](../key-vault/key-vault-overview.md) para a gestão de certificados, verifique que as suas chaves do cofre permitem a **Encrypt** e **desencriptar** operações. Caso contrário, a codificação e decodificação de ações falharem.

  No portal do Azure, aceda ao seu Cofre de chaves, ver a sua chave de cofre **permitido operações**e confirme que o **Encrypt** e **desencriptar** operações estão selecionadas.

  ![Verifique as operações do Cofre de chaves](media/logic-apps-enterprise-integration-as2/vault-key-permitted-operations.png)

<a name="encode"></a>

## <a name="encode-as2-messages"></a>Codificar nas mensagens AS2

1. Se ainda não o fez, no [portal do Azure](https://portal.azure.com), abra a aplicação lógica no Estruturador da aplicação lógica.

1. No designer, adicione uma nova ação à sua aplicação lógica. 

1. Sob **escolher uma ação** e a pesquisa de caixa, escolha **todos os**. Na caixa de pesquisa, introduza "codificar as2" e selecione a ação: **Codificar em mensagem AS2**.

   ![Selecione "Codificar em mensagem AS2"](./media/logic-apps-enterprise-integration-as2/select-as2-encode.png)

1. Se não tiver uma ligação existente à sua conta de integração, lhe for pedido para criar essa conexão agora. Nome da sua ligação, selecione a conta de integração que pretende ligar e escolha **criar**.

   ![Criar ligação para a conta de integração](./media/logic-apps-enterprise-integration-as2/as2-create-connection.png)  
 
1. Agora fornecem informações para estas propriedades:

   | Propriedade | Descrição |
   |----------|-------------|
   | **AS2-do** | O identificador para o remetente da mensagem como especificado pelo seu contrato de AS2 |
   | **AS2-para** | O identificador para o destinatário da mensagem como especificado pelo seu contrato de AS2 |
   | **body** | O payload de mensagem |
   |||

   Por exemplo:

   ![Propriedades da mensagem de codificação](./media/logic-apps-enterprise-integration-as2/as2-message-encoding-details.png)

<a name="decode"></a>

## <a name="decode-as2-messages"></a>Descodificar nas mensagens AS2

1. Se ainda não o fez, no [portal do Azure](https://portal.azure.com), abra a aplicação lógica no Estruturador da aplicação lógica.

1. No designer, adicione uma nova ação à sua aplicação lógica. 

1. Sob **escolher uma ação** e a pesquisa de caixa, escolha **todos os**. Na caixa de pesquisa, introduza "descodificação as2" e selecione a ação: **Descodificar mensagem AS2**

   ![Selecione "Mensagem AS2 Decodificação"](media/logic-apps-enterprise-integration-as2/select-as2-decode.png)

1. Se não tiver uma ligação existente à sua conta de integração, lhe for pedido para criar essa conexão agora. Nome da sua ligação, selecione a conta de integração que pretende ligar e escolha **criar**.

   ![Criar ligação para a conta de integração](./media/logic-apps-enterprise-integration-as2/as2-create-connection.png)  

1. Para **corpo** e **cabeçalhos**, selecione esses valores de saídas de Acionador ou ação anteriores.

   Por exemplo, suponha que a aplicação lógica recebe mensagens por meio de um acionador de pedido. Pode selecionar as saídas nesse disparador.

   ![Selecione corpo e cabeçalhos de pedido de saídas](media/logic-apps-enterprise-integration-as2/as2-message-decoding-details.png) 

## <a name="sample"></a>Exemplo

Para experimentar a implantação de um cenário de AS2 de exemplo e a aplicação lógica totalmente operacional, consulte a [AS2 modelo da aplicação lógica e cenário](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/).

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos, como disparadores, ações e limites, conforme descrito pelo OpenAPI do conector (anteriormente Swagger) de ficheiros, consulte a [página de referência do conector](/connectors/as2/).

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre o [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)

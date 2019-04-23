---
title: Descodificar nas mensagens AS2 - Azure Logic Apps | Documentos da Microsoft
description: Descodificar como mensagens com o Azure Logic Apps e o Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: jonfan, divswa, LADocs
ms.topic: article
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.date: 08/08/2018
ms.openlocfilehash: ca297e1b4a007db3020b4369132b190608484738
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60001631"
---
# <a name="decode-as2-messages-with-azure-logic-apps-and-enterprise-integration-pack"></a>Descodificação de AS2 mensagens com o Azure Logic Apps e o Enterprise Integration Pack 

Para estabelecer a segurança e confiabilidade durante a transmissão de mensagens, utilize o conector de mensagem de descodificação AS2. Este conector proporciona a assinatura digital, desencriptação e as confirmações por meio de notificações de disposição de mensagem (MDN).

## <a name="before-you-start"></a>Antes de começar

Eis os itens que precisa:

* Uma conta do Azure; Pode criar um [conta gratuita](https://azure.microsoft.com/free)
* Uma [conta de integração](logic-apps-enterprise-integration-create-integration-account.md) que já definida e associada à sua subscrição do Azure. Tem de ter uma conta de integração para utilizar o conector de mensagem de descodificação AS2.
* Pelo menos dois [parceiros](logic-apps-enterprise-integration-partners.md) que já estão definidas na sua conta de integração
* Uma [contrato de AS2](logic-apps-enterprise-integration-as2.md) que já está definido na sua conta de integração

## <a name="decode-as2-messages"></a>Descodificar nas mensagens AS2

1. [Criar uma aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

2. O conector de mensagem de descodificação AS2 não tem acionadores, para que deve adicionar um acionador para iniciar a sua aplicação lógica, como um acionador de pedido. No Estruturador da aplicação lógica, adicionar um acionador e, em seguida, adicione uma ação à sua aplicação lógica.

3.  Na caixa de pesquisa, introduza "AS2" para o filtro. Selecione **AS2 - mensagem de descodificação AS2**.
   
    ![Procure "AS2"](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage1.png)

4. Se não tiver criado anteriormente todas as ligações à sua conta de integração, lhe for pedido para criar essa conexão agora. Nomeie a sua ligação e selecione a conta de integração que pretende ligar.
   
    ![Criar ligação de integração](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage2.png)

    Propriedades com um asterisco são necessárias.

    | Propriedade | Detalhes |
    | --- | --- |
    | Nome da ligação * |Introduza qualquer nome para a sua ligação. |
    | Conta de integração * |Introduza um nome para a sua conta de integração. Certifique-se de que a sua aplicação de lógica e a conta de integração estão na mesma localização do Azure. |

5.  Quando tiver terminado, seus detalhes de ligação devem ter um aspeto semelhantes a este exemplo. Para acabar de criar a ligação, escolha **criar**.

    ![detalhes de ligação de integração](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage3.png)

6. Depois de criar a ligação, conforme mostrado neste exemplo, selecione **corpo** e **cabeçalhos** das saídas de pedido.
   
    ![ligação de integração criada](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage4.png) 

    Por exemplo:

    ![Selecione corpo e cabeçalhos de pedido de saídas](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage5.png) 


## <a name="as2-decoder-details"></a>Detalhes do descodificador de AS2

O conector de descodificação AS2 executa estas tarefas: 

* Processa cabeçalhos de AS2/HTTP
* Verifica a assinatura (se configurada)
* Desencripta as mensagens (se configurada)
* Descomprime a mensagem (se configurada)
* Verifique e não permitir duplicados de ID de mensagem (se configurada)
* Reconcilia um MDN recebido com a mensagem de saída original
* Atualizações e correlaciona os registos na base de dados não-repúdio
* Grava os registros para relatórios de estado de AS2
* O conteúdo do payload de saída é codificados em base64
* Determina se um MDN é necessário e se o MDN deve ser síncrono ou assíncrono com base na configuração no contrato de AS2
* Gera um MDN síncrona ou assíncrona (com base nas configurações de contrato)
* Define as propriedades e os tokens de correlação de MDN


  > [!NOTE]
  > Se utilizar o Azure Key Vault para a gestão de certificados, certifique-se de que configura as chaves para permitir a **desencriptar** operação.
  > Caso contrário, irá falhar descodificação de AS2.
  >
  > ![Desencripta o Cofre de chaves](media/logic-apps-enterprise-integration-as2-decode/keyvault1.png)

## <a name="try-this-sample"></a>Experimente este exemplo

Para experimentar a implantação de um cenário de AS2 de exemplo e a aplicação lógica totalmente operacional, consulte a [AS2 modelo da aplicação lógica e cenário](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/).

## <a name="view-the-swagger"></a>Ver o swagger
Consulte a [detalhes de swagger](/connectors/as2/). 

## <a name="next-steps"></a>Passos Seguintes
[Saiba mais sobre o Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md) 


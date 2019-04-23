---
title: Codificar nas mensagens AS2 - Azure Logic Apps | Documentos da Microsoft
description: Codificar como mensagens com o Azure Logic Apps e o Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: jonfan, divswa, LADocs
ms.topic: article
ms.assetid: 332fb9e3-576c-4683-bd10-d177a0ebe9a3
ms.date: 08/08/2018
ms.openlocfilehash: 455056feaa1b13022be3ab3c15880a87c50dedd9
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60003552"
---
# <a name="encode-as2-messages-with-azure-logic-apps-and-enterprise-integration-pack"></a>Codificar com o Azure Logic Apps e o Enterprise Integration Pack nas mensagens AS2

Para estabelecer a segurança e confiabilidade durante a transmissão de mensagens, utilize o conector de mensagem de codificação AS2. Este conector proporciona a assinatura digital, a encriptação e as confirmações por meio de mensagem disposição notificações (MDN), que também leva para oferecer suporte a não rejeição.

## <a name="before-you-start"></a>Antes de começar

Eis os itens que precisa:

* Uma conta do Azure; Pode criar um [conta gratuita](https://azure.microsoft.com/free)
* Uma [conta de integração](logic-apps-enterprise-integration-create-integration-account.md) que já definida e associada à sua subscrição do Azure. Tem de ter uma conta de integração para utilizar o conector de mensagem AS2 codificar.
* Pelo menos dois [parceiros](logic-apps-enterprise-integration-partners.md) que já estão definidas na sua conta de integração
* Uma [contrato de AS2](logic-apps-enterprise-integration-as2.md) que já está definido na sua conta de integração

## <a name="encode-as2-messages"></a>Codificar nas mensagens AS2

1. [Criar uma aplicação lógica](quickstart-create-first-logic-app-workflow.md).

2. O conector de mensagem AS2 codificar não tem acionadores, para que deve adicionar um acionador para iniciar a sua aplicação lógica, como um acionador de pedido. No Estruturador da aplicação lógica, adicionar um acionador e, em seguida, adicione uma ação à sua aplicação lógica.

3.  Na caixa de pesquisa, introduza "AS2" para o filtro. Selecione **AS2 - mensagem AS2 codificar**.
   
    ![Procure "AS2"](./media/logic-apps-enterprise-integration-as2-encode/as2decodeimage1.png)

4. Se não tiver criado anteriormente todas as ligações à sua conta de integração, lhe for pedido para criar essa conexão agora. Nomeie a sua ligação e selecione a conta de integração que pretende ligar. 
   
    ![Criar ligação para a conta de integração](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage1.png)  

    Propriedades com um asterisco são necessárias.

    | Propriedade | Detalhes |
    | --- | --- |
    | Nome da ligação * |Introduza qualquer nome para a sua ligação. |
    | Conta de integração * |Introduza um nome para a sua conta de integração. Certifique-se de que a sua aplicação de lógica e a conta de integração estão na mesma localização do Azure. |

5.  Quando tiver terminado, seus detalhes de ligação devem ter um aspeto semelhantes a este exemplo. Para acabar de criar a ligação, escolha **criar**.
   
    ![detalhes de ligação de integração](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage2.png)

6. Depois de criar a ligação, conforme mostrado neste exemplo, indique os detalhes para **AS2-partir**, **AS2-aos identificadores** conforme configurado no seu contrato, e **corpo**, que é o payload de mensagem.
   
    ![fornecer os campos de preenchimento obrigatório](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage3.png)

## <a name="as2-encoder-details"></a>Detalhes do codificador de AS2

O conector de codificação AS2 executa estas tarefas: 

* Aplica-se de cabeçalhos de AS2/HTTP
* Sinais de mensagens de saída (se configurada)
* Encripta as mensagens de saída (se configurada)
* Compacta a mensagem (se configurada)
* Transmitir o nome de ficheiro no cabeçalho MIME (se configurada)


  > [!NOTE]
  > Se utilizar o Azure Key Vault para a gestão de certificados, certifique-se de que configura as chaves para permitir a **Encrypt** operação.
  > Caso contrário, a codificação de AS2 falhará.
  >
  > ![Desencripta o Cofre de chaves](media/logic-apps-enterprise-integration-as2-encode/keyvault1.png)

## <a name="try-this-sample"></a>Experimente este exemplo

Para experimentar a implantação de um cenário de AS2 de exemplo e a aplicação lógica totalmente operacional, consulte a [AS2 modelo da aplicação lógica e cenário](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/).

## <a name="view-the-swagger"></a>Ver o swagger
Consulte a [detalhes de swagger](/connectors/as2/). 

## <a name="next-steps"></a>Passos Seguintes
[Saiba mais sobre o Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack") 


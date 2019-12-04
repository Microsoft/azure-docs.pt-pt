---
title: Criar integrações corporativas B2B
description: Receber dados B2B em aplicativos lógicos do Azure com Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 07/08/2016
ms.openlocfilehash: 39966b8171296a8608b9436485f7682d114c8410
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74793094"
---
# <a name="receive-b2b-data-with-azure-logic-apps-and-enterprise-integration-pack"></a>Receber dados B2B com aplicativos lógicos do Azure e Enterprise Integration Pack

Depois de criar uma conta de integração que tenha parceiros e contratos, você estará pronto para criar um fluxo de trabalho B2B (Business to Business) para seu aplicativo lógico com o [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

Para usar as ações AS2 e X12, você deve ter uma conta de Enterprise Integration. Saiba [como criar uma conta de Enterprise Integration](../logic-apps/logic-apps-enterprise-integration-accounts.md).

## <a name="create-a-logic-app-with-b2b-connectors"></a>Criar um aplicativo lógico com conectores B2B

Siga estas etapas para criar um aplicativo lógico B2B que usa as ações AS2 e X12 para receber dados de um parceiro comercial:

1. Crie um aplicativo lógico e, em seguida, [vincule seu aplicativo à sua conta de integração](../logic-apps/logic-apps-enterprise-integration-accounts.md).

2. Adicione um gatilho **solicitação-quando uma solicitação HTTP é recebida** para seu aplicativo lógico.

    ![](./media/logic-apps-enterprise-integration-b2b/flatfile-1.png)

3. Para adicionar a ação de **decodificar AS2** , selecione **Adicionar uma ação**.

    ![](./media/logic-apps-enterprise-integration-b2b/transform-2.png)

4. Para filtrar todas as ações para aquela que você deseja, insira a palavra **AS2** na caixa de pesquisa.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-5.png)

5. Selecione a ação **AS2-decodificar mensagem AS2** .

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-6.png)

6. Adicione o **corpo** que você deseja usar como entrada. 
   Neste exemplo, selecione o corpo da solicitação HTTP que dispara o aplicativo lógico. Ou insira uma expressão que insere os cabeçalhos no campo **cabeçalhos** :

    @triggerOutputs() [' cabeçalhos ']

7. Adicione os **cabeçalhos** necessários para AS2, que você pode encontrar nos cabeçalhos de solicitação HTTP. 
   Neste exemplo, selecione os cabeçalhos da solicitação HTTP que disparam o aplicativo lógico.

8. Agora, adicione a ação decodificar mensagem X12. Selecione **Adicionar uma ação**.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-9.png)

9. Para filtrar todas as ações para aquela que você deseja, insira a palavra **X12** na caixa de pesquisa.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-10.png)

10. Selecione a ação de **mensagem de X12 de decodificação X12** .

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-as2message.png)

11. Agora você deve especificar a entrada para esta ação. 
    Essa entrada é a saída da ação AS2 anterior.

    O conteúdo real da mensagem está em um objeto JSON e é codificado em base64, portanto, você deve especificar uma expressão como a entrada. 
    Insira a seguinte expressão no campo de entrada **X12 mensagem de arquivo simples para decodificação** :
    
    @base64ToString(corpo (' Decode_AS2_message ')? [' AS2Message']? [' Content '])

    Agora, adicione etapas para decodificar os dados de X12 recebidos do parceiro comercial e itens de saída em um objeto JSON. 
    Para notificar o parceiro de que os dados foram recebidos, você pode enviar uma resposta que contém a notificação de disposição de mensagens AS2 (MDN) em uma ação de resposta HTTP.

12. Para adicionar a ação de **resposta** , escolha **Adicionar uma ação**.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-14.png)

13. Para filtrar todas as ações para aquela que você deseja, insira a palavra **resposta** na caixa de pesquisa.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-15.png)

14. Selecione a ação de **resposta** .

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-16.png)

15. Para acessar o MDN da saída da ação **decodificar mensagem X12** , defina o campo **corpo** da resposta com esta expressão:

    @base64ToString(corpo (' Decode_AS2_message ')? [' OutgoingMdn']? [' Content '])

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-17.png)  

16. Guarde o seu trabalho.

    ![](./media/logic-apps-enterprise-integration-b2b/transform-5.png)  

Agora você concluiu a configuração do seu aplicativo lógico B2B. Em um aplicativo real, talvez você queira armazenar os dados X12 decodificados em um aplicativo de linha de negócios (LOB) ou armazenamento de dados. Para conectar seus próprios aplicativos LOB e usar essas APIs em seu aplicativo lógico, você pode adicionar mais ações ou gravar APIs personalizadas.

## <a name="features-and-use-cases"></a>Recursos e casos de uso

* As ações de decodificação e codificação AS2 e X12 permitem que você troque dados entre parceiros comerciais usando protocolos padrão do setor em aplicativos lógicos.
* Para trocar dados com parceiros comerciais, você pode usar AS2 e X12 com ou sem o outro.
* As ações B2B ajudam você a criar parceiros e contratos facilmente em sua conta de integração e consumi-los em um aplicativo lógico.
* Ao estender seu aplicativo lógico com outras ações, você pode enviar e receber dados entre outros aplicativos e serviços como o SalesForce.

## <a name="learn-more"></a>Saber mais
[Saiba mais sobre o Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)

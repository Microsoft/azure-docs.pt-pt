---
title: Criar integrações de enterprise B2B - Azure Logic Apps | Documentos da Microsoft
description: Receber dados de B2B no Azure Logic Apps com o Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 20fc3722-6f8b-402f-b391-b84e9df6fcff
ms.date: 07/08/2016
ms.openlocfilehash: 05368f627c5e9482a43d5e30b0e16b1d47f6217c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58074730"
---
# <a name="receive-b2b-data-with-azure-logic-apps-and-enterprise-integration-pack"></a>Receber dados de B2B com o Azure Logic Apps e o Enterprise Integration Pack

Depois de criar uma conta de integração com parceiros e contratos, está pronto para criar um fluxo de trabalho do empresa-empresa (B2B) para a sua aplicação lógica com o [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar o AS2 e X12 ações, tem de ter uma conta de integração da empresa. Saiba mais [como criar uma conta de integração do Enterprise](../logic-apps/logic-apps-enterprise-integration-accounts.md).

## <a name="create-a-logic-app-with-b2b-connectors"></a>Criar uma aplicação lógica com conectores de B2B

Siga estes passos para criar uma aplicação de lógica de B2B que utiliza o AS2 e X12 ações receba dados de um parceiro comercial:

1. Criar uma aplicação de lógica, em seguida, [ligar a sua aplicação à sua conta de integração](../logic-apps/logic-apps-enterprise-integration-accounts.md).

2. Adicionar uma **pedido - pedido de HTTP de uma quando é recebido** acionador à sua aplicação lógica.

    ![](./media/logic-apps-enterprise-integration-b2b/flatfile-1.png)

3. Para adicionar o **descodificação AS2** ação, selecione **adicionar uma ação**.

    ![](./media/logic-apps-enterprise-integration-b2b/transform-2.png)

4. Para filtrar todas as ações para aquele que pretende, introduza a palavra **as2** na caixa de pesquisa.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-5.png)

5. Selecione o **AS2 - mensagem de descodificação AS2** ação.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-6.png)

6. Adicionar a **corpo** que pretende utilizar como entrada. 
   Neste exemplo, selecione o corpo da solicitação HTTP que aciona a aplicação lógica. Ou introduza uma expressão que insere os cabeçalhos na **CABEÇALHOS** campo:

    @triggerOutputs()['headers']

7. Adicionar necessários **cabeçalhos** para AS2, o que pode ser encontrado nos cabeçalhos do pedido HTTP. 
   Neste exemplo, selecione os cabeçalhos da solicitação HTTP que acionam a aplicação lógica.

8. Agora, adicione a ação de mensagem de Decodificação X12. Selecione **adicionar uma ação**.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-9.png)

9. Para filtrar todas as ações para aquele que pretende, introduza a palavra **x12** na caixa de pesquisa.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-10.png)

10. Selecione o **X12 de descodificação de X12-mensagem** ação.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-as2message.png)

11. Agora tem de especificar a entrada para esta ação. 
    Esta entrada é o resultado da ação de AS2 anterior.

    O conteúdo da mensagem real está num objeto JSON e é a codificação base64, pelo que tem de especificar uma expressão como entrada. 
    Introduza a seguinte expressão na **DECODIFICAÇÃO de TO de mensagem de ficheiro simples de X12** campo de entrada:
    
    @base64ToString(body('Decode_AS2_message')?['AS2Message']?['Content'])

    Agora, adicione passos para descodificação X12 dados receberam do parceiro comercial e itens num objeto JSON de saída. 
    Para notificar o parceiro que os dados foram recebidos, pode enviar de volta uma resposta que contém o AS2 mensagem disposição notificação (MDN) numa ação de resposta de HTTP.

12. Para adicionar o **resposta** ação, escolha **adicionar uma ação**.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-14.png)

13. Para filtrar todas as ações para aquele que pretende, introduza a palavra **resposta** na caixa de pesquisa.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-15.png)

14. Selecione o **resposta** ação.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-16.png)

15. Para acessar o MDN da saída dos **descodificador de mensagem X12** ação, definir a resposta **corpo** campo com esta expressão:

    @base64ToString(body('Decode_AS2_message')?['OutgoingMdn']?['Content'])

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-17.png)  

16. Guarde o seu trabalho.

    ![](./media/logic-apps-enterprise-integration-b2b/transform-5.png)  

Terminou agora configurar a sua aplicação de lógica de B2B. Num aplicativo do mundo real, pode querer armazenar X12 descodificada dados num linha de negócio (LOB) aplicações ou arquivo de dados. Para ligar as suas aplicações LOB e usam essas APIs na sua aplicação lógica, pode adicionar mais ações ou escrever APIs personalizadas.

## <a name="features-and-use-cases"></a>Recursos e casos de utilização

* O AS2 e X12 a descodificar e codificar ações let trocar dados entre parceiros comerciais usando protocolos padrão do setor no logic apps.
* Para trocar dados com parceiros comerciais, pode utilizar o AS2 e X12 com ou sem entre si.
* As ações B2B ajudam a criar facilmente parceiros e contratos na sua conta de integração e usá-los numa aplicação lógica.
* Quando expande a sua aplicação lógica com outras ações, pode enviar e receber dados, entre outras aplicações e serviços como o SalesForce.

## <a name="learn-more"></a>Saiba mais
[Saiba mais sobre o Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)

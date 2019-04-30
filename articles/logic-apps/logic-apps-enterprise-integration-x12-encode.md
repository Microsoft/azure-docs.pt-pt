---
title: X12 de codificação mensagens - Azure Logic Apps | Documentos da Microsoft
description: Validar EDI e converter codificado em XML mensagens com X12 mensagem codificador no Azure Logic Apps com o Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: jonfan, divswa, LADocs
ms.topic: article
ms.assetid: a01e9ca9-816b-479e-ab11-4a984f10f62d
ms.date: 01/27/2017
ms.openlocfilehash: 871d6d2b2019372bd258f8909ed0feeeddac4af7
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62106515"
---
# <a name="encode-x12-messages-in-azure-logic-apps-with-enterprise-integration-pack"></a>X12 de codificação mensagens no Azure Logic Apps com o Enterprise Integration Pack

Com o conector de mensagem X12 de codificar, pode validar EDI e propriedades específicas de parceiro, converter mensagens XML codificado em conjuntos de transações de EDI no intercâmbio e pedir uma confirmação técnicas, funcionais de confirmação ou ambos.
Para utilizar este conector, tem de adicionar o conector para um acionador existente na sua aplicação lógica.

## <a name="before-you-start"></a>Antes de começar

Eis os itens que precisa:

* Uma conta do Azure; Pode criar um [conta gratuita](https://azure.microsoft.com/free)
* Uma [conta de integração](logic-apps-enterprise-integration-create-integration-account.md) que já definida e associada à sua subscrição do Azure. Tem de ter uma conta de integração para utilizar o conector de mensagem X12 de codificar.
* Pelo menos dois [parceiros](logic-apps-enterprise-integration-partners.md) que já estão definidas na sua conta de integração
* Uma [X12 contrato](logic-apps-enterprise-integration-x12.md) que já está definido na sua conta de integração

## <a name="encode-x12-messages"></a>X12 de codificação mensagens

1. [Criar uma aplicação lógica](quickstart-create-first-logic-app-workflow.md).

2. O conector de mensagem Encode X12 não tem acionadores, para que deve adicionar um acionador para iniciar a sua aplicação lógica, como um acionador de pedido. No Estruturador da aplicação lógica, adicionar um acionador e, em seguida, adicione uma ação à sua aplicação lógica.

3.  Na caixa de pesquisa, introduza "x12" para o filtro. Selecione **X12-codificação X12 mensagem por nome do contrato** ou **X12-codificação X12 mensagem por identidades**.
   
    ![Procure "x12"](./media/logic-apps-enterprise-integration-x12-encode/x12decodeimage1.png) 

3. Se não tiver criado anteriormente todas as ligações à sua conta de integração, lhe for pedido para criar essa conexão agora. Nomeie a sua ligação e selecione a conta de integração que pretende ligar. 
   
    ![ligação da conta de integração](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage1.png)

    Propriedades com um asterisco são necessárias.

    | Propriedade | Detalhes |
    | --- | --- |
    | Nome da ligação * |Introduza qualquer nome para a sua ligação. |
    | Conta de integração * |Introduza um nome para a sua conta de integração. Certifique-se de que a sua aplicação de lógica e a conta de integração estão na mesma localização do Azure. |

5.  Quando tiver terminado, seus detalhes de ligação devem ter um aspeto semelhantes a este exemplo. Para acabar de criar a ligação, escolha **criar**.

    ![ligação da conta de integração criada](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage2.png)

    A ligação está agora criada.

    ![detalhes de ligação da conta de integração](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage3.png) 

#### <a name="encode-x12-messages-by-agreement-name"></a>X12 de codificação mensagens por nome do contrato

Se optar por codificar X12 mensagens por nome do contrato, abra a **nome de X12 contrato** lista, introduza ou selecione o seu X12 existente contrato. Introduza a mensagem XML a codificar.

![Introduza X12 nome do contrato e mensagem XML a codificar](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage4.png)

#### <a name="encode-x12-messages-by-identities"></a>X12 de codificação mensagens por identidades

Se optar por codificar X12 mensagens por identidades, introduza o identificador do remetente, qualificador do remetente, identificador do destinatário e qualificador do destinatário, conforme configurado na sua X12 contrato. Selecione a mensagem XML a codificar.
   
![Dê identidades para o remetente e o receptor, selecione a mensagem XML a codificar](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage5.png) 

## <a name="x12-encode-details"></a>X12 codificar detalhes

X12 Encode conector executa estas tarefas:

* Resolução do contrato pela correspondência entre o remetente e o receptor propriedades de contexto.
* Serializa o intercâmbio EDI, conversão de mensagens XML codificado em conjuntos de transações de EDI no intercâmbio.
* Aplica-se a transação de segmentos de cabeçalho e as informações finais de conjunto
* Gera um número de controlo de intercâmbio, um número de controlo de grupo e um número de controlo do conjunto de transações para cada intercâmbio de saída
* Substitui os separadores nos dados de payload
* Valida o EDI e propriedades específicas de parceiro
  * Validação de esquema dos elementos de dados do conjunto de transações em relação a mensagem de esquema
  * É executada uma validação de EDI em elementos de dados do conjunto de transações.
  * Validação estendida executada em elementos de dados do conjunto de transações
* Solicita uma confirmação técnica e/ou funcional (se configurada).
  * Gera uma confirmação de técnicas como resultado de validação de cabeçalho. A confirmação técnica comunica o estado do processamento de um cabeçalho de intercâmbio e as informações finais pelo recetor de endereço
  * Gera uma confirmação funcional como resultado de validação de corpo. A confirmação funcional relatórios cada erro encontrado ao processar o documento recebido

## <a name="view-the-swagger"></a>Ver o swagger
Consulte a [detalhes de swagger](/connectors/x12/). 

## <a name="next-steps"></a>Passos Seguintes
[Saiba mais sobre o Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack") 


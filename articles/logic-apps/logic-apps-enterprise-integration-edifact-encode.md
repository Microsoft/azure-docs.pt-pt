---
title: Codificar mensagens EDIFACT - Azure Logic Apps | Documentos da Microsoft
description: Validar EDI e gerar o XML com o codificador de mensagem EDIFACT para o Azure Logic Apps com o Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: jonfan, divswa, LADocs
ms.topic: article
ms.assetid: 974ac339-d97a-4715-bc92-62d02281e900
ms.date: 01/27/2017
ms.openlocfilehash: 7396aee56acdf2476ed1bb3cc5a9909349662dc7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64705532"
---
# <a name="encode-edifact-messages-for-azure-logic-apps-with-enterprise-integration-pack"></a>Codificar mensagens EDIFACT para o Azure Logic Apps com o Enterprise Integration Pack

Com o conector de mensagem de EDIFACT codificar, pode validar EDI e propriedades específicas de parceiro, gerar um documento XML para cada conjunto de transações e pedir uma confirmação técnicas, funcionais de confirmação ou ambos.
Para utilizar este conector, tem de adicionar o conector para um acionador existente na sua aplicação lógica.

## <a name="before-you-start"></a>Antes de começar

Eis os itens que precisa:

* Uma conta do Azure; Pode criar um [conta gratuita](https://azure.microsoft.com/free)
* Uma [conta de integração](logic-apps-enterprise-integration-create-integration-account.md) que já definida e associada à sua subscrição do Azure. Tem de ter uma conta de integração para utilizar o conector de mensagem de EDIFACT de codificar. 
* Pelo menos dois [parceiros](logic-apps-enterprise-integration-partners.md) que já estão definidas na sua conta de integração
* Uma [contrato EDIFACT](logic-apps-enterprise-integration-edifact.md) que já está definido na sua conta de integração

## <a name="encode-edifact-messages"></a>Codificar mensagens EDIFACT

1. [Criar uma aplicação lógica](quickstart-create-first-logic-app-workflow.md).

2. O conector de mensagem de EDIFACT codificar não tem acionadores, para que deve adicionar um acionador para iniciar a sua aplicação lógica, como um acionador de pedido. No Estruturador da aplicação lógica, adicionar um acionador e, em seguida, adicione uma ação à sua aplicação lógica.

3.  Na caixa de pesquisa, introduza "EDIFACT" como o filtro. Selecione **codificador de mensagem EDIFACT por nome do contrato** ou **codificar em mensagem EDIFACT por identidades**.
   
    ![pesquisa de EDIFACT](media/logic-apps-enterprise-integration-edifact-encode/edifactdecodeimage1.png)  

3. Se não tiver criado anteriormente todas as ligações à sua conta de integração, lhe for pedido para criar essa conexão agora. Nomeie a sua ligação e selecione a conta de integração que pretende ligar.

    ![criar a ligação da conta de integração](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage1.png)  

    Propriedades com um asterisco são necessárias.

    | Propriedade | Detalhes |
    | --- | --- |
    | Nome da ligação * |Introduza qualquer nome para a sua ligação. |
    | Conta de integração * |Introduza um nome para a sua conta de integração. Certifique-se de que a sua aplicação de lógica e a conta de integração estão na mesma localização do Azure. |

5.  Quando tiver terminado, seus detalhes de ligação devem ter um aspeto semelhantes a este exemplo. Para acabar de criar a ligação, escolha **criar**.

    ![detalhes de ligação da conta de integração](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage2.png)

    A ligação está agora criada.

    ![ligação da conta de integração criada](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage4.png)

#### <a name="encode-edifact-message-by-agreement-name"></a>Codificar a mensagem EDIFACT por nome do contrato

Se optar por codificar mensagens EDIFACT por nome do contrato, abra a **contrato de nome de EDIFACT** lista, introduza ou selecione o nome do contrato EDIFACT. Introduza a mensagem XML a codificar.

![Introduza o nome do contrato EDIFACT e a mensagem XML a codificar](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage6.png)

#### <a name="encode-edifact-message-by-identities"></a>Codificar a mensagem EDIFACT por identidades

Se optar por codificar mensagens EDIFACT por identidades, introduza o identificador do remetente, qualificador do remetente, identificador do destinatário e qualificador do destinatário, conforme configurado no seu contrato EDIFACT. Selecione a mensagem XML a codificar.

![Dê identidades para o remetente e o receptor, selecione a mensagem XML a codificar](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage7.png)

## <a name="edifact-encode-details"></a>Detalhes de codificação EDIFACT.

O conector do EDIFACT codificar executa estas tarefas: 

* Resolver contrato pela correspondência entre o qualificador do remetente & identificador e qualificador do destinatário e identificador
* Serializa o intercâmbio EDI, conversão de mensagens XML codificado em conjuntos de transações de EDI no intercâmbio.
* Aplica-se a transação de segmentos de cabeçalho e as informações finais de conjunto
* Gera um número de controlo de intercâmbio, um número de controlo de grupo e um número de controlo do conjunto de transações para cada intercâmbio de saída
* Substitui os separadores nos dados de payload
* Valida o EDI e propriedades específicas de parceiro
  * Validação de esquema dos elementos de dados do conjunto de transações no esquema de mensagem.
  * É executada uma validação de EDI em elementos de dados do conjunto de transações.
  * Validação estendida executada em elementos de dados do conjunto de transações
* Gera um documento XML para cada conjunto de transações.
* Solicita uma confirmação técnica e/ou funcional (se configurada).
  * Como uma confirmação técnica, a mensagem CONTRL indica o recebimento de um intercâmbio.
  * Como uma confirmação funcional, a mensagem CONTRL indica a aceitação ou de rejeição do recebidos de intercâmbio, grupo ou mensagem, com uma lista de erros ou funcionalidades não suportadas

## <a name="view-swagger-file"></a>Ver o ficheiro Swagger
Para ver os detalhes de Swagger para o conector do EDIFACT, consulte [EDIFACT](/connectors/edifact/).

## <a name="next-steps"></a>Passos Seguintes
[Saiba mais sobre o Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack") 


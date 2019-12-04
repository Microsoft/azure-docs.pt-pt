---
title: Codificar mensagens X12
description: Validar EDI e converter mensagens codificadas em XML com o codificador de mensagem X12 em aplicativos lógicos do Azure com Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: c87a61dc77e656a1cfe667ce87f852303a0cc486
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792358"
---
# <a name="encode-x12-messages-in-azure-logic-apps-with-enterprise-integration-pack"></a>Codificar mensagens X12 em aplicativos lógicos do Azure com Enterprise Integration Pack

Com o conector de mensagem X12 de codificação, você pode validar o EDI e as propriedades específicas de parceiro, converter mensagens codificadas em XML em conjuntos de transações EDI no intercâmbio e solicitar uma confirmação técnica, uma confirmação funcional ou ambas.
Para usar esse conector, você deve adicionar o conector a um gatilho existente em seu aplicativo lógico.

## <a name="before-you-start"></a>Antes de começar

Aqui estão os itens de que você precisa:

* Uma conta do Azure; Você pode criar uma [conta gratuita](https://azure.microsoft.com/free)
* Uma [conta de integração](logic-apps-enterprise-integration-create-integration-account.md) que já está definida e associada à sua assinatura do Azure. Você deve ter uma conta de integração para usar o conector de mensagem X12 de codificação.
* Pelo menos dois [parceiros](logic-apps-enterprise-integration-partners.md) que já estão definidos em sua conta de integração
* Um [contrato do X12](logic-apps-enterprise-integration-x12.md) que já está definido em sua conta de integração

## <a name="encode-x12-messages"></a>Codificar mensagens X12

1. [Crie um aplicativo lógico](quickstart-create-first-logic-app-workflow.md).

2. O conector de mensagem X12 de codificação não tem gatilhos, portanto, você deve adicionar um gatilho para iniciar seu aplicativo lógico, como um gatilho de solicitação. No designer do aplicativo lógico, adicione um gatilho e, em seguida, adicione uma ação ao seu aplicativo lógico.

3.  Na caixa de pesquisa, digite "X12" para o filtro. Selecione **X12 para a mensagem X12 pelo nome do contrato** ou **X12-Codifique para X12 mensagem por identidades**.
   
    ![Pesquise por "X12"](./media/logic-apps-enterprise-integration-x12-encode/x12decodeimage1.png) 

3. Se você não tiver criado anteriormente nenhuma conexão com sua conta de integração, será solicitado a criá-la agora. Nomeie sua conexão e selecione a conta de integração que você deseja conectar. 
   
    ![conexão da conta de integração](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage1.png)

    As propriedades com um asterisco são obrigatórias.

    | Propriedade | Detalhes |
    | --- | --- |
    | Nome da conexão * |Insira qualquer nome para a conexão. |
    | Conta de integração * |Insira um nome para sua conta de integração. Verifique se sua conta de integração e o aplicativo lógico estão no mesmo local do Azure. |

5.  Quando terminar, os detalhes da conexão deverão ser semelhantes a este exemplo. Para concluir a criação da conexão, escolha **criar**.

    ![conexão da conta de integração criada](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage2.png)

    Sua conexão foi criada agora.

    ![detalhes de conexão da conta de integração](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage3.png) 

#### <a name="encode-x12-messages-by-agreement-name"></a>Codificar mensagens X12 por nome do contrato

Se você optar por codificar mensagens X12 por nome do contrato, abra o **nome da lista de contratos X12** , insira ou selecione seu contrato X12 existente. Insira a mensagem XML a ser codificada.

![Insira o nome do contrato X12 e a mensagem XML para codificar](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage4.png)

#### <a name="encode-x12-messages-by-identities"></a>Codificar mensagens X12 por identidades

Se você optar por codificar mensagens X12 por identidades, insira o identificador do remetente, o qualificador do remetente, o identificador do receptor e o qualificador do receptor, conforme configurado no seu contrato do X12. Selecione a mensagem XML a ser codificada.
   
![Fornecer identidades para remetente e destinatário, selecione a mensagem XML a ser codificada](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage5.png) 

## <a name="x12-encode-details"></a>Detalhes de codificação do X12

O conector de codificação X12 executa estas tarefas:

* Resolução do contrato por correspondência das propriedades de contexto do remetente e do destinatário.
* Serializa o intercâmbio EDI, convertendo mensagens codificadas em XML em conjuntos de transações EDI no intercâmbio.
* Aplica o cabeçalho do conjunto de transações e segmentos de trailer
* Gera um número de controle de intercâmbio, um número de controle de grupo e um número de controle de conjunto de transações para cada intercâmbio de saída
* Substitui os separadores nos dados de carga
* Valida o EDI e as propriedades específicas do parceiro
  * Validação de esquema dos elementos de dados do conjunto de transações em relação ao esquema da mensagem
  * Validação de EDI executada em elementos de dados do conjunto de transações.
  * Validação estendida executada em elementos de dados do conjunto de transações
* Solicita uma confirmação técnica e/ou funcional (se configurada).
  * Uma confirmação técnica é gerada como resultado da validação do cabeçalho. A confirmação técnica relata o status do processamento de um cabeçalho e trailer do intercâmbio pelo destinatário do endereço
  * Uma confirmação funcional é gerada como resultado da validação do corpo. A confirmação funcional relata cada erro encontrado durante o processamento do documento recebido

## <a name="view-the-swagger"></a>Exibir o Swagger
Consulte os [detalhes do Swagger](/connectors/x12/). 

## <a name="next-steps"></a>Passos seguintes
[Saiba mais sobre o Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack") 


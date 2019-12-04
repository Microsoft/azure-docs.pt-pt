---
title: Codificar mensagens EDIFACT
description: Validar EDI e gerar XML com o codificador de mensagem EDIFACT para aplicativos lógicos do Azure com Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, divswa, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: 257cbd0b1a68ddd2b16235e6f8dec5d5b0eb10e2
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74790657"
---
# <a name="encode-edifact-messages-for-azure-logic-apps-with-enterprise-integration-pack"></a>Codificar mensagens EDIFACT para aplicativos lógicos do Azure com Enterprise Integration Pack

Com o conector de mensagem EDIFACT de codificação, você pode validar o EDI e as propriedades específicas de parceiro, gerar um documento XML para cada conjunto de transações e solicitar uma confirmação técnica, uma confirmação funcional ou ambas.
Para usar esse conector, você deve adicionar o conector a um gatilho existente em seu aplicativo lógico.

## <a name="before-you-start"></a>Antes de começar

Aqui estão os itens de que você precisa:

* Uma conta do Azure; Você pode criar uma [conta gratuita](https://azure.microsoft.com/free)
* Uma [conta de integração](logic-apps-enterprise-integration-create-integration-account.md) que já está definida e associada à sua assinatura do Azure. Você deve ter uma conta de integração para usar o conector de mensagem EDIFACT de codificação. 
* Pelo menos dois [parceiros](logic-apps-enterprise-integration-partners.md) que já estão definidos em sua conta de integração
* Um [contrato do EDIFACT](logic-apps-enterprise-integration-edifact.md) que já está definido em sua conta de integração

## <a name="encode-edifact-messages"></a>Codificar mensagens EDIFACT

1. [Crie um aplicativo lógico](quickstart-create-first-logic-app-workflow.md).

2. O conector de mensagem EDIFACT de codificação não tem gatilhos, portanto, você deve adicionar um gatilho para iniciar seu aplicativo lógico, como um gatilho de solicitação. No designer do aplicativo lógico, adicione um gatilho e, em seguida, adicione uma ação ao seu aplicativo lógico.

3.  Na caixa de pesquisa, digite "EDIFACT" como filtro. Selecione **codificar mensagem EDIFACT pelo nome do contrato** ou **codificar a mensagem EDIFACT por identidades**.
   
    ![Pesquisar EDIFACT](media/logic-apps-enterprise-integration-edifact-encode/edifactdecodeimage1.png)  

3. Se você não tiver criado anteriormente nenhuma conexão com sua conta de integração, será solicitado a criá-la agora. Nomeie sua conexão e selecione a conta de integração que você deseja conectar.

    ![criar conexão de conta de integração](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage1.png)  

    As propriedades com um asterisco são obrigatórias.

    | Propriedade | Detalhes |
    | --- | --- |
    | Nome da conexão * |Insira qualquer nome para a conexão. |
    | Conta de integração * |Insira um nome para sua conta de integração. Verifique se sua conta de integração e o aplicativo lógico estão no mesmo local do Azure. |

5.  Quando terminar, os detalhes da conexão deverão ser semelhantes a este exemplo. Para concluir a criação da conexão, escolha **criar**.

    ![detalhes de conexão da conta de integração](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage2.png)

    Sua conexão foi criada agora.

    ![conexão da conta de integração criada](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage4.png)

#### <a name="encode-edifact-message-by-agreement-name"></a>Codificar mensagem EDIFACT pelo nome do contrato

Se você optar por codificar mensagens EDIFACT por nome do contrato, abra o **nome da lista de contratos EDIFACT** , insira ou selecione o nome do contrato EDIFACT. Insira a mensagem XML a ser codificada.

![Insira o nome do contrato EDIFACT e a mensagem XML para codificar](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage6.png)

#### <a name="encode-edifact-message-by-identities"></a>Codificar mensagem EDIFACT por identidades

Se você optar por codificar mensagens EDIFACT por identidades, insira o identificador do remetente, o qualificador do remetente, o identificador do receptor e o qualificador do receptor, conforme configurado no seu contrato do EDIFACT. Selecione a mensagem XML a ser codificada.

![Fornecer identidades para remetente e destinatário, selecione a mensagem XML a ser codificada](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage7.png)

## <a name="edifact-encode-details"></a>Detalhes de codificação do EDIFACT

O conector EDIFACT de codificação executa estas tarefas: 

* Resolver o contrato combinando o qualificador do remetente & o identificador e o qualificador do receptor e o identificador
* Serializa o intercâmbio EDI, convertendo mensagens codificadas em XML em conjuntos de transações EDI no intercâmbio.
* Aplica o cabeçalho do conjunto de transações e segmentos de trailer
* Gera um número de controle de intercâmbio, um número de controle de grupo e um número de controle de conjunto de transações para cada intercâmbio de saída
* Substitui os separadores nos dados de carga
* Valida o EDI e as propriedades específicas do parceiro
  * Validação de esquema dos elementos de dados do conjunto de transações em relação ao esquema da mensagem.
  * Validação de EDI executada em elementos de dados do conjunto de transações.
  * Validação estendida executada em elementos de dados do conjunto de transações
* Gera um documento XML para cada conjunto de transações.
* Solicita uma confirmação técnica e/ou funcional (se configurada).
  * Como uma confirmação técnica, a mensagem CONTRL indica o recebimento de um intercâmbio.
  * Como uma confirmação funcional, a mensagem CONTRL indica a aceitação ou rejeição do intercâmbio, grupo ou mensagem recebido, com uma lista de erros ou funcionalidade sem suporte

## <a name="view-swagger-file"></a>Exibir arquivo do Swagger
Para exibir os detalhes do Swagger para o conector do EDIFACT, consulte [EDIFACT](/connectors/edifact/).

## <a name="next-steps"></a>Passos seguintes
[Saiba mais sobre o Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack") 


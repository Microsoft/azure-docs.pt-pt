---
title: Descodificar mensagens EDIFACT - Azure Logic Apps | Documentos da Microsoft
description: Validar EDI e gerar confirmações de receção com o descodificador de mensagem EDIFACT para o Azure Logic Apps com o Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: jonfan, divswa, LADocs
ms.topic: article
ms.assetid: 0e61501d-21a2-4419-8c6c-88724d346e81
ms.date: 01/27/2017
ms.openlocfilehash: ccad6eab68fff0891ba287a076692f9437495a4c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097832"
---
# <a name="decode-edifact-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Descodificar mensagens EDIFACT para o Azure Logic Apps com o Enterprise Integration Pack

Com o conector de mensagem de descodificação EDIFACT, pode validar EDI e propriedades específicas de parceiro, dividir intercâmbios em conjuntos de transações ou preservar intercâmbios todos e gerar confirmações para transações processadas. Para utilizar este conector, tem de adicionar o conector para um acionador existente na sua aplicação lógica.

## <a name="before-you-start"></a>Antes de começar

Eis os itens que precisa:

* Uma conta do Azure; Pode criar um [conta gratuita](https://azure.microsoft.com/free)
* Uma [conta de integração](logic-apps-enterprise-integration-create-integration-account.md) que já definida e associada à sua subscrição do Azure. Tem de ter uma conta de integração para utilizar o conector de mensagem de descodificação EDIFACT. 
* Pelo menos dois [parceiros](logic-apps-enterprise-integration-partners.md) que já estão definidas na sua conta de integração
* Uma [contrato EDIFACT](logic-apps-enterprise-integration-edifact.md) que já está definido na sua conta de integração

## <a name="decode-edifact-messages"></a>Descodificar mensagens EDIFACT

1. [Criar uma aplicação lógica](quickstart-create-first-logic-app-workflow.md).

2. O conector de mensagem de descodificação EDIFACT não tem acionadores, para que deve adicionar um acionador para iniciar a sua aplicação lógica, como um acionador de pedido. No Estruturador da aplicação lógica, adicionar um acionador e, em seguida, adicione uma ação à sua aplicação lógica.

3. Na caixa de pesquisa, introduza "EDIFACT" como o filtro. Selecione **descodificar mensagem EDIFACT**.
   
    ![pesquisa de EDIFACT](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage1.png)

3. Se não tiver criado anteriormente todas as ligações à sua conta de integração, lhe for pedido para criar essa conexão agora. Nomeie a sua ligação e selecione a conta de integração que pretende ligar.
   
    ![criar conta de integração](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage2.png)

    Propriedades com um asterisco são necessárias.

    | Propriedade | Detalhes |
    | --- | --- |
    | Nome da ligação * |Introduza qualquer nome para a sua ligação. |
    | Conta de integração * |Introduza um nome para a sua conta de integração. Certifique-se de que a sua aplicação de lógica e a conta de integração estão na mesma localização do Azure. |

4. Quando estiver pronto para concluir a criação da sua ligação, escolha **criar**. Detalhes da sua ligação devem ter um aspeto semelhantes a este exemplo:

    ![Detalhes da conta de integração](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage3.png)  

5. Depois de criar a ligação, conforme mostrado neste exemplo, selecione a mensagem de ficheiro simples EDIFACT a descodificar.

    ![ligação da conta de integração criada](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage4.png)  

    Por exemplo:

    ![Selecione a mensagem de ficheiro simples EDIFACT para descodificação](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage5.png)  

## <a name="edifact-decoder-details"></a>Detalhes de Decodificador EDIFACT

O conector de descodificação EDIFACT executa estas tarefas: 

* Valida o envelope no contrato de parceiro de negociação.
* Resolve o contrato pela correspondência entre o qualificador do remetente e o identificador e a qualificador do destinatário e o identificador.
* Divide um intercâmbio em várias transações quando o intercâmbio tem mais do que uma transação com base no contrato receber a configuração de definições.
* Desmonta o intercâmbio.
* Valida o EDI e propriedades específicas de parceiros, incluindo:
  * Validação da estrutura do envelope de intercâmbio
  * Validação de esquema do envelope no esquema de controlo
  * Validação de esquema dos elementos de dados do conjunto de transações no esquema de mensagem
  * Validação de EDI efetuada em elementos de dados do conjunto de transações
* Verifica que os números de controlo de intercâmbio, grupo e transação conjunto não são duplicados (se configurada) 
  * Verifica o número de controlo de intercâmbio contra intercâmbios anteriormente recebidos. 
  * Verifica o número de controlo de grupo em relação a outros números de controlo de grupo no intercâmbio. 
  * Verifica o que número de controlo do conjunto das transações em relação a outros números de controlo do conjunto de transações desse grupo.
* Divide o intercâmbio em conjuntos de transação ou preserva o intercâmbio todo:
  * Dividir intercâmbio como conjuntos de transação - suspender conjuntos transação com erro: Intercâmbio de divisões numa transação define e analisa cada conjunto de transações. 
  X12 Decodificação ação produz apenas essas transações define que a falha de validação para `badMessages`e saídas as transações restantes define como `goodMessages`.
  * Dividir intercâmbio como conjuntos de transação - suspender intercâmbio com erro: Intercâmbio de divisões numa transação define e analisa cada conjunto de transações. 
  Se um ou mais transações define no intercâmbio a falha de validação, a ação de Decodificação produz todos os a transação define nesse intercâmbio de X12 `badMessages`.
  * Preservar intercâmbio - suspender conjuntos transação com erro: Preservar o intercâmbio e processar o intercâmbio em lote inteiro. 
  X12 Decodificação ação produz apenas essas transações define que a falha de validação para `badMessages`e saídas as transações restantes define como `goodMessages`.
  * Preservar intercâmbio - suspender intercâmbio com erro: Preservar o intercâmbio e processar o intercâmbio em lote inteiro. 
  Se um ou mais transações define no intercâmbio a falha de validação, a ação de Decodificação produz todos os a transação define nesse intercâmbio de X12 `badMessages`.
* Gera uma técnica (controlo) e/ou funcional confirmação (se configurada).
  * Uma confirmação técnico ou o ACK CONTRL reporta os resultados de uma verificação sintática do intercâmbio recebido completa.
  * Uma confirmação funcional reconhece aceitar ou rejeitar um intercâmbio recebido ou um grupo

## <a name="view-swagger-file"></a>Ver o ficheiro Swagger
Para ver os detalhes de Swagger para o conector do EDIFACT, consulte [EDIFACT](/connectors/edifact/).

## <a name="next-steps"></a>Passos Seguintes
[Saiba mais sobre o Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack") 


---
title: Decodificar mensagens EDIFACT
description: Validar EDI e gerar confirmações com o decodificador de mensagem EDIFACT para aplicativos lógicos do Azure com Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, divswa, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: 78c0d20c0f32a6d63d134e958b30d38fe11fcc5c
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74790662"
---
# <a name="decode-edifact-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Decodificar mensagens EDIFACT para aplicativos lógicos do Azure com o Enterprise Integration Pack

Com o conector decodificar mensagem EDIFACT, você pode validar EDI e propriedades específicas de parceiro, dividir intercâmbios em conjuntos de transações ou preservar intercâmbios inteiros e gerar confirmações para transações processadas. Para usar esse conector, você deve adicionar o conector a um gatilho existente em seu aplicativo lógico.

## <a name="before-you-start"></a>Antes de começar

Aqui estão os itens de que você precisa:

* Uma conta do Azure; Você pode criar uma [conta gratuita](https://azure.microsoft.com/free)
* Uma [conta de integração](logic-apps-enterprise-integration-create-integration-account.md) que já está definida e associada à sua assinatura do Azure. Você deve ter uma conta de integração para usar o conector de mensagem EDIFACT de decodificação. 
* Pelo menos dois [parceiros](logic-apps-enterprise-integration-partners.md) que já estão definidos em sua conta de integração
* Um [contrato do EDIFACT](logic-apps-enterprise-integration-edifact.md) que já está definido em sua conta de integração

## <a name="decode-edifact-messages"></a>Decodificar mensagens EDIFACT

1. [Crie um aplicativo lógico](quickstart-create-first-logic-app-workflow.md).

2. O conector de mensagem EDIFACT de decodificação não tem gatilhos, portanto, você deve adicionar um gatilho para iniciar seu aplicativo lógico, como um gatilho de solicitação. No designer do aplicativo lógico, adicione um gatilho e, em seguida, adicione uma ação ao seu aplicativo lógico.

3. Na caixa de pesquisa, digite "EDIFACT" como filtro. Selecione **decodificar mensagem EDIFACT**.
   
    ![Pesquisar EDIFACT](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage1.png)

3. Se você não tiver criado anteriormente nenhuma conexão com sua conta de integração, será solicitado a criá-la agora. Nomeie sua conexão e selecione a conta de integração que você deseja conectar.
   
    ![criar conta de integração](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage2.png)

    As propriedades com um asterisco são obrigatórias.

    | Propriedade | Detalhes |
    | --- | --- |
    | Nome da conexão * |Insira qualquer nome para a conexão. |
    | Conta de integração * |Insira um nome para sua conta de integração. Verifique se sua conta de integração e o aplicativo lógico estão no mesmo local do Azure. |

4. Quando você terminar de concluir a criação da conexão, escolha **criar**. Os detalhes da conexão devem ser semelhantes a este exemplo:

    ![detalhes da conta de integração](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage3.png)  

5. Depois que a conexão for criada, conforme mostrado neste exemplo, selecione a mensagem de arquivo simples EDIFACT a ser decodificada.

    ![conexão da conta de integração criada](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage4.png)  

    Por exemplo:

    ![Selecione a mensagem de arquivo simples EDIFACT para decodificação](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage5.png)  

## <a name="edifact-decoder-details"></a>Detalhes do decodificador EDIFACT

O conector decodificar EDIFACT executa estas tarefas: 

* Valida o envelope em relação ao acordo entre parceiros comerciais.
* Resolve o contrato combinando o identificador de & do qualificador de remetente e o identificador de & do qualificador de receptor.
* Divide um intercâmbio em várias transações quando o intercâmbio tem mais de uma transação com base na configuração de configurações de recebimento do contrato.
* Desmonta o intercâmbio.
* Valida o EDI e as propriedades específicas do parceiro, incluindo:
  * Validação da estrutura do envelope do intercâmbio
  * Validação de esquema do envelope em relação ao esquema de controle
  * Validação de esquema dos elementos de dados do conjunto de transações em relação ao esquema da mensagem
  * Validação de EDI executada em elementos de dados do conjunto de transações
* Verifica se os números de controle de conjunto de intercâmbio, grupo e transação não são duplicados (se configurados) 
  * Verifica o número de controle de intercâmbio em relação às trocas recebidas anteriormente. 
  * Verifica o número de controle de grupo em relação a outros números de controle de grupo no intercâmbio. 
  * Verifica o número de controle do conjunto de transações em relação a outros números de controle do conjunto de transações nesse grupo.
* Divide o intercâmbio em conjuntos de transação ou preserva todo o intercâmbio:
  * Dividir intercâmbio como conjuntos de transações – suspender conjuntos de transação com erro: divide o intercâmbio em conjuntos de transações e analisa cada conjunto de transações. 
  A ação de decodificação X12 gera apenas os conjuntos de transações que falham na validação para `badMessages`e gera os conjuntos de transações restantes para `goodMessages`.
  * Dividir o intercâmbio como conjuntos de transações – suspender intercâmbio com erro: divide o intercâmbio em conjuntos de transações e analisa cada conjunto de transações. 
  Se um ou mais conjuntos de transações no intercâmbio falharem na validação, a ação de decodificação X12 produzirá todos os conjuntos de transações nesse intercâmbio para `badMessages`.
  * Preservar intercâmbio – suspender conjuntos de transação com erro: Preserve o intercâmbio e processe todo o intercâmbio em lote. 
  A ação de decodificação X12 gera apenas os conjuntos de transações que falham na validação para `badMessages`e gera os conjuntos de transações restantes para `goodMessages`.
  * Preservar intercâmbio-suspender intercâmbio com erro: Preserve o intercâmbio e processe todo o intercâmbio em lote. 
  Se um ou mais conjuntos de transações no intercâmbio falharem na validação, a ação de decodificação X12 produzirá todos os conjuntos de transações nesse intercâmbio para `badMessages`.
* Gera uma confirmação técnica (controle) e/ou funcional (se configurada).
  * Uma confirmação técnica ou a confirmação CONTRL relata os resultados de uma verificação sintática do intercâmbio completo recebido.
  * Uma confirmação funcional reconhece aceitar ou rejeitar um intercâmbio recebido ou um grupo

## <a name="view-swagger-file"></a>Exibir arquivo do Swagger
Para exibir os detalhes do Swagger para o conector do EDIFACT, consulte [EDIFACT](/connectors/edifact/).

## <a name="next-steps"></a>Passos seguintes
[Saiba mais sobre o Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack") 


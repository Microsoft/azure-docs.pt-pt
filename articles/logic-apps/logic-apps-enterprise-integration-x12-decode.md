---
title: Decodificar mensagens X12
description: Validar EDI e gerar confirmações com o decodificador de mensagem X12 em aplicativos lógicos do Azure com Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: 918516a5629f8570d54c641ffc29f2367937266f
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792369"
---
# <a name="decode-x12-messages-in-azure-logic-apps-with-enterprise-integration-pack"></a>Decodificar mensagens X12 em aplicativos lógicos do Azure com Enterprise Integration Pack

Com o conector decodificar mensagem X12, você pode validar o envelope em relação a um acordo entre parceiros comerciais, validar propriedades específicas de parceiro e EDI, dividir intercâmbios em conjuntos de transações ou preservar intercâmbios inteiros e gerar confirmações para transações processadas. Para usar esse conector, você deve adicionar o conector a um gatilho existente em seu aplicativo lógico.

## <a name="before-you-start"></a>Antes de começar

Aqui estão os itens de que você precisa:

* Uma conta do Azure; Você pode criar uma [conta gratuita](https://azure.microsoft.com/free)
* Uma [conta de integração](logic-apps-enterprise-integration-create-integration-account.md) que já está definida e associada à sua assinatura do Azure. Você deve ter uma conta de integração para usar o conector de mensagem X12 de decodificação.
* Pelo menos dois [parceiros](logic-apps-enterprise-integration-partners.md) que já estão definidos em sua conta de integração
* Um [contrato do X12](logic-apps-enterprise-integration-x12.md) que já está definido em sua conta de integração

## <a name="decode-x12-messages"></a>Decodificar mensagens X12

1. [Crie um aplicativo lógico](quickstart-create-first-logic-app-workflow.md).

2. O conector de mensagem X12 de decodificação não tem gatilhos, portanto, você deve adicionar um gatilho para iniciar seu aplicativo lógico, como um gatilho de solicitação. No designer do aplicativo lógico, adicione um gatilho e, em seguida, adicione uma ação ao seu aplicativo lógico.

3.  Na caixa de pesquisa, digite "X12" para o filtro. Selecione **X12-decodificar mensagem X12**.
   
    ![Pesquise por "X12"](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage1.png)  

3. Se você não tiver criado anteriormente nenhuma conexão com sua conta de integração, será solicitado a criá-la agora. Nomeie sua conexão e selecione a conta de integração que você deseja conectar. 

    ![Fornecer detalhes de conexão da conta de integração](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage4.png)

    As propriedades com um asterisco são obrigatórias.

    | Propriedade | Detalhes |
    | --- | --- |
    | Nome da conexão * |Insira qualquer nome para a conexão. |
    | Conta de integração * |Insira um nome para sua conta de integração. Verifique se sua conta de integração e o aplicativo lógico estão no mesmo local do Azure. |

5.  Quando terminar, os detalhes da conexão deverão ser semelhantes a este exemplo. Para concluir a criação da conexão, escolha **criar**.
   
    ![detalhes de conexão da conta de integração](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage5.png) 

6. Depois que a conexão for criada, conforme mostrado neste exemplo, selecione a mensagem de arquivo simples X12 a ser decodificada.

    ![conexão da conta de integração criada](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage6.png) 

    Por exemplo:

    ![Selecione a mensagem de arquivo simples X12 para decodificação](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage7.png) 

   > [!NOTE]
   > O conteúdo ou a carga da mensagem real para a matriz de mensagens, boa ou ruim, é codificado em base64. Portanto, você deve especificar uma expressão que processa esse conteúdo.
   > Aqui está um exemplo que processa o conteúdo como XML que você pode inserir na exibição de código ou usando o construtor de expressões no designer.
   > ``` json
   > "content": "@xml(base64ToBinary(item()?['Payload']))"
   > ```
   > ![Exemplo de conteúdo](media/logic-apps-enterprise-integration-x12-decode/content-example.png)
   >


## <a name="x12-decode-details"></a>Detalhes de decodificação X12

O conector de decodificação X12 executa estas tarefas:

* Valida o envelope em relação ao acordo entre parceiros comerciais
* Valida o EDI e as propriedades específicas do parceiro
  * Validação estrutural EDI e validação de esquema estendido
  * Validação da estrutura do envelope de intercâmbio.
  * Validação de esquema do envelope em relação ao esquema de controle.
  * Validação de esquema dos elementos de dados do conjunto de transações em relação ao esquema da mensagem.
  * Validação de EDI executada em elementos de dados do conjunto de transações 
* Verifica se os números de controle de conjunto de intercâmbio, grupo e transação não são duplicatas
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
* Gera uma confirmação técnica e/ou funcional (se configurada).
  * Uma confirmação técnica é gerada como resultado da validação do cabeçalho. A confirmação técnica relata o status do processamento de um cabeçalho de intercâmbio e o trailer pelo destinatário do endereço.
  * Uma confirmação funcional é gerada como resultado da validação do corpo. A confirmação funcional relata cada erro encontrado durante o processamento do documento recebido

## <a name="view-the-swagger"></a>Exibir o Swagger
Consulte os [detalhes do Swagger](/connectors/x12/). 

## <a name="next-steps"></a>Passos seguintes
[Saiba mais sobre o Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack") 


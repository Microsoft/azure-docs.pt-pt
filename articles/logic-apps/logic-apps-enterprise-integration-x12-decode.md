---
title: Descodificação de X12 mensagens - Azure Logic Apps | Documentos da Microsoft
description: Validar EDI e gerar confirmações de receção com X12 descodificador de mensagem no Azure Logic Apps com o Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: jonfan, divswa, LADocs
ms.topic: article
ms.assetid: 4fd48d2d-2008-4080-b6a1-8ae183b48131
ms.date: 01/27/2017
ms.openlocfilehash: 4a19462f4f849602fd14fe1204f1c7e3c01e6ec4
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64701452"
---
# <a name="decode-x12-messages-in-azure-logic-apps-with-enterprise-integration-pack"></a>Descodificação de X12 mensagens no Azure Logic Apps com o Enterprise Integration Pack

Com o conector de mensagem de Decodificação X12, pode validar o envelope em relação a um contrato de parceiro comercial, validar EDI e propriedades específicas de parceiro, dividir intercâmbios em conjuntos de transações ou preservar intercâmbios todos e gerar confirmações para transações processadas. Para utilizar este conector, tem de adicionar o conector para um acionador existente na sua aplicação lógica.

## <a name="before-you-start"></a>Antes de começar

Eis os itens que precisa:

* Uma conta do Azure; Pode criar um [conta gratuita](https://azure.microsoft.com/free)
* Uma [conta de integração](logic-apps-enterprise-integration-create-integration-account.md) que já definida e associada à sua subscrição do Azure. Tem de ter uma conta de integração para utilizar o conector de mensagem de Decodificação X12.
* Pelo menos dois [parceiros](logic-apps-enterprise-integration-partners.md) que já estão definidas na sua conta de integração
* Uma [X12 contrato](logic-apps-enterprise-integration-x12.md) que já está definido na sua conta de integração

## <a name="decode-x12-messages"></a>Descodificação de X12 mensagens

1. [Criar uma aplicação lógica](quickstart-create-first-logic-app-workflow.md).

2. O conector de mensagem de Decodificação X12 não tem acionadores, para que deve adicionar um acionador para iniciar a sua aplicação lógica, como um acionador de pedido. No Estruturador da aplicação lógica, adicionar um acionador e, em seguida, adicione uma ação à sua aplicação lógica.

3.  Na caixa de pesquisa, introduza "x12" para o filtro. Selecione **X12 de descodificação de X12-mensagem**.
   
    ![Procure "x12"](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage1.png)  

3. Se não tiver criado anteriormente todas as ligações à sua conta de integração, lhe for pedido para criar essa conexão agora. Nomeie a sua ligação e selecione a conta de integração que pretende ligar. 

    ![Forneça detalhes de ligação da conta de integração](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage4.png)

    Propriedades com um asterisco são necessárias.

    | Propriedade | Detalhes |
    | --- | --- |
    | Nome da ligação * |Introduza qualquer nome para a sua ligação. |
    | Conta de integração * |Introduza um nome para a sua conta de integração. Certifique-se de que a sua aplicação de lógica e a conta de integração estão na mesma localização do Azure. |

5.  Quando tiver terminado, seus detalhes de ligação devem ter um aspeto semelhantes a este exemplo. Para acabar de criar a ligação, escolha **criar**.
   
    ![detalhes de ligação da conta de integração](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage5.png) 

6. Após a ligação é criada, como mostrado neste exemplo, a mensagem de ficheiro simples selecione X12 a descodificar.

    ![ligação da conta de integração criada](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage6.png) 

    Por exemplo:

    ![Mensagem de ficheiro à decodificação simples X12 selecione](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage7.png) 

   > [!NOTE]
   > O conteúdo real da mensagem ou o payload da matriz de mensagem, bom ou ruim, é codificado em base64. Por isso, tem de especificar uma expressão que processa este conteúdo.
   > Eis um exemplo que processa o conteúdo XML que pode introduzir na vista de código ou com o construtor de expressões no designer.
   > ``` json
   > "content": "@xml(base64ToBinary(item()?['Payload']))"
   > ```
   > ![Exemplo de conteúdo](media/logic-apps-enterprise-integration-x12-decode/content-example.png)
   >


## <a name="x12-decode-details"></a>X12 descodificar detalhes

X12 conector de Decodificação executa estas tarefas:

* Valida o envelope no contrato de parceiro de negociação
* Valida o EDI e propriedades específicas de parceiro
  * Validação de EDI estrutural e a validação de esquema expandido
  * Validação da estrutura do envelope intercâmbio.
  * Validação de esquema do envelope no esquema de controlo.
  * Validação de esquema dos elementos de dados do conjunto de transações no esquema de mensagem.
  * Validação de EDI efetuada em elementos de dados do conjunto de transações 
* Verifica se os números de controlo de intercâmbio, grupo e transação conjunto não são duplicados
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
* Gera uma confirmação técnica e/ou funcional (se configurada).
  * Gera uma confirmação de técnicas como resultado de validação de cabeçalho. A confirmação técnica comunica o estado do processamento de um cabeçalho de intercâmbio e as informações finais pelo recetor de endereço.
  * Gera uma confirmação funcional como resultado de validação de corpo. A confirmação funcional relatórios cada erro encontrado ao processar o documento recebido

## <a name="view-the-swagger"></a>Ver o swagger
Consulte a [detalhes de swagger](/connectors/x12/). 

## <a name="next-steps"></a>Passos Seguintes
[Saiba mais sobre o Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack") 


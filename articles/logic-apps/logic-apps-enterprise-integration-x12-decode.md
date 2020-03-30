---
title: Descodificar mensagens X12
description: Valide EDI e gere reconhecimentos com descodificador de mensagens X12 em Aplicações lógicas Azure com Pacote de Integração Empresarial
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: 918516a5629f8570d54c641ffc29f2367937266f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792369"
---
# <a name="decode-x12-messages-in-azure-logic-apps-with-enterprise-integration-pack"></a>Descodificar mensagens X12 em Aplicações Lógicas Azure com Pacote de Integração Empresarial

Com o conector de mensagens Decode X12, pode validar o envelope com um acordo de parceiro comercial, validar propriedades EDI e específicas de parceiros, dividir intercâmbios em conjuntos de transações ou preservar intercâmbios inteiros, e gerar reconhecimentos para transações processadas. Para utilizar este conector, deve adicionar o conector a um gatilho existente na sua aplicação lógica.

## <a name="before-you-start"></a>Antes de começar

Aqui estão os itens que precisa:

* Uma conta Azure; pode criar uma [conta gratuita](https://azure.microsoft.com/free)
* Uma conta de [integração](logic-apps-enterprise-integration-create-integration-account.md) que já está definida e associada à sua subscrição Azure. Deve ter uma conta de integração para utilizar o conector de mensagens Decode X12.
* Pelo menos dois [parceiros](logic-apps-enterprise-integration-partners.md) que já estão definidos na sua conta de integração
* Um [acordo X12](logic-apps-enterprise-integration-x12.md) que já está definido na sua conta de integração

## <a name="decode-x12-messages"></a>Descodificar mensagens X12

1. [Criar uma aplicação lógica.](quickstart-create-first-logic-app-workflow.md)

2. O conector de mensagens Decode X12 não tem gatilhos, pelo que deve adicionar um gatilho para iniciar a sua aplicação lógica, como um gatilho de Pedido. No Logic App Designer, adicione um gatilho e adicione uma ação à sua aplicação lógica.

3.  Na caixa de pesquisa, introduza "x12" para o filtro. Selecione **X12 - Descodificar a mensagem X12**.
   
    ![Pesquisa por "x12"](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage1.png)  

3. Se não criou anteriormente quaisquer ligações à sua conta de integração, é-lhe pedido que crie essa ligação agora. Nomeie a sua ligação e selecione a conta de integração que pretende ligar. 

    ![Fornecer detalhes de conexão de conta de integração](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage4.png)

    São necessárias propriedades com asterisco.

    | Propriedade | Detalhes |
    | --- | --- |
    | Nome da ligação * |Insira qualquer nome para a sua ligação. |
    | Conta de Integração * |Insira um nome para a sua conta de integração. Certifique-se de que a sua conta de integração e app lógica estão na mesma localização Azure. |

5.  Quando terminar, os seus dados de ligação devem ser semelhantes a este exemplo. Para terminar de criar a sua ligação, escolha **Criar**.
   
    ![detalhes de conexão de conta de integração](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage5.png) 

6. Depois da sua ligação ser criada, como mostra este exemplo, selecione a mensagem de ficheiro plano X12 para descodificar.

    ![conexão conta de integração criada](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage6.png) 

    Por exemplo:

    ![Selecione mensagem de ficheiro plano X12 para descodificação](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage7.png) 

   > [!NOTE]
   > O conteúdo real da mensagem ou carga útil para o array de mensagem, bom ou mau, é codificado pela base64. Por isso, deve especificar uma expressão que processa este conteúdo.
   > Aqui está um exemplo que processa o conteúdo como XML que pode introduzir na vista de código ou usando o construtor de expressão no designer.
   > ``` json
   > "content": "@xml(base64ToBinary(item()?['Payload']))"
   > ```
   > ![Exemplo de conteúdo](media/logic-apps-enterprise-integration-x12-decode/content-example.png)
   >


## <a name="x12-decode-details"></a>Detalhes de descodificação X12

O conector X12 Decode executa estas tarefas:

* Valida o envelope contra o acordo de parceiro comercial
* Valida propriedades EDI e específicas de parceiros
  * Validação estrutural edi, e validação de esquemas alargado
  * Validação da estrutura do envelope de intercâmbio.
  * Validação de esquemas do envelope contra o esquema de controlo.
  * Validação schema dos elementos de dados definidos de transação contra o esquema da mensagem.
  * Validação EDI realizada em elementos de dados definidos por transações 
* Verifica que os números de controlo de intercâmbio, grupo e de conjunto de transações não são duplicados
  * Verifica o número de controlo de intercâmbio contra as trocas anteriormente recebidas.
  * Verifica o número de controlo do grupo com outros números de controlo do grupo no intercâmbio.
  * Verifica o número de controlo definido pela transação contra outros números de controlo definidos por transações nesse grupo.
* Divide o intercâmbio em conjuntos de transações ou preserva todo o intercâmbio:
  * Divisões como conjuntos de transações - suspenda os conjuntos de transações por erro: Divide o intercâmbio em conjuntos de transações e analisa cada conjunto de transações. 
  O X12 Decode produz apenas os conjuntos `badMessages`de transações que falham `goodMessages`na validação e as saídas que as restantes transações se fixam para .
  * Divisões como conjuntos de transações - suspenda o intercâmbio por erro: Divide o intercâmbio em conjuntos de transações e analisa cada conjunto de transações. 
  Se um ou mais conjuntos de transações na validação de falhas de troca, a `badMessages`ação X12 Decode produz todos os conjuntos de transações nesse intercâmbio para .
  * Preserve o Interchange - suspenda os conjuntos de transações por erro: Preservar o intercâmbio e processar todo o intercâmbio em lota. 
  O X12 Decode produz apenas os conjuntos `badMessages`de transações que falham `goodMessages`na validação e as saídas que as restantes transações se fixam para .
  * Preservar o Intercâmbio - suspender o intercâmbio por erro: Preservar o intercâmbio e processar todo o intercâmbio em lota. 
  Se um ou mais conjuntos de transações na validação de falhas de troca, a `badMessages`ação X12 Decode produz todos os conjuntos de transações nesse intercâmbio para . 
* Gera um reconhecimento técnico e/ou funcional (se configurado).
  * Um reconhecimento técnico gera como resultado da validação do cabeçalho. O reconhecimento técnico informa o estado do processamento de um cabeçalho de intercâmbio e do reboque pelo recetor de endereços.
  * Um Reconhecimento Funcional gera como resultado da validação do corpo. O reconhecimento funcional relata cada erro encontrado durante o processamento do documento recebido

## <a name="view-the-swagger"></a>Veja a arrogância
Veja os detalhes da [arrogância.](/connectors/x12/) 

## <a name="next-steps"></a>Passos seguintes
[Saiba mais sobre o Pacote de Integração Empresarial](../logic-apps/logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Pacote de Integração Empresarial") 


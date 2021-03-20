---
title: Descodificar mensagens X12
description: Validar eDI e gerar reconhecimentos com descodificador de mensagens X12 em Azure Logic Apps com Pacote de Integração Empresarial
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: 918516a5629f8570d54c641ffc29f2367937266f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "74792369"
---
# <a name="decode-x12-messages-in-azure-logic-apps-with-enterprise-integration-pack"></a>Descodificar mensagens X12 no Azure Logic Apps com o Enterprise Integration Pack

Com o conector Descodificar mensagens X12, pode validar o envelope num contrato, validar as propriedades de EDI e de parceiros específicos, dividir intercâmbios em conjuntos de transações ou preservar intercâmbios inteiros e gerar confirmações para transações processadas. Para utilizar este conector, tem de o adicionar a um acionador existente na aplicação lógica.

## <a name="before-you-start"></a>Antes de começar

Aqui estão os itens que precisa:

* Uma conta Azure; pode criar uma [conta gratuita](https://azure.microsoft.com/free)
* Uma conta de [integração](logic-apps-enterprise-integration-create-integration-account.md) já definida e associada à sua subscrição do Azure. Tem de ter uma conta de integração para utilizar o conector de mensagem Decode X12.
* Pelo menos dois [parceiros](logic-apps-enterprise-integration-partners.md) que já estão definidos na sua conta de integração
* Um [acordo X12](logic-apps-enterprise-integration-x12.md) que já está definido na sua conta de integração

## <a name="decode-x12-messages"></a>Descodificar mensagens X12

1. [Crie uma aplicação lógica.](quickstart-create-first-logic-app-workflow.md)

2. O conector de mensagens Decode X12 não tem gatilhos, por isso deve adicionar um gatilho para iniciar a sua aplicação lógica, como um gatilho Request. No Logic App Designer, adicione um gatilho e, em seguida, adicione uma ação à sua aplicação lógica.

3.  Na caixa de pesquisa, introduza "x12" para o seu filtro. Selecione **X12 - Descodificar a mensagem X12**.
   
    ![Pesquisa por "x12"](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage1.png)  

3. Se não criou previamente quaisquer ligações à sua conta de integração, é solicitado que crie essa ligação agora. Nomeie a sua ligação e selecione a conta de integração que pretende ligar. 

    ![Fornecer detalhes de conexão de conta de integração](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage4.png)

    São necessárias propriedades com asterisco.

    | Propriedade | Detalhes |
    | --- | --- |
    | Nome de conexão * |Insira qualquer nome para a sua ligação. |
    | Conta de Integração * |Insira um nome para a sua conta de integração. Certifique-se de que a sua conta de integração e aplicação lógica estão na mesma localização Azure. |

5.  Quando terminar, os seus detalhes de conexão devem ser semelhantes a este exemplo. Para terminar de criar a sua ligação, escolha **Criar**.
   
    ![detalhes da conexão da conta de integração](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage5.png) 

6. Depois de a sua ligação ser criada, como mostrado neste exemplo, selecione a mensagem de ficheiro plano X12 para descodificar.

    ![ligação conta de integração criada](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage6.png) 

    Por exemplo:

    ![Selecione mensagem de ficheiro plano X12 para descodição](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage7.png) 

   > [!NOTE]
   > O conteúdo real da mensagem ou carga útil para o conjunto de mensagens, bom ou mau, está codificado na base64. Então, deve especificar uma expressão que processe este conteúdo.
   > Aqui está um exemplo que processa o conteúdo como XML que pode introduzir na visualização de código ou usando o construtor de expressão no designer.
   > ``` json
   > "content": "@xml(base64ToBinary(item()?['Payload']))"
   > ```
   > ![Exemplo de conteúdo](media/logic-apps-enterprise-integration-x12-decode/content-example.png)
   >


## <a name="x12-decode-details"></a>Detalhes da Descodificação X12

O conector X12 Decode executa estas tarefas:

* Valida o envelope contra o acordo de parceiro comercial
* Valida propriedades EDI e parceiras
  * Validação estrutural do EDI e validação alargada de esquemas
  * Validação da estrutura do envelope de intercâmbio.
  * Validação de esquema do envelope contra o esquema de controlo.
  * Validação de esquema dos elementos de dados definidos por transação contra o esquema de mensagem.
  * Validação do EDI realizada em elementos de dados definidos por transações 
* Verifica que os números de controlo do intercâmbio, do grupo e do conjunto de transações não são duplicados
  * Verifica o número de controlo de intercâmbio contra as trocas previamente recebidas.
  * Verifica o número de controlo do grupo contra outros números de controlo do grupo no intercâmbio.
  * Verifica o número de controlo da transação em número de controlo de outras transações definidas nesse grupo.
* Divide o intercâmbio em conjuntos de transações ou preserva todo o intercâmbio:
  * Split Interchange como conjuntos de transações - suspender conjuntos de transações por erro: Divide o intercâmbio em conjuntos de transações e analisa cada conjunto de transações. 
  O X12 Descodifica as saídas de ação apenas os conjuntos de transações que falham na validação `badMessages` de , e as saídas que as restantes transações definem para `goodMessages` .
  * Split Interchange como conjuntos de transações - suspender o intercâmbio por erro: Divide o intercâmbio em conjuntos de transações e analisa cada conjunto de transações. 
  Se uma ou mais transações se estabelecerem na validação de falhas de câmbio, a ação X12 Descodifica todas as transações nesse intercâmbio para `badMessages` .
  * Preserve Interchange - suspenda os conjuntos de transações por erro: Preserve o intercâmbio e processe todo o intercâmbio em lote. 
  O X12 Descodifica as saídas de ação apenas os conjuntos de transações que falham na validação `badMessages` de , e as saídas que as restantes transações definem para `goodMessages` .
  * Preservar o Intercâmbio - suspender o intercâmbio por erro: Preservar o intercâmbio e processar todo o intercâmbio em lote. 
  Se uma ou mais transações se estabelecerem na validação de falhas de câmbio, a ação X12 Descodifica todas as transações nesse intercâmbio para `badMessages` . 
* Gera um reconhecimento técnico e/ou funcional (se configurado).
  * Um Reconhecimento Técnico gera como resultado da validação do cabeçalho. O reconhecimento técnico informa o estado do processamento de um cabeçalho e reboque de intercâmbio pelo recetor de endereços.
  * Um Reconhecimento Funcional gera como resultado da validação do corpo. O reconhecimento funcional relata cada erro encontrado durante o processamento do documento recebido

## <a name="view-the-swagger"></a>Ver a arrogância
Veja os [detalhes da arrogância.](/connectors/x12/) 

## <a name="next-steps"></a>Passos seguintes
[Saiba mais sobre o Pacote de Integração Empresarial](../logic-apps/logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Pacote de Integração Empresarial") 


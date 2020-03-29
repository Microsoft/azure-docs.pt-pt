---
title: Codificar mensagens X12
description: Valide EDI e converta mensagens codificadas xML com codificador de mensagens X12 em Aplicações Lógicas Azure com Pacote de Integração Empresarial
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: c87a61dc77e656a1cfe667ce87f852303a0cc486
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792358"
---
# <a name="encode-x12-messages-in-azure-logic-apps-with-enterprise-integration-pack"></a>Encode X12 mensagens em Aplicativos Lógica Azure com Pacote de Integração Empresarial

Com o conector de mensagens Encode X12, pode validar propriedades EDI e específicas do parceiro, converter mensagens codificadas por XML em conjuntos de transações EDI no intercâmbio, e solicitar um Reconhecimento Técnico, Reconhecimento Funcional ou ambos.
Para utilizar este conector, deve adicionar o conector a um gatilho existente na sua aplicação lógica.

## <a name="before-you-start"></a>Antes de começar

Aqui estão os itens que precisa:

* Uma conta Azure; pode criar uma [conta gratuita](https://azure.microsoft.com/free)
* Uma conta de [integração](logic-apps-enterprise-integration-create-integration-account.md) que já está definida e associada à sua subscrição Azure. Deve ter uma conta de integração para utilizar o conector de mensagens Encode X12.
* Pelo menos dois [parceiros](logic-apps-enterprise-integration-partners.md) que já estão definidos na sua conta de integração
* Um [acordo X12](logic-apps-enterprise-integration-x12.md) que já está definido na sua conta de integração

## <a name="encode-x12-messages"></a>Codificar mensagens X12

1. [Criar uma aplicação lógica.](quickstart-create-first-logic-app-workflow.md)

2. O conector de mensagens Encode X12 não tem gatilhos, pelo que deve adicionar um gatilho para iniciar a sua aplicação lógica, como um gatilho de Pedido. No Logic App Designer, adicione um gatilho e adicione uma ação à sua aplicação lógica.

3.  Na caixa de pesquisa, introduza "x12" para o filtro. Selecione **o X12 - Encode to X12 message por nome** de acordo ou **X12 - Encode to X12 message by identits**.
   
    ![Pesquisa por "x12"](./media/logic-apps-enterprise-integration-x12-encode/x12decodeimage1.png) 

3. Se não criou anteriormente quaisquer ligações à sua conta de integração, é-lhe pedido que crie essa ligação agora. Nomeie a sua ligação e selecione a conta de integração que pretende ligar. 
   
    ![conexão conta de integração](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage1.png)

    São necessárias propriedades com asterisco.

    | Propriedade | Detalhes |
    | --- | --- |
    | Nome da ligação * |Insira qualquer nome para a sua ligação. |
    | Conta de Integração * |Insira um nome para a sua conta de integração. Certifique-se de que a sua conta de integração e app lógica estão na mesma localização Azure. |

5.  Quando terminar, os seus dados de ligação devem ser semelhantes a este exemplo. Para terminar de criar a sua ligação, escolha **Criar**.

    ![conexão conta de integração criada](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage2.png)

    A sua ligação está agora criada.

    ![detalhes de conexão de conta de integração](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage3.png) 

#### <a name="encode-x12-messages-by-agreement-name"></a>Codificar mensagens X12 por nome de acordo

Se optou por codificar mensagens X12 por nome de acordo, abra a lista **de acordos de Nome X12,** introduza ou selecione o seu contrato X12 existente. Introduza a mensagem XML para codificar.

![Insira o nome do acordo X12 e a mensagem XML para codificar](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage4.png)

#### <a name="encode-x12-messages-by-identities"></a>Codificar mensagens X12 por identidades

Se optar por codificar mensagens X12 por identidades, introduza o identificador de remetente, o apuramento do remetente, o identificador recetor e o recetor de qualificação, tal como configurado no seu contrato X12. Selecione a mensagem XML para codificar.
   
![Forneça identidades para remetente e recetor, selecione mensagem XML para codificar](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage5.png) 

## <a name="x12-encode-details"></a>Detalhes do Código X12

O conector X12 Encode executa estas tarefas:

* Resolução do acordo combinando propriedades de contexto de remetente e recetor.
* Serializa o intercâmbio EDI, convertendo mensagens codificadas por XML em conjuntos de transações EDI no intercâmbio.
* Aplica segmentos de cabeçalho e reboque conjunto de transações
* Gera um número de controlo de intercâmbio, um número de controlo de grupo e um número de controlo definido por transações para cada intercâmbio de saída
* Substitui os separadores nos dados de carga útil
* Valida propriedades EDI e específicas de parceiros
  * Validação do Schema dos elementos de dados definidos por transação contra a mensagem Schema
  * Validação EDI realizada em elementos de dados definidos por transações.
  * Validação alargada realizada em elementos de dados de conjuntos de transações
* Solicita um reconhecimento técnico e/ou funcional (se configurado).
  * Um reconhecimento técnico gera como resultado da validação do cabeçalho. O reconhecimento técnico informa o estado do processamento de um cabeçalho e reboque de intercâmbio pelo recetor de endereços
  * Um Reconhecimento Funcional gera como resultado da validação do corpo. O reconhecimento funcional relata cada erro encontrado durante o processamento do documento recebido

## <a name="view-the-swagger"></a>Veja a arrogância
Veja os detalhes da [arrogância.](/connectors/x12/) 

## <a name="next-steps"></a>Passos seguintes
[Saiba mais sobre o Pacote de Integração Empresarial](logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Pacote de Integração Empresarial") 


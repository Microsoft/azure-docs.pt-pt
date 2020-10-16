---
title: Codificar mensagens X12
description: Valide o EDI e converta mensagens codificadas por XML com codificação de mensagens X12 em Azure Logic Apps com Pacote de Integração Empresarial
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: c87a61dc77e656a1cfe667ce87f852303a0cc486
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "74792358"
---
# <a name="encode-x12-messages-in-azure-logic-apps-with-enterprise-integration-pack"></a>Codificar mensagens X12 no Azure Logic Apps com o Enterprise Integration Pack

Com o conector Codificar mensagem X12, pode validar as propriedades de EDI e de parceiros específicos, converter mensagens com codificação XML em conjuntos de transações EDI no intercâmbio e pedir uma Confirmação Técnica, Confirmação Funcional ou ambas.
Para utilizar este conector, tem de o adicionar a um acionador existente na aplicação lógica.

## <a name="before-you-start"></a>Antes de começar

Aqui estão os itens que precisa:

* Uma conta Azure; pode criar uma [conta gratuita](https://azure.microsoft.com/free)
* Uma conta de [integração](logic-apps-enterprise-integration-create-integration-account.md) já definida e associada à sua subscrição do Azure. Tem de ter uma conta de integração para utilizar o conector de mensagens Encode X12.
* Pelo menos dois [parceiros](logic-apps-enterprise-integration-partners.md) que já estão definidos na sua conta de integração
* Um [acordo X12](logic-apps-enterprise-integration-x12.md) que já está definido na sua conta de integração

## <a name="encode-x12-messages"></a>Codificar mensagens X12

1. [Crie uma aplicação lógica.](quickstart-create-first-logic-app-workflow.md)

2. O conector de mensagens Encode X12 não tem gatilhos, pelo que tem de adicionar um gatilho para iniciar a sua aplicação lógica, como um gatilho Request. No Logic App Designer, adicione um gatilho e, em seguida, adicione uma ação à sua aplicação lógica.

3.  Na caixa de pesquisa, introduza "x12" para o seu filtro. Selecione **X12 - Codificar para mensagem X12 com o nome de acordo** ou **X12 - Codificar para mensagem X12 por identidades**.
   
    ![Pesquisa por "x12"](./media/logic-apps-enterprise-integration-x12-encode/x12decodeimage1.png) 

3. Se não criou previamente quaisquer ligações à sua conta de integração, é solicitado que crie essa ligação agora. Nomeie a sua ligação e selecione a conta de integração que pretende ligar. 
   
    ![conexão conta de integração](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage1.png)

    São necessárias propriedades com asterisco.

    | Propriedade | Detalhes |
    | --- | --- |
    | Nome de conexão * |Insira qualquer nome para a sua ligação. |
    | Conta de Integração * |Insira um nome para a sua conta de integração. Certifique-se de que a sua conta de integração e aplicação lógica estão na mesma localização Azure. |

5.  Quando terminar, os seus detalhes de conexão devem ser semelhantes a este exemplo. Para terminar de criar a sua ligação, escolha **Criar**.

    ![ligação conta de integração criada](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage2.png)

    A sua ligação foi agora criada.

    ![detalhes da conexão da conta de integração](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage3.png) 

#### <a name="encode-x12-messages-by-agreement-name"></a>Codificar mensagens X12 pelo nome do acordo

Se optar por codificar mensagens X12 pelo nome de acordo, abra a lista **de contratos Nome de X12,** insira ou selecione o seu contrato X12 existente. Introduza a mensagem XML para codificar.

![Introduza o nome do acordo X12 e a mensagem XML para codificar](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage4.png)

#### <a name="encode-x12-messages-by-identities"></a>Codificar mensagens X12 por identidades

Se optar por codificar mensagens X12 por identidades, insira o identificador de remetente, o qualificador de remetente, o identificador do recetor e o recetor como configurado no seu acordo X12. Selecione a mensagem XML para codificar.
   
![Fornecer identidades para remetente e recetor, selecione mensagem XML para codificar](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage5.png) 

## <a name="x12-encode-details"></a>Detalhes da Codificação X12

O conector X12 Encode executa estas tarefas:

* Resolução de acordos combinando propriedades de contexto de remetente e recetor.
* Serializa o intercâmbio EDI, convertendo mensagens codificadas por XML em conjuntos de transações EDI no intercâmbio.
* Aplica segmentos de cabeçalho e reboque definidos de transações
* Gera um número de controlo de intercâmbio, um número de controlo de grupo e um número de controlo definido de transação para cada intercâmbio de saída
* Substitui separadores nos dados de carga útil
* Valida propriedades EDI e parceiras
  * Validação de esquema dos elementos de dados definidos por transação contra a mensagem Schema
  * Validação de EDI realizada em elementos de dados de conjunto de transações.
  * Validação alargada realizada em elementos de dados definidos por transações
* Solicita um reconhecimento técnico e/ou funcional (se configurado).
  * Um Reconhecimento Técnico gera como resultado da validação do cabeçalho. O reconhecimento técnico informa o estado do processamento de um cabeçalho e reboque de intercâmbio pelo recetor de endereços
  * Um Reconhecimento Funcional gera como resultado da validação do corpo. O reconhecimento funcional relata cada erro encontrado durante o processamento do documento recebido

## <a name="view-the-swagger"></a>Ver a arrogância
Veja os [detalhes da arrogância.](/connectors/x12/) 

## <a name="next-steps"></a>Passos seguintes
[Saiba mais sobre o Pacote de Integração Empresarial](logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Pacote de Integração Empresarial") 


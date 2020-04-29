---
title: Codificar mensagens EDIFACT
description: Valide edi e gere XML com codificador de mensagens EDIFACT para Aplicações Lógicas Azure com Pacote de Integração Empresarial
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, divswa, logicappspm
ms.topic: article
ms.date: 04/22/2020
ms.openlocfilehash: cee97fddc619840de821ebb70d32e0cab1bbe040
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82106530"
---
# <a name="encode-edifact-messages-for-azure-logic-apps-with-enterprise-integration-pack"></a>Codificar mensagens EDIFACT para Aplicações Lógicas Azure com Pacote de Integração Empresarial

Com o conector de mensagens Encode EDIFACT, pode validar propriedades EDI e específicas do parceiro, gerar um documento XML para cada conjunto de transações, e solicitar um Reconhecimento Técnico, Reconhecimento Funcional ou ambos.
Para utilizar este conector, deve adicionar o conector a um gatilho existente na sua aplicação lógica.

## <a name="before-you-start"></a>Antes de começar

Aqui estão os itens que precisa:

* Uma conta Azure; pode criar uma [conta gratuita](https://azure.microsoft.com/free)
* Uma conta de [integração](logic-apps-enterprise-integration-create-integration-account.md) que já está definida e associada à sua subscrição Azure. Deve ter uma conta de integração para utilizar o conector de mensagens Encode EDIFACT. 
* Pelo menos dois [parceiros](logic-apps-enterprise-integration-partners.md) que já estão definidos na sua conta de integração
* Um [acordo EDIFACT](logic-apps-enterprise-integration-edifact.md) que já está definido na sua conta de integração

## <a name="encode-edifact-messages"></a>Codificar mensagens EDIFACT

> [!IMPORTANT]
> O conector EDIFACT suporta apenas caracteres UTF-8.
> Se a sua saída contiver caracteres inesperados, verifique se as suas mensagens EDIFACT utilizam o conjunto de caracteres UTF-8.

1. [Criar uma aplicação lógica.](quickstart-create-first-logic-app-workflow.md)

2. O conector de mensagens Encode EDIFACT não tem gatilhos, pelo que deve adicionar um gatilho para iniciar a sua aplicação lógica, como um gatilho de Pedido. No Logic App Designer, adicione um gatilho e adicione uma ação à sua aplicação lógica.

3.  Na caixa de pesquisa, introduza "EDIFACT" como filtro. Selecione **ou Encode EDIFACT Message por nome** de acordo ou **Encode para a mensagem EDIFACT por identidades**.
   
    ![pesquisar EDIFACT](media/logic-apps-enterprise-integration-edifact-encode/edifactdecodeimage1.png)  

3. Se não criou anteriormente quaisquer ligações à sua conta de integração, é-lhe pedido que crie essa ligação agora. Nomeie a sua ligação e selecione a conta de integração que pretende ligar.

    ![criar conexão conta de integração](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage1.png)  

    São necessárias propriedades com asterisco.

    | Propriedade | Detalhes |
    | --- | --- |
    | Nome da ligação * |Insira qualquer nome para a sua ligação. |
    | Conta de Integração * |Insira um nome para a sua conta de integração. Certifique-se de que a sua conta de integração e app lógica estão na mesma localização Azure. |

5.  Quando terminar, os seus dados de ligação devem ser semelhantes a este exemplo. Para terminar de criar a sua ligação, escolha **Criar**.

    ![detalhes de conexão de conta de integração](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage2.png)

    A sua ligação está agora criada.

    ![conexão conta de integração criada](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage4.png)

#### <a name="encode-edifact-message-by-agreement-name"></a>Codificar mensagem EDIFACT por nome de acordo

Se optou por codificar mensagens EDIFACT por nome de acordo, abra o nome da lista **de acordos EDIFACT,** introduza ou selecione o seu nome de acordo EDIFACT. Introduza a mensagem XML para codificar.

![Insira o nome do acordo EDIFACT e a mensagem XML para codificar](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage6.png)

#### <a name="encode-edifact-message-by-identities"></a>Codificar mensagem EDIFACT por identidades

Se optar por codificar mensagens EDIFACT por identidades, introduza o identificador de remetente, o apuramento do remetente, o identificador recetor e o recetor de qualificação, tal como configurado no seu acordo EDIFACT. Selecione a mensagem XML para codificar.

![Forneça identidades para remetente e recetor, selecione mensagem XML para codificar](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage7.png)

## <a name="edifact-encode-details"></a>EdIFACT Codifica detalhes

O conector Encode EDIFACT executa estas tarefas: 

* Resolver o acordo combinando o qualificador de emissor & identificador e recetor e identificador
* Serializa o intercâmbio EDI, convertendo mensagens codificadas por XML em conjuntos de transações EDI no intercâmbio.
* Aplica segmentos de cabeçalho e reboque conjunto de transações
* Gera um número de controlo de intercâmbio, um número de controlo de grupo e um número de controlo definido por transações para cada intercâmbio de saída
* Substitui os separadores nos dados de carga útil
* Valida propriedades EDI e específicas de parceiros
  * Validação schema dos elementos de dados definidos de transação contra o esquema da mensagem.
  * Validação EDI realizada em elementos de dados definidos por transações.
  * Validação alargada realizada em elementos de dados de conjuntos de transações
* Gera um documento XML para cada conjunto de transações.
* Solicita um reconhecimento técnico e/ou funcional (se configurado).
  * Como reconhecimento técnico, a mensagem CONTRL indica a receção de um intercâmbio.
  * Como reconhecimento funcional, a mensagem CONTRL indica aceitação ou rejeição do intercâmbio, grupo ou mensagem recebidos, com uma lista de erros ou funcionalidade não suportada

## <a name="view-swagger-file"></a>Ver ficheiro Swagger
Para ver os detalhes da Swagger para o conector EDIFACT, consulte [EDIFACT](/connectors/edifact/).

## <a name="next-steps"></a>Passos seguintes
[Saiba mais sobre o Pacote de Integração Empresarial](logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Pacote de Integração Empresarial") 


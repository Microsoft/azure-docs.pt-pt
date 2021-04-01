---
title: Codificar mensagens EDIFACT
description: Validar eDI e gerar XML com codificadora de mensagens EDIFACT para apps Azure Logic com Pacote de Integração Empresarial
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, divswa, logicappspm
ms.topic: article
ms.date: 04/22/2020
ms.openlocfilehash: cee97fddc619840de821ebb70d32e0cab1bbe040
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "82106530"
---
# <a name="encode-edifact-messages-for-azure-logic-apps-with-enterprise-integration-pack"></a>Codificar mensagens EDIFACT para apps lógicas Azure com pacote de integração empresarial

Com o conector Codificar mensagem EDIFACT, pode validar as propriedades de EDI e de parceiros específicos, gerar um documento XML para cada conjunto de transações e pedir uma Confirmação Técnica, Confirmação Funcional ou ambas.
Para utilizar este conector, tem de o adicionar a um acionador existente na aplicação lógica.

## <a name="before-you-start"></a>Antes de começar

Aqui estão os itens que precisa:

* Uma conta Azure; pode criar uma [conta gratuita](https://azure.microsoft.com/free)
* Uma conta de [integração](logic-apps-enterprise-integration-create-integration-account.md) já definida e associada à sua subscrição do Azure. Tem de ter uma conta de integração para utilizar o conector de mensagens EDIFACT do Código Descodificador. 
* Pelo menos dois [parceiros](logic-apps-enterprise-integration-partners.md) que já estão definidos na sua conta de integração
* Um [acordo EDIFACT](logic-apps-enterprise-integration-edifact.md) que já está definido na sua conta de integração

## <a name="encode-edifact-messages"></a>Codificar mensagens EDIFACT

> [!IMPORTANT]
> O conector EDIFACT suporta apenas caracteres UTF-8.
> Se a sua saída contiver caracteres inesperados, verifique se as suas mensagens EDIFACT utilizam o conjunto de caracteres UTF-8.

1. [Crie uma aplicação lógica.](quickstart-create-first-logic-app-workflow.md)

2. O conector de mensagens EDIFACT do Código Descodifica não tem gatilhos, pelo que tem de adicionar um gatilho para iniciar a sua aplicação lógica, como um gatilho Request. No Logic App Designer, adicione um gatilho e, em seguida, adicione uma ação à sua aplicação lógica.

3.  Na caixa de pesquisa, introduza "EDIFACT" como filtro. Selecione **a mensagem EDIFACT codificar o nome do acordo** ou **codificar a mensagem EDIFACT por identidades**.
   
    ![pesquisar EDIFACT](media/logic-apps-enterprise-integration-edifact-encode/edifactdecodeimage1.png)  

3. Se não criou previamente quaisquer ligações à sua conta de integração, é solicitado que crie essa ligação agora. Nomeie a sua ligação e selecione a conta de integração que pretende ligar.

    ![criar conexão conta de integração](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage1.png)  

    São necessárias propriedades com asterisco.

    | Propriedade | Detalhes |
    | --- | --- |
    | Nome de conexão * |Insira qualquer nome para a sua ligação. |
    | Conta de Integração * |Insira um nome para a sua conta de integração. Certifique-se de que a sua conta de integração e aplicação lógica estão na mesma localização Azure. |

5.  Quando terminar, os seus detalhes de conexão devem ser semelhantes a este exemplo. Para terminar de criar a sua ligação, escolha **Criar**.

    ![detalhes da conexão da conta de integração](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage2.png)

    A sua ligação foi agora criada.

    ![ligação conta de integração criada](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage4.png)

#### <a name="encode-edifact-message-by-agreement-name"></a>Codificar Mensagem EDIFACT por nome de acordo

Se optar por codificar mensagens EDIFACT pelo nome de acordo, abra a lista de nomes da lista **de acordos EDIFACT,** insira ou selecione o nome do seu acordo EDIFACT. Introduza a mensagem XML para codificar.

![Introduza o nome do acordo EDIFACT e a mensagem XML para codificar](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage6.png)

#### <a name="encode-edifact-message-by-identities"></a>Codificar mensagem EDIFACT por identidades

Se optar por codificar mensagens EDIFACT por identidades, insira o identificador de remetente, o qualificador de remetente, o identificador do recetor e o recetor como configurado no seu acordo EDIFACT. Selecione a mensagem XML para codificar.

![Fornecer identidades para remetente e recetor, selecione mensagem XML para codificar](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage7.png)

## <a name="edifact-encode-details"></a>Detalhes da Codificação EDIFACT

O conector EDIFACT do Código Encode executa estas tarefas: 

* Resolver o acordo combinando o qualificador de remetente & identificador e recetor e identificador
* Serializa o intercâmbio EDI, convertendo mensagens codificadas por XML em conjuntos de transações EDI no intercâmbio.
* Aplica segmentos de cabeçalho e reboque definidos de transações
* Gera um número de controlo de intercâmbio, um número de controlo de grupo e um número de controlo definido de transação para cada intercâmbio de saída
* Substitui separadores nos dados de carga útil
* Valida propriedades EDI e parceiras
  * Validação de esquema dos elementos de dados definidos por transação contra o esquema de mensagem.
  * Validação de EDI realizada em elementos de dados de conjunto de transações.
  * Validação alargada realizada em elementos de dados definidos por transações
* Gera um documento XML para cada conjunto de transações.
* Solicita um reconhecimento técnico e/ou funcional (se configurado).
  * Como reconhecimento técnico, a mensagem CONTRL indica a receção de um intercâmbio.
  * Como reconhecimento funcional, a mensagem CONTRL indica aceitação ou rejeição do intercâmbio, grupo ou mensagem recebido, com uma lista de erros ou funcionalidade não apoiada

## <a name="view-swagger-file"></a>Ver ficheiro Swagger
Para ver os detalhes da Swagger para o conector EDIFACT, consulte [EDIFACT](/connectors/edifact/).

## <a name="next-steps"></a>Passos seguintes
[Saiba mais sobre o Pacote de Integração Empresarial](logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Pacote de Integração Empresarial") 


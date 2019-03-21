---
title: Ligar ao Dynamics 365 - Azure Logic Apps | Documentos da Microsoft
description: Criar e gerir os registos com APIs de REST do Dynamics 365 (online) e o Azure Logic Apps
author: Mattp123
ms.author: matp
ms.service: logic-apps
services: logic-apps
ms.reviewer: estfan, LADocs
ms.suite: integration
ms.topic: article
ms.date: 08/18/2018
tags: connectors
ms.openlocfilehash: 9fe41cf2946525948897635a4e30213d161431ef
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58295305"
---
# <a name="manage-dynamics-365-records-with-azure-logic-apps"></a>Gerir registos do Dynamics 365 com o Azure Logic Apps

Com o Azure Logic Apps e o conector do Dynamics 365, pode criar tarefas automatizadas e fluxos de trabalho com base nos seus registos no Dynamics 365. Os fluxos de trabalho podem criar registos, itens de atualização, registos de retornados e muito mais na sua conta do Dynamics 365. Pode incluir ações nas suas aplicações lógicas que obtém respostas a partir do Dynamics 365 e disponibilizar a saída para outras ações. Por exemplo, quando um item é atualizado no Dynamics 365, pode enviar um e-mail com o Office 365.

Este artigo mostra como criar uma aplicação lógica que cria uma tarefa no Dynamics 365, sempre que for criado um novo registo de oportunidades potenciais do Dynamics 365.
Se estiver familiarizado com aplicações lógicas, reveja [o que é o Azure Logic Apps?](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se para obter uma conta do Azure gratuita</a>. 

* A [conta Dynamics 365](https://dynamics.microsoft.com)

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A aplicação de lógica onde pretende aceder à sua conta do Dynamics 365. Para começar a sua aplicação lógica com um acionador do Dynamics 365, tem um [aplicação lógica em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

## <a name="add-dynamics-365-trigger"></a>Adicionar o acionador do Dynamics 365

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

Primeiro, adicione um acionador do Dynamics 365 que é acionado quando é apresentado um novo registo de oportunidades potenciais no Dynamics 365.

1. Na [portal do Azure](https://portal.azure.com), abra a aplicação lógica em branco no Estruturador da aplicação lógica, se não estiver já abrir.

1. Na caixa de pesquisa, introduza "Dynamics 365" como o filtro. Neste exemplo, abaixo da lista de disparadores, selecione este acionador: **Quando é criado um registo**

   ![Selecionar acionador](./media/connectors-create-api-crmonline/select-dynamics-365-trigger.png)

1. Se lhe for pedido para iniciar sessão no Dynamics 365, inicie sessão agora.

1. Forneça estes detalhes do acionador:

   | Propriedade | Necessário | Descrição | 
   |----------|----------|-------------| 
   | **Nome da organização** | Sim | O nome da instância do Dynamics 365 da sua organização a monitorizar, por exemplo, "Contoso" |
   | **Nome da entidade** | Sim | O nome da entidade monitorizar, por exemplo, "leva" | 
   | **Frequência** | Sim | A unidade de tempo para utilizar com intervalos de, quando a verificação de atualizações relacionados com o acionador |
   | **Intervalo** | Sim | O número de segundos, minutos, horas, dias, semanas ou meses que passar antes da verificação seguinte |
   ||| 

   ![Detalhes do acionador](./media/connectors-create-api-crmonline/trigger-details.png)

## <a name="add-dynamics-365-action"></a>Adicionar ação do Dynamics 365

Agora, adicione a ação de Dynamics 365 que cria um registo de tarefas para o novo registro de oportunidades potenciais.

1. No acionador, escolha **novo passo**.

1. Na caixa de pesquisa, introduza "Dynamics 365" como o filtro. Na lista de ações, selecione a ação: **Criar um novo registo**

   ![Ação de seleção](./media/connectors-create-api-crmonline/select-action.png)

1. Forneça estes detalhes da ação:

   | Propriedade | Necessário | Descrição | 
   |----------|----------|-------------| 
   | **Nome da organização** | Sim | A instância do Dynamics 365 onde pretende criar o registo, que não tem de ser a mesma no seu acionador, mas é "Contoso" neste exemplo |
   | **Nome da entidade** | Sim | A entidade em que pretende criar o registo, por exemplo, "Tarefas" | 
   | | |

   ![Detalhes da ação](./media/connectors-create-api-crmonline/action-details.png)

1. Quando o **assunto** é apresentada a caixa na sua ação, clique no interior da **assunto** caixa para que a lista de conteúdo dinâmico apareça. Nesta lista, selecione os valores de campo para incluir no registo de tarefa associado com o novo registro de oportunidades potenciais:

   | Campo | Descrição | 
   |-------|-------------| 
   | **Apelido** | O apelido da oportunidade potencial, como o contacto principal no registo |
   | **Tópico** | O nome descritivo para o líder no registo | 
   | | | 

   ![Detalhes de registo da tarefa](./media/connectors-create-api-crmonline/create-record-details.png)

1. Na barra de ferramentas da estruturador, escolha **guardar** para a aplicação lógica. 

1. Para iniciar manualmente a aplicação lógica, na barra de ferramentas da estruturador, escolha **executar**.

   ![Executar a aplicação lógica](./media/connectors-create-api-crmonline/designer-toolbar-run.png)

1. Agora, crie um registo de oportunidades potenciais no Dynamics 365 para que pode acionar o fluxo de trabalho da sua aplicação lógica.

## <a name="add-filter-or-query"></a>Adicionar filtro ou de consulta

Para especificar como filtrar dados numa ação do Dynamics 365, escolha **Mostrar opções avançadas** em ação. Em seguida, pode adicionar um filtro ou a ordem pela consulta.
Por exemplo, pode utilizar uma consulta de filtro para obter apenas as contas do Active Directory e ordenar os registos por nome da conta. Para essa tarefa, siga estes passos:

1. Sob **consulta de filtro**, introduza esta consulta de filtro de OData: `statuscode eq 1`

2. Sob **Order By**, quando for apresentada a lista de conteúdo dinâmico, selecione **nome da conta**. 

   ![Especifique o filtro e ordem](./media/connectors-create-api-crmonline/advanced-options.png)

Para obter mais informações, consulte estas opções de consulta de sistema do Dynamics 365 Customer Engagement Web API: 

* [$filter](https://docs.microsoft.com/dynamics365/customer-engagement/developer/webapi/query-data-web-api#filter-results)
* [$orderby](https://docs.microsoft.com/dynamics365/customer-engagement/developer/webapi/query-data-web-api#order-results)

### <a name="best-practices-for-advanced-options"></a>Melhores práticas para as opções avançadas

Quando especificar um valor para um campo de uma ação ou acionador, o tipo de dados do valor tem de corresponder o tipo de campo se introduzir o valor ou selecione o valor da lista de conteúdo dinâmico manualmente.

A tabela seguinte descreve alguns dos tipos de campo e os tipos de dados necessários para seus valores.

| Tipo de campo | Tipo de dados obrigatório | Descrição | 
|------------|--------------------|-------------|
| Campos de texto | Uma linha de texto | Estes campos requerem uma única linha de texto ou conteúdo dinâmico, que tem o tipo de texto. <p><p>*Campos de exemplo*: **Descrição** e **categoria** | 
| Campos de número inteiro | Número inteiro | Alguns campos requerem o número inteiro ou conteúdo dinâmico, que tem o tipo de número inteiro. <p><p>*Campos de exemplo*: **Percentagem de conclusão** e **duração** | 
| Campos de data | Data e hora | Alguns campos requerem uma data com o formato dd/mm/aaaa ou conteúdo dinâmico, que tem o tipo de data. <p><p>*Campos de exemplo*: **Criado em**, **data de início**, **início real**, **fim real**, e **data de vencimento** | 
| Tipo de campos que requerem um ID de registo e a pesquisa | Chave primária | Alguns campos que fazem referência a outro registo de entidade requerem um ID de registo e um tipo de pesquisa. | 
||||

Expandindo esses tipos de campo, seguem-se os campos de exemplo no Dynamics 365 acionadores e ações que requerem um ID de registo e o tipo de pesquisa. Este requisito significa que os valores que selecionou na lista de dinâmico não funcionarão. 

| Campo | Descrição | 
|-------|-------------|
| **Proprietário** | Tem de ser um ID de utilizador válido ou da Equipe do ID de registo. | 
| **Tipo de proprietário** | Tem de ser **systemusers** ou **equipes**. | 
| **Relativamente à** | Tem de ser um ID de registo válido, por exemplo, um ID de conta ou contacte o ID de registo. | 
| **Tipo de relativamente** | Tem de ser um tipo de pesquisa, tal como **contas** ou **contactos**. | 
| **Cliente** | Tem de ser um ID de registo válido, por exemplo, um ID de conta ou contacte o ID de registo. | 
| **Tipo de cliente** | Tem de ser o tipo de pesquisa, tal como **contas** ou **contactos**. | 
|||

Neste exemplo, a ação com o nome **criar um novo registo** cria um novo registo de tarefas: 

![Criar registo de tarefas com IDs de registo e tipos de pesquisa](./media/connectors-create-api-crmonline/create-record-advanced.png)

Esta ação atribui o registo de tarefas para um ID de utilizador específico ou o ID de registo de equipe, com base no ID de registo no **proprietário** campo e a pesquisa de digitar a **tipo de proprietário** campo:

![Tipo de ID e a pesquisa de registo no proprietário](./media/connectors-create-api-crmonline/owner-record-id-and-lookup-type.png)

Esta ação também adiciona um registo de conta que está associada o registo ID adicionado na **relativamente à** campo e a pesquisa de digitar a **relativamente ao tipo** campo: 

![Escreva sobre pesquisa e ID de registo](./media/connectors-create-api-crmonline/regarding-record-id-lookup-type-account.png)

## <a name="find-record-id"></a>Localizar o ID de registo

Para localizar um ID de registo, siga estes passos: 

1. No Dynamics 365, abra um registo, como um registo de conta.

2. Na barra de ferramentas de ações, escolha um destes passos:

   * Escolher **destacar**. ![destacar registo](./media/connectors-create-api-crmonline/popout-record.png) 
   * Escolher **enviar uma ligação** pelo que pode copiar o URL completo para o programa de e-mail predefinido.

   O registo ID aparece no URL entre o `%7b` e `%7d` codificação de caracteres:

   ![Localizar o ID de registo](./media/connectors-create-api-crmonline/find-record-ID.png)

## <a name="troubleshoot-failed-runs"></a>Resolver problemas relacionados com execuções falhadas

Para localizar e reveja os passos falhados na sua aplicação lógica, pode ver o histórico de execuções da sua aplicação lógica, estado, entradas, saídas e assim por diante.

1. No portal do Azure, no menu principal da sua aplicação lógica, selecione **descrição geral**. Na **histórico de execuções** seção, que mostra todos os Estados de execução para a aplicação lógica, selecione uma execução falhada para obter mais informações.

   ![Estado de execução da aplicação lógica](./media/connectors-create-api-crmonline/run-history.png)

1. Expanda um passo falhado para que pode ver mais detalhes. 

   ![Expanda o passo falhado](./media/connectors-create-api-crmonline/expand-failed-step.png)

1. Reveja os detalhes de um passo, como as entradas e saídas, que podem ajudar a encontrar a causa da falha.

   ![Passo falhado - entradas e saídas](./media/connectors-create-api-crmonline/expand-failed-step-inputs-outputs.png)

Para obter mais informações sobre resolução de problemas de aplicações lógicas, consulte [diagnosticar falhas de aplicação lógica](../logic-apps/logic-apps-diagnosing-failures.md).

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos, como disparadores, ações e limites, conforme descrito pelo ficheiro de Swagger do conector, consulte a [página de referência do conector](/connectors/dynamicscrmonline/). 

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre outras [conectores do Logic Apps](../connectors/apis-list.md)

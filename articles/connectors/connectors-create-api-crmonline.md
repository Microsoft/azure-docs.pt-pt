---
title: Conectar-se ao Dynamics 365 – aplicativos lógicos do Azure
description: Criar e gerenciar registros com APIs REST do Dynamics 365 (online) e aplicativos lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: Mattp123
ms.author: matp
manager: carmonm
ms.reviewer: estfan, LADocs
ms.topic: conceptual
ms.date: 08/18/2018
tags: connectors
ms.openlocfilehash: ce83e6b1847a8f08467cb7877e517bdaace27953
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051022"
---
# <a name="manage-dynamics-365-records-with-azure-logic-apps"></a>Gerenciar registros do Dynamics 365 com aplicativos lógicos do Azure

Com as Azure Logic Apps e o conector do Dynamics 365, pode criar tarefas e fluxos de trabalho automatizados com base nos seus registos no Dynamics 365. Os seus fluxos de trabalho podem criar registos, atualizar itens, devolver registos e muito mais na sua conta do Dynamics 365. Você pode incluir ações em seus aplicativos lógicos que obtêm respostas do Dynamics 365 e disponibilizar a saída para outras ações. Por exemplo, quando um item é atualizado no Dynamics 365, você pode enviar um email usando o Office 365.

Este artigo mostra como você pode criar um aplicativo lógico que cria uma tarefa no Dynamics 365 sempre que um novo registro de Lead é criado no Dynamics 365.
Se você for novo em aplicativos lógicos, examine [o que são os aplicativos lógicos do Azure?](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* Uma [conta do Dynamics 365](https://dynamics.microsoft.com)

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* O aplicativo lógico no qual você deseja acessar sua conta do Dynamics 365. Para iniciar seu aplicativo lógico com um gatilho do Dynamics 365, você precisa de um [aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-dynamics-365-trigger"></a>Adicionar gatilho do Dynamics 365

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

Primeiro, adicione um gatilho do Dynamics 365 que é acionado quando um novo registro de Lead aparece no Dynamics 365.

1. No [portal do Azure](https://portal.azure.com), abra seu aplicativo lógico em branco no designer de aplicativo lógico, se ainda não estiver aberto.

1. Na caixa de pesquisa, insira "Dynamics 365" como seu filtro. Para este exemplo, na lista de gatilhos, selecione este gatilho: **Quando um registro é criado**

   ![Selecionar acionador](./media/connectors-create-api-crmonline/select-dynamics-365-trigger.png)

1. Se você for solicitado a entrar no Dynamics 365, entre agora.

1. Forneça estes detalhes do gatilho:

   | Propriedade | Requerido | Descrição |
   |----------|----------|-------------|
   | **Nome da organização** | Sim | O nome da instância do Dynamics 365 da sua organização a ser monitorada, por exemplo, "contoso" |
   | **Nome da entidade** | Sim | O nome da entidade a ser monitorada, por exemplo, "leads" | 
   | **Frequência** | Sim | A unidade de tempo a ser usada com intervalos ao verificar se há atualizações relacionadas ao gatilho |
   | **Intervalo** | Sim | O número de segundos, minutos, horas, dias, semanas ou meses que passa antes da próxima verificação |
   ||| 

   ![Detalhes do gatilho](./media/connectors-create-api-crmonline/trigger-details.png)

## <a name="add-dynamics-365-action"></a>Adicionar ação do Dynamics 365

Agora, adicione a ação do Dynamics 365 que cria um registro de tarefa para o novo registro de Lead.

1. Em seu gatilho, escolha **nova etapa**.

1. Na caixa de pesquisa, insira "Dynamics 365" como seu filtro. Na lista ações, selecione esta ação: **Criar um novo registro**

   ![Selecionar ação](./media/connectors-create-api-crmonline/select-action.png)

1. Forneça estes detalhes de ação:

   | Propriedade | Requerido | Descrição |
   |----------|----------|-------------|
   | **Nome da organização** | Sim | A instância do Dynamics 365 em que você deseja criar o registro, que não precisa ser a mesma instância em seu gatilho, mas é "contoso" neste exemplo |
   | **Nome da entidade** | Sim | A entidade na qual você deseja criar o registro, por exemplo, "tarefas" |
   | | |

   ![Detalhes da ação](./media/connectors-create-api-crmonline/action-details.png)

1. Quando a caixa **assunto** aparecer em sua ação, clique dentro da caixa **assunto** para que a lista de conteúdo dinâmico seja exibida. Nessa lista, selecione os valores de campo a serem incluídos no registro de tarefa associado ao novo registro de Lead:

   | Campo | Descrição |
   |-------|-------------|
   | **Sobrenome** | O último nome do cliente potencial como o contato principal no registro |
   | **Tópico** | O nome descritivo do cliente potencial no registro |
   | | |

   ![Detalhes do registro de tarefa](./media/connectors-create-api-crmonline/create-record-details.png)

1. Na barra de ferramentas do designer, escolha **salvar** para seu aplicativo lógico. 

1. Para iniciar manualmente o aplicativo lógico, na barra de ferramentas do designer, escolha **executar**.

   ![Executar aplicação lógica](./media/connectors-create-api-crmonline/designer-toolbar-run.png)

1. Agora, crie um registro de Lead no Dynamics 365 para que você possa disparar o fluxo de trabalho do aplicativo lógico.

## <a name="add-filter-or-query"></a>Adicionar filtro ou consulta

Para especificar como filtrar dados em uma ação do Dynamics 365, escolha **Mostrar opções avançadas** nessa ação. Em seguida, você pode adicionar uma consulta de filtro ou ordenar por.
Por exemplo, você pode usar uma consulta de filtro para obter apenas as contas ativas e ordenar esses registros por nome de conta. Para essa tarefa, siga estas etapas:

1. Em **consulta de filtro**, insira esta consulta de filtro OData:`statuscode eq 1`

2. Em **ordenar por**, quando a lista de conteúdo dinâmico for exibida, selecione **nome da conta**. 

   ![Especificar filtro e ordem](./media/connectors-create-api-crmonline/advanced-options.png)

Para obter mais informações, consulte essas opções de consulta do sistema de API Web do engajamento do cliente Dynamics 365:

* [$filter](https://docs.microsoft.com/dynamics365/customer-engagement/developer/webapi/query-data-web-api#filter-results)
* [$orderby](https://docs.microsoft.com/dynamics365/customer-engagement/developer/webapi/query-data-web-api#order-results)

### <a name="best-practices-for-advanced-options"></a>Práticas recomendadas para opções avançadas

Quando você especifica um valor para um campo em uma ação ou gatilho, o tipo de dados do valor deve corresponder ao tipo de campo, independentemente de você inserir manualmente o valor ou selecionar o valor da lista de conteúdo dinâmico.

Esta tabela descreve alguns dos tipos de campo e os tipos de dados necessários para seus valores.

| Tipo de campo | Tipo de dados necessário | Descrição | 
|------------|--------------------|-------------|
| Campos de texto | Linha única de texto | Esses campos exigem uma única linha de texto ou conteúdo dinâmico que tenha o tipo de texto. <p><p>*Campos de exemplo*: **Descrição** e **categoria** | 
| Campos de inteiro | Número inteiro | Alguns campos exigem conteúdo inteiro ou dinâmico que tenha o tipo de inteiro. <p><p>*Campos de exemplo*: **Porcentagem concluída** e **duração** | 
| Campos de data | Data e hora | Alguns campos exigem uma data com o formato mm/dd/aaaa ou conteúdo dinâmico que tenha o tipo de data. <p><p>*Campos de exemplo*: **Criado em**, **data de início**, **início real**, **fim real**e **data de vencimento** | 
| Campos que exigem uma ID de registro e um tipo de pesquisa | Chave primária | Alguns campos que fazem referência a outro registro de entidade exigem uma ID de registro e um tipo de pesquisa. | 
||||

Expandindo esses tipos de campo, aqui estão campos de exemplo em gatilhos e ações do Dynamics 365 que exigem uma ID de registro e o tipo de pesquisa. Esse requisito significa que os valores que você selecionar na lista dinâmica não funcionarão.

| Campo | Descrição |
|-------|-------------|
| **Proprietário** | Deve ser uma ID de usuário válida ou ID de registro de equipe. |
| **Tipo de proprietário** | Deve ser `systemusers` ou `teams`. |
| **Cerne** | Deve ser uma ID de registro válida, como uma ID de conta ou ID de registro de contato. |
| **Tipo referente a** | Deve ser um tipo de pesquisa, `accounts` como ou. `contacts` |
| **Cliente** | Deve ser uma ID de registro válida, como uma ID de conta ou ID de registro de contato. |
| **Tipo de cliente** | Deve ser o tipo de pesquisa, `accounts` como ou. `contacts` |
|||

Neste exemplo, a ação chamada **criar um novo registro** cria um novo registro de tarefa:

![Criar registro de tarefa com IDs de registro e tipos de pesquisa](./media/connectors-create-api-crmonline/create-record-advanced.png)

Essa ação atribui o registro de tarefa a uma ID de usuário específica ou ID de registro de equipe, com base na ID de registro no campo **proprietário** e no tipo de pesquisa no campo **tipo de proprietário** :

![ID de registro do proprietário e tipo de pesquisa](./media/connectors-create-api-crmonline/owner-record-id-and-lookup-type.png)

Essa ação também adiciona um registro de conta associado à ID de registro adicionada no campo **referente** e o tipo de pesquisa no campo **tipo referente** :

![Em relação à ID de registro e ao tipo de pesquisa](./media/connectors-create-api-crmonline/regarding-record-id-lookup-type-account.png)

## <a name="find-record-id"></a>Localizar ID de registro

Para localizar uma ID de registro, siga estas etapas:

1. No Dynamics 365, abra um registro, como um registro de conta.

2. Na barra de ferramentas ações, escolha uma destas etapas:

   * Escolha **pop-out**. ![registro de popout](./media/connectors-create-api-crmonline/popout-record.png) 
   * Escolha **enviar um link por email** para que você possa copiar a URL completa para seu programa de email padrão.

   A ID do registro aparece na URL entre os `%7b` caracteres `%7d` de codificação e:

   ![Localizar ID de registro](./media/connectors-create-api-crmonline/find-record-ID.png)

## <a name="troubleshoot-failed-runs"></a>Solucionar problemas de execuções com falha

Para localizar e revisar as etapas com falha em seu aplicativo lógico, você pode exibir o histórico de execuções, o status, as entradas, as saídas e assim por diante do aplicativo lógico.

1. No portal do Azure, no menu principal do aplicativo lógico, selecione **visão geral**. Na seção **histórico** de execuções, que mostra todos os status de execução para seu aplicativo lógico, selecione uma execução com falha para obter mais informações.

   ![Status de execução do aplicativo lógico](./media/connectors-create-api-crmonline/run-history.png)

1. Expanda uma etapa com falha para que você possa exibir mais detalhes.

   ![Expandir etapa com falha](./media/connectors-create-api-crmonline/expand-failed-step.png)

1. Examine os detalhes da etapa, como entradas e saídas, que podem ajudá-lo a encontrar a causa por trás da falha.

   ![Falha na etapa-entradas e saídas](./media/connectors-create-api-crmonline/expand-failed-step-inputs-outputs.png)

Para obter mais informações sobre como solucionar problemas de aplicativos lógicos, consulte [Diagnosticando falhas de aplicativo lógico](../logic-apps/logic-apps-diagnosing-failures.md).

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos, como gatilhos, ações e limites, conforme descrito pelo arquivo OpenAPI (anteriormente Swagger) do conector, consulte a [página de referência do conector](/connectors/dynamicscrmonline/).

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre outros conectores de [aplicativos lógicos](../connectors/apis-list.md)

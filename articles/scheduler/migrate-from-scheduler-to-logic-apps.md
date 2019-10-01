---
title: Migrar do Agendador do Azure para o aplicativo lógico do Azure
description: Saiba como você pode substituir trabalhos no Agendador do Azure, que está sendo desativado, com os aplicativos lógicos do Azure
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 09/23/2019
ms.openlocfilehash: 73aa641fc4bb01ef3d06820ecd18b61197ab81e7
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695506"
---
# <a name="migrate-azure-scheduler-jobs-to-azure-logic-apps"></a>Migrar trabalhos do Agendador do Azure para aplicativos lógicos do Azure

> [!IMPORTANT]
> O [aplicativo lógico do Azure](../logic-apps/logic-apps-overview.md) está substituindo o Agendador do Azure, que está [sendo desativado](#retire-date). Para continuar trabalhando com os trabalhos que você configurou no Agendador, mova para o aplicativo lógico do Azure assim que possível, seguindo este artigo. 

Este artigo mostra como você pode agendar trabalhos de uso único e recorrente criando fluxos de trabalho automatizados com aplicativos lógicos do Azure, em vez de com o Agendador do Azure. Ao criar trabalhos agendados com aplicativos lógicos, você obtém estes benefícios:

* Crie seu trabalho usando um designer visual e [conectores prontos para uso](../connectors/apis-list.md) de centenas de serviços, como o armazenamento de BLOBs do Azure, o barramento de serviço do Azure, o Office 365 Outlook e o SAP.

* Gerencie cada fluxo de trabalho agendado como um recurso do Azure de primeira classe. Você não precisa se preocupar com o conceito de uma *coleção de trabalhos* porque cada aplicativo lógico é um recurso individual do Azure.

* Execute vários trabalhos únicos usando um único aplicativo lógico.

* Defina agendas que dão suporte a fusos horários e se ajustem automaticamente para o horário de Verão (DST).

Para saber mais, confira [o que são os aplicativos lógicos do Azure?](../logic-apps/logic-apps-overview.md) ou tente criar seu primeiro aplicativo lógico neste guia de início rápido: [Crie seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* Para disparar seu aplicativo lógico enviando solicitações HTTP, use uma ferramenta como o aplicativo de [área de trabalho do postmaster](https://www.getpostman.com/apps).

## <a name="migrate-by-using-a-script"></a>Migrar usando um script

Cada trabalho do Agendador é exclusivo, portanto, não existe uma ferramenta de tamanho único para migrar trabalhos do Agendador para os aplicativos lógicos do Azure. No entanto, você pode [Editar esse script](https://github.com/Azure/logicapps/tree/master/scripts/scheduler-migration) para atender às suas necessidades.

## <a name="schedule-one-time-jobs"></a>Agendar trabalhos de uso único

Você pode executar vários trabalhos únicos criando apenas um único aplicativo lógico. 

1. No [portal do Azure](https://portal.azure.com), crie um aplicativo lógico em branco no designer de aplicativo lógico. 

   Para as etapas básicas, siga [o início rápido: Crie seu primeiro aplicativo](../logic-apps/quickstart-create-first-logic-app-workflow.md)lógico.

1. Na caixa de pesquisa, digite "quando uma solicitação HTTP" como seu filtro. Na lista de gatilhos, selecione este gatilho: **Quando uma solicitação HTTP é recebida** 

   ![Adicionar gatilho de "solicitação"](./media/migrate-from-scheduler-to-logic-apps/request-trigger.png)

1. Para o gatilho de solicitação, você pode, opcionalmente, fornecer um esquema JSON, que ajuda o designer de aplicativo lógico a entender a estrutura das entradas da solicitação de entrada e torna as saídas mais fáceis de selecionar posteriormente no fluxo de trabalho.

   Para especificar um esquema, insira o esquema na caixa **esquema JSON do corpo da solicitação** , por exemplo: 

   ![Esquema de solicitação](./media/migrate-from-scheduler-to-logic-apps/request-schema.png)

   Se você não tiver um esquema, mas tiver um conteúdo de exemplo no formato JSON, poderá gerar um esquema a partir dessa carga.

   1. No gatilho de solicitação, selecione **usar conteúdo de exemplo para gerar o esquema**.

   1. Em **Inserir ou colar um exemplo de carga JSON**, forneça seu conteúdo de exemplo e, em seguida, selecione **concluído**, por exemplo:

      ![Conteúdo de exemplo](./media/migrate-from-scheduler-to-logic-apps/sample-payload.png)

1. No gatilho, selecione **próxima etapa**. 

1. Na caixa de pesquisa, insira "atrasar até" como seu filtro. Na lista ações, selecione esta ação: **Atraso até**

   Essa ação pausa o fluxo de trabalho do aplicativo lógico até uma data e hora especificadas.

   ![Adicionar a ação "atrasar até"](./media/migrate-from-scheduler-to-logic-apps/delay-until.png)

1. Insira o carimbo de data/hora para quando você quiser iniciar o fluxo de trabalho do aplicativo lógico. 

   Quando você clica dentro da caixa **timestamp** , a lista de conteúdo dinâmico é exibida para que você possa, opcionalmente, selecionar uma saída do gatilho.

   ![Fornecer detalhes de "atraso até"](./media/migrate-from-scheduler-to-logic-apps/delay-until-details.png)

1. Adicione outras ações que você deseja executar selecionando entre [centenas de conectores prontos para uso](../connectors/apis-list.md). 

   Por exemplo, você pode incluir uma ação HTTP que envia uma solicitação para uma URL ou ações que funcionam com filas de armazenamento, filas do barramento de serviço ou tópicos do barramento de serviço: 

   ![Ação HTTP](./media/migrate-from-scheduler-to-logic-apps/request-http-action.png)

1. Quando tiver terminado, salve seu aplicativo lógico.

   ![Guardar a aplicação lógica](./media/migrate-from-scheduler-to-logic-apps/save-logic-app.png)

   Quando você salva seu aplicativo lógico pela primeira vez, a URL do ponto de extremidade do gatilho de solicitação do seu aplicativo lógico aparece na caixa **URL http post** . 
   Quando você quiser chamar seu aplicativo lógico e enviar entradas para o aplicativo lógico para processamento, use essa URL como o destino da chamada.

   ![Salvar URL do ponto de extremidade do gatilho de solicitação](./media/migrate-from-scheduler-to-logic-apps/request-endpoint-url.png)

1. Copie e salve essa URL de ponto de extremidade para que você possa enviar posteriormente uma solicitação manual que dispara seu aplicativo lógico. 

## <a name="start-a-one-time-job"></a>Iniciar um trabalho de uso único

Para executar ou disparar manualmente um trabalho ocasional, envie uma chamada para a URL do ponto de extremidade para o gatilho de solicitação do seu aplicativo lógico. Nesta chamada, especifique a entrada ou a carga a ser enviada, que você pode ter descrito anteriormente especificando um esquema. 

Por exemplo, usando o aplicativo de postmaster, você pode criar uma solicitação POST com as configurações semelhantes a este exemplo e, em seguida, selecione **Enviar** para fazer a solicitação.

| Método do pedido | URL | Body | Cabeçalhos |
|----------------|-----|------|---------|
| **POST** | <*endpoint-URL*> | **recebem** <p>**JSON(application/json)** <p>Na caixa **bruto** , insira a carga que você deseja enviar na solicitação. <p>**Nota**: Essa configuração configura automaticamente os valores dos **cabeçalhos** . | **Chave**: Tipo de conteúdo <br>**Valor**: aplicativo/JSON |
|||||

![Enviar solicitação para disparar o aplicativo lógico manualmente](./media/migrate-from-scheduler-to-logic-apps/postman-send-post-request.png)

Depois de enviar a chamada, a resposta do seu aplicativo lógico aparece sob a caixa **bruta** na guia **corpo** . 

<a name="workflow-run-id"></a>

> [!IMPORTANT]
>
> Se você quiser cancelar o trabalho mais tarde, selecione a guia **cabeçalhos** . Localize e copie o valor do cabeçalho **x-MS-Workflow-Run-ID** na resposta. 
>
> ![Resposta](./media/migrate-from-scheduler-to-logic-apps/postman-response.png)

## <a name="cancel-a-one-time-job"></a>Cancelar um trabalho ocasional

Em aplicativos lógicos, cada trabalho único é executado como uma única instância de execução do aplicativo lógico. Para cancelar um trabalho único, você pode usar execuções de [Workflow-cancelar](https://docs.microsoft.com/rest/api/logic/workflowruns/cancel) na API REST de aplicativos lógicos. Quando você envia uma chamada para o gatilho, forneça a [ID de execução do fluxo de trabalho](#workflow-run-id).

## <a name="schedule-recurring-jobs"></a>Agendar tarefas periódicas

1. No [portal do Azure](https://portal.azure.com), crie um aplicativo lógico em branco no designer de aplicativo lógico. 

   Para as etapas básicas, siga [o início rápido: Crie seu primeiro aplicativo](../logic-apps/quickstart-create-first-logic-app-workflow.md)lógico.

1. Na caixa de pesquisa, digite "recorrência" como filtro. Na lista de gatilhos, selecione este gatilho: **Periodicidade** 

   ![Adicionar gatilho de "recorrência"](./media/migrate-from-scheduler-to-logic-apps/recurrence-trigger.png)

1. Configure uma agenda mais avançada, se desejar.

   ![Agendamento avançado](./media/migrate-from-scheduler-to-logic-apps/recurrence-advanced-schedule.png)

   Para obter mais informações sobre opções de agendamento avançadas, consulte [criar e executar tarefas recorrentes e fluxos de trabalho com aplicativos lógicos do Azure](../connectors/connectors-native-recurrence.md).

1. Adicione outras ações desejadas, selecionando entre [centenas de pronto para uso](../connectors/apis-list.md). No gatilho, selecione **próxima etapa**. Localize e selecione as ações desejadas.

   Por exemplo, você pode incluir uma ação HTTP que envia uma solicitação para uma URL ou ações que funcionam com filas de armazenamento, filas do barramento de serviço ou tópicos do barramento de serviço: 

   ![Ação HTTP](./media/migrate-from-scheduler-to-logic-apps/recurrence-http-action.png)

1. Quando tiver terminado, salve seu aplicativo lógico.

   ![Guardar a aplicação lógica](./media/migrate-from-scheduler-to-logic-apps/save-logic-app.png)

## <a name="advanced-setup"></a>Configuração avançada

Aqui estão outras maneiras que você pode personalizar seus trabalhos.

### <a name="retry-policy"></a>Política de repetição

Para controlar a maneira como uma ação tenta executar novamente em seu aplicativo lógico quando ocorrem falhas intermitentes, você pode definir a [política de repetição](../logic-apps/logic-apps-exception-handling.md#retry-policies) nas configurações de cada ação, por exemplo:

1. Abra o menu de reticências da ação ( **...** ) e selecione **configurações**.

   ![Abrir configurações de ação](./media/migrate-from-scheduler-to-logic-apps/action-settings.png)

1. Selecione a política de repetição que você deseja. Para obter mais informações sobre cada política, consulte [políticas de repetição](../logic-apps/logic-apps-exception-handling.md#retry-policies).

   ![Selecionar política de repetição](./media/migrate-from-scheduler-to-logic-apps/retry-policy.png)

## <a name="handle-exceptions-and-errors"></a>Tratar exceções e erros

No Agendador do Azure, se a ação padrão falhar ao ser executada, você poderá executar uma ação alternativa que resolve a condição de erro. Em aplicativos lógicos do Azure, você também pode executar a mesma tarefa.

1. No designer de aplicativo lógico, acima da ação que você deseja manipular, mova o ponteiro sobre a seta entre as etapas e selecione **Adicionar uma ramificação paralela**. 

   ![Adicionar ramificação paralela](./media/migrate-from-scheduler-to-logic-apps/add-parallel-branch.png)

1. Localize e selecione a ação que deseja executar, em vez disso, como a ação alternativa.

   ![Adicionar ação paralela](./media/migrate-from-scheduler-to-logic-apps/add-parallel-action.png)

1. Na ação alternativa, abra o menu de reticências ( **...** ) e selecione **configurar execução após**.

   ![Configurar execução após](./media/migrate-from-scheduler-to-logic-apps/configure-run-after.png)

1. Desmarque a caixa a propriedade **é bem-sucedida** . Selecione estas propriedades: **falha**, **foi ignorado**e atingiu o **tempo limite**

   ![Configurar propriedades de "executar após"](./media/migrate-from-scheduler-to-logic-apps/select-run-after-properties.png)

1. Quando tiver terminado, selecione **Concluído**.

Para saber mais sobre a manipulação de exceção, consulte [tratar erros e exceções – Propriedade RunAfter](../logic-apps/logic-apps-exception-handling.md#catch-and-handle-failures-with-the-runafter-property).

## <a name="faq"></a>FAQ

<a name="retire-date"></a>

**P**: Quando o Agendador do Azure é desativado? <br>
**A**: O Agendador do Azure está agendado para ser totalmente desativado em 31 de dezembro de 2019. Para ver as etapas importantes a serem seguidas antes dessa data e uma linha do tempo detalhada, consulte [estendendo a data de aposentadoria do Agendador para 31 de dezembro de 2019](https://azure.microsoft.com/en-us/updates/extending-retirement-date-of-scheduler/). Para atualizações gerais, consulte [atualizações do Azure – Agendador](https://azure.microsoft.com/updates/?product=scheduler).

**P**: O que acontece com minhas coleções de trabalho e trabalhos depois que o serviço se aposenta? <br>
**A**: Todas as coleções de trabalhos e trabalhos do Agendador param de executar e são excluídos do sistema.

**P**: É necessário fazer backup ou executar outras tarefas antes de migrar meus trabalhos do Agendador para os aplicativos lógicos? <br>
**A**: Como prática recomendada, sempre faça backup do seu trabalho. Verifique se os aplicativos lógicos que você criou estão sendo executados conforme o esperado antes de excluir ou desabilitar os trabalhos do Agendador. 

**P**: Há uma ferramenta que pode me ajudar a migrar meus trabalhos do Agendador para os aplicativos lógicos? <br>
**A**: Cada trabalho do Agendador é exclusivo e, portanto, uma ferramenta de tamanho único não existe. No entanto, com base em suas necessidades, você pode [Editar esse script para migrar trabalhos do Agendador do Azure para aplicativos lógicos do Azure](https://github.com/Azure/logicapps/tree/master/scripts/scheduler-migration).

**P**: Onde posso obter suporte para migrar meus trabalhos do Agendador? <br>
**A**: Aqui estão algumas maneiras de obter suporte: 

**Azure portal**

Se sua assinatura do Azure tiver um plano de suporte pago, você poderá criar uma solicitação de suporte técnico no portal do Azure. Caso contrário, você pode selecionar uma opção de suporte diferente.

1. No menu [portal do Azure](https://portal.azure.com) principal, selecione **ajuda + suporte**.

1. No menu de **suporte** , selecione **nova solicitação de suporte**. Forneça essas informações sobre para sua solicitação:

   | Propriedade | Value |
   |---------|-------|
   | **Tipo de problema** | **Technical** |
   | **Subscrição** | <*your-Azure-subscription*> |
   | **Serviço** | Em **monitoramento & gerenciamento**, selecione **Agendador**. Se você não conseguir localizar o **Agendador**, selecione **todos os serviços** primeiro. |
   ||| 

1. Selecione a opção de suporte desejada. Se você tiver um plano de suporte pago, selecione **Avançar**.

**Comunidade**

* [Fórum de aplicativos lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-scheduler)

## <a name="next-steps"></a>Passos seguintes

* [Criar tarefas e fluxos de trabalho em execução regularmente com aplicativos lógicos do Azure](../connectors/connectors-native-recurrence.md)
* [Tutorial: Verificar o tráfego com um aplicativo lógico baseado em agenda](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)

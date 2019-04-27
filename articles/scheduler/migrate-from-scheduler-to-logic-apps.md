---
title: Migrar do agendador do Azure para o Azure Logic Apps
description: Saiba como pode substituir tarefas no Azure Scheduler, que está a ser descontinuado, com o Azure Logic Apps
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 09/20/2018
ms.openlocfilehash: 25ed66fd75301475542dbac8e8a01670ee37563c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60531472"
---
# <a name="migrate-azure-scheduler-jobs-to-azure-logic-apps"></a>Migrar os trabalhos do programador do Azure para o Azure Logic Apps

> [!IMPORTANT]
> O Azure Logic Apps é substituir o agendador do Azure, que está a ser descontinuado. Para agendar tarefas, siga este artigo para mover para o Azure Logic Apps em vez disso.

Este artigo mostra como pode agendar as tarefas de uso individual e recorrentes através da criação de fluxos de trabalho automatizados com o Azure Logic Apps, em vez de agendador do Azure. Ao criar tarefas agendadas com o Logic Apps, obtém esses benefícios:

* Não precisa se preocupar sobre o conceito de um *a coleção de tarefas* porque cada aplicação de lógica é um recurso do Azure separado.

* Pode executar várias tarefas de uso individual através de uma aplicação lógica única.

* O serviço do Azure Logic Apps suporta o fuso horário e horário de Verão (horário de Verão).

Para obter mais informações, consulte [o que é o Azure Logic Apps?](../logic-apps/logic-apps-overview.md) em alternativa, tente criar a sua primeira aplicação lógica neste início rápido: [Criar a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se para obter uma conta do Azure gratuita</a>.

* Para acionar a sua aplicação lógica com o envio de pedidos de HTTP, utilize uma ferramenta como o [aplicação de ambiente de trabalho Postman](https://www.getpostman.com/apps).

## <a name="schedule-one-time-jobs"></a>Agendar tarefas de uso individual

Pode executar várias tarefas de uso individual, criando apenas uma aplicação de lógica única. 

### <a name="create-your-logic-app"></a>Criar uma aplicação lógica

1. Na [portal do Azure](https://portal.azure.com), criar uma aplicação lógica em branco no Estruturador da aplicação lógica. 

   Para obter os passos básicos, siga [início rápido: Criar a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Na caixa de pesquisa, introduza "quando um pedido de http" como o filtro. Na lista de disparadores, selecione este acionador: **Quando é recebido um pedido HTTP** 

   ![Adicionar acionador "Pedir"](./media/migrate-from-scheduler-to-logic-apps/request-trigger.png)

1. Para o acionador de pedido, opcionalmente, pode fornecer um esquema JSON, que ajuda o Estruturador da aplicação lógica compreender a estrutura para as entradas da solicitação recebida e facilita as saídas para selecionar mais tarde no fluxo de trabalho.

   Para especificar um esquema, introduza o esquema na **pedir esquema JSON do corpo** caixa, por exemplo: 

   ![Esquema de pedido](./media/migrate-from-scheduler-to-logic-apps/request-schema.png)

   Se não tem um esquema, mas tem um payload de exemplo no formato JSON, pode gerar um esquema a partir desse payload.

   1. No acionador do pedido, escolha **utilizar payload de amostra para gerar esquema**.

   1. Sob **introduza ou cole um payload JSON de exemplo**, forneça o payload de exemplo e, em seguida, escolha **feito**, por exemplo:

      ![Payload de exemplo](./media/migrate-from-scheduler-to-logic-apps/sample-payload.png)

1. No acionador, escolha **passo seguinte**. 

1. Na caixa de pesquisa, introduza "atraso até" como o filtro. Abaixo da lista de ações, selecione a ação: **Atraso até**

   Esta ação coloca em pausa o fluxo de trabalho de aplicação lógica até uma data e hora especificadas.

   ![Adicionar ação "Atraso até"](./media/migrate-from-scheduler-to-logic-apps/delay-until.png)

1. Introduza o carimbo de hora para quando deseja iniciar o fluxo de trabalho da aplicação lógica. 

   Quando clica dentro da **Timestamp** caixa, é apresentada uma lista de conteúdo dinâmico, para, opcionalmente, pode selecionar uma saída a partir do acionador.

   ![Fornecer detalhes de "Atraso até"](./media/migrate-from-scheduler-to-logic-apps/delay-until-details.png)

1. Adicionar outras ações que pretende executar ao selecionar a partir [~ mais de 200 conectores](../connectors/apis-list.md). 

   Por exemplo, pode incluir uma ação de HTTP que envia um pedido para um URL ou ações que funcionam com o armazenamento de filas, filas do Service Bus ou tópicos do Service Bus: 

   ![Ação de HTTP](./media/migrate-from-scheduler-to-logic-apps/request-http-action.png)

1. Quando tiver terminado, guarde a aplicação lógica.

   ![Guardar a aplicação lógica](./media/migrate-from-scheduler-to-logic-apps/save-logic-app.png)

   Ao guardar a aplicação lógica pela primeira vez, o ponto final do URL para o acionador de pedido da sua aplicação lógica aparece no **URL do HTTP POST** caixa. 
   Quando deseja chamar a sua aplicação lógica e envie entradas para a sua aplicação lógica para processamento, utilize este URL como o destino da chamada.

   ![Guardar o URL de ponto final de Acionador de pedido](./media/migrate-from-scheduler-to-logic-apps/request-endpoint-url.png)

1. Copie e guarde este URL de ponto final para que mais tarde possa enviar um pedido manual que aciona a sua aplicação lógica. 

## <a name="start-a-one-time-job"></a>Iniciar uma tarefa de uso individual

Para executar ou acionar uma única tarefa manualmente, envie uma chamada para o URL de ponto final para o acionador de pedido da sua aplicação lógica. Na chamada, especifique o payload para enviar, que poderá ter a descrito anteriormente, especificando um esquema ou de entrada. 

Por exemplo, utilizar a aplicação Postman, pode criar um pedido POST semelhante ao seguinte exemplo com as definições e, em seguida, escolha **enviar** para fazer o pedido.

| Método do pedido | do IdP | Corpo | Cabeçalhos |
|----------------|-----|------|---------| 
| **POST** | <*endpoint-URL*> | **raw** <p>**JSON(application/json)** <p>Na **brutos** , introduza o payload de que pretende enviar no pedido. <p>**Nota**: Automaticamente esta definição configura a **cabeçalhos** valores. | **chave**: Content-Type <br>**Valor**: aplicação/json
 |||| 

![Enviar pedido para acionar manualmente a aplicação lógica](./media/migrate-from-scheduler-to-logic-apps/postman-send-post-request.png)

Depois de enviar a chamada, a resposta da sua aplicação lógica aparece sob o **não processados** caixa o **corpo** separador. 

<a name="workflow-run-id"></a>

> [!IMPORTANT]
>
> Se pretender cancelar a tarefa mais tarde, escolha o **cabeçalhos** separador. Localize e copie os **x-ms-fluxo de trabalho-run-id** valor de cabeçalho na resposta. 
>
> ![Resposta](./media/migrate-from-scheduler-to-logic-apps/postman-response.png)

## <a name="cancel-a-one-time-job"></a>Cancelar uma tarefa de uso individual

No Logic Apps, cada tarefa de uso individual é executado como uma instância de execução da aplicação lógica única. Para cancelar uma tarefa única, pode usar [execuções de fluxo de trabalho, Cancelar](https://docs.microsoft.com/rest/api/logic/workflowruns/cancel) na API de REST de aplicações lógicas. Quando envia uma chamada para o acionador, forneça o [ID de execução do fluxo de trabalho](#workflow-run-id).

## <a name="schedule-recurring-jobs"></a>Agendar tarefas periódicas

### <a name="create-your-logic-app"></a>Criar uma aplicação lógica

1. Na [portal do Azure](https://portal.azure.com), criar uma aplicação lógica em branco no Estruturador da aplicação lógica. 

   Para obter os passos básicos, siga [início rápido: Criar a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Na caixa de pesquisa, introduza "recurrence" como o filtro. Na lista de disparadores, selecione este acionador: **Periodicidade** 

   ![Adicionar acionador "Recurrence"](./media/migrate-from-scheduler-to-logic-apps/recurrence-trigger.png)

1. Defina um agendamento mais avançado, se desejar.

   ![Agenda avançada](./media/migrate-from-scheduler-to-logic-apps/recurrence-advanced-schedule.png)

   Para obter mais informações sobre opções avançadas de programação, consulte [criar e executadas tarefas recorrentes e fluxos de trabalho com o Azure Logic Apps](../connectors/connectors-native-recurrence.md)

1. Adicionar outras ações que pretenda ao selecionar a partir [mais de 200 conectores](../connectors/apis-list.md). No acionador, escolha **passo seguinte**. Localize e selecione as ações desejadas.

   Por exemplo, pode incluir uma ação de HTTP que envia um pedido para um URL ou ações que funcionam com o armazenamento de filas, filas do Service Bus ou tópicos do Service Bus: 

   ![Ação de HTTP](./media/migrate-from-scheduler-to-logic-apps/recurrence-http-action.png)

1. Quando tiver terminado, guarde a aplicação lógica.

   ![Guardar a aplicação lógica](./media/migrate-from-scheduler-to-logic-apps/save-logic-app.png)

## <a name="advanced-setup"></a>Configuração avançada

Aqui estão outras formas de personalizar os trabalhos.

### <a name="retry-policy"></a>Política de repetição

Para controlar a forma que uma ação tenta voltar a executar na sua aplicação lógica quando ocorrem falhas intermitentes, pode definir o [política de repetição](../logic-apps/logic-apps-exception-handling.md#retry-policies) nas definições de cada ação, por exemplo:

1. Abra a ação (**...** ) e, selecione **definições**.

   ![Abra as definições de ação](./media/migrate-from-scheduler-to-logic-apps/action-settings.png)

1. Selecione a política de repetição que pretende. Para obter mais informações sobre cada política, consulte [políticas de repetição](../logic-apps/logic-apps-exception-handling.md#retry-policies).

   ![Selecione a política de repetição](./media/migrate-from-scheduler-to-logic-apps/retry-policy.png)

## <a name="handle-exceptions-and-errors"></a>Processar exceções e erros

No agendador do Azure, se a ação padrão não conseguir executar, pode executar uma ação alterative que aborda a condição de erro. No Azure Logic Apps, também pode executar a mesma tarefa.

1. No Estruturador da aplicação lógica, cima da ação que pretende processar, mova o ponteiro do mouse sobre a seta entre passos e selecione e **adicionar um ramo paralelo**. 

   ![Adicionar ramo paralelo](./media/migrate-from-scheduler-to-logic-apps/add-parallel-branch.png)

1. Localize e selecione a ação que pretende executar em vez disso, como a ação alternativa.

   ![Adicionar ação paralela](./media/migrate-from-scheduler-to-logic-apps/add-parallel-action.png)

1. Na ação alternativa, abra o (**...** ) e, selecione **configurar execução posterior**.

   ![Configurar a execução posterior](./media/migrate-from-scheduler-to-logic-apps/configure-run-after.png)

1. Desmarque a caixa para o **for concluída com êxito** propriedade. Selecione estas propriedades: **falhou**, **é ignorada**, e **foi excedido**

   ![Configurar propriedades de "execução após"](./media/migrate-from-scheduler-to-logic-apps/select-run-after-properties.png)

1. Quando tiver terminado, selecione **Concluído**.

Para saber mais sobre a manipulação de exceções, veja [lidar com erros e exceções - propriedade RunAfter](../logic-apps/logic-apps-exception-handling.md#catch-and-handle-failures-with-the-runafter-property).

## <a name="faq"></a>FAQ

<a name="retire-date"></a> 

**Q**: Quando está desativando o agendador do Azure? <br>
**A**: O Azure Scheduler é agendado para extinguir 30 de Setembro de 2019.

**Q**: O que acontece aos meus coleções de tarefas do Scheduler e tarefas depois do serviço ter extinguido? <br>
**A**: Todas as tarefas e coleções de tarefas do Scheduler serão eliminadas do sistema.

**Q**: É necessário que criar cópias de segurança ou executar quaisquer outras tarefas antes de migrar as minhas tarefas do Scheduler para Logic Apps? <br>
**A**: Como melhor prática, sempre realizar backup de seu trabalho. Verifique se as aplicações lógicas que criou estão a funcionar como esperado antes de eliminar ou desativar a seus trabalhos do programador. 

**Q**: Existe uma ferramenta que pode me ajudar a migrar as minhas tarefas do Scheduler para o Logic Apps? <br>
**A**: Cada tarefa do Scheduler é exclusiva, pelo que não existe uma ferramenta adequada. No entanto, vários scripts vão estar disponíveis para que possa modificar para as suas necessidades. Para uma disponibilidade de script, verifique novamente mais tarde.

**Q**: Onde posso obter suporte para migrar as minhas tarefas do Scheduler? <br>
**A**: Aqui estão algumas formas de obter suporte: 

**Portal do Azure**

Se a sua subscrição do Azure tiver um plano de suporte pago, pode criar um pedido de suporte técnico no portal do Azure. Caso contrário, pode selecionar uma opção de suporte diferentes.

1. Sobre o [portal do Azure](https://portal.azure.com) menu principal, selecione **ajuda + suporte**.

1. Sob **suportar**, selecione **novo pedido de suporte**. Forneça estes detalhes para o seu pedido:

   | Definição | Value |
   |---------|-------|
   | **Tipo de problema** | **Technical** | 
   | **Subscrição** | <*your-Azure-subscription*> | 
   | **Serviço** | Sob **monitorização e gestão**, selecione **Scheduler**. | 
   ||| 

1. Selecione a opção de suporte que pretende. Se tiver um plano de suporte pago, escolha **seguinte**.

**Comunidade**

* [Fórum de Logic Apps do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-scheduler)

## <a name="next-steps"></a>Passos Seguintes

* [Criar fluxos de trabalho e tarefas regularmente em execução no Azure Logic Apps](../connectors/connectors-native-recurrence.md)
* [Tutorial: Verificar o tráfego com uma aplicação lógica com base na agenda](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)
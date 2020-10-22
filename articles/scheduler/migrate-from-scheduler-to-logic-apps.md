---
title: Migrar de Azure Scheduler para Azure Logic Apps
description: Saiba como pode substituir empregos no Azure Scheduler, que está a ser reformado, com a Azure Logic Apps
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 02/29/2020
ms.openlocfilehash: 0e9ca2c7b5d15fcc6be451bbe215c8b85d5309eb
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92368269"
---
# <a name="migrate-azure-scheduler-jobs-to-azure-logic-apps"></a>Migrar tarefas do Azure Scheduler para o Azure Logic Apps

> [!IMPORTANT]
> [A Azure Logic Apps](../logic-apps/logic-apps-overview.md) está a substituir o Azure Scheduler, que está [a ser reformado.](#retire-date) Para continuar a trabalhar com os trabalhos que criou no Scheduler, por favor, migra para a Azure Logic Apps o mais rapidamente possível, seguindo este artigo. 
>
> O programador já não está disponível no portal Azure, mas os cmdlets [REST API](/rest/api/scheduler) e [Azure Scheduler PowerShell](scheduler-powershell-reference.md) permanecem disponíveis neste momento para que possa gerir os seus empregos e coleções de emprego.

Este artigo mostra como pode agendar empregos únicos e recorrentes criando fluxos de trabalho automatizados com apps Azure Logic, em vez de com o Azure Scheduler. Quando cria empregos programados com aplicações lógicas, obtém estes benefícios:

* Construa o seu trabalho utilizando um designer visual e [conectores prontos a usar](../connectors/apis-list.md) a partir de centenas de serviços, tais como Azure Blob Storage, Azure Service Bus, Office 365 Outlook e SAP.

* Gerir cada fluxo de trabalho programado como um recurso Azure de primeira classe. Não tem de se preocupar com o conceito de *uma coleção* de empregos porque cada aplicação lógica é um recurso Azure individual.

* Executar vários trabalhos únicos usando uma única aplicação lógica.

* Definir horários que suportam fusos horários e ajuste automaticamente ao horário de verão (DST).

Para saber mais, veja [o que é Azure Logic Apps?](../logic-apps/logic-apps-overview.md) [Create your first logic app](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* Para ativar a sua aplicação lógica enviando pedidos HTTP, utilize uma ferramenta como a [aplicação de desktop do Carteiro](https://www.getpostman.com/apps).

## <a name="migrate-by-using-a-script"></a>Migrar usando um script

Cada trabalho de Scheduler é único, por isso não existe nenhuma ferramenta de tamanho único para migrar trabalhos de Programadores para Azure Logic Apps. No entanto, pode [editar este script](https://github.com/Azure/logicapps/tree/master/scripts/scheduler-migration) para atender às suas necessidades.

## <a name="schedule-one-time-jobs"></a>Agende empregos únicos

Você pode executar vários trabalhos únicos criando apenas uma única aplicação lógica.

1. No [portal Azure,](https://portal.azure.com)crie uma aplicação lógica em branco no Logic App Designer.

   Para os passos básicos, siga [o Quickstart: Crie a sua primeira aplicação lógica.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

1. Na caixa de pesquisa, insira `when a http request` para encontrar o gatilho 'Pedido'. A partir da lista de gatilhos, selecione este gatilho: **Quando um pedido HTTP for recebido**

   ![Adicionar gatilho "Pedido"](./media/migrate-from-scheduler-to-logic-apps/request-trigger.png)

1. Para o gatilho Pedido, pode opcionalmente fornecer um esquema JSON, o que ajuda o Designer de Aplicações Lógicas a compreender a estrutura das entradas incluídas na chamada de entrada para o gatilho do Pedido e facilita a seleção posterior do seu fluxo de trabalho.

   Na caixa **de esquema do Corpo de Pedido JSON,** introduza o esquema, por exemplo:

   ![Pedido de esquema](./media/migrate-from-scheduler-to-logic-apps/request-schema.png)

   Se não tiver um esquema, mas tiver uma carga útil de amostra no formato JSON, pode gerar um esquema a partir dessa carga útil.

   1. No gatilho 'Pedido', **selecione Utilize a carga útil da amostra para gerar esquema**.

   1. Em **Entrar ou colar uma amostra de carga JSON,** fornecer a sua carga útil da amostra, e selecione **Fazer,** por exemplo:

      ![Carga útil da amostra](./media/migrate-from-scheduler-to-logic-apps/sample-payload.png)

      ```json
      {
         "runat": "2012-08-04T00:00Z",
         "endpoint": "https://www.bing.com"
      }
      ```

1. Sob o gatilho, selecione **o próximo passo**.

1. Na caixa de pesquisa, introduza `delay until` como filtro. Na lista de ações, selecione esta ação: **Atrase até**

   Esta ação interrompe o fluxo de trabalho da aplicação lógica até uma data e hora especificadas.

   ![Adicione ação "Adiar até"](./media/migrate-from-scheduler-to-logic-apps/delay-until.png)

1. Introduza o horário para quando pretender iniciar o fluxo de trabalho da aplicação lógica.

   Quando clica dentro da caixa **Timestamp,** aparece a lista de conteúdos dinâmicos para que possa selecionar opcionalmente uma saída a partir do gatilho.

   ![Fornecer detalhes de "Delay até"](./media/migrate-from-scheduler-to-logic-apps/delay-until-details.png)

1. Adicione quaisquer outras ações que pretenda executar selecionando a partir [de centenas de conectores prontos a usar](../connectors/apis-list.md).

   Por exemplo, pode incluir uma ação HTTP que envia um pedido para um URL, ou ações que funcionam com filas de armazenamento, filas de autocarros de serviço ou tópicos de Service Bus:

   ![A screenshot mostra um atraso até que a ação seguida por uma ação H T T P com um método POST.](./media/migrate-from-scheduler-to-logic-apps/request-http-action.png)

1. Quando terminar, guarde a sua aplicação lógica.

   ![Guardar a aplicação lógica](./media/migrate-from-scheduler-to-logic-apps/save-logic-app.png)

   Quando guarda a sua aplicação lógica pela primeira vez, o URL de ponto final para o gatilho pedido da sua aplicação lógica aparece na caixa **URL HTTP POST.** Quando quiser ligar para a sua aplicação lógica e enviar entradas para a sua aplicação lógica para processamento, utilize este URL como destino de chamada.

   ![Save Request trigger endpoint URL](./media/migrate-from-scheduler-to-logic-apps/request-endpoint-url.png)

1. Copie e guarde este URL de ponto final para que possa enviar mais tarde um pedido manual que desencadeie a sua aplicação lógica.

## <a name="start-a-one-time-job"></a>Começar um trabalho único

Para executar ou desencadear manualmente um trabalho único, envie uma chamada para o URL do ponto final para o gatilho do pedido da sua aplicação lógica. Nesta chamada, especifique a entrada ou carga útil para enviar, que poderia ter descrito anteriormente especificando um esquema.

Por exemplo, usando a aplicação Postman, pode criar um pedido DEM com as definições semelhantes a esta amostra e, em seguida, selecionar **Enviar** para fazer o pedido.

| Método de pedido | URL | Corpo | Cabeçalhos |
|----------------|-----|------|---------|
| **Publicar** | <*endpoint-URL*> | **cru** <p>**JSON (aplicação/json)** <p>Na caixa **bruta,** insira a carga útil que pretende enviar no pedido. <p>**Nota:** Esta definição configura automaticamente os **valores dos cabeçalhos.** | **Chave**: Tipo de conteúdo <br>**Valor**: aplicação/json |
|||||

![Envie um pedido para ativar manualmente a sua aplicação lógica](./media/migrate-from-scheduler-to-logic-apps/postman-send-post-request.png)

Depois de enviar a chamada, a resposta da sua aplicação lógica aparece na caixa **crua** no **separador Corpo.** 

<a name="workflow-run-id"></a>

> [!IMPORTANT]
>
> Se pretender cancelar o trabalho mais tarde, selecione o **separador Cabeçalhos.** Encontre e copie o valor do cabeçalho **x-ms-workflow-run-id** na resposta. 
>
> ![Resposta](./media/migrate-from-scheduler-to-logic-apps/postman-response.png)

## <a name="cancel-a-one-time-job"></a>Cancelar um trabalho único

Em Logic Apps, cada trabalho único executa como uma única instância de execução de aplicações lógicas. Para cancelar um trabalho único, pode utilizar [Workflow Runs - Cancelar](/rest/api/logic/workflowruns/cancel) na API de Apps Lógicas REST. Quando enviar uma chamada para o gatilho, forneça o [ID do fluxo de trabalho](#workflow-run-id).

## <a name="schedule-recurring-jobs"></a>Agendar empregos recorrentes

1. No [portal Azure,](https://portal.azure.com)crie uma aplicação lógica em branco no Logic App Designer.

   Para os passos básicos, siga [o Quickstart: Crie a sua primeira aplicação lógica.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

1. Na caixa de pesquisa, introduza a "recorrência" como filtro. A partir da lista de gatilhos, selecione este gatilho: **Recorrência**

   ![Adicionar o gatilho "Recorrência"](./media/migrate-from-scheduler-to-logic-apps/recurrence-trigger.png)

1. Estabeleça um horário mais avançado, se quiser.

   ![Horário avançado](./media/migrate-from-scheduler-to-logic-apps/recurrence-advanced-schedule.png)

   Para obter mais informações sobre opções avançadas de agendamento, consulte [Criar e executar tarefas e fluxos de trabalho recorrentes com apps Azure Logic](../connectors/connectors-native-recurrence.md).

1. Adicione outras ações desejando selecionar a partir [de centenas de pronto-a-usar](../connectors/apis-list.md). Sob o gatilho, selecione **o próximo passo**. Encontre e selecione as ações que deseja.

   Por exemplo, pode incluir uma ação HTTP que envia um pedido para um URL, ou ações que funcionam com filas de armazenamento, filas de autocarros de serviço ou tópicos de Service Bus:

   ![A screenshot mostra uma ação H T T P com um método POST.](./media/migrate-from-scheduler-to-logic-apps/recurrence-http-action.png)

1. Quando terminar, guarde a sua aplicação lógica.

   ![Guardar a aplicação lógica](./media/migrate-from-scheduler-to-logic-apps/save-logic-app.png)

## <a name="advanced-setup"></a>Configuração avançada

Aqui estão outras formas de personalizar os seus trabalhos.

### <a name="retry-policy"></a>Política de Repetição

Para controlar a forma como uma ação tenta refazer a sua aplicação lógica quando ocorrerem falhas intermitentes, pode definir a [política de repetição](../logic-apps/logic-apps-exception-handling.md#retry-policies) nas definições de cada ação, por exemplo:

1. Abra as elipses da ação **(...**) e selecione **Definições**.

   ![Abrir configurações de ação](./media/migrate-from-scheduler-to-logic-apps/action-settings.png)

1. Selecione a política de repetição que deseja. Para obter mais informações sobre cada política, consulte [as políticas de Retry](../logic-apps/logic-apps-exception-handling.md#retry-policies).

   ![Selecione política de relemissão](./media/migrate-from-scheduler-to-logic-apps/retry-policy.png)

## <a name="handle-exceptions-and-errors"></a>Lidar com exceções e erros

No Azure Scheduler, se a ação padrão não funcionar, pode executar uma ação alterativa que aborde a condição de erro. Nas Azure Logic Apps, também pode executar a mesma tarefa.

1. Na Logic App Designer, acima da ação que pretende manusear, mova o ponteiro sobre a seta entre os degraus e **selecione Adicione um ramo paralelo**.

   ![Adicionar ramo paralelo](./media/migrate-from-scheduler-to-logic-apps/add-parallel-branch.png)

1. Encontre e selecione a ação que pretende executar como ação alternativa.

   ![Adicionar ação paralela](./media/migrate-from-scheduler-to-logic-apps/add-parallel-action.png)

1. Na ação alternativa, abra o menu de elipses **(...**) e selecione **Configure run depois**.

   ![Configure correr depois](./media/migrate-from-scheduler-to-logic-apps/configure-run-after.png)

1. Limpe a caixa para a propriedade **de sucesso.** Selecione estas propriedades: **falhou,** **é ignorado,** e **tem tempo para fora**

   ![Configurar propriedades "executar depois"](./media/migrate-from-scheduler-to-logic-apps/select-run-after-properties.png)

1. Quando tiver terminado, selecione **Concluído**.

Para saber mais sobre o manuseamento de exceções, consulte [erros e exceções do Handle - RunAfter property](../logic-apps/logic-apps-exception-handling.md#control-run-after-behavior).

## <a name="faq"></a>FAQ

<a name="retire-date"></a>

**P:** Quando é que o Azure Scheduler se vai reformar? <br>
**R**: Azure Scheduler está agendado para se reformar totalmente em 31 de dezembro de 2019. Para que sejam dados passos importantes antes desta data e uma cronologia detalhada, consulte [a data de reforma alargada para o Programador até 31 de dezembro de 2019](https://azure.microsoft.com/updates/extending-retirement-date-of-scheduler/). Para obter atualizações gerais, consulte [as atualizações do Azure - Agendador](https://azure.microsoft.com/updates/?product=scheduler).

**P:** O que acontece com as minhas coleções de emprego e empregos após a reforma do serviço? <br>
**R**: Todas as coleções de emprego e postos de trabalho do Programador deixam de funcionar e são eliminados do sistema.

**P:** Tenho de fazer o back up ou executar outras tarefas antes de migrar os meus trabalhos de Agendador para a Logic Apps? <br>
**R**: Como uma boa prática, sempre apoie o seu trabalho. Verifique se as aplicações lógicas criadas estão a funcionar como esperado antes de eliminar ou desativar os seus trabalhos de Agendar.

**P:** Existe alguma ferramenta que me possa ajudar a migrar os meus empregos de Scheduler para Logic Apps? <br>
**R**: Cada trabalho do Programador é único, por isso não existe uma ferramenta de tamanho único. No entanto, com base nas suas necessidades, pode [editar este script para migrar os trabalhos do Azure Scheduler para a Azure Logic Apps](https://github.com/Azure/logicapps/tree/master/scripts/scheduler-migration).

**P:** Onde posso obter apoio para migrar os meus trabalhos de Agendador? <br>
**R**: Eis algumas formas de obter apoio:

**Portal do Azure**

Se a sua subscrição Azure tiver um plano de suporte pago, pode criar um pedido de suporte técnico no portal Azure. Caso contrário, pode selecionar uma opção de suporte diferente.

1. No menu principal do [portal Azure,](https://portal.azure.com) selecione **Help + support**.

1. No menu **Suporte,** selecione **Novo pedido de suporte.** Forneça estas informações sobre o seu pedido:

   | Propriedade | Valor |
   |---------|-------|
   | **Tipo de problema** | **Parte Técnica** |
   | **Subscrição** | <*sua subscrição-Azure*> |
   | **Serviço** | Em **Gestão de & De Monitorização**, selecione **Scheduler**. Se não conseguir encontrar **o Scheduler,** selecione **todos os serviços** primeiro. |
   ||| 

1. Selecione a opção de suporte desejada. Se tiver um plano de apoio pago, selecione **Next**.

**Comunidade**

* [Microsoft Q&Uma página de perguntas para apps Azure Logic](/answers/topics/azure-logic-apps.html)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-scheduler)

## <a name="next-steps"></a>Passos seguintes

* [Criar tarefas e fluxos de trabalho regulares com apps Azure Logic](../connectors/connectors-native-recurrence.md)
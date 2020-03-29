---
title: Migrar do Programador Azure para as Aplicações Lógicas Azure
description: Saiba como pode substituir postos de trabalho no Azure Scheduler, que está a ser reformado, com apps da Azure Logic
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 02/29/2020
ms.openlocfilehash: 90c3cc2e096b9b58465987bc53f718c5d06c6203
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78899116"
---
# <a name="migrate-azure-scheduler-jobs-to-azure-logic-apps"></a>Empregos de programadores do Azure migrate para apps lógicas azure

> [!IMPORTANT]
> [A Azure Logic Apps](../logic-apps/logic-apps-overview.md) está a substituir o Programador Azure, que está [a ser reformado.](#retire-date) Para continuar a trabalhar com os trabalhos que criou no Scheduler, por favor, emigre para as Aplicações Lógicas Azure o mais rapidamente possível, seguindo este artigo. 
>
> O agendador já não está disponível no portal Azure, mas os cmdlets Rest [API](/rest/api/scheduler) e [Azure Scheduler PowerShell](scheduler-powershell-reference.md) permanecem disponíveis neste momento para que possa gerir os seus empregos e recolhas de emprego.

Este artigo mostra como pode agendar trabalhos únicos e recorrentes através da criação de fluxos de trabalho automatizados com apps lógicas azure, em vez de com o Azure Scheduler. Quando cria empregos programados com Apps Lógicas, obtém-se estes benefícios:

* Construa o seu trabalho utilizando um designer visual e [conectores prontos a usar](../connectors/apis-list.md) a partir de centenas de serviços, tais como Armazenamento Azure Blob, Azure Service Bus, Office 365 Outlook e SAP.

* Gerencie cada fluxo de trabalho programado como um recurso Azure de primeira classe. Não tem que se preocupar com o conceito de coleção de *emprego* porque cada aplicação lógica é um recurso Azure individual.

* Executar vários trabalhos únicos usando uma única aplicação lógica.

* Detete os horários que suportam os fusos horários e ajuste automaticamente ao horário de verão (DST).

Para saber mais, veja [o que são as Aplicações Lógicas Do Azure?](../logic-apps/logic-apps-overview.md) [Create your first logic app](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* Para desencadear a sua aplicação lógica enviando pedidos HTTP, utilize uma ferramenta como a aplicação de [desktop Postman](https://www.getpostman.com/apps).

## <a name="migrate-by-using-a-script"></a>Migrar usando um script

Cada trabalho de Scheduler é único, por isso não existe nenhuma ferramenta de tamanho único para migrar trabalhos de Scheduler para apps lógicas azure. No entanto, pode [editar este guião](https://github.com/Azure/logicapps/tree/master/scripts/scheduler-migration) para atender às suas necessidades.

## <a name="schedule-one-time-jobs"></a>Agendar trabalhos únicos

Você pode executar vários trabalhos únicos criando apenas uma aplicação lógica única.

1. No [portal Azure, crie](https://portal.azure.com)uma aplicação lógica em branco no Logic App Designer.

   Para os passos básicos, siga [o Quickstart: Crie a sua primeira aplicação lógica.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

1. Na caixa de `when a http request` pesquisa, introduza para encontrar o gatilho do Pedido. A partir da lista de gatilhos, selecione este gatilho: **Quando um pedido HTTP é recebido**

   ![Adicionar gatilho "Pedido"](./media/migrate-from-scheduler-to-logic-apps/request-trigger.png)

1. Para o gatilho de Pedido, pode opcionalmente fornecer um esquema JSON, que ajuda o Logic App Designer a compreender a estrutura das inputs incluídas na chamada de entrada para o gatilho do Pedido e facilita a seleção das saídas mais tarde no seu fluxo de trabalho.

   Na caixa **Do Corpo de Pedido JSON Schema,** introduza o esquema, por exemplo:

   ![Solicitar esquema](./media/migrate-from-scheduler-to-logic-apps/request-schema.png)

   Se não tiver um esquema, mas tiver uma carga útil de amostra no formato JSON, pode gerar um esquema a partir dessa carga útil.

   1. No gatilho Solicitar, selecione Utilize a carga útil da **amostra para gerar esquema**.

   1. Em **'Entrar ou colar uma amostra De carga útil JSON',** forneça a sua carga útil da amostra e selecione **Done**, por exemplo:

      ![Carga útil da amostra](./media/migrate-from-scheduler-to-logic-apps/sample-payload.png)

      ```json
      {
         "runat": "2012-08-04T00:00Z",
         "endpoint": "https://www.bing.com"
      }
      ```

1. Sob o gatilho, selecione **Próximo passo**.

1. Na caixa de `delay until` pesquisa, introduza como filtro. Na lista de ações, selecione esta ação: **Atrase até**

   Esta ação interrompe o fluxo de trabalho da sua aplicação lógica até uma data e hora especificadas.

   ![Adicione a ação "Delay until"](./media/migrate-from-scheduler-to-logic-apps/delay-until.png)

1. Introduza a marca de tempo para quando quiser iniciar o fluxo de trabalho da aplicação lógica.

   Quando clica dentro da caixa **timestamp,** aparece a lista de conteúdos dinâmicos para que possa selecionar opcionalmente uma saída a partir do gatilho.

   ![Fornecer detalhes de "Delay until"](./media/migrate-from-scheduler-to-logic-apps/delay-until-details.png)

1. Adicione quaisquer outras ações que pretenda executar selecionando a partir de [centenas de conectores prontos a utilizar](../connectors/apis-list.md).

   Por exemplo, pode incluir uma ação HTTP que envia um pedido para um URL, ou ações que funcionam com filas de armazenamento, filas de ônibus de serviço ou tópicos de ônibus de serviço:

   ![Ação HTTP](./media/migrate-from-scheduler-to-logic-apps/request-http-action.png)

1. Quando terminar, guarde a sua aplicação lógica.

   ![Guardar a aplicação lógica](./media/migrate-from-scheduler-to-logic-apps/save-logic-app.png)

   Quando guarda pela primeira vez a sua aplicação lógica, o URL de ponto final para o gatilho de pedido da aplicação lógica aparece na caixa **DEURL HTTP POST.** Quando quiser ligar para a sua aplicação lógica e enviar inputs para a sua aplicação lógica para processamento, use este URL como destino de chamada.

   ![Salvar o url final do gatilho do pedido](./media/migrate-from-scheduler-to-logic-apps/request-endpoint-url.png)

1. Copie e guarde este URL de ponto final para que possa enviar mais tarde um pedido manual que desencadeie a sua aplicação lógica.

## <a name="start-a-one-time-job"></a>Começar um trabalho único

Para executar manualmente ou desencadear um trabalho único, envie uma chamada para o URL de ponto final para o gatilho de pedido da sua aplicação lógica. Nesta chamada, especifique a entrada ou carga útil para enviar, o que poderia ter descrito anteriormente especificando um esquema.

Por exemplo, utilizando a aplicação Postman, pode criar um pedido post com as definições semelhantes a esta amostra e, em seguida, selecionar **Enviar** para fazer o pedido.

| Método de pedido | do IdP | Corpo | Cabeçalhos |
|----------------|-----|------|---------|
| **POST** | <*endpoint-URL*> | **cru** <p>**JSON (aplicação/json)** <p>Na caixa **crua,** introduza a carga útil que pretende enviar no pedido. <p>**Nota:** Esta definição configura automaticamente os **valores cabeçalhos.** | **Chave**: Tipo de conteúdo <br>**Valor**: aplicação/json |
|||||

![Envie pedido para desencadear manualmente a sua aplicação lógica](./media/migrate-from-scheduler-to-logic-apps/postman-send-post-request.png)

Depois de enviar a chamada, a resposta da sua aplicação lógica aparece sob a caixa **crua** no separador **Body.** 

<a name="workflow-run-id"></a>

> [!IMPORTANT]
>
> Se quiser cancelar o trabalho mais tarde, selecione o separador **Headers.** Encontre e copie o valor do cabeçalho **x-ms-workflow-run-id** na resposta. 
>
> ![Resposta](./media/migrate-from-scheduler-to-logic-apps/postman-response.png)

## <a name="cancel-a-one-time-job"></a>Cancele um trabalho único

Em Aplicações Lógicas, cada trabalho único executa como uma única instância de execução de aplicações lógicas. Para cancelar um trabalho único, pode utilizar [Workflow Runs - Cancele](https://docs.microsoft.com/rest/api/logic/workflowruns/cancel) na Logic Apps REST API. Quando enviar uma chamada para o gatilho, forneça o ID de [funcionamento](#workflow-run-id)de fluxo de trabalho .

## <a name="schedule-recurring-jobs"></a>Agendar empregos recorrentes

1. No [portal Azure, crie](https://portal.azure.com)uma aplicação lógica em branco no Logic App Designer.

   Para os passos básicos, siga [o Quickstart: Crie a sua primeira aplicação lógica.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

1. Na caixa de pesquisa, introduza "recorrência" como filtro. A partir da lista de gatilhos, selecione este gatilho: **Recurrence**

   ![Adicione o gatilho "Recurrence"](./media/migrate-from-scheduler-to-logic-apps/recurrence-trigger.png)

1. Estabeleça um horário mais avançado, se quiser.

   ![Horário avançado](./media/migrate-from-scheduler-to-logic-apps/recurrence-advanced-schedule.png)

   Para obter mais informações sobre opções avançadas de agendamento, consulte [Criar e executar tarefas e fluxos de trabalho recorrentes com aplicações lógicas azure](../connectors/connectors-native-recurrence.md).

1. Adicione outras ações que pretende selecionando a partir de [centenas de pronto-a-usar](../connectors/apis-list.md). Sob o gatilho, selecione **Próximo passo**. Encontre e selecione as ações que deseja.

   Por exemplo, pode incluir uma ação HTTP que envia um pedido para um URL, ou ações que funcionam com filas de armazenamento, filas de ônibus de serviço ou tópicos de ônibus de serviço:

   ![Ação HTTP](./media/migrate-from-scheduler-to-logic-apps/recurrence-http-action.png)

1. Quando terminar, guarde a sua aplicação lógica.

   ![Guardar a aplicação lógica](./media/migrate-from-scheduler-to-logic-apps/save-logic-app.png)

## <a name="advanced-setup"></a>Configuração avançada

Aqui estão outras formas de personalizar o seu trabalho.

### <a name="retry-policy"></a>Política de repetição

Para controlar a forma como uma ação tenta repetir na sua aplicação lógica quando falhas intermitentes acontecem, pode definir a política de [retry](../logic-apps/logic-apps-exception-handling.md#retry-policies) nas definições de cada ação, por exemplo:

1. Abra as elipses da ação **(...**) menu e selecione **Definições**.

   ![Definições de ação aberta](./media/migrate-from-scheduler-to-logic-apps/action-settings.png)

1. Selecione a política de retry que deseja. Para obter mais informações sobre cada política, consulte [as políticas de Retry](../logic-apps/logic-apps-exception-handling.md#retry-policies).

   ![Selecione a política de retry](./media/migrate-from-scheduler-to-logic-apps/retry-policy.png)

## <a name="handle-exceptions-and-errors"></a>Lidar com exceções e erros

No Programador Azure, se a ação padrão não funcionar, pode executar uma ação alterativa que aborda a condição de erro. Nas Aplicações Lógicas Azure, também pode realizar a mesma tarefa.

1. Na Logic App Designer, acima da ação que pretende manusear, mova o ponteiro sobre a seta entre passos e selecione **Adicionar um ramo paralelo**.

   ![Adicionar ramo paralelo](./media/migrate-from-scheduler-to-logic-apps/add-parallel-branch.png)

1. Encontre e selecione a ação que pretende executar em vez disso como a ação alternativa.

   ![Adicionar ação paralela](./media/migrate-from-scheduler-to-logic-apps/add-parallel-action.png)

1. Na ação alternativa, abra o menu elipses (**...**) e selecione **Configure correr depois**.

   ![Configurar correr depois](./media/migrate-from-scheduler-to-logic-apps/configure-run-after.png)

1. Limpe a caixa para a propriedade **de sucesso.** Selecione estas propriedades: **falhou,** **é ignorada,** e **tem tempo esgotado**

   ![Configurar propriedades "correr depois"](./media/migrate-from-scheduler-to-logic-apps/select-run-after-properties.png)

1. Quando tiver terminado, selecione **Concluído**.

Para saber mais sobre o manuseamento de exceções, consulte [erros e exceções do Handle - Propriedade RunAfter](../logic-apps/logic-apps-exception-handling.md#control-run-after-behavior).

## <a name="faq"></a>FAQ

<a name="retire-date"></a>

**P:** Quando é que o Azure Scheduler se vai reformar? <br>
**R**: O Programador Azure está previsto reformar-se totalmente a 31 de dezembro de 2019. Para que as medidas importantes tomem medidas antes desta data e uma cronologia detalhada, consulte o alargamento da data de [reforma para o Programador até 31 de dezembro de 2019](https://azure.microsoft.com/updates/extending-retirement-date-of-scheduler/). Para atualizações gerais, consulte [atualizações do Azure - Scheduler](https://azure.microsoft.com/updates/?product=scheduler).

**P:** O que acontece às minhas coleções de emprego e empregos após a reforma do serviço? <br>
**R:** Todas as coleções de emprego e emprego sem horário saem de funcionamento e são eliminadas do sistema.

**P:** Tenho de fazer apoio ou executar outras tarefas antes de migrar os meus trabalhos de Scheduler para Apps Lógicas? <br>
**R:** Como uma boa prática, sempre recue o seu trabalho. Verifique se as aplicações lógicas que criou estão a funcionar como esperado antes de apagar ou desativar os seus trabalhos de Scheduler.

**P:** Existe uma ferramenta que me ajude a migrar os meus empregos de Scheduler para Aplicações Lógicas? <br>
**R:** Cada trabalho de Scheduler é único, por isso não existe uma ferramenta de tamanho único. No entanto, com base nas suas necessidades, pode [editar este guião para migrar empregos do Programador Azure para aplicações lógicas azure](https://github.com/Azure/logicapps/tree/master/scripts/scheduler-migration).

**P:** Onde posso obter apoio para migrar os meus empregos de Agendadores? <br>
**R:** Aqui estão algumas formas de obter apoio:

**Portal Azure**

Se a sua subscrição Azure tiver um plano de suporte pago, pode criar um pedido de suporte técnico no portal Azure. Caso contrário, pode selecionar uma opção de suporte diferente.

1. No menu principal do [portal Azure,](https://portal.azure.com) selecione **Ajuda + suporte**.

1. No menu **suporte,** selecione **Novo pedido**de suporte . Forneça esta informação sobre o seu pedido:

   | Propriedade | Valor |
   |---------|-------|
   | **Tipo de emissão** | **Parte Técnica** |
   | **Assinatura** | <*sua assinatura Azure*> |
   | **Serviço** | Sob **monitorização & Management,** selecione **Scheduler**. Se não encontrar o **Scheduler,** selecione **todos os serviços** primeiro. |
   ||| 

1. Selecione a opção de suporte que deseja. Se tiver um plano de apoio pago, selecione **Next**.

**Comunidade**

* [Fórum de Aplicações lógicas azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-scheduler)

## <a name="next-steps"></a>Passos seguintes

* [Criar tarefas e fluxos de trabalho regulares com aplicações lógicas azure](../connectors/connectors-native-recurrence.md)
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
ms.date: 09/20/2018
ms.openlocfilehash: 0225a9f34e016a4b1de51c06ba982d384e41007c
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302080"
---
# <a name="migrate-azure-scheduler-jobs-to-azure-logic-apps"></a>Migrar trabalhos do Agendador do Azure para aplicativos lógicos do Azure

> [!IMPORTANT]
> O aplicativo lógico do Azure está substituindo o Agendador do Azure, que está sendo desativado. Para agendar trabalhos, siga este artigo para migrar para os aplicativos lógicos do Azure em vez disso.

Este artigo mostra como você pode agendar trabalhos de uso único e recorrente criando fluxos de trabalho automatizados com aplicativos lógicos do Azure, em vez de com o Agendador do Azure. Ao criar trabalhos agendados com aplicativos lógicos, você obtém estes benefícios:

* Você não precisa se preocupar com o conceito de uma *coleção de trabalhos* porque cada aplicativo lógico é um recurso separado do Azure.

* Você pode executar vários trabalhos únicos usando um único aplicativo lógico.

* O serviço de aplicativos lógicos do Azure dá suporte ao fuso horário e horário de Verão (DST).

Para saber mais, confira [o que são os aplicativos lógicos do Azure?](../logic-apps/logic-apps-overview.md) ou tente criar seu primeiro aplicativo lógico neste guia de início rápido: [Crie seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se para obter uma conta do Azure gratuita</a>.

* Para disparar seu aplicativo lógico enviando solicitações HTTP, use uma ferramenta como o aplicativo de [área de trabalho do postmaster](https://www.getpostman.com/apps).

## <a name="schedule-one-time-jobs"></a>Agendar trabalhos de uso único

Você pode executar vários trabalhos únicos criando apenas um único aplicativo lógico. 

### <a name="create-your-logic-app"></a>Criar uma aplicação lógica

1. No [portal do Azure](https://portal.azure.com), crie um aplicativo lógico em branco no designer de aplicativo lógico. 

   Para as etapas básicas, siga [o início rápido: Crie seu primeiro aplicativo](../logic-apps/quickstart-create-first-logic-app-workflow.md)lógico.

1. Na caixa de pesquisa, digite "quando uma solicitação HTTP" como seu filtro. Na lista de gatilhos, selecione este gatilho: **Quando uma solicitação HTTP é recebida** 

   ![Adicionar gatilho de "solicitação"](./media/migrate-from-scheduler-to-logic-apps/request-trigger.png)

1. Para o gatilho de solicitação, você pode, opcionalmente, fornecer um esquema JSON, que ajuda o designer de aplicativo lógico a entender a estrutura das entradas da solicitação de entrada e torna as saídas mais fáceis de selecionar posteriormente no fluxo de trabalho.

   Para especificar um esquema, insira o esquema na caixa **esquema JSON do corpo da solicitação** , por exemplo: 

   ![Esquema de solicitação](./media/migrate-from-scheduler-to-logic-apps/request-schema.png)

   Se você não tiver um esquema, mas tiver um conteúdo de exemplo no formato JSON, poderá gerar um esquema a partir dessa carga.

   1. No gatilho de solicitação, escolha **usar conteúdo de exemplo para gerar o esquema**.

   1. Em **Inserir ou colar um exemplo de carga JSON**, forneça seu conteúdo de exemplo e, em seguida, escolha **concluído**, por exemplo:

      ![Conteúdo de exemplo](./media/migrate-from-scheduler-to-logic-apps/sample-payload.png)

1. No gatilho, escolha a **próxima etapa**. 

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

Por exemplo, usando o aplicativo de postmaster, você pode criar uma solicitação POST com as configurações semelhantes a este exemplo e, em seguida, escolher **Enviar** para fazer a solicitação.

| Método de solicitação | URL | Corpo | Cabeçalhos |
|----------------|-----|------|---------| 
| **POST** | <*endpoint-URL*> | **recebem** <p>**JSON(application/json)** <p>Na caixa **bruto** , insira a carga que você deseja enviar na solicitação. <p>**Nota**: Essa configuração configura automaticamente os valores dos **cabeçalhos** . | **Chave**: Tipo de conteúdo <br>**Valor**: aplicativo/JSON
 |||| 

![Enviar solicitação para disparar o aplicativo lógico manualmente](./media/migrate-from-scheduler-to-logic-apps/postman-send-post-request.png)

Depois de enviar a chamada, a resposta do seu aplicativo lógico aparece sob a caixa **bruta** na guia **corpo** . 

<a name="workflow-run-id"></a>

> [!IMPORTANT]
>
> Se você quiser cancelar o trabalho mais tarde, escolha a guia **cabeçalhos** . Localize e copie o valor do cabeçalho **x-MS-Workflow-Run-ID** na resposta. 
>
> ![Resposta](./media/migrate-from-scheduler-to-logic-apps/postman-response.png)

## <a name="cancel-a-one-time-job"></a>Cancelar um trabalho ocasional

Em aplicativos lógicos, cada trabalho único é executado como uma única instância de execução do aplicativo lógico. Para cancelar um trabalho único, você pode usar execuções de [Workflow-cancelar](https://docs.microsoft.com/rest/api/logic/workflowruns/cancel) na API REST de aplicativos lógicos. Quando você envia uma chamada para o gatilho, forneça a [ID de execução do fluxo de trabalho](#workflow-run-id).

## <a name="schedule-recurring-jobs"></a>Agendar tarefas periódicas

### <a name="create-your-logic-app"></a>Criar uma aplicação lógica

1. No [portal do Azure](https://portal.azure.com), crie um aplicativo lógico em branco no designer de aplicativo lógico. 

   Para as etapas básicas, siga [o início rápido: Crie seu primeiro aplicativo](../logic-apps/quickstart-create-first-logic-app-workflow.md)lógico.

1. Na caixa de pesquisa, digite "recorrência" como filtro. Na lista de gatilhos, selecione este gatilho: **Periodicidade** 

   ![Adicionar gatilho de "recorrência"](./media/migrate-from-scheduler-to-logic-apps/recurrence-trigger.png)

1. Configure uma agenda mais avançada, se desejar.

   ![Agendamento avançado](./media/migrate-from-scheduler-to-logic-apps/recurrence-advanced-schedule.png)

   Para obter mais informações sobre opções de agendamento avançadas, consulte [criar e executar tarefas recorrentes e fluxos de trabalho com aplicativos lógicos do Azure](../connectors/connectors-native-recurrence.md)

1. Adicione outras ações desejadas, selecionando entre [centenas de pronto para uso](../connectors/apis-list.md). No gatilho, escolha a **próxima etapa**. Localize e selecione as ações desejadas.

   Por exemplo, você pode incluir uma ação HTTP que envia uma solicitação para uma URL ou ações que funcionam com filas de armazenamento, filas do barramento de serviço ou tópicos do barramento de serviço: 

   ![Ação HTTP](./media/migrate-from-scheduler-to-logic-apps/recurrence-http-action.png)

1. Quando tiver terminado, salve seu aplicativo lógico.

   ![Guardar a aplicação lógica](./media/migrate-from-scheduler-to-logic-apps/save-logic-app.png)

## <a name="advanced-setup"></a>Configuração avançada

Aqui estão outras maneiras que você pode personalizar seus trabalhos.

### <a name="retry-policy"></a>Política de repetição

Para controlar a maneira como uma ação tenta executar novamente em seu aplicativo lógico quando ocorrem falhas intermitentes, você pode definir a [política de repetição](../logic-apps/logic-apps-exception-handling.md#retry-policies) nas configurações de cada ação, por exemplo:

1. Abra o menu da ação ( **...** ) e selecione **configurações**.

   ![Abrir configurações de ação](./media/migrate-from-scheduler-to-logic-apps/action-settings.png)

1. Selecione a política de repetição desejada. Para obter mais informações sobre cada política, consulte [políticas de repetição](../logic-apps/logic-apps-exception-handling.md#retry-policies).

   ![Selecionar política de repetição](./media/migrate-from-scheduler-to-logic-apps/retry-policy.png)

## <a name="handle-exceptions-and-errors"></a>Tratar exceções e erros

No Agendador do Azure, se a ação padrão falhar ao ser executada, você poderá executar uma ação alternativa que resolve a condição de erro. Em aplicativos lógicos do Azure, você também pode executar a mesma tarefa.

1. No designer de aplicativo lógico, acima da ação que você deseja manipular, mova o ponteiro sobre a seta entre as etapas e selecione e **adicione uma ramificação paralela**. 

   ![Adicionar ramificação paralela](./media/migrate-from-scheduler-to-logic-apps/add-parallel-branch.png)

1. Localize e selecione a ação que deseja executar, em vez disso, como a ação alternativa.

   ![Adicionar ação paralela](./media/migrate-from-scheduler-to-logic-apps/add-parallel-action.png)

1. Na ação alternativa, abra o menu ( **...** ) e selecione **configurar execução após**.

   ![Configurar execução após](./media/migrate-from-scheduler-to-logic-apps/configure-run-after.png)

1. Desmarque a caixa a propriedade **é bem-sucedida** . Selecione estas propriedades: **falha**, **foi ignorado**e atingiu o **tempo limite**

   ![Configurar propriedades de "executar após"](./media/migrate-from-scheduler-to-logic-apps/select-run-after-properties.png)

1. Quando tiver terminado, selecione **Concluído**.

Para saber mais sobre a manipulação de exceção, consulte [tratar erros e exceções – Propriedade RunAfter](../logic-apps/logic-apps-exception-handling.md#catch-and-handle-failures-with-the-runafter-property).

## <a name="faq"></a>FAQ

<a name="retire-date"></a> 

**P**: Quando o Agendador do Azure é desativado? <br>
**A**: O Agendador do Azure está agendado para ser desativado em 30 de setembro de 2019.

**P**: O que acontece com meus trabalhos e coleções de trabalho do Agendador depois que o serviço se aposenta? <br>
**A**: Todos os trabalhos e coleções de trabalho do Agendador serão excluídos do sistema.

**P**: É necessário fazer backup ou executar outras tarefas antes de migrar meus trabalhos do Agendador para os aplicativos lógicos? <br>
**A**: Como prática recomendada, sempre faça backup do seu trabalho. Verifique se os aplicativos lógicos que você criou estão sendo executados conforme o esperado antes de excluir ou desabilitar os trabalhos do Agendador. 

**P**: Há uma ferramenta que pode me ajudar a migrar meus trabalhos do Agendador para os aplicativos lógicos? <br>
**A**: Cada trabalho do Agendador é exclusivo e, portanto, uma ferramenta de tamanho único não existe. No entanto, vários scripts estarão disponíveis para você modificar para suas necessidades. Para disponibilidade de script, verifique novamente mais tarde.

**P**: Onde posso obter suporte para migrar meus trabalhos do Agendador? <br>
**A**: Aqui estão algumas maneiras de obter suporte: 

**Azure portal**

Se sua assinatura do Azure tiver um plano de suporte pago, você poderá criar uma solicitação de suporte técnico no portal do Azure. Caso contrário, você pode selecionar uma opção de suporte diferente.

1. No menu [portal do Azure](https://portal.azure.com) principal, selecione **ajuda + suporte**.

1. Em **suporte**, selecione **nova solicitação de suporte**. Forneça estes detalhes para sua solicitação:

   | Definição | Value |
   |---------|-------|
   | **Tipo de problema** | **Technical** | 
   | **Subscrição** | <*your-Azure-subscription*> | 
   | **Serviço** | Em **monitoramento & gerenciamento**, selecione **Agendador**. | 
   ||| 

1. Selecione a opção de suporte desejada. Se você tiver um plano de suporte pago, escolha **Avançar**.

**Comunidade**

* [Fórum de aplicativos lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-scheduler)

## <a name="next-steps"></a>Passos Seguintes

* [Criar tarefas e fluxos de trabalho em execução regularmente com aplicativos lógicos do Azure](../connectors/connectors-native-recurrence.md)
* [Tutorial: Verificar o tráfego com um aplicativo lógico baseado em agenda](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)

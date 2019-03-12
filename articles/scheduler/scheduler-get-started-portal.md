---
title: Criar tarefas agendadas com o Microsoft Azure Scheduler – portal do Azure | Microsoft Docs
description: Saiba como criar, agendar e executar a sua primeira tarefa automatizada com o Microsoft Azure Scheduler no portal do Azure
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: e69542ec-d10f-4f17-9b7a-2ee441ee7d68
ms.topic: conceptual
ms.date: 09/17/2018
ms.openlocfilehash: 7e5abf1100c09f8f28d268b3f2defe953c5f36f0
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57770140"
---
# <a name="create-and-schedule-your-first-job-with-azure-scheduler---azure-portal"></a>Criar e agendar a primeira tarefa com o Microsoft Azure Scheduler – portal do Azure

> [!IMPORTANT]
> O [Azure Logic Apps](../logic-apps/logic-apps-overview.md) está a substituir o Microsoft Azure Scheduler, que está a ser descontinuado. Para agendar tarefas, [experimente antes o Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

Este tutorial mostra como pode criar e agendar facilmente uma tarefa e, em seguida, monitorizar e gerir essa tarefa. 

Se não tiver uma subscrição do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se para obter uma conta do Azure gratuita</a>.

## <a name="create-job"></a>Criar tarefa

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).  

1. No menu principal do Azure, selecione **Criar um recurso**. Na caixa de pesquisa, introduza “scheduler”. Na lista de resultados, selecione **Scheduler** e, em seguida, escolha **Criar**.

   ![Criar o recurso do Scheduler](./media/scheduler-get-started-portal/scheduler-v2-portal-marketplace-create.png)

   Agora, crie uma tarefa que envia um pedido GET para este URL: `http://www.microsoft.com/` 

1. Em **Tarefa do Scheduler**, introduza estas informações:

   | Propriedade | Valor de exemplo | Descrição |
   |----------|---------------|-------------| 
   | **Nome** | getMicrosoft | O nome da tarefa | 
   | **Coleção de tarefas** | <*job-collection-name*> | Criar uma coleção de tarefas ou selecionar uma coleção existente | 
   | **Subscrição** | <*Azure-subscription-name*> | O nome para a subscrição do Azure | 
   |||| 

1. Selecione **Definições da ação – Configurar**, forneça estas informações e, em seguida, escolha **OK** quando terminar:

   | Propriedade | Valor de exemplo | Descrição |
   |----------|---------------|-------------| 
   | **ação** | **Http** | O tipo de ação a executar | 
   | **Método** | **Get** | O método a chamar | 
   | **URL** | **http://www.microsoft.com** | O URL de destino | 
   |||| 
   
   ![Definir a tarefa](./media/scheduler-get-started-portal/scheduler-v2-portal-action-settings.png)

1. Selecione **Agendar – Configurar**, defina a agenda e, em seguida, selecione **OK** quando terminar:

   Embora seja possível criar uma tarefa única, este exemplo configura uma agenda de periodicidade.

   | Propriedade | Valor de exemplo | Descrição |
   |----------|---------------|-------------| 
   | **Periodicidade** | **Periódico** | Uma tarefa única ou periódica | 
   | **Começar a** | <*data de hoje*> | A data de início da tarefa | 
   | **Repetir a cada** | **hora** | O intervalo e a frequência da periodicidade | 
   | **End** | **Terminar** dois dias depois da data de hoje | A data de fim da tarefa | 
   | **Diferença para UTC** | **UTC +08:00** | A diferença de hora entre a Hora Universal Coordenada (UTC) e a hora observada na sua localização | 
   |||| 

   ![Definir a agenda](./media/scheduler-get-started-portal/scheduler-v2-portal-recurrence-schedule.png)

1. Quando estiver preparado, escolha **Criar**.

   Depois de criar a tarefa, o Azure implementa-a e, em seguida, é apresentada no dashboard do Azure. 

1. Quando o Azure mostra uma notificação que a implementação foi concluída com êxito, escolha **Afixar no dashboard**. Caso contrário, escolha o ícone **Notificações** (campainha) na barra de ferramentas do Azure e, em seguida, escolha **Afixar no dashboard**.

## <a name="monitor-and-manage-jobs"></a>Monitorizar e gerir tarefas

Para rever, monitorizar e gerir a tarefa, no dashboard do Azure, escolha a sua tarefa. Em **Definições**, pode rever e gerir estas áreas da tarefa:

![Definições da tarefa](./media/scheduler-get-started-portal/scheduler-v2-portal-job-overview-1.png)

Para obter mais informações sobre estas áreas, selecione uma área:

* [**Propriedades**](#properties)
* [**Definições da ação**](#action-settings)
* [**Agenda**](#schedule)
* [**Histórico**](#history)
* [**Utilizadores**](#users)

<a name="properties"></a>

### <a name="properties"></a>Propriedades

Para ver as propriedades só de leitura que descrevem os metadados de gestão da tarefa, selecione **Propriedades**.

![Ver propriedades da tarefa](./media/scheduler-get-started-portal/scheduler-v2-portal-job-properties.png)

<a name="action-settings"></a>

### <a name="action-settings"></a>Definições da ação

Para alterar as definições avançadas da tarefa, selecione **Definições da ação**. 

![Definições da ação de revisão](./media/scheduler-get-started-portal/scheduler-v2-portal-job-action-settings.png)

| Tipo de ação | Descrição | 
|-------------|-------------| 
| Todos os tipos | Pode alterar a **Política de repetição** e as definições da **Ação de erro**. | 
| HTTP e HTTPS | Pode alterar o **Método** para qualquer método permitido. Também pode adicionar, eliminar ou alterar os cabeçalhos e as informações da autenticação básica. | 
| Fila de armazenamento| Pode alterar a conta de armazenamento, o nome da fila, o token de SAS e o corpo. | 
| Service Bus | Pode alterar o espaço de nomes, o caminho do tópico ou da fila, as definições de autenticação, o tipo de transporte, as propriedades e o corpo da mensagem. | 
||| 

<a name="schedule"></a>

### <a name="schedule"></a>Agenda

Se configurar uma agenda através do assistente de tarefas, poderá alterar essa agenda, tais como a data e hora de início, a agenda de periodicidade e a data e hora de fim das tarefas periódicas.
Também pode criar [agendas mais complexas e periodicidades avançadas](scheduler-advanced-complexity.md).

Para alterar a vista ou alterar a agenda da tarefa, selecione **Agenda**:

![Ver a agenda de tarefas](./media/scheduler-get-started-portal/scheduler-v2-portal-job-schedule.png)

<a name="history"></a>

### <a name="history"></a>Histórico

Para ver as métricas sobre cada execução de uma tarefa selecionada, selecione **Histórico**. Estas métricas fornecem valores em tempo real sobre o estado de funcionamento da tarefa, como o estado, o número de repetições, o número de ocorrências, a hora de início e a hora de fim.

![Ver histórico de tarefas e métricas](./media/scheduler-get-started-portal/scheduler-v2-portal-job-history.png)

Para ver os detalhes do histórico de cada execução, tal como a resposta completa de cada execução, em **Histórico**, selecione cada execução. 

![Ver detalhes do histórico de tarefas](./media/scheduler-get-started-portal/scheduler-v2-portal-job-history-details.png)

<a name="users"></a>

### <a name="users"></a>Utilizadores

Pode gerir o acesso ao Azure Scheduler para cada utilizador num nível granular com o Controlo de Acesso Baseado em Funções (RBAC). Para saber como configurar o acesso baseado em funções, veja [Gerir o acesso através do RBAC](../role-based-access-control/role-assignments-portal.md)

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [conceitos, terminologia e hierarquia de entidades](scheduler-concepts-terms.md)
* [Criar agendas complexas e periodicidade avançada](scheduler-advanced-complexity.md)
* Saiba mais sobre [elevada disponibilidade e fiabilidade do Scheduler](scheduler-high-availability-reliability.md)
* Saiba mais sobre [limites, quotas, valores predefinidos e códigos de erro](scheduler-limits-defaults-errors.md)

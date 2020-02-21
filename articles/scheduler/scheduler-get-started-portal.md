---
title: Criar empregos programados - Portal Azure
description: Crie, marque e execute o seu primeiro trabalho automatizado no portal Azure usando o Programador Azure
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: estfan
ms.reviewer: klam, estfan, logicappspm
ms.topic: conceptual
ms.date: 02/29/2020
ms.openlocfilehash: a9f7169f4b54dfc08612b1d53bfde48154ee2d1d
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2020
ms.locfileid: "77524806"
---
# <a name="create-and-schedule-your-first-job-by-using-azure-scheduler---azure-portal"></a>Crie e agende o seu primeiro trabalho utilizando o Portal Azure Scheduler - Azure

> [!IMPORTANT]
> [A Azure Logic Apps](../logic-apps/logic-apps-overview.md) está a substituir o Programador Azure, que está [a ser reformado.](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date) Para continuar a trabalhar com os trabalhos que criou no Scheduler, por [favor, emigre para as Aplicações Lógicas Azure](../scheduler/migrate-from-scheduler-to-logic-apps.md) o mais rapidamente possível.

Este tutorial mostra como pode criar e agendar facilmente uma tarefa e, em seguida, monitorizar e gerir essa tarefa.

Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

## <a name="create-job"></a>Criar tarefa

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).

1. Na caixa de pesquisa Azure, introduza `scheduler` como filtro. Na lista de resultados, selecione **Scheduler Job Collections,** e selecione **Create**.

   ![Criar o recurso do Scheduler](./media/scheduler-get-started-portal/scheduler-v2-portal-marketplace-create.png)

   Agora, crie uma tarefa que envia um pedido GET para este URL: `https://www.microsoft.com/` 

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
   | **Ação** | **Http** | O tipo de ação a executar | 
   | **Método** | **Get** | O método a chamar | 
   | **URL** | **https://www.microsoft.com** | O URL de destino | 
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

### <a name="action-settings"></a>Definições de ação

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

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [conceitos, terminologia e hierarquia de entidades](scheduler-concepts-terms.md)
* [Criar agendas complexas e periodicidade avançada](scheduler-advanced-complexity.md)
* Saiba mais sobre [elevada disponibilidade e fiabilidade do Scheduler](scheduler-high-availability-reliability.md)
* Saiba mais sobre [limites, quotas, valores predefinidos e códigos de erro](scheduler-limits-defaults-errors.md)

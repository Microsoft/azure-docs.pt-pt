---
title: Conceitos, termos e entidades – Azure Scheduler | Microsoft Docs
description: Conheça os conceitos, a terminologia e a hierarquia de entidades, incluindo as tarefas e as coleções de tarefas, no Azure Scheduler
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 3ef16fab-d18a-48ba-8e56-3f3e0a1bcb92
ms.topic: conceptual
ms.date: 08/18/2016
ms.openlocfilehash: a58b247732125574a067deff1d5b03859cd036fc
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57782297"
---
# <a name="concepts-terminology-and-entities-in-azure-scheduler"></a>Conceitos, terminologia e entidades no Azure Scheduler

> [!IMPORTANT]
> O [Azure Logic Apps](../logic-apps/logic-apps-overview.md) está a substituir o Microsoft Azure Scheduler, que está a ser descontinuado. Para agendar tarefas, [experimente antes o Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

## <a name="entity-hierarchy"></a>Hierarquia de entidades

A API REST do Azure Scheduler expõe e utiliza estas entidades principais ou recursos:

| Entidade | Descrição |
|--------|-------------|
| **Tarefa** | Define uma única ação periódica, com estratégias de execução simples ou complexas. As ações podem incluir pedidos HTTP, da fila do Armazenamento, da fila do Service Bus ou do tópico do Service Bus. | 
| **Coleção de tarefas** | Contém um grupo de tarefas e mantém as definições, quotas e limitações que são partilhadas pelas tarefas na coleção. Enquanto proprietário da subscrição do Azure, pode criar coleções de tarefas e agrupar as tarefas com base nos limites da sua utilização ou aplicação. Uma coleção de tarefas tem estes atributos: <p>- Está restringida a uma região. <br>- Permite-lhe impor quotas para poder restringir a utilização de todas as tarefas numa coleção. <br>- As quotas incluem MaxJobs e MaxRecurrence. | 
| **Histórico de tarefas** | Descreve os detalhes de uma execução de tarefa, por exemplo, o estado e quaisquer detalhes de resposta. |
||| 

## <a name="entity-management"></a>Gestão de entidades

A um nível elevado, a API REST do Scheduler expõe estas operações para a gestão de entidades.

### <a name="job-management"></a>Gestão de tarefas

Suporta operações de criação e edição de tarefas. Todas as tarefas têm de pertencer a uma coleção de tarefas existente, pelo que não existe nenhuma criação implícita. Para obter mais informações, veja [API REST do Scheduler – Tarefas](https://docs.microsoft.com/rest/api/scheduler/jobs). Este é o endereço URI dessas operações:

`https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}`

### <a name="job-collection-management"></a>Gestão de coleções de tarefas

Suporta operações de criação e edição de tarefas e coleções de tarefas, que são mapeadas para quotas e definições partilhadas. Por exemplo, as quotas especificam o número máximo de tarefas e o intervalo de periodicidade mais pequeno. Para obter mais informações, veja [API REST do Scheduler – Coleções de Tarefas](https://docs.microsoft.com/rest/api/scheduler/jobcollections). Este é o endereço URI dessas operações:

`https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}`

### <a name="job-history-management"></a>Gestão do histórico de tarefas

Suporta a operação GET para obter 60 dias do histórico de execuções de tarefas, por exemplo, o tempo decorrido da tarefa e os resultados de execução da tarefa. Inclui suporte de parâmetros de cadeias de consulta para filtrar com base no estado. Para obter mais informações, veja [API REST do Scheduler – Tarefas – Lista do Histórico de Tarefas](https://docs.microsoft.com/rest/api/scheduler/jobs/listjobhistory). Este é o endereço URI desta operação:

`https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}/history`

## <a name="job-types"></a>Tipos de tarefa

O Azure Scheduler suporta vários tipos de tarefas: 

* Tarefas HTTP, incluindo as tarefas HTTPS que suportam SSL, no caso de ter o ponto final de um serviço ou carga de trabalho existente
* Tarefas da fila do Armazenamento para cargas de trabalho que utilizam as filas do Armazenamento, como publicar mensagens nas filas do Armazenamento
* Tarefas de fila do Service Bus para cargas de trabalho que utilizam as filas do Service Bus
* Tarefas do tópico do Service Bus para cargas de trabalho que utilizam os tópicos do Service Bus

## <a name="job-definition"></a>Definição da tarefa

A um nível elevado, uma tarefa do Scheduler tem estas partes básicas:

* A ação que é executada quando é acionado o temporizador da tarefa
* Opcional: O tempo para executar a tarefa
* Opcional: Quando e como muitas vezes repetir a tarefa
* Opcional: Uma ação de erro que é executado se a ação principal falhar

A tarefa também inclui os dados fornecidos pelo sistema, como o próximo tempo de execução agendado da tarefa. A definição do código da tarefa é um objeto no formato JavaScript Object Notation (JSON), que tem estes elementos:

| Elemento | Necessário | Descrição | 
|---------|----------|-------------| 
| [**startTime**](#start-time) | Não | A hora de início da tarefa com um desvio de fuso horário no [formato ISO 8601](http://en.wikipedia.org/wiki/ISO_8601) | 
| [**action**](#action) | Sim | Os detalhes da ação principal, que pode incluir um objeto **errorAction** | 
| [**errorAction**](#error-action) | Não | Os detalhes da ação secundária que será executada se a ação principal falhar |
| [**recurrence**](#recurrence) | Não | Os detalhes, como a frequência e o intervalo de uma tarefa periódica | 
| [**retryPolicy**](#retry-policy) | Não | Os detalhes de quantas vezes se repete uma ação | 
| [**state**](#state) | Sim | Os detalhes do estado atual da tarefa |
| [**status**](#status) | Sim | Os detalhes do estado atual da tarefa, que é controlado pelo serviço |
||||

Veja a seguir um exemplo que mostra uma definição abrangente da tarefa para uma ação HTTP com detalhes de elemento mais completos descritos nas secções posteriores: 

```json
"properties": {
   "startTime": "2012-08-04T00:00Z",
   "action": {
      "type": "Http",
      "request": {
         "uri": "http://contoso.com/some-method", 
         "method": "PUT",          
         "body": "Posting from a timer",
         "headers": {
            "Content-Type": "application/json"
         },
         "retryPolicy": { 
             "retryType": "None" 
         },
      },
      "errorAction": {
         "type": "Http",
         "request": {
            "uri": "http://contoso.com/notifyError",
            "method": "POST"
         }
      }
   },
   "recurrence": {
      "frequency": "Week",
      "interval": 1,
      "schedule": {
         "weekDays": ["Monday", "Wednesday", "Friday"],
         "hours": [10, 22]
      },
      "count": 10,
      "endTime": "2012-11-04"
   },
   "state": "Disabled",
   "status": {
      "lastExecutionTime": "2007-03-01T13:00:00Z",
      "nextExecutionTime": "2007-03-01T14:00:00Z ",
      "executionCount": 3,
      "failureCount": 0,
      "faultedCount": 0
   }
}
```

<a name="start-time"></a>

## <a name="starttime"></a>startTime

No objeto **startTime**, pode especificar a hora de início e um desvio de fuso horário no [formato ISO 8601](http://en.wikipedia.org/wiki/ISO_8601).

<a name="action"></a>

## <a name="action"></a>action

A tarefa do Scheduler executa uma **ação** primária com base na agenda especificada. O Scheduler suporta ações HTTP, da fila do Armazenamento, da fila do Service Bus e do tópico do Service Bus. Se a **ação** primária falhar, o Scheduler poderá executar uma [**errorAction**](#errorAction) secundária que processará o erro. O objeto **action** descreve estes elementos:

* O tipo de serviço da ação
* Os detalhes da ação
* Uma **errorAction** alternativa

O exemplo anterior descreve uma ação HTTP. Veja a seguir um exemplo de uma ação da fila do Armazenamento:

```json
"action": {
   "type": "storageQueue",
   "queueMessage": {
      "storageAccount": "myStorageAccount",  
      "queueName": "myqueue",                
      "sasToken": "TOKEN",                   
      "message": "My message body"
    }
}
```

Veja a seguir um exemplo de uma ação da fila do Service Bus:

```json
"action": {
   "type": "serviceBusQueue",
   "serviceBusQueueMessage": {
      "queueName": "q1",  
      "namespace": "mySBNamespace",
      "transportType": "netMessaging", // Either netMessaging or AMQP
      "authentication": {  
         "sasKeyName": "QPolicy",
         "type": "sharedAccessKey"
      },
      "message": "Some message",  
      "brokeredMessageProperties": {},
      "customMessageProperties": {
         "appname": "FromScheduler"
      }
   }
},
```

Veja a seguir um exemplo de uma ação do tópico do Service Bus:

```json
"action": {
   "type": "serviceBusTopic",
   "serviceBusTopicMessage": {
      "topicPath": "t1",  
      "namespace": "mySBNamespace",
      "transportType": "netMessaging", // Either netMessaging or AMQP
      "authentication": {
         "sasKeyName": "QPolicy",
         "type": "sharedAccessKey"
      },
      "message": "Some message",
      "brokeredMessageProperties": {},
      "customMessageProperties": {
         "appname": "FromScheduler"
      }
   }
},
```

Para obter mais informações sobre os tokens de Assinatura de Acesso Partilhado (SAS), veja [Autorizar com Assinaturas de Acesso Partilhado](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

<a name="error-action"></a>

## <a name="erroraction"></a>errorAction

Se a **ação** primária da tarefa falhar, o Scheduler poderá executar uma **errorAction** secundária que processará o erro. Na **ação** primária, pode especificar um objeto **errorAction** para que o Scheduler possa chamar um ponto final de processamento de erros ou enviar uma notificação ao utilizador. 

Por exemplo, se ocorrer um desastre no ponto final primário, poderá utilizar **errorAction** para chamar um ponto final secundário ou para notificar um ponto final de processamento de erros. 

Tal como a **ação** primária, a ação de erro pode utilizar uma lógica simples ou composta com base noutras ações. 

<a name="recurrence"></a>

## <a name="recurrence"></a>recurrence

Uma tarefa voltará a ocorrer se a definição JSON da tarefa incluir o objeto **recurrence**, por exemplo:

```json
"recurrence": {
   "frequency": "Week",
   "interval": 1,
   "schedule": {
      "hours": [10, 22],
      "minutes": [0, 30],
      "weekDays": ["Monday", "Wednesday", "Friday"]
   },
   "count": 10,
   "endTime": "2012-11-04"
},
```

| Propriedade | Necessário | Value | Descrição | 
|----------|----------|-------|-------------| 
| **frequency** | Sim, quando a **periodicidade** é utilizada | “Minuto”, “Hora”, “Dia”, “Semana”, “Mês”, “Ano” | A unidade de tempo entre ocorrências | 
| **interval** | Não | 1 a 1000, inclusive | Um número inteiro positivo que determina o número de unidades de tempo entre cada ocorrência com base na **frequência** | 
| **schedule** | Não | Varia | Os detalhes das agendas mais complexas e avançadas. Veja **hours**, **hours**, **weekDays**, **months** e **monthDays** | 
| **hours** | Não | 1 a 24 | Um matriz com a hora marca quando a tarefa será executada | 
| **minutes** | Não | 1 a 24 | Um matriz com os minutos marca quando a tarefa será executada | 
| **months** | Não | 1 a 12 | Um matriz com os meses marca quando a tarefa será executada | 
| **monthDays** | Não | Varia | Um matriz com os dias do mês marca quando a tarefa será executada | 
| **weekDays** | Não | “Segunda-feira”, “Terça-feira”, “Quarta-feira”, “Quinta-feira”, “Sexta-feira”, “Sábado”, “Domingo” | Um matriz com os dias da semana marca quando a tarefa será executada | 
| **count** | Não | <*nenhum*> | O número de repetições. A predefinição é a repetição infinita. Não pode utilizar **count** e **endTime** em simultâneo, será respeitada a primeira regra a ser concluída. | 
| **endTime** | Não | <*nenhum*> | A data e hora para quando parar a periodicidade. A predefinição é a repetição infinita. Não pode utilizar **count** e **endTime** em simultâneo, será respeitada a primeira regra a ser concluída. | 
||||

Para obter mais informações sobre estes elementos, veja [Criar agendas complexas e periodicidades avançadas](../scheduler/scheduler-advanced-complexity.md).

<a name="retry-policy"></a>

## <a name="retrypolicy"></a>retryPolicy

Para o caso de uma tarefa do Scheduler poder falhar, pode configurar uma política de repetição, o que determina se e como o Scheduler repete a ação. Por predefinição, o Scheduler tenta repetir a tarefa mais quatro vezes em intervalos de 30 segundos. Pode tornar esta política mais ou menos agressiva; por exemplo, esta política repete uma ação duas vezes por dia:

```json
"retryPolicy": { 
   "retryType": "Fixed",
   "retryInterval": "PT1D",
   "retryCount": 2
},
```

| Propriedade | Necessário | Value | Descrição | 
|----------|----------|-------|-------------| 
| **retryType** | Sim | **Determinado**, **Nenhum** | Determina se especificou uma política de repetição (**determinado**) ou não (**nenhum**). | 
| **retryInterval** | Não | PT30S | Especifica o intervalo e a frequência entre tentativas de repetição no [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). O valor mínimo é 15 segundos e o valor máximo é 18 meses. | 
| **retryCount** | Não | 4 | Especifica o número de tentativas de repetição. O valor máximo é 20. | 
||||

Para obter mais informações, veja [Elevada disponibilidade e fiabilidade](../scheduler/scheduler-high-availability-reliability.md).

<a name="status"></a>

## <a name="state"></a>state

O estado de uma tarefa é **Ativado**, **Desativado**, **Concluído** ou **Em falha**, por exemplo: 

`"state": "Disabled"`

Para alterar as tarefas para o estado **Ativado** ou **Desativado**, pode usar a operação PUT ou PATCH nessas tarefas.
No entanto, se um trabalho tiver o estado **Concluído** ou **Em falha**, não poderá atualizar o estado, embora seja possível executar a operação DELETE na tarefa. O Scheduler elimina as tarefas concluídas e com falha após 60 dias. 

<a name="status"></a>

## <a name="status"></a>status

Depois de uma tarefa ser iniciada, o Scheduler devolve informações sobre o estado da tarefa através do objeto **status**, que é controlado apenas pelo Scheduler. No entanto, é possível encontrar o objeto **status** dentro do objeto **job**. Informações incluídas no estado de uma tarefa:

* Hora da execução anterior, se existir
* Hora da próxima execução agendada para as tarefas em curso
* O número de execuções das tarefas
* O número total de falhas, se existirem
* O número total de falhas, se existirem

Por exemplo:

```json
"status": {
   "lastExecutionTime": "2007-03-01T13:00:00Z",
   "nextExecutionTime": "2007-03-01T14:00:00Z ",
   "executionCount": 3,
   "failureCount": 0,
   "faultedCount": 0
}
```

## <a name="see-also"></a>Consulte também

* [O que é o Microsoft Azure Scheduler?](scheduler-intro.md)
* [Conceitos, terminologia e hierarquia de entidades](scheduler-concepts-terms.md)
* [Criar agendas complexas e periodicidade avançada](scheduler-advanced-complexity.md)
* [Limites, quotas, valores predefinidos e códigos de erro](scheduler-limits-defaults-errors.md)
* [Referência da API REST do Azure Scheduler](/rest/api/scheduler)
* [Referência de cmdlets do PowerShell do Azure Scheduler](scheduler-powershell-reference.md)

---
title: Grelha de Eventos Azure - Ativar registos de diagnóstico para tópicos ou domínios
description: Este artigo fornece instruções passo a passo sobre como ativar registos de diagnóstico para um tópico de grelha de eventos Azure.
ms.topic: how-to
ms.date: 12/03/2020
ms.openlocfilehash: ff00c1438c49cbc9f9e67eba0cf0acef7991a5a4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96576456"
---
#  <a name="enable-diagnostic-logs-for-azure-event-grid-topics-or-domains"></a>Ativar registos de diagnóstico para tópicos ou domínios da grelha de eventos Azure
Este artigo fornece instruções passo a passo para permitir configurações de diagnóstico para tópicos ou domínios da Grelha de Eventos.  Estas definições permitem capturar e visualizar registos **de falha de publicação e entrega.** 

## <a name="prerequisites"></a>Pré-requisitos

- Um tópico de grelha de eventos a provisionado
- Um destino previsto para a captura de registos de diagnóstico. Pode ser um dos seguintes destinos no mesmo local que o tópico da grelha de eventos:
    - Conta de armazenamento do Azure
    - Hub de eventos
    - Área de trabalho do Log Analytics

## <a name="enable-diagnostic-logs-for-a-custom-topic"></a>Ativar registos de diagnóstico para um tópico personalizado

> [!NOTE]
> O procedimento a seguir fornece instruções passo a passo para permitir registos de diagnóstico para um tópico. Os passos para permitir registos de diagnóstico para um domínio são muito semelhantes. No passo 2, navegue para o **domínio** da grelha de eventos no portal Azure.  

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Navegue para o tópico da grelha de eventos para o qual deseja ativar as definições de registo de diagnóstico. 
    1. Na barra de pesquisa no topo, procure **tópicos de grelha de eventos.** 
    
        ![Pesquisa de tópicos personalizados](./media/enable-diagnostic-logs-topic/search-custom-topics.png)
    1. Selecione o **tópico** da lista para a qual pretende configurar as definições de diagnóstico. 
1. Selecione **as definições de diagnóstico** em **Monitorização** no menu esquerdo.
1. Na página de **definições de Diagnóstico,** selecione **Adicionar Nova Definição de Diagnóstico**. 
    
    ![Adicionar botão de definição de diagnóstico](./media/enable-diagnostic-logs-topic/diagnostic-settings-add.png)
5. Especifique um **nome** para a definição de diagnóstico. 
6. Selecione as opções **DeliveryFailures** e **PublishFailures** na secção **Registar.** 
    ![Selecione as falhas](./media/enable-diagnostic-logs-topic/log-failures.png)
7. Ative um ou mais destinos de captura para os registos e, em seguida, configurá-los selecionando um recurso de captura criado anteriormente. 
    - Se selecionar **o Arquivo para uma conta de armazenamento**, selecione a conta de Armazenamento - **Configurar**- e, em seguida, selecione a conta de armazenamento na sua subscrição Azure. 

        ![Screenshot que mostra a página "Definições de diagnóstico" com "Arquivo para uma conta de armazenamento Azure" verificada e uma conta de armazenamento selecionada.](./media/enable-diagnostic-logs-topic/archive-storage.png)
    - Se selecionar **o Stream para um centro de eventos**, selecione Event hub - **Configure**, e, em seguida, selecione o espaço de nomes dos Centros de Eventos, o centro de eventos e a política de acesso. 
        ![Screenshot que mostra a página "Definições de diagnóstico" com "Stream to a event hub" verificado.](./media/enable-diagnostic-logs-topic/archive-event-hub.png)
    - Se selecionar **Enviar para registar análises,** selecione o espaço de trabalho 'Log Analytics'.
        ![Screenshot que mostra a página "Definições de diagnóstico" com "Enviar para registar analytics" verificado.](./media/enable-diagnostic-logs-topic/send-log-analytics.png)
8. Selecione **Guardar**. Em seguida, selecione **X** no canto direito para fechar a página. 
9. Agora, de volta à página de **definições de Diagnóstico,** confirme que vê uma nova entrada na tabela **Definições de Diagnóstico.** 
    ![Screenshot que mostra a página "Definições de diagnóstico" com uma nova entrada realçada na tabela "Definições de Diagnóstico".](./media/enable-diagnostic-logs-topic/diagnostic-setting-list.png)

     Também pode ativar a recolha de todas as métricas para o tópico. 

## <a name="enable-diagnostic-logs-for-a-system-topic"></a>Ativar registos de diagnóstico para um tópico do sistema

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Navegue para o tópico da grelha de eventos para o qual deseja ativar as definições de registo de diagnóstico. 
    1. Na barra de pesquisa no topo, procure **tópicos do sistema de grelha de eventos.** 
    
        ![Pesquisa de tópicos do sistema](./media/enable-diagnostic-logs-topic/search-system-topics.png)
    1. Selecione o tópico do **sistema** para o qual pretende configurar as definições de diagnóstico. 
    
        ![Selecione o tópico do sistema](./media/enable-diagnostic-logs-topic/select-system-topic.png)
3. Selecione **as definições de diagnóstico** em **Monitorização** no menu esquerdo e, em seguida, selecione **Adicionar a definição de diagnóstico**. 

    ![Adicionar definições de diagnóstico - botão](./media/enable-diagnostic-logs-topic/system-topic-add-diagnostic-settings-button.png)
4. Especifique um **nome** para a definição de diagnóstico. 
7. Selecione as **DeliveryFailures** na secção **Registar.** 
    ![Selecione falhas de entrega](./media/enable-diagnostic-logs-topic/system-topic-select-delivery-failures.png)
6. Ative um ou mais destinos de captura para os registos e, em seguida, configurá-los selecionando um recurso de captura criado anteriormente. 
    - Se selecionar **Enviar para registar análises,** selecione o espaço de trabalho 'Log Analytics'.
        ![Enviar para o Log Analytics](./media/enable-diagnostic-logs-topic/system-topic-select-log-workspace.png) 
    - Se selecionar **o Arquivo para uma conta de armazenamento**, selecione a conta de Armazenamento - **Configurar**- e, em seguida, selecione a conta de armazenamento na sua subscrição Azure. 

        ![Arquivar para uma conta de armazenamento Azure](./media/enable-diagnostic-logs-topic/system-topic-select-storage-account.png)
    - Se selecionar **o Stream para um centro de eventos**, selecione Event hub - **Configure**, e, em seguida, selecione o espaço de nomes dos Centros de Eventos, o centro de eventos e a política de acesso. 
        ![Transmitir em fluxo para um hub de eventos](./media/enable-diagnostic-logs-topic/system-topic-select-event-hub.png)
8. Selecione **Guardar**. Em seguida, selecione **X** no canto direito para fechar a página. 
9. Agora, de volta à página de **definições de Diagnóstico,** confirme que vê uma nova entrada na tabela **Definições de Diagnóstico.** 
    ![Definição de diagnóstico na lista](./media/enable-diagnostic-logs-topic/system-topic-diagnostic-settings-targets.png)

     Também pode ativar a recolha de todas as **métricas** para o tópico do sistema.

    ![Tópico do sistema - ativar todas as métricas](./media/enable-diagnostic-logs-topic/system-topics-metrics.png)

## <a name="view-diagnostic-logs-in-azure-storage"></a>Ver registos de diagnóstico no Azure Storage 

1. Uma vez que você ativa uma conta de armazenamento como um destino de captura, a Grade de Evento começa a emitir registos de diagnóstico. Deverá ver novos contentores **nomeados insights-logs-deliveryfailures** e **insights-logs-publishisfailures** na conta de armazenamento. 

    ![Armazenamento - recipientes para registos de diagnóstico](./media/enable-diagnostic-logs-topic/storage-containers.png)
2. Ao navegar por um dos contentores, acabará numa bolha no formato JSON. O ficheiro contém entradas de registo para uma falha de entrega ou uma falha de publicação. O caminho de navegação representa o **ResourceId** do tópico da grelha de evento e o tempotamp (nível de minuto) sobre quando as entradas de registo foram emitidas. O ficheiro blob/JSON, que é transferível, acaba por aderir ao esquema descrito na secção seguinte. 

    [![Ficheiro JSON no armazenamento ](./media/enable-diagnostic-logs-topic/select-json.png)](./media/enable-diagnostic-logs-topic/select-json.png)
3. Deve ver conteúdo no ficheiro JSON semelhante ao seguinte exemplo: 

    ```json
    {
        "time": "2019-11-01T00:17:13.4389048Z",
        "resourceId": "/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID /RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME ",
        "eventSubscriptionName": "SAMPLEDESTINATION",
        "category": "DeliveryFailures",
        "operationName": "Deliver",
        "message": "Message:outcome=NotFound, latencyInMs=2635, id=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx, systemId=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx, state=FilteredFailingDelivery, deliveryTime=11/1/2019 12:17:10 AM, deliveryCount=0, probationCount=0, deliverySchema=EventGridEvent, eventSubscriptionDeliverySchema=EventGridEvent, fields=InputEvent, EventSubscriptionId, DeliveryTime, State, Id, DeliverySchema, LastDeliveryAttemptTime, SystemId, fieldCount=, requestExpiration=1/1/0001 12:00:00 AM, delivered=False publishTime=11/1/2019 12:17:10 AM, eventTime=11/1/2019 12:17:09 AM, eventType=Type, deliveryTime=11/1/2019 12:17:10 AM, filteringState=FilteredWithRpc, inputSchema=EventGridEvent, publisher=DIAGNOSTICLOGSTEST-EASTUS.EASTUS-1.EVENTGRID.AZURE.NET, size=363, fields=Id, PublishTime, SerializedBody, EventType, Topic, Subject, FilteringHashCode, SystemId, Publisher, FilteringTopic, TopicCategory, DataVersion, MetadataVersion, InputSchema, EventTime, fieldCount=15, url=sb://diagnosticlogstesting-eastus.servicebus.windows.net/, deliveryResponse=NotFound: The messaging entity 'sb://diagnosticlogstesting-eastus.servicebus.windows.net/eh-diagnosticlogstest' could not be found. TrackingId:c98c5af6-11f0-400b-8f56-c605662fb849_G14, SystemTracker:diagnosticlogstesting-eastus.servicebus.windows.net:eh-diagnosticlogstest, Timestamp:2019-11-01T00:17:13, referenceId: ac141738a9a54451b12b4cc31a10dedc_G14:"
    }
    ```
## <a name="next-steps"></a>Passos seguintes
Para o esquema de registo e outras informações conceptuais sobre registos de diagnóstico para tópicos ou domínios, consulte [registos de diagnóstico](diagnostic-logs.md).

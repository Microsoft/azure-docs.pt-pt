---
title: Recolher registos da Atividade Azure num espaço de trabalho de Log Analytics entre os inquilinos do Azure  Microsoft Docs
description: Utilize Hubs de eventos e aplicações lógicas para recolher dados do Registo de Atividades do Azure e enviá-lo para um espaço de trabalho de Log Analytics no Azure Monitor em um inquilino diferente.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/06/2019
ms.openlocfilehash: 52bf8b955ef4dc9cfae7fd74fbad0df744609196
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77669272"
---
# <a name="collect-azure-activity-logs-into-azure-monitor-across-azure-active-directory-tenants-legacy"></a>Colete sessão de atividade azure no Azure Monitor através de inquilinos do Diretório Ativo Azure (legado)

> [!NOTE]
> Este artigo descreve o método legado para configurar o log da Atividade Azure através dos inquilinos do Azure para ser recolhido num espaço de trabalho log Analytics.  Agora pode recolher o log da Atividade num espaço de trabalho do Log Analytics utilizando uma definição de diagnóstico semelhante à forma como recolhe registos de recursos. Consulte recolher e analisar registos de atividade do Azure no espaço de [trabalho do Log Analytics no Monitor Azure](activity-log-collect.md).


Este artigo passa por um método para recolher registos de atividade do Azure num espaço de trabalho de Log Analytics no Monitor Azure utilizando o conector de colecionador de dados Azure Log Analytics para Aplicações Lógicas. Utilize o processo neste artigo quando necessitar de enviar registos para um espaço de trabalho num inquilino azure Ative Diretório diferente. Por exemplo, se for um fornecedor de serviços geridos, poderá querer recolher registos de atividades da subscrição de um cliente e armazená-los numa área de trabalho do Log Analytics na sua própria subscrição.

Se o espaço de trabalho do Log Analytics estiver na mesma subscrição do Azure, ou numa subscrição diferente, mas no mesmo Diretório Ativo Azure, utilize os passos no Collect e analise os registos de atividade do Azure no espaço de [trabalho do Log Analytics no Monitor Azure](activity-log-collect.md) para recolher registos de Atividade Azure.

## <a name="overview"></a>Descrição geral

A estratégia utilizada neste cenário consiste em fazer com que o Azure Activity Log envie eventos para um [Hub de Eventos](../../event-hubs/event-hubs-about.md), onde uma [aplicação lógica](../../logic-apps/logic-apps-overview.md) os envia para a sua área de trabalho do Log Analytics. 

![imagem do fluxo de dados do registo de atividade para log Analytics espaço de trabalho](media/collect-activity-logs-subscriptions/data-flow-overview.png)

As vantagens desta abordagem incluem:
- Baixa latência, uma vez que o Registo de Atividades do Azure é transmitido em fluxo para o Hub de Eventos.  A Aplicação Lógica é então desencadeada e publica os dados no espaço de trabalho. 
- Só é preciso código mínimo e não é necessário implementar infraestruturas de servidores.

Este artigo descreve como:
1. Criar um Hub de Eventos. 
2. Exportar registos de atividades para um Hub de Eventos com o perfil de exportação do Registo de Atividades do Azure.
3. Crie uma Aplicação Lógica para ler a partir do Centro de Eventos e envie eventos para log analytics espaço de trabalho.

## <a name="requirements"></a>Requisitos
Seguem-se os requisitos para os recursos do Azure utilizados neste cenário.

- O espaço de nomes do Hub de Eventos tem de estar na mesma subscrição que a subscrição que emite os registos. O utilizador que configura a definição tem de ter permissões de acesso adequadas para ambas as subscrições. Se tiver várias subscrições no mesmo Azure Active Directory, pode enviar os registos de atividades de todas as subscrições para um hub de eventos único.
- A aplicação lógica pode estar numa subscrição diferente do que o hub de eventos e não tem de estar no mesmo Azure Active Directory. A aplicação lógica utiliza a chave de acesso partilhado para ler a partir do Hub de Eventos.
- A área de trabalho do Log Analytics pode estar numa subscrição e num Azure Active Directory diferentes da aplicação lógica. Contudo, para simplificar, recomendamos que estejam na mesma subscrição. A Aplicação Lógica envia para o espaço de trabalho usando o ID e a chave do espaço de trabalho Log Analytics.



## <a name="step-1---create-an-event-hub"></a>Passo 1 - Criar um hub de eventos

<!-- Follow the steps in [how to create an Event Hubs namespace and Event Hub](../../event-hubs/event-hubs-create.md) to create your event hub. -->

1. No portal do Azure, selecione **Criar um recurso** > **Internet das Coisas** > **Hubs de Eventos**.

   ![Novo hub de eventos do Marketplace](media/collect-activity-logs-subscriptions/marketplace-new-event-hub.png)

3. Em **Criar espaço de nomes**, introduza um espaço de nomes novo ou selecione um já existente. O sistema verifica imediatamente a disponibilidade do nome.

   ![imagem da caixa de diálogo “criar hub de eventos”](media/collect-activity-logs-subscriptions/create-event-hub1.png)

4. Escolha o escalão de preço (Básico ou Standard) e uma subscrição, um grupo de recursos e uma localização do Azure para o recurso novo.  Clique em **Criar** para criar o espaço de nomes. Poderá ter de aguardar alguns minutos para que o sistema aprovisione totalmente os recursos.
6. Clique no nome do espaço de nomes que acabou de criar na lista.
7. Selecione **Políticas de acesso partilhado** e clique em **RootManageSharedAccessKey**.

   ![Imagem das políticas de acesso partilhado do hub de eventos](media/collect-activity-logs-subscriptions/create-event-hub7.png)
   
8. Clique no botão Copiar para copiar a cadeia de ligação **RootManageSharedAccessKey** para a área de transferência. 

   ![imagem da chave de acesso partilhado do hub de eventos](media/collect-activity-logs-subscriptions/create-event-hub8.png)

9. Numa localização temporária, como o Bloco de notas, guarde uma cópia do nome do Hub de Eventos e a cadeia de ligação principal ou secundária do Hub de Eventos. A aplicação lógica precisa destes valores.  Para a cadeia de ligação do Hub de Eventos, pode utilizar a cadeia **RootManageSharedAccessKey** ou criar uma separada.  A cadeia de ligação que utilizar tem de começar com `Endpoint=sb://` e destinar-se a uma política que tenha a política de acesso **Gerir**.


## <a name="step-2---export-activity-logs-to-event-hub"></a>Passo 2 - Exportar Registos de Atividade para os Hubs de Eventos

Para ativar a transmissão em fluxo do Registo de Atividade, escolha um Espaço de Nomes do Hub de Eventos e uma política de acesso partilhado para esse espaço. É criado um Hub de Eventos nesse espaço de nomes quando ocorrer o primeiro evento novo do Registo de Atividades. 

Pode utilizar um espaço de nomes de hub de eventos que não esteja na mesma subscrição que a subscrição que emite os registos. No entanto, as subscrições têm de estar no mesmo Azure Active Directory. O utilizador que configura a definição tem de ter o RBAC adequado para aceder a ambas as subscrições. 

1. No portal do Azure, selecione **Monitorizar** > **Registo de Atividades**.
3. Clique no botão **Exportar**, na parte superior da página.

   ![imagem da monitorização do azure na navegação](media/collect-activity-logs-subscriptions/activity-log-blade.png)

4. Selecione a **Subscrição** a partir da qual exportar e clique em **Selecionar Tudo**, no menu pendente **Regiões**, para selecionar eventos de recursos em todas as regiões. Clique na caixa de verificação **Exportar para um hub de eventos**.
7. Clique em **Espaço de Nomes do Service Bus** e selecione a **Subscrição** com o hub de eventos, o **espaço de nomes do hub de eventos** e um **nome de política do hub de eventos**.

    ![imagem da página “exportar para o hub de eventos”](media/collect-activity-logs-subscriptions/export-activity-log2.png)

11. Clique em **OK** e em **Guardar** para guardar estas definições. As definições são imediatamente aplicadas à sua subscrição.

<!-- Follow the steps in [stream the Azure Activity Log to Event Hubs](../../azure-monitor/platform/activity-logs-stream-event-hubs.md) to configure a log profile that writes activity logs to an event hub. -->

## <a name="step-3---create-logic-app"></a>Passo 3 - Criar a Aplicação Lógica

Assim que os registos de atividade estiverem a escrever para o centro do evento, cria-se uma Aplicação Lógica para recolher os registos do centro de eventos e escrevê-los no espaço de trabalho do Log Analytics.

A aplicação lógica inclui o seguinte:
- Um acionador de [conector do Hub de Eventos](https://docs.microsoft.com/connectors/eventhubs/) para ler a partir do Hub de Eventos.
- Uma [ação Parse JSON](../../logic-apps/logic-apps-content-type.md) (Analisar JSON) para extrair os eventos do JSON
- Uma [ação Compose](../../logic-apps/logic-apps-workflow-actions-triggers.md#compose-action) (Compor) para converter o JSON num objeto.
- Um [Log Analytics envia o conector](https://docs.microsoft.com/connectors/azureloganalyticsdatacollector/) de dados para publicar os dados no espaço de trabalho do Log Analytics.

   ![imagem da adição do acionador do hub de eventos nas aplicações lógicas.](media/collect-activity-logs-subscriptions/log-analytics-logic-apps-activity-log-overview.png)

### <a name="logic-app-requirements"></a>Requisitos da Aplicação Lógica
Antes de criar a sua aplicação lógica, confirme que tem as seguintes informações dos passos anteriores:
- O nome do hub de eventos
- A cadeia de ligação do hub de eventos (a principal ou a secundária) do espaço de nomes do Hub de Eventos.
- O ID da área de trabalho do Log Analytics
- A chave partilhada do Log Analytics

Para obter o nome e a cadeia de ligação do Hub de Eventos, siga os passos em [Check Event Hubs namespace permissions and find the connection string](../../connectors/connectors-create-api-azure-event-hubs.md#permissions-connection-string) (Verificar as permissões de espaços de nomes dos Hubs de Eventos e encontrar a cadeia de ligação).


### <a name="create-a-new-blank-logic-app"></a>Criar uma aplicação lógica em branco nova

1. No portal do Azure, escolha **Criar um recurso** > **Enterprise Integration** > **Aplicação Lógica**.

    ![Nova aplicação lógica do Marketplace](media/collect-activity-logs-subscriptions/marketplace-new-logic-app.png)

2. Introduza as definições da tabela abaixo.

    ![Criar uma aplicação lógica](media/collect-activity-logs-subscriptions/create-logic-app.png)

   |Definição | Descrição  |
   |:---|:---|
   | Nome           | Nome exclusivo para a aplicação lógica. |
   | Subscrição   | Selecione a subscrição do Azure que irá conter a aplicação lógica. |
   | Grupo de Recursos | Selecione um grupo de recursos do Azure existente ou crie um novo para a aplicação lógica. |
   | Localização       | Selecione a região do datacenter para implementar a sua aplicação lógica. |
   | Log Analytics  | Selecione se pretender registar o estado de cada execução da sua aplicação lógica num espaço de trabalho de Log Analytics.  |

    
3. Selecione **Criar**. Se for apresentada a mensagem **Implementação Concluída com Êxito**, clique em **Ir para o recurso** para abrir a aplicação lógica.

4. Em **Modelos**, escolha **Aplicação Lógica em Branco**. 

O Estruturador de Aplicações Lógicas mostra-lhe agora os conectores disponíveis e respetivos acionadores, que são utilizados para iniciar o fluxo de trabalho da sua aplicação lógica.

<!-- Learn [how to create a logic app](../../logic-apps/quickstart-create-first-logic-app-workflow.md). -->

### <a name="add-event-hub-trigger"></a>Adicionar o acionador do Hub de Eventos

1. Na caixa de pesquisa do Estruturador da Aplicação Lógica, escreva *hubs de eventos* como filtro. Selecione o acionador **Event Hubs - When events are available in Event Hub** (Hubs de Eventos - quando estiverem disponíveis eventos no Hub de Eventos).

   ![imagem da adição do acionador do hub de eventos nas aplicações lógicas.](media/collect-activity-logs-subscriptions/logic-apps-event-hub-add-trigger.png)

2. Quando lhe forem pedidas as credenciais, ligue-se ao espaço de nomes do Hub de Eventos. Introduza um nome para a ligação e, depois, introduza a cadeia de ligação que copiou.  Selecione **Criar**.

   ![imagem da adição da ligação do hub de eventos nas aplicações lógicas](media/collect-activity-logs-subscriptions/logic-apps-event-hub-add-connection.png)

3. Depois de criar a ligação, edite as definições do acionador. Comece por selecionar **insights-operational-logs** no menu pendente **Event Hub name** (Nome do hub de eventos).

   ![Caixa de diálogo “quando estão disponíveis eventos”](media/collect-activity-logs-subscriptions/logic-apps-event-hub-read-events.png)

5. Expanda **Show advanced options** (Mostrar opções avançadas) e altere **Content type** (Tipo de conteúdo) para *application/json*

   ![Caixa de diálogo da configuração do hub de eventos](media/collect-activity-logs-subscriptions/logic-apps-event-hub-configuration.png)

### <a name="add-parse-json-action"></a>Adicionar ação Parse JSON

A saída do hub de eventos contém um payload JSON com uma matriz de registos. A ação [Parse JSON](../../logic-apps/logic-apps-content-type.md) é usada para extrair apenas o conjunto de registos para envio para log analytics espaço de trabalho.

1. Clique em **New step** (Novo passo)  > **Add an action** (Adicionar uma ação)
2. Na caixa de pesquisa, escreva *parse json* como o filtro. Selecione a ação **Data Operations - Parse JSON** (Operações de Dados - Analisar JSON).

   ![Adicionar ação parse json na aplicação lógica](media/collect-activity-logs-subscriptions/logic-apps-add-parse-json-action.png)

3. Clique no campo **Content** (Conteúdo) e selecione *Body* (Corpo).

4. Copie e cole o seguinte esquema para o campo **Schema** (Esquema).  Este esquema corresponde ao resultado da ação do hub de eventos.  

   ![Caixa de diálogo “parse json”](media/collect-activity-logs-subscriptions/logic-apps-parse-json-configuration.png)

``` json-schema
{
    "properties": {
        "body": {
            "properties": {
                "ContentData": {
                    "type": "string"
                },
                "Properties": {
                    "properties": {
                        "ProfileName": {
                            "type": "string"
                        },
                        "x-opt-enqueued-time": {
                            "type": "string"
                        },
                        "x-opt-offset": {
                            "type": "string"
                        },
                        "x-opt-sequence-number": {
                            "type": "number"
                        }
                    },
                    "type": "object"
                },
                "SystemProperties": {
                    "properties": {
                        "EnqueuedTimeUtc": {
                            "type": "string"
                        },
                        "Offset": {
                            "type": "string"
                        },
                        "PartitionKey": {},
                        "SequenceNumber": {
                            "type": "number"
                        }
                    },
                    "type": "object"
                }
            },
            "type": "object"
        },
        "headers": {
            "properties": {
                "Cache-Control": {
                    "type": "string"
                },
                "Content-Length": {
                    "type": "string"
                },
                "Content-Type": {
                    "type": "string"
                },
                "Date": {
                    "type": "string"
                },
                "Expires": {
                    "type": "string"
                },
                "Location": {
                    "type": "string"
                },
                "Pragma": {
                    "type": "string"
                },
                "Retry-After": {
                    "type": "string"
                },
                "Timing-Allow-Origin": {
                    "type": "string"
                },
                "Transfer-Encoding": {
                    "type": "string"
                },
                "Vary": {
                    "type": "string"
                },
                "X-AspNet-Version": {
                    "type": "string"
                },
                "X-Powered-By": {
                    "type": "string"
                },
                "x-ms-request-id": {
                    "type": "string"
                }
            },
            "type": "object"
        }
    },
    "type": "object"
}
```

>[!TIP]
> Pode obter um payload de exemplo ao clicar em **Run** (Executar) e observar **Raw Output** (Saída não processada) no hub de eventos.  Em seguida, pode utilizar esse resultado com **Use sample payload to generate schema** (Utilizar payload de exemplo para gerar esquema) na atividade **Parse JSON** para gerar o esquema.

### <a name="add-compose-action"></a>Adicionar ação compor
A ação [Compose](../../logic-apps/logic-apps-workflow-actions-triggers.md#compose-action) utiliza a saída JSON e cria um objeto que pode ser utilizado pela ação do Log Analytics.

1. Clique em **New step** (Novo passo)  > **Add an action** (Adicionar uma ação)
2. Escreva *compor* como o filtro e, em seguida, selecione a ação **Data Operations - Compose** (Operações de Dados - Compor).

    ![Adicionar ação compor](media/collect-activity-logs-subscriptions/logic-apps-add-compose-action.png)

3. Clique no campo **Inputs** (Entradas) e selecione **Body**, na atividade **Parse JSON**.


### <a name="add-log-analytics-send-data-action"></a>Adicionar ação Send Data do Log Analytics
A ação de Colecionador de [Dados Azure Log Analytics](https://docs.microsoft.com/connectors/azureloganalyticsdatacollector/) retira o objeto da ação Compor e envia-o para um espaço de trabalho de Log Analytics.

1. Clique em **New step** (Novo passo)  > **Add an action** (Adicionar uma ação)
2. Escreva *log analytics* como o filtro e selecione a ação **Azure Log Analytics Data Collector - Send Data** (Recoletor de Dados do Azure Log Analytics - Enviar Dados).

   ![Adicionar a ação “log analytics send data” nas aplicações lógicas](media/collect-activity-logs-subscriptions/logic-apps-send-data-to-log-analytics-connector.png)

3. Introduza um nome para a sua ligação e cole **Workspace ID** (ID da Área de Trabalho) e **Workspace Key** (Chave da Área de Trabalho) na sua área de trabalho do Log Analytics.  Clique em **Criar**.

   ![Adicionar a ligação da análise de registos nas aplicações lógicas](media/collect-activity-logs-subscriptions/logic-apps-log-analytics-add-connection.png)

4. Depois de criar a ligação, edite as definições da tabela abaixo. 

    ![Configurar a ação “send data”](media/collect-activity-logs-subscriptions/logic-apps-send-data-to-log-analytics-configuration.png)

   |Definição        | Valor           | Descrição  |
   |---------------|---------------------------|--------------|
   |JSON Request body (Corpo do pedido JSON)  | **Saída** da ação **Compose** | Obtém os registos do corpo da ação Compose. |
   | Custom Log Name (Nome do Registo Personalizado) | AzureActivity | Nome da tabela de registos personalizada para criar no espaço de trabalho log Analytics para conter os dados importados. |
   | Time-generated-field | hora | Não selecione o campo JSON relativo a **time** (hora); escreva a palavra “hora”. Se selecionar o campo JSON, o estruturador coloca a ação **Send Data** num ciclo *For Each*, que não é o que se pretende. |




10. Clique em **Guardar** para guardar as alterações que fez à sua aplicação lógica.

## <a name="step-4---test-and-troubleshoot-the-logic-app"></a>Passo 4 - Testar e resolver problemas com a aplicação lógica
Com o fluxo de trabalho concluído, pode testar no designer para verificar se está a funcionar sem erros.

No Estruturador de Aplicações Lógicas, selecione, clique em **Run** para testar a aplicação lógica. Cada passo na aplicação lógica mostra um ícone de estado, com uma marca de verificação branca num círculo verde que indica êxito.

   ![testar a aplicação lógica](media/collect-activity-logs-subscriptions/test-logic-app.png)

Para ver informações detalhadas sobre cada passo, clique nos nomes dos mesmos para expandi-los. Clique em **Show raw inputs** (Mostrar entradas não processadas) e em **Show raw outputs** (Mostrar saídas não processadas) para ver mais informações sobre os dados recebidos e enviados em cada um dos passos.

## <a name="step-5---view-azure-activity-log-in-log-analytics"></a>Passo 5 - Ver o Registo de Atividades do Azure no Log Analytics
O último passo é verificar a área de trabalho do Log Analytics, para confirmar que os dados estão a ser recolhidos conforme esperado.

1. No portal do Azure, clique em **Todos os serviços**, que se encontra no canto superior esquerdo. Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **Log Analytics**.
2. Na lista de áreas de trabalho do Log Analytics, selecione a sua área de trabalho.
3.  Clique no mosaico **Pesquisa de Registos**, no painel Pesquisa de Registos, no tipo de campo de consulta `AzureActivity_CL` e, em seguida, prima Enter ou clique no botão de pesquisa à direita do campo de consulta. Se não tiver dado o nome *AzureActivity* ao seu registo personalizado, escreva o nome que escolheu e acrescente `_CL`.

>[!NOTE]
> A primeira vez que um novo registo personalizado é enviado para o espaço de trabalho do Log Analytics pode demorar até uma hora para que o registo personalizado seja pesquisável.

>[!NOTE]
> Os registos de atividades são escritos numa tabela personalizada e não aparecem na [solução Registo de Atividades](./activity-log-collect.md).


![testar a aplicação lógica](media/collect-activity-logs-subscriptions/log-analytics-results.png)

## <a name="next-steps"></a>Passos seguintes

Neste artigo, criou uma aplicação lógica para ler Registos de Atividade seletiva saem de um Hub de Eventos e enviá-los para o espaço de trabalho do Log Analytics para análise. Para saber mais sobre visualizar dados num espaço de trabalho, incluindo a criação de dashboards, reveja o tutorial de Dados Visualizalizantes.

> [!div class="nextstepaction"]
> [Visualize Log Search data tutorial](./../../azure-monitor/learn/tutorial-logs-dashboards.md) (Tutorial sobre como Visualizar dados da Pesquisa de Registos)

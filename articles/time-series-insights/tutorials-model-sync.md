---
title: Sincronização de modelos entre gémeos digitais Azure e Time Series Insights | Microsoft Docs
description: Boas práticas e ferramentas usadas para traduzir modelo de ativo em ADT para modelo de ativo em Azure TSI
ms.service: time-series-insights
services: time-series-insights
author: msrsrinivasan
ms.author: radhsrin
manager: diviso
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/19/2021
ms.custom: dpalled
ms.openlocfilehash: 5e8e5367daa532fa0cc36a18cd28b382e7cd8f78
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98682907"
---
# <a name="model-synchronization-between-azure-digital-twins-and-time-series-insights-gen2"></a>Sincronização de modelos entre o Azure Digital Twins e o Time Series Insights Gen2

Este artigo explica as melhores práticas e ferramentas usadas para traduzir o modelo de ativos em Azure Digital Twins (ADT) para modelo de ativos em Azure Time Series Insights (TSI).  Este artigo é a segunda parte de uma série tutorial de duas partes que explica a integração de Azure Digital Twins com Azure Time Series Insights. A integração de Azure Digital Twins com Time Series Insights permite arquivar e acompanhar a história das telemetrias e propriedades calculadas de Gémeos Digitais. Esta série de tutoriais destina-se a desenvolvedores que trabalham para integrar a Time Series Insights com a Azure Digital Twins. A Parte 1 explica  [o estabelecimento do pipeline de dados que traz os dados reais das séries de tempo da Azure Digital Twins à Time Series Insights](../digital-twins/how-to-integrate-time-series-insights.md) e esta, segunda parte da série tutorial explica a sincronização do modelo de ativos entre a Azure Digital Twins e a Time Series Insights. Este tutorial explica as melhores práticas na escolha e criação de convenções de nomeação para iD séries temporítneas (TS ID) e estabelecendo manualmente hierarquias no Modelo série de tempo (TSM).

## <a name="choosing-a-time-series-id"></a>Escolher um ID da Série De Tempo

Time Series ID é um identificador único usado para identificar ativos em Time Series Insights. Os dados da série de tempo (telemetrias do campo, que são pares de valor tempo) são representados utilizando variáveis listadas no TSID. Em Azure Digital Twins, as propriedades gémeas e as telemetrias são usadas para representar o estado de um gémeo e medições produzidas pelo gémeo, respectivamente. A partir do design atual da TSM, os TSIDs têm de ser únicos. A utilização de IDs gémeos dos gémeos em Azure Digital Twins ou combinadas com o nome de propriedade ou telemetria fará sempre TS ID único em TSM. Num caso típico, o **_`<Twin ID>`_** será o TSID, e os nomes de propriedade e telemetria serão as variáveis em TSM. No entanto, existem casos de utilização em que é preferível que as hierarquias de ativos em Time Series Insights sejam achatadas utilizando o formato de teclas compósitas, tais como **_`<Twin ID>+ <Delimiter of Choice> + <Name of the Property or Telemetry>`_** . Vamos dar um exemplo para explicar o caso posterior. Considere um quarto em um edifício modelado como um gémeo e tem twin ID Room22. A sua propriedade de regulação de temperatura deve ser traduzida como **_Room22_TempSetting TSID_** e medição de temperatura a traduzir para **_Room22_TempMea_** em TSM.

[![Escolher um ID da Série De Tempo](media/tutorials-model-sync/choosing-tsid.png)](media/tutorials-model-sync/choosing-tsid.png#lightbox)

## <a name="contextualizing-time-series"></a>Séries temporais contextualizantes

A contextualização de dados (na sua maioria espacial) em Time Series Insights é conseguida através de hierarquias de ativos e o mesmo é usado para facilitar a navegação de dados através de uma visão de árvore no explorador time Series Insights. Os tipos de séries de tempo e hierarquias são definidos usando o Modelo da Série De Tempo (TSM) em Time Series Insights. Os tipos em TSM ajudam a definir variáveis, enquanto os níveis de hierarquia e os valores de campo de instância são usados para construir a vista da árvore no explorador de Insights de Séries Tempotamos. Para obter mais informações sobre a TSM, consulte a [documentação online da Time Series Insights](./concepts-model-overview.md).

Em Azure Digital Twins, a ligação entre os ativos é expressa usando relações gémeas. As relações gémeas são simplesmente um gráfico de ativos ligados. No entanto, na Time Series Insight, as relações entre ativos são hierárquicas de natureza. Ou seja, os ativos partilham uma relação de od tipo pai-filho e é representado usando uma estrutura de árvore. Para traduzir informações de relacionamento de Azure Digital Twins em hierarquias time Series Insights, precisamos escolher relações hierárquicas relevantes de Azure Digital Twins. Azure Digital Twins usa uma linguagem de modelo aberta chamada Linguagem de Definição Digital Twin (DTDL). Nos modelos DTDL são descritos usando uma variante de JSON chamada JSON-LD. Consulte a [documentação do DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) para obter todos os detalhes sobre a especificação.

[![Ligação entre ativos](media/tutorials-model-sync/asset-connection.png)](media/tutorials-model-sync/asset-connection.png#lightbox)

## <a name="translating-graph-representation-in-azure-digital-twins-to-tree-structure-in-time-series-insights"></a>Traduzindo a representação de gráficos em Azure Digital Twins para a estrutura de árvores em Time Series Insights

As seguintes secções do tutorial captam alguns cenários fundamentais de tradução manual da estrutura do gráfico em Azure Digital Twins para a estrutura de árvores em Time Series Insights.

Sistema de Amostra: Este tutorial faz uso do seguinte exemplo para explicar os conceitos discutidos abaixo. É um sistema de gestão de edifícios simples e fictício com um piso e dois quartos. Tem três termóstatos, um em cada um dos quartos e outro comum ao chão. Além disso, tem também um medidor de fluxo de água que mede o fluxo de água da Sala21 para a Sala22 através de uma ligação de tubo entre as salas. Olhando para a relação espacial entre gémeos, tem ambos os tipos de relacionamentos.

1. Relação hierárquica mais comum. Por exemplo, Construção 40 -> Piso01 -> Temperatura >
1. Não é uma relação circular e comum. Por exemplo, a partir do Edifício 40, o FlowMtr pode ser rastreado por dois caminhos diferentes.

   1. Edifício 40 -> Floor01 -> Sala21 -> FlowMtr* -> Flow
   1. Edifício 40 -> Floor01 -> Sala22 -> FlowMtr* -> Flow  
      Onde "Flow" é a telemetria real que mede o fluxo de água entre a Sala21 e a Sala22

> [!Note]
>
> `*` O FloorTS significa FloorThermoStat e FlowMtr significa Flow Meter e tipicamente escolhido como TSID. Temperatura e Fluxo são a telemetria bruta referida como variáveis em Time Series Insights.

Dada a atual limitação em Time Series Insights de que um ativo não pode ser representado em vários ramos, as seguintes secções explicam a modelação de relações hierárquicas e circulares em Time Series Insights.

## <a name="case-1-hierarchical-parent-child-relationship"></a>Caso 1: Relação hierárquica (pai-filho)

É o tipo de relação mais comum entre os gémeos. modelar a relação pai-filho puro é explicado na seguinte ilustração. Os campos de exemplo e o TSID são derivados de ids gémeos, como mostrado abaixo. Embora os campos de instância possam ser atualizados manualmente usando o explorador time series Insights, a secção abaixo denominada "Atualizar campos de ocorrência usando APIs" explica ouvir alterações de modelos em Azure Digital Twins e atualizar campos de instância em Time Series Insights utilizando funções Azure.

[![Mapeamento de IDs gémeos](media/tutorials-model-sync/mapping-twin-ids.png)](media/tutorials-model-sync/mapping-twin-ids.png#lightbox)

[![Mapeamento de IDs gémeos 2](media/tutorials-model-sync/mapping-twin-ids2.png)](media/tutorials-model-sync/mapping-twin-ids2.png#lightbox)

## <a name="case-2-circular-relationship"></a>Caso 2: Relação circular

### <a name="circular-relationship-in-azure-digital-twins-to-single-hierarchy-relationship-in-time-series-insights"></a>Relação circular em Azure Digital Twins para relação de hierarquia única em Time Series Insights

Dado que a TSID deve ser única e só pode ser representada numa hierarquia, este caso representa o _"FlowMtr"_ com uma telemetria denominada _"Flow"_ logo abaixo _da «Sala21»_ gémea. No futuro, quando a Time Series Insights puder suportar a representação múltipla de séries temporizadas na TSM, a telemetria _'Flow'_ será representada na _'Sala 21'_ e _'Sala 22'_

A imagem que se segue mostra o mapeamento manual de twin IDs no campo Azure Digital Twins to Instance em TSM e a hierarquia resultante em Time Series Insights.

[![Mapeamento de IDs gémeos em Azure Digital Twins](media/tutorials-model-sync/mapping-twin-ids-adt.png)](media/tutorials-model-sync/mapping-twin-ids-adt.png#lightbox)

### <a name="circular-relationship-in-azure-digital-twins-to-multiple-hierarchies-in-time-series-insights-using-duplicates"></a>Relação circular em Azure Digital Twins a múltiplas hierarquias em Time Series Insights, usando duplicados

A parte 1 do tutorial explica como um programa de clientes (uma função Azure) ajuda a transferir dados de telemetria do IoT Hub ou de outras fontes de evento para a Azure Digital Twins. Esta abordagem sugere o uso do mesmo programa de clientes para fazer atualizações para propriedades relevantes dos gémeos pais. No exemplo dado, ao ler a telemetria FlowMtr do IoT Hub e atualizar a propriedade "Flow" em FlowMtr twin, o programa também pode atualizar propriedades correspondentes em todos os possíveis gémeos-mãe da fonte. No nosso exemplo, seria propriedade "outflowmea" (identificada usando a relação 'outflow') propriedade da Sala21 e propriedade "inflowmea" do Room22.  A imagem abaixo mostra a experiência final do utilizador no explorador time series Insights. É de notar que dispomos de duplicados de dados, tomando esta abordagem.

[![Explorador de Insights da Série De Tempo](media/tutorials-model-sync/tsi-explorer.png)](media/tutorials-model-sync/tsi-explorer.png#lightbox)

O código abaixo mostra como a aplicação do cliente foi capaz de navegar na relação gémea usando APIs de Gémeos Digitais Azure.

> [!Note]
>
> Este exemplo de código assume que os leitores estão familiarizados com a [Parte 01](../digital-twins/tutorial-end-to-end.md#set-up-the-sample-function-app) do tutorial e esta alteração de código foi feita dentro da Função "ProcessHubToDTEvents".

```csharp
if (propertyPath.Equals("/Flow"))
{
//Update the flow value property of the flow meter
await AdtUtilities.UpdateTwinProperty(client, twinId, "replace",
propertyPath, "double", propertyValue, log);

//also update the sending end flow
string parentIdOutflow = await AdtUtilities.FindParent(client, twinId,
"outflow", log);
if (parentIdOutflow != null)
await AdtUtilities.UpdateTwinProperty(client, parentIdOutflow, "replace", "outflow", "double", propertyValue, log);
else
log.LogInformation("Unable to find Parent with outflow
relationship for " + twinId );
//and receiving end flow value
string parentIdinflow = await AdtUtilities.FindParent(client, twinId,
"inflow", log);
if (parentIdinflow != null)

await AdtUtilities.UpdateTwinProperty(client, parentIdinflow,
"replace", "inflow", "double", propertyValue, log);
else
log.LogInformation("Unable to find Parent with inflow
relationship for " + twinId);
}
```

## <a name="updating-instance-fields-using-apis"></a>Atualizar campos de instância usando APIs

Esta secção do tutorial explica a audição de mudanças de modelo em Azure Digital Twins, como criação, eliminação de gémeos ou mudança de relações entre gémeos e atualização de campos de instância e hierarquias programáticamente usando APIs modelo Time Series Insights. Este método de atualização do modelo Time Series Insights é geralmente alcançado através das funções Azure. No Azure Digital Twins, notificações de eventos como adição de gémeos ou eliminações podem ser encaminhadas para baixo serviços como Os Centros de Eventos que por sua vez podem ser alimentados para funções Azure. Mais detalhes sobre o encaminhamento e filtragem do evento são explicados [aqui.](../digital-twins/how-to-manage-routes-portal.md)  O resto desta secção explica a utilização do modelo DesAC em Azure para atualizar o modelo Time Series Insights em resposta à adição de dois modelos (um tipo de mudança de modelo) em Azure Digital Twins.

### <a name="receiving-and-identifying-twin-addition-event-notification"></a>Receber e identificar a notificação de evento de adição dupla

```csharp
[FunctionName("RouteEventsToTsi")]
public async Task Run([EventGridTrigger]EventGridEvent eventGridEvent)
{
    try
    {
        if (eventGridEvent != null && eventGridEvent.Data != null)
        {
            logger.LogInformation($"EventType: {eventGridEvent.EventType}");
            logger.LogInformation($"EventGridEvent: {JsonConvert.SerializeObject(eventGridEvent)}");

            //Shape event and Send event data to event hub and tsi
            await SendEventToEventHubAsync(eventGridEvent).ConfigureAwait(false);

            //If a new twin was created, update the newly created instance in TSI with info retrieved from ADT
            if (eventGridEvent.EventType == Constants.TwinCreateEventType)
            {
                //retrieve building, floor and room of value twin
                var twinInfo = await RetrieveTwinInfoAsync(eventGridEvent).ConfigureAwait(false);
                //Update Tsi instance with type(sensor type), hierarchy(space hierarchy) and instance fields(twin info retrieved above)
                var instance = await CreateInstanceToSendAsync(twinInfo).ConfigureAwait(false);
                var instanceToUpdate = new List<TimeSeriesInstance>() { instance };
                var response = await tsiClient.TimeSeriesInstances.ExecuteBatchAsync(new InstancesBatchRequest(update: instanceToUpdate)).ConfigureAwait(false);
            }
        }
    }
    catch (Exception ex)
    {
        logger.LogError($"Exception: {ex.Message}");
    }
}
```

### <a name="creating-time-series-insights-client-and-adding-instance-details"></a>Criar insights de séries de tempo cliente e adicionar detalhes de instância

```csharp
private async Task<TimeSeriesInstance> CreateInstanceToSendAsync(Dictionary<string, string> twinInfo)
{
    try
    {
        tsiClient = await GetTSIClientAsync().ConfigureAwait(false);

        var timeSeriesId = new object[] { twinInfo[Constants.DtId] };
        var instances = await tsiClient.TimeSeriesInstances.ExecuteBatchAsync(
            new InstancesBatchRequest(
                get: new InstancesRequestBatchGetOrDelete(
                    new IList<object>[] { timeSeriesId }))).ConfigureAwait(false);
        var instance = instances.Get.First().Instance;

        if (instance != null)
        {
            instance = await AddHierarchyToInstanceAsync(instance).ConfigureAwait(false);
            instance = await AddTypeToInstanceAsync(instance, twinInfo[Constants.TwinType]).ConfigureAwait(false);

            instance.InstanceFields = new Dictionary<string, object>
                            {
                                { "Building", twinInfo[Constants.BuildingName] },
                                { "Floor", twinInfo[Constants.FloorName] },
                                { "Room", twinInfo[Constants.ParentName] }
                            };

            //If value twin is a sensor value twin, add sensor type instance field to diff from spatial value twin
            if (twinInfo[Constants.ParentType] == Constants.Sensor)
            {
                instance.InstanceFields.Add(Constants.SensorType, twinInfo[Constants.TwinType]);
            }
            return instance;
        }
        else
        {
            logger.LogError($"instance with id {twinInfo[Constants.DtId]} not found");
            return new TimeSeriesInstance();
        }
    }
    catch (Exception e)
    {
        logger.LogError(e.Message);
        throw;
    }
}
```

### <a name="applying-hierarchy-info-to-instance"></a>Aplicação de informação hierárquica à instância

```csharp
private async Task<TimeSeriesInstance> AddHierarchyToInstanceAsync(TimeSeriesInstance instance)
{
    if (instance.HierarchyIds == null)
    {
        TimeSeriesHierarchy spacesHierarchy = null;
        try
        {
            var hierarchy = await RunGetHierarchiesAsync(Constants.SpaceHierarchy).ConfigureAwait(false);
            spacesHierarchy = hierarchy.First(h => h.Name.Equals(Constants.SpaceHierarchy));
            instance.HierarchyIds = new List<Guid?>();
            instance.HierarchyIds.Add(spacesHierarchy.Id);
        }
        catch (Exception ex)
        {
            logger.LogWarning($"Hierarchy 'space hierarchy' not found, {ex}");
            throw;
        }
    }
    return instance;
}
```

## <a name="next-steps"></a>Passos seguintes

Em terceiro lugar na série de tutoriais é mostrar como consultar dados históricos de Azure Digital Twins usando AZIs da Série De Tempo. É um trabalho em curso e a secção será atualizada quando estiver pronta. Entretanto, os leitores são encorajados a consultar a [documentação da API da Time Series Insights](./concepts-query-overview.md).

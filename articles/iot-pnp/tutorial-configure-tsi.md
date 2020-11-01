---
title: Use Time Series Insights para armazenar e analisar a telemetria do seu dispositivo IoT Plug e Play ! Microsoft Docs
description: Configurar um ambiente de Insights de Séries Tempotamos e ligue o seu Hub IoT para visualizar e analisar a telemetria a partir dos seus dispositivos IoT Plug e Play.
author: lyrana
ms.author: lyhughes
ms.date: 10/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 0d4c24ed771b4d112a448ceec0e9fc2f9e8ac359
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2020
ms.locfileid: "93146437"
---
# <a name="tutorial-create-and-connect-to-time-series-insights-gen2-to-store-visualize-and-analyze-iot-plug-and-play-device-telemetry"></a>Tutorial: Criar e ligar à Série De Tempo Insights Gen2 para armazenar, visualizar e analisar telemetria de dispositivo ioT plug e play

Neste tutorial, você aprenderá a criar e configurar corretamente um ambiente [Azure Time Series Insights Gen2](https://docs.microsoft.com/azure/time-series-insights/overview-what-is-tsi) (TSI) para integrar-se com a sua solução IoT Plug and Play. Com a TSI, pode recolher, processar, armazenar, consultar e visualizar dados de séries de tempo na escala Internet of Things (IoT).

Primeiro irá providenciar um ambiente de TSI e ligar o seu Hub IoT como fonte de eventos de streaming. Em seguida, trabalhará através da sincronização de modelos para autor do modelo da Série DeTemporal do seu ambiente TSI baseado nos ficheiros de modelos de definição de [gémeos digitais (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl) que utilizou para o controlador de temperatura e dispositivos termostatos.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Para evitar a necessidade de instalar o Azure CLI localmente, pode utilizar o Azure Cloud Shell para configurar os serviços na nuvem.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="time-series-id-selection"></a>Seleção de ID séries de tempo

Ao providenciar o seu ambiente de TSI, será obrigado a selecionar um ID da Série De Tempo. A seleção do ID da Série De Tempo apropriado é fundamental, uma vez que a propriedade é imutável e não pode ser alterada após o seu set. Escolher um ID da Série De Tempo é como escolher uma chave de partição para uma base de dados. Normalmente, o seu ID TS deve ser o nó de folha do seu modelo de ativo. Por outras palavras, normalmente pretende selecionar a propriedade ID do ativo ou sensor mais granular que está a emitir telemetria.

Como utilizador IoT Plug and Play, a questão pertinente para a seleção do seu ID TS é a presença de componentes nos [modelos](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#component) do seu dispositivo. 

![Seleção de ID TS](./media/tutorial-configure-tsi/ts-id-selection-pnp.png)

* Se tiver feito o Quickstart e o seu dispositivo IoT Hub representar o [termóstato,](https://raw.githubusercontent.com/Azure/opendigitaltwins-dtdl/master/DTDL/v2/samples/Thermostat.json)use `iot-hub-connection-device-id` como ID TS.

* Se tiver feito um dos tutoriais para o multi-componente [TemperatureController,](https://raw.githubusercontent.com/Azure/opendigitaltwins-dtdl/master/DTDL/v2/samples/TemperatureController.json)use uma chave composta na secção abaixo, escrita como  `iot-hub-connection-device-id, dt-subject`

## <a name="provision-your-azure-time-series-insights-gen2-environment"></a>Provisionar o seu ambiente Azure Time Series Insights Gen2

O comando abaixo faz o seguinte:

* Cria uma conta de armazenamento Azure para o [cold store](https://docs.microsoft.com/azure/time-series-insights/concepts-storage#cold-store)do seu ambiente, projetado para retenção e análise a longo prazo sobre dados históricos.
  * `mytsicoldstore`Substitua-o por um nome único para a sua conta.
* Cria um ambiente Azure Time Series Insights Gen2, incluindo armazenamento quente com um período de retenção de 7 dias, e um cold store para retenção infinita. 
  * Substitua `my-tsi-env` por um nome único para o seu ambiente de TSI 
  * `my-pnp-resourcegroup`Substitua-o pelo nome do grupo de recursos utilizado durante a configuração
  * Substitua `my-ts-id-property` pelo seu valor de propriedade TS ID com base nos critérios de seleção acima

```azurecli-interactive
storage=mytsicoldstore
rg=my-pnp-resourcegroup
az storage account create -g $rg -n $storage --https-only
key=$(az storage account keys list -g $rg -n $storage --query [0].value --output tsv)
az timeseriesinsights environment longterm create --name my-tsi-env --resource-group $rg --time-series-id-properties my-ts-id-property --sku-name L1 --sku-capacity 1 --data-retention 7 --storage-account-name $storage --storage-management-key $key --location eastus2
```

Agora vai configurar o Hub IoT que criou anteriormente como fonte de [eventos](https://docs.microsoft.com/azure/time-series-insights/concepts-streaming-ingestion-event-sources)do seu ambiente. Quando a fonte do evento estiver ligada, a TSI começará a indexar eventos a partir do seu hub, começando com o evento mais antigo da fila.

Primeiro crie um grupo de consumidores único no seu IoT Hub para o seu ambiente TSI. `my-pnp-hub`Substitua-o pelo nome do Hub IoT que utilizou anteriormente.

```azurecli-interactive
az iot hub consumer-group create --hub-name my-pnp-hub --name tsi-consumer-group 
```

Ligue o Hub IoT. `my-pnp-resourcegroup`Substitua, e com os `my-pnp-hub` `my-tsi-env` respetivos valores.

```azurecli-interactive
rg=my-pnp-resourcegroup
iothub=my-pnp-hub
env=my-tsi-env
es_resource_id=$(az iot hub create -g $rg -n $iothub --query id --output tsv)
shared_access_key=$(az iot hub policy list -g $rg --hub-name $iothub --query "[?keyName=='service'].primaryKey" --output tsv)
az timeseriesinsights event-source iothub create -g $rg --environment-name $env -n iot-hub-event-source --consumer-group-name tsi-consumer-group  --key-name iothubowner --shared-access-key $shared_access_key --event-source-resource-id $es_resource_id
```
Navegue para o seu grupo de recursos no [portal Azure](https://portal.azure.com) e selecione o seu ambiente de Insights de Séries de Tempo recém-criado. Visite o *URL do Time Series Insights Explorer* mostrado na visão geral do exemplo.

![Página de descrição geral do portal](./media/tutorial-configure-tsi/view-environment.png)

No explorador, deve ver os seus dois termóstatos em "Todas as hierarquias". Em seguida, irás fazer a curadoria do teu Modelo série de tempo com base no modelo do teu dispositivo.

![Vista do explorador 1](./media/tutorial-configure-tsi/tsi-env-first-view.png)

## <a name="model-synchronization-between-digital-twins-definition-language-and-time-series-insights-gen2"></a>Sincronização de modelos entre linguagem de definição de gémeos digitais e insights de séries de tempo gen2

Em seguida, irá traduzir o seu modelo de dispositivo DTDL para o modelo de ativo em Azure Time Series Insights (TSI). O Modelo de Séries Tempotares da TSI é uma ferramenta de modelação semântica para a contextualização de dados dentro da TSI. O Modelo série de tempo tem três componentes principais:

* [Exemplos de modelos de séries de tempo](https://docs.microsoft.com/azure/time-series-insights/concepts-model-overview#time-series-model-instances). Os casos são representações virtuais das próprias séries temporências. As instâncias serão identificadas exclusivamente pelo seu ID TS.
* [Hierarquias modelo séries de tempo](https://docs.microsoft.com/azure/time-series-insights/concepts-model-overview#time-series-model-hierarchies). As hierarquias organizam instâncias especificando nomes de propriedade e suas relações.
* [Tipos de modelos séries de tempo](https://docs.microsoft.com/azure/time-series-insights/concepts-model-overview#time-series-model-types). Os tipos ajudam-no a definir [variáveis](https://docs.microsoft.com/azure/time-series-insights/concepts-variables) ou fórmulas para fazer cálculos. Os tipos estão associados a uma instância específica.

> [!NOTE]
> Os exemplos abaixo são para o multi componente TemperatureController.

### <a name="define-your-types"></a>Defina os seus tipos

Pode começar a ingerir dados em Azure Time Series Insights Gen2 sem ter um modelo pré-definido. Quando a telemetria chegar, a TSI tentará resolver automaticamente as séries de tempo com base no valor da propriedade de ID TS. Todas as instâncias serão atribuídas ao *Tipo Predefinido* . Terá de criar manualmente um novo Tipo para representar os seus modelos. A imagem abaixo retrata um método simples para sincronizar um modelo DTDL e um Tipo TSM:

![DTDL para Tipo TSM](./media/tutorial-configure-tsi/DTDL-to-TSM-Type.png)

* O seu identificador de modelos gémeos digitais (DTMI) tornar-se-á o seu Tipo ID
* O nome do tipo pode ser o nome do modelo ou o nome do ecrã
* A descrição do modelo torna-se a descrição do Tipo
* Pelo menos uma variável tipo é criada para cada componente de telemetria que tem um esquema numérico. 
  * Apenas tipos de dados numéricos podem ser usados para variáveis, mas se um valor é enviado como uma cadeia que é parsable, `"0"` por exemplo, você pode usar uma função [de conversão](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax#conversion-functions) como `toDouble`
* O nome variável pode ser o nome da telemetria ou o nome de exibição
* Ao definir a expressão variável time series (TSX), consulte o nome da telemetria no fio, e é o seu tipo de dados.

> [!NOTE]
> Este exemplo mostra apenas duas variáveis -- um agregado e umnumérico, mas cada tipo pode ter até 100. Diferentes variáveis podem referenciar o mesmo valor de telemetria para executar calucaultions diferentes conforme necessário. Para a lista completa de filtros, agregados e funções escalar, consulte a documentação de sintaxe de [expressão de série sonora da Série Tempo Gen2.](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax)

Abra o seu editor de texto de eleição e guarde o JSON abaixo para a sua unidade local:

```JSON
{
  "put": [
    {
      "id": "dtmi:com:example:Thermostat;1",
      "name": "Thermostat",
      "description": "Reports current temperature and provides desired temperature control.",
      "variables": {
        "EventCount": {
          "kind": "aggregate",
          "aggregation": {
            "tsx": "count()"
          }
        },
        "Temperature": {
          "kind": "numeric",
          "value": {
            "tsx": "$event.temperature.Double"
          },
          "aggregation": {
            "tsx": "avg($value)"
          }
        }
      }
    }
  ]
}
```

No seu Time Series Insights Explorer navegue para o separador Modelo clicando no ícone do modelo à esquerda. Clique em **'Tipos'** e clique no **Upload JSON** :

![Carregar](./media/tutorial-configure-tsi/upload-type.png)

**Selecione Escolha o ficheiro,** selecione o JSON que guardou anteriormente e clique em **Upload**

Deve ver o tipo de termóstato recentemente definido.

### <a name="optional---create-a-hierarchy"></a>Opcional - Criar uma hierarquia

Pode, opcionalmente, criar uma hierarquia para organizar os dois componentes do termóstato sob o seu progenitor TemeraptureController.

Clique em *Hierarquias* e *selecione Adicione uma hierarquia* . Insira `Device Fleet` como o nome e crie um nível chamado `Device Name` e, em seguida, clique em *Guardar* .

![Adicione uma hierarquia](./media/tutorial-configure-tsi/add-hierarchy.png)

### <a name="assign-your-instances-to-the-correct-type"></a>Atribua as suas instâncias ao tipo correto

Em seguida, você vai alterar o tipo de suas instâncias e opcionalmente situa-los dentro da hierarquia

Selecione o *separador Instâncias* e clique no ícone *Editar* na extrema direita.

![Editar instâncias](./media/tutorial-configure-tsi/edit-instance.png)

Clique no dropdown do tipo e selecione `Thermostat` . 

![Alterar tipo de instância](./media/tutorial-configure-tsi/change-type.png)

Se criou uma hierarquia, selecione *campos de instância* e verifique a `Device Fleet` caixa. Introduza `Temperature Controller` como valor do seu dispositivo principal e, em seguida, clique em *Guardar* .

![Atribuir à hierarquia](./media/tutorial-configure-tsi/assign-to-hierarchy.png)

Repita os passos acima para o segundo termóstato.

### <a name="view-your-data"></a>Ver os dados

Navegue de volta para o painel de gráficos e expanda a frota do dispositivo e o Controlador de Temperatura. Clique no termóstato1, selecione a `Temperature` variável e, em seguida, clique em *Adicionar* para traçar o valor. Faça o mesmo com o termostato2.

![Alterar tipo de instância](./media/tutorial-configure-tsi/charting-values.png)

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre as várias opções de gráficos, incluindo tamanho de intervalo e controlos de eixo Y, consulte a documentação do [Azure Time Series Insights Explorer.](https://docs.microsoft.com/azure/time-series-insights/concepts-ux-panels)

* Para uma visão geral aprofundada do modelo da Série De Tempo do seu ambiente consulte [este](https://docs.microsoft.com/azure/time-series-insights/concepts-model-overview) artigo.

* Para mergulhar nas APIs de consulta e na sintaxe de expressão da série de tempo, [selecione](https://docs.microsoft.com/rest/api/time-series-insights/reference-query-apis).





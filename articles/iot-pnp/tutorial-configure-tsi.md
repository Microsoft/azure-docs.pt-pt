---
title: Use insights da Série De Tempo Azure para armazenar e analisar a telemetria do seu dispositivo Azure IoT Plug and Play
description: Configurar um ambiente de Insights de Séries Tempotamos e ligar o seu hub IoT para visualizar e analisar a telemetria a partir dos seus dispositivos IoT Plug e Play.
author: lyrana
ms.author: lyhughes
ms.date: 10/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 5491df61a1198e8eee4ba4701ccfc56154ec75eb
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905103"
---
# <a name="preview-tutorial-create-and-configure-a-time-series-insights-gen2-environment"></a>Tutorial de pré-visualização: Criar e configurar um ambiente de Insights Gen2 da Série Temporal

Neste tutorial, aprende-se a criar e configurar um ambiente [Azure Time Series Insights Gen2](../time-series-insights/overview-what-is-tsi.md) para integrar-se com a sua solução IoT Plug and Play. Use Time Series Insights para recolher, processar, armazenar, consultar e visualizar dados de séries de tempo na escala da Internet das Coisas (IoT).

Em primeiro lugar, fornece um ambiente time series Insights e conecta o seu hub IoT como uma fonte de evento de streaming. Em seguida, trabalha através da sincronização de modelos para autor do seu [Modelo série de tempo.](../time-series-insights/concepts-model-overview.md) Utiliza os ficheiros de modelos de amostra de [definição de gémeos digitais (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl) que utilizou para o controlador de temperatura e dispositivos do termóstato.

> [!NOTE]
> Esta integração entre Time Series Insights e IoT Plug and Play está em pré-visualização. A forma como os modelos de dispositivos DTDL mapeiam para o modelo da Série De Tempo insights da Série Temporal da Série Temporal da Série Temporal pode mudar. 

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Neste momento, tem:

* Um centro de IoT Azure.
* Uma instância do Serviço de Provisionamento de Dispositivos (DPS) ligada ao seu hub IoT. A instância DPS deve ter uma inscrição individual de dispositivo para o seu dispositivo IoT Plug and Play.
* Uma ligação ao seu hub IoT a partir de um dispositivo de um único componente ou de um dispositivo de múltiplos componentes que transmite dados simulados.

Para evitar a necessidade de instalar o Azure CLI localmente, pode utilizar a Azure Cloud Shell para configurar os serviços na nuvem.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-your-event-source"></a>Prepare a sua fonte de eventos

O hub IoT que criou anteriormente será a fonte de [eventos](../time-series-insights/concepts-streaming-ingestion-event-sources.md)do seu Time Series Insights environment.

> [!IMPORTANT]
> Desative quaisquer rotas IoT Hub existentes. Há um problema conhecido em usar um hub IoT com [encaminhamento](../iot-hub/iot-hub-devguide-messages-d2c.md#routing-endpoints) configurado. Desative temporariamente quaisquer pontos finais de encaminhamento. Quando o seu hub IoT estiver ligado ao Time Series Insights, pode voltar a ativar os pontos finais de encaminhamento.

No seu hub IoT, crie um grupo de consumidores único para a Time Series Insights consumir. No exemplo seguinte, `my-pnp-hub` substitua-o pelo nome do hub IoT que utilizou anteriormente.

```azurecli-interactive
az iot hub consumer-group create --hub-name my-pnp-hub --name tsi-consumer-group
```

## <a name="choose-a-time-series-id"></a>Escolher um ID de Série de Tempo

Ao fornecer o seu ambiente de Insights de Séries De Tempo, tem de selecionar um *ID da Série De Tempo*. É importante selecionar o ID da Série De Tempo apropriado. Esta propriedade é imutável e não pode ser mudada depois de definida. Um ID da Série De Tempo é como uma chave de partição de base de dados. O ID da Série De Tempo funciona como a chave primária para o seu modelo de séries de tempo. Para obter mais informações, consulte [as melhores práticas para escolher um ID da Série De Tempo.](../time-series-insights/how-to-select-tsid.md)

Como utilizador IoT Plug and Play, para o seu ID da Série De Tempo, especifique uma _chave composta_ que consiste em `iothub-connection-device-id` e `dt-subject` . O hub IoT adiciona estas propriedades do sistema que contêm o ID do seu IoT Plug e play device ID e os nomes dos componentes do dispositivo, respectivamente.

Mesmo que os seus modelos ioT plug e dispositivo de reprodução não utilizem atualmente componentes, deve incluir `dt-subject` como parte de uma chave composta para que possa utilizar componentes no futuro. Como o seu ID da Série De Tempo é imutável, a Microsoft recomenda ativar esta opção caso precise dela no futuro.

> [!NOTE]
> Os exemplos deste artigo são para o dispositivo de múltiplos `TemperatureController` componentes. Mas os conceitos são os mesmos para o dispositivo sem `Thermostat` componente.

## <a name="provision-your-time-series-insights-environment"></a>Provisione o seu ambiente de Insights de Séries Tempo

Esta secção descreve como fornecer o seu ambiente Azure Time Series Insights Gen2.

Executar o seguinte comando para:

* Crie uma conta de armazenamento Azure para a loja de [frio](../time-series-insights/concepts-storage.md#cold-store)do seu ambiente. Esta conta destina-se a retenção e análise a longo prazo para dados históricos.
  * No seu código, `mytsicoldstore` substitua-o por um nome único para a sua conta de armazenamento frio.
* Crie um ambiente Azure Time Series Insights Gen2. O ambiente será criado com armazenamento quente que tem um período de retenção de sete dias. A conta de armazenamento frio será anexada para retenção infinita.
  * No seu código, `my-tsi-env` substitua-o por um nome único para o seu ambiente De Insights de Séries Tempoturais.
  * No seu código, `my-pnp-resourcegroup` substitua-o pelo nome do grupo de recursos utilizado durante a configuração.
  * A sua propriedade de ID séries de tempo é `iothub-connection-device-id, dt-subject` .

```azurecli-interactive
storage=mytsicoldstore
rg=my-pnp-resourcegroup
az storage account create -g $rg -n $storage --https-only
key=$(az storage account keys list -g $rg -n $storage --query [0].value --output tsv)
az timeseriesinsights environment longterm create --name my-tsi-env --resource-group $rg --time-series-id-properties iothub-connection-device-id, dt-subject --sku-name L1 --sku-capacity 1 --data-retention 7 --storage-account-name $storage --storage-management-key $key --location eastus2
```

Ligue a sua fonte de eventos IoT Hub. `my-pnp-resourcegroup` `my-pnp-hub` Substitua, e pelos `my-tsi-env` valores que escolheu. O seguinte comando refere o grupo de consumidores para Time Series Insights que criou anteriormente:

```azurecli-interactive
rg=my-pnp-resourcegroup
iothub=my-pnp-hub
env=my-tsi-env
es_resource_id=$(az iot hub create -g $rg -n $iothub --query id --output tsv)
shared_access_key=$(az iot hub policy list -g $rg --hub-name $iothub --query "[?keyName=='service'].primaryKey" --output tsv)
az timeseriesinsights event-source iothub create -g $rg --environment-name $env -n iot-hub-event-source --consumer-group-name tsi-consumer-group  --key-name iothubowner --shared-access-key $shared_access_key --event-source-resource-id $es_resource_id
```

No [portal Azure,](https://portal.azure.com)vá ao seu grupo de recursos e, em seguida, selecione o seu novo ambiente Time Series Insights. Aceda ao **URL do Time Series Insights Explorer** mostrado na visão geral do exemplo:

![Screenshot da página geral do portal.](./media/tutorial-configure-tsi/view-environment.png)

No Explorer, vê-se três instâncias:

* &lt;o seu ID do dispositivo pnp, &gt; termostato1
* &lt;o seu ID do dispositivo pnp, &gt; termostato2
* &lt;o seu ID do dispositivo &gt; pnp, `null`

> [!NOTE]
> A terceira etiqueta representa a telemetria a partir de `TemperatureController` si mesma, tal como o conjunto de trabalho da memória do dispositivo. Como esta é uma propriedade de alto nível, o valor para o nome do componente é nulo. Num passo posterior, torna este nome mais fácil de utilizar.

![Screenshot mostrando três instâncias no Explorer.](./media/tutorial-configure-tsi/tsi-env-first-view.png)

## <a name="configure-model-translation"></a>Configurar tradução de modelo

Em seguida, traduz o seu modelo de dispositivo DTDL para o modelo de ativo em Azure Time Series Insights. Em Time Series Insights, o Modelo série de tempo é uma ferramenta de modelação semântica para a contextualização de dados. O modelo tem três componentes principais:

* [As instâncias do Modelo de Séries de Tempo](../time-series-insights/concepts-model-overview.md#time-series-model-instances) são representações virtuais das próprias séries tempor.000. As instâncias são identificadas exclusivamente pelo seu ID da Série De Tempo.
* [As hierarquias do Modelo série de tempo](../time-series-insights/concepts-model-overview.md#time-series-model-hierarchies) organizam instâncias especificando nomes de propriedade e suas relações.
* [Os tipos de modelos de séries de tempo](../time-series-insights/concepts-model-overview.md#time-series-model-types) ajudam-no a definir [variáveis](../time-series-insights/concepts-variables.md) ou fórmulas para computação. Os tipos estão associados a uma instância específica.

### <a name="define-your-types"></a>Defina os seus tipos

Pode começar a ingerir dados no Azure Time Series Insights Gen2 sem ter predefinido um modelo. Quando a telemetria chega, a Time Series Insights tenta resolver automaticamente as instâncias das séries de tempo com base nos valores de propriedade da Série Time Series. Todas as instâncias são atribuídas ao *tipo predefinido*. É necessário criar manualmente um novo tipo para categorizar corretamente as suas ocorrências. 

Os seguintes detalhes descrevem o método mais simples para sincronizar os modelos DTDL do seu dispositivo com os seus tipos de Modelos séries de tempo:

* O seu identificador de modelos gémeos digitais torna-se o seu ID tipo.
* O nome do tipo pode ser o nome do modelo ou o nome do visor.
* A descrição do modelo torna-se a descrição do tipo.
* Pelo menos uma variável do tipo é criada para cada telemetria que tem um esquema numérico.
  * Apenas tipos de dados numéricos podem ser usados para variáveis, mas se um valor for enviado como outro tipo que pode ser convertido, `"0"` por exemplo, pode utilizar uma função de [conversão](/rest/api/time-series-insights/reference-time-series-expression-syntax#conversion-functions) como `toDouble` .
* O nome variável pode ser o nome da telemetria ou o nome do visor.
* Quando definir a variável de expressão de séries de tempo, consulte o nome da telemetria no fio e o tipo de dados da telemetria.

| DTDL JSON | Modelo série de tempo JSON | Valor de exemplo |
|-----------|------------------|-------------|
| `@id` | `id` | `dtmi:com:example:TemperatureController;1` |
| `displayName`    | `name`   |   `Temperature Controller`  |
| `description`  |  `description`  |  `Device with two thermostats and remote reboot.` |  
|`contents` (matriz)| `variables` (objeto)  | Veja o seguinte exemplo.

![Screenshot mostrando D T D L para modelo série de tempo.](./media/tutorial-configure-tsi/DTDL-to-TSM-Type.png)

> [!NOTE]
> Este exemplo mostra três variáveis, mas cada tipo pode ter até 100 variáveis. Diferentes variáveis podem referenciar o mesmo valor de telemetria para fazer cálculos diferentes conforme necessário. Para a lista completa de filtros, agregados e funções escalar, consulte a [sintaxe de expressão da série de tempo da Série De Tempo De Tempo de Insights De Tempo .](/rest/api/time-series-insights/reference-time-series-expression-syntax)

Abra um editor de texto e guarde o seguinte JSON para a sua unidade local.

```JSON
{
  "put": [
    {
      "id": "dtmi:com:example:TemperatureController;1",
      "name": "Temperature Controller",
      "description": "Device with two thermostats and remote reboot.",
      "variables": {
        "workingSet": {
          "kind": "numeric",
          "value": {
            "tsx": "coalesce($event.workingSet.Long, toLong($event.workingSet.Double))"
          }, 
          "aggregation": {
            "tsx": "avg($value)"
          }
        },
        "temperature": {
          "kind": "numeric",
          "value": {
            "tsx": "coalesce($event.temperature.Long, toLong($event.temperature.Double))"
          },
          "aggregation": {
            "tsx": "avg($value)"
          }
        },
        "eventCount": {
          "kind": "aggregate",
          "aggregation": {
            "tsx": "count()"
          }
        }
      }
    }
  ]
}
```

No Time Series Insights Explorer, selecione o ícone do modelo à esquerda para abrir o **separador Modelo.** Selecione **Tipos** e, em seguida, selecione **Upload JSON**:

![Screenshot mostrando como carregar JSON.](./media/tutorial-configure-tsi/upload-type.png)

**Selecione Escolha o ficheiro**, selecione o JSON que guardou anteriormente e, em seguida, selecione **Upload**.

Vê-se o tipo de **controlador de temperatura** recentemente definido.

### <a name="create-a-hierarchy"></a>Criar uma hierarquia

Criar uma hierarquia para organizar as etiquetas sob os seus `TemperatureController` pais. O exemplo simples seguinte tem um único nível, mas as soluções IoT geralmente têm muitos níveis de nidificação para contextualizar tags dentro da sua posição física e semântica dentro de uma organização.

Selecione **hierarquias** e, em seguida, **selecione Adicionar hierarquia**. Para obter o nome, *insira a Frota de Dispositivos*. Criar um nível chamado *Nome do Dispositivo*. Em seguida, selecione **Guardar**.

![Screenshot mostrando como adicionar uma hierarquia.](./media/tutorial-configure-tsi/add-hierarchy.png)

### <a name="assign-your-instances-to-the-correct-type"></a>Atribua as suas instâncias ao tipo correto

Em seguida, muda-se o tipo de instâncias e coloca-se na hierarquia.

Selecione o **separador Instâncias.** Encontre a instância que representa o conjunto de trabalho do dispositivo e, em seguida, selecione o ícone **Editar** na extrema-direita.

![Screenshot mostrando como editar um caso.](./media/tutorial-configure-tsi/edit-instance.png)

Abra o menu **type** drop-down e, em seguida, selecione **O Controlador de Temperatura**. Introduza *o número <your device name> padrãoComponent,* para atualizar o nome da instância que representa todas as tags de nível superior associadas ao seu dispositivo.

![Screenshot mostrando como alterar um tipo de instância.](./media/tutorial-configure-tsi/change-type.png)

Antes de selecionar **,** selecione primeiro o **separador Campos de Exemplo** e, em seguida, selecione A Frota do **Dispositivo**. Para agrupar a telemetria, *\<your device name> insira - Controlador Temporário*. Em seguida, selecione **Guardar**.

![Screenshot mostrando como atribuir um caso a uma hierarquia](./media/tutorial-configure-tsi/assign-to-hierarchy.png)

Repita os passos anteriores para atribuir as etiquetas do termóstato corretas ao tipo e à hierarquia.

## <a name="view-your-data"></a>Ver os dados

Volte ao painel de gráficos e expanda a **Frota de Dispositivos** > o seu dispositivo. Selecione **o termóstato1,** selecione a variável **Temperatura** e, em seguida, selecione **Adicionar** para traçar o valor. Faça o mesmo para **o termóstato2** e o valor padrão **de trabalhocomponente.** **defaultComponent**

![Screenshot mostrando como alterar o tipo de instância para o termostato2.](./media/tutorial-configure-tsi/charting-values.png)

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre as várias opções de gráficos, incluindo tamanho de intervalo e controlos de eixo y, consulte [o Azure Time Series Insights Explorer](../time-series-insights/concepts-ux-panels.md).

* Para uma visão geral aprofundada do modelo da Série De Tempo do seu ambiente, consulte [o Modelo da Série de Tempo em Azure Time Series Insights Gen2](../time-series-insights/concepts-model-overview.md).

* Para mergulhar nas APIs de consulta e na sintaxe de expressão da série de tempo, consulte [Azure Time Series Insights Gen2 Consultas APIs](/rest/api/time-series-insights/reference-query-apis).

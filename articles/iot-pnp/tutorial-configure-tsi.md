---
title: Use Time Series Insights para armazenar e analisar a telemetria do seu dispositivo Azure IoT Plug e Play [ Microsoft Docs
description: Configurar um ambiente de Insights de Séries Tempotamos e ligar o seu hub IoT para visualizar e analisar a telemetria a partir dos seus dispositivos IoT Plug e Play.
author: lyrana
ms.author: lyhughes
ms.date: 10/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: ad5c6f205fc832eb125e52b4135990fc58742e62
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96453239"
---
# <a name="preview-tutorial-create-and-connect-to-time-series-insights-gen2-to-store-visualize-and-analyze-iot-plug-and-play-device-telemetry"></a>Tutorial de Pré-visualização: Criar e Ligar à Série De Tempo Insights Gen2 para armazenar, visualizar e analisar telemetria de ioT plug e play dispositivo

Neste tutorial, aprende-se a criar e configurar corretamente um ambiente [Azure Time Series Insights Gen2](../time-series-insights/overview-what-is-tsi.md) (TSI) para integrar-se com a sua solução IoT Plug and Play. Utilize a TSI para recolher, processar, armazenar, consultar e visualizar dados de séries de tempo na escala Internet of Things (IoT).

Em primeiro lugar, fornece um ambiente TSI e liga o seu hub IoT como fonte de eventos de streaming. Em seguida, trabalha através da sincronização de modelos para autorizar o seu [modelo de série de tempo](../time-series-insights/concepts-model-overview.md) com base nos ficheiros de modelos de [definição de gémeos digitais (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl) que utilizou para o controlador de temperatura e dispositivos termostatos.

> [!NOTE]
> Esta integração está em pré-visualização. Como os modelos de dispositivos DTDL mapeiam para o Modelo série de tempo pode mudar.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Neste momento, tem:

* Um centro de IoT Azure.
* Uma instância DPS ligada ao seu hub IoT, com uma inscrição individual de dispositivo para o seu dispositivo IoT Plug and Play.
* Uma ligação ao seu hub IoT a partir de um único ou multi-componente, streaming de dados simulados.

Para evitar a necessidade de instalar o Azure CLI localmente, pode utilizar o Azure Cloud Shell para configurar os serviços na nuvem.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-your-event-source"></a>Prepare a sua fonte de eventos

O hub IoT que criou anteriormente será a fonte de [eventos](../time-series-insights/concepts-streaming-ingestion-event-sources.md)do seu ambiente TSI.

> [!IMPORTANT]
> Desative quaisquer rotas IoT Hub existentes. Há um problema conhecido quando se usa um hub IoT como fonte de eventos TSI com [encaminhamento](../iot-hub/iot-hub-devguide-messages-d2c.md#routing-endpoints) configurado. Desative temporariamente quaisquer pontos finais de encaminhamento e, quando o seu hub IoT estiver ligado à TSI, pode voltar a capacitá-los.

Crie um grupo de consumidores único no seu hub IoT para a TSI consumir. `my-pnp-hub`Substitua-o pelo nome do hub IoT que utilizou anteriormente:

```azurecli-interactive
az iot hub consumer-group create --hub-name my-pnp-hub --name tsi-consumer-group
```

## <a name="choose-your-time-series-id"></a>Escolha o seu ID série de tempo

Ao providenciar o seu ambiente de TSI, tem de selecionar um *ID da Série De Tempo*. É importante selecionar o ID da Série De Tempo apropriado, porque esta propriedade é imutável e não pode ser alterada depois de definida. Um ID da Série De Tempo é como uma chave de partição de base de dados. O ID da Série De Tempo funciona como a chave primária para o seu modelo de séries de tempo. Para saber mais, consulte [as melhores práticas para escolher um ID da Série De Tempo.](../time-series-insights/how-to-select-tsid.md)

Como utilizador IoT Plug and Play, especifique uma _chave composta_ que consiste em e como o `iothub-connection-device-id` seu `dt-subject` ID da Série De Tempo. O IoT Hub adiciona estas propriedades do sistema que contêm o IOT Plug e o Dispositivo Play ID e os nomes dos componentes do dispositivo, respectivamente.

Mesmo que os seus modelos ioT plug e dispositivo de reprodução não utilizem atualmente componentes, deve incluir `dt-subject` como parte de uma chave composta para que possa usá-los no futuro. Como o seu ID da Série De Tempo é imutável, a Microsoft recomenda ativar esta opção caso precise dela no futuro.

> [!NOTE]
> Os exemplos abaixo são para o dispositivo **TemperatureController** multicomponente, mas os conceitos são os mesmos para o dispositivo **termóstato** sem componente.

## <a name="provision-your-tsi-environment"></a>Provisione o seu ambiente TSI

Esta secção descreve como fornecer o seu ambiente Azure Time Series Insights Gen2.

O seguinte comando:

* Cria uma conta de armazenamento Azure para o [cold store](../time-series-insights/concepts-storage.md#cold-store)do seu ambiente, projetado para retenção e análise a longo prazo sobre dados históricos.
  * `mytsicoldstore`Substitua-o por um nome único para a sua conta de armazenamento frio.
* Cria um ambiente Azure Time Series Insights Gen2, incluindo armazenamento quente com um período de retenção de sete dias, e armazenamento frio para retenção infinita.
  * `my-tsi-env`Substitua-o por um nome único para o seu ambiente de TSI.
  * `my-pnp-resourcegroup`Substitua-o pelo nome do grupo de recursos utilizado durante a configuração.
  * `iothub-connection-device-id, dt-subject` é a sua propriedade de ID séries de tempo.

```azurecli-interactive
storage=mytsicoldstore
rg=my-pnp-resourcegroup
az storage account create -g $rg -n $storage --https-only
key=$(az storage account keys list -g $rg -n $storage --query [0].value --output tsv)
az timeseriesinsights environment longterm create --name my-tsi-env --resource-group $rg --time-series-id-properties iothub-connection-device-id, dt-subject --sku-name L1 --sku-capacity 1 --data-retention 7 --storage-account-name $storage --storage-management-key $key --location eastus2
```

Ligue a sua fonte de eventos IoT Hub. `my-pnp-resourcegroup` `my-pnp-hub` Substitua, e pelos `my-tsi-env` valores que escolheu. O seguinte comando refere o grupo de consumidores para a TSI que criou anteriormente:

```azurecli-interactive
rg=my-pnp-resourcegroup
iothub=my-pnp-hub
env=my-tsi-env
es_resource_id=$(az iot hub create -g $rg -n $iothub --query id --output tsv)
shared_access_key=$(az iot hub policy list -g $rg --hub-name $iothub --query "[?keyName=='service'].primaryKey" --output tsv)
az timeseriesinsights event-source iothub create -g $rg --environment-name $env -n iot-hub-event-source --consumer-group-name tsi-consumer-group  --key-name iothubowner --shared-access-key $shared_access_key --event-source-resource-id $es_resource_id
```

Navegue para o seu grupo de recursos no [portal Azure](https://portal.azure.com) e selecione o seu novo ambiente Time Series Insights. Visite o *URL do Time Series Insights Explorer* mostrado na visão geral do exemplo:

![Página de descrição geral do portal](./media/tutorial-configure-tsi/view-environment.png)

No explorador, vê-se três instâncias:

* &lt;o seu ID do dispositivo pnp, &gt; termostato1
* &lt;o seu ID do dispositivo pnp, &gt; termostato2
* &lt;o seu ID do dispositivo &gt; pnp, `null`

> [!NOTE]
> A terceira etiqueta representa a telemetria do próprio **TemperatureController,** tal como o conjunto de trabalho da memória do dispositivo. Como esta é uma propriedade de alto nível, o valor para o nome do componente é nulo. Num passo posterior, atualiza-o para um nome mais fácil de utilizar.

![Vista do explorador 1](./media/tutorial-configure-tsi/tsi-env-first-view.png)

## <a name="configure-model-translation"></a>Configurar tradução de modelo

Em seguida, traduz o seu modelo de dispositivo DTDL para o modelo de ativo em Azure Time Series Insights (TSI). O Modelo de Séries Tempotares da TSI é uma ferramenta de modelação semântica para a contextualização de dados dentro da TSI. O Modelo série de tempo tem três componentes principais:

* [Exemplos de modelos de séries de tempo](../time-series-insights/concepts-model-overview.md#time-series-model-instances). Os casos são representações virtuais das próprias séries temporências. As instâncias são identificadas exclusivamente pelo seu ID da Série De Tempo.
* [Hierarquias modelo séries de tempo](../time-series-insights/concepts-model-overview.md#time-series-model-hierarchies). As hierarquias organizam instâncias especificando nomes de propriedade e suas relações.
* [Tipos de modelos séries de tempo](../time-series-insights/concepts-model-overview.md#time-series-model-types). Os tipos ajudam-no a definir [variáveis](../time-series-insights/concepts-variables.md) ou fórmulas para computação. Os tipos estão associados a uma instância específica.

### <a name="define-your-types"></a>Defina os seus tipos

Pode começar a ingerir dados no Azure Time Series Insights Gen2 sem ter predefinido um modelo. Quando a telemetria chega, a TSI tenta autoestimar casos de séries de tempo com base no valor de propriedade da Série Time Series. Todas as instâncias são atribuídas ao *tipo predefinido*. É necessário criar manualmente um novo tipo para categorizar corretamente as suas ocorrências. Os seguintes detalhes mostram o método mais simples para sincronizar os modelos DTDL do seu dispositivo com os seus tipos de Modelos séries de tempo:

* O seu identificador de modelos gémeos digitais torna-se o seu ID tipo.
* O nome do tipo pode ser o nome do modelo ou o nome do visor.
* A descrição do modelo torna-se a descrição do tipo.
* Pelo menos uma variável do tipo é criada para cada telemetria com um esquema numérico.
  * Apenas tipos de dados numéricos podem ser usados para variáveis, mas se um valor for enviado como outro tipo que pode ser convertido, `"0"` por exemplo, pode utilizar uma função de [conversão](/rest/api/time-series-insights/reference-time-series-expression-syntax.md#conversion-functions) como `toDouble` .
* O nome variável pode ser o nome da telemetria ou o nome do visor.
* Quando definir a expressão variável time series, consulte o nome da telemetria no fio, e é o tipo de dados.

| DTDL JSON | Modelo série de tempo JSON | Valor de exemplo |
|-----------|------------------|-------------|
| `@id` | `id` | `dtmi:com:example:TemperatureController;1` |
| `displayName`    | `name`   |   `Temperature Controller`  |
| `description`  |  `description`  |  `Device with two thermostats and remote reboot.` |  
|`contents` (matriz)| `variables` (objeto)  | Veja o exemplo abaixo

![DTDL para tipo de modelo série de tempo](./media/tutorial-configure-tsi/DTDL-to-TSM-Type.png)

> [!NOTE]
> Este exemplo mostra três variáveis, mas cada tipo pode ter até 100. Diferentes variáveis podem referenciar o mesmo valor de telemetria para realizar diferentes cálculos conforme necessário. Para a lista completa de filtros, agregados e funções escalar consulte a sintaxe de [expressão da série de tempo de Insights Gen2.](/rest/api/time-series-insights/reference-time-series-expression-syntax.md)

Abra um editor de texto e guarde o seguinte JSON para a sua unidade local:

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

No seu Time Series Insights Explorer, navegue para o **separador Modelo** selecionando o ícone do modelo à esquerda. Selecione **Tipos** e, em seguida, selecione **Upload JSON**:

![Carregar](./media/tutorial-configure-tsi/upload-type.png)

**Selecione Escolha o ficheiro**, selecione o JSON que guardou anteriormente e selecione **Upload**

Vê-se o tipo de **controlador de temperatura** recentemente definido.

### <a name="create-a-hierarchy"></a>Criar uma hierarquia

Crie uma hierarquia para organizar as etiquetas sob o seu pai **TemperatureController.** O exemplo simples seguinte tem um único nível, mas as soluções IoT geralmente têm muitos níveis de nidificação para contextualizar tags dentro da sua posição física e semântica dentro de uma organização.

Selecione **Hierarquias** e **selecione Adicionar hierarquia**. Insira a *Frota de Dispositivos* como o nome e crie um nível chamado *Nome do Dispositivo*. Em seguida, selecione **Guardar**.

![Adicione uma hierarquia](./media/tutorial-configure-tsi/add-hierarchy.png)

### <a name="assign-your-instances-to-the-correct-type"></a>Atribua as suas instâncias ao tipo correto

Em seguida, muda-se o tipo de instâncias e coloca-se na hierarquia.

Selecione o **separador Instâncias,** encontre a instância que representa o conjunto de trabalho do dispositivo e selecione o ícone **Editar** na extrema-direita:

![Editar instâncias](./media/tutorial-configure-tsi/edit-instance.png)

Selecione o **tipo** de descida e selecione **o controlador de temperatura**. Introduza *o número <your device name> padrãoComponent,* para atualizar o nome da instância que representa todas as tags de nível superior associadas ao seu dispositivo.

![Alterar tipo de instância](./media/tutorial-configure-tsi/change-type.png)

Antes de selecionar, selecione o **separador Campos de Ocorrência** e verifique a caixa **da Frota do Dispositivo.** `<your device name> - Temp Controller`Insira para agrupar a telemetria e, em seguida, **selecione Save**.

![Atribuir à hierarquia](./media/tutorial-configure-tsi/assign-to-hierarchy.png)

Repita os passos anteriores para atribuir as etiquetas do termóstato corretas ao tipo e à hierarquia.

## <a name="view-your-data"></a>Ver os dados

Volte para o painel de gráficos e expanda a **Frota de Dispositivos > o seu dispositivo**. Selecione **o termóstato1,** selecione a variável **Temperatura** e, em seguida, selecione **Adicionar** para traçar o valor. Faça o mesmo para **o termóstato2** e o valor padrão **de trabalhocomponente.** **defaultComponent**

![Alterar tipo de instância para termóstato2](./media/tutorial-configure-tsi/charting-values.png)

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre as várias opções de gráficos, incluindo tamanho de intervalo e controlos de eixo Y, consulte [o Azure Time Series Insights Explorer](../time-series-insights/concepts-ux-panels.md).

* Para uma visão geral aprofundada do modelo da Série De Tempo do seu ambiente, consulte o Modelo da Série de Tempo no artigo [Azure Time Series Insights Gen2.](../time-series-insights/concepts-model-overview.md)

* Para mergulhar nas APIs de consulta e na sintaxe de expressão da série de tempo, consulte [Azure Time Series Insights Gen2 Consultas APIs](/rest/api/time-series-insights/reference-query-apis.md).

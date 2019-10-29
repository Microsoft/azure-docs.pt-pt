---
title: Práticas recomendadas para escolher uma ID de série temporal na visualização Azure Time Series Insights | Microsoft Docs
description: Noções básicas sobre práticas recomendadas quando você escolhe uma ID de série temporal na visualização Azure Time Series Insights.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/22/2019
ms.custom: seodec18
ms.openlocfilehash: 48f1fb542f5e28c7b8130d03cd86442390a8ad56
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2019
ms.locfileid: "72989946"
---
# <a name="best-practices-for-choosing-a-time-series-id"></a>Práticas recomendadas para escolher uma ID de série temporal

Este artigo resume a importância da ID da série temporal para seu ambiente de visualização Azure Time Series Insights e as práticas recomendadas para escolher uma.

## <a name="choose-a-time-series-id"></a>Escolher um ID de Série de Tempo

Escolher uma ID de série temporal é como escolher uma chave de partição para um banco de dados. Ele precisa ser selecionado enquanto você estiver criando um ambiente de visualização de Time Series Insights. É uma propriedade *imutável* . Ou seja, depois de criar um ambiente de visualização de Time Series Insights com uma ID de série temporal, você não poderá alterá-lo para esse ambiente. 

> [!IMPORTANT]
> A ID da série temporal diferencia maiúsculas de minúsculas.

A seleção de uma ID de série temporal apropriada é crítica. Aqui estão algumas das práticas recomendadas que você pode seguir:

* Escolha uma chave de partição com muitos valores distintos (por exemplo, centenas ou milhares). Em muitos casos, essa pode ser a ID do dispositivo, ID do sensor ou ID de marca em seu JSON.
* A ID da série temporal deve ser exclusiva no nível do nó folha do seu [modelo de série temporal](./time-series-insights-update-tsm.md).
* Se a origem do evento for um hub IoT, a ID da série temporal provavelmente será *iothub-Connection-Device-ID*.
* O limite de caracteres para a cadeia de caracteres do nome da propriedade da ID da série temporal é 128. Para o valor da propriedade da ID da série temporal, o limite de caracteres é 1.024.
* Se um valor de propriedade exclusivo para a ID da série temporal estiver ausente, ele será tratado como um valor nulo e seguirá a mesma regra da restrição de exclusividade.
* Você também pode selecionar até *três* Propriedades de chave como sua ID de série temporal. Sua combinação será uma chave composta que representa a ID da série temporal.  

  > [!NOTE]
  > Suas propriedades de três chaves devem ser cadeias de caracteres.
  > Você precisaria consultar essa chave composta em vez de uma propriedade de cada vez.

Os cenários a seguir descrevem a seleção de mais de uma propriedade de chave como sua ID de série temporal.  

### <a name="example-1-time-series-id-with-a-unique-key"></a>Exemplo 1: ID de série temporal com uma chave exclusiva

* Você tem frotas de ativos herdados. Cada um tem uma chave exclusiva.
* Uma frota é identificada exclusivamente pela propriedade **DeviceID**. Para outra frota, a propriedade Unique é **ObjectID**. Nenhuma frota contém a propriedade exclusiva da outra frota. Neste exemplo, você selecionaria duas chaves, **DeviceID** e **ObjectID**, como chaves exclusivas.
* Aceitamos valores nulos e a falta de uma presença de propriedade no conteúdo do evento conta como um valor nulo. Essa também é a maneira apropriada de lidar com o envio de dados para duas origens de evento, em que os dados em cada origem de evento têm uma ID de série temporal exclusiva.

### <a name="example-2-time-series-id-with-a-composite-key"></a>Exemplo 2: ID de série temporal com uma chave composta

* Você precisa de várias propriedades para serem exclusivas na mesma frota de ativos. 
* Você é um fabricante de prédios inteligentes e implanta sensores em todas as salas. Em cada sala, você normalmente tem os mesmos valores para **sensorid**. Os exemplos são **sensor1**, **sensor2**e **sensor3**.
* Seu edifício tem sobreposição de piso e números de sala entre sites na propriedade **flrRm**. Esses números têm valores como **1a**, **2B**e **3a**.
* Você tem uma propriedade, **local**, que contém valores como **Redmond**, **Barcelona**e **Tokyo**. Para criar a exclusividade, você designa as três propriedades a seguir como suas chaves de ID de série temporal: **sensorid**, **flrRm**e **local**.

Exemplo de evento bruto:

```JSON
{
  "sensorId": "sensor1",
  "flrRm": "1a",
  "location": "Redmond",
  "temperature": 78
}
```

Na portal do Azure, você pode inserir essa chave composta como: 

`[{"name":"sensorId","type":"String"},{"name":"flrRm","type":"String"},{"name":"location","type":"string"}]`

## <a name="next-steps"></a>Passos seguintes

* Leia mais sobre [modelagem de dados](./time-series-insights-update-tsm.md).

* Planeje seu [ambiente de visualização de Azure Time Series insights](./time-series-insights-update-plan.md).
---
title: Práticas recomendadas para escolher uma ID de série temporal-Azure Time Series Insights | Microsoft Docs
description: Saiba mais sobre as práticas recomendadas ao escolher uma ID de série temporal na visualização Azure Time Series Insights.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/19/2019
ms.custom: seodec18
ms.openlocfilehash: 6fd8d6187c86306840c33b3aaf334e71086b20a1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75452748"
---
# <a name="best-practices-for-choosing-a-time-series-id"></a>Melhores práticas para a escolha de um ID de série de tempo

Este artigo resume a importância da ID da série temporal para seu ambiente de visualização Azure Time Series Insights e as práticas recomendadas para escolher uma.

## <a name="choose-a-time-series-id"></a>Escolher um ID de Série de Tempo

A seleção de uma ID de série temporal apropriada é crítica. Escolher um ID de série de tempo é como escolher uma chave de partição para uma base de dados. É necessário quando você cria um ambiente de visualização de Time Series Insights. 

> [!IMPORTANT]
> As IDs de série temporal são:
> * Uma propriedade que *diferencia maiúsculas de minúsculas* : letras e maiúsculas de caracteres são usadas em pesquisas, comparações, atualizações e ao particionar.
> * Uma propriedade *imutável* : uma vez criada, ela não pode ser alterada.

> [!TIP]
> Se a origem do evento for um hub IoT, a ID da série temporal provavelmente será ***iothub-Connection-Device-ID***.

As principais práticas recomendadas a serem seguidas incluem:

* Escolha uma chave de partição com muitos valores distintos (por exemplo, centenas ou milhares). Em muitos casos, essa pode ser a ID do dispositivo, ID do sensor ou ID de marca em seu JSON.
* O ID de série de tempo deve ser exclusivo no nível de nó folha da sua [modelo de série de tempo](./time-series-insights-update-tsm.md).
* O limite de caracteres para a cadeia de caracteres do nome da propriedade da ID da série temporal é 128. Para o valor da propriedade da ID da série temporal, o limite de caracteres é 1.024.
* Se um valor de propriedade exclusivo para a ID da série temporal estiver ausente, ele será tratado como um valor nulo e seguirá a mesma regra da restrição de exclusividade.
* Você também pode selecionar até *três* Propriedades de chave como sua ID de série temporal. Sua combinação será uma chave composta que representa a ID da série temporal.  
  > [!NOTE]
  > Suas propriedades de três chaves devem ser cadeias de caracteres.
  > Você precisaria consultar essa chave composta em vez de uma propriedade de cada vez.

## <a name="select-more-than-one-key-property"></a>Selecionar mais de uma propriedade de chave

Os cenários a seguir descrevem a seleção de mais de uma propriedade de chave como sua ID de série temporal.  

### <a name="example-1-time-series-id-with-a-unique-key"></a>Exemplo 1: ID de série temporal com uma chave exclusiva

* Você tem frotas de ativos herdados. Cada um tem uma chave exclusiva.
* Uma frota é identificada exclusivamente pela propriedade **DeviceID**. Para outra frota, a propriedade Unique é **ObjectID**. Nenhuma frota contém a propriedade exclusiva da outra frota. Neste exemplo, você selecionaria duas chaves, **DeviceID** e **ObjectID**, como chaves exclusivas.
* Aceitamos valores nulos e a falta de uma presença de propriedade no conteúdo do evento conta como um valor nulo. Essa também é a maneira apropriada de lidar com o envio de dados para duas origens de evento, em que os dados em cada origem de evento têm uma ID de série temporal exclusiva.

### <a name="example-2-time-series-id-with-a-composite-key"></a>Exemplo 2: ID de série temporal com uma chave composta

* Precisa de várias propriedades de ser exclusivo dentro do mesmo frota de ativos. 
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

Na portal do Azure, você pode inserir a chave composta da seguinte maneira: 

```JSON
[{"name":"sensorId","type":"String"},{"name":"flrRm","type":"String"},{"name":"location","type":"string"}]
```

## <a name="next-steps"></a>Passos seguintes

* Leia mais sobre [modelagem de dados](./time-series-insights-update-tsm.md).

* Planeje seu [ambiente de visualização de Azure Time Series insights](./time-series-insights-update-plan.md).
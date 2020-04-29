---
title: Boas práticas para escolher um ID da Série Tempo - Azure Time Series Insights [ Microsoft Docs
description: Aprenda sobre as melhores práticas ao escolher um ID da Série De Tempo em Visualização de Insights da Série De Tempo Azure.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: seodec18
ms.openlocfilehash: a62c2460698408f6a2bfa51c6638bdeaf88bb31f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77083522"
---
# <a name="best-practices-for-choosing-a-time-series-id"></a>Boas práticas para escolher um ID da Série De Tempo

Este artigo resume a importância do ID da Série De Tempo para o seu ambiente de pré-visualização da Série De Tempo Azure Insights, e as melhores práticas para escolher um.

## <a name="choose-a-time-series-id"></a>Escolher um ID de Série de Tempo

A seleção de um ID de Série de Tempo apropriado é fundamental. Escolher um ID da Série De Tempo é como escolher uma chave de partição para uma base de dados. É necessário quando cria um ambiente de pré-visualização de Insights da Série De Tempo. 

> [!IMPORTANT]
> Os IDs da Série Tempora são:
> * Uma propriedade *sensível a casos:* os invólucros de letras e caracteres são usados em pesquisas, comparações, atualizações e quando se partificam.
> * Uma propriedade *imutável:* uma vez criada, não pode ser alterada.

> [!TIP]
> Se a sua fonte de evento for um hub IoT, o id da Série Time será provavelmente ***iothub-connection-device-id***.

As principais práticas a seguir incluem:

* Escolha uma chave de partição com muitos valores distintos (por exemplo, centenas ou milhares). Em muitos casos, este pode ser o ID do dispositivo, identificação do sensor ou identificação de etiqueta seleções no seu JSON.
* O ID da Série De Tempo deve ser único ao nível do nó da folha do seu [Modelo de Série de Tempo](./time-series-insights-update-tsm.md).
* O limite de caracteres para a cadeia de nomes de propriedade da Time Series ID é 128. Para o valor de propriedade da Série Time ID, o limite de caracteres é 1.024.
* Se falta um valor único de propriedade para o ID da Série De Tempo, é tratado como um valor nulo e segue a mesma regra da restrição de singularidade.
* Também pode selecionar até *três* propriedades chave como id da Série Time. A sua combinação será uma chave composta que representa o ID da Série Tempo.  
  > [!NOTE]
  > As suas três propriedades-chave devem ser cordas.
  > Teria que fazer uma consulta contra esta chave composta em vez de uma propriedade de cada vez.

## <a name="select-more-than-one-key-property"></a>Selecione mais do que uma propriedade chave

Os seguintes cenários descrevem a seleção de mais de uma propriedade chave como id da Série De Tempo.  

### <a name="example-1-time-series-id-with-a-unique-key"></a>Exemplo 1: ID da série de tempo com uma chave única

* Tem frotas de bens legados. Cada um tem uma chave única.
* Uma frota é identificada exclusivamente pelo dispositivo de **propriedadeId**. Para outra frota, a propriedade única é **objectId**. Nenhuma frota contém a propriedade única da outra frota. Neste exemplo, selecionaria duas teclas, **dispositivoId** e **objectId,** como teclas únicas.
* Aceitamos valores nulos, e a falta de presença de um imóvel no evento a carga útil conta como um valor nulo. Esta é também a forma apropriada de lidar com o envio de dados para duas fontes de eventos onde os dados em cada fonte de evento saem de um ID exclusivo da Série de Tempo.

### <a name="example-2-time-series-id-with-a-composite-key"></a>Exemplo 2: ID da série de tempo com uma chave composta

* Você precisa que várias propriedades sejam únicas dentro da mesma frota de ativos. 
* És um fabricante de edifícios inteligentes e implantas sensores em todas as salas. Em cada quarto, você normalmente tem os mesmos valores para **sensorId**. Exemplos são **sensor1,** **sensor2**, e **sensor3**.
* O seu edifício tem números sobrepostos de piso e quartos em locais da **propriedade flrRm**. Estes números têm valores como **1a,** **2b**e **3a**.
* Você tem uma propriedade, **localização,** que contém valores como **Redmond,** **Barcelona,** e **Tóquio.** Para criar uma singularidade, designa as seguintes três propriedades como chaves ID da Série Time: **sensorId,** **flrRm,** e **localização**.

Exemplo de evento bruto:

```JSON
{
  "sensorId": "sensor1",
  "flrRm": "1a",
  "location": "Redmond",
  "temperature": 78
}
```

No portal Azure, pode então introduzir a chave composta da seguinte forma: 

```JSON
[{"name":"sensorId","type":"String"},{"name":"flrRm","type":"String"},{"name":"location","type":"string"}]
```

## <a name="next-steps"></a>Passos seguintes

* Leia mais sobre [modelação](./time-series-insights-update-tsm.md)de dados .

* Planeie o ambiente de [pré-visualização](./time-series-insights-update-plan.md)da série de tempo Azure Insights .
---
title: Melhores práticas para escolher um ID da Série De Tempo - Azure Time Series Insights | Microsoft Docs
description: Saiba mais sobre as melhores práticas ao escolher um ID da Série De Tempo em Azure Time Series Insights Gen2.
author: shipramishra
ms.author: shmishr
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 03/23/2021
ms.custom: seodec18
ms.openlocfilehash: 8aefa6efa5f10dae8a28e7126b91b7b5f6ac77a6
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "104950744"
---
# <a name="best-practices-for-choosing-a-time-series-id"></a>Melhores práticas para escolher um ID da Série Temporal

Este artigo resume a importância do ID da Série De Tempo para o seu ambiente Azure Time Series Insights Gen2, e as melhores práticas para escolher um.

## <a name="choose-a-time-series-id"></a>Escolher um ID de Série de Tempo

A seleção de um ID de série de tempo apropriado é fundamental. Escolher um ID da Série De Tempo é como escolher uma chave de partição para uma base de dados. É necessário quando se cria um ambiente Azure Time Series Insights Gen2.

Observe o tutorial de provisão ambiental para uma explicação detalhada do ID da Série De Tempo. Você verá dois exemplos diferentes de carga útil de telemetria JSON e a seleção correta de ID série de tempo para cada um.</br>

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWzk3P]


> [!IMPORTANT]
> Os IDs da Série De Tempo são:
>
> * Uma propriedade *sensível a casos:* as cápsulas de letra e de caracteres são usadas em pesquisas, comparações, atualizações e quando partições.
> * Uma propriedade *imutável:* uma vez criada não pode ser alterada.

> [!TIP]
> Se a sua fonte de evento for um hub IoT, o seu ID da Série De Tempo será provavelmente ***iothub-connection-device-id***.

As melhores práticas a seguir incluem:

* Escolha uma chave de partição com muitos valores distintos (por exemplo, centenas ou milhares). Em muitos casos, este pode ser o ID do dispositivo, iD do sensor ou identificação de etiqueta no seu JSON.
* O ID da Série De Tempo deve ser único ao nível do nó de folha do seu [modelo de séries de tempo.](./concepts-model-overview.md)
* O limite de caracteres para a cadeia de nomes de propriedade da Série DeTemporal é 128. Para o valor da propriedade do Time Series ID, o limite de caracteres é de 1.024.
* Se faltar um valor único de propriedade para o ID da Série De Tempo, é tratado como um valor nulo e segue a mesma regra da restrição de singularidade.
* Se o seu ID da Série De Tempo estiver aninhado dentro de um objeto JSON complexo, certifique-se de seguir as regras de [achatamento](./concepts-json-flattening-escaping-rules.md) ao fornecer o seu nome de propriedade. Confira o exemplo [B](concepts-json-flattening-escaping-rules.md#example-b).
* Também pode selecionar até *três* propriedades chave como o seu ID série de tempo. A sua combinação será uma chave composta que representa o ID da Série De Tempo.  
  > [!NOTE]
  > Suas três propriedades chave devem ser cordas.
  > Teria que consultar esta chave composta em vez de uma propriedade de cada vez.

## <a name="select-more-than-one-key-property"></a>Selecione mais de uma propriedade chave

Os seguintes cenários descrevem a seleção de mais de uma propriedade chave como o seu ID da Série De Tempo.  

### <a name="example-1-time-series-id-with-a-unique-key"></a>Exemplo 1: ID da Série De Tempo com uma chave única

* Tem frotas de bens antigas. Cada um tem uma chave única.
* Uma frota é identificada exclusivamente pelo dispositivo de **propriedadeId.** Para outra frota, a propriedade única é **objectId.** Nenhuma frota contém a propriedade única da outra frota. Neste exemplo, você selecionaria duas teclas, **deviceId** e **objectId,** como teclas únicas.
* Aceitamos valores nulos, e a falta de presença de um imóvel no evento a carga conta como um valor nulo. Esta é também a forma apropriada de lidar com o envio de dados para duas fontes de eventos onde os dados em cada fonte de evento tem um ID de Série Sonora único.

### <a name="example-2-time-series-id-with-a-composite-key"></a>Exemplo 2: ID da Série de Tempo com uma chave composta

* Você precisa que várias propriedades sejam únicas dentro da mesma frota de ativos.
* És um fabricante de edifícios inteligentes e sensores em todas as salas. Em cada quarto, normalmente tem os mesmos valores para **sensorId**. Exemplos são **sensores1,** **sensor2** e **sensores3**.
* O seu edifício tem números de piso e quarto sobrepostos em locais da propriedade **flrRm**. Estes números têm valores como **1a,** **2b,** e **3a.**
* Você tem uma propriedade, **localização,** que contém valores como **Redmond,** **Barcelona,** e **Tóquio.** Para criar a singularidade, designa as seguintes três propriedades como as suas teclas de ID da Série De Tempo: **sensorId,** **flrRm** e **localização**.

Exemplo de evento cru:

```JSON
{
  "sensorId": "sensor1",
  "flrRm": "1a",
  "location": "Redmond",
  "temperature": 78
}
```

No portal Azure, pode então introduzir a tecla composta da seguinte forma:

[![Configurar o ID da Série De Tempo para o ambiente.](media/v2-how-to-tsid/configure-environment-key.png)](media/v2-how-to-tsid/configure-environment-key.png#lightbox)

  > [!NOTE]
  > No portal Azure, não introduza nomes separados de vírgula de propriedades numa caixa de texbox, caso contrário, será tratado como um único nome de propriedade que contém vírgulas.
  > Insira cada nome de propriedade na sua própria caixa de texbox.

## <a name="next-steps"></a>Passos seguintes

* Leia o [JSON achatando e escapando às regras](./concepts-json-flattening-escaping-rules.md) para entender como os eventos serão armazenados.

* Planeie o seu [ambiente Azure Time Series Insights Gen2](./how-to-plan-your-environment.md).
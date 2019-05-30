---
title: Melhores práticas para a escolha de um ID de série de tempo na pré-visualização do Azure Time Series Insights | Documentos da Microsoft
description: Noções básicas sobre melhores práticas ao escolher um ID de série de tempo na pré-visualização do Azure Time Series Insights.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 05/08/2019
ms.custom: seodec18
ms.openlocfilehash: 4b2f538831ee9410eaf1a2d272f01fd30a9236e6
ms.sourcegitcommit: 17411cbf03c3fa3602e624e641099196769d718b
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/10/2019
ms.locfileid: "65519445"
---
# <a name="best-practices-for-choosing-a-time-series-id"></a>Melhores práticas para a escolha de um ID de série de tempo

Este artigo aborda a chave de partição de pré-visualização do Azure Time Series Insights, o ID de série de tempo e melhores práticas para escolher um.

## <a name="choose-a-time-series-id"></a>Escolher um ID de Série de Tempo

Escolher um ID de série de tempo é como escolher uma chave de partição para uma base de dados. É uma decisão importante que deve ser feita em tempo de design. Não é possível atualizar um ambiente de pré-visualização do Time Series Insights existente para utilizar um ID de série de tempo diferente Em outras palavras, quando um ambiente é criado com um ID de série de tempo, a política é uma propriedade imutável que não pode ser alterada.

> [!IMPORTANT]
> O ID de série de tempo é maiúsculas e minúsculas e imutável (não pode ser alterado após ser definido).

Com isso em mente, selecionar o ID de série de tempo adequado, é fundamental. Quando seleciona um ID de série de tempo, considere seguir estas melhores práticas:

* Escolha um nome de propriedade que tem uma vasta gama de valores e tem até mesmo os padrões de acesso. É recomendável ter uma chave de partição com muitos valores distintos (por exemplo, centenas ou milhares). Para muitos clientes, este será algo como o DeviceID ou SensorID no JSON.
* O ID de série de tempo deve ser exclusivo no nível de nó folha da sua [modelo de série de tempo](./time-series-insights-update-tsm.md).
* Uma cadeia de caracteres de nome de propriedade de ID de série de tempo pode ter até 128 carateres e valores de propriedade de ID de série de tempo podem ter até 1024 carateres.
* Se faltam alguns valores de propriedade de ID de série de tempo exclusivos, são tratadas como valores nulos, o que faça parte da restrição de exclusividade.

Além disso, pode selecionar até *três* propriedades chave (3) como sua série de tempo de ID.

  > [!NOTE]
  > Sua *três* propriedades chave (3) tem de ser cadeias de caracteres.

Os seguintes cenários descrevem selecionar mais do que uma propriedade chave como o seu ID de série de tempo:  

### <a name="scenario-one"></a>Cenário um

* Terá de frotas de legado de ativos, cada um com uma chave exclusiva.
* Por exemplo, uma frota seja identificada exclusivamente pela propriedade *deviceId* e outra em que é a propriedade exclusiva *objectId*. Nenhuma das frota contém a propriedade exclusivo de outros frota. Neste exemplo, deve selecionar duas chaves, deviceId e objectId, como chaves exclusivas.
* Aceitamos valores nulos, e a falta de presença de uma propriedade no payload do evento é contabilizado como um `null` valor. Isso também é a forma adequada para lidar com o envio de dados para duas origens de eventos diferentes em que os dados em cada origem de evento tem um ID de série de tempo exclusivo

### <a name="scenario-two"></a>Cenário dois

* Precisa de várias propriedades de ser exclusivo dentro do mesmo frota de ativos. 
* Por exemplo, digamos que for um fabricante de construção inteligentes e implementar sensores em cada sala. Em cada sala, normalmente têm os mesmos valores para *sensorId*, tal como *sensor1*, *sensor2*, e *sensor3*.
* Além disso, a construção tem a sobreposição de números de andar e espaço em todos os sites na propriedade *flrRm*, que têm valores como *1a*, *2b*, *3a* e assim por diante.
* Finalmente, tem uma propriedade *localização*, que contém valores como *Redmond*, *Barcelona*, e *Tóquio*. Para criar a exclusividade, teria de designar as três propriedades seguintes como as chaves de ID de série de tempo: *sensorId*, *flrRm*, e *localização*.

## <a name="next-steps"></a>Passos Seguintes

* Leia mais sobre [modelação de dados](./time-series-insights-update-tsm.md).

* Planear a sua [ambiente do Azure Time Series Insights (pré-visualização)](./time-series-insights-update-plan.md).
---
title: Modelo de consumo do Azure Maps | Documentos da Microsoft
description: Saiba mais sobre o modelo de consumo do Azure Maps
author: subbarayudukamma
ms.author: skamma
ms.date: 05/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 5f75f656312c11a4668ca9ef9fe7b2a61a7d13e8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60797910"
---
# <a name="consumption-model"></a>Modelo de consumo

Encaminhamento online fornece um conjunto de parâmetros para uma descrição detalhada do modelo de consumo específicos de veículos.
Dependendo do valor de **vehicleEngineType**, são suportados dois modelos de consumo principal: _Combustion_ e _Electric_. Especificar os parâmetros que pertencem a diferentes modelos no mesmo pedido é um erro.
Não é possível utilizar o Modelo de Consumo com os valores _bicycle_ e _pedestrian_ de **travelMode**.

## <a name="parameter-constraints-for-consumption-model"></a>Restrições de parâmetro de modelo de consumo

Em ambos os modelos de consumo, especificar explicitamente alguns parâmetros exige a especificação de alguns outros também. Estas dependências estão:

* Todos os parâmetros exigem **constantSpeedConsumption** seja especificado pelo utilizador. É um erro para especificar qualquer outro consumo modelo parâmetro, com exceção do **vehicleWeight**, se **constantSpeedConsumption** não for especificado.
* **accelerationEfficiency** e **decelerationEfficiency** sempre deve ser especificado como um par (ou seja, ambos ou nenhum).
* Se **accelerationEfficiency** e **decelerationEfficiency** forem especificados, produto dos respetivos valores não tem de ser superior a 1 (para evitar perpetual motion).
* **uphillEfficiency** e **downhillEfficiency** sempre deve ser especificado como um par (ou seja, ambos ou nenhum).
* Se **uphillEfficiency** e **downhillEfficiency** forem especificados, produto dos respetivos valores não tem de ser superior a 1 (para evitar perpetual motion).
* Se o \* __eficiência__ parâmetros especificados pelo usuário, em seguida, **vehicleWeight** também tem de ser especificado. Quando **vehicleEngineType** é _combustion_, **fuelEnergyDensityInMJoulesPerLiter** tem de ser especificado bem.
* **maxChargeInkWh** e **currentChargeInkWh** sempre deve ser especificado como um par (ou seja, ambos ou nenhum).

> [!NOTE]
> Se só **constantSpeedConsumption** for especificado, não existem outros aspectos de consumo, como slopes e aceleração de veículos são levados em consideração para computações de consumo.

## <a name="combustion-consumption-model"></a>Modelo de consumo Combustion

O modelo de consumo Combustion é usado quando **vehicleEngineType** está definida como _combustion_.
A lista de parâmetros que pertencem a esse modelo estão abaixo. Consulte a secção de parâmetros para a descrição detalhada.

* constantSpeedConsumptionInLitersPerHundredkm
* VehicleWeight
* currentFuelInLiters
* auxiliaryPowerInLitersPerHour
* fuelEnergyDensityInMJoulesPerLiter
* accelerationEfficiency
* decelerationEfficiency
* uphillEfficiency
* downhillEfficiency

## <a name="electric-consumption-model"></a>Modelo de consumo Electric

O modelo de consumo Electric é usado quando **vehicleEngineType** está definida como _electric_.
A lista de parâmetros que pertencem a esse modelo estão abaixo. Consulte a secção de parâmetros para a descrição detalhada.

* constantSpeedConsumptionInkWhPerHundredkm
* VehicleWeight
* currentChargeInkWh
* maxChargeInkWh
* auxiliaryPowerInkW
* accelerationEfficiency
* decelerationEfficiency
* uphillEfficiency
* downhillEfficiency

## <a name="sensible-values-of-consumption-parameters"></a>Valores razoáveis dos parâmetros de consumo

Pode ser rejeitado um determinado conjunto de parâmetros de consumo, mesmo que ele pode atender a todos os requisitos explícitos especificados acima. Isso acontece quando o valor de um parâmetro específico ou uma combinação de valores de vários parâmetros, é considerado para levar a magnitudes excessiva de valores de consumo. Se isto acontecer, é muito provável que indica um erro de entrada, conforme adequado cuidado para acomodar todos os valores razoáveis dos parâmetros de consumo. No caso de um determinado conjunto de parâmetros de consumo é rejeitado, a mensagem de erro que acompanha este artigo irá conter uma explicação textual sobre os motivos.
As descrições detalhadas dos parâmetros têm de exemplos de valores razoáveis para ambos os modelos.

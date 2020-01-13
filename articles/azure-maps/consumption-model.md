---
title: Modelos de consumo de veículo para roteamento | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá sobre os modelos de consumo de veículo para roteamento em mapas de Microsoft Azure.
author: subbarayudukamma
ms.author: skamma
ms.date: 05/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 5a8a0778ce279846b0d7a66b1729b6898e80a4b5
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911700"
---
# <a name="consumption-model"></a>Modelo de consumo

O roteamento online fornece um conjunto de parâmetros para uma descrição detalhada do modelo de consumo específico do veículo.
Dependendo do valor de **vehicleEngineType**, há suporte para dois modelos de consumo _principal:_ compendênciaing e _Electric_. A especificação de parâmetros que pertencem a modelos diferentes na mesma solicitação é um erro.
Não é possível utilizar o Modelo de Consumo com os valores _bicycle_ e _pedestrian_ de **travelMode**.

## <a name="parameter-constraints-for-consumption-model"></a>Restrições de parâmetro para o modelo de consumo

Em ambos os modelos de consumo, especificar explicitamente alguns parâmetros requer a especificação de alguns outros também. Essas dependências são:

* Todos os parâmetros exigem que o **constantSpeedConsumption** seja especificado pelo usuário. É um erro especificar qualquer outro parâmetro de modelo de consumo, com exceção de **vehicleWeight**, se **constantSpeedConsumption** não for especificado.
* **accelerationEfficiency** e **decelerationEfficiency** devem ser sempre especificados como um par (ou seja, ambos ou nenhum).
* Se **accelerationEfficiency** e **decelerationEfficiency** forem especificados, o produto dos seus valores não deverá ser maior que 1 (para evitar o movimento permanente).
* **uphillEfficiency** e **downhillEfficiency** devem ser sempre especificados como um par (ou seja, ambos ou nenhum).
* Se **uphillEfficiency** e **downhillEfficiency** forem especificados, o produto dos seus valores não deverá ser maior que 1 (para evitar o movimento permanente).
* Se os parâmetros de __eficiência__ \*forem especificados pelo usuário, **vehicleWeight** também deverá ser especificado. Quando **vehicleEngineType** forrefazer, **fuelEnergyDensityInMJoulesPerLiter** também deverá ser especificado.
* **maxChargeInkWh** e **currentChargeInkWh** devem ser sempre especificados como um par (ou seja, ambos ou nenhum).

> [!NOTE]
> Se apenas **constantSpeedConsumption** for especificado, nenhum outro aspecto de consumo como inclinação e aceleração do veículo será levado em conta para cálculos de consumo.

## <a name="combustion-consumption-model"></a>Modelo de consumo de reutilização

O modelo de consumo de retratação é usado quando **vehicleEngineType** é _definido como_resumido.
A lista de parâmetros que pertencem a este modelo está abaixo. Consulte a seção de parâmetros para obter uma descrição detalhada.

* constantSpeedConsumptionInLitersPerHundredkm
* vehicleWeight
* currentFuelInLiters
* auxiliaryPowerInLitersPerHour
* fuelEnergyDensityInMJoulesPerLiter
* accelerationEfficiency
* decelerationEfficiency
* uphillEfficiency
* downhillEfficiency

## <a name="electric-consumption-model"></a>Modelo de consumo elétrico

O modelo de consumo elétrico é usado quando **vehicleEngineType** é definido como _elétrico_.
A lista de parâmetros que pertencem a este modelo está abaixo. Consulte a seção de parâmetros para obter uma descrição detalhada.

* constantSpeedConsumptionInkWhPerHundredkm
* vehicleWeight
* currentChargeInkWh
* maxChargeInkWh
* auxiliaryPowerInkW
* accelerationEfficiency
* decelerationEfficiency
* uphillEfficiency
* downhillEfficiency

## <a name="sensible-values-of-consumption-parameters"></a>Valores razoáveis de parâmetros de consumo

Um determinado conjunto de parâmetros de consumo pode ser rejeitado, embora possa atender a todos os requisitos explícitos especificados acima. Acontece quando o valor de um parâmetro específico, ou uma combinação de valores de vários parâmetros, é considerado para levar a magnitudes indesejadas de valores de consumo. Se isso acontecer, provavelmente indica um erro de entrada, pois é necessário tomar cuidado para acomodar todos os valores razoáveis dos parâmetros de consumo. Caso um determinado conjunto de parâmetros de consumo seja rejeitado, a mensagem de erro que o acompanha conterá uma explicação textual do (s) motivo (es).
As descrições detalhadas dos parâmetros têm exemplos de valores razoáveis para ambos os modelos.

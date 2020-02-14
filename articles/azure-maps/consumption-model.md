---
title: Modelos de consumo de veículos para encaminhamento Microsoft Azure Maps
description: Neste artigo, você vai aprender sobre modelos de consumo de veículos para encaminhamento no Microsoft Azure Maps.
author: subbarayudukamma
ms.author: skamma
ms.date: 05/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: c1572eddf78ca2d5f8f4e3ee9f1fe47b0d43f5aa
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190255"
---
# <a name="consumption-model"></a>Modelo de consumo

O serviço de encaminhamento fornece um conjunto de parâmetros para uma descrição detalhada do Modelo de Consumo específico do veículo.
Dependendo do valor do **veículoEngineType,** são suportados dois principais modelos de consumo: _Combustão_ e _Elétrico_. É incorreto especificar parâmetros que pertencem a diferentes modelos no mesmo pedido. Além disso, os parâmetros do Modelo de Consumo não podem ser utilizados com os seguintes valores **de viagemMode:** _bicicleta_ e _peão_.

## <a name="parameter-constraints-for-consumption-model"></a>Restrições paraparâmetros para o modelo de consumo

Em ambos os modelos de consumo, existem algumas dependências na especificação dos parâmetros. Ou seja, especificar explicitamente alguns parâmetros pode exigir especificar outros parâmetros. Aqui estão estas dependências para estar atento:

* Todos os parâmetros requerem que o **Consumo speed constante** seja especificado pelo utilizador. É um erro especificar qualquer outro parâmetro do modelo de consumo, se não for especificado **o speedconsumption constante.** O parâmetro **do peso do veículo** é uma exceção para este requisito.
* **aceleração A eficiência** e **a desaceleração** devem ser sempre especificadas como um par (isto é, ambos ou nenhum).
* Se for especificada **a aceleraçãoEficiência** e **desaceleração A eficiência,** o produto dos seus valores não deve ser superior a 1 (para evitar o movimento perpétuo).
* **uphillEficiência** e **descidaA eficiência** deve ser sempre especificada como um par (isto é, ambos ou nenhum).
* Se for especificada **a eficiência ascendente** e a **descida,** o produto dos seus valores não deve ser superior a 1 (para evitar o movimento perpétuo).
* Se os parâmetros de __eficiência__ \*forem especificados pelo utilizador, então o **veículoPeso** também deve ser especificado. Quando **o veículoEngineType** é _combustão,_ **fuelEnergyDensitYInMJoulesPerLiter** também deve ser especificado.
* **maxChargeInkWh** e **currentChargeInkWh** devem ser sempre especificados como um par (isto é, ambos ou nenhum).

> [!NOTE]
> Se for especificado apenas **o speedconsumption constante,** nenhum outro aspeto de consumo, como declives e aceleração do veículo, é tomado em consideração para as computações de consumo.

## <a name="combustion-consumption-model"></a>Modelo de consumo de combustão

O Modelo de Consumo de Combustão é utilizado quando **o veículoEngineType** está definido para _a combustão_.
A lista de parâmetros que pertencem a este modelo está abaixo. Consulte a secção Parâmetros para uma descrição detalhada.

* constantSpeedConsumptionInLitersPerHundredkm
* veículoPeso
* actualFuelInLiters
* auxiliaryPowerInLitersPerHour
* fuelEnergyDensityInMJoulesPerLiter
* aceleraçãoEficiência
* desaceleraçãoEficiência
* uphillEficiência
* descidaEficiência

## <a name="electric-consumption-model"></a>Modelo de consumo elétrico

O Modelo de Consumo Elétrico é utilizado quando o **veículoEngineType** está definido para _elétrico_.
A lista de parâmetros que pertencem a este modelo está abaixo. Consulte a secção Parâmetros para uma descrição detalhada.

* constantSpeedConsumptionInkWhPerHundredkm
* veículoPeso
* currentChargeInkWh
* maxChargeInkWh
* auxiliaryPowerInkW
* aceleraçãoEficiência
* desaceleraçãoEficiência
* uphillEficiência
* descidaEficiência

## <a name="sensible-values-of-consumption-parameters"></a>Valores sensatos dos parâmetros de consumo

Um conjunto específico de parâmetros de consumo pode ser rejeitado, mesmo que o conjunto possa satisfazer todos os requisitos explícitos. Acontece quando o valor de um parâmetro específico, ou uma combinação de valores de vários parâmetros, é considerado como conduzindo a magnitudes despropositadas dos valores de consumo. Se isso acontecer, provavelmente indica um erro de entrada, uma vez que é tomado o devido cuidado para acomodar todos os valores sensatos dos parâmetros de consumo. No caso de um determinado conjunto de parâmetros de consumo ser rejeitado, a mensagem de erro que o acompanha conterá uma explicação textual da(s) razão.
As descrições detalhadas dos parâmetros têm exemplos de valores sensatos para ambos os modelos.

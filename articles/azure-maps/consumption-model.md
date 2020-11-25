---
title: Modelos de consumo de veículos para encaminhamento Microsoft Azure Maps
description: 'Conheça os modelos de consumo que a Azure Maps suporta: combustão e elétrico. Veja quais os parâmetros que cada modelo utiliza e veja as restrições dos parâmetros.'
author: subbarayudukamma
ms.author: skamma
ms.date: 05/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: b44186d783a249192a8c13ee97063034ee319df7
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013144"
---
# <a name="consumption-model"></a>Modelo de consumo

O serviço de encaminhamento fornece um conjunto de parâmetros para uma descrição detalhada do modelo de consumo específico do veículo.
Dependendo do valor do **veículoEngineType,** são suportados dois modelos principais de consumo: _Combustão_ e _Elétrica_. É incorreto especificar parâmetros que pertencem a diferentes modelos no mesmo pedido. Além disso, os parâmetros do Modelo de Consumo não podem ser utilizados com os seguintes valores **de viagemMode:** _bicicleta_ e _pedestre._

## <a name="parameter-constraints-for-consumption-model"></a>Restrições de parâmetros para o modelo de consumo

Em ambos os Modelos de Consumo, existem algumas dependências ao especificar parâmetros. O que significa que, especificando explicitamente alguns parâmetros pode exigir especificar alguns outros parâmetros. Aqui estão estas dependências para estar ciente de:

* Todos os parâmetros requerem que a **temperatura constante** seja especificada pelo utilizador. É um erro especificar qualquer outro parâmetro do modelo de consumo, se não for especificado o **consumo constante.** O parâmetro **peso do veículo** é uma exceção para este requisito.
* **aceleração A eficácia** e **a desaceleração A eficácia** deve ser sempre especificada como um par (isto é, ambos ou nenhum).
* Se forem especificadas **aceleraçõesEficiência** e **desaceleração A eficácia,** o produto dos seus valores não deve ser superior a 1 (para evitar movimentos perpétuos).
* **uphillEficiência** e **downhillEficiência** deve ser sempre especificado como um par (isto é, ambos ou nenhum).
* Se for especificada **a eficácia** e **a descida,** o produto dos seus valores não deve ser superior a 1 (para evitar movimentos perpétuos).
* Se os \* parâmetros __de eficiência__ forem especificados pelo utilizador, então o peso **do veículo** também deve ser especificado. Quando **o veículoEngineType** estiver _a combustão,_ **o fuelEnergyDensityInMJoulesPerLiter** também deve ser especificado.
* **maxChargeInkWh** e **actualChargeInkWh** devem ser sempre especificados como um par (isto é, ambos ou nenhum).

> [!NOTE]
> Se **apenas forem especificados constantesSpeedConsução,** nenhum outro aspeto de consumo, como declives e aceleração do veículo, são tidos em conta para os cálculos de consumo.

## <a name="combustion-consumption-model"></a>Modelo de consumo de combustão

O Modelo de Consumo de Combustão é utilizado quando **o veículoEngineType** está definido para _a combustão_.
A lista de parâmetros que pertencem a este modelo estão abaixo. Consulte a secção parâmetros para obter uma descrição detalhada.

* constantSpeedConsumptionInLitersPerHundredkm
* peso do veículo
* actualfuelInLiters
* auxiliarPowerInLitersPerHour
* fuelEnergyDensityInMJoulesPerLiter
* aceleraçãoEficiência
* desaceleraçãoEficiência
* uphillEficiência
* downhillEficiência

## <a name="electric-consumption-model"></a>Modelo de consumo elétrico

O Modelo de Consumo Elétrico é utilizado quando **o veículoEngineType** está definido para _elétrico_.
A lista de parâmetros que pertencem a este modelo estão abaixo. Consulte a secção parâmetros para obter uma descrição detalhada.

* constantSpeedConsumptionInkWhPerHundredkm
* peso do veículo
* actualChargeInkWh
* maxChargeInkWh
* AuxiliarPowerInkW
* aceleraçãoEficiência
* desaceleraçãoEficiência
* uphillEficiência
* downhillEficiência

## <a name="sensible-values-of-consumption-parameters"></a>Valores sensatos dos parâmetros de consumo

Um determinado conjunto de parâmetros de consumo pode ser rejeitado, mesmo que o conjunto possa satisfazer todos os requisitos explícitos. Acontece quando o valor de um parâmetro específico, ou uma combinação de valores de vários parâmetros, é considerado como conduzindo a magnitudes irrazoáveis dos valores de consumo. Se isso acontecer, é provável que indique um erro de entrada, uma vez que é tomado o devido cuidado para acomodar todos os valores sensatos dos parâmetros de consumo. Caso um determinado conjunto de parâmetros de consumo seja rejeitado, a mensagem de erro que o acompanha conterá uma explicação textual da(s).
As descrições detalhadas dos parâmetros têm exemplos de valores sensatos para ambos os modelos.

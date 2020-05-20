---
title: Configurar regras e ações no Azure IoT Central | Microsoft Docs
description: Este artigo de como fazer mostra-lhe, como construtor, como configurar regras e ações baseadas em telemetria na sua aplicação Azure IoT Central.
author: vavilla
ms.author: vavilla
ms.date: 11/27/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: c4d0639831d2f6f60a719637c5158fba5caf6f43
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83659345"
---
# <a name="configure-rules"></a>Configurar regras

*Este artigo aplica-se a operadores, construtores e administradores.*

As regras da IoT Central servem como uma ferramenta de resposta personalizável que desencadeia eventos monitorizados ativamente a partir de dispositivos conectados. As seguintes secções descrevem como as regras são avaliadas.

## <a name="select-target-devices"></a>Selecione dispositivos-alvo

Utilize a secção de dispositivos-alvo para selecionar em que tipo de dispositivos esta regra será aplicada. Os filtros permitem-lhe aperfeiçoar ainda mais os dispositivos que devem ser incluídos. Os filtros utilizam propriedades no modelo do dispositivo para filtrar o conjunto de dispositivos. Os filtros em si não desencadeiam uma ação. Na seguinte imagem, os dispositivos que estão a ser visados são do tipo de modelo do dispositivo **Frigorífico**. O filtro estabelece que a regra só deve incluir **frigoríficos** onde a propriedade **do Estado Manufaturado** é igual a **Washington.**

![Condições](media/howto-configure-rules/filters.png)

## <a name="use-multiple-conditions"></a>Use várias condições

As condições são as regras que desencadeiam. Atualmente, quando se adicionam múltiplas condições a uma regra, são logicamente e juntas. Por outras palavras, devem ser reunidas todas as condições para que a regra avalie como verdadeira.  

Na seguinte imagem, as condições verificam quando a temperatura é superior a 70 &deg; F e a humidade é inferior a 10. Quando ambas as declarações são verdadeiras, a regra avalia a verdade e desencadeia uma ação.

![Condições](media/howto-configure-rules/conditions.png)

### <a name="use-a-cloud-property-in-a-value-field"></a>Use uma propriedade em nuvem em um campo de valor

Pode fazer referência a uma propriedade em nuvem a partir do modelo do dispositivo no campo **Valor** para uma condição. A propriedade em nuvem e o valor da telemetria devem ter tipos semelhantes. Por exemplo, se a **temperatura** for dupla, então apenas as propriedades em nuvem do tipo duplo mostram como opções no **drop-down valor.**

Se escolher um valor de telemetria tipo evento, o **valor** de entrega inclui a opção **Qualquer**. A **opção Qualquer** opção significa que a regra dispara quando a sua aplicação recebe um evento desse tipo, qualquer que seja a carga útil.

## <a name="use-aggregate-windowing"></a>Use janelas agregadas

As regras avaliam janelas de tempo agregadas como janelas caindo. Na imagem abaixo, a janela do tempo é de cinco minutos. A cada cinco minutos, a regra avalia os últimos cinco minutos de dados. Os dados só são avaliados uma vez na janela à qual correspondem.

![Janelas caindo](media/howto-configure-rules/tumbling-window.png)

## <a name="use-rules-with-iot-edge-modules"></a>Utilize regras com módulos IoT Edge

Uma restrição aplica-se às regras que são aplicadas aos módulos IoT Edge. As regras sobre telemetria de diferentes módulos não são avaliadas como regras válidas. Tome o seguinte como um exemplo. O primeiro estado da regra está numa telemetria de temperatura do Módulo A. A segunda condição da regra está numa telemetria de humidade no módulo B. Uma vez que as duas condições são de diferentes módulos, este é um conjunto de condições inválidas. A regra não é válida e vai lançar um erro ao tentar salvar a regra.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a configurar uma regra na sua aplicação Azure IoT Central, pode aprender a [configurar regras avançadas](howto-configure-rules-advanced.md) usando aplicações power automate ou Azure Logic.

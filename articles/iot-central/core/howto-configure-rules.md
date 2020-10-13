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
ms.openlocfilehash: 8391202e991c240b03bd79b947c1c3c6dcc9afd7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91362542"
---
# <a name="configure-rules"></a>Configurar regras

*Este artigo aplica-se a operadores, construtores e administradores.*

As regras da IoT Central servem como uma ferramenta de resposta personalizável que desencadeia eventos monitorizados ativamente a partir de dispositivos conectados. As seguintes secções descrevem como as regras são avaliadas.

## <a name="select-target-devices"></a>Selecione dispositivos-alvo

Utilize a secção de dispositivos-alvo para selecionar em que tipo de dispositivos esta regra será aplicada. Os filtros permitem-lhe aperfeiçoar ainda mais os dispositivos que devem ser incluídos. Os filtros utilizam propriedades no modelo do dispositivo para filtrar o conjunto de dispositivos. Os filtros em si não despoletam uma ação. Na imagem seguinte, os dispositivos que estão a ser alvo são do tipo de modelo do **dispositivo Frigorífico**. O filtro estabelece que a regra só deve incluir **frigoríficos** onde a propriedade **do Estado Manufaturado** seja igual a **Washington.**

![Condições](media/howto-configure-rules/filters.png)

## <a name="use-multiple-conditions"></a>Use várias condições

As condições são o que as regras desencadeiam. Atualmente, quando se adicionam múltiplas condições a uma regra, são logicamente e juntam-se. Por outras palavras, todas as condições devem ser reunidas para que a regra avalie como verdadeira.  

Na imagem seguinte, as condições verificam quando a temperatura é superior a 70 &deg; F e a humidade é inferior a 10. Quando ambas as declarações são verdadeiras, a regra avalia a verdade e despoleta uma ação.

![A imagem mostra um monitor de frigorífico com condições especificadas para temperatura e humidade.](media/howto-configure-rules/conditions.png)

### <a name="use-a-cloud-property-in-a-value-field"></a>Use uma propriedade em nuvem num campo de valor

Pode fazer referência a uma propriedade em nuvem a partir do modelo do dispositivo no campo **Valor** para uma condição. A propriedade em nuvem e o valor da telemetria devem ter tipos semelhantes. Por exemplo, se **a temperatura** for dupla, então apenas as propriedades em nuvem do tipo duplo mostram como opções no **drop-down** value.

Se escolher um valor de telemetria tipo evento, a descida de **valor** inclui a opção **Any**. A **opção Qualquer** significa que a regra dispara quando a sua aplicação recebe um evento desse tipo, qualquer que seja a carga útil.

## <a name="use-aggregate-windowing"></a>Use a janela agregada

As regras avaliam as janelas de tempo agregadas como janelas caindo. Na imagem abaixo, a janela de tempo é de cinco minutos. A cada cinco minutos, a regra avalia os últimos cinco minutos de dados. Os dados só são avaliados uma vez na janela a que corresponde.

![Janelas caindo](media/howto-configure-rules/tumbling-window.png)

## <a name="use-rules-with-iot-edge-modules"></a>Use regras com módulos IoT Edge

Uma restrição aplica-se às regras que são aplicadas aos módulos IoT Edge. As regras sobre telemetria de diferentes módulos não são avaliadas como regras válidas. Tome o seguinte como um exemplo. A primeira condição da regra é uma telemetria de temperatura do módulo A. A segunda condição da regra é sobre uma telemetria de humidade no módulo B. Uma vez que as duas condições são de diferentes módulos, este é um conjunto de condições inválidas. A regra não é válida e vai lançar um erro ao tentar salvar a regra.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a configurar uma regra na sua aplicação Azure IoT Central, pode aprender a [configurar regras avançadas usando Power](howto-configure-rules-advanced.md) Automamate ou Azure Logic Apps.

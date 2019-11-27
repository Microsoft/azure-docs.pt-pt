---
title: Configurar regras e ações no Azure IoT Central | Microsoft Docs
description: Este artigo de instruções mostra, como um construtor, como configurar regras e ações baseadas em telemetria no aplicativo IoT Central do Azure.
author: vavilla
ms.author: vavilla
ms.date: 11/11/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 76a9d8302d53d50bc3c1e306e31fbb26ed230436
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484915"
---
# <a name="configure-rules-preview-features"></a>Configurar regras (recursos de visualização)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

*Este artigo aplica-se a operadores, construtores e administradores.*

As regras no IoT Central servem como uma ferramenta de resposta personalizável que dispara eventos ativamente monitorados de dispositivos conectados. As seções a seguir descrevem como as regras são avaliadas.

## <a name="select-target-devices"></a>Selecionar dispositivos de destino

Use a seção dispositivos de destino para selecionar em que tipo de dispositivos essa regra será aplicada. Os filtros permitem refinar ainda mais quais dispositivos devem ser incluídos. Os filtros usam propriedades no modelo de dispositivo para filtrar o conjunto de dispositivos. Os próprios filtros não disparam uma ação. Na captura de tela a seguir, os dispositivos que estão sendo direcionados são do tipo de modelo de dispositivo **refrigerator**. O filtro indica que a regra deve incluir apenas **refrigeradores** em que a propriedade de **estado fabricada** seja igual a **Washington**.

![Condições](media/howto-configure-rules/filters.png)

## <a name="use-multiple-conditions"></a>Usar várias condições

As condições são as regras disparadas. Atualmente, quando você adiciona várias condições a uma regra, elas são logicamente e juntas. Em outras palavras, todas as condições devem ser atendidas para que a regra seja avaliada como verdadeira.  

Na captura de tela a seguir, as condições verificam quando a temperatura é maior que 90 e a umidade é menor que 10. Quando ambas as instruções são true, a regra é avaliada como true e dispara uma ação.

![Condições](media/howto-configure-rules/conditions.png)

## <a name="use-aggregate-windowing"></a>Usar janelas agregadas

As regras avaliam as janelas de tempo agregado como janelas em cascata. Na captura de tela abaixo, a janela de tempo é de cinco minutos. A cada cinco minutos, a regra é avaliada nos últimos cinco minutos de dados. Os dados são avaliados apenas uma vez na janela à qual ele corresponde.

![Janelas em cascata](media/howto-configure-rules/tumbling-window.png)

## <a name="use-rules-with-iot-edge-modules"></a>Usar regras com módulos IoT Edge

Uma restrição se aplica às regras que são aplicadas aos módulos IoT Edge. As regras na telemetria de diferentes módulos não são avaliadas como regras válidas. Veja o seguinte como exemplo. A primeira condição da regra está em uma telemetria de temperatura do módulo A. A segunda condição da regra está em uma telemetria de umidade no módulo B. Como as duas condições são de módulos diferentes, esse é um conjunto inválido de condições. A regra não é válida e gerará um erro ao tentar salvar a regra.

## <a name="next-steps"></a>Passos Seguintes

Agora que você aprendeu como configurar uma regra em seu aplicativo de IoT Central do Azure, você pode:

> [!div class="nextstepaction"]
> [Analise seus dados em tempo real](howto-create-analytics.md)

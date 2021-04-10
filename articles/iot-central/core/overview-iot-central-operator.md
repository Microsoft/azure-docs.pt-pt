---
title: Guia do operador central Azure IoT
description: O Azure IoT Central é uma plataforma de aplicações IoT que simplifica a criação de soluções de IoT. Este artigo fornece uma visão geral do papel do operador na IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 03/19/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: b8692973f187743e282de6f8e54a55363cc3105b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104669948"
---
# <a name="iot-central-operator-guide"></a>Guia do operador central IoT

*Este artigo aplica-se aos operadores.*

Uma aplicação IoT Central permite monitorizar e gerir milhões de dispositivos ao longo do seu ciclo de vida. Este guia destina-se aos operadores que utilizam uma aplicação IoT Central para gerir dispositivos IoT.

Um operador:

- Monitoriza e gere os dispositivos ligados à aplicação.
- Resoluções de problemas e remedia problemas com dispositivos.
- Fornece novos dispositivos.

## <a name="monitor-and-manage-devices"></a>Monitorizar e gerir dispositivos

:::image type="content" source="media/overview-iot-central-operator/simulated-telemetry.png" alt-text="Screenshot que mostra uma vista do dispositivo":::

Para monitorizar os dispositivos, um operador pode utilizar as vistas do dispositivo definidas pelo construtor de soluções como parte do modelo do dispositivo. Estas vistas podem mostrar telemetria do dispositivo e valores de propriedade. Um exemplo é a **vista geral** mostrada na imagem anterior.

Para obter informações mais detalhadas, um operador pode utilizar grupos de dispositivos e as funcionalidades de análise incorporadas. Para saber mais, consulte [Como utilizar a análise para analisar dados do dispositivo.](howto-create-analytics.md)

Para gerir dispositivos individuais, um operador pode usar as vistas do dispositivo para definir propriedades do dispositivo e da nuvem e chamar os comandos do dispositivo. Exemplos, incluem as vistas do **dispositivo Manage** e **Commands** na imagem anterior.

Para gerir dispositivos a granel, um operador pode criar e agendar postos de trabalho. Os trabalhos podem atualizar propriedades e executar comandos em vários dispositivos. Para saber mais, consulte [Criar e executar um trabalho na sua aplicação Azure IoT Central](howto-run-a-job.md).

## <a name="troubleshoot-and-remediate-issues"></a>Resolução de problemas e problemas de reparação

O operador é responsável pela saúde da aplicação e dos seus dispositivos. O [guia de resolução de problemas](troubleshoot-connection.md) ajuda os operadores a diagnosticar e a remediar questões comuns. Um operador pode utilizar a página **dispositivos** para bloquear dispositivos que parecem estar avariados até que o problema seja resolvido.

## <a name="add-and-remove-devices"></a>Adicione e remova dispositivos

O operador pode adicionar e remover dispositivos à sua aplicação IoT Central individualmente ou a granel. Para saber mais, consulte [Gerir os dispositivos na sua aplicação Azure IoT Central](howto-manage-devices.md).

## <a name="personalize"></a>Personalizar

Os operadores podem criar dashboards personalizados numa aplicação IoT Central que contenha ligações com os recursos que mais usam. Para saber mais, consulte [gerir os dashboards.](howto-create-personal-dashboards.md#manage-dashboards)

## <a name="next-steps"></a>Passos seguintes

Se quiser saber mais sobre a utilização da IoT Central, os próximos passos sugeridos são experimentar os quickstarts, começando com [a configuração de uma aplicação Azure IoT Central](./quick-deploy-iot-central.md).

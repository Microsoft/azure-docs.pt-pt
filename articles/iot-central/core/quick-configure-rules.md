---
title: Quickstart - Configurar regras e ações em Azure IoT Central
description: Este quickstart mostra-lhe, como construtor, como configurar regras e ações baseadas em telemetria na sua aplicação Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: f65614de97e8ff6eed732e624ae30c3f2b70bd60
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589011"
---
# <a name="quickstart-configure-rules-and-actions-for-your-device-in-azure-iot-central"></a>Quickstart: Configurar regras e ações para o seu dispositivo no Azure IoT Central

Neste arranque rápido, cria-se uma regra que envia um e-mail quando a humidade reportada por um sensor de dispositivos ultrapassa os 55%.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, deverá completar os dois quickstarts anteriores [Criar uma aplicação Azure IoT Central](./quick-deploy-iot-central.md) e adicionar um dispositivo [simulado à sua aplicação IoT Central](./quick-create-simulated-device.md) para criar o modelo de dispositivo do Controlador de **Sensor** para trabalhar.

## <a name="create-a-telemetry-based-rule"></a>Criar uma regra baseada em telemetria

1. Para adicionar uma nova regra baseada em telemetria à sua aplicação, no painel esquerdo, selecione **Regras**.

1. Para criar uma nova regra, selecione **+ Novo**.

1. **Insira a humidade ambiental** como o nome de regra.

1. Na secção **dispositivos Target,** selecione **o Controlador de Sensores** como modelo do dispositivo. Esta opção filtra os dispositivos a que a regra se aplica pelo tipo de modelo do dispositivo. Pode adicionar mais critérios de filtro selecionando **+ Filtro**.

1. Na secção **Condições,** define o que desencadeia a sua regra. Utilize as seguintes informações para definir uma condição baseada na telemetria de temperatura:

    | Campo        | Valor            |
    | ------------ | ---------------- |
    | Medida  | SensorHumid      |
    | Operador     | é maior que  |
    | Valor        | 55               |

    Para adicionar mais condições, selecione **+ Condição**.

    :::image type="content" source="media/quick-configure-rules/condition.png" alt-text="Screenshot mostrando a condição de regra":::

1. Para adicionar uma ação de e-mail para executar quando a regra disparar, selecione **+ E-mail**.

1. Utilize as informações na tabela seguinte para definir a sua ação e, em seguida, selecione **Fazer**:

    | Definição   | Valor                                             |
    | --------- | ------------------------------------------------- |
    | Nome a apresentar | Ação de e-mail do operador                          |
    | Para        | O seu endereço de e-mail                                |
    | Notas     | A humidade ambiental ultrapassou o limiar. |

    > [!NOTE]
    > Para receber uma notificação por e-mail, o endereço de e-mail deve ser um [ID de utilizador na aplicação](howto-administer.md), e esse utilizador deve ter sessão iniciada na aplicação, pelo menos, uma vez.

    :::image type="content" source="media/quick-configure-rules/action.png" alt-text="Screenshot que mostra uma ação de e-mail adicionada à regra":::

1. Selecione **Guardar**. A regra é apresentada na página **Regras**.

## <a name="test-the-rule"></a>Testar a regra

Pouco tempo depois de guardar a regra, esta é ativada. Quando as condições definidas na regra são cumpridas, a sua candidatura envia um e-mail para o endereço especificado na ação.

> [!NOTE]
> Depois de o seu teste estar concluído, desligue a regra para deixar de receber alertas na sua caixa de entrada.

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, aprendeu a:

* Criar uma regra baseada em telemetria
* Adicionar uma ação

Para saber mais sobre dispositivos de monitorização ligados à sua aplicação, continue a iniciar rapidamente:

> [!div class="nextstepaction"]
> [Utilize a Azure IoT Central para monitorizar os seus dispositivos](quick-monitor-devices.md).

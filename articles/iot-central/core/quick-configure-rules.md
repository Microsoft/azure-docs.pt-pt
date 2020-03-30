---
title: Quickstart - Configure regras e ações na Central Azure IoT
description: Este quickstart mostra-lhe, como construtor, como configurar regras e ações baseadas em telemetria na sua aplicação Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 02/12/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 4bbf571d6b73a6f43c1c3b1ce261da6963a74183
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "77169483"
---
# <a name="quickstart-configure-rules-and-actions-for-your-device-in-azure-iot-central"></a>Quickstart: Configure regras e ações para o seu dispositivo em Azure IoT Central

*Este artigo aplica-se a operadores, construtores e administradores.*

Neste arranque rápido, cria-se uma regra que envia um e-mail&deg; quando a temperatura reportada por um sensor de dispositivo supõe que seja superior a 90 F.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, deve completar os dois quickstarts anteriores [Criar uma aplicação Central Azure IoT](./quick-deploy-iot-central.md) e [adicionar um dispositivo simulado à sua aplicação IoT Central](./quick-create-pnp-device.md) para criar o modelo de dispositivo **MXChip IoT DevKit** para trabalhar.

## <a name="create-a-telemetry-based-rule"></a>Criar uma regra baseada em telemetria

1. Para adicionar uma nova regra baseada na telemetria à sua aplicação, no painel esquerdo, selecione **Regras**.

1. Para criar uma nova **+** regra, selecione .

1. Introduza a **temperatura ambiental** como o nome da regra.

1. Na secção **de dispositivos Target,** selecione **MXChip IoT DevKit** como modelo de dispositivo. Esta opção filtra os dispositivos a que a regra se aplica pelo modelo do dispositivo. Pode adicionar mais critérios de filtro selecionando **+ Filtro**.

1. Na secção **Condições,** define o que desencadeia a sua regra. Utilize as seguintes informações para definir uma condição baseada na telemetria da temperatura:

    | Campo        | Valor            |
    | ------------ | ---------------- |
    | Medida  | Temperatura      |
    | Operador     | é maior que  |
    | Valor        | 90               |

    Para adicionar mais condições, selecione **+ Condição**.

    ![Criar condição de regra](./media/quick-configure-rules/condition.png)

1. Para adicionar uma ação de e-mail para executar quando a regra dispara, **selecione + E-mail**.

1. Utilize as informações na tabela a seguir para definir a sua ação e, em seguida, selecione **Done:**

    | Definição   | Valor                                             |
    | --------- | ------------------------------------------------- |
    | Nome a apresentar | Ação de e-mail do operador                          |
    | Para        | O seu endereço de e-mail                                |
    | Notas     | A temperatura ambiental excedeu o limiar. |

    > [!NOTE]
    > Para receber uma notificação por e-mail, o endereço de e-mail deve ser um [ID de utilizador na aplicação](howto-administer.md), e esse utilizador deve ter sessão iniciada na aplicação, pelo menos, uma vez.

    ![Criar ação de regras](./media/quick-configure-rules/action.png)

1. Selecione **Guardar**. A sua regra está listada na página do **Regimento.**

## <a name="test-the-rule"></a>Testar a regra

Pouco tempo depois de guardar a regra, esta é ativada. Quando forem cumpridas as condições definidas na regra, a aplicação envia uma mensagem para o endereço de e-mail que especificou na ação.

> [!NOTE]
> Depois de concluído o teste, desligue a regra para deixar de receber alertas na sua caixa de entrada.

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, aprendeu a:

* Criar uma regra baseada em telemetria
* Adicionar uma ação

Para saber mais sobre dispositivos de monitorização ligados à sua aplicação, continue a iniciar rapidamente:

> [!div class="nextstepaction"]
> Utilize a [Central Azure IoT para monitorizar os seus dispositivos](quick-monitor-devices.md).

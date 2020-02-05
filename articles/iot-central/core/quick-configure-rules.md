---
title: Início rápido-configurar regras e ações no Azure IoT Central
description: Este guia de início rápido mostra você, como um construtor, como configurar regras e ações baseadas em telemetria no aplicativo IoT Central do Azure.
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: a63e77fb3c50d4387c27912336d65f9f84d2d5c9
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77027805"
---
# <a name="quickstart-configure-rules-and-actions-for-your-device-in-azure-iot-central"></a>Quickstart: Configure regras e ações para o seu dispositivo em Azure IoT Central

*Este artigo aplica-se a operadores, construtores e administradores.*

Neste arranque rápido, cria-se uma regra que envia um e-mail quando a temperatura reportada por um sensor de dispositivo salta para 90&deg; F.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, deve completar os dois quickstarts anteriores [Criar uma aplicação Central Azure IoT](./quick-deploy-iot-central.md) e [adicionar um dispositivo simulado à sua aplicação IoT Central](./quick-create-pnp-device.md) para criar o modelo de dispositivo **MXChip IoT DevKit** para trabalhar.

## <a name="create-a-telemetry-based-rule"></a>Criar uma regra baseada em telemetria

1. Para adicionar uma nova regra baseada em telemetria ao seu aplicativo, no painel esquerdo, selecione **regras**.

1. Para criar uma nova regra, selecione **+** .

1. Insira **temperatura ambiental** como o nome da regra.

1. Na secção **de dispositivos Target,** selecione **MXChip IoT DevKit** como modelo de dispositivo. Essa opção filtra os dispositivos aos quais a regra se aplica por tipo de modelo de dispositivo. Pode adicionar mais critérios de filtragem selecionando **+ Filtro**.

1. Na seção **condições** , você define o que dispara sua regra. Use as informações a seguir para definir uma condição com base na telemetria de temperatura:

    | Campo        | Valor            |
    | ------------ | ---------------- |
    | Medida  | Temperatura      |
    | Operador     | é maior do que  |
    | Valor        | 90               |

    Para adicionar mais condições, selecione **+ condição**.

    ![Criar condição de regra](./media/quick-configure-rules/condition.png)

1. Para adicionar uma ação de email a ser executada quando a regra for disparada, selecione **+ email**.

1. Utilize as informações na tabela a seguir para definir a sua ação e, em seguida, selecione **Done:**

    | Definição   | Valor                                             |
    | --------- | ------------------------------------------------- |
    | Nome a apresentar | Ação de email do operador                          |
    | a        | O seu endereço de e-mail                                |
    | Notas     | A temperatura ambiental excedeu o limite. |

    > [!NOTE]
    > Para receber uma notificação por e-mail, o endereço de e-mail deve ser um [ID de utilizador na aplicação](howto-administer.md), e esse utilizador deve ter sessão iniciada na aplicação, pelo menos, uma vez.

    ![Criar ação de regra](./media/quick-configure-rules/action.png)

1. Selecione **Guardar**. A sua regra está listada na página do **Regimento.**

## <a name="test-the-rule"></a>Testar a regra

Pouco tempo depois de guardar a regra, esta é ativada. Quando forem cumpridas as condições definidas na regra, a aplicação envia uma mensagem para o endereço de e-mail que especificou na ação.

> [!NOTE]
> Após a conclusão do teste, desative a regra para parar de receber alertas em sua caixa de entrada.

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, aprendeu a:

* Criar uma regra baseada em telemetria
* Adicionar uma ação

Para saber mais sobre como monitorar dispositivos conectados ao seu aplicativo, vá para o início rápido:

> [!div class="nextstepaction"]
> [Use o IOT central do Azure para monitorar seus dispositivos](quick-monitor-devices.md).

---
title: 'Tutorial: configurar regras e ações no Azure IoT Central'
description: Este tutorial mostra-lhe, como construtor, como configurar regras baseadas em telemetria e ações na sua aplicação Azure IoT Central.
author: ankitscribbles
ms.author: ankitgup
ms.date: 11/13/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 9140114e7d31f24770bdcce9aae849b01aae9996
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76263661"
---
# <a name="tutorial-configure-rules-and-actions-for-your-device-in-azure-iot-central"></a>Tutorial: Configurar regras e ações para o seu dispositivo no Azure IoT Central

*Este artigo aplica-se a operadores, construtores e administradores.*

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Neste tutorial, você cria uma regra que envia um email quando a temperatura em um dispositivo de ar condicionado conectado excede 70&deg; F.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Criar uma regra baseada em telemetria
> * Adicionar uma ação

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, deve concluir o tutorial [Definir um novo tipo de dispositivo na sua aplicação](tutorial-define-device-type.md) para criar o modelo de dispositivo **Dispositivo de Ar Condicionado Ligado** com que irá trabalhar.

## <a name="create-a-telemetry-based-rule"></a>Criar uma regra baseada em telemetria

1. Para adicionar uma nova regra baseada em telemetria ao seu aplicativo, no painel esquerdo, selecione **modelos de dispositivo**:

    ![Página modelos de dispositivo](media/tutorial-configure-rules/templatespage1.png)

    Você verá o modelo de dispositivo da **1.0.0 (ar condicionador conectado)** criado no tutorial anterior.

2. Para personalizar o modelo de dispositivo, selecione o modelo **conectado do ar-condicionado** criado no tutorial anterior.

3. Para adicionar uma regra baseada em telemetria na exibição **regras** , selecione **regras**, selecione **+ nova regra**e, em seguida, selecione **telemetria**:

    ![Vista de regras](media/tutorial-configure-rules/newrule.png)

4. Para definir a sua regra, utilize as informações na tabela seguinte:

    | Definição                                      | Valor                             |
    | -------------------------------------------- | ------------------------------    |
    | Nome                                         | Alerta de temperatura do ar condicionado |
    | Ativar regra para todos os dispositivos deste modelo | Ativado                                |
    | Condição                                    | A temperatura é maior que 70    |
    | Agregação                                  | Nenhuma                              |

    ![Condição da regra de temperatura](media/tutorial-configure-rules/temperaturerule.png)

    Em seguida, selecione **Guardar**.

## <a name="add-an-action"></a>Adicionar uma ação

Quando definir uma regra, também pode definir uma ação a executar quando são satisfeitas as condições de regra. Neste tutorial, você cria uma regra com uma ação que envia uma notificação por email.

1. Para adicionar uma **ação**, primeiro **salve** a regra e, em seguida, role para baixo no painel **Configurar regra de telemetria** . Escolha o **+** ao lado de **ações**e, em seguida, escolha **email**:

    ![Ação de regra de temperatura](media/tutorial-configure-rules/addaction.png)

2. Para definir a sua ação, utilize as informações na tabela seguinte:

    | Definição      | Valor                                               |
    | ------------ | --------------------------------------------------- |
    | Nome a apresentar | Alerta de temperatura de email                             |
    | a           | O seu endereço de e-mail                                  |
    | Notas        | A temperatura do ar condicionado excedeu o limite. |

    > [!NOTE]
    > Para receber uma notificação por e-mail, o endereço de e-mail deve ser um [ID de utilizador na aplicação](howto-administer.md), e esse utilizador deve ter sessão iniciada na aplicação, pelo menos, uma vez.

    ![Ação de temperatura](media/tutorial-configure-rules/temperatureaction.png)

3. Selecione **Guardar**. A regra é apresentada na página **Regras**.

## <a name="test-the-rule"></a>Testar a regra

Pouco tempo depois de guardar a regra, esta é ativada. Quando forem cumpridas as condições definidas na regra, a aplicação envia uma mensagem para o endereço de e-mail que especificou na ação.

> [!NOTE]
> Após a conclusão do teste, desative a regra para parar de receber alertas em sua caixa de entrada.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
>
> * Criar uma regra baseada em telemetria
> * Adicionar uma ação

Agora que você definiu uma regra baseada em limite, a próxima etapa sugerida é [Personalizar as exibições do operador](tutorial-customize-operator.md).

Para saber mais sobre os diferentes tipos de regras no Azure IoT Central e como parametrizar a definição da regra, consulte:

* [Criar uma regra de telemetria e configurar as notificações](howto-create-telemetry-rules.md).
* [Criar uma regra de eventos e configurar as notificações](howto-create-event-rules.md).

<!-- Next tutorials in the sequence -->
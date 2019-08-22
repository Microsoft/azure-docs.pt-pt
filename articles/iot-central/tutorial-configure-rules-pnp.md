---
title: Configurar regras e ações no Azure IoT Central | Microsoft Docs
description: Este tutorial mostra-lhe, como construtor, como configurar regras baseadas em telemetria e ações na sua aplicação Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 08/06/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: c2aa6edfe7ce9b670ea1015e2da72f3ecc48c9ec
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878153"
---
# <a name="tutorial-configure-rules-and-actions-for-your-device-in-azure-iot-central-preview-features"></a>Tutorial: Configurar regras e ações para seu dispositivo no Azure IoT Central (recursos de visualização)

*Este artigo aplica-se a operadores, construtores e administradores.*

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Neste tutorial, você cria uma regra que envia um email quando a temperatura em um dispositivo de sensor ambiental excede 90&deg; F.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma regra baseada em telemetria
> * Adicionar uma ação

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, você deve concluir o tutorial [definir um novo tipo de dispositivo no aplicativo](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) para criar o modelo de dispositivo de **sensor de ambiente** com o qual trabalhar.

## <a name="create-a-telemetry-based-rule"></a>Criar uma regra baseada em telemetria

1. Para adicionar uma nova regra baseada em telemetria ao seu aplicativo, no menu de navegação à esquerda, selecione **regras**.

1. Para criar uma nova regra, selecione **+ novo**. Em seguida, escolha telemetria.

1. Insira **temperatura ambiental** como o nome da regra.

1. Na seção **escopo** , selecione o **sensor de ambiente** como o modelo de dispositivo. O escopo ao qual os dispositivos esta regra se aplica. Você pode adicionar mais critérios de filtro usando **+ filtro**.

1. Na seção **condição** , você define o que dispara a regra. Use as informações a seguir para definir uma condição com base na telemetria de temperatura:

    | Campo                                        | Value                             |
    | -------------------------------------------- | ------------------------------    |
    | Medida                                  | Temperatura                       |
    | Operator                                     | é maior que                   |
    | Value                                        | 90                                |

    Para adicionar mais condições, selecione **+ condição**.

    ![Criar condição de regra](./media/tutorial-configure-rules-pnp/condition.png)

1. Para adicionar uma ação a ser executada quando a regra for disparada, selecione **+ ação**e escolha **email**.

1. Use as informações na tabela a seguir para definir sua ação:

    | Definição   | Value                                             |
    | --------- | ------------------------------------------------- |
    | Para        | O seu endereço de correio eletrónico                                |
    | Notas     | A temperatura ambiental excedeu o limite. |

    > [!NOTE]
    > Para receber uma notificação por e-mail, o endereço de e-mail deve ser um [ID de utilizador na aplicação](howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json), e esse utilizador deve ter sessão iniciada na aplicação, pelo menos, uma vez.

    ![Criar ação de regra](./media/tutorial-configure-rules-pnp/action.png)

1. Selecione **Guardar**. Sua regra está listada na página **regras** .

## <a name="test-the-rule"></a>Testar a regra

Pouco tempo depois de guardar a regra, esta é ativada. Quando forem cumpridas as condições definidas na regra, a aplicação envia uma mensagem para o endereço de e-mail que especificou na ação.

> [!NOTE]
> Após a conclusão do teste, desative a regra para parar de receber alertas em sua caixa de entrada.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

* Criar uma regra baseada em telemetria
* Adicionar uma ação

Agora que você definiu uma regra baseada em limite, a próxima etapa sugerida é:

> [!div class="nextstepaction"]
> [Criar uma regra de eventos e configurar as notificações](howto-create-event-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

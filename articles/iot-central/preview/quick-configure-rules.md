---
title: Configurar regras e ações no Azure IoT Central | Microsoft Docs
description: Este guia de início rápido mostra você, como um construtor, como configurar regras e ações baseadas em telemetria no aplicativo IoT Central do Azure.
author: dominicbetts
ms.author: dobett
ms.date: 10/24/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 7e2047ee824c3dc8b6387f7879757a1e7be7a4a9
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73894076"
---
# <a name="quickstart-configure-rules-and-actions-for-your-device-in-azure-iot-central-preview-features"></a>Início rápido: configurar regras e ações para seu dispositivo no Azure IoT Central (recursos de visualização)

*Este artigo aplica-se a operadores, construtores e administradores.*

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Neste guia de início rápido, você cria uma regra que envia um email quando a temperatura em um dispositivo do sensor ambiental excede 90&deg; F.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, você deve concluir os dois guias de início rápido anteriores [criar um aplicativo de IOT central do Azure](./quick-deploy-iot-central.md) e [Adicionar um dispositivo simulado ao seu aplicativo de IOT central](./quick-create-pnp-device.md) para criar o modelo de dispositivo de **sensor de ambiente** com o qual trabalhar.

## <a name="create-a-telemetry-based-rule"></a>Criar uma regra baseada em telemetria

1. Para adicionar uma nova regra baseada em telemetria ao seu aplicativo, no painel esquerdo, selecione **regras**.

1. Para criar uma nova regra, selecione **+ novo**.

1. Insira **temperatura ambiental** como o nome da regra.

1. Na seção **dispositivos de destino** , selecione o **sensor de ambiente** como o modelo de dispositivo. Essa opção filtra os dispositivos aos quais a regra se aplica por tipo de modelo de dispositivo. Você pode adicionar mais critérios de filtro escolhendo **+ filtro**.

1. Na seção **condições** , você define o que dispara sua regra. Use as informações a seguir para definir uma condição com base na telemetria de temperatura:

    | Campo                                        | Valor                             |
    | -------------------------------------------- | ------------------------------    |
    | Medida                                  | Temperatura                       |
    | Operador                                     | é maior que                   |
    | Valor                                        | 90                                |

    Para adicionar mais condições, selecione **+ condição**.

    ![Criar condição de regra](./media/quick-configure-rules/condition.png)

1. Para adicionar uma ação de email a ser executada quando a regra for disparada, selecione **+ email**.

1. Use as informações na tabela a seguir para definir sua ação:

    | Definição   | Valor                                             |
    | --------- | ------------------------------------------------- |
    | Nome a apresentar | Ação de email do operador                          |
    | Para        | O seu endereço de e-mail                                |
    | Notas     | A temperatura ambiental excedeu o limite. |

    > [!NOTE]
    > Para receber uma notificação por e-mail, o endereço de e-mail deve ser um [ID de utilizador na aplicação](howto-administer.md), e esse utilizador deve ter sessão iniciada na aplicação, pelo menos, uma vez.

    ![Criar ação de regra](./media/quick-configure-rules/action.png)

1. Selecione **Guardar**. Sua regra está listada na página **regras** .

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

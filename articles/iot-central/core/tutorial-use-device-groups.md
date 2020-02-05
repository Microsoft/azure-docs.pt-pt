---
title: Utilize grupos de dispositivos na sua aplicação Azure IoT Central / Microsoft Docs
description: Como operador, aprenda a utilizar grupos de dispositivos para analisar a telemetria a partir de dispositivos na sua aplicação Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: 4fd05631e7f54b6258978f70fdd5dfb9705f989b
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77026968"
---
# <a name="tutorial-use-device-groups-to-analyze-device-telemetry"></a>Tutorial: Use grupos de dispositivos para analisar a telemetria do dispositivo



Este artigo descreve como, como operador, usar grupos de dispositivos para analisar a telemetria do dispositivo na sua aplicação Azure IoT Central.

Um grupo de dispositivos é uma lista de dispositivos que são agrupados porque correspondem a alguns critérios especificados. Os grupos de dispositivos ajudam-no a gerir, visualizar e analisar dispositivos em escala, agrupando dispositivos em grupos lógicos mais pequenos. Por exemplo, pode criar um grupo de dispositivos para listar todos os dispositivos de ar condicionado em Seattle para permitir que um técnico encontre os dispositivos pelos quais são responsáveis.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um grupo de dispositivos
> * USe um grupo de dispositivos para analisar telemetria de dispositivos

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, você deve concluir a [criação de um aplicativo de IOT central do Azure](./quick-deploy-iot-central.md) e [Adicionar um dispositivo simulado aos seus](./quick-create-pnp-device.md) guias de início rápido do aplicativo IOT central para criar o modelo de dispositivo do **sensor de ambiente** com o qual trabalhar.

## <a name="create-simulated-devices"></a>Criar dispositivos simulados

Antes de criar um grupo de dispositivos, adicione pelo menos cinco dispositivos simulados do modelo do dispositivo do **Sensor** ambiental para utilizar neste tutorial:

![Cinco dispositivos de sensores ambientais simulados](./media/tutorial-use-device-groups/simulated-devices.png)

Para quatro dos dispositivos de sensor ambiental, utilize a visão do **Sensor Ambiental** para definir o nome do cliente para **Contoso:**

![Definir o nome do cliente para Contoso](./media/tutorial-use-device-groups/customer-name.png)

## <a name="create-a-device-group"></a>Criar um grupo de dispositivos

Para criar um grupo de dispositivos:

1. Escolha grupos de **dispositivos** no painel esquerdo.

1. Selecione **+ Novo**.

    ![Novo grupo de dispositivos](media/tutorial-use-device-groups/image1.png)

1. Dê ao seu grupo de dispositivos um nome como **dispositivos Contoso**. Também pode adicionar uma descrição. Um grupo de dispositivos só pode conter dispositivos a partir de um único modelo de dispositivo. Escolha o modelo do dispositivo **Sensor Ambiental** para utilizar para este grupo.

1. Crie a consulta para identificar os dispositivos pertencentes a **Contoso** para o grupo de dispositivos, selecionando a propriedade **Nome do Cliente,** o operador de comparação **Equals,** e **Contoso** como o valor. Pode adicionar múltiplas consultas e dispositivos que satisfaçam **todos os** critérios colocados no grupo do dispositivo. O grupo de dispositivos que cria é acessível a qualquer pessoa que tenha acesso à aplicação, para que qualquer pessoa possa visualizar, modificar ou eliminar o grupo do dispositivo.

    ![Consulta de grupo de dispositivos](media/tutorial-use-device-groups/image2.png)

    > [!NOTE]
    > O grupo de dispositivos é uma consulta dinâmica. Sempre que vê a lista de dispositivos, pode haver diferentes dispositivos na lista. A lista depende dos dispositivos que atualmente satisfazem os critérios da consulta.

1. Escolha **Guardar**.

> [!NOTE]
> Para dispositivos Azure IoT Edge, selecione modelos Azure IoT Edge para criar um grupo de dispositivos.

## <a name="analytics"></a>Análise

Pode utilizar o **Analytics** com um grupo de dispositivos para analisar a telemetria dos dispositivos do grupo. Por exemplo, pode traçar a temperatura média reportada por todos os sensores ambientais contoso.

Para analisar a telemetria para um grupo de dispositivos:

1. Escolha **o Analytics** no painel esquerdo.

1. Selecione o grupo de **dispositivos Contoso** que criou. Em seguida, adicione os tipos de telemetria **temperatura** e **humidade:**

    ![Criar análises](./media/tutorial-use-device-groups/create-analysis.png)

    Utilize os ícones da roda de engrenagem ao lado dos tipos de telemetria para selecionar um tipo de agregação. O padrão é **Médio**. Use **Split por para** alterar a forma como os dados agregados são mostrados. Por exemplo, se dividir por id do dispositivo, verá um enredo para cada dispositivo quando selecionar **Analyze**.

1. Selecione **Analisar** para ver os valores médios de telemetria:

    ![Ver análise](./media/tutorial-use-device-groups/view-analysis.png)

    Pode personalizar a vista, alterar o período de tempo mostrado e exportar os dados.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a usar grupos de dispositivos na sua aplicação Azure IoT Central, eis o próximo passo sugerido:

> [!div class="nextstepaction"]
> [Como criar regras de telemetria](tutorial-create-telemetry-rules.md)

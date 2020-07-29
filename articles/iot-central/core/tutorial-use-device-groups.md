---
title: Utilize grupos de dispositivos na sua aplicação Azure IoT Central / Microsoft Docs
description: Como operador, aprenda a utilizar grupos de dispositivos para analisar a telemetria a partir de dispositivos na sua aplicação Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 02/12/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: 58fc71ab05c34e8acd252e7a1984c55996d1b3a7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80999044"
---
# <a name="tutorial-use-device-groups-to-analyze-device-telemetry"></a>Tutorial: Utilize grupos de dispositivos para analisar a telemetria do dispositivo

Este artigo descreve como, como operador, utilizar grupos de dispositivos para analisar a telemetria do dispositivo na sua aplicação Azure IoT Central.

Um grupo de dispositivos é uma lista de dispositivos que são agrupados porque correspondem a alguns critérios especificados. Os grupos de dispositivos ajudam-no a gerir, visualizar e analisar dispositivos em escala, agrupar dispositivos em grupos mais pequenos e lógicos. Por exemplo, pode criar um grupo de dispositivos para listar todos os dispositivos de ar condicionado em Seattle para permitir que um técnico encontre os dispositivos pelos quais são responsáveis.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um grupo de dispositivos
> * Utilize um grupo de dispositivos para analisar a telemetria do dispositivo

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, deverá completar a [aplicação Create a Azure IoT Central](./quick-deploy-iot-central.md) e [adicionar um dispositivo simulado à sua aplicação IoT Central](./quick-create-simulated-device.md) para criar o modelo de dispositivo **MXChip IoT DevKit** para trabalhar.

## <a name="create-simulated-devices"></a>Criar dispositivos simulados

Antes de criar um grupo de dispositivos, adicione pelo menos cinco dispositivos simulados do modelo de dispositivo **MXChip IoT DevKit** para utilizar neste tutorial:

![Cinco dispositivos de sensor simulado](./media/tutorial-use-device-groups/simulated-devices.png)

Para quatro dos dispositivos de sensor simulado, utilize a visão do **dispositivo Manage** para definir o nome do cliente para *Contoso:*

![Definir nome do cliente para Contoso](./media/tutorial-use-device-groups/customer-name.png)

## <a name="create-a-device-group"></a>Criar um grupo de dispositivos

Para criar um grupo de dispositivos:

1. Escolha **grupos de dispositivos** no painel esquerdo.

1. **+** Selecione:

    ![Novo grupo de dispositivos](media/tutorial-use-device-groups/image1.png)

1. Dê ao seu grupo de dispositivos o nome *De Dispositivos Contoso*. Também pode adicionar uma descrição. Um grupo de dispositivos só pode conter dispositivos a partir de um único modelo de dispositivo. Escolha o modelo de dispositivo **MXChip IoT DevKit** para utilizar para este grupo.

1. Para personalizar o grupo de dispositivos para incluir apenas os dispositivos pertencentes ao **Contoso,** selecione **+ Filtro**. Selecione a propriedade Nome do **Cliente,** o operador de comparação **Igual** e **Contoso** como o valor. Pode adicionar vários filtros e dispositivos que satisfaçam **todos os** critérios do filtro no grupo do dispositivo. O grupo de dispositivos que cria está acessível a qualquer pessoa que tenha acesso à aplicação, para que qualquer pessoa possa ver, modificar ou eliminar o grupo de dispositivos:

    ![Consulta de grupo de dispositivos](media/tutorial-use-device-groups/image2.png)

    > [!TIP]
    > O grupo de dispositivos é uma consulta dinâmica. Sempre que vê a lista de dispositivos, pode haver diferentes dispositivos na lista. A lista depende dos dispositivos que atualmente satisfazem os critérios da consulta.

1. Escolha **guardar**.

> [!NOTE]
> Para dispositivos Azure IoT Edge, selecione modelos Azure IoT Edge para criar um grupo de dispositivos.

## <a name="analytics"></a>Análise

Pode utilizar **o Analytics** com um grupo de dispositivos para analisar a telemetria dos dispositivos do grupo. Por exemplo, pode traçar a temperatura média reportada por todos os sensores ambientais contoso.

Para analisar a telemetria de um grupo de dispositivos:

1. Escolha **Analytics** no painel esquerdo.

1. Selecione o grupo **de dispositivos Contoso** que criou. Em seguida, adicione os tipos de telemetria **temperatura** e **humidade:**

    ![Criar análises](./media/tutorial-use-device-groups/create-analysis.png)

    Utilize os ícones das rodas de engrenagem ao lado dos tipos de telemetria para selecionar um tipo de agregação. O padrão é **médio.** Utilize **Split por** para alterar a forma como os dados agregados são mostrados. Por exemplo, se dividir por ID do dispositivo, vê um enredo para cada dispositivo quando seleciona **Analisar**.

1. Selecione **Analisar** para ver os valores médios de telemetria:

    ![Ver análise](./media/tutorial-use-device-groups/view-analysis.png)

    Pode personalizar a vista, alterar o período de tempo mostrado e exportar os dados.

## <a name="next-steps"></a>Próximos passos

Agora que aprendeu a usar grupos de dispositivos na sua aplicação Azure IoT Central, aqui está o próximo passo sugerido:

> [!div class="nextstepaction"]
> [Como criar regras de telemetria](tutorial-create-telemetry-rules.md)

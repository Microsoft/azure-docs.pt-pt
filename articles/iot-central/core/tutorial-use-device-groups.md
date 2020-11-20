---
title: Tutorial - Use grupos de dispositivos na sua aplicação Azure IoT Central / Microsoft Docs
description: Tutorial - Como operador, aprenda a usar grupos de dispositivos para analisar a telemetria a partir de dispositivos na sua aplicação Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 11/16/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 8c26afc9cf9630f6d26ddc76759393a6ea1a8696
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94990303"
---
# <a name="tutorial-use-device-groups-to-analyze-device-telemetry"></a>Tutorial: Utilize grupos de dispositivos para analisar a telemetria do dispositivo

Este artigo descreve como, como operador, utilizar grupos de dispositivos para analisar a telemetria do dispositivo na sua aplicação Azure IoT Central.

Um grupo de dispositivos é uma lista de dispositivos que são agrupados porque correspondem a alguns critérios especificados. Os grupos de dispositivos ajudam-no a gerir, visualizar e analisar dispositivos em escala, agrupar dispositivos em grupos mais pequenos e lógicos. Por exemplo, pode criar um grupo de dispositivos para listar todos os dispositivos de ar condicionado em Seattle para permitir que um técnico encontre os dispositivos pelos quais são responsáveis.

Neste tutorial, vai aprender a:

> [!div class="checklist"]
> * Criar um grupo de dispositivos
> * Utilize um grupo de dispositivos para analisar a telemetria do dispositivo

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, deverá completar a [aplicação Create a Azure IoT Central](./quick-deploy-iot-central.md) e [adicionar um dispositivo simulado à sua aplicação IoT Central](./quick-create-simulated-device.md) para criar o modelo de dispositivo do Controlador de **Sensor** para trabalhar.

## <a name="create-simulated-devices"></a>Criar dispositivos simulados

Antes de criar um grupo de dispositivos, adicione pelo menos cinco dispositivos simulados baseados no modelo do dispositivo **do Controlador de Sensores** para utilizar neste tutorial:


:::image type="content" source="media/tutorial-use-device-groups/simulated-devices.png" alt-text="Screenshot mostrando cinco dispositivos simulados de controlador de sensores":::

Para quatro dos dispositivos de sensor simulado, utilize a visão do **dispositivo Manage** para definir o nome do cliente para *Contoso:*

:::image type="content" source="media/tutorial-use-device-groups/customer-name.png" alt-text="Screenshot que mostra como definir a propriedade cloud do Nome do Cliente":::

## <a name="create-a-device-group"></a>Criar um grupo de dispositivos

Para criar um grupo de dispositivos:

1. Escolha **grupos de dispositivos** no painel esquerdo.

1. Selecione **+ Novo**.

1. Nomeie os dispositivos do seu grupo *de dispositivos Contoso*. Também pode adicionar uma descrição. Um grupo de dispositivos só pode conter dispositivos a partir de um único modelo de dispositivo. Escolha o modelo do dispositivo **do controlador de sensor** para usar para este grupo.

1. Para personalizar o grupo de dispositivos para incluir apenas os dispositivos pertencentes ao **Contoso,** selecione **+ Filtro**. Selecione a propriedade Nome do **Cliente,** o operador de comparação **Igual** e **Contoso** como o valor. Pode adicionar vários filtros e dispositivos que satisfaçam **todos os** critérios do filtro no grupo do dispositivo. O grupo de dispositivos que cria está acessível a qualquer pessoa que tenha acesso à aplicação, para que qualquer pessoa possa ver, modificar ou eliminar o grupo de dispositivos.

    > [!TIP]
    > O grupo de dispositivos é uma consulta dinâmica. Sempre que vê a lista de dispositivos, pode haver diferentes dispositivos na lista. A lista depende dos dispositivos que atualmente satisfazem os critérios da consulta.

1. Escolha **guardar**.

:::image type="content" source="media/tutorial-use-device-groups/device-group-query.png" alt-text="Screenshot que mostra a configuração de consulta do grupo do dispositivo":::

> [!NOTE]
> Para dispositivos Azure IoT Edge, selecione modelos Azure IoT Edge para criar um grupo de dispositivos.

## <a name="analytics"></a>Análise

Pode utilizar **o Analytics** com um grupo de dispositivos para analisar a telemetria dos dispositivos do grupo. Por exemplo, pode traçar a temperatura média reportada por todos os sensores ambientais contoso.

Para analisar a telemetria de um grupo de dispositivos:

1. Escolha **Analytics** no painel esquerdo.

1. Selecione o grupo **de dispositivos Contoso** que criou. Em seguida, adicione os tipos de telemetria **temperatura** e **humidade:**

    :::image type="content" source="media/tutorial-use-device-groups/create-analysis.png" alt-text="Screenshot que mostra os tipos de telemetria selecionados para análise":::

    Utilize os ícones das rodas de engrenagem ao lado dos tipos de telemetria para selecionar um tipo de agregação. O padrão é **médio.** Utilize **o Grupo para** alterar a forma como os dados agregados são mostrados. Por exemplo, se dividir por ID do dispositivo, vê um enredo para cada dispositivo quando seleciona **Analisar**.

1. Selecione **Analisar** para ver os valores médios de telemetria:

    :::image type="content" source="media/tutorial-use-device-groups/view-analysis.png" alt-text="Screenshot que mostra valores médios para todos os dispositivos Contoso":::

    Pode personalizar a vista, alterar o período de tempo mostrado e exportar os dados.

## <a name="next-steps"></a>Próximos passos

Agora que aprendeu a usar grupos de dispositivos na sua aplicação Azure IoT Central, aqui está o próximo passo sugerido:

> [!div class="nextstepaction"]
> [Como criar regras de telemetria](tutorial-create-telemetry-rules.md)

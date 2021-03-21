---
title: incluir ficheiro
description: incluir ficheiro
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 03/12/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 28f676892967abbd0da63d7a75ea3d164b87ce97
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96017517"
---
Como operador na sua aplicação Azure IoT Central, pode:

* Ver a telemetria enviada pelo dispositivo na página **'Vista Geral':**

    :::image type="content" source="media/iot-central-monitor-thermostat/view-telemetry.png" alt-text="Ver a telemetria do dispositivo":::

* Ver as propriedades do dispositivo na página **Sobre:**

    :::image type="content" source="media/iot-central-monitor-thermostat/about-properties.png" alt-text="Ver propriedades do dispositivo":::

## <a name="customize-the-device-template"></a>Personalize o modelo do dispositivo

Como desenvolvedor de soluções, pode personalizar o modelo de dispositivo que a IoT Central criou automaticamente quando o dispositivo do termóstato está ligado.

Para adicionar uma propriedade em nuvem para armazenar o nome do cliente associado ao dispositivo:

1. Na sua aplicação IoT Central, navegue para o modelo do dispositivo **termóstato** na página **de modelos** do dispositivo.

1. No modelo do dispositivo **termóstato,** selecione **propriedades cloud**.

1. **Selecione Adicionar propriedade em nuvem**. Introduza *o nome do Cliente* como nome do **Visor** e escolha **String** como **o Esquema**. Em seguida, selecione **Guardar**.

Para personalizar como o comando **'Obter Max-Min reporte'** na sua aplicação IoT Central, selecione **Personalizar** no modelo do dispositivo. Substitua **o relatório Get Max-Min.** com *o relatório de estado da Get*. Em seguida, selecione **Guardar**.

O modelo **termóstato** inclui a propriedade de escrita **Temperatura Alvo,** o modelo do dispositivo inclui a propriedade cloud **Nome do Cliente.** Criar uma vista que um operador pode usar para editar estas propriedades:

1. Selecione **Vistas** e, em seguida, selecione o dispositivo de edição e o azulejo **de dados em nuvem.**

1. _Insira propriedades_ como o nome do formulário.

1. Selecione as propriedades **'Temperatura alvo'** e **nome do cliente.** Em seguida, **selecione Secção adicionar**.

1. Guarde as alterações.

:::image type="content" source="media/iot-central-monitor-thermostat/properties-view.png" alt-text="Ver para atualizar valores de propriedade":::

## <a name="publish-the-device-template"></a>Publicar o modelo de dispositivo

Antes que um operador possa ver e utilizar as personalizações que fez, tem de publicar o modelo do dispositivo.

A partir do modelo do dispositivo **do termóstato,** selecione **Publicar**. No modelo publicar este dispositivo para o painel **de aplicações,** selecione **Publicar**.

Um operador pode agora usar a vista **Propriedades** para atualizar os valores da propriedade, e chamar um relatório de estado chamado **Get report** na página de comandos do dispositivo:

* Atualizar valores de propriedade writeable na página **Propriedades:**

    :::image type="content" source="media/iot-central-monitor-thermostat/update-properties.png" alt-text="Atualizar as propriedades do dispositivo":::

* Ligue para os comandos da página **Comandos:**

    :::image type="content" source="media/iot-central-monitor-thermostat/call-command.png" alt-text="Chame o comando":::

    :::image type="content" source="media/iot-central-monitor-thermostat/command-response.png" alt-text="Ver a resposta do comando":::

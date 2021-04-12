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
ms.openlocfilehash: 77fdaf297fff0e145b1dd53908887bc14f9d3f14
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491135"
---
<!-- All needs updating -->
Como operador na sua aplicação Azure IoT Central, pode:

* Ver a telemetria enviada pelos dois componentes do termóstato na página **'Vista Geral':**

    :::image type="content" source="media/iot-central-monitor-thermostat/view-telemetry.png" alt-text="Ver a telemetria do dispositivo":::

* Veja as propriedades do dispositivo na página **Sobre.** Esta página mostra as propriedades do componente de informação do dispositivo e os dois componentes do termóstato:

    :::image type="content" source="media/iot-central-monitor-thermostat/about-properties.png" alt-text="Ver propriedades do dispositivo":::

## <a name="customize-the-device-template"></a>Personalize o modelo do dispositivo

Como desenvolvedor de soluções, pode personalizar o modelo de dispositivo que a IoT Central criou automaticamente quando o dispositivo do controlador de temperatura está ligado.

Para adicionar uma propriedade em nuvem para armazenar o nome do cliente associado ao dispositivo:

1. Na sua aplicação IoT Central, navegue para o modelo do dispositivo **do controlador de temperatura** na página de modelos do **dispositivo.**

1. No modelo do dispositivo **do controlador de temperatura,** selecione **propriedades cloud**.

1. **Selecione Adicionar propriedade em nuvem**. Introduza *o nome do Cliente* como nome do **Visor** e escolha **String** como **o Esquema**. Em seguida, selecione **Guardar**.

Para personalizar como os comandos **de relatório Get Max-Min** exibem na sua aplicação IoT Central:

1. **Selecione Personalizar** no modelo do dispositivo.

1. Para **obter O Relatório DeMaxMin (termóstato1),** substitua o relatório Get *Max-Min.* com *o relatório de estado do termóstato1*.

1. Para **obter O Relatório DeMaxMin (termóstato2),** substitua o relatório Get *Max-Min.* com *o relatório do estado do termóstato2*.

1. Selecione **Guardar**.

Para personalizar como as propriedades writable **temperatura alvo** exibem na sua aplicação IoT Central:

1. **Selecione Personalizar** no modelo do dispositivo.

1. Para **a temperatura-alvo (termóstato1),** substitua *a temperatura-alvo* pela *temperatura-alvo (1)*.

1. Para **a temperatura-alvo (termóstato2),** substitua *a temperatura-alvo* pela *temperatura-alvo (2)*.

1. Selecione **Guardar**.

Os componentes do termóstato no modelo **do Controlador de Temperatura** incluem a propriedade de classificação da Temperatura **Alvo,** o modelo do dispositivo inclui a propriedade cloud **do Nome do Cliente.** Criar uma vista que um operador pode usar para editar estas propriedades:

1. Selecione **Vistas** e, em seguida, selecione o dispositivo de edição e o azulejo **de dados em nuvem.**

1. _Insira propriedades_ como o nome do formulário.

1. Selecione as propriedades **'Temperatura-Alvo' (1)**, **Temperatura-Alvo (2)** e **Nome do Cliente.** Em seguida, **selecione Secção adicionar**.

1. Guarde as alterações.

:::image type="content" source="media/iot-central-monitor-thermostat/properties-view.png" alt-text="Ver para atualizar valores de propriedade":::

## <a name="publish-the-device-template"></a>Publicar o modelo de dispositivo

Antes que um operador possa ver e utilizar as personalizações que fez, tem de publicar o modelo do dispositivo.

A partir do modelo do dispositivo **do termóstato,** selecione **Publicar**. No modelo publicar este dispositivo para o painel **de aplicações,** selecione **Publicar**.

Um operador pode agora usar a visto **Propriedades** para atualizar os valores de propriedade, e chamar os comandos chamados **Get thermostat1 status report** e Obter o relatório de estado do **termóstato2** na página de comandos do dispositivo:

* Atualizar valores de propriedade writeable na página **Propriedades:**

    :::image type="content" source="media/iot-central-monitor-thermostat/update-properties.png" alt-text="Atualizar as propriedades do dispositivo":::

* Ligue para os comandos da página **Comandos:**

    :::image type="content" source="media/iot-central-monitor-thermostat/call-command.png" alt-text="Chame o comando":::

    :::image type="content" source="media/iot-central-monitor-thermostat/command-response.png" alt-text="Ver a resposta do comando":::

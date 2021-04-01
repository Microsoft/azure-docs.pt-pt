---
title: Ligue um dispositivo Azure Sphere na Azure IoT Central | Microsoft Docs
description: Saiba como ligar um dispositivo Azure Sphere (DevKit) a uma aplicação Azure IoT Central.
services: iot-central
ms.service: iot-central
ms.topic: how-to
ms.author: sandeepu
author: sandeeppujar
ms.date: 04/30/2020
ms.custom: device-developer
ms.openlocfilehash: 770f6e56a669ab2d9b425a7a2879eeef5d37377b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92123428"
---
# <a name="connect-an-azure-sphere-device-to-your-azure-iot-central-application"></a>Ligar um dispositivo do Azure Sphere à aplicação do Azure IoT Central

*Este artigo aplica-se aos desenvolvedores de dispositivos.*

Este artigo mostra-lhe como ligar um dispositivo Azure Sphere (DevKit) a uma aplicação Azure IoT Central.

O Azure Sphere é uma plataforma de aplicações segura e de elevado nível com funcionalidades de comunicação e segurança integradas para dispositivos ligados à Internet. Inclui uma unidade de microcontrolador de cruzamento (MCU) segura, conectada e crossover, um sistema operativo personalizado baseado em Linux (OS) e um serviço de segurança baseado na nuvem que fornece segurança contínua e renovável. Para mais informações, veja [o que é Azure Sphere?](/azure-sphere/product-overview/what-is-azure-sphere)

[Os kits de desenvolvimento da Azure Sphere](https://azure.microsoft.com/services/azure-sphere/get-started/) fornecem tudo o que precisa para começar a prototipagem e desenvolver aplicações da Esfera Azure. Azure IoT Central com Esfera Azure permite uma pilha de ponta a ponta para uma Solução IoT. A Azure Sphere fornece o suporte do dispositivo e a IoT Central como uma plataforma de aplicação IoT gerida por código zero.

Neste artigo de como fazer, você:

- Crie um dispositivo Azure Sphere em IoT Central utilizando o modelo de dispositivo Azure Sphere DevKit da biblioteca.
- Prepare o dispositivo Azure Sphere DevKit para Azure IoT.
- Ligue o DevKit da Esfera azul ao Azure IoT Central.
- Veja a telemetria do dispositivo na IoT Central.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir os passos neste artigo, precisa dos seguintes recursos:

- Uma aplicação Azure IoT Central.
- Visual Studio 2019, versão 16.4 ou mais tarde.
- Um [kit de desenvolvimento Azure Sphere MT3620 da Seeed Studios.](/azure-sphere/hardware/mt3620-reference-board-design)

> [!NOTE]
> Se não tiver um dispositivo físico, depois do primeiro passo, salte para a última secção para experimentar um dispositivo simulado.

## <a name="create-the-device-in-iot-central"></a>Crie o dispositivo na IoT Central

Para criar um dispositivo Azure Sphere na IoT Central:

1. Na sua aplicação Azure IoT Central, selecione o **separador Modelos de Dispositivo** e selecione **+ Novo**. Na secção **Utilize um modelo de dispositivo em destaque**, selecione O Dispositivo de Amostra da Esfera **azul**.

    :::image type="content" source="media/howto-connect-sphere/sphere-create-template.png" alt-text="Modelo de dispositivo para Azure Sphere DevKit":::

1. No modelo do dispositivo, edite a vista chamada **Visão Geral** para mostrar **temperatura** e **pressão de botão**.

1. Selecione o **tipo de visualização do dispositivo de edição e da nuvem** para adicionar outra vista que mostre o LED de estado **de** leitura/escrita . Arraste a propriedade **Status LED** para o retângulo vazio e pontilhado no lado direito do formulário. Selecione **Guardar**.

## <a name="prepare-the-device"></a>Prepare o dispositivo

Antes de poder ligar o dispositivo Azure Sphere DevKit à IoT Central, é necessário [configurar o dispositivo e o ambiente de desenvolvimento](https://github.com/Azure/azure-sphere-samples/tree/master/Samples/AzureIoT).

## <a name="connect-the-device"></a>Ligue o dispositivo

Para permitir que a amostra se conecte à IoT Central, deve [configurar uma aplicação Azure IoT Central e, em seguida, modificar o manifesto de aplicação da amostra](https://aka.ms/iotcentral-sphere-git-readme).

## <a name="view-the-telemetry-from-the-device"></a>Ver a telemetria do dispositivo

Quando o dispositivo está ligado à IoT Central, pode ver a telemetria no painel de instrumentos.

:::image type="content" source="media/howto-connect-sphere/sphere-view.png" alt-text="Painel de instrumentos para Azure Sphere DevKit":::

## <a name="create-a-simulated-device"></a>Criar um dispositivo simulado

Se não tiver um dispositivo físico Azure Sphere DevKit, pode criar um dispositivo simulado para experimentar a aplicação Azure IoT Central.

Para criar um dispositivo simulado:

- **Selecione dispositivos > esfera Azure IoT**
- Selecione **+ Novo**.
- Introduza um **ID de dispositivo** único e um nome de **dispositivo** amigável .
- Ativar a **definição simulada.**
- Selecione **Criar**.

## <a name="next-steps"></a>Passos seguintes

Se você é um desenvolvedor de dispositivos, alguns passos sugeridos seguintes são:

- Leia sobre [a conectividade do dispositivo no Azure IoT Central](./concepts-get-connected.md)
- Saiba como monitorizar a [conectividade do dispositivo utilizando o Azure CLI](./howto-monitor-devices-azure-cli.md)
---
title: Ligue um dispositivo Azure Sphere na Central Azure IoT [ Microsoft Docs
description: Saiba como ligar um dispositivo Azure Sphere (DevKit) a uma aplicação Azure IoT Central.
services: iot-central
ms.service: iot-central
ms.topic: how-to
ms.author: sandeepu
author: sandeeppujar
ms.date: 04/30/2020
ms.openlocfilehash: cd583b79d6c73ff8da1c9770bf72b3e6990c3140
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597206"
---
# <a name="connect-an-azure-sphere-device-to-your-azure-iot-central-application"></a>Ligue um dispositivo Azure Sphere à sua aplicação Central Azure IoT

*Este artigo aplica-se aos desenvolvedores de dispositivos.*

Este artigo mostra-lhe como ligar um dispositivo Azure Sphere (DevKit) a uma aplicação Azure IoT Central.

A Esfera Azure é uma plataforma de aplicação segura e de alto nível com funcionalidades de comunicação e segurança incorporadas para dispositivos ligados à Internet. Inclui uma unidade de microcontrolador de crossover (MCU) segura, conectada e crossover (MCU), um sistema operativo baseado em Linux (OS) personalizado, e um serviço de segurança baseado na nuvem que fornece segurança contínua e renovável. Para mais informações, veja[o que é a Esfera Azure?](https://docs.microsoft.com/azure-sphere/product-overview/what-is-azure-sphere)

Os kits de [desenvolvimento da Esfera Azure](https://azure.microsoft.com/services/azure-sphere/get-started/) fornecem tudo o que precisa para começar a prototipagem e desenvolver aplicações da Esfera Azure. A Central Azure IoT com a Esfera Azure permite uma pilha de ponta a ponta para uma solução IoT. A Azure Sphere fornece o suporte do dispositivo e a IoT Central como uma plataforma de aplicação IoT gerida por código zero.

Neste artigo de como fazer, você:

- Crie um dispositivo Azure Sphere na IoT Central utilizando o modelo de dispositivo Azure Sphere DevKit a partir da biblioteca.
- Prepare o dispositivo Azure Sphere DevKit para o Azure IoT.
- Ligue o Azure Sphere DevKit à Central Azure IoT.
- Veja a telemetria do dispositivo na IoT Central.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir os passos neste artigo, precisa dos seguintes recursos:

- Uma aplicação Azure IoT Central.
- Visual Studio 2019, versão 16.4 ou mais tarde.
- Um kit de [desenvolvimento Azure Sphere MT3620 da Seeed Studios.](https://docs.microsoft.com/azure-sphere/hardware/mt3620-reference-board-design)

> [!NOTE]
> Se não tiver um dispositivo físico, depois do primeiro passo salte para a última secção para experimentar um dispositivo simulado.

## <a name="create-the-device-in-iot-central"></a>Crie o dispositivo em IoT Central

Para criar um dispositivo Azure Sphere em IoT Central:

1. Na sua aplicação Azure IoT Central, selecione o separador **Modelos** de Dispositivo e selecione **+ Novo**. Na secção Utilize um modelo de **dispositivo em destaque,** selecione Dispositivo de **amostra de esfera azul**.

    :::image type="content" source="media/howto-connect-sphere/sphere-create-template.png" alt-text="Modelo de dispositivo para DevKit esfera Azure":::

1. No modelo do dispositivo, edite a vista chamada **Visão Geral** para mostrar **Temperatura** e Pressão do **Botão**.

1. Selecione o tipo de visualização de **Dispositivo de Edição e Dados de Nuvem** para adicionar outra vista que mostre o LED de **estado**de leitura/escrita . Arraste a propriedade **LED de Estado** para o retângulo vazio e pontilhado no lado direito do formulário. Selecione **Guardar**.

## <a name="prepare-the-device"></a>Preparar o dispositivo

Antes de poder ligar o dispositivo Azure Sphere DevKit à IoT Central, é necessário [configurar o ambiente de dispositivo e desenvolvimento.](https://github.com/Azure/azure-sphere-samples/tree/master/Samples/AzureIoT)

## <a name="connect-the-device"></a>Ligue o dispositivo

Para permitir que a amostra se coneca à IoT Central, deve [configurar uma aplicação Central Azure IoT e, em seguida, modificar o manifesto de aplicação da amostra](https://aka.ms/iotcentral-sphere-git-readme).

## <a name="view-the-telemetry-from-the-device"></a>Ver a telemetria do dispositivo

Quando o dispositivo está ligado à IoT Central, pode ver a telemetria no painel de instrumentos.

:::image type="content" source="media/howto-connect-sphere/sphere-view.png" alt-text="Painel de instrumentos para Azure Sphere DevKit":::

## <a name="create-a-simulated-device"></a>Criar um dispositivo simulado

Se não tiver um dispositivo físico Azure Sphere DevKit, pode criar um dispositivo simulado para experimentar a aplicação Azure IoT Central.

Para criar um dispositivo simulado:

- Selecione **dispositivos > esfera Azure IoT**
- Selecione **+ Novo**.
- Introduza um ID único **do dispositivo** e um nome de **dispositivo**amigável .
- Ativar a definição **simulada.**
- Selecione **Criar**.

## <a name="next-steps"></a>Passos seguintes

Se é um desenvolvedor de dispositivos, alguns sugeriu que os próximos passos são para:

- Ler sobre [conectividade do dispositivo na Central Azure IoT](./concepts-get-connected.md)
- Saiba como monitorizar a [conectividade do dispositivo utilizando o Azure CLI](./howto-monitor-devices-azure-cli.md)

---
title: Criar uma simulação de IoT nova - Azure | Microsoft Docs
description: Neste tutorial, vai utilizar a Simulação de Dispositivos para criar e executar uma simulação nova.
author: troyhopwood
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: tutorial
ms.custom: mvc
ms.date: 10/25/2018
ms.author: troyhop
ms.openlocfilehash: 268efa35f63ac8b9e0c6ea3f22a71242a2984726
ms.sourcegitcommit: 3dcb1a3993e51963954194ba2a5e42260d0be258
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2018
ms.locfileid: "50756850"
---
# <a name="tutorial-create-and-run-an-iot-device-simulation"></a>Tutorial: Criar e executar uma simulação de dispositivo IoT

Neste tutorial, vai utilizar a Simulação de Dispositivos para criar e executar uma simulação de IoT nova com um ou mais dispositivos simulados.

Neste tutorial, vai:

>[!div class="checklist"]
> * Ver todas as simulações ativas e históricas
> * Criar e executar uma simulação nova
> * Ver métricas relativas a uma simulação
> * Parar uma simulação

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este tutorial, precisa de uma instância implementada da Simulação de Dispositivos na sua subscrição do Azure.

Se ainda não tiver implementado a Simulação de Dispositivos, deve concluir o início rápido [Implementar e executar uma simulação de dispositivo no Azure](quickstart-device-simulation-deploy.md).

## <a name="open-device-simulation"></a>Abrir a Simulação de Dispositivo

Para executar a Simulação de Dispositivos no browser, comece por navegar para [Microsoft Azure IoT Solution Accelerators](https://www.azureiotsolutions.com) (Aceleradores de Soluções do Microsoft Azure IoT). 

Poderá ser-lhe pedido para iniciar sessão com as credenciais da subscrição do Azure.

Em seguida, clique em **Launch** (Iniciar), no mosaico relativo à Simulação de Dispositivos que implementou no [Início Rápido](quickstart-device-simulation-deploy.md).

## <a name="view-simulations"></a>Ver simulações

Selecione **Simulações**, na barra de menus. A página **Simulações** apresenta informações sobre todas as simulações disponíveis. Nessa página, pode ver as simulações que estão atualmente em execução e as que foram executadas anteriormente. Para voltar a executar uma simulação já executada, clique no mosaico da simulação para abrir os detalhes da mesma:

![Simulações](media/iot-accelerators-device-simulation-create-simulation/dashboard.png)

## <a name="create-a-simulation"></a>Criar uma simulação

Para criar uma simulação, clique em **+ New Simulation** (+ Simulação Nova), no canto superior direito.

As simulações consistem em um ou mais modelos de dispositivos. O modelo do dispositivo define o comportamento, a telemetria e o formato das mensagens do dispositivo que vai ser simulado.

Para adicionar um modelo de dispositivo, clique em **+ Add a device type** (+ Adicionar um tipo de dispositivo) e selecione o modelo na lista. A sua simulação pode ter mais de um modelo de dispositivo. Cada modelo pode ter um número de dispositivos e uma frequência de mensagens diferente.

Quando o formulário da simulação nova estiver concluído, clique em **Start Simulation** (Iniciar Simulação) para começar a simulação.

Dependendo do número de dispositivos simulados, a simulação pode demorar alguns minutos a ser configurada e iniciada:

![Simulação nova](media/iot-accelerators-device-simulation-create-simulation/newsimulation.png)

## <a name="stop-a-simulation"></a>Parar uma simulação

Quando clica em **Start Simulation** (Iniciar Simulação), é apresentada a página de detalhes da simulação. Esta página mostra estatísticas e métricas em direto do Hub IoT. Também pode clicar no mosaico da simulação na página **Simulations** (Simulações) para navegar para esta página de detalhes.

Para parar uma simulação, clique em **Stop Simulation** (Parar Simulação), na barra de ações, no canto superior direito.

![Parar simulação](media/iot-accelerators-device-simulation-create-simulation/simulationdetails.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a criar, executar e parar uma simulação. Também aprendeu a ver os detalhes da simulação. Para saber mais sobre como executar simulações, avance para o próximo tutorial:

> [!div class="nextstepaction"]
> [Create a custom simulated device](iot-accelerators-device-simulation-create-custom-device.md) (Criar um dispositivo simulado personalizado)

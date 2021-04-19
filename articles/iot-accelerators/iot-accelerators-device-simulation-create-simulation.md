---
title: Criar uma simulação de IoT nova - Azure | Microsoft Docs
description: Neste tutorial, vai utilizar a Simulação de Dispositivos para criar e executar uma simulação nova.
author: troyhopwood
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/08/2019
ms.author: troyhop
ms.openlocfilehash: df658a95069f39026daab7afb14c5f1d0f47c5a5
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713899"
---
# <a name="tutorial-create-and-run-an-iot-device-simulation"></a>Tutorial: Criar e executar uma simulação de dispositivo IoT

Neste tutorial, vai utilizar a Simulação de Dispositivos para criar e executar uma simulação de IoT nova com um ou mais dispositivos simulados.

Neste tutorial:

>[!div class="checklist"]
> * Ver todas as simulações ativas e históricas
> * Criar e executar uma simulação nova
> * Ver métricas relativas a uma simulação
> * Parar uma simulação

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este tutorial, precisa de uma instância implementada da Simulação de Dispositivos na sua subscrição do Azure.

Se ainda não implementou a Simulação do Dispositivo, consulte a [implementação da simulação do dispositivo](https://github.com/Azure/azure-iot-pcs-device-simulation/blob/master/README.md) no GitHub.

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
> [Criar um dispositivo simulado personalizado](iot-accelerators-device-simulation-create-custom-device.md)

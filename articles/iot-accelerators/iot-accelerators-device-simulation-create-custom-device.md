---
title: Criar um dispositivo simulado personalizado - Azure | Microsoft Docs
description: Neste tutorial, vai utilizar a Simulação de Dispositivos para criar um dispositivo simulado personalizado e utilizá-lo em simulações.
author: troyhopwood
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: tutorial
ms.custom: mvc
ms.date: 10/25/2018
ms.author: troyhop
ms.openlocfilehash: 7ddd1aa9865cb4061212ce77b31efc82ad339283
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2020
ms.locfileid: "92165575"
---
# <a name="tutorial-create-a-custom-simulated-device"></a>Tutorial: Criar um dispositivo simulado personalizado

Neste tutorial, vai utilizar a Simulação de Dispositivos para criar um dispositivo simulado personalizado e utilizá-lo em simulações. Para começar a utilizar a Simulação de Dispositivos, pode utilizar um dos dispositivos simulados de exemplo incluídos. Também pode seguir as indicações neste artigo e criar dispositivos simulados personalizados. Para obter mais opções de personalização, veja [Create an advanced device model](iot-accelerators-device-simulation-advanced-device.md) (Criar um modelo de dispositivos avançado).

Neste tutorial:

>[!div class="checklist"]
> * Ver uma lista dos seus modelos de dispositivos simulados
> * Criar um dispositivo simulado personalizado
> * Clonar um modelo de dispositivo
> * Eliminar um modelo de dispositivo

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este tutorial, precisa de uma instância implementada da Simulação de Dispositivos na sua subscrição do Azure.

Se ainda não tiver implementado a Simulação de Dispositivos, deve concluir o início rápido [Implementar e executar uma simulação de dispositivo no Azure](quickstart-device-simulation-deploy.md).

## <a name="open-device-simulation"></a>Abrir a Simulação de Dispositivo

Para executar a Simulação de Dispositivos no browser, comece por navegar para [Microsoft Azure IoT Solution Accelerators](https://www.azureiotsolutions.com) (Aceleradores de Soluções do Microsoft Azure IoT).

Poderá ser-lhe pedido para iniciar sessão com as credenciais da subscrição do Azure.

Em seguida, clique em **Launch** (Iniciar), no mosaico relativo à Simulação de Dispositivos que implementou no início rápido [Implementar e executar uma simulação de dispositivos IoT no Azure](quickstart-device-simulation-deploy.md).

## <a name="view-your-device-models"></a>Ver os modelos de dispositivos

Selecione **Device models** (Modelos de dispositivos), na barra de menus. A página **Device models** (Modelos de dispositivos) mostra todos os modelos disponíveis nesta instância da Simulação de Dispositivos:

![Modelos de dispositivos](media/iot-accelerators-device-simulation-create-custom-device/devicemodelnav.png)

## <a name="create-a-device-model"></a>Criar um modelo de dispositivo

Clique em **+ Add Device Models** (+ Adicionar Modelos de Dispositivos), no canto superior direito da página:

![Adicionar modelo de dispositivo](media/iot-accelerators-device-simulation-create-custom-device/devicemodels.png)

Neste tutorial, vai criar um frigorífico simulado que envia dados de temperatura e humidade.

Preencha o formulário com as informações seguintes:

| Definição             | Valor                                                |
| ------------------- | ---------------------------------------------------- |
| Nome do modelo de dispositivo   | Frigorífico                                         |
| Descrição do modelo   | Frigorífico com sensores de temperatura e humidade |
| Versão             | 1.0                                                  |

> [!NOTE]
> O nome do modelo de dispositivo tem de ser exclusivo.

Clique em **+ Add data point** (+ Adicionar ponto de dados) para adicionar pontos de dados de temperatura e humidade com os valores seguintes:

| Ponto de Dados          | Comportamento        | Valor Mínimo | Valor Máximo | Unidade |
| ------------------- | --------------- | --------- | --------- | ---- |
| Temperatura         | Aleatório          | -50       | 100       | F    |
| Humidade            | Aleatório          | 0         | 100       | %    |

Clique em **Guardar** para guardar o modelo de dispositivo.

![Criar o modelo de dispositivo](media/iot-accelerators-device-simulation-create-custom-device/adddevicemodel.png)

O frigorífico está agora incluído na lista de modelos de dispositivos. Poderá ter de clicar em **Next** (Seguinte) para aceder a outra página para ver o frigorífico.

## <a name="clone-a-device-model"></a>Clonar um modelo de dispositivo

A clonagem de um modelo de dispositivo permite-lhe criar uma cópia de um modelo já existente. Depois, pode editar a cópia de modo a que satisfaça as suas necessidades específicas. Se tiver de criar modelos de dispositivos semelhantes, a clonagem ajuda-o a poupar tempo.

Para clonar um modelo de dispositivo, selecione a caixa junto ao modelo e clique em **Clone** (Clonar), na barra de ações:

![Screenshot que destaca o modelo selecionado e o botão Clone.](media/iot-accelerators-device-simulation-create-custom-device/clonedevice.png)

## <a name="delete-a-device-model"></a>Eliminar um modelo de dispositivo

Pode eliminar qualquer modelo de dispositivo personalizado. Para eliminar um modelo de dispositivo, selecione a caixa junto ao modelo e clique em **Delete** (Eliminar), na barra de ações:

![Eliminar modelo de dispositivo](media/iot-accelerators-device-simulation-create-custom-device/deletedevice.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a criar, clonar e eliminar modelos de dispositivos personalizados. Para obter mais informações sobre os modelos de dispositivos, veja o artigo de instruções abaixo:

> [!div class="nextstepaction"]
> [Create an advanced device model](iot-accelerators-device-simulation-advanced-device.md) (Criar um modelo de dispositivo avançado)
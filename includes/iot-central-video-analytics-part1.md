---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/06/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 7246375468b3419c3d52ee3d5a51a95aa20050a9
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91877204"
---
A aplicação da amostra inclui dois dispositivos simulados e um gateway IoT Edge. Os seguintes tutoriais mostram duas abordagens para experimentar e compreender as capacidades do portal:

* Crie o gateway IoT Edge num Azure VM e ligue uma câmara simulada.
* Crie o gateway IoT Edge num dispositivo real, como um Intel NUC e conecte uma câmara real.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Utilize o modelo de aplicação de análise de vídeo Azure IoT Central para criar uma aplicação de loja de retalho
> * Personalize as definições de aplicação
> * Crie um modelo de dispositivo para um dispositivo de gateway IoT Edge
> * Adicione um dispositivo de gateway à sua aplicação IoT Central

## <a name="prerequisites"></a>Pré-requisitos

Para completar esta série tutorial, precisa:

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, pode criar uma na página de inscrição do [Azure](https://aka.ms/createazuresubscription).
* Se estiver a utilizar uma câmara real, precisa de conectividade entre o dispositivo IoT Edge e a câmara, e precisa do canal **de Protocolo de Streaming em Tempo Real.**

## <a name="initial-setup"></a>Configuração inicial

Nestes tutoriais, atualiza-se e utiliza vários ficheiros de configuração. As versões iniciais destes ficheiros estão disponíveis no repositório [GitHub de gateway LVA.](https://github.com/Azure/live-video-analytics/tree/master/ref-apps/lva-edge-iot-central-gateway) O repositório também inclui um ficheiro de texto de scratchpad para que possa descarregar e utilizar para registar valores de configuração a partir dos serviços que implementa.

Crie uma pasta chamada *configuração de Lva* na sua máquina local para guardar cópias destes ficheiros. Em seguida, clique com o botão direito em cada um dos seguintes links e escolha **Guardar como** guardar o ficheiro na pasta *de configuração de Iva:*

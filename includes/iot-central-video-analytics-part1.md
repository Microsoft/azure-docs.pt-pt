---
title: incluir ficheiro
description: incluir ficheiro
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/06/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 2f3e4bf640b8da31a7fa4d818b94b0372d3026b8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96763440"
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

Nestes tutoriais, atualiza-se e utiliza vários ficheiros de configuração. As versões iniciais destes ficheiros estão disponíveis no repositório [GitHub de gateway LVA.](https://github.com/Azure/live-video-analytics/tree/master/ref-apps/lva-edge-iot-central-gateway) O repositório inclui um ficheiro de texto [de risco](https://github.com/Azure/live-video-analytics/blob/master/ref-apps/lva-edge-iot-central-gateway/setup/Scratchpad.txt) para que possa descarregar e utilizar para registar valores de configuração a partir dos serviços que implementa. Este ficheiro ajuda-o a completar os passos posteriores nos tutoriais.

Crie uma pasta chamada *configuração de Lva* na sua máquina local para guardar cópias destes ficheiros. Em seguida, clique com o botão direito em cada um dos seguintes links e escolha **Guardar como** guardar o ficheiro na pasta *de configuração de Iva:*

---
title: Conectar seu aplicativo de IoT Central do Azure com os serviços de campo do Dynamics 365 | Microsoft Docs
description: Saiba como criar uma solução de ponta a ponta com o Azure IoT Central e o serviço de campo do Dynamics 365
author: miriambrus
ms.author: miriamb
ms.date: 10/23/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 957e9337bf8ea5941b140ba4f3266417d36df6a7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498765"
---
# <a name="build-end-to-end-solution-with-azure-iot-central-and-dynamics-365-field-service"></a>Crie uma solução de ponta a ponta com o Azure IoT Central e o serviço de campo do Dynamics 365 

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Como um construtor, você pode habilitar a integração do seu aplicativo de IoT Central do Azure a outros sistemas de negócios. 


Por exemplo, em uma solução de gerenciamento de lixo conectada, você pode otimizar o despacho de caminhões de coleções de lixo com base em dados de sensores IoT de compartimentos de lixo conectados. Em seu [IOT central aplicativo de gerenciamento de resíduos conectado](./tutorial-connected-waste-management.md) , você pode configurar regras e ações e defini-lo para disparar a criação de alertas no serviço de campo do Dynamics. Esse cenário é realizado usando Microsoft Flow, que você pode configurar diretamente no IoT Central para automatizar fluxos de trabalho entre aplicativos e serviços. Além disso, com base nas atividades de serviço no serviço de campo, as informações podem ser enviadas de volta para o Azure IoT Central. 

## <a name="how-to-connect-your-azure-iot-central-application-with-dynamics-365-field-services"></a>Como conectar seu aplicativo de IoT Central do Azure com os serviços de campo do Dynamics 365 

Os processos de integração de ponta a ponta abaixo podem ser facilmente implementados com base em uma experiência de configuração pura:
* O Azure IoT Central pode enviar informações sobre anomalias de dispositivo para o serviço de campo conectado (como um alerta de IoT) para diagnóstico.
* O serviço de campo conectado pode criar casos ou ordens de trabalho disparadas de anomalias de dispositivo.
* O serviço de campo conectado pode agendar técnicos para inspeção para evitar incidentes de tempo de inatividade.
* O painel do dispositivo IoT Central do Azure pode ser atualizado com informações relevantes de serviço e agendamento.


## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre os [modelos do governo IOT central](./overview-iot-central-government.md)
* Saiba mais sobre o [IOT central](https://docs.microsoft.com/azure/iot-central/core/overview-iot-central)
* Saiba mais sobre os [serviços de campo do Dynamics 365](https://docs.microsoft.com/dynamics365/field-service/cfs-iot-overview)

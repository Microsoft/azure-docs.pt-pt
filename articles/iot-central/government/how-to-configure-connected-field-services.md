---
title: Ligue a sua aplicação Azure IoT Central com a Dynamics 365 Field Services [ Serviços de Campo] Microsoft Docs
description: Saiba como construir uma solução de ponta a ponta com o Azure IoT Central e o Dynamics 365 Field Service
author: miriambrus
ms.author: miriamb
ms.date: 10/23/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 7c708268eaeade1cfb625c73ff8758a1941ff5b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157454"
---
# <a name="build-end-to-end-solution-with-azure-iot-central-and-dynamics-365-field-service"></a>Construir solução de ponta a ponta com o Azure IoT Central e o Dynamics 365 Field Service 



Como construtor, pode permitir a integração da sua aplicação Azure IoT Central a outros sistemas de negócio. 


Por exemplo, numa solução de gestão de resíduos conectada pode otimizar o despacho de camiões de recolha de lixo. A otimização pode ser feita com base em dados de sensores IoT a partir de caixotes de lixo conectados. Na sua [aplicação de gestão de resíduos conectada IoT Central](./tutorial-connected-waste-management.md) pode configurar regras e ações e defini-la para desencadear a criação de alertas no Dynamics Field Service. Este cenário é realizado utilizando o Microsoft Flow, que será configurado diretamente na IoT Central para automatizar fluxos de trabalho através de aplicações e serviços. Além disso, com base em atividades de serviço no Field Service, a informação pode ser enviada de volta para a Azure IoT Central. 

## <a name="how-to-connect-your-azure-iot-central-application-with-dynamics-365-field-services"></a>Como ligar a sua aplicação Azure IoT Central com a Dynamics 365 Field Services 

Os processos de integração abaixo podem ser facilmente implementados com base numa experiência de configuração pura:
* A Azure IoT Central pode enviar informações sobre anomalias do dispositivo para o Connected Field Service (como alerta IoT) para diagnóstico.
* O Serviço de Campo Conectado pode criar casos ou ordens de trabalho desencadeadas a partir de anomalias do dispositivo.
* O Serviço de Campo Conectado pode agendar técnicos para inspeção para evitar os incidentes de inatividade.
* O painel de instrumentos do dispositivo Azure IoT Central pode ser atualizado com informações de serviço e agendamento relevantes.


## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [os modelos do governo central ioT](./overview-iot-central-government.md)
* Saiba mais sobre [a IoT Central](https://docs.microsoft.com/azure/iot-central/core/overview-iot-central)
* Saiba mais sobre a [Dynamics 365 Field Services](https://docs.microsoft.com/dynamics365/field-service/cfs-iot-overview)

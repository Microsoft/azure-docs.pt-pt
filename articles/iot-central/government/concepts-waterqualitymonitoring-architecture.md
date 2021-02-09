---
title: Arquitetura de referência para a solução de monitorização da qualidade da água construída com Azure IoT Central| Microsoft Docs
description: Aprenda conceitos para uma solução de monitorização da qualidade da água construída com a Azure IoT Central.
author: miriambrus
ms.author: miriamb
ms.date: 12/11/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 3dad4aea56586444bd54ac47c0462232913e173f
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2021
ms.locfileid: "99831608"
---
# <a name="water-quality-monitoring-reference-architecture"></a>Arquitetura de referência da monitorização da qualidade da água 
As soluções de monitorização da qualidade da água podem ser construídas com o modelo de **aplicação Azure IoT Central** como uma aplicação IoT de arranque de pontapé. Este artigo fornece uma orientação de arquitetura de referência de alto nível sobre a construção de uma solução final. 

![Arquitetura de monitorização da qualidade da água](./media/concepts-waterqualitymonitoring-architecture/concepts-waterqualitymonitoring-architecture1.png)

Conceitos:

1. Dispositivos e conectividade  
1. IoT Central 
1. Extensibilidade e integrações
1. Aplicações empresariais

Vamos dar uma olhada nos componentes-chave que geralmente desempenham um papel numa solução de monitorização da qualidade da água.

## <a name="devices-and-connectivity"></a>Dispositivos e conectividade 
Nesta secção, referimo-nos aos dispositivos utilizados na monitorização da qualidade da água ou na monitorização do consumo de água, geralmente como dispositivos de água inteligentes. Os dispositivos de água inteligentes podem ser medidores de fluxo, monitores de qualidade da água, válvulas inteligentes, detetores de fugas, e assim por diante.

Os dispositivos utilizados em soluções de água inteligentes serão geralmente ligados através de redes de área de baixa potência (LPWAN), através de um operador de rede de terceiros. Para este tipo de dispositivos, pode aproveitar a Ponte central de [dispositivos Azure IoT](../core/howto-build-iotc-device-bridge.md) para enviar os dados do seu dispositivo para a sua aplicação IoT no Azure IoT Central. Em alternativa, pode ter gateways de dispositivos capazes de IP e pode ligar-se diretamente à IoT Central.

## <a name="iot-central"></a>IoT Central 
O Azure IoT Central é uma plataforma IoT App, que o faz começar a funcionar rapidamente na sua solução IoT. Pode marcar, personalizar e integrar a sua solução com serviços de terceiros.
Depois de ligar os seus dispositivos de água inteligentes à IoT Central, obtém comando e controlo do dispositivo, monitorização e alerta, interface do utilizador com RBAC incorporado, dashboards de insights configuráveis e opções de extensibilidade. 

## <a name="extensibility-and-integrations"></a>Extensibilidade e integrações
Pode estender a sua aplicação IoT na IoT Central e opcionalmente:
* transformar e integrar os seus dados IoT para análises avançadas, por exemplo, modelos de aprendizagem automática de formação, através da exportação contínua de dados a partir da aplicação IoT Central
* automatizar fluxos de trabalho noutros sistemas desencadeando ações utilizando Power Automamate ou webhooks a partir da aplicação IoT Central
* aceder programaticamente à sua aplicação IoT na IoT Central através de APIs centrais ioT

## <a name="business-applications"></a>Aplicações empresariais 
Os dados IoT podem ser usados para alimentar uma variedade de aplicações empresariais dentro de um utilitário de água. Para aprender a ligar a sua aplicação de monitorização da qualidade da água IoT Central com os serviços de campo, siga o artigo sobre [como integrar-se com os Serviços de Campo Dynamics 365.](./how-to-configure-connected-field-services.md) 


## <a name="next-steps"></a>Passos seguintes
* Saiba como criar uma aplicação IoT Central [de monitorização da qualidade da água](./tutorial-water-quality-monitoring.md)
* Saiba mais sobre [os modelos do governo central do IoT](./overview-iot-central-government.md)
* Para saber mais sobre o IoT Central, consulte [a visão geral do IoT Central](../core/overview-iot-central.md)

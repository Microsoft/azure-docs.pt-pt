---
title: Arquitetura de referência para a solução de monitorização do consumo de água construída com Azure IoT Central| Microsoft Docs
description: Aprenda conceitos para uma solução de monitorização do consumo de água construída com a Azure IoT Central.
author: miriambrus
ms.author: miriamb
ms.date: 12/11/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: da1eafee719919d7f3086a059d8d2c70ef8d8cb1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99831659"
---
# <a name="water-consumption-monitoring-reference-architecture"></a>Arquitetura de referência da monitorização do consumo de água 

As soluções de monitorização do consumo de água podem ser construídas com o modelo de **aplicação Azure IoT Central** como uma aplicação IoT de arranque de pontapé. Este artigo fornece uma orientação de arquitetura de referência de alto nível sobre a construção de uma solução final. 

![Arquitetura de monitorização do consumo de água](./media/concepts-waterconsumptionmonitoring-architecture/concepts-waterconsumptionmonitoring-architecture1.png)

Conceitos:

1. Dispositivos e conectividade  
1. IoT Central 
1. Extensibilidade e integrações
1. Aplicações empresariais

Vamos olhar para os componentes-chave que geralmente desempenham um papel numa solução de monitorização do consumo de água.

## <a name="devices-and-connectivity"></a>Dispositivos e conectividade 
Esta secção refere-se a dispositivos utilizados para soluções inteligentes de água, como a monitorização da qualidade da água ou a monitorização do consumo de água, geralmente como dispositivos de água inteligentes. Os dispositivos de água inteligentes podem ser medidores de fluxo, monitores de qualidade da água, válvulas inteligentes, detetores de fugas, e assim por diante.

Os dispositivos utilizados em soluções de água inteligentes serão geralmente ligados através de redes de área de baixa potência (LPWAN), através de um operador de rede de terceiros. Para este tipo de dispositivos, pode utilizar a [Ponte Central de Dispositivos Azure IoT](../core/howto-build-iotc-device-bridge.md) para enviar os dados do seu dispositivo para a sua aplicação IoT no Azure IoT Central. Em alternativa, pode ter gateways de dispositivos capazes de IP e pode ligar-se diretamente à IoT Central.

## <a name="iot-central"></a>IoT Central 
O Azure IoT Central é uma plataforma IoT App, que o faz começar a funcionar rapidamente na sua solução IoT. Pode marcar, personalizar e integrar a sua solução com serviços de terceiros.
Depois de ligar os seus dispositivos de água inteligentes à IoT Central, obtém comando e controlo do dispositivo, monitorização e alerta, interface do utilizador com RBAC incorporado, dashboards de insights configuráveis e opções de extensibilidade. 


## <a name="extensibility-and-integrations"></a>Extensibilidade e integrações
Pode estender a sua aplicação IoT na IoT Central e opcionalmente:
* transformar e integrar os seus dados IoT para análises avançadas, por exemplo, modelos de aprendizagem automática de formação, através da exportação contínua de dados a partir da aplicação IoT Central
* automatizar fluxos de trabalho noutros sistemas desencadeando ações utilizando Power Automamate ou webhooks a partir da aplicação IoT Central
* aceder programaticamente à sua aplicação IoT na IoT Central através de APIs centrais ioT

## <a name="business-applications"></a>Aplicações Empresariais 
Os dados IoT podem ser usados para alimentar diferentes tipos de aplicações empresariais dentro de um utilitário de água. Para aprender a ligar a sua aplicação de monitorização do consumo de água IoT Central com serviços de campo, siga o tutorial sobre [como se integrar com a Dynamics 365 Field Services](./how-to-configure-connected-field-services.md) 


## <a name="next-steps"></a>Passos seguintes
* Saiba como criar uma aplicação IoT Central [de consumo de água](./tutorial-water-consumption-monitoring.md)
* Saiba mais sobre [os modelos do governo central do IoT](./overview-iot-central-government.md)
* Para saber mais sobre o IoT Central, consulte [a visão geral do IoT Central](../core/overview-iot-central.md)

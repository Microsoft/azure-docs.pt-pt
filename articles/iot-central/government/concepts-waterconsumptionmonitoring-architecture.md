---
title: Arquitetura de referência para a solução de monitorização do consumo de água construída com a Azure IoT Central Microsoft Docs
description: Aprenda conceitos para uma solução de monitorização do consumo de água construída com a Azure IoT Central.
author: miriambrus
ms.author: miriamb
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 3a64ca5b0c2a092f895873e097ea6beb9a235a37
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "77017705"
---
# <a name="water-consumption-monitoring-reference-architecture"></a>Arquitetura de referência da monitorização do consumo de água 



As soluções de monitorização do consumo de água podem ser construídas com o modelo de **aplicação Azure IoT Central** como aplicação IoT de arranque de pontapé. Este artigo fornece uma orientação de arquitetura de referência de alto nível sobre a construção de uma solução final. 

![Arquitetura de monitorização do consumo de água](./media/concepts-waterconsumptionmonitoring-architecture/concepts-waterconsumptionmonitoring-architecture1.png)

Conceitos:

1. Dispositivos e conectividade  
1. Centro de IoT 
2. Extebilidade e integrações
3. Aplicações empresariais

Vamos ver os componentes-chave que geralmente desempenham um papel numa solução de monitorização do consumo de água.

## <a name="devices-and-connectivity"></a>Dispositivos e conectividade 
Esta secção refere-se a dispositivos utilizados para soluções de água inteligente, como a monitorização da qualidade da água ou a monitorização do consumo de água, geralmente como dispositivos de água inteligentes. Os dispositivos de água inteligentes podem ser contadores de fluxo, monitores de qualidade da água, válvulas inteligentes, detetores de fugas, e assim por diante.

Os dispositivos utilizados em soluções de água inteligente serão geralmente ligados através de redes de área de baixa potência (LPWAN), através de um operador de rede de terceiros. Para este tipo de dispositivos, pode utilizar a Ponte Central de [Dispositivos Azure IoT](https://docs.microsoft.com/azure/iot-central/core/howto-build-iotc-device-bridge) para enviar os dados do seu dispositivo para a sua aplicação IoT na Central Azure IoT. Em alternativa, pode ter gateways de dispositivos que são capazes de IP e podem ligar-se diretamente à IoT Central.

## <a name="iot-central"></a>Centro de IoT 
O Azure IoT Central é uma plataforma IoT App, que o faz começar a funcionar rapidamente na sua solução IoT. Pode marcar, personalizar e integrar a sua solução com serviços de terceiros.
Depois de ligar os seus dispositivos de água inteligentes à IoT Central, obtém o comando e controlo do dispositivo, monitorização e alerta, interface do utilizador com RBAC incorporado, dashboards de insights configuráveis e opções de extensibility. 


## <a name="extensibility-and-integrations"></a>Extebilidade e integrações 
Pode alargar a sua aplicação ioT na IoT Central e opcionalmente:
* transforme e integre os seus dados ioT para análise avançada, por exemplo, treinando modelos de aprendizagem automática, através da exportação contínua de dados a partir da aplicação IoT Central
* automatizar fluxos de trabalho noutros sistemas desencadeando ações utilizando o Microsoft Flow ou webhooks da aplicação IoT Central
* aceda programaticamente à sua aplicação IoT em IoT Central através de APIs centrais ioT

## <a name="business-applications"></a>Aplicações Empresariais 
Os dados do IoT podem ser usados para alimentar diferentes tipos de aplicações empresariais dentro de uma utilidade de água. Para aprender a ligar a sua aplicação de monitorização do consumo de água IoT Central com os serviços de campo, siga o tutorial sobre [como integrar-se com a Dynamics 365 Field Services](./how-to-configure-connected-field-services.md) 


## <a name="next-steps"></a>Passos seguintes
* Saiba como [criar uma](./tutorial-water-consumption-monitoring.md) aplicação Central de consumo de água
* Saiba mais sobre [os modelos do governo central ioT](./overview-iot-central-government.md)
* Para saber mais sobre a IoT Central, consulte a [visão geral da IoT Central](https://docs.microsoft.com/azure/iot-central/core/overview-iot-central)

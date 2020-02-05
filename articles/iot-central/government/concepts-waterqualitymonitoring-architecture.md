---
title: Arquitetura de referência para solução de monitorização da qualidade da água construída com a Azure IoT Central Microsoft Docs
description: Aprenda conceitos para uma solução de monitorização da qualidade da água construída com a Azure IoT Central.
author: miriambrus
ms.author: miriamb
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 0c4b13c56a68205195bd5ad4b696d9e01786a8dd
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77016549"
---
# <a name="water-quality-monitoring-reference-architecture"></a>Arquitetura de referência da monitorização da qualidade da água 



As soluções de monitorização da qualidade da água podem ser construídas com o modelo de **aplicação Azure IoT Central** como aplicação IoT de arranque de pontapé. Este artigo fornece uma orientação de arquitetura de referência de alto nível sobre a construção de uma solução final. 


![Arquitetura de monitorização da qualidade da água](./media/concepts-waterqualitymonitoring-architecture/concepts-waterqualitymonitoring-architecture1.png)

Conceitos:

1. Dispositivos e conectividade  
1. IoT Central 
2. Extebilidade e integrações
3. Aplicações de negócio

Vamos ver os componentes-chave que geralmente desempenham um papel numa solução de monitorização da qualidade da água.

## <a name="devices-and-connectivity"></a>Dispositivos e conectividade 
Nesta secção, referir-nos-emos aos dispositivos utilizados na monitorização da qualidade da água ou na monitorização do consumo de água, geralmente como dispositivos de água inteligente. Os dispositivos de água inteligentes podem ser contadores de fluxo, monitores de qualidade da água, válvulas inteligentes, detetores de fugas, e assim por diante.

Os dispositivos utilizados em soluções de água inteligente serão geralmente ligados através de redes de área de baixa potência (LPWAN), através de um operador de rede de terceiros. Para este tipo de dispositivos, pode aproveitar a Ponte Central de [Dispositivos Azure IoT](https://docs.microsoft.com/azure/iot-central/core/howto-build-iotc-device-bridge) para enviar os dados do seu dispositivo para a sua aplicação IoT na Central Azure IoT. Em alternativa, pode ter gateways de dispositivos que são capazes de IP e podem ligar-se diretamente à IoT Central.

## <a name="iot-central"></a>IoT Central 
O Azure IoT Central é uma plataforma IoT App, que o faz começar a funcionar rapidamente na sua solução IoT. Pode marcar, personalizar e integrar a sua solução com serviços de terceiros.
Depois de ligar os seus dispositivos de água inteligentes à IoT Central, obtém o comando e controlo do dispositivo, monitorização e alerta, interface do utilizador com RBAC incorporado, dashboards de insights configuráveis e opções de extensibility. 

## <a name="extensibility-and-integrations"></a>Extebilidade e integrações 
Pode alargar a sua aplicação ioT na IoT Central e opcionalmente:
* transforme e integre os seus dados ioT para análise avançada, por exemplo, treinando modelos de aprendizagem automática, através da exportação contínua de dados a partir da aplicação IoT Central
* automatizar fluxos de trabalho noutros sistemas desencadeando ações utilizando o Microsoft Flow ou webhooks da aplicação IoT Central
* aceda programaticamente à sua aplicação IoT em IoT Central através de APIs centrais ioT

## <a name="business-applications"></a>Aplicações de negócio 
Os dados do IoT podem ser usados para alimentar uma variedade de aplicações comerciais dentro de um utilitário de água. Para aprender a ligar a sua aplicação de monitorização da qualidade da água IoT Central com os serviços de campo, siga o artigo sobre [como integrar-se com a Dynamics 365 Field Services](./how-to-configure-connected-field-services.md). 


## <a name="next-steps"></a>Passos seguintes
* Saiba como criar uma aplicação IoT Central [de monitorização da qualidade da água](./tutorial-water-quality-monitoring.md)
* Saiba mais sobre [os modelos do governo central ioT](./overview-iot-central-government.md)
* Para saber mais sobre a IoT Central, consulte a [visão geral da IoT Central](https://docs.microsoft.com/azure/iot-central/core/overview-iot-central)


---
title: Arquitetura de referência para solução de gestão de resíduos conectada construída com a Azure IoT Central Microsoft Docs
description: Aprenda conceitos para uma solução de gestão de resíduos conectada construída com a Azure IoT Central.
author: miriambrus
ms.author: miriamb
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 763fb82e0725e5a98112af3a79856c3703a5c2c3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "77016090"
---
# <a name="connected-waste-monitoring-reference-architecture"></a>Arquitetura de referência da monitorização de resíduos associada 



Uma solução de gestão de resíduos conectada pode ser construída usando o modelo de **aplicação Azure IoT Central** como uma aplicação IoT de arranque de pontapé. Este artigo fornece uma orientação de arquitetura de referência de alto nível sobre a construção de uma solução final. 

![Arquitetura de gestão de resíduos conectado](./media/concepts-connectedwastemanagement-architecture/concepts-connectedwastemanagement-architecture1.png)


Conceitos:

1. Dispositivos e conectividade  
1. Centro de IoT 
2. Extebilidade e integrações
3. Aplicações empresariais

Vamos ver os componentes-chave que geralmente desempenham um papel numa solução de monitorização do consumo de água.

## <a name="devices-and-connectivity"></a>Dispositivos e conectividade 
Os dispositivos utilizados em ambientes abertos, como caixotes do lixo, podem ser ligados através de redes de área de baixa potência (LPWAN), através de um operador de rede de terceiros. Para este tipo de dispositivos, pode utilizar a Ponte Central de [Dispositivos Azure IoT](https://docs.microsoft.com/azure/iot-central/core/howto-build-iotc-device-bridge) para enviar os dados do seu dispositivo para a sua aplicação IoT na Central Azure IoT. Em alternativa, pode ter gateways de dispositivos que são capazes de IP e podem ligar-se diretamente à IoT Central.

## <a name="iot-central"></a>Centro de IoT 
O Azure IoT Central é uma plataforma IoT App, que o faz começar a funcionar rapidamente na sua solução IoT. Pode marcar, personalizar e integrar a sua solução com serviços de terceiros.
Depois de ligar os seus dispositivos de água inteligentes à IoT Central, obtém o comando e controlo do dispositivo, monitorização e alerta, interface do utilizador com RBAC incorporado, dashboards de insights configuráveis e opções de extensibility. 

## <a name="extensibility-and-integrations"></a>Extebilidade e integrações 
Pode alargar a sua aplicação ioT na IoT Central e opcionalmente:
* transforme e integre os seus dados ioT para análiseavançada, por exemplo, treinando modelos de aprendizagem automática, através da exportação contínua de dados a partir da aplicação IoT Central. 
* automatizar fluxos de trabalho noutros sistemas desencadeando ações utilizando o Microsoft Flow ou webhooks da aplicação IoT Central
* aceda à sua aplicação IoT em IoT Central através de APIs centrais ioT.

## <a name="business-applications"></a>Aplicações empresariais 
Os dados do IoT podem ser usados para alimentar uma variedade de aplicações empresariais dentro de uma utilidade de resíduos. Para aprender a ligar a sua aplicação de gestão de resíduos conectada IoT Central com serviços de campo, siga o tutorial sobre [como integrar-se com a Dynamics 365 Field Services](./how-to-configure-connected-field-services.md) 

## <a name="next-steps"></a>Passos seguintes
* Saiba como criar uma aplicação IoT Central de [gestão de resíduos conectada](./tutorial-connected-waste-management.md)
* Saiba mais sobre [os modelos do governo central ioT](./overview-iot-central-government.md)
* Para saber mais sobre a IoT Central, consulte a [visão geral da IoT Central](https://docs.microsoft.com/azure/iot-central/core/overview-iot-central)


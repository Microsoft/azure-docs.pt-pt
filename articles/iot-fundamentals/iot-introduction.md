---
title: Introdução à Internet das Coisas do Azure (IoT)
description: Introdução explicando os fundamentos do Azure IoT e dos serviços IoT, incluindo exemplos que ajudam a ilustrar o uso de IoT.
author: dominicbetts
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: overview
ms.date: 01/15/2020
ms.author: dobett
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: f1053062302c9a00cf49cee1cd8de5ca6652b745
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106167707"
---
# <a name="what-is-azure-internet-of-things-iot"></a>O que é Azure Internet of Things (IoT)?

A Internet das Coisas (IoT) do Azure é uma coleção de serviços cloud geridos pela Microsoft que ligam, monitorizam e controlam milhares de milhões de recursos de IoT. Em termos mais simples, uma solução IoT é composta por um ou mais dispositivos IoT que comunicam com um ou mais serviços back-end alojados na nuvem. 

## <a name="iot-devices"></a>Dispositivos IoT

Um dispositivo IoT é normalmente composto por uma placa de circuito com sensores ligados que usam Wi-Fi para se ligar à internet. Por exemplo:

* Um sensor de pressão numa bomba de óleo remota.
* Sensores de temperatura e humidade numa unidade de ar condicionado.
* Um acelerómetro num elevador.
* Sensores de presença numa sala.

Há uma grande variedade de dispositivos disponíveis de diferentes fabricantes para construir a sua solução. Para obter uma lista de dispositivos certificados para trabalhar com o Azure IoT Hub, consulte o [catálogo de dispositivos Azure Certified for IoT](https://devicecatalog.azure.com). Para prototipagem, pode utilizar dispositivos como um [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) ou um [Raspberry Pi](https://www.raspberrypi.org/). O Devkit tem sensores incorporados para temperatura, pressão, humidade e um giroscópio, acelerómetro e magnetómetro. O Raspberry Pi permite-lhe anexar vários tipos diferentes de sensores. 

A Microsoft fornece [SDKs](../iot-hub/iot-hub-devguide-sdks.md) de dispositivo de código aberto que pode usar para construir as aplicações que executam nos seus dispositivos. Estes [SDKs simplificam e aceleram](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/) o desenvolvimento das suas soluções IoT.

## <a name="communication"></a>Comunicação

Normalmente, os dispositivos IoT enviam telemetria dos sensores para serviços de back-end na nuvem. No entanto, outros tipos de comunicação são possíveis, como um serviço de back-end enviando comandos para os seus dispositivos. Seguem-se alguns exemplos de comunicação dispositivo-a-nuvem e nuvem-para-dispositivo:

* Um caminhão de refrigeração móvel envia temperatura a cada 5 minutos para um hub IoT. 

* O serviço back-end envia um comando a um dispositivo para alterar a frequência a que envia telemetria para ajudar a diagnosticar um problema. 

* Um dispositivo envia alertas com base nos valores lidos nos seus sensores. Por exemplo, um dispositivo que monitoriza um reator de lote numa instalação química, envia um alerta quando a temperatura excede um determinado valor.

* Os seus dispositivos enviam informações para visualizar num painel de instrumentos para visualização por operadores humanos. Por exemplo, uma sala de controlo numa refinaria pode mostrar os volumes de temperatura, pressão e fluxo em cada tubo, permitindo aos operadores monitorizar a instalação. 

Os [SDKs de Dispositivo IoT](../iot-hub/iot-hub-devguide-sdks.md) e o IoT Hub suportam protocolos de [comunicação comuns](../iot-hub/iot-hub-devguide-protocols.md) como HTTP, MQTT e AMQP.

Os dispositivos IoT têm características diferentes quando comparados com outros clientes, como navegadores e aplicações móveis. Os SDKs do dispositivo ajudam-no a enfrentar os desafios da ligação segura e fiável ao seu serviço de back-end.  Especificamente, os dispositivos IoT:

* São, frequentemente, sistemas incorporados sem nenhum operador humano (ao contrário de um telefone).
* Podem ser implementados em localizações remotas, onde o acesso físico é dispendioso.
* Podem apenas ser acessíveis através do back-end da solução.
* Podem recursos de processamento e um poder limitados.
* Podem ter uma conectividade de rede intermitente, lente ou dispendiosa.
* Podem ter de utilizar protocolos de aplicação proprietários, personalizados ou específicos da indústria.

## <a name="back-end-services"></a>Serviços back-end 

Numa solução IoT, o serviço back-end fornece funcionalidades como:

* Receber telemetria à escala dos seus dispositivos e determinar como processar e armazenar esses dados.
* Analisar a telemetria para fornecer insights, em tempo real ou após o facto.
* Enviando comandos da nuvem para um dispositivo específico. 
* A provisionar dispositivos e a controlar quais os dispositivos que podem ligar à sua infraestrutura.
* Controlando o estado dos seus dispositivos e monitorizando as suas atividades.
* Gerir o firmware instalado nos seus dispositivos.

Por exemplo, numa solução de monitorização remota para uma estação de bombagem de óleo, a parte traseira da nuvem utiliza telemetria das bombas para identificar comportamentos anómalos. Quando o serviço de back-end identificar uma anomalia, pode enviar automaticamente um comando de volta ao dispositivo para tomar uma ação corretiva. Este processo gera um ciclo de comentários automatizado entre o dispositivo e a cloud, que aumenta significativamente a eficiência da solução.

## <a name="azure-iot-examples"></a>Exemplos de Azure IoT

Para exemplos da vida real de como as organizações usam Azure IoT, consulte [os Estudos de Caso Técnicos da Microsoft para IoT](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured). 

Para um debate aprofundado da arquitetura do IoT, veja [Microsoft Azure IoT Reference Architecture (Arquitetura de Referência do Microsoft Azure IoT)](/azure/architecture/reference-architectures/iot).

## <a name="next-steps"></a>Passos seguintes

Para alguns casos de negócios reais e a arquitetura utilizada, consulte o [Microsoft Azure IoT Technical Case Studies](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured).

Para alguns projetos de amostra que você pode experimentar com um IoT DevKit, consulte o Catálogo de [Projetos IoT DevKit.](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) 

Para uma explicação mais abrangente dos diferentes serviços e como são usados, consulte [os serviços e tecnologias Azure IoT](iot-services-and-technologies.md).
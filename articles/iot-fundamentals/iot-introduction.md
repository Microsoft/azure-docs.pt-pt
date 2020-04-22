---
title: Introdução à Internet Das Coisas Azure (IoT)
description: Introdução explicando os fundamentos do Azure IoT e dos serviços IoT, incluindo exemplos que ajudam a ilustrar a utilização do IoT.
author: dominicbetts
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: overview
ms.date: 01/15/2020
ms.author: dobett
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 73eb0b3164a386bb270e42ceba56d5dc7045af1c
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81728993"
---
# <a name="what-is-azure-internet-of-things-iot"></a>O que é Azure Internet of Things (IoT)?

A Internet das Coisas (IoT) do Azure é uma coleção de serviços cloud geridos pela Microsoft que ligam, monitorizam e controlam milhares de milhões de recursos de IoT. Em termos mais simples, uma solução IoT é composta por um ou mais dispositivos IoT que comunicam com um ou mais serviços de back-end hospedados na nuvem. 

## <a name="iot-devices"></a>Dispositivos IoT

Um dispositivo IoT é tipicamente composto por uma placa de circuito com sensores ligados que usam Wi-Fi para ligar à internet. Por exemplo:

* Um sensor de pressão numa bomba de óleo remota.
* Sensores de temperatura e humidade numa unidade de ar condicionado.
* Um acelerómetro num elevador.
* Sensores de presença numa sala.

Há uma grande variedade de dispositivos disponíveis de diferentes fabricantes para construir a sua solução. Para obter uma lista de dispositivos certificados para trabalhar com o Azure IoT Hub, consulte o [catálogo azure Certified para o catálogo de dispositivos IoT](https://catalog.azureiotsolutions.com/alldevices). Para prototipagem, pode utilizar dispositivos como um [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) ou um [Raspberry Pi](https://www.raspberrypi.org/). O Devkit tem sensores incorporados para temperatura, pressão, humidade e um giroscópio, acelerómetro e magnetómetro. O Raspberry Pi permite-lhe anexar muitos tipos diferentes de sensores. 

A Microsoft fornece [SDKs](../iot-hub/iot-hub-devguide-sdks.md) de dispositivo de código aberto que pode utilizar para construir as aplicações que funcionam nos seus dispositivos. Estes [SDKs simplificam e aceleram](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/) o desenvolvimento das suas soluções IoT.

## <a name="communication"></a>Comunicação

Normalmente, os dispositivos IoT enviam telemetria dos sensores para serviços de back-end na nuvem. No entanto, outros tipos de comunicação são possíveis, como um serviço de back-end que envia comandos para os seus dispositivos. Seguem-se alguns exemplos de comunicação dispositivo-nuvem e nuvem-a-dispositivo:

* Um caminhão de refrigeração móvel envia temperatura a cada 5 minutos para um Hub IoT. 

* O serviço de back-end envia um comando para um dispositivo para alterar a frequência a que envia telemetria para ajudar a diagnosticar um problema. 

* Um dispositivo envia alertas com base nos valores lidos a partir dos seus sensores. Por exemplo, um dispositivo que monitoriza um reator de lote numa fábrica química, envia um alerta quando a temperatura excede um determinado valor.

* Os seus dispositivos enviam informações para visualização num painel de instrumentos para visualização por operadores humanos. Por exemplo, uma sala de controlo numa refinaria pode apresentar os volumes de temperatura, pressão e fluxo em cada tubo, permitindo aos operadores monitorizar em cada tubo. 

Os [SDKs](../iot-hub/iot-hub-devguide-sdks.md) e IoT Device Hub suportam [protocolos comuns](../iot-hub/iot-hub-devguide-protocols.md) de comunicação como HTTP, MQTT e AMQP.

Os dispositivos IoT têm características diferentes quando comparados com outros clientes, como navegadores e aplicações móveis. Os SDKs do dispositivo ajudam-no a enfrentar os desafios de ligar os dispositivos de forma segura e fiável ao seu serviço de back-end.  Especificamente, os dispositivos IoT:

* São, frequentemente, sistemas incorporados sem nenhum operador humano (ao contrário de um telefone).
* Podem ser implementados em localizações remotas, onde o acesso físico é dispendioso.
* Podem apenas ser acessíveis através do back-end da solução.
* Podem recursos de processamento e um poder limitados.
* Podem ter uma conectividade de rede intermitente, lente ou dispendiosa.
* Podem ter de utilizar protocolos de aplicação proprietários, personalizados ou específicos da indústria.

## <a name="back-end-services"></a>Serviços de back-end 

Numa solução IoT, o serviço back-end fornece funcionalidades como:

* Receber telemetria à escala dos seus dispositivos e determinar como processar e armazenar esses dados.
* Analisando a telemetria para fornecer insights, em tempo real ou após o facto.
* Enviando comandos da nuvem para um dispositivo específico. 
* Dispositivos de fornecimento e controlo de quais dispositivos podem ligar-se à sua infraestrutura.
* Controlar o estado dos seus dispositivos e monitorizar as suas atividades.
* Gerir o firmware instalado nos seus dispositivos.

Por exemplo, numa solução de monitorização remota para uma estação de bombagem de óleo, a extremidade da nuvem utiliza telemetria das bombas para identificar comportamentos anómalos. Quando o serviço de back-end identifica uma anomalia, pode enviar automaticamente um comando de volta para o dispositivo para tomar uma ação corretiva. Este processo gera um ciclo de comentários automatizado entre o dispositivo e a cloud, que aumenta significativamente a eficiência da solução.

## <a name="azure-iot-examples"></a>Exemplos azure IoT

Para exemplos da vida real de como as organizações usam o Azure IoT, consulte [o Microsoft Technical Case Studies for IoT](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured). 

## <a name="next-steps"></a>Passos seguintes

Para alguns casos reais de negócios e a arquitetura utilizada, consulte o [Microsoft Azure IoT Technical Case Studies](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured).

Para alguns projetos de amostra que pode experimentar com um IoT DevKit, consulte o [IoT DevKit Project Catalog](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/). 

Para uma explicação mais abrangente dos diferentes serviços e como são usados, consulte [os serviços e tecnologias Azure IoT.](iot-services-and-technologies.md)

Para um debate aprofundado da arquitetura do IoT, veja [Microsoft Azure IoT Reference Architecture (Arquitetura de Referência do Microsoft Azure IoT)](https://aka.ms/iotrefarchitecture).

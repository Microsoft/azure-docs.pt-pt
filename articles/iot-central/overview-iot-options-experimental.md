---
title: Opções de IoT do Microsoft Azure | Microsoft Docs
description: Escolha como pretende implementar a sua solução de IoT do Azure com o Azure IoT Central, aceleradores de solução de IoT ou IoT Hub.
author: dominicbetts
ms.author: dobett
ms.date: 11/30/2017
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: de08894a8493ba64a59f4e012bc3f6b4f6e95b83
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56880755"
---
# <a name="compare-azure-iot-central-and-azure-iot-options"></a>Comparar as opções do Azure IoT Central e do Azure IoT

Microsoft Azure IoT Central e o Azure IoT oferecem várias opções para criar uma solução de IoT. Estas opções são adequadas para diferentes conjuntos de requisitos de cliente:

* [O Azure IoT Central](overview-iot-central-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) é um software como uma solução de serviço (SaaS) que utiliza uma abordagem baseada em modelo para ajudá-lo a criar soluções de IoT de nível empresarial sem a necessidade de conhecimentos sobre o desenvolvimento de soluções na cloud.

* [Aceleradores de solução de IoT do Azure](https://docs.microsoft.com/azure/iot-accelerators/) são uma coleção de nível empresarial de [Aceleradores de solução](../iot-accelerators/iot-accelerators-what-are-solution-accelerators.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) criados na plataforma Azure como um serviço (PaaS) que o ajudam a acelerar o desenvolvimento de soluções de IoT personalizadas.

## <a name="azure-iot-hub"></a>Azure IoT Hub

O Hub IoT é a principal PaaS do Azure que o Azure IoT Central e os aceleradores de soluções do Azure IoT utilizam. IoT Hub suporta comunicações bidirecionais fiáveis e seguras entre milhões de dispositivos IoT e uma solução na cloud. IoT Hub ajuda-o a cumprir os desafios de implementação de IoT, como:

* Gestão e de conectividade do dispositivo de grande volume
* Ingestão de telemetria de volume elevado
* Comando e controlo de dispositivos
* Imposição de segurança do dispositivo

## <a name="compare-azure-iot-central-and-azure-iot-solution-accelerators"></a>Comparar os aceleradores de soluções do Azure IoT Central e do Azure IoT

Escolher o produto do Azure IoT é uma parte crítica do planeamento da solução de IoT. O Hub IoT é um serviço do Azure individual que, por si só, não fornece uma solução de IoT ponto a ponto. Pode utilizar o IoT Hub como um ponto de partida para qualquer solução de IoT. Também não precisa usar Aceleradores de solução de IoT do Azure ou do Azure IoT Central para utilizar o IoT Hub. Tanto os aceleradores de soluções do IoT como o Azure IoT Central utilizam o Hub IoT, juntamente com outros serviços do Azure.

A tabela seguinte resume as principais diferenças entre os aceleradores de soluções do IoT e o Azure IoT Central, para o ajudar a escolher o mais adequado para os seus requisitos:

|     | Azure IoT Central | Aceleradores de soluções do Azure IoT |
| --- | ----------- | --------- |
| Utilização principal                      | Acelerar o tempo de colocação no mercado para soluções de IoT simples que não necessitam de personalização avançada do serviço.                                                    | Acelerar o desenvolvimento de uma solução de IoT personalizada que precisa da máxima flexibilidade.                                                                                                                             |
| Acesso aos serviços PaaS subjacentes | SaaS. Como é uma solução totalmente gerida, os serviços subjacentes não são expostos.                                                                                            | Tem acesso aos serviços do Azure subjacentes para geri-los ou substituí-los, conforme necessário.                                                                                                                    |
| Flexibilidade                        | Média. Pode utilizar a experiência de utilizador baseada no browser incorporado para personalizar o modelo de solução e os aspetos da IU. A infraestrutura não é personalizável porque os diferentes componentes não são expostos. | Elevada. O código para os microsserviços é open source e pode modificá-lo de forma alguma que lhe parecer adequado. Além disso, pode personalizar a infraestrutura de implementação.                                               |
| Nível de competência                        | Baixo. Precisa de competências de modelação para personalizar a solução. Não são necessárias competências de codificação.                                                                          | Médio alto. Precisa de competências em Java ou .NET para personalizar o back-end da solução. Precisa de competências em JavaScript para personalizar a visualização.                                                                       |
| Introdução a experiência de introdução             | Modelos de aplicativos e modelos de dispositivos fornecem modelos pré-criados. Pode ser implementado em segundos.                                                                                                  | As soluções pré-configuradas implementam cenários de IoT comuns. Pode ser implementado em segundos.                                                                                                                            |
| Preços                            | Estrutura de preços simples e previsível.                                                                                                                           | Pode ajustar os serviços para controlar o custo.                                                                                                                                                            |

Produto a utilizar para criar a sua solução de IoT que depende o tempo limite:

* Os seus requisitos comerciais.
* O tipo de solução que pretende criar.
* O conjunto de competências da organização para criar e manter a solução a longo prazo.

## <a name="next-steps"></a>Passos Seguintes

Com base no produto e na abordagem escolhidos, os passos seguintes sugeridos são:

* **Azure IoT Central**: [O que é o Azure IoT Central?](overview-iot-central-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
* **Aceleradores de solução de IoT**: [Quais são os Aceleradores de solução de IoT do Azure?](../iot-accelerators/iot-accelerators-what-are-solution-accelerators.md)
* **IoT Hub**: [O que é o Hub IoT do Azure?](https://docs.microsoft.com/azure/iot-hub/iot-hub-what-is-iot-hub)
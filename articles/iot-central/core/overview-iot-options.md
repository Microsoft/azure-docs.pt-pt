---
title: Opções de IoT do Microsoft Azure | Microsoft Docs
description: Escolha como implementar sua solução de IoT do Azure usando o Azure IoT Central, aceleradores de solução de IoT ou Hub IoT.
author: dominicbetts
ms.author: dobett
ms.date: 06/09/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: f57d36f6f24aab44d13ea07d8706bf40b7dcf552
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72958162"
---
# <a name="compare-azure-iot-central-and-azure-iot-options"></a>Comparar as opções do Azure IoT Central e do Azure IoT

Microsoft Azure IoT Central e o Azure IoT oferecem várias opções para a criação de uma solução de IoT. Essas opções são apropriadas para diferentes conjuntos de requisitos do cliente:

* O [Azure IOT central](overview-iot-central.md) é uma solução de SaaS (software como serviço) que usa uma abordagem baseada em modelo para ajudá-lo a criar soluções de IOT de nível empresarial sem a necessidade de conhecimento no desenvolvimento da solução de nuvem.

* Os [aceleradores de solução do Azure IOT](https://docs.microsoft.com/azure/iot-accelerators/) são uma coleção de nível empresarial de [Solution Accelerators](../../iot-accelerators/iot-accelerators-what-are-solution-accelerators.md) criada na plataforma como um serviço (PaaS) do Azure que ajuda a acelerar o desenvolvimento de soluções de IOT personalizadas.

## <a name="azure-iot-hub"></a>Hub IoT do Azure

O Hub IoT é a principal PaaS do Azure que o Azure IoT Central e os aceleradores de soluções do Azure IoT utilizam. O Hub IoT dá suporte a comunicações bidirecionais confiáveis e seguras entre milhões de dispositivos IoT e uma solução de nuvem. O Hub IoT ajuda a atender aos desafios de implementação de IoT, como:

* Conectividade e gerenciamento de dispositivos de alto volume
* Ingestão de telemetria de alto volume
* Comando e controle de dispositivos
* Imposição de segurança do dispositivo

## <a name="compare-azure-iot-central-and-azure-iot-solution-accelerators"></a>Comparar os aceleradores de soluções do Azure IoT Central e do Azure IoT

Escolher o produto do Azure IoT é uma parte crítica do planeamento da solução de IoT. O Hub IoT é um serviço do Azure individual que, por si só, não fornece uma solução de IoT ponto a ponto. Você pode usar o Hub IoT como um ponto de partida para qualquer solução de IoT. Você também não precisa usar os aceleradores de solução do Azure IoT ou o Azure IoT Central para usar o Hub IoT. Tanto os aceleradores de soluções do IoT como o Azure IoT Central utilizam o Hub IoT, juntamente com outros serviços do Azure.

A tabela seguinte resume as principais diferenças entre os aceleradores de soluções do IoT e o Azure IoT Central, para o ajudar a escolher o mais adequado para os seus requisitos:

|     | Azure IoT Central | Aceleradores de soluções do Azure IoT |
| --- | ----------- | --------- |
| Utilização principal                      | Acelerar o tempo de colocação no mercado para soluções de IoT simples que não necessitam de personalização avançada do serviço.                                                    | Acelerar o desenvolvimento de uma solução de IoT personalizada que precisa da máxima flexibilidade.                                                                                                                             |
| Acesso aos serviços PaaS subjacentes | SaaS. Como é uma solução totalmente gerenciada, os serviços subjacentes não são expostos.                                                                                            | Tem acesso aos serviços do Azure subjacentes para geri-los ou substituí-los, conforme necessário.                                                                                                                    |
| Flexibilidade                        | Média. Pode utilizar a experiência de utilizador baseada no browser incorporado para personalizar o modelo de solução e os aspetos da IU. A infraestrutura não é personalizável porque os diferentes componentes não estão expostos. | Elevada. O código para os microserviços é de software livre, e você pode modificá-lo de qualquer forma que você vir adequado. Além disso, pode personalizar a infraestrutura de implementação.                                               |
| Nível de competência                        | Baixo. Precisa de competências de modelação para personalizar a solução. Não são necessárias competências de codificação.                                                                          | Médio-alto. Você precisa de habilidades Java ou .NET para personalizar o back-end da solução. Precisa de competências em JavaScript para personalizar a visualização.                                                                       |
| Experiência de introdução             | Modelos de aplicativo e modelos de dispositivo fornecem modelos predefinidos. Pode ser implementado em segundos.                                                                                                  | As soluções pré-configuradas implementam cenários de IoT comuns. Pode ser implementado em segundos.                                                                                                                            |
| Preços                            | Estrutura de preços simples e previsível.                                                                                                                           | Pode ajustar os serviços para controlar o custo.                                                                                                                                                            |

O produto a ser usado para criar sua solução de IoT depende de:

* Os seus requisitos comerciais.
* O tipo de solução que você deseja compilar.
* O conjunto de competências da organização para criar e manter a solução a longo prazo.

## <a name="next-steps"></a>Passos seguintes

Com base no produto e na abordagem escolhidos, os passos seguintes sugeridos são:

* **IOT central do Azure**: [o que é o IOT central do Azure?](overview-iot-central.md)
* **Aceleradores de solução de IOT**: [o que são os aceleradores de solução do Azure IOT?](../../iot-accelerators/iot-accelerators-what-are-solution-accelerators.md)
* **Hub IOT**: [o que é o Hub IOT do Azure?](https://docs.microsoft.com/azure/iot-hub/iot-hub-what-is-iot-hub)
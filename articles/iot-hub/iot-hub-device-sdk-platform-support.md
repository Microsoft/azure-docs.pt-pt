---
title: Suporte à plataforma de SDKs do dispositivo IoT do Azure | Microsoft Docs
description: Conceitos-lista de plataformas com suporte dos SDKs do dispositivo IoT do Azure
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: robinsh
ms.openlocfilehash: 9a64c6e476910ab4fe983fa949680f05fdded3ae
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70161850"
---
# <a name="azure-iot-sdks-platform-support"></a>Suporte à plataforma de SDKs do Azure IoT

Os [SDKs do IOT do Azure](iot-hub-devguide-sdks.md) são um conjunto de bibliotecas para interagir com o Hub IOT e o serviço de provisionamento de dispositivos com amplo suporte a linguagens e plataformas. Os SDKs são executados em plataformas mais comuns, e os desenvolvedores podem portar o SDK do C para uma plataforma específica seguindo as [diretrizes](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)de portabilidade. 

A Microsoft dá suporte a uma variedade de sistemas operacionais/plataformas/estruturas e pode ser estendida usando o SDK do Azure IoT C. Alguns têm suporte oficialmente pela equipe, agrupados em camadas que representam o nível de suporte que os usuários podem esperar. *Plataformas com suporte total* significa que a Microsoft:

- Compila e executa continuamente testes de ponta a ponta em relação ao mestre e às versões com suporte do LTS.  Para fornecer cobertura de teste entre diferentes versões, geralmente testamos a versão mais recente do LTS e a versão mais popular.  Outras versões da mesma plataforma podem ter suporte por meio da compatibilidade de versão da plataforma.
- Fornece orientações de instalação ou pacotes, se aplicável.
- Dá suporte total às plataformas no GitHub.

Além disso, uma lista de parceiros tem portado nosso SDK do C para mais plataformas e está mantendo a PAL (camada de abstração de plataforma). O [Catálogo de dispositivos Azure Certified para IOT](https://catalog.azureiotsolutions.com/) também apresenta uma lista de plataformas de sistema operacional nas quais vários SDKs foram testados. Os SDKs também se baseiam regularmente nessas plataformas, com testes e suporte limitados:

* MBED2
* Arduino
* Windows CE 2013 (preterido em 2018 de outubro)
* .NET Standard 1,3 com o .NET Core 2,1 e .NET Framework 4,7
* Xamarin iOS, Android, UWP

## <a name="supported-platforms"></a>Plataformas suportadas

Há várias plataformas com suporte.

### <a name="c-sdk"></a>SDK DO C

| OS                  | Arquea | Compiler             | Biblioteca TLS       |
|---------------------|------|----------------------|-------------------|
| Ubuntu 16.04 LTS    | X64  | gcc-5.4.0            | OpenSSL-1.0.2 g |
| Ubuntu 18, 4 LTS    | X64  | gcc-7,3              | WolfSSL – 1.13    |
| Ubuntu 18, 4 LTS    | X64  | Clang 6.0. X          | OpenSSL – 1.1.0 g  |
| OSX 10.13.4         | x64  | 9\.4.1 do XCode          | OSX nativo        |
| Windows Server 2016 | x64  | Visual Studio 14.0.X | SChannel          |
| Windows Server 2016 | x86  | Visual Studio 14.0.X | SChannel          |
| Stretch do Debian 9    | x64  | gcc-7,3              | OpenSSL – 1.1.0 f  |

### <a name="python-sdk"></a>SDK Python

| OS                  | Arquea | Compiler   | Biblioteca TLS |
|---------------------|------|------------|-------------|
| Windows Server 2016 | x86  | Python 2.7 | OpenSSL     |
| Windows Server 2016 | x64  | Python 2.7 | OpenSSL     |
| Windows Server 2016 | x86  | Python 3,5 | OpenSSL     |
| Windows Server 2016 | x64  | Python 3,5 | OpenSSL     |
| Ubuntu 18, 4 LTS    | x86  | Python 2.7 | OpenSSL     |
| Ubuntu 18, 4 LTS    | x86  | Python 3,4 | OpenSSL     |
| MacOS High Sierra   | x64  | Python 2.7 | OpenSSL     |

### <a name="net-sdk"></a>SDK .NET

| OS                  | Arquea | Framework            | Standard          |
|---------------------|------|----------------------|-------------------|
| Ubuntu 16.04 LTS    | X64  | .NET Core 2.1        | .NET Standard 2,0 |
| Windows Server 2016 | X64  | .NET Core 2.1        | .NET Standard 2,0 |
| Windows Server 2016 | X64  | .NET Framework 4,7   | .NET Standard 2,0 |
| Windows Server 2016 | X64  | .NET Framework 4.5.1 | N/A               |

### <a name="nodejs-sdk"></a>SDK Node.js

| OS                                           | Arquea | Versão do nó    |
|----------------------------------------------|------|-----------------|
| Ubuntu 16, 4 LTS (usando a imagem do Docker do node 6) | X64  | LTS e atual |
| Windows Server 2016                          | X64  | LTS e atual |

### <a name="java-sdk"></a>SDK Java

| OS                  | Arquea | Versão de Java |
|---------------------|------|--------------|
| Ubuntu 16.04 LTS    | X64  | Java 8       |
| Windows Server 2016 | X64  | Java 8       |
| API do Android 28 | X64  | Java 8       |
| Coisas do Android | X64  | Java 8      |

## <a name="partner-supported-platforms"></a>Plataformas com suporte do parceiro

Os clientes podem estender nosso suporte de plataforma ao portar o SDK do Azure IoT C, especificamente criando a PAL (camada de abstração de plataforma) do SDK. A Microsoft trabalha com parceiros para fornecer suporte estendido. Uma lista de parceiros portou o SDK do C para mais plataformas e mantém a PAL.

| Partner             | Dispositivos                            | Ligação                     | Suporte |
|---------------------|------------------------------------|--------------------------|---------|
| Espressif           | ESP32 <br/> ESP8266                              | [Esp-azure](https://github.com/espressif/esp-azure)                | [GitHub](https://github.com/espressif/esp-azure)  
| Qualcomm            | Modem de IoT do Qualcomm MDM9206 LTE     | [SDK do Qualcomm LTE para IoT](https://developer.qualcomm.com/software/lte-iot-sdk) | [Fórum](https://developer.qualcomm.com/forums/software/lte-iot-sdk)   |
| ST-eletrônicos | Série STM32L4 <br/> Série STM32F4 <br/>  Série STM32F7 <br/>  Kit de descoberta do STM32L4 para o nó IoT    | [X-CUBE-CLOUD](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32cube-expansion-packages/x-cube-cloud.html) <br/> [X-CUBE-AZURE](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32cube-expansion-packages/x-cube-azure.html) <br/> [P-NUCLEO-AZURE](https://www.st.com/content/st_com/en/products/evaluation-tools/solution-evaluation-tools/communication-and-connectivity-solution-eval-boards/p-nucleo-azure1.html) <br/> [FP-CLD-AZURE](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32-ode-function-pack-sw/fp-cld-azure1.html)            | [Suporte](https://www.st.com/content/st_com/en/support/support-home.html)
| Texas Instruments   | LaunchPad CC3220SF </br> LaunchPad CC3220S </br> LaunchPad CC3235SF </br> LaunchPad CC3235S </br> LaunchPad MSP432E4 | [Plug-in do Azure IoT para SimpleLink](https://github.com/TexasInstruments/azure-iot-pal-simplelink) | [Fórum de TI E2E](https://e2e.ti.com) <br/> [Fórum de TI E2E para CC3220](https://e2e.ti.com/support/wireless_connectivity/simplelink_wifi_cc31xx_cc32xx/) <br/> [Fórum de TI E2E para MSP432E4](https://e2e.ti.com/support/microcontrollers/msp430/) |

## <a name="next-steps"></a>Passos Seguintes

* [SDKs de dispositivos e de serviços](iot-hub-devguide-sdks.md)
* [Diretrizes de portabilidade](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)

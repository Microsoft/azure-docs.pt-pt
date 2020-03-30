---
title: Suporte à plataforma SDKs do dispositivo Azure IoT / Microsoft Docs
description: Os SDKs do dispositivo de código aberto estão disponíveis no GitHub em C, .NET (C#), Java, Node.js e Python, para ligar os dispositivos ao Azure IoT Hub e ao Device Provisioning Service (DPS).
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: robinsh
ms.openlocfilehash: 496b890cc49b6b6b9f15213a48472447f801b1c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76045131"
---
# <a name="azure-iot-device-sdks-platform-support"></a>Suporte à plataforma SDKs do dispositivo Azure IoT

A Microsoft esforça-se por expandir continuamente o universo de dispositivos capazes do Azure IoT Hub. A Microsoft publica SDKs de dispositivo de código aberto no GitHub para ajudar a ligar dispositivos ao Azure IoT Hub e ao Serviço de Provisionamento de Dispositivos. Os SDKs do dispositivo estão disponíveis para C, .NET (C#), Java, Node.js e Python. A Microsoft testa cada SDK para garantir que funciona nas configurações suportadas detalhadas para o mesmo na secção de suporte da [plataforma Microsoft SDKs e dispositivo.](#microsoft-sdks-and-device-platform-support)

Além do dispositivo SDKs, a Microsoft fornece várias outras vias para capacitar clientes e desenvolvedores para ligar os seus dispositivos ao Azure IoT:

* A Microsoft colabora com várias empresas parceiras para ajudá-las a publicar kits de desenvolvimento, baseados no Azure IoT C SDK, para as suas plataformas de hardware.

* A Microsoft trabalha com parceiros fidedignos da Microsoft para fornecer um conjunto de dispositivos em constante expansão que foram testados e certificados para o Azure IoT. Para obter uma lista atual destes dispositivos, consulte o Azure certificado para o catálogo de [dispositivos IoT](https://catalog.azureiotsolutions.com/).

* A Microsoft fornece uma camada de abstração de plataforma (PAL) no Azure IoT Hub Device C SDK que ajuda os desenvolvedores a facilmente colocar o SDK na sua plataforma. Para saber mais, consulte a [orientação porção C SDK](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md).

Este tópico fornece informações sobre os SDKs da Microsoft e as configurações da plataforma que suportam, bem como cada uma das outras opções listadas acima.

## <a name="microsoft-sdks-and-device-platform-support"></a>Microsoft SDKs e suporte à plataforma do dispositivo

A Microsoft publica SDKs de código aberto no GitHub para os seguintes idiomas: C, .NET (C#), Node.js, Java e Python. Os SDKs e as suas dependências estão listados nesta secção. Os SDKs são suportados em qualquer plataforma de dispositivoque satisfaça estas dependências.

Para cada um dos SDKs listados, microsoft:

* Continuamente constrói e executa testes de ponta a ponta contra o ramo principal do SDK relevante no GitHub em várias plataformas populares.  Para fornecer cobertura de teste em diferentes versões compiladas, geralmente testamos contra a versão MAIS recente LTS e a versão mais popular.

* Fornece orientação de instalação ou pacotes de instalação, se aplicável.

* Suporta totalmente os SDKs no GitHub com código de código de código aberto, um caminho para contribuições para o cliente e envolvimento da equipa de produtos com problemas gitHub.

### <a name="c-sdk"></a>C SDK

O [dispositivo Azure IoT Hub C SDK](https://github.com/Azure/azure-iot-sdk-c) é testado e suporta as seguintes configurações.

| SO                  | Biblioteca TLS                  | Requisitos adicionais                                                                     |
|---------------------|------------------------------|---------------------------------------------------------------------------------------------|
| Linux               | OpenSSL, WolfSSL ou BearSSL | Tomadas de Berkeley</br></br>Interface portátil do sistema operativo (POSIX)                       |
| iOS 12.2            | OpenSSL                      | XCode emulado em OSX 10.13.4                                                               |
| Família Windows 10   | Canal                     |                                                                                             |
| Mbed OS 5.4         | Mbed TLS 2                   | [Kit de dev MXChip IoT](https://microsoft.github.io/azure-iot-developer-kit/)                  |
| Esfera Azure OS     | Wolfssl                      | [Esfera Azure MT3620](https://azure.microsoft.com/services/azure-sphere/get-started/) |
| Rio Arduino             | Bearssl                      | [ESP32 ou ESP8266](https://github.com/Azure/azure-iot-arduino#simple-sample-instructions) 

### <a name="python-sdk"></a>SDK Python

O [dispositivo Azure IoT Hub Python SDK](https://github.com/Azure/azure-iot-sdk-python) é testado e suporta as seguintes configurações.

| SO                  | Compilador                          |
|---------------------|-----------------------------------|
| Linux               | Python 2.7.*, 3.5 ou mais tarde |
| MacOS High Sierra   | Python 2.7.*, 3.5 ou mais tarde |
| Família Windows 10   | Python 2.7.*, 3.5 ou mais tarde |

Apenas a versão Python 3.5.3 ou posterior suportam as APIs assíncronas, recomendamos a utilização da versão 3.7 ou posterior.

### <a name="net-sdk"></a>SDK .NET

O [dispositivo Azure IoT Hub .NET (C#) é](https://github.com/Azure/azure-iot-sdk-csharp) testado e suporta as seguintes configurações.

| SO                                   | Standard                                                   |
|--------------------------------------|------------------------------------------------------------|
| Linux                                | .NET Core 2.1                                              |
| Windows 10 Desktop e Servidor SKUs   | .NET Core 2.1, .NET Quadro 4.5.1, ou .NET Quadro 4.7 |

O .NET SDK também pode ser usado com o Windows IoT Core com o Agente de [Dispositivos Azure](https://github.com/ms-iot/azure-client-tools/blob/master/docs/device-agent/device-agent.md) ou [um NTService personalizado que pode usar RPC para comunicar com aplicações UWP](https://docs.microsoft.com/samples/microsoft/windows-iotcore-samples/ntservice-rpc/).

### <a name="nodejs-sdk"></a>SDK Node.js

O [dispositivo Azure IoT Hub Node.js é](https://github.com/Azure/azure-iot-sdk-node) testado e suporta as seguintes configurações.

| SO                  | Versão do nó    |
|---------------------|-----------------|
| Linux               | LTS e Current |
| Família Windows 10   | LTS e Current |

### <a name="java-sdk"></a>SDK Java

O [dispositivo Azure IoT Hub Java é](https://github.com/Azure/azure-iot-sdk-java) testado e suporta as seguintes configurações.

| SO                     | Versão Java |
|------------------------|--------------|
| Android API 28         | Java 8       |
| Linux x64             | Java 8       |
| Família Windows 10 x64  | Java 8       |

## <a name="partner-supported-development-kits"></a>Kits de desenvolvimento apoiados por parceiros

A Microsoft trabalha com vários parceiros para fornecer kits de desenvolvimento para várias arquiteturas de microprocessadores. Estes parceiros levaram o Azure IoT C SDK para a sua plataforma. Os parceiros criam e mantêm a camada de abstração da plataforma (PAL) do SDK. A Microsoft trabalha com estes parceiros para fornecer um suporte alargado.

| Partner             | Dispositivos                            | Ligação                     | Suporte |
|---------------------|------------------------------------|--------------------------|---------|
| Espressif           | ESP32 <br/> ESP8266                              | [Esp-azure](https://github.com/espressif/esp-azure)                | [GitHub](https://github.com/espressif/esp-azure)  
| Qualcomm            | Qualcomm MDM9206 LTE IoT Modem     | [Qualcomm LTE para IoT SDK](https://developer.qualcomm.com/software/lte-iot-sdk) | [Fórum](https://developer.qualcomm.com/forums/software/lte-iot-sdk)   |
| Microeletrónica ST | Série STM32L4 <br/> Série STM32F4 <br/>  Série STM32F7 <br/>  Kit de descoberta STM32L4 para nó IoT    | [X-CUBE-AZURE](https://www.st.com/en/embedded-software/x-cube-azure.html) <br/>  <br/> [P-NUCLEO-AZURE](https://www.st.com/content/st_com/en/products/evaluation-tools/solution-evaluation-tools/communication-and-connectivity-solution-eval-boards/p-nucleo-azure1.html) <br/> [FP-CLD-AZURE](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32-ode-function-pack-sw/fp-cld-azure1.html)            | [Suporte](https://www.st.com/content/st_com/en/support/support-home.html)
| Instrumentos do Texas   | Plataforma de lançamento CC3220SF </br> Plataforma de lançamento CC3220S </br> Plataforma de lançamento CC3235SF </br> Plataforma de lançamento CC3235S </br> Plataforma de lançamento MSP432E4 | [Plugin Azure IoT para SimpleLink](https://github.com/TexasInstruments/azure-iot-pal-simplelink) | [Fórum TI E2E](https://e2e.ti.com) <br/> [Fórum TI E2E para CC3220](https://e2e.ti.com/support/wireless_connectivity/simplelink_wifi_cc31xx_cc32xx/) <br/> [Fórum TI E2E para MSP432E4](https://e2e.ti.com/support/microcontrollers/msp430/) |

## <a name="porting-the-microsoft-azure-iot-c-sdk"></a>Porting the Microsoft Azure IoT C SDK

Se a sua plataforma de dispositivonão estiver coberta por uma das secções anteriores, pode considerar a possibilidade de porpor o Azure IoT C SDK. Porting o C SDK envolve principalmente a implementação da camada de abstração da plataforma (PAL) do SDK. O PAL define primitivos que fornecem a cola entre o seu dispositivo e funções de nível superior no SDK. Para mais informações, consulte [A Orientação porosa](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md).

## <a name="microsoft-partners-and-certified-azure-iot-devices"></a>Parceiros da Microsoft e dispositivos Azure IoT certificados

A Microsoft trabalha com vários parceiros para expandir continuamente o universo Azure IoT com dispositivos testados e certificados do Azure IoT.

* Para navegar em dispositivos certificados Azure IoT, consulte [o Microsoft Azure Certified para o Catálogo de Dispositivos IoT](https://catalog.azureiotsolutions.com/).

* Para saber mais sobre o ecossistema Azure Certified for IoT, consulte [Join the Certified for IoT ecosystem](https://catalog.azureiotsolutions.com/register).

## <a name="connecting-to-iot-hub-without-an-sdk"></a>Ligação ao IoT Hub sem um SDK

Se não conseguir utilizar um dos SDKs do dispositivo IoT Hub, pode ligar-se diretamente ao IoT Hub utilizando as [APIs ioT Hub REST](https://docs.microsoft.com/rest/api/iothub/) de qualquer aplicação capaz de enviar e receber pedidos e respostas HTTPS.

## <a name="support-and-other-resources"></a>Apoio e outros recursos

Se tiver problemas ao utilizar os SDKs do dispositivo Azure IoT, existem várias formas de procurar apoio. Pode experimentar um dos seguintes canais:

**Reportar bugs** – Bugs no dispositivo SDKs podem ser reportados na página de problemas do projeto GitHub relevante. As correções rapidamente fazem o seu caminho desde o projeto até às atualizações do produto.

* [Problemas azure IoT Hub C SDK](https://github.com/Azure/azure-iot-sdk-c/issues)

* [Problemas de SDK Azure IoT Hub .NET (C#)](https://github.com/Azure/azure-iot-sdk-csharp/issues)

* [Problemas azure IoT Hub Java SDK](https://github.com/Azure/azure-iot-sdk-java/issues)

* [Problemas do Azure IoT Hub Node.js SDK](https://github.com/Azure/azure-iot-sdk-node/issues)

* [Problemas azure IoT Hub Python SDK](https://github.com/Azure/azure-iot-sdk-python/issues)

**Equipa de Suporte ao Cliente** da Microsoft – Os utilizadores que tenham um [plano](https://azure.microsoft.com/support/plans/) de suporte podem contratar a equipa de Suporte ao Cliente da Microsoft, criando um novo pedido de suporte diretamente a partir do [portal Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

Pedidos de **funcionalidades** – Os pedidos de funcionalidade SiOT são rastreados através da página de Voz do [Utilizador](https://feedback.azure.com/forums/321918-azure-iot)do produto.

## <a name="next-steps"></a>Passos seguintes

* [SDKs de dispositivos e de serviços](iot-hub-devguide-sdks.md)
* [Orientação porção](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)

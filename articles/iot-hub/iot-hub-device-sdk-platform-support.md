---
title: Suporte da plataforma Azure IoT SDKs | Microsoft Docs
description: Os SDKs de dispositivo de código aberto estão disponíveis no GitHub em C, .NET (C#), Java, Node.js e Python, para ligar dispositivos ao Azure IoT Hub e ao Serviço de Provisionamento de Dispositivos (DPS).
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: robinsh
ms.openlocfilehash: ab450c6ec6ae32cafcf07e88940736f209973d8d
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106168047"
---
# <a name="azure-iot-device-sdks-platform-support"></a>Suporte da plataforma Azure IoT Device SDKs

A Microsoft esforça-se por expandir continuamente o universo de dispositivos capazes Azure IoT Hub. A Microsoft publica SDKs de dispositivo de código aberto no GitHub para ajudar a ligar os dispositivos ao Azure IoT Hub e ao Serviço de Provisionamento de Dispositivos. Os SDKs do dispositivo estão disponíveis para C, .NET (C#), Java, Node.js e Python. A Microsoft testa cada SDK para garantir que funciona nas configurações suportadas detalhadas para o mesmo na secção de suporte da [plataforma microsoft SDKs e dispositivos.](#microsoft-sdks-and-device-platform-support)

Além dos SDKs do dispositivo, a Microsoft fornece várias outras vias para capacitar clientes e desenvolvedores a ligarem os seus dispositivos ao Azure IoT:

* A Microsoft colabora com várias empresas parceiras para os ajudar a publicar kits de desenvolvimento, com base no Azure IoT C SDK, para as suas plataformas de hardware.

* A Microsoft trabalha com parceiros de confiança da Microsoft para fornecer um conjunto de dispositivos em constante expansão que foram testados e certificados para o Azure IoT. Para obter uma lista atual destes dispositivos, consulte o [Azure certificado para o catálogo de dispositivos IoT](https://devicecatalog.azure.com/).

* A Microsoft fornece uma camada de abstração de plataforma (PAL) no Azure IoT Hub Device C SDK que ajuda os desenvolvedores a facilmente colocar o SDK na sua plataforma. Para saber mais, consulte a [orientação de porção da C SDK.](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)

Este tópico fornece informações sobre os SDKs da Microsoft e as configurações da plataforma que suportam, bem como cada uma das outras opções listadas acima.

## <a name="microsoft-sdks-and-device-platform-support"></a>Microsoft SDKs e suporte à plataforma de dispositivos

A Microsoft publica SDKs de código aberto no GitHub para os seguintes idiomas: C, .NET (C#), Node.js, Java e Python. Os SDKs e as suas dependências estão listados nesta secção. Os SDKs são suportados em qualquer plataforma de dispositivo que satisfaça estas dependências.

Para cada um dos SDKs listados, a Microsoft:

* Continuamente constrói e executa testes de ponta a ponta contra o ramo principal do relevante SDK em GitHub em várias plataformas populares.  Para fornecer cobertura de teste em diferentes versões de compilador, geralmente testamos com a versão LTS mais recente e a versão mais popular.

* Fornece orientação de instalação ou pacotes de instalação, se aplicável.

* Suporta totalmente os SDKs no GitHub com código de código aberto, um caminho para as contribuições dos clientes e envolvimento da equipa de produtos com problemas do GitHub.

### <a name="c-sdk"></a>C SDK

O [dispositivo Azure IoT Hub C SDK](https://github.com/Azure/azure-iot-sdk-c) é testado e suporta as seguintes configurações.

| SO                  | Biblioteca TLS                  | Requisitos adicionais                                                                     |
|---------------------|------------------------------|---------------------------------------------------------------------------------------------|
| Linux               | OpenSSL, WolfSSL ou BearSSL | Tomadas berkeley</br></br>Interface do sistema operativo portátil (POSIX)                       |
| iOS 12.2            | OpenSSL                      | XCode emulado em OSX 10.13.4                                                               |
| Família do Windows 10   | Canal da Schannel                     |                                                                                             |
| Mbed OS 5.4         | Mbed TLS 2                   | [Kit dev MXChip IoT](https://microsoft.github.io/azure-iot-developer-kit/)                  |
| Esfera de Azure OS     | Wolfssl                      | [Esfera azul MT3620](https://azure.microsoft.com/services/azure-sphere/get-started/) |
| Rio Arduino             | Urso                      | [ESP32 ou ESP8266](https://github.com/Azure/azure-iot-arduino#simple-sample-instructions) 

### <a name="python-sdk"></a>SDK Python

O [dispositivo Azure IoT Hub Python SDK](https://github.com/Azure/azure-iot-sdk-python) é testado e suporta as seguintes configurações.

| SO                  | Compilador                          |
|---------------------|-----------------------------------|
| Linux               | Python 2.7.*, 3.6 ou mais tarde |
| macOS High Sierra   | Python 2.7.*, 3.6 ou mais tarde |
| Família do Windows 10   | Python 2.7.*, 3.6 ou mais tarde |

Apenas a versão Python 3.5.3 ou posteriormente suporta as APIs assíncronos, recomendamos a utilização da versão 3.7 ou posterior.

### <a name="net-sdk"></a>SDK .NET

O [dispositivo Azure IoT Hub .NET (C#) SDK](https://github.com/Azure/azure-iot-sdk-csharp) é testado e suporta as seguintes configurações.

| SO                                   | Standard                                                   |
|--------------------------------------|------------------------------------------------------------|
| Linux                                | .NET Core 2.1                                              |
| COMPUTADORES do Windows 10 e SKUs do servidor   | .NET Core 2.1, .NET Framework 4.5.1, ou .NET Framework 4.7 |

O .NET SDK também pode ser usado com o Windows IoT Core com o [Agente de Dispositivos Azure](https://github.com/ms-iot/azure-client-tools/blob/master/docs/device-agent/device-agent.md) ou [um NTService personalizado que pode usar RPC para comunicar com aplicações UWP](/samples/microsoft/windows-iotcore-samples/ntservice-rpc/).

### <a name="nodejs-sdk"></a>SDK Node.js

O [Azure IoT Hub Node.js dispositivo SDK](https://github.com/Azure/azure-iot-sdk-node) é testado e suporta as seguintes configurações.

| SO                  | Versão nó    |
|---------------------|-----------------|
| Linux               | LTS e Corrente |
| Família do Windows 10   | LTS e Corrente |

### <a name="java-sdk"></a>SDK Java

O [dispositivo Azure IoT Hub Java É](https://github.com/Azure/azure-iot-sdk-java) testado e suporta as seguintes configurações.

| SO                     | Versão java |
|------------------------|--------------|
| Android API 28         | Java 8       |
| Linux x64             | Java 8       |
| Família do Windows 10 x64  | Java 8       |

## <a name="partner-supported-development-kits"></a>Kits de desenvolvimento apoiados por parceiros

A Microsoft trabalha com vários parceiros para fornecer kits de desenvolvimento para várias arquiteturas de microprocessadores. Estes parceiros têm a portado o Azure IoT C SDK para a sua plataforma. Os parceiros criam e mantêm a camada de abstração da plataforma (PAL) do SDK. A Microsoft trabalha com estes parceiros para fornecer suporte alargado.

| Parceiro             | Dispositivos                            | Ligação                     | Suporte |
|---------------------|------------------------------------|--------------------------|---------|
| Espressif           | ESP32 <br/> ESP8266                              | [Esp-azure](https://github.com/espressif/esp-azure)                | [GitHub](https://github.com/espressif/esp-azure)  
| Qualcomm            | Qualcomm MDM9206 LTE IoT Modem     | [Qualcomm LTE para IoT SDK](https://developer.qualcomm.com/software/lte-iot-sdk) | [Fórum](https://developer.qualcomm.com/forums/software/lte-iot-sdk)   |
| ST Microeletrónica | Série STM32L4 <br/> Série STM32F4 <br/>  Série STM32F7 <br/>  Kit de descoberta STM32L4 para nó IoT    | [X-CUBE-AZURE](https://www.st.com/en/embedded-software/x-cube-azure.html) <br/>  <br/> [P-NUCLEO-AZURE](https://www.st.com/content/st_com/en/products/evaluation-tools/solution-evaluation-tools/communication-and-connectivity-solution-eval-boards/p-nucleo-azure1.html) <br/> [FP-CLD-AZURE](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32-ode-function-pack-sw/fp-cld-azure1.html)            | [Suporte](https://www.st.com/content/st_com/en/support/support-home.html)
| Instrumentos do Texas   | Plataforma de lançamento CC3220SF </br> Plataforma de lançamento CC3220S </br> Plataforma de lançamento CC3235SF </br> Plataforma de lançamento CC3235S </br> Plataforma de lançamento MSP432E4 | [Plugin Azure IoT para SimpleLink](https://github.com/TexasInstruments/azure-iot-pal-simplelink) | [Fórum TI E2E](https://e2e.ti.com) <br/> [Ti E2E Forum para CC3220](https://e2e.ti.com/support/wireless_connectivity/simplelink_wifi_cc31xx_cc32xx/) <br/> [Ti E2E Forum para MSP432E4](https://e2e.ti.com/support/microcontrollers/msp430/) |

## <a name="porting-the-microsoft-azure-iot-c-sdk"></a>Porting o Microsoft Azure IoT C SDK

Se a plataforma do seu dispositivo não estiver coberta por uma das secções anteriores, pode considerar a deslocalização do Azure IoT C SDK. A porção do C SDK envolve principalmente a implementação da camada de abstração da plataforma (PAL) do SDK. O PAL define primitivos que fornecem a cola entre o seu dispositivo e funções de nível superior no SDK. Para mais informações, consulte [a Orientação porting.](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)

## <a name="microsoft-partners-and-certified-azure-iot-devices"></a>Parceiros microsoft e dispositivos Azure IoT certificados

A Microsoft trabalha com vários parceiros para expandir continuamente o universo Azure IoT com dispositivos testados e certificados Azure IoT.

* Para navegar em dispositivos certificados Azure IoT, consulte [o Microsoft Azure Certified for IoT Device Catalog](https://devicecatalog.azure.com/).

* Para saber mais sobre o ecossistema Azure Certified for IoT, consulte [Junte-se ao ecossistema Certificado para IoT.](../certification/overview.md)

## <a name="connecting-to-iot-hub-without-an-sdk"></a>Ligação ao IoT Hub sem um SDK

Se não conseguir utilizar um dos SDKs do dispositivo IoT Hub, pode ligar-se diretamente ao IoT Hub utilizando um protocolo de comunicação suportado. Para saber mais, consulte [Develop without a Azure IoT SDK](iot-hub-devguide-no-sdk.md).

## <a name="support-and-other-resources"></a>Suporte e outros recursos

Se tiver problemas durante a utilização dos SDKs do dispositivo Azure IoT, existem várias formas de procurar suporte resumido abaixo. Para obter informações completas sobre todas as suas opções de suporte, consulte [as Opções de Suporte e Ajuda Azure IoT.](../iot-fundamentals/iot-support-help.md) 

**Reportar bugs** – Bugs no dispositivo SDKs podem ser reportados na página de problemas do projeto GitHub relevante. As correções fazem rapidamente o seu caminho desde o projeto até atualizações de produtos.

* [Problemas de Azure IoT Hub C SDK](https://github.com/Azure/azure-iot-sdk-c/issues)

* [Azure IoT Hub .NET (C#) Problemas SDK](https://github.com/Azure/azure-iot-sdk-csharp/issues)

* [Problemas de Azure IoT Hub Java SDK](https://github.com/Azure/azure-iot-sdk-java/issues)

* [Azure IoT Hub Node.js problemas SDK](https://github.com/Azure/azure-iot-sdk-node/issues)

* [Problemas de Azure IoT Hub Python SDK](https://github.com/Azure/azure-iot-sdk-python/issues)

**Perguntas Técnicas** – Pode fazer perguntas técnicas tanto no [Microsoft Q&A](/answers/topics/azure-iot-sdk.html) como no Stack [Overflow](https://stackoverflow.com/questions/tagged/azure-iot-sdk) utilizando a etiqueta *azure-iot-sdk*.

**Equipa de Apoio ao Cliente da Microsoft** – Os utilizadores que tenham um plano de [suporte](https://azure.microsoft.com/support/plans/) podem envolver a equipa de Suporte ao Cliente da Microsoft, criando um novo pedido de suporte diretamente do [portal Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

**Pedidos de funcionalidades** – Os pedidos de funcionalidadeS Azure IoT são rastreados através da página user [Voice](https://feedback.azure.com/forums/321918-azure-iot)do produto .

## <a name="next-steps"></a>Passos seguintes

* [SDKs de dispositivos e de serviços](iot-hub-devguide-sdks.md)
* [Orientação porting](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)

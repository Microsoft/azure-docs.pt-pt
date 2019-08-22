---
author: ChrisGMsft
ms.service: iot-pnp
ms.topic: include
ms.date: 06/28/2019
ms.author: chrisgre
ms.openlocfilehash: 7e8174855800bc6beea8750c32a6dd32588ccd9e
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69880465"
---
## <a name="iot-plug-and-play-device"></a>Dispositivo de Plug and Play de IoT

Um dispositivo de Plug and Play IoT normalmente é um dispositivo de computação independente de pequena escala que pode coletar dados ou controlar outros dispositivos, e que executa software ou firmware que implementa os recursos declarados em um [modelo de capacidade de dispositivo](#device-capability-model).  Por exemplo, um dispositivo IoT Plug and Play pode ser um dispositivo de monitoramento ambiental ou um controlador para um sistema de irrigação agrigulture inteligente. As soluções de IoT hospedadas na nuvem podem ser gravadas no comando, no controle e no recebimento de dados de dispositivos IoT Plug and Play. Os dispositivos IoT Plug and Play podem ser encontrados por meio do [Catálogo de dispositivos certificado pelo Azure para IOT](https://catalog.azureiotsolutions.com/). Cada dispositivo de Plug and Play IoT no catálogo foi validado e tem um [modelo de funcionalidade de dispositivo](#device-capability-model).

## <a name="digital-twin"></a>Entrelaçar digital

Digital gêmeos são modelos de dispositivos IoT Plug and Play.  As gêmeos digitais são modeladas usando a [linguagem de definição de entrelaçamento digital](https://aka.ms/DTDL).  Você pode usar a API do Azure IoT para interagir com o digital gêmeos. 

## <a name="digital-twin-definition-language"></a>Linguagem de definição de entrelaçamento digital

Uma linguagem para descrever modelos e interfaces para [dispositivos de plug and Play IOT](#iot-plug-and-play-device).  Use a [linguagem de definição de entrelaçamento digital](https://aka.ms/DTDL) para descrever os recursos de uma teledigital e habilitar a plataforma IOT e as soluções de IOT para aproveitar a semântica da entidade. [](#digital-twin)

## <a name="device-capability-model"></a>Modelo de funcionalidade do dispositivo

Um modelo de capacidade de dispositivo descreve um dispositivo e define o conjunto de interfaces implementadas pelo dispositivo. Crie um modelo de capacidade de dispositivo para corresponder a um dispositivo físico, produto ou SKU.

## <a name="interface"></a>Interface

Uma interface descreve os recursos relacionados que são implementados por um dispositivo ou um. digital. As interfaces podem ser reutilizadas em diferentes modelos de recursos.

## <a name="property"></a>Propriedade

As propriedades são campos de dados definidos em uma [interface](#interface) que representam algum estado de uma. digital. Você pode declarar propriedades como somente leitura ou gravável. As propriedades somente leitura são definidas pelo código em execução no contexto do próprio dispositivo de Plug and Play de IoT, como o número de série.  As propriedades graváveis são definidas por entidades externas, como limites de alarme.

## <a name="telemetry"></a>Telemetria

Os campos de telemetria definidos em uma [interface](#interface) representam medidas. Essas medidas são normalmente valores como leituras de sensor.

## <a name="command"></a>Comando

Os comandos definidos em uma [interface](#interface) representam métodos que podem ser executados no digital. Por exemplo, um comando de redefinição para redefinir um dispositivo.

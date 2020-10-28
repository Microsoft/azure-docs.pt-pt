---
title: Guia de desenvolvimento para Azure IoT Hub Microsoft Docs
description: O guia de desenvolvimento do Azure IoT Hub inclui discussões sobre pontos finais, segurança, registo de identidade, gestão de dispositivos, métodos diretos, gémeos de dispositivos, uploads de ficheiros, empregos, a linguagem de consulta ioT Hub e mensagens.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.custom: mqtt
ms.openlocfilehash: 03e05eb2bc56a07c2bda3d3fb224012abb3c9e4a
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92735937"
---
# <a name="azure-iot-hub-developer-guide"></a>Guia de desenvolvimento do Azure IoT Hub

O Azure IoT Hub é um serviço totalmente gerido que ajuda a permitir comunicações biducionais fiáveis e seguras entre milhões de dispositivos e uma solução traseira.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

O Azure IoT Hub fornece-lhe:

* Proteja as comunicações utilizando credenciais de segurança por dispositivo e controlo de acesso.

* Múltiplas opções de comunicação em nuvem-a-nuvem e nuvem-para-dispositivo.

* Armazenamento consultado de informações e meta-dados por dispositivo.

* Fácil conectividade de dispositivos com bibliotecas de dispositivos para os idiomas e plataformas mais populares.

Este guia de desenvolvimento do IoT Hub inclui os seguintes artigos:

* [A orientação de comunicação dispositivo-a-nuvem](iot-hub-devguide-d2c-guidance.md) ajuda-o a escolher entre mensagens de dispositivo para nuvem, propriedades reportadas do dispositivo twin e upload de ficheiros.

* [A orientação de comunicação nuvem-a-dispositivo ajuda-o](iot-hub-devguide-c2d-guidance.md) a escolher entre métodos diretos, propriedades desejadas do dispositivo Twin e mensagens nuvem-dispositivo.

* [Mensagens de dispositivo para nuvem e nuvem-para-dispositivo com IoT Hub](iot-hub-devguide-messaging.md) descrevem as funcionalidades de mensagens (dispositivo-a-nuvem e nuvem-para-dispositivo) que o IoT Hub expõe.

  * [Envie mensagens dispositivo-a-nuvem para o IoT Hub](iot-hub-devguide-messages-d2c.md).

  * [Leia as mensagens dispositivo-nuvem do ponto final incorporado](iot-hub-devguide-messages-read-builtin.md).

  * [Utilize pontos finais personalizados e regras de encaminhamento para mensagens dispositivo-nuvem](iot-hub-devguide-messages-read-custom.md).

  * [Envie mensagens nuvem-para-dispositivo a partir do IoT Hub](iot-hub-devguide-messages-c2d.md).

  * [Crie e leia mensagens IoT Hub](iot-hub-devguide-messages-construct.md).

* [O upload de ficheiros de um dispositivo](iot-hub-devguide-file-upload.md) descreve como pode fazer upload de ficheiros a partir de um dispositivo. O artigo também inclui informações sobre tópicos como as notificações que o processo de upload pode enviar.

* [Gerir identidades de dispositivos no IoT Hub](iot-hub-devguide-identity-registry.md) descreve que informação cada centro de registo de identidade de IoT armazena. O artigo também descreve como pode aceder e modificá-lo.

* [O acesso ao IoT Hub](iot-hub-devguide-security.md) descreve o modelo de segurança utilizado para garantir o acesso à funcionalidade IoT Hub tanto para dispositivos como para componentes em nuvem. O artigo inclui informações sobre a utilização de fichas e certificados X.509, bem como detalhes das permissões que pode conceder.

* [Utilize gémeos do dispositivo para sincronizar o estado e as configurações](iot-hub-devguide-device-twins.md) descreve o conceito de twin do *dispositivo.* O artigo também descreve o dispositivo de funcionalidade que os gémeos expõem, como por exemplo sincronizar um dispositivo com o seu dispositivo gémeo. O artigo inclui informações sobre os dados armazenados num dispositivo gémeo.

* [Invocar um método direto num dispositivo](iot-hub-devguide-direct-methods.md) descreve o ciclo de vida de um método direto. O artigo descreve como invocar métodos num dispositivo a partir da sua aplicação back-end e lidar com o método direto no seu dispositivo.

* [Agendar trabalhos em vários dispositivos](iot-hub-devguide-jobs.md) descreve como pode agendar trabalhos em vários dispositivos. O artigo descreve como submeter trabalhos que executam tarefas como executar um método direto, atualizando um dispositivo usando um dispositivo gémeo. Também descreve como consultar o estado de um trabalho.

* [Referência - escolha um protocolo de comunicação](iot-hub-devguide-protocols.md) descreve os protocolos de comunicação que o IoT Hub suporta para a comunicação do dispositivo e lista as portas que devem estar abertas.

* [Referência - Os pontos finais do IoT Hub](iot-hub-devguide-endpoints.md) descrevem os vários pontos finais que cada hub IoT expõe para operações de tempo de execução e gestão. O artigo também descreve como pode criar pontos finais adicionais no seu hub IoT e como usar uma porta de entrada de campo para permitir a conectividade com os seus pontos finais IoT Hub em cenários não padrão.

* [Referência - IoT Hub linguagem de consulta para gémeos de dispositivos, empregos e encaminhamento de mensagens](iot-hub-devguide-query-language.md) descreve que a linguagem de consulta IoT Hub que lhe permite obter informações do seu centro sobre os gémeos e empregos do seu dispositivo.

* [Referência - quotas e estrangulamento](iot-hub-devguide-quotas-throttling.md) resume as quotas estabelecidas no serviço IoT Hub e o estrangulamento que ocorre quando excede uma quota.

* [Referência - os preços](iot-hub-devguide-pricing.md) fornecem informações gerais sobre diferentes SKUs e preços para o IoT Hub e detalhes sobre como as várias funcionalidades do IoT Hub são medidos como mensagens pelo IoT Hub.

* [Referência - Dispositivo e serviço Os SDKs](iot-hub-devguide-sdks.md) listam os SDKs Azure IoT para o desenvolvimento de aplicações de dispositivos e serviços que interagem com o seu hub IoT. O artigo inclui links para documentação online da API.

* [Referência - O suporte do IoT Hub MQTT](iot-hub-mqtt-support.md) fornece informações detalhadas sobre como o IoT Hub suporta o protocolo MQTT. O artigo descreve o suporte para o protocolo MQTT incorporado aos SDKs Azure IoT e fornece informações sobre a utilização direta do protocolo MQTT.

* [Glossário](iot-hub-devguide-glossary.md) uma lista de termos comuns relacionados com o IoT Hub.

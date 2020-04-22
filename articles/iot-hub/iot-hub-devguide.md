---
title: Guia de desenvolvimento para o Hub Azure IoT [ Microsoft Docs
description: O guia de desenvolvimento do Azure IoT Hub inclui discussões sobre pontos finais, segurança, registo de identidade, gestão de dispositivos, métodos diretos, gémeos de dispositivos, uploads de ficheiros, empregos, a linguagem de consulta ioT Hub e mensagens.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.custom: mqtt
ms.openlocfilehash: 4e380fdcd72ab21f00272536b9f08145dcb09bc1
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81728821"
---
# <a name="azure-iot-hub-developer-guide"></a>Guia de desenvolvimento do Azure IoT Hub

O Azure IoT Hub é um serviço totalmente gerido que ajuda a permitir comunicações bidirecionais fiáveis e seguras entre milhões de dispositivos e uma solução traseira.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

O Azure IoT Hub fornece-lhe:

* Proteja as comunicações utilizando credenciais de segurança por dispositivo e controlo de acesso.

* Múltiplas opções de comunicação hiper-escala de dispositivo-nuvem e nuvem-a-dispositivo.

* Armazenamento consultadoráveis de informações estatais por dispositivo e meta-dados.

* Conectividade fácil do dispositivo com bibliotecas de dispositivos para as línguas e plataformas mais populares.

Este guia de desenvolvimento do IoT Hub inclui os seguintes artigos:

* [A orientação de comunicação dispositivo-nuvem ajuda-o](iot-hub-devguide-d2c-guidance.md) a escolher entre mensagens dispositivo-cloud, propriedades reportadas do dispositivo Twin e upload de ficheiros.

* [A orientação de comunicação cloud-to-device ajuda-o](iot-hub-devguide-c2d-guidance.md) a escolher entre métodos diretos, propriedades dedispositivos e mensagens cloud-to-device.

* [As mensagens de dispositivo-a-nuvem e nuvem-a-dispositivo com o IoT Hub](iot-hub-devguide-messaging.md) descrevem as funcionalidades de mensagens (dispositivo-para-nuvem e cloud-to-device) que o IoT Hub expõe.

  * [Envie mensagens de dispositivo-nuvem para IoT Hub](iot-hub-devguide-messages-d2c.md).

  * [Leia as mensagens dispositivo-to-cloud do ponto final incorporado](iot-hub-devguide-messages-read-builtin.md).

  * [Utilize pontos finais personalizados e regras de encaminhamento para mensagens dispositivo-cloud](iot-hub-devguide-messages-read-custom.md).

  * [Envie mensagens cloud-to-device a partir do IoT Hub](iot-hub-devguide-messages-c2d.md).

  * [Crie e leia mensagens IoT Hub](iot-hub-devguide-messages-construct.md).

* [O upload de ficheiros de um dispositivo](iot-hub-devguide-file-upload.md) descreve como pode fazer upload de ficheiros a partir de um dispositivo. O artigo também inclui informações sobre tópicos como as notificações que o processo de upload pode enviar.

* [Gerir identidades de dispositivos no IoT Hub](iot-hub-devguide-identity-registry.md)descreve que informações cada loja de registo de identidade do ioT hub. O artigo também descreve como pode aceder e modificá-lo.

* [O acesso ao IoT Hub](iot-hub-devguide-security.md) descreve o modelo de segurança utilizado para dar acesso à funcionalidade IoT Hub tanto para dispositivos como para componentes de nuvem. O artigo inclui informações sobre a utilização de tokens e certificados X.509, e detalhes das permissões que pode conceder.

* [Use gémeos dispositivos para sincronizar estado e configurações](iot-hub-devguide-device-twins.md) descrevem o conceito gémeo do *dispositivo.* O artigo também descreve o dispositivo de funcionalidade que os gémeos expõem, como sincronizar um dispositivo com o seu dispositivo twin. O artigo inclui informações sobre os dados armazenados num dispositivo twin.

* [Invocar um método direto num dispositivo](iot-hub-devguide-direct-methods.md) descreve o ciclo de vida de um método direto. O artigo descreve como invocar métodos num dispositivo a partir da sua aplicação back-end e lidar com o método direto no seu dispositivo.

* [Agendar trabalhos em vários dispositivos](iot-hub-devguide-jobs.md) descreve como pode agendar trabalhos em vários dispositivos. O artigo descreve como submeter trabalhos que executam tarefas como executar um método direto, atualizando um dispositivo usando um dispositivo twin. Também descreve como consultar o estado de um trabalho.

* [Referência - escolha um protocolo](iot-hub-devguide-protocols.md) de comunicação descreve os protocolos de comunicação que o IoT Hub suporta para a comunicação do dispositivo e lista as portas que devem estar abertas.

* [Referência - Os pontos finais do IoT Hub](iot-hub-devguide-endpoints.md) descrevem os vários pontos finais que cada hub IoT expõe para operações de execução e gestão. O artigo também descreve como você pode criar pontos finais adicionais no seu hub IoT, e como usar um gateway de campo para permitir a conectividade com os seus pontos finais Do IoT Hub em cenários não padrão.

* [Referência - IoT Hub consulta linguagem para gémeos dispositivos, empregos e encaminhamento](iot-hub-devguide-query-language.md) de mensagens descreve que a linguagem de consulta IoT Hub que lhe permite obter informações do seu hub sobre o seu dispositivo gémeos e empregos.

* [Referência - quotas e estrangulamentos](iot-hub-devguide-quotas-throttling.md) resumem as quotas definidas no serviço IoT Hub e a aceleração que ocorre quando se excede uma quota.

* [Referência - os preços](iot-hub-devguide-pricing.md) fornecem informações gerais sobre diferentes SKUs e preços para IoT Hub e detalhes sobre como as várias funcionalidades do IoT Hub são medidos como mensagens pelo IoT Hub.

* [Referência - Dispositivo e serviço SDKs](iot-hub-devguide-sdks.md) lista maquetes Azure IoT para desenvolver dispositivos e aplicações de serviço que interagem com o seu hub IoT. O artigo inclui links para documentação api online.

* [Referência - Suporte IoT Hub MQTT](iot-hub-mqtt-support.md) fornece informações detalhadas sobre como o IoT Hub suporta o protocolo MQTT. O artigo descreve o suporte para o protocolo MQTT incorporado nos SDKs Azure IoT e fornece informações sobre a utilização direta do protocolo MQTT.

* [Glossário](iot-hub-devguide-glossary.md) uma lista de termos comuns relacionados com o IoT Hub.
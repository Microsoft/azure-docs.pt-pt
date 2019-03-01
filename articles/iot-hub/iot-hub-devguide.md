---
title: Guia do programador para o IoT Hub do Azure | Documentos da Microsoft
description: Guia do programador do IoT Hub do Azure inclui discussões de pontos de extremidade, segurança, registo de identidade, gestão de dispositivos, os métodos diretos, dispositivos duplos, carregamentos de ficheiros, tarefas, a linguagem de consulta do IoT Hub e de mensagens.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: 0246b19c0155fe2707ac780c7acb61be424d2caf
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "57008936"
---
# <a name="azure-iot-hub-developer-guide"></a>Guia de programador do IoT Hub do Azure

O IoT Hub do Azure é um serviço totalmente gerido que ajuda a habilitar comunicações bidirecionais fiáveis e seguras entre milhões de dispositivos e uma solução de back-end.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

O IoT Hub do Azure fornecem-lhe:

* Comunicações seguras utilizando credenciais de segurança por dispositivo e controlo de acesso.

* Várias opções de comunicação de hiperescala do dispositivo para a nuvem e na cloud para o dispositivo.

* Armazenamento consultável de informações de estado por dispositivo e metadados.

* Conectividade do dispositivo de simples com bibliotecas de dispositivo para as linguagens e plataformas mais populares.

Este guia de programador do IoT Hub inclui os seguintes artigos:

* [Orientações de comunicação do dispositivo-para-cloud](iot-hub-devguide-d2c-guidance.md) ajuda a escolher entre as mensagens do dispositivo para a cloud, propriedades comunicadas do dispositivo duplo e carregamento de ficheiros.

* [Orientações de comunicação de cloud-para-dispositivo](iot-hub-devguide-c2d-guidance.md) ajuda a escolher entre os métodos diretos, dispositivo duplo as propriedades pretendidas e mensagens na cloud para o dispositivo.

* [Dispositivo-para-cloud e as mensagens com o IoT Hub na cloud para dispositivo](iot-hub-devguide-messaging.md) descreve as funcionalidades de mensagens (dispositivo-para-cloud e cloud-para-dispositivo) que expõe o IoT Hub.

  * [Enviar mensagens dispositivo-para a nuvem ao IoT Hub](iot-hub-devguide-messages-d2c.md).

  * [Ler mensagens do dispositivo para cloud a partir do ponto final incorporado](iot-hub-devguide-messages-read-builtin.md).

  * [Utilizar os pontos finais personalizados e regras de encaminhamento para mensagens de dispositivo-para-cloud](iot-hub-devguide-messages-read-custom.md).

  * [Enviar mensagens da cloud para dispositivo do IoT Hub](iot-hub-devguide-messages-c2d.md).

  * [Criar e ler as mensagens de IoT Hub](iot-hub-devguide-messages-construct.md).

* [Carregar ficheiros a partir de um dispositivo](iot-hub-devguide-file-upload.md) descreve como pode carregar ficheiros a partir de um dispositivo. O artigo também inclui informações sobre os tópicos como as notificações, o processo de carregamento pode enviar.

* [Gerir identidades de dispositivos no IoT Hub](iot-hub-devguide-identity-registry.md)descreve as informações que arquivos de registo de identidade de cada hub IoT. O artigo também descreve como pode aceder e modificá-lo.

* [Controlar o acesso ao IoT Hub](iot-hub-devguide-security.md) descreve o modelo de segurança utilizado para conceder acesso à funcionalidade do IoT Hub para ambos os dispositivos e componentes da cloud. O artigo inclui informações sobre como utilizar tokens e certificados X.509 e os detalhes de permissões que pode conceder.

* [Utilizar dispositivos duplos para sincronizar o estado e configurações](iot-hub-devguide-device-twins.md) descreve a *dispositivo duplo* conceito. O artigo também descibes dispositivos duplos a funcionalidade expor, tais como sincronizar um dispositivo com o seu dispositivo duplo. O artigo inclui informações sobre os dados armazenados num dispositivo duplo.

* [Invocar um método direto num dispositivo](iot-hub-devguide-direct-methods.md) descreve o ciclo de vida de um método direto. O artigo descreve como invocar métodos num dispositivo da sua aplicação de back-end e manipular o método direto no seu dispositivo.

* [Agendar tarefas em vários dispositivos](iot-hub-devguide-jobs.md) descreve como pode agendar tarefas em vários dispositivos. O artigo descreve como submeter tarefas que executam tarefas como a execução de um método direto, a atualização de um dispositivo com um dispositivo duplo. Ele descreve também como consultar o estado de uma tarefa.

* [Referência - escolher um protocolo de comunicação](iot-hub-devguide-protocols.md) descreve os protocolos de comunicação que o IoT Hub suporta para a comunicação do dispositivo e apresenta uma lista de portas que devem ser apresentadas.

* [Referência - pontos finais do IoT Hub](iot-hub-devguide-endpoints.md) descreve vários pontos de extremidade que cada hub IoT expõe para operações de tempo de execução e gestão. O artigo também descreve como pode criar pontos finais adicionais do seu hub IoT e como utilizar um gateway de campo para ativar a conectividade para seus pontos de extremidade do IoT Hub em cenários não padrão.

* [Referência - linguagem de consulta do IoT Hub para dispositivos duplos, tarefas e encaminhamento de mensagens](iot-hub-devguide-query-language.md) descreve essa linguagem de consulta do IoT Hub que permite-lhe obter informações do seu hub sobre os seus dispositivos duplos e trabalhos.

* [Referência - quotas e limitação](iot-hub-devguide-quotas-throttling.md) resume as quotas definidas no serviço IoT Hub e a limitação que ocorre quando uma quota seja excedida.

* [Referência - preços](iot-hub-devguide-pricing.md) fornece informações gerais sobre os SKUs diferentes e preços do IoT Hub e detalhes sobre como as diversas funcionalidades do IoT Hub são medidas como mensagens pelo IoT Hub.

* [Referência - SDKs de serviço e dispositivo](iot-hub-devguide-sdks.md) lista os SDKs IoT do Azure para o desenvolvimento de aplicações de dispositivos e de serviços que interagem com o seu hub IoT. O artigo contém ligações para documentação de API online.

* [Referência - suporte para MQTT do IoT Hub](iot-hub-mqtt-support.md) fornece informações detalhadas sobre como o IoT Hub suporta o protocolo MQTT. O artigo descreve o suporte do protocolo MQTT incorporado para os SDKs IoT do Azure e fornece informações sobre como utilizar o protocolo MQTT diretamente.

* [Glossário](iot-hub-devguide-glossary.md) uma lista de termos comuns relacionados com o IoT Hub.
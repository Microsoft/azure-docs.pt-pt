---
title: Saiba mais sobre as opções de conexão para os desenvolvedores de dispositivos Azure IoT
description: Saiba mais sobre opções de ligação do dispositivo comumente usadas e ferramentas para desenvolvedores de dispositivos Azure IoT.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: 6bbd7d37418af68065daa194d4ff4bd80f6fd09c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100654369"
---
# <a name="overview-connection-options-for-azure-iot-device-developers"></a>Visão geral: Opções de conexão para desenvolvedores de dispositivos Azure IoT
Como desenvolvedor que trabalha com dispositivos, tem várias opções para ligar e gerir dispositivos Azure IoT. Este artigo vê as opções e ferramentas mais utilizadas para ajudá-lo a conectar e gerir dispositivos.

Ao avaliar as opções de ligação Azure IoT para os seus dispositivos, é útil comparar os seguintes itens:
- Plataformas de aplicação de dispositivos Azure IoT
- Ferramentas para ligar e gerir dispositivos

## <a name="application-platforms-iot-central-and-iot-hub"></a>Plataformas de aplicação: IoT Central e IoT Hub
O Azure IoT contém dois serviços que são plataformas para aplicações em nuvem ativadas por dispositivos: Azure IoT Central e Azure IoT Hub. Pode utilizar qualquer um para hospedar uma aplicação IoT e ligar dispositivos.
- [A IoT Central](../iot-central/core/overview-iot-central.md) foi concebida para reduzir a complexidade e o custo de trabalhar com soluções IoT. É uma aplicação de software-as-a-service (SaaS) que fornece uma plataforma completa para hospedar aplicações IoT. Você pode usar a UI web IoT Central para agilizar todo o ciclo de vida de criar e gerir aplicações IoT. A UI web simplifica as tarefas de criar aplicações, conectando e gerindo de alguns até milhões de dispositivos. A IoT Central utiliza o IoT Hub para criar e gerir aplicações, mas mantém os detalhes transparentes para o utilizador. Em geral, a IoT Central proporciona uma complexidade e esforço de desenvolvimento reduzidos e uma gestão simplificada dos dispositivos através da UI web.
- [O IoT Hub](../iot-hub/about-iot-hub.md) funciona como um centro de mensagens central para a comunicação bidis entre aplicações IoT e dispositivos conectados. É uma aplicação de plataforma-as-a-service (PaaS) que também fornece uma plataforma para hospedar aplicações IoT. Tal como a IoT Central, pode escalar para suportar milhões de dispositivos. Em geral, o IoT Hub oferece um maior controlo e personalização sobre o design da sua aplicação, e mais opções de ferramentas de desenvolvimento para trabalhar com o serviço, à custa de algum aumento na complexidade de desenvolvimento e gestão.

## <a name="tools-to-connect-and-manage-devices"></a>Ferramentas para ligar e gerir dispositivos
Depois de selecionar IoT Hub ou IoT Central para hospedar a sua aplicação IoT, tem várias opções de ferramentas de desenvolvimento. Pode utilizar estas ferramentas para ligar à sua plataforma de aplicação IoT selecionada e para criar e gerir aplicações e dispositivos. A tabela a seguir resume as opções comuns da ferramenta. 

> [!NOTE]
> Além das seguintes ferramentas, pode criar e gerir programáticamente aplicações IoT utilizando modelos REST API's, Azure SDKs ou Azure Resource Manager. Você pode saber mais na documentação do serviço [IoT Hub](../iot-hub/about-iot-hub.md) e [IoT Central.](../iot-central/core/overview-iot-central.md)

|Ferramenta  |Suporta plataforma &nbsp; &nbsp; &nbsp; IoT &nbsp; |Documentação  |Description  |
|---------|---------|---------|---------|
|Central web UI     | Centro | [Início rápido central](../iot-central/core/quick-deploy-iot-central.md) | Portal baseado no navegador para ioT Central. |
|Portal do Azure     | Hub      | [Criar um hub IoT com portal Azure,](../iot-hub/iot-hub-create-through-portal.md) [Gerir a IoT Central a partir do portal Azure](../iot-central/core/howto-manage-iot-central-from-portal.md)| Portal baseado no navegador para IoT Hub e dispositivos. Também trabalha com outros recursos da Azure, incluindo a IoT Central. |
|CLI do Azure     | Hub          | [Criar um hub IoT com CLI,](../iot-hub/iot-hub-create-using-cli.md) [Gerir a IoT Central a partir de Azure CLI](../iot-central/core/howto-manage-iot-central-from-cli.md) | Interface de linha de comando para criar e gerir aplicações IoT. |
|Azure PowerShell     | Hub   | [Criar um hub IoT com PowerShell,](../iot-hub/iot-hub-create-using-powershell.md) [Gerir a IoT Central da Azure PowerShell](../iot-central/core/howto-manage-iot-central-from-powershell.md) | Interface PowerShell para criar e gerir aplicações IoT |
|Azure IoT Tools para VS Code  | Hub | [Criar um hub IoT com ferramentas para código VS](../iot-hub/iot-hub-create-use-iot-toolkit.md) | Extensão do Código VS para aplicações IoT Hub. |
|Explorador Azure IoT     | Hub | [Explorador Azure IoT](https://github.com/Azure/azure-iot-explorer) | Não é possível criar centros IoT. Conecta-se a um hub IoT existente para gerir dispositivos. Frequentemente usado com CLI ou Portal.|

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre as suas opções de ligação de dispositivos ao Azure IoT, explore os seguintes quickstarts:
- IoT Central: [Criar uma aplicação Azure IoT Central](../iot-central/core/quick-deploy-iot-central.md)
- IoT Hub: [Enviar telemetria de um dispositivo para um hub IoT e monitorizá-lo com o Azure CLI](../iot-hub/quickstart-send-telemetry-cli.md)
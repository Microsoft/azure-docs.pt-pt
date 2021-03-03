---
title: Atualização do dispositivo de provisionamento para o agente hub Azure IoT| Microsoft Docs
description: Atualização do dispositivo de provisionamento para agente do hub IoT Azure
author: ValOlson
ms.author: valls
ms.date: 2/16/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: 79bac3f057412f3973121f48cd735f72d0a97d04
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101680013"
---
# <a name="device-update-agent"></a>Agente de atualização de dispositivos

A Atualização do Dispositivo para o IoT Hub suporta duas formas de atualizações – baseadas em imagem e baseadas em pacotes. 

* As atualizações de imagem proporcionam um maior nível de confiança no estado final do dispositivo. É tipicamente mais fácil replicar os resultados de uma atualização de imagem entre um ambiente de pré-produção e um ambiente de produção, uma vez que não coloca os mesmos desafios que os pacotes e as suas dependências. Devido à sua natureza atómica, também se pode adotar facilmente um modelo de failover A/B. 
* As atualizações baseadas em pacotes são atualizações direcionadas que alteram apenas um componente ou aplicação específico no dispositivo. Assim, o que leva a um menor consumo de largura de banda e ajuda a reduzir o tempo para descarregar e instalar a atualização. As atualizações de pacotes normalmente permitem menos tempo de inatividade dos dispositivos ao aplicar uma atualização e evitar a sobrecarga de criação de imagens. 

Siga os links abaixo sobre como construir, executar e modificar o agente de atualização do dispositivo.

## <a name="build-the-device-update-agent"></a>Construa o agente de atualização do dispositivo

Siga as instruções para [construir](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-build-agent-code.md) o Agente de Atualização do Dispositivo a partir da fonte.

## <a name="run-the-device-update-agent"></a>Executar o agente de atualização do dispositivo

Assim que o agente estiver a construir com sucesso, está na hora de [executar](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md) o agente.

## <a name="modifying-the-device-update-agent"></a>Modificar o agente de atualização do dispositivo

Agora, faça as alterações necessárias para incorporar o agente na sua imagem.  Veja como [modificar](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-modify-the-agent-code.m) o Agente de Atualização do Dispositivo para obter orientação.

### <a name="troubleshooting-guide"></a>Guia de Resolução de Problemas

Se tiver problemas, reveja o Guia de [Resolução de Problemas](troubleshoot-device-update.md) do IoT Hub para ajudar a desbloquear eventuais problemas e recolher as informações necessárias para fornecer à Microsoft.

## <a name="next-steps"></a>Passos Seguintes

Utilize imagens e binários pré-construídos para uma demonstração fácil da Atualização do Dispositivo para ioT Hub.  

[Atualização de imagem: Começar com Raspberry Pi 3 B+ Referência Yocto Image](device-update-raspberry-pi.md)

[Atualização de imagem:Começar a usar ubuntu (18,04 x64) Agente de referência do simulador](device-update-simulator.md)

[Atualização do pacote:Começar a usar o agente de pacote ubuntu Server 18.04 x64](device-update-ubuntu-agent.md)


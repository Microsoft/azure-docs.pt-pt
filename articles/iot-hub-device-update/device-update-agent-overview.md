---
title: Compreenda a atualização do dispositivo para o agente hub Azure IoT| Microsoft Docs
description: Compreenda a atualização do dispositivo para o agente hub Azure IoT.
author: ValOlson
ms.author: valls
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: efe5d0171463668bda19a0d0445fc67f3734aaee
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105561241"
---
# <a name="device-update-for-iot-hub-agent-overview"></a>Descrição Geral da Atualização de Dispositivos para o Agente do Hub IoT

O Agente de Atualização de Dispositivos é composto por duas camadas conceptuais:

* A Camada de Interface baseia-se no topo do [Azure IoT Plug and Play (PnP)](../iot-pnp/overview-iot-plug-and-play.md) permitindo que as mensagens fluam entre o Agente de Atualização do Dispositivo e os Serviços de Atualização de Dispositivos.
* A Camada de Plataforma é responsável pelas ações de atualização de alto nível de Download, Install e Apply que podem ser plataforma ou dispositivo específico.

:::image type="content" source="media/understand-device-update/client-agent-reference-implementations.png" alt-text="Implementações de Agentes." lightbox="media/understand-device-update/client-agent-reference-implementations.png":::

## <a name="the-interface-layer"></a>A Camada de Interface

A camada de interface é composta pela Interface Central da [ADU](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/adu_core_interface) e pela [Interface de Informação](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/device_info_interface)do Dispositivo.

Estas interfaces baseiam-se num ficheiro de configuração para valores predefinidos. Os valores predefinidos incluem aduc_manufacturer e aduc_model para a interface AzureDeviceUpdateCore e modelo e fabricante para a interface DeviceInformation. [Saiba mais](device-update-configuration-file.md) o ficheiro de configuração.

### <a name="adu-core-interface"></a>ADU Core Interface

A interface 'ADU Core' é o canal de comunicação primário entre o Agente e Serviços de Atualização do Dispositivo. [Saiba mais](device-update-plug-and-play.md#adu-core-interface) sobre esta interface.

### <a name="device-information-interface"></a>Interface de informação do dispositivo

A Interface de Informação do Dispositivo é utilizada para implementar a `Azure IoT PnP DeviceInformation` interface. [Saiba mais](device-update-plug-and-play.md#device-information-interface) sobre esta interface.

## <a name="the-platform-layer"></a>A Camada da Plataforma

Existem duas implementações da Camada de Plataforma. A Camada de Plataforma do Simulador tem uma implementação trivial das ações de atualização e é usada para testar e avaliar rapidamente a Atualização do Dispositivo para serviços e configuração do Hub IoT. Quando o Agente de Atualização do Dispositivo é construído com a Camada de Plataforma do Simulador, referimo-nos a ele como o Agente do Simulador de Atualização do Dispositivo ou apenas simulador. [Saiba mais](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md) sobre como usar o agente simulador. A Camada de Plataforma Linux integra-se com [a Otimização de Entrega](https://github.com/microsoft/do-client) para downloads e é usada na nossa imagem de referência Raspberry Pi, e todos os clientes que funcionam em sistemas Linux.

### <a name="simulator-platform-layer"></a>Camada de plataforma do simulador

A implementação da Camada de Plataforma do Simulador pode ser encontrada no `src/platform_layers/simulator_platform_layer` e pode ser usada para testar e avaliar a atualização do dispositivo para o IoT Hub.  Muitas das ações na implementação do "simulador" são ridicularizadas para reduzir as alterações físicas para experimentar com a Atualização do Dispositivo para o IoT Hub.  Uma atualização "simulada" pode ser realizada com esta Camada de Plataforma. [Saiba mais](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md) sobre a execução do agente simulador.

### <a name="linux-platform-layer"></a>Camada de plataforma linux

A implementação da Camada de Plataforma Linux pode ser encontrada no `src/platform_layers/linux_platform_layer` e integra-se com o [Cliente de Otimização de Entrega](https://github.com/microsoft/do-client/releases) para downloads e é usada na nossa imagem de referência Raspberry Pi, e todos os clientes que funcionam em sistemas Linux.

Esta camada pode integrar-se com diferentes Manipuladores de Atualização para implementar o instalador. Por exemplo, o `SWUpdate` Manipulador de Atualização invoca um script de concha para ligar para o `SWUpdate` executável para executar uma atualização.

## <a name="update-handlers"></a>Manipuladores de atualização

Os Manipuladores de Atualização são componentes que lidam com conteúdo ou peças específicas do instalador da atualização. As implementações do Update Handler estão em `src/content_handlers` .

### <a name="simulator-update-handler"></a>Manipulador de atualização de simulador

O Manipulador de Atualização de Simuladores é utilizado pela Camada de Plataforma do Simulador e pode ser utilizado com a Camada de Plataforma Linux para falsas interações com um Manipulador de Conteúdos. O Manipulador de Atualização do Simulador implementa as APIs do Manipulador de Atualização com a maioria das ops. A implementação do Manipulador de Atualização do Simulador pode ser encontrada abaixo:
* [Simulador de atualização de imagem](https://github.com/Azure/iot-hub-device-update/blob/main/src/content_handlers/swupdate_handler/inc/aduc/swupdate_simulator_handler.hpp)
* [Simulador de apt de atualização de pacote](https://github.com/Azure/iot-hub-device-update/blob/main/src/content_handlers/apt_handler/inc/aduc/apt_simulator_handler.hpp)

Nota: O campo deCriteria Instalado na interface AzureDeviceUpdateCore PnP deve ser o hash sha256 do conteúdo. Este é o mesmo haxixe que está presente no [Objeto Manifesto de Importação.](import-update.md#create-device-update-import-manifest) [Saiba mais](device-update-plug-and-play.md) `installedCriteria` sobre e sobre a `AzureDeviceUpdateCore` interface.

### <a name="swupdate-update-handler"></a>`SWUpdate` Manipulador de atualização

O `SWUpdate` Manipulador de Atualização" integra-se com a `SWUpdate` linha de comando executável e outros comandos de concha para implementar atualizações A/B especificamente para a imagem de referência Raspberry Pi. Encontre [aqui](https://github.com/Azure/iot-hub-device-update/releases)a mais recente imagem de referência de Raspberry Pi. O `SWUpdate` Manipulador de Atualização é implementado em [src/content_handlers/swupdate_content_handler](https://github.com/Azure/iot-hub-device-update/tree/main/src/content_handlers/swupdate_handler).

### <a name="apt-update-handler"></a>Manipulador de atualização APT

O APT Update Handler processa um Manifesto de Atualização específico da APT e invoca o APT para instalar ou atualizar os pacotes Debian especificados.

## <a name="self-update-device-update-agent"></a>Agente de atualização do dispositivo de auto-actualização

O agente de atualização do dispositivo e as suas dependências podem ser atualizados através da Atualização do Dispositivo para o pipeline IoT Hub. Se estiver a utilizar uma atualização baseada em imagem, inclua o mais recente agente de atualização do dispositivo na sua nova imagem. Se estiver a utilizar uma atualização baseada em pacotes, inclua o agente de atualização do dispositivo e a versão desejada no manifesto apt como qualquer outro pacote. [Saiba mais](device-update-apt-manifest.md) sobre o manifesto apto. Pode verificar a versão instalada do agente de Atualização do Dispositivo e o agente de otimização de entrega na secção Propriedades do Dispositivo do seu [dispositivo IoT twin](../iot-hub/iot-hub-devguide-device-twins.md). [Saiba mais sobre as propriedades do dispositivo em ADU Core Interface.](device-update-plug-and-play.md#device-properties)

## <a name="next-steps"></a>Passos Seguintes
[Compreenda o ficheiro de configuração do agente de atualização de dispositivos](device-update-configuration-file.md)
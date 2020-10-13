---
title: IoT Plug e Play Bridge / Microsoft Docs
description: Compreenda a ponte IoT Plug and Play e como usá-la para ligar os dispositivos existentes ligados a um windows ou a um gateway Linux como dispositivos IoT Plug e Play.
author: usivagna
ms.author: ugans
ms.date: 09/22/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 224f86a40fa812003463301f97bcae07de907f3c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91580468"
---
# <a name="iot-plug-and-play-bridge"></a>Bridge do IoT Plug and Play

A ponte IoT Plug and Play é uma aplicação de código aberto para ligar dispositivos existentes ligados ao Windows ou ao Gateway Linux como dispositivos IoT Plug e Play. Depois de instalar e configurar a aplicação no seu Windows ou máquina Linux, pode usá-la para ligar dispositivos ligados a um hub IoT. Pode utilizar a ponte para mapear interfaces IoT Plug e Play para a telemetria que os dispositivos anexos estão a enviar, trabalhar com as propriedades do dispositivo e invocar comandos.

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-high-level.png" alt-text="Do lado esquerdo existem alguns sensores existentes (ligados e sem fios) a um PC Windows ou Linux contendo IoT Plug e Play Bridge. A ponte IoT Plug and Play liga-se então a um hub IoT no lado direito":::

A ponte IoT Plug e Play pode ser implantada como uma ponte autónoma executável em qualquer dispositivo IoT, PC industrial, servidor ou gateway que executa o Windows 10 ou Linux. Também pode ser compilado no seu código de aplicação. Um simples ficheiro JSON de configuração diz à ponte IoT Plug and Play que os dispositivos/periféricos ligados devem ser expostos até Azure.

## <a name="supported-protocols-and-sensors"></a>Protocolos e Sensores Apoiados

A ponte IoT Plug and Play suporta por predefinição os seguintes tipos de periféricos, com ligações à documentação do adaptador:

|Periférico|Windows|Linux|
|---------|---------|---------|
|[Bluetooth LE](https://aka.ms/iot-pnp-bridge-bluetooth)       |Sim|Não|
|[Câmaras](https://aka.ms/iot-pnp-bridge-camera)               |Sim|Não|
|[Rio Modbus](https://aka.ms/iot-pnp-bridge-modbus)                |Sim|Sim|
|[MQTT](https://aka.ms/iot-pnp-bridge-mqtt)                    |Sim|Sim|
|[Série](https://aka.ms/iot-pnp-bridge-serial)                |Sim|Sim|
|[Periféricos USB do Windows](https://aka.ms/iot-pnp-bridge-usb)  |Sim|Não Aplicável|

>[!Important]
>Os desenvolvedores podem estender a ponte IoT Plug e Play para suportar protocolos adicionais de dispositivos através das instruções na documentação do desenvolvedor de **[pontes IoT Plug e Play aqui](https://aka.ms/iot-pnp-bridge-dev-doc)**.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="os-platform"></a>Plataforma OS

As seguintes plataformas e versões de SO são suportadas:

|Plataforma  |Versões Suportadas  |
|---------|---------|
|Windows 10 |     Todos os SKUs do Windows são suportados. Por exemplo: IoT Enterprise, Server, Desktop, IoT Core. *Para a funcionalidade de monitorização de saúde da câmara, recomenda-se a construção de 20H1 ou posterior. Todas as outras funcionalidades estão disponíveis em todas as construções do Windows 10.*  |
|Linux     |Testada e suportada no Ubuntu 18.04, a funcionalidade em outras distribuições não foi testada.         |
||

### <a name="hardware"></a>Hardware

- Qualquer plataforma de hardware capaz de suportar os SKUs e versões de SO acima.
- Os periféricos e sensores de série, USB, Bluetooth e Camera são suportados de forma nativa. A Ponte IoT Plug e Play Bridge pode ser estendida para suportar qualquer periférico ou sensor personalizado[(ver secção periférica acima).](#iot-plug-and-play-bridge)

### <a name="development-environment"></a>Ambiente de Programação

Para construir, estender e desenvolver a ponte IoT Plug e Play que você precisará:  

- Um ambiente de desenvolvimento que suporta a compilação de C++ como: [Visual Studio (Comunidade, Profissional ou Enterprise)](https://visualstudio.microsoft.com/downloads/)- certifique-se de que inclui o Desktop Development com carga de trabalho C++ quando instalar o Visual Studio.
- [CMake](https://cmake.org/download/) - quando instalar o CMake, selecione a opção `Add CMake to the system PATH` .
- Se estiver a construir no Windows, também terá de descarregar o Windows 17763 SDK: [https://developer.microsoft.com/windows/downloads/windows-10-sdk](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
- [Dispositivo Azure IoT Hub C SDK](https://github.com/Azure/azure-iot-sdk-c). Os scripts de construção incluídos neste repo clonarão automaticamente o Azure IoT C SDK necessário para si.

### <a name="azure-iot-products-and-tools"></a>Produtos e ferramentas Azure IoT

- **Azure IoT Hub** - Você precisará de um [hub Azure IoT](https://docs.microsoft.com/azure/iot-hub/) na sua assinatura Azure para ligar o seu dispositivo. Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar. Se não tiver um hub IoT, [siga estas instruções para criar uma](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-using-cli).

> [!Note]
> O IoT Plug and Play está atualmente disponível em hubs IoT criados nas regiões centro-americana, norte da Europa e Japão Oriental. O suporte IoT Plug e Play não está incluído em centros IoT de nível básico. Para interagir com o seu dispositivo IoT Plug and Play, pode utilizar a ferramenta exploradorA Azure IoT. [Descarregue e instale a mais recente versão do explorador Azure IoT](./howto-use-iot-explorer.md) para o seu sistema operativo.

## <a name="iot-plug-and-play-bridge-architecture"></a>IoT Plug e Play Bridge Architecture

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-components.png" alt-text="Do lado esquerdo existem alguns sensores existentes (ligados e sem fios) a um PC Windows ou Linux contendo IoT Plug e Play Bridge. A ponte IoT Plug and Play liga-se então a um hub IoT no lado direito":::

## <a name="download-iot-plug-and-play-bridge"></a>Baixar IoT Plug e Play Bridge

Você pode baixar uma versão pré-construída da ponte com adaptadores suportados em [lançamentos de ponte IoT Plug e Play](https://aka.ms/iot-pnp-bridge-releases) e expandir a lista de ativos para o lançamento mais recente. Descarregue a versão mais recente da aplicação para o seu sistema operativo.

Também pode baixar e ver o código fonte da [ponte IoT Plug e Play no GitHub](https://aka.ms/bridge).

## <a name="next-steps"></a>Passos seguintes

Agora que tem uma visão geral da arquitetura da IoT Plug and Play bridge, os próximos passos são para saber mais sobre:

- [Como usar a ponte IoT Plug e Play](./howto-use-iot-pnp-bridge.md)
- [Consulte a referência do GitHub Developer para ioT plug and play bridge](https://aka.ms/iot-pnp-bridge-dev-doc)
- [IoT Plug e Play bridge no GitHub](https://aka.ms/iotplugandplaybridge)

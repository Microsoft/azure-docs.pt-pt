---
title: IoT Plug e Play bridge | Microsoft Docs
description: Compreenda a ponte IoT Plug and Play e como usá-la para ligar os dispositivos existentes ligados a um windows ou a um gateway Linux como dispositivos IoT Plug e Play.
author: usivagna
ms.author: ugans
ms.date: 1/20/2021
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: a45efd90043ecb4d457db7ed39651f1a9b5bbd4d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98890612"
---
# <a name="iot-plug-and-play-bridge"></a>Bridge do IoT Plug and Play

A ponte IoT Plug and Play é uma aplicação de código aberto para ligar dispositivos existentes ligados ao Windows ou ao Gateway Linux como dispositivos IoT Plug e Play. Depois de instalar e configurar a aplicação no seu Windows ou máquina Linux, pode usá-la para ligar dispositivos ligados a um hub IoT. Pode utilizar a ponte para mapear interfaces IoT Plug e Play para a telemetria que os dispositivos anexos estão a enviar, trabalhar com as propriedades do dispositivo e invocar comandos.

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-high-level.png" alt-text="Do lado esquerdo existem alguns sensores existentes (ligados e sem fios) a um PC Windows ou Linux contendo IoT Plug e Play Bridge. A ponte IoT Plug and Play liga-se então a um hub IoT no lado direito":::

A ponte IoT Plug e Play pode ser implantada como uma ponte autónoma executável em qualquer dispositivo IoT, PC industrial, servidor ou gateway que executa o Windows 10 ou Linux. Também pode ser compilado no seu código de aplicação. Um simples ficheiro JSON de configuração diz à ponte IoT Plug and Play que os dispositivos/periféricos ligados devem ser expostos até Azure.

## <a name="supported-protocols-and-sensors"></a>Protocolos e sensores suportados

A ponte IoT Plug and Play suporta por predefinição os seguintes tipos de periféricos, com ligações à documentação do adaptador:

|Periférico|Windows|Linux|
|---------|---------|---------|
|[O adaptador de sensor Bluetooth](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/bluetooth_sensor_adapter.md) liga sensores bluetooth de baixa energia (BLE) detetados.       |Yes|No|
|[O adaptador de câmara](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/camera_adapter.md) liga as câmaras a um dispositivo Windows 10.               |Yes|No|
|[O adaptador Modbus](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/modbus_adapters.md) liga sensores num dispositivo Modbus.              |Yes|Yes|
|[O adaptador MQTT](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/mqtt_adapter.md) conecta dispositivos que utilizam um corretor MQTT.                  |Yes|Yes|
|[O adaptador SerialPnP](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/serialpnp/Readme.md) conecta dispositivos que comunicam através de uma ligação em série.               |Yes|Yes|
|[Os periféricos USB](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/coredevicehealth_adapter.md) do Windows utilizam uma lista de classes de interface de dispositivos suportados por adaptador para ligar dispositivos que têm um ID de hardware específico.  |Yes|Não Aplicável|

Para aprender a estender a ponte IoT Plug and Play para suportar protocolos adicionais do dispositivo, consulte [alargar a ponte IoT Plug e Play](howto-author-pnp-bridge-adapter.md). Para aprender a construir e implantar a ponte IoT Plug and Play, consulte [Construir e implementar a ponte IoT Plug and Play](howto-build-deploy-extend-pnp-bridge.md).

## <a name="iot-plug-and-play-bridge-architecture"></a>IoT Plug e Play bridge arquitetura

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-components.png" alt-text="No lado esquerdo existem várias caixas que indicam vários periféricos ligados a um PC Windows ou Linux contendo IoT Plug e Play Bridge. A partir de cima, uma configuração com a etiqueta da caixa aponta para a ponte. A ponte liga-se então a um hub IoT no lado direito do diagrama.":::

### <a name="iot-plug-and-play-bridge-adapters"></a>Adaptadores de ponte IoT Plug e Play

A ponte IoT Plug and Play suporta um conjunto de adaptadores de ponte IoT Plug e Play para vários tipos de dispositivo. Um *adaptador manifesta-se* estáticamente define os adaptadores para uma ponte.

O gestor do adaptador de ponte utiliza o manifesto para identificar e chamar funções de adaptador. O gestor do adaptador apenas chama a função de criar nos adaptadores de ponte que são exigidos pelos componentes de interface listados no ficheiro de configuração. É criada uma instância adaptadora para cada componente IoT Plug and Play.

Um adaptador de ponte cria e adquire um cabo de interface digital twin interface. O adaptador utiliza esta pega para ligar a funcionalidade do dispositivo ao gémeo digital.

Utilizando informações no ficheiro de configuração, o adaptador de ponte utiliza as seguintes técnicas para permitir que o dispositivo completo para a comunicação digital gémea através da ponte:

- Estabelece um canal de comunicação diretamente.
- Cria um observador de dispositivos para aguardar a disponibilização de um canal de comunicação.

### <a name="configuration-file"></a>Ficheiro de configuração

A ponte IoT Plug and Play utiliza um ficheiro de configuração baseado em JSON que especifica:

- Como ligar-se a um hub IoT ou a uma aplicação IoT Central: As opções incluem cordas de ligação, parâmetros de autenticação ou Serviço de Provisionamento de Dispositivos (DPS).
- A localização dos modelos de capacidade IoT Plug e Play que a ponte utiliza. O modelo define as capacidades de um dispositivo IoT Plug and Play, e é estático e imutável.
- Uma lista de componentes de interface IoT Plug e Play e as seguintes informações para cada componente:
- O ID de interface e o nome do componente.
- O adaptador de ponte necessário para interagir com o componente.
- Informação do dispositivo de que o adaptador de ponte precisa de estabelecer comunicação com o dispositivo. Por exemplo, ID de hardware ou informações específicas para um adaptador, interface ou protocolo.
- Um subtipo ou configuração de interface do adaptador de ponte opcional se o adaptador suportar vários tipos de comunicação com dispositivos semelhantes. O exemplo mostra como um componente de sensor bluetooth pode ser configurado:

    ```json
    {
      "_comment": "Component BLE sensor",
      "pnp_bridge_component_name": "blesensor1",
      "pnp_bridge_adapter_id": "bluetooth-sensor-pnp-adapter",
      "pnp_bridge_adapter_config": {
        "bluetooth_address": "267541100483311",
        "blesensor_identity" : "Blesensor1"
      }
    }
    ```

- Uma lista opcional de parâmetros globais do adaptador de pontes. Por exemplo, o adaptador de ponte de sensor bluetooth tem um dicionário de configurações suportadas. Um componente de interface que requer o adaptador de sensor bluetooth pode escolher uma destas configurações `blesensor_identity` como:

    ```json
    {
      "pnp_bridge_adapter_global_configs": {
        "bluetooth-sensor-pnp-adapter": {
          "Blesensor1" : {
            "company_id": "0x499",
            "endianness": "big",
            "telemetry_descriptor": [
              {
                "telemetry_name": "humidity",
                "data_parse_type": "uint8",
                "data_offset": 1,
                "conversion_bias": 0,
                "conversion_coefficient": 0.5
              },
              {
                "telemetry_name": "temperature",
                "data_parse_type": "int8",
                "data_offset": 2,
                "conversion_bias": 0,
                "conversion_coefficient": 1.0
              },
              {
                "telemetry_name": "pressure",
                "data_parse_type": "int16",
                "data_offset": 4,
                "conversion_bias": 0,
                "conversion_coefficient": 1.0
              },
              {
                "telemetry_name": "acceleration_x",
                "data_parse_type": "int16",
                "data_offset": 6,
                "conversion_bias": 0,
                "conversion_coefficient": 0.00980665
              },
              {
                "telemetry_name": "acceleration_y",
                "data_parse_type": "int16",
                "data_offset": 8,
                "conversion_bias": 0,
                "conversion_coefficient": 0.00980665
              },
              {
                "telemetry_name": "acceleration_z",
                "data_parse_type": "int16",
                "data_offset": 10,
                "conversion_bias": 0,
                "conversion_coefficient": 0.00980665
              }
            ]
          }
        }
      }
    }
    ```

## <a name="download-iot-plug-and-play-bridge"></a>Baixar IoT Plug e Play Bridge

Você pode baixar uma versão pré-construída da ponte com adaptadores suportados em [lançamentos de ponte IoT Plug e Play](https://github.com/Azure/iot-plug-and-play-bridge/releases) e expandir a lista de ativos para o lançamento mais recente. Descarregue a versão mais recente da aplicação para o seu sistema operativo.

Também pode baixar e ver o código fonte da [ponte IoT Plug e Play no GitHub](https://github.com/Azure/iot-plug-and-play-bridge).

## <a name="next-steps"></a>Passos seguintes

Agora que tem uma visão geral da arquitetura da IoT Plug and Play bridge, os próximos passos são para saber mais sobre:

- [Como ligar uma amostra de ponte IoT Plug e Play que funciona no Linux ou windows ao IoT Hub](./howto-use-iot-pnp-bridge.md)
- [Construa e implemente a ponte IoT Plug e Play](howto-build-deploy-extend-pnp-bridge.md)
- [Estender a ficha ioT e jogar ponte](howto-author-pnp-bridge-adapter.md)
- [IoT Plug e Play bridge no GitHub](https://github.com/Azure/iot-plug-and-play-bridge)

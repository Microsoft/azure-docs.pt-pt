---
title: Como gerir as gémeas digitais IoT Plug e Play
description: Como gerir o dispositivo IoT Plug and Play utilizando APIs digitais de gémeos
author: prashmo
ms.author: prashmo
ms.date: 07/20/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: ddb8027c145f6a38bfcd953be66dae2943a20c3a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97654613"
---
# <a name="manage-iot-plug-and-play-digital-twins"></a>Gerir ioT plug e jogar gémeos digitais

IoT Plug and Play suporta **Obter twin digital** e atualizar operações digitais **twin** para gerir gémeos digitais. Pode utilizar as [APIs REST](/rest/api/iothub/service/digitaltwin) ou um dos [SDKs](libraries-sdks.md)de serviço.

No momento da escrita, a versão digital gémea da API é `2020-09-30` .

## <a name="update-a-digital-twin"></a>Atualize um gémeo digital

Um dispositivo IoT Plug and Play implementa um modelo descrito pela [Digital Twins Definition Language v2 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl). Os desenvolvedores de soluções podem usar a **API Digital Twin para** atualizar o estado da componente e as propriedades do twin digital.

O dispositivo IoT Plug and Play utilizado como exemplo neste artigo implementa o [modelo de Controlador de Temperatura](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) com componentes do [termóstato.](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json)

O seguinte corte mostra a resposta a um pedido **de gémeos digitais** formatado como um objeto JSON. Para saber mais sobre o formato digital twin, consulte [o Understand IoT Plug e Play digital twins](./concepts-digital-twin.md#digital-twin-example):

```json
{
    "$dtId": "sample-device",
    "serialNumber": "alwinexlepaho8329",
    "thermostat1": {
        "maxTempSinceLastReboot": 25.3,
        "targetTemperature": 20.4,
        "$metadata": {
            "targetTemperature": {
                "desiredValue": 20.4,
                "desiredVersion": 4,
                "ackVersion": 4,
                "ackCode": 200,
                "ackDescription": "Successfully executed patch",
                "lastUpdateTime": "2020-07-17T06:11:04.9309159Z"
            },
            "maxTempSinceLastReboot": {
                "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
            }
        }
    },
    "$metadata": {
        "$model": "dtmi:com:example:TemperatureController;1",
        "serialNumber": {
            "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
        }
    }
}
```

Os gémeos digitais permitem-lhe atualizar um componente ou propriedade inteiro utilizando um [Patch JSON](http://jsonpatch.com/).

Por exemplo, pode atualizar a `targetTemperature` propriedade da seguinte forma:

```json
[
    {
        "op": "add",
        "path": "/thermostat1/targetTemperature",
        "value": 21.4
    }
]
```

A atualização anterior define o valor desejado de uma propriedade no nível de componente `$metadata` correspondente, tal como mostrado no seguinte corte. IoT Hub atualiza a versão desejada da propriedade:

```json
"thermostat1": {
    "targetTemperature": 20.4,
    "$metadata": {
        "targetTemperature": {
            "desiredValue": 21.4,
            "desiredVersion": 5,
            "ackVersion": 4,
            "ackCode": 200,
            "ackDescription": "Successfully executed patch",
            "lastUpdateTime": "2020-07-17T06:11:04.9309159Z"
        }
    }
}
```

### <a name="add-replace-or-remove-a-component"></a>Adicione, substitua ou remova um componente

As operações de nível de componente requerem um marcador de objeto vazio `$metadata` dentro do valor.

Uma operação de adicionar ou substituir componentes define os valores desejados de todas as propriedades fornecidas. Também limpa os valores desejados para quaisquer propriedades writable não fornecidas com a atualização.

A remoção de um componente limpa os valores desejados de todas as propriedades imparciais presentes. Um dispositivo eventualmente sincroniza esta remoção e deixa de reportar as propriedades individuais. O componente é então removido do gémeo digital.

A seguinte amostra do Patch JSON mostra como adicionar, substituir ou remover um componente:

```json
[
    {
        "op": "add",
        "path": "/thermostat1",
        "value": {
            "targetTemperature": 21.4,
            "anotherWritableProperty": 42,
            "$metadata": {}
        }
    },
    {
        "op": "replace",
        "path": "/thermostat1",
        "value": {
            "targetTemperature": 21.4,
            "$metadata": {}
        }
    },
    {
        "op": "remove",
        "path": "/thermostat2"
    }
]
```

### <a name="add-replace-or-remove-a-property"></a>Adicionar, substituir ou remover uma propriedade

Uma operação de adição ou substituição define o valor desejado de um imóvel. O dispositivo pode sincronizar o estado e reportar uma atualização do valor juntamente com um `ack` código, versão e descrição.

A remoção de uma propriedade limpa o valor desejado da propriedade se estiver definida. O dispositivo pode então parar de reportar esta propriedade e é removido do componente. Se esta propriedade é a última no componente, então o componente também é removido.

A seguinte amostra do Patch JSON mostra como adicionar, substituir ou remover uma propriedade dentro de um componente:

```json
[
    {
        "op": "add",
        "path": "/thermostat1/targetTemperature",
        "value": 21.4
    },
    {
        "op": "replace",
        "path": "/thermostat1/anotherWritableProperty",
        "value": 42
    },
    {
        "op": "remove",
        "path": "/thermostat2/targetTemperature",
    }
]
```

### <a name="rules-for-setting-the-desired-value-of-a-digital-twin-property"></a>Regras para definir o valor desejado de uma propriedade digital twin

**Nome**

O nome de um componente ou propriedade deve ser válido nome DTDL v2.

Os caracteres permitidos são a-z, A-Z, 0-9 (não como o primeiro personagem) e sublinham (não como o primeiro ou último personagem).

Um nome pode ter 1-64 caracteres de comprimento.

**Valor patrimonial**

O valor deve ser um [DTDL v2 Propriedade](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#property)válido.

Todos os tipos primitivos são suportados. Dentro de tipos complexos, são suportados mapas e objetos. Para saber mais, consulte [DTDL v2 Schemas](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#schemas).

As propriedades não suportam matriz ou qualquer esquema complexo com uma matriz.

Uma profundidade máxima de cinco níveis é suportada para um objeto complexo.

Todos os nomes de campo dentro de objeto complexo devem ser nomes DTDL v2 válidos.

Todas as chaves do mapa devem ser nomes DTDL v2 válidos.

## <a name="troubleshoot-update-digital-twin-api-errors"></a>Resolução de problemas atualiza erros digitais da API

A API digital twin lança a seguinte mensagem de erro genérica:

`ErrorCode:ArgumentInvalid;'{propertyName}' exists within the device twin and is not digital twin conformant property. Please refer to aka.ms/dtpatch to update this to be conformant.`

Se vir este erro, certifique-se de que o patch de atualização segue [as regras para definir](#rules-for-setting-the-desired-value-of-a-digital-twin-property) o valor pretendido de uma propriedade digital twin

Quando atualizar um componente, certifique-se de que o [objeto vazio $metadata marcador](#add-replace-or-remove-a-component) está definido.

As atualizações podem falhar se os valores reportados de um dispositivo não estiverem em conformidade com as [convenções de plug-plug e play IoT](./concepts-convention.md#writable-properties).

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu sobre gémeos digitais, aqui estão alguns recursos adicionais:

- [Interagir com um dispositivo a partir da sua solução](quickstart-service.md)
- [IoT Digital Twin REST API](/rest/api/iothub/service/digitaltwin)
- [Explorador de Azure IoT](howto-use-iot-explorer.md)

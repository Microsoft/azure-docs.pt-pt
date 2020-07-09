---
title: Interfaces comuns - IoT Plug e Play Preview / Microsoft Docs
description: Descrição de interfaces comuns para desenvolvedores IoT Plug e Play
author: Philmea
ms.author: philmea
ms.date: 12/26/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 5773ec2b3ea88fa9a507b7c1b0b84bb7ea305a94
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81770479"
---
# <a name="iot-plug-and-play-preview-common-interfaces"></a>Interfaces comuns IoT Plug e Play Preview

Espera-se que todos os dispositivos IoT Plug e Play implementem algumas interfaces comuns. As interfaces comuns beneficiam as soluções IoT porque fornecem uma funcionalidade consistente. [A certificação](tutorial-build-device-certification.md) requer que o seu dispositivo implemente várias interfaces comuns. Pode recuperar definições comuns de interface a partir do repositório de modelos públicos.

## <a name="summary-of-common-interfaces"></a>Resumo das interfaces comuns

| Name | ID | Descrição | Implementado por Azure IoT SDK | Deve ser declarado no modelo de capacidade |
| -------- | -------- | -------- | -------- | -------- | -------- |
| Informação do Modelo | urn:azureiot:ModelDiscovery:ModelInformation:1 | Para que os dispositivos declarem o ID do modelo de capacidade e interfaces. Necessário para todos os dispositivos IoT Plug e Play. | Sim | Não |
| Informações digitais sobre o SDK do cliente twin digital | urn:azureiot:Cliente:SDKInformation:1 | Cliente SDK para ligar o dispositivo com Azure. Necessário para [certificação](tutorial-build-device-certification.md) | Sim | Não |
| Informações do dispositivo | urn:azureiot:DeviceManagement:DeviceInformation:1 | Informações sobre o sistema de hardware e funcionamento sobre o dispositivo. Necessário para [certificação](tutorial-build-device-certification.md) | Não | Sim |
| Definição de modelo | urn:azureiot:ModelDiscovery:ModelDefinition:1 | Para que os dispositivos declarem a definição completa para o seu modelo de capacidade e interfaces. Deve ser implementado quando as definições do modelo não são hospedadas num repositório de modelos. | Não | Sim |
| Digital Twin | urn:azureiot:ModelDiscovery:DigitalTwin:1 | Para os desenvolvedores de soluções recuperar o ID do modelo de capacidade e iDs de interface para um gémeo digital. Esta interface não é declarada ou implementada por um dispositivo IoT Plug and Play. | Não | Não |

- Implementado por Azure IoT SDK - Se o Azure IoT SDK implementa as capacidades declaradas nas interfaces. Os dispositivos IoT Plug e Play que utilizam o Azure IoT SDK não precisam de implementar esta interface.
- Deve ser declarado no modelo de capacidade - Se 'sim', esta interface deve ser declarada na `"implements":` secção do modelo de capacidade do dispositivo para este dispositivo IoT Plug and Play.

## <a name="retrieve-interface-definitions-from-the-public-repository"></a>Recuperar definições de interface do repositório público

### <a name="cli"></a>CLI

Você pode usar a extensão Azure IoT para Azure CLI para recuperar as interfaces comuns do repositório de modelo público.

```azurecli
az iot pnp interface show --interface {InterfaceID}
```

```azurecli
az iot pnp capability-model show --model {ModelID}
```

### <a name="vs-code"></a>Código VS

1. Utilize **ctrl+Shift+P** para abrir a paleta de comando.

1. Introduza **plug e play** e, em seguida, selecione o plug e play **IoT: Abra o comando do Repositório de Modelos** Abertos. Escolha **o repositório público.** O repositório de modelo público abre no Código VS.

1. No repositório de modelos públicos, insira o nome da interface no campo de pesquisa.

1. Para criar uma cópia local da interface, selecione-a nos resultados da pesquisa e, em seguida, selecione **Download**.

## <a name="next-steps"></a>Próximos passos

Agora que aprendeu sobre interfaces comuns, aqui estão alguns recursos adicionais:

- [Linguagem de definição digital twin (DTDL)](https://aka.ms/DTDL)
- [SDK de Dispositivo C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](https://docs.microsoft.com/rest/api/iothub/device)

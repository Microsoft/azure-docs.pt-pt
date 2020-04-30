---
title: Interfaces comuns - IoT Plug e Pré-visualização de reprodução / Microsoft Docs
description: Descrição das interfaces comuns para desenvolvedores ioT Plug e Play
author: Philmea
ms.author: philmea
ms.date: 12/26/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 5773ec2b3ea88fa9a507b7c1b0b84bb7ea305a94
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81770479"
---
# <a name="iot-plug-and-play-preview-common-interfaces"></a>IoT Plug e Play Preview interfaces comuns

Espera-se que todos os dispositivos IoT Plug e Play implementem algumas interfaces comuns. As interfaces comuns beneficiam as soluções IoT porque fornecem uma funcionalidade consistente. [A certificação](tutorial-build-device-certification.md) requer que o seu dispositivo implemente várias interfaces comuns. Pode obter definições comuns de interface a partir do repositório de modelos públicos.

## <a name="summary-of-common-interfaces"></a>Resumo das interfaces comuns

| Nome | ID | Descrição | Implementado por Azure IoT SDK | Deve ser declarado no modelo de capacidade |
| -------- | -------- | -------- | -------- | -------- | -------- |
| Informação do Modelo | urn:azureiot:ModelDiscovery:ModelInformation:1 | Para que os dispositivos declarem o ID e as interfaces do modelo de capacidade. Necessário para todos os dispositivos IoT Plug and Play. | Sim | Não |
| Informação Digital Twin Client SDK | urn:azureiot:Cliente:SDKInformation:1 | Client SDK para ligar o dispositivo ao Azure. Necessário para [a certificação](tutorial-build-device-certification.md) | Sim | Não |
| Informações do dispositivo | urn:azureiot:DeviceManagement:DeviceInformation:1 | Informações sobre o dispositivo e hardware e sistema operativo. Necessário para [a certificação](tutorial-build-device-certification.md) | Não | Sim |
| Definição de Modelo | urn:azureiot:ModelDiscovery:ModelDefinition:1 | Para que os dispositivos declarem a definição completa para o seu modelo de capacidade e interfaces. Deve ser implementado quando as definições de modelo não são hospedadas num repositório modelo. | Não | Sim |
| Digital Twin | urn:azureiot:ModelDiscovery:DigitalTwin:1 | Para que os desenvolvedores de soluções recuperem os ID do modelo de capacidade e os IDs de interface para um gémeo digital. Esta interface não é declarada ou implementada por um dispositivo IoT Plug and Play. | Não | Não |

- Implementado por Azure IoT SDK - Se o Azure IoT SDK implementa as capacidades declaradas nas interfaces. Os dispositivos IoT Plug and Play que utilizam o SDK Azure IoT não precisam de implementar esta interface.
- Deve ser declarado no modelo de capacidade - Se 'sim', esta interface deve ser declarada dentro da `"implements":` secção do modelo de capacidade do dispositivo para este dispositivo IoT Plug and Play.

## <a name="retrieve-interface-definitions-from-the-public-repository"></a>Recuperar definições de interface do repositório público

### <a name="cli"></a>CLI

Pode utilizar a extensão Azure IoT para o Azure CLI para recuperar as interfaces comuns do repositório de modelos públicos.

```azurecli
az iot pnp interface show --interface {InterfaceID}
```

```azurecli
az iot pnp capability-model show --model {ModelID}
```

### <a name="vs-code"></a>Código VS

1. Utilize **ctrl+Shift+P** para abrir a paleta de comando.

1. Introduza **plug and play** e, em seguida, selecione o **IoT Plug and Play: Open Model Repository** Command. Escolha **o repositório público.** O repositório de modelopúblico abre no Código VS.

1. No repositório de modelo público, introduza o nome da interface no campo de pesquisa.

1. Para criar uma cópia local da interface, selecione-a nos resultados da pesquisa e, em seguida, selecione **Download**.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu sobre interfaces comuns, aqui estão alguns recursos adicionais:

- [Linguagem de Definição Dupla Digital (DTDL)](https://aka.ms/DTDL)
- [SDK de Dispositivo C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](https://docs.microsoft.com/rest/api/iothub/device)

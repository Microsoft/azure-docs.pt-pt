---
title: Interfaces comuns – visualização de Plug and Play de IoT | Microsoft Docs
description: Descrição de interfaces comuns para desenvolvedores de Plug and Play IoT
author: ChrisGMsft
ms.author: chrisgre
ms.date: 07/16/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 848210509bf9ab0ffec35004cbb07e39d6de1bc0
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69879607"
---
# <a name="iot-plug-and-play-preview-common-interfaces"></a>Interfaces comuns da visualização de Plug and Play de IoT

Espera-se que todos os dispositivos IoT Plug and Play implementem algumas interfaces comuns. As interfaces comuns beneficiam as soluções de IoT porque fornecem funcionalidade consistente. A [certificação](tutorial-build-device-certification.md) exige que seu dispositivo implemente várias interfaces comuns. Você pode recuperar definições de interface comuns do repositório de modelos globais.

## <a name="summary-of-common-interfaces"></a>Resumo de interfaces comuns

| Name | id | Descrição | Implementado pelo SDK do Azure IoT | Deve ser declarado no modelo de funcionalidade |
| -------- | -------- | -------- | -------- | -------- | -------- |
| Informações do modelo | urn: azureiot: ModelDiscovery: ModelInformation: 1 | Para dispositivos declararem a ID e as interfaces do modelo de funcionalidade. Necessário para todos os dispositivos de Plug and Play IoT. | Sim | Não |
| Informações do SDK do cliente digital de dados | urn: azureiot: Client: SDKInformation: 1 | SDK do cliente para conectar o dispositivo com o Azure. Necessário para a [certificação](tutorial-build-device-certification.md) | Sim | Não |
| Informações do dispositivo | urn: azureiot: DeviceManagement: DeviceInformation: 1 | Informações de hardware e do sistema operacional sobre o dispositivo. Necessário para a [certificação](tutorial-build-device-certification.md) | Não | Sim |
| Definição do modelo | urn: azureiot: ModelDiscovery: ModelDefinition: 1 | Para dispositivos declarar a definição completa para seu modelo de funcionalidade e interfaces. Deve ser implementado quando as definições de modelo não são hospedadas em um repositório de modelo. | Não | Sim |
| Entrelaçar digital | urn: azureiot: ModelDiscovery: DigitalTwin: 1 | Para que os desenvolvedores de soluções recuperem a ID do modelo de funcionalidade e as IDs de interface para um ' digital. Essa interface não é declarada ou implementada por um dispositivo de Plug and Play de IoT. | Não | Não |

- Implementado pelo SDK do Azure IoT – se o SDK do Azure IoT implementa os recursos declarados nas interfaces. Os dispositivos IoT Plug and Play que usam o SDK do Azure IoT não precisam implementar essa interface.
- Deve ser declarado no modelo de funcionalidade-se ' Sim ', essa interface deverá ser declarada dentro da `"implements":` seção do modelo de funcionalidade do dispositivo para este dispositivo IOT plug and Play.

## <a name="retrieve-interface-definitions-from-the-public-repository"></a>Recuperar definições de interface do repositório público

### <a name="cli"></a>CLI

Você pode usar a extensão do Azure IoT para CLI do Azure recuperar as interfaces comuns do repositório de modelos globais.

```cmd/sh
az iot pnp interface show --interface {InterfaceID} --login {ModelRepoConnectionString}
```

```cmd/sh
az iot pnp model show --interface {InterfaceID} --login {ModelRepoConnectionString}
```

### <a name="vs-code"></a>VS Code

1. Use **Ctrl + Shift + P** para abrir a paleta de comandos.

1. Insira **plug and Play** e, em seguida **, selecione o plug and Play de IOT: Abra o comando** de repositório de modelos. Escolha **repositório de modelos globais**. O repositório de modelos globais é aberto no VS Code.

1. No repositório de modelos globais, insira o nome da interface no campo de pesquisa.

1. Para criar uma cópia local da interface, selecione-a nos resultados da pesquisa e, em seguida, selecione **baixar**.

## <a name="next-steps"></a>Passos Seguintes

Agora que você aprendeu sobre interfaces comuns, aqui estão alguns recursos adicionais:

- [DTDL (digital treme Definition Language)](https://aka.ms/DTDL)
- [SDK de dispositivo para C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [API REST de IoT](https://docs.microsoft.com/rest/api/iothub/device)

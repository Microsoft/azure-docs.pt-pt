---
title: Monitorar a conectividade do dispositivo usando o Gerenciador de IoT Central do Azure
description: Monitore mensagens do dispositivo e observe as alterações do dispositivo com a CLI do IoT Central Explorer.
author: viv-liu
ms.author: viviali
ms.date: 09/27/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: 2b1c4c64fc02df67f38e36194072efd5db3b8e38
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72953177"
---
# <a name="monitor-device-connectivity-using-azure-cli-preview-features"></a>Monitorar a conectividade do dispositivo usando o CLI do Azure (recursos de visualização)

*Este tópico se aplica a construtores e administradores.*

Use a extensão CLI do Azure IoT para ver as mensagens que seus dispositivos estão enviando para IoT Central e observar alterações no dispositivo. Você pode usar essa ferramenta para depurar e observar a conectividade do dispositivo e diagnosticar problemas de mensagens do dispositivo que não estão atingindo a nuvem ou os dispositivos que não estão respondendo a alterações de conexão.

[Visite a referência de extensões de CLI do Azure para obter mais detalhes](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/central)

## <a name="prerequisites"></a>Pré-requisitos

+ CLI do Azure instalado e é a versão 2.0.7 ou superior. Verifique a versão do CLI do Azure executando `az --version`. Saiba como instalar e atualizar a partir do [CLI do Azure docs](https://docs.microsoft.com/cli/azure/install-azure-cli)
+ Uma conta corporativa ou de estudante no Azure, adicionada como um usuário em um aplicativo IoT Central.

## <a name="install-the-iot-central-extension"></a>Instalar a extensão de IoT Central

Execute o seguinte comando na linha de comando para instalar:

```cmd/sh
az extension add --name azure-cli-iot-ext
```

Verifique a versão da extensão executando 
```cmd/sh
az --version
```
Você deve ver a extensão Azure-CLI-IOT-ext é 0.8.1 ou superior. Se não estiver, execute
```cmd/sh
az extension update --name azure-cli-iot-ext
```

## <a name="using-the-extension"></a>Usando a extensão

As seções a seguir descrevem comandos e opções comuns que você pode usar ao executar o `az iot central`. Para exibir o conjunto completo de comandos e opções, passe `--help` para `az iot central` ou qualquer um de seus subcomandos.

### <a name="login"></a>Início de Sessão

Comece entrando no CLI do Azure. 

```cmd/sh
az login
```

### <a name="get-the-application-id-of-your-iot-central-app"></a>Obter a ID do aplicativo IoT Central aplicativo
Em **Administração/configurações do aplicativo**, copie a **ID do aplicativo**. Você o usará em etapas posteriores.

### <a name="monitor-messages"></a>Monitorar mensagens
Monitore as mensagens que estão sendo enviadas para seu aplicativo IoT Central de seus dispositivos. Isso incluirá todos os cabeçalhos e anotações.

```cmd/sh
az iot central app monitor-events --app-id <app-id> --properties all
```

### <a name="view-device-properties"></a>Exibir Propriedades do dispositivo
Exibir as propriedades do dispositivo atual de leitura e leitura/gravação para um determinado dispositivo.

```cmd/sh
az iot central device-twin show --app-id <app-id> --device-id <device-id>
```

## <a name="next-steps"></a>Passos seguintes

Agora que você aprendeu a usar o IoT Central Explorer, a próxima etapa sugerida é explorar o [Gerenciamento de dispositivos IOT central](howto-manage-devices.md).

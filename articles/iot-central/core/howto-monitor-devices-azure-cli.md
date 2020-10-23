---
title: Monitor de conectividade do dispositivo utilizando o Azure IoT Central Explorer
description: Monitorize as mensagens do dispositivo e observe as alterações gémeas do dispositivo através do IoT Central Explorer CLI.
author: viv-liu
ms.author: viviali
ms.date: 03/27/2020
ms.topic: how-to
ms.service: iot-central
ms.custom: devx-track-azurecli, device-developer
services: iot-central
manager: corywink
ms.openlocfilehash: b266def0dbe81542714fc6d47986f3cde8235dd0
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92122965"
---
# <a name="monitor-device-connectivity-using-azure-cli"></a>Monitorizar a conectividade do dispositivo com a CLI do Azure

*Este tópico aplica-se aos desenvolvedores de dispositivos e aos construtores de soluções.*

Utilize a extensão Azure CLI IoT para ver mensagens que os seus dispositivos estão a enviar para a IoT Central e observe alterações no dispositivo twin. Pode utilizar esta ferramenta para depurar e observar problemas de conectividade do dispositivo e diagnosticar problemas de mensagens do dispositivo que não cheguem à nuvem ou dispositivos que não respondam a alterações gémeas.

[Visite a referência de extensões do Azure CLI para mais detalhes](/cli/azure/ext/azure-iot/iot/central?view=azure-cli-latest)

## <a name="prerequisites"></a>Pré-requisitos

+ Azure CLI instalado e é a versão 2.7.0 ou superior. Verifique a versão do seu Azure CLI em funcionamento `az --version` . Saiba como instalar e atualizar a partir dos [docs Azure CLI](/cli/azure/install-azure-cli)
+ Uma conta de trabalho ou escola em Azure, adicionada como utilizador numa aplicação IoT Central.

## <a name="install-the-iot-central-extension"></a>Instale a extensão IoT Central

Executar o seguinte comando a partir da sua linha de comando para instalar:

```azurecli
az extension add --name azure-iot
```

Verifique a versão da extensão em execução:

```azurecli
az --version
```

Deve ver que a extensão azure-iot é 0.9.9 ou superior. Se não for, corra:

```azurecli
az extension update --name azure-iot
```

## <a name="using-the-extension"></a>Usando a extensão

As seguintes secções descrevem comandos e opções comuns que pode utilizar quando correr `az iot central` . Para ver o conjunto completo de comandos e opções, passe `--help` para ou qualquer um dos seus `az iot central` subcomecos.

### <a name="login"></a>Iniciar sessão

Comece por assinar no Azure CLI. 

```azurecli
az login
```

### <a name="get-the-application-id-of-your-iot-central-app"></a>Obtenha o ID da aplicação IoT Central
Nas **Definições de Administração/Aplicação,** copie o **ID do formulário de aplicação**. Usa este valor em passos posteriores.

### <a name="monitor-messages"></a>Monitorizar mensagens
Monitorize as mensagens que estão a ser enviadas para a sua aplicação IoT Central a partir dos seus dispositivos. A saída inclui todos os cabeçalhos e anotações.

```azurecli
az iot central diagnostics monitor-events --app-id <app-id> --properties all
```

### <a name="view-device-properties"></a>Ver propriedades do dispositivo
Ver as propriedades do dispositivo de leitura e leitura/escrita atuais para um determinado dispositivo.

```azurecli
az iot central device twin show --app-id <app-id> --device-id <device-id>
```

## <a name="next-steps"></a>Passos seguintes

Se você é um desenvolvedor de dispositivos, um passo sugerido seguinte é ler sobre a [conectividade do dispositivo em Azure IoT Central](./concepts-get-connected.md).
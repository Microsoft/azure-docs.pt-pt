---
title: Monitorize a conectividade do dispositivo utilizando o Explorador Central Azure IoT
description: Monitorize as mensagens do dispositivo e observe as alterações gémeas do dispositivo através do IoT Central Explorer CLI.
author: viv-liu
ms.author: viviali
ms.date: 03/27/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: 0fa1b58547e0260ec5486003cf4cb85d855de33a
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310221"
---
# <a name="monitor-device-connectivity-using-azure-cli"></a>Monitorizar a conectividade do dispositivo com a CLI do Azure

*Este tópico aplica-se a construtores e administradores.*

Utilize a extensão Azure CLI IoT para ver mensagens que os seus dispositivos estão a enviar para a IoT Central e observar alterações no dispositivo twin. Pode utilizar esta ferramenta para depurar e observar a conectividade do dispositivo e diagnosticar problemas de mensagens de dispositivos que não chegam à nuvem ou dispositivos que não respondem a alterações duplas.

[Visite a referência de extensões azure CLI para mais detalhes](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/central?view=azure-cli-latest)

## <a name="prerequisites"></a>Pré-requisitos

+ Azure CLI instalado e é versão 2.0.7 ou superior. Verifique a versão do seu Azure CLI executando `az --version`. Saiba como instalar e atualizar a partir dos [docs do Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)
+ Uma conta de trabalho ou escola em Azure, adicionada como utilizadora numa aplicação IoT Central.

## <a name="install-the-iot-central-extension"></a>Instale a extensão IoT Central

Executar o seguinte comando a partir da sua linha de comando para instalar:

```azurecli
az extension add --name azure-iot
```

Verifique a versão da extensão executando:

```azurecli
az --version
```

Deve ver que a extensão azure-iot é de 0,8.1 ou superior. Se não for, corra:

```azurecli
az extension update --name azure-iot
```

## <a name="using-the-extension"></a>Usando a extensão

As seguintes secções descrevem comandos e opções `az iot central`comuns que pode utilizar quando executa . Para ver o conjunto completo de `--help` comandos e opções, passe para `az iot central` ou qualquer um dos seus subcomandos.

### <a name="login"></a>Iniciar sessão

Comece por assinar no Azure CLI. 

```azurecli
az login
```

### <a name="get-the-application-id-of-your-iot-central-app"></a>Obtenha o ID de aplicação da sua aplicação IoT Central
Nas Definições de **Administração/Aplicação,** copie o ID da **aplicação**. Usa este valor em etapas posteriores.

### <a name="monitor-messages"></a>Monitorizar mensagens
Monitorize as mensagens que estão a ser enviadas para a sua aplicação IoT Central a partir dos seus dispositivos. A saída inclui todos os cabeçalhos e anotações.

```azurecli
az iot central app monitor-events --app-id <app-id> --properties all
```

### <a name="view-device-properties"></a>Ver propriedades do dispositivo
Veja as propriedades atuais do dispositivo de leitura e leitura/escrita para um determinado dispositivo.

```azurecli
az iot central device-twin show --app-id <app-id> --device-id <device-id>
```

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a usar o IoT Central Explorer, o próximo passo sugerido é explorar os dispositivos de [gestão IoT Central](howto-manage-devices.md).

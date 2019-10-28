---
title: Monitorar a conectividade do dispositivo usando o Gerenciador de IoT Central do Azure
description: Monitore mensagens do dispositivo e observe as alterações do dispositivo com a CLI do IoT Central Explorer.
author: viv-liu
ms.author: viviali
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: ec3ccc0a9fa6d1ae5d6193aacd1b068f2d97afe0
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72949615"
---
# <a name="monitor-device-connectivity-using-the-azure-iot-central-explorer"></a>Monitorar a conectividade do dispositivo usando o Gerenciador de IoT Central do Azure

*Este tópico se aplica a construtores e administradores.*

Use a CLI do IoT Central Explorer para ver as mensagens que seus dispositivos estão enviando para IoT Central e observar alterações no Hub IoT. Você pode usar essa ferramenta de código-fonte aberto para obter uma visão mais profunda do estado da conectividade do dispositivo e diagnosticar problemas de mensagens do dispositivo que não chegam à nuvem ou aos dispositivos que não estão respondendo às alterações de conexão.

[Visite o repositório IOTC-Explorer no GitHub.](https://aka.ms/iotciotcexplorercligithub)

## <a name="prerequisites"></a>Pré-requisitos

+ Node. js versão 8. x ou superior- https://nodejs.org
+ Um administrador do seu aplicativo deve gerar um token de acesso para você usar no IOTC-Explorer

## <a name="install-iotc-explorer"></a>Instalar o IOTC-Explorer

Execute o seguinte comando na linha de comando para instalar:

```cmd/sh
npm install -g iotc-explorer
```

> [!NOTE]
> Normalmente, você precisa executar o comando install com `sudo` em ambientes semelhantes ao Unix.

## <a name="run-iotc-explorer"></a>Executar o IOTC-Explorer

As seções a seguir descrevem comandos e opções comuns que você pode usar ao executar o `iotc-explorer`. Para exibir o conjunto completo de comandos e opções, passe `--help` para `iotc-explorer` ou qualquer um de seus subcomandos.

### <a name="login"></a>Início de Sessão

Antes de começar, você precisa ter um administrador de seu aplicativo IoT Central para obter um token de acesso para uso. O administrador executa as seguintes etapas:

1. Navegue até **Administração** e, em seguida, **acesse tokens**.
1. Selecione **gerar token**.
    ](media/howto-use-iotc-explorer/accesstokenspage.png) captura de tela da página token de acesso do ![

1. Insira um nome de token, selecione **Avançar**e, em seguida, **copiar**.
    > [!NOTE]
    > O valor do token é mostrado apenas uma vez, portanto, ele deve ser copiado antes de fechar a caixa de diálogo. Depois de fechar a caixa de diálogo, ela nunca é mostrada novamente.

    ![Captura de tela da caixa de diálogo copiar token de acesso](media/howto-use-iotc-explorer/copyaccesstoken.png)

Você pode usar o token para fazer logon na CLI da seguinte maneira:

```cmd/sh
iotc-explorer login "<Token value>"
```

Se preferir não ter o token persistido no histórico do Shell, você poderá deixar o token fora e, em vez disso, fornecê-lo quando solicitado:

```cmd/sh
iotc-explorer login
```

### <a name="monitor-device-messages"></a>Monitorar mensagens do dispositivo

Você pode observar as mensagens provenientes de um dispositivo específico ou de todos os dispositivos em seu aplicativo usando o comando `monitor-messages`. Esse comando inicia um observador que gera continuamente novas mensagens à medida que elas chegam:

Para assistir a todos os dispositivos em seu aplicativo, execute o seguinte comando:

```cmd/sh
iotc-explorer monitor-messages
```

Saída:

![monitor-saída de comando de mensagens](media/howto-use-iotc-explorer/monitormessages.png)

Para assistir a um dispositivo específico, basta adicionar a ID do dispositivo ao final do comando:

```cmd/sh
iotc-explorer monitor-messages <your-device-id>
```

Você também pode gerar um formato mais amigável adicionando a opção `--raw` ao comando:

```cmd/sh
iotc-explorer monitor-messages --raw
```

### <a name="get-device-twin"></a>Obter o dispositivo de entrelaçamento

Você pode usar o comando `get-twin` para obter o conteúdo de um dispositivo de IoT Central. Para fazer isso, execute o seguinte comando:

```cmd/sh
iotc-explorer get-twin <your-device-id>
```

Saída:

![saída do comando Get-bitreme](media/howto-use-iotc-explorer/getdevicetwin.png)

Assim como ocorre com `monitor-messages`, você pode obter uma saída mais amigável passando a opção `--raw`:

```cmd/sh
iotc-explorer get-twin <your-device-id> --raw
```

## <a name="next-steps"></a>Passos seguintes

Agora que você aprendeu a usar o IoT Central Explorer, a próxima etapa sugerida é explorar o [Gerenciamento de dispositivos IOT central](howto-manage-devices.md).

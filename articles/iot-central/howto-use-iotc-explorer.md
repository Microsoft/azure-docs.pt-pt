---
title: Monitorizar a conectividade do dispositivo com o Azure IoT Central Explorer
description: Monitorizar mensagens do dispositivo e observe as alterações de twin do dispositivo através da CLI do IoT Central Explorer.
author: viv-liu
ms.author: viviali
ms.date: 02/20/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 16cb27ab330118d1bb59cf4f3d782bf55fa28d43
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57779747"
---
# <a name="monitor-device-connectivity-using-the-azure-iot-central-explorer"></a>Monitorizar a conectividade do dispositivo com o Azure IoT Central Explorer

*Este tópico aplica-se para criadores e administradores.*

Utilize a CLI do IoT Central Explorer para ver mensagens de seus dispositivos estão a enviar para o IoT Central e observar alterações no duplo IoT Hub. Pode utilizar esta ferramenta de código-fonte aberto para obter informações mais aprofundadas sobre o estado de conectividade do dispositivo e diagnosticar problemas de mensagens do dispositivo não alcance a cloud ou não a responder a alterações de twin de dispositivos.

[Visite o repositório de iotc-explorer no GitHub.](https://aka.ms/iotciotcexplorercligithub)

## <a name="prerequisites"></a>Pré-requisitos

+ Versão node. js 8.x ou superior - https://nodejs.org
+ Um administrador da sua aplicação tem de gerar um token de acesso para utilizar no Explorador de iotc

## <a name="install-iotc-explorer"></a>Instalar o Explorador de iotc

Execute o seguinte comando a partir da linha de comando para instalar:

```cmd/sh
npm install -g iotc-explorer
```

> [!NOTE]
> Normalmente, tem de executar o comando de instalação com `sudo` em ambientes de família UNIX.

## <a name="run-iotc-explorer"></a>Executar o Explorador de iotc

As secções seguintes descrevem as opções que pode utilizar ao executar e comandos comuns `iotc-explorer`. Para ver o conjunto completo de comandos e opções, de transmitir `--help` para `iotc-explorer` ou qualquer um dos respetivos subcomandos.

### <a name="login"></a>Iniciar sessão

Antes de começar, tem de ter um administrador da sua aplicação do Centro de IoT para obter um token de acesso para utilizar. O administrador utiliza os seguintes passos:

1. Navegue para **Administration** , em seguida, **Tokens de acesso**.
1. Selecione **gerar Token**.
    ![Captura de ecrã de página token de acesso](media/howto-use-iotc-explorer/accesstokenspage.png)

1. Introduza um nome de Token, selecione **próxima**e, em seguida **cópia**.
    > [!NOTE]
    > O valor do token é mostrado apenas uma vez, para que ele deve ser copiado antes de fechar a caixa de diálogo. Depois de fechar a caixa de diálogo, ele nunca apareça novamente.

    ![Captura de ecrã de caixa de diálogo token de acesso de cópia](media/howto-use-iotc-explorer/copyaccesstoken.png)

Pode usar o token para iniciar sessão para a CLI da seguinte forma:

```cmd/sh
iotc-explorer login "<Token value>"
```

Se não tem o token que permanecem no seu histórico de shell, pode deixar o token de expansão e em vez disso, fornecendo-quando lhe for pedido:

```cmd/sh
iotc-explorer login
```

### <a name="monitor-device-messages"></a>Monitorizar mensagens do dispositivo

Pode ver as mensagens recebidas a partir de um dispositivo específico ou todos os dispositivos na sua aplicação utilizando o `monitor-messages` comando. Este comando inicia um observador que produz continuamente novas mensagens à medida que chegam:

Para ver todos os dispositivos na sua aplicação, execute o seguinte comando:

```cmd/sh
iotc-explorer monitor-messages
```

Saída:

![saída do comando de mensagens de monitor](media/howto-use-iotc-explorer/monitormessages.png)

Para ver um dispositivo específico, basta adicione o id de dispositivo para o final do comando:

```cmd/sh
iotc-explorer monitor-messages <your-device-id>
```

Também pode emitir um formato mais amigáveis a máquina, adicionando o `--raw` opção para o comando:

```
iotc-explorer monitor-messages --raw
```

### <a name="get-device-twin"></a>Obter o dispositivo duplo

Pode utilizar o `get-twin` comando para obter o conteúdo do duplo para um dispositivo de IoT Central. Para tal, execute o seguinte comando:

```cmd/sh
iotc-explorer get-twin <your-device-id>
```

Saída:

![saída do comando GET-twin](media/howto-use-iotc-explorer/getdevicetwin.png)

Tal como acontece com `monitor-messages`, pode obter um mais máquina saída amigável ao transmitir o `--raw` opção:

```cmd/sh
iotc-explorer get-twin <your-device-id> --raw
```

## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu como utilizar o Explorador de Central de IoT, a próxima etapa sugerida é explorar [gestão de dispositivos IoT Central](howto-manage-devices.md).

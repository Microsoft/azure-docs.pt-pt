---
title: Monitorizar a conectividade do dispositivo com o Azure IoT Central Explorer
description: Monitorizar mensagens do dispositivo e observe as alterações de twin do dispositivo através da CLI do IoT Central Explorer.
author: viv-liu
ms.author: viviali
ms.date: 09/12/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 39359054818b81c0f5f2cdc9413ae2a0c1dc8c65
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57310272"
---
# <a name="monitor-device-connectivity-using-the-azure-iot-central-explorer"></a>Monitorizar a conectividade do dispositivo com o Azure IoT Central Explorer

*Este tópico aplica-se para criadores e administradores.*

Utilize a CLI do IoT Central Explorer para ver mensagens de seus dispositivos estão a enviar para o IoT Central e observar alterações no duplo IoT Hub. Pode utilizar esta ferramenta de código-fonte aberto para obter informações mais aprofundadas sobre o estado de conectividade do dispositivo e diagnosticar problemas de mensagens do dispositivo não alcance a cloud ou não a responder a alterações de twin de dispositivos.

## <a name="visit-the-iotc-explorer-repo-in-githubhttpsakamsiotciotcexplorercligithub"></a>[Visite o repositório de iotc-explorer no GitHub](https://aka.ms/iotciotcexplorercligithub)

## <a name="prerequisites"></a>Pré-requisitos
+ Versão node. js 8.x ou superior - https://nodejs.org
+ Terá de o administrador da sua aplicação para gerar um token de acesso para utilizar no Explorador de iotc

## <a name="installing-iotc-explorer"></a>Instalar iotc-explorer

Execute o seguinte comando a partir da linha de comando para instalar:

```
npm install -g iotc-explorer
```

> [!NOTE]
> Normalmente, terá de executar o comando de instalação com `sudo` em ambientes de família UNIX.

## <a name="running-iotc-explorer"></a>Executar iotc-explorer

Seguem-se alguns comandos e opções comuns que podem ser executadas ao usar `iotc-explorer`. Para ver o conjunto completo de comandos e opções, pode passar `--help` para `iotc-explorer` ou qualquer um dos respetivos subcomandos.

### <a name="login"></a>Iniciar sessão

Antes de começar, tem de ter um administrador da sua aplicação do Centro de IoT para obter um token de acesso para utilizar. O administrador utiliza os seguintes passos:
1. Aceda a **Tokens de acesso deadministração/**. 
1. Selecione **gerar**.
![Captura de ecrã de página token de acesso](media/howto-use-iotc-explorer/accesstokenspage.png)

1. Introduza um nome de Token, selecione **próxima**, e **copie o valor de Token**.
    > [!NOTE]
    > O valor do token só será apresentado uma vez, para que ele deve ser copiado antes de fechar a caixa de diálogo. Depois de fechar a caixa de diálogo, ele nunca verá novamente.

    ![Captura de ecrã de caixa de diálogo token de acesso de cópia](media/howto-use-iotc-explorer/copyaccesstoken.png)

Em seguida, pode utilizar esse token para iniciar sessão para a CLI ao executar:

```sh
iotc-explorer login "<Token value>"
```

Se não precisará o token que permanecem no seu histórico de shell, pode deixar o token de expansão e em vez disso, fornecendo-quando lhe for pedido:

```
iotc-explorer login
```

### <a name="monitor-device-messages"></a>Monitorizar mensagens do dispositivo

Pode ver as mensagens recebidas a partir de um dispositivo específico ou todos os dispositivos na sua aplicação utilizando o `monitor-messages` comando. Esta ação iniciará um observador que produzirá continuamente novas mensagens que estes.

Para ver todos os dispositivos na sua aplicação, execute o seguinte comando:

```
iotc-explorer monitor-messages
```
Saída: ![saída do comando de mensagens de monitor](media/howto-use-iotc-explorer/monitormessages.PNG)

Para ver um dispositivo específico, basta adicione o ID de dispositivo para o final do comando:

```
iotc-explorer monitor-messages <your-device-id>
```

Também pode ter o comando num formato mais amigáveis a máquina de saída ao adicionar o `--raw` opção para o comando:

```
iotc-explorer monitor-messages --raw
```

### <a name="get-device-twin"></a>Obter o dispositivo duplo

Pode utilizar o `get-twin` comando para obter o conteúdo do duplo para um dispositivo de IoT Central. Para tal, execute o seguinte comando:

```
iotc-explorer get-twin <your-device-id>
```

Saída: ![saída do comando get-twin](media/howto-use-iotc-explorer/getdevicetwin.PNG)

Tal como acontece com `monitor-messages`, pode obter um mais máquina saída amigável ao transmitir o `--raw` opção:

```
iotc-explorer get-twin <your-device-id> --raw
```

## <a name="next-steps"></a>Passos Seguintes
Agora que sabe como utilizar o Explorador de Central de IoT, a próxima etapa sugerida é explorar [gestão de dispositivos IoT Central](howto-manage-devices.md).

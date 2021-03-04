---
title: Problemas de resolução de problemas com módulos Azure Percept Audio e fala
description: Obtenha dicas de resolução de problemas para algumas das questões mais comuns encontradas durante a experiência de embarque
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: a3877ea680e7b4c705f127c54e0fa10c45d3b51d
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102097980"
---
# <a name="azure-percept-audio-and-speech-module-troubleshooting"></a>Azure Percept Audio e resolução de problemas do módulo de fala

Utilize as diretrizes abaixo para resolver problemas de aplicação de assistente de voz.

## <a name="collecting-speech-module-logs"></a>Coletando registos de módulos de fala

Para executar estes comandos, [ligue-se ao Azure Percept DK Wi-Fi ponto de acesso e ligue-se ao kit dev sobre SSH](./how-to-ssh-into-percept-dk.md) e introduza os comandos no terminal SSH.

```console
 iotedge logs azureearspeechclientmodule
```

Para redirecionar qualquer saída para um ficheiro .txt para análise mais aprofundada, utilize a seguinte sintaxe:

```console
[command] > [file name].txt
```

Depois de redirecionar a saída para um ficheiro .txt, copie o ficheiro para o seu PC anfitrião via SCP:

```console
scp [remote username]@[IP address]:[remote file path]/[file name].txt [local host file path]
```

[Caminho de ficheiro de anfitrião local] refere-se à localização no seu PC anfitrião ao qual gostaria de copiar o ficheiro .txt. [Nome de utilizador remoto] é o nome de utilizador SSH escolhido durante a [experiência de embarque](./quickstart-percept-dk-set-up.md). Se não tiver configurado um login SSH durante a experiência de embarque do Azure Percept DK, o seu nome de utilizador remoto é raiz.

## <a name="checking-runtime-status-of-the-speech-module"></a>Verificação do estado de funcionamento do módulo de fala

Verifique se o estado de funcionamento do **azureearspeechclientmodule** mostra como **funcionamento**. Para localizar o estado de funcionamento dos módulos do seu dispositivo, abra o [portal Azure](https://portal.azure.com/) e navegue para **todos os recursos**  ->  **\<your IoT hub>**  ->  **IoT Edge**  ->  **\<your device ID>** . Clique no **separador Módulos** para ver o estado de funcionação de todos os módulos instalados.

:::image type="content" source="./media/troubleshoot-audio-accessory-speech-module/over-the-air-iot-edge-device-page.png" alt-text="Página do dispositivo de borda no portal Azure.":::

Se o estado de funcionamento do **azureearspeechclientmodule** não estiver listado como **funcionamento,** clique em **Definir módulos**  ->  **azureearspeechclientmodule**. Na página **Definições** do Módulo, defina o **Estado Desejado** para **executar** e clique em **Atualizar**.

## <a name="understanding-ear-som-led-indicators"></a>Compreender os indicadores LED do SoM do Ouvido

Pode utilizar indicadores LED para perceber em que estado se encontra o dispositivo. Normalmente, o módulo demora cerca de 2 minutos a inicializar completamente após *a alimentação.* À medida que passa pelos passos de inicialização, verá:

1. 1 LED branco central - o dispositivo está ligado. 
2. 1 led branco central piscando - a autenticação está em andamento. 
3. Os três LEDs mudarão para azul assim que o dispositivo for autenticado e pronto a ser utilizado.

|LED|   Estado LED|  Estado do Som do Ouvido|
|---|------------|----------------| 
|L02|   1x branco, estática em |Ligado |
|L02|   1x branco, 0,5 Hz piscando|  Autenticação em curso |
|L01 & L02 & L03|   3x azul, estática em|     À espera da palavra-chave|
|L01 & L02 & L03|   Matriz LED piscando, 20fps | Ouvir ou falar|
|L01 & L02 & L03|   Corrida de matriz led, 20fps|    Pensar|
|L01 & L02 & L03|   3x vermelho, estática em | Mudo|

## <a name="next-steps"></a>Passos seguintes

Consulte o [guia geral de resolução de problemas](./troubleshoot-dev-kit.md) para obter mais informações sobre a resolução de problemas do seu Azure Percept DK.

---
title: Problemas de resolução de problemas com Azure Percept Audio e o módulo de fala
description: Obtenha dicas de resolução de problemas para Azure Percept Audio e azureearspeechclientmodule
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: template-how-to
ms.openlocfilehash: c4fc7d7564ecd30326fbec832639b2a81d55e6d5
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105605659"
---
# <a name="azure-percept-audio-and-speech-module-troubleshooting"></a>Azure Percept Audio e resolução de problemas do módulo de fala

Utilize as diretrizes abaixo para resolver problemas de aplicação de assistente de voz.

## <a name="collecting-speech-module-logs"></a>Coletando registos de módulos de fala

Para executar estes comandos, [sSH no kit dev](./how-to-ssh-into-percept-dk.md) e insira os comandos no pedido do cliente SSH.

Recolher registos de módulos de fala:

```console
sudo iotedge logs azureearspeechclientmodule
```

Para redirecionar a saída para um ficheiro .txt para análise mais aprofundada, utilize a seguinte sintaxe:

```console
sudo [command] > [file name].txt
```

Altere as permissões do ficheiro .txt para que possa ser copiado:

```console
sudo chmod 666 [file name].txt
```

Depois de redirecionar a saída para um ficheiro .txt, copie o ficheiro para o seu PC anfitrião via SCP:

```console
scp [remote username]@[IP address]:[remote file path]/[file name].txt [local host file path]
```

[Caminho de ficheiro de anfitrião local] refere-se à localização no seu PC anfitrião ao qual gostaria de copiar o ficheiro .txt. [nome de utilizador remoto] é o nome de utilizador SSH escolhido durante a [experiência de configuração](./quickstart-percept-dk-set-up.md).

## <a name="checking-runtime-status-of-the-speech-module"></a>Verificação do estado de funcionamento do módulo de fala

Verifique se o estado de funcionamento do **azureearspeechclientmodule** mostra como **funcionamento**. Para localizar o estado de funcionamento dos módulos do seu dispositivo, abra o [portal Azure](https://portal.azure.com/) e navegue para **todos os recursos**  ->  **[o seu hub IoT]**  ->  **IoT Edge**  ->  **[iD do seu dispositivo]**. Clique no **separador Módulos** para ver o estado de funcionação de todos os módulos instalados.

:::image type="content" source="./media/troubleshoot-audio-accessory-speech-module/over-the-air-iot-edge-device-page.png" alt-text="Página do dispositivo de borda no portal Azure.":::

Se o estado de funcionamento do **azureearspeechclientmodule** não estiver listado como **funcionamento,** clique em **Definir módulos**  ->  **azureearspeechclientmodule**. Na página **Definições** do Módulo, defina o **Estado Desejado** para **executar** e clique em **Atualizar**.

## <a name="understanding-ear-som-led-indicators"></a>Compreender os indicadores LED do SoM do Ouvido

Pode utilizar indicadores LED para perceber em que estado se encontra o dispositivo. Normalmente, o módulo demora cerca de 2 minutos a inicializar completamente depois de o dispositivo se ter ligado. À medida que passa pelos passos de inicialização, verá:

1. Led branco centralizado (estático): o dispositivo está ligado.
2. Led branco central ligado (piscando): a autenticação está em andamento.
3. Os três LEDs mudarão para azul assim que o dispositivo for autenticado e pronto a ser utilizado.

|LED|Estado LED|Estado do Som do Ouvido|
|---|---------|--------------|
|L02|1x branco, estática em|Ligado |
|L02|1x branco, 0,5 Hz piscando|Autenticação em curso |
|L01 & L02 & L03|3x azul, estática em|À espera da palavra-chave|
|L01 & L02 & L03|Matriz LED piscando, 20fps |Ouvir ou falar|
|L01 & L02 & L03|Corrida de matriz led, 20fps|Pensar|
|L01 & L02 & L03|3x vermelho, estática em |Mudo|

## <a name="next-steps"></a>Passos seguintes

Consulte o [guia geral de resolução de problemas](./troubleshoot-dev-kit.md) para obter mais informações sobre a resolução de problemas do seu Azure Percept DK.

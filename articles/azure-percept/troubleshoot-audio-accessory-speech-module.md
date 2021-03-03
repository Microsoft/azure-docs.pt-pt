---
title: Problemas de resolução de problemas com módulos Azure Percept Audio e fala
description: Obtenha dicas de resolução de problemas para algumas das questões mais comuns encontradas durante a experiência de embarque
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 1e2ad920afb55066f07430568f976154f6d7cae1
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101680132"
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

[Caminho de ficheiro de anfitrião local] refere-se à localização no seu PC anfitrião ao qual gostaria de copiar o ficheiro .txt. [nome de utilizador remoto] é o nome de utilizador SSH escolhido durante a [experiência de configuração](./quickstart-percept-dk-set-up.md). Se não tiver configurado um login SSH durante o OOBE, o seu nome de utilizador remoto é raiz.

## <a name="checking-runtime-status-of-the-speech-module"></a>Verificação do estado de funcionamento do módulo de fala

Verifique se o estado de funcionamento do **azureearspeechclientmodule** mostra como **funcionamento**. Para localizar o estado de funcionamento dos módulos do seu dispositivo, abra o [portal Azure](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home) e navegue para **todos os recursos**  ->  **\<your IoT hub>**  ->  **IoT Edge**  ->  **\<your device ID>** . Clique no **separador Módulos** para ver o estado de funcionação de todos os módulos instalados.

:::image type="content" source="./media/troubleshoot-audio-accessory-speech-module/over-the-air-iot-edge-device-page.png" alt-text="Página do dispositivo de borda no portal Azure.":::

Se o estado de funcionamento do **azureearspeechclientmodule** não estiver listado como **funcionamento,** clique em **Definir módulos**  ->  **azureearspeechclientmodule**. Na página **Definições** do Módulo, defina o **Estado Desejado** para **executar** e clique em **Atualizar**.

:::image type="content" source="./media/troubleshoot-audio-accessory-speech-module/firmware-desired-status-stopped.png" alt-text="Definir o ecrã dos módulos no portal Azure.":::

## <a name="understanding-ear-som-led-indicators"></a>Compreender os indicadores LED do SoM do Ouvido

Pode utilizar indicadores LED para perceber em que estado se encontra o dispositivo. Normalmente, o módulo demora cerca de 2 minutos a inicializar completamente após *a alimentação.* À medida que passa pelos passos de inicialização, verá:

1. 1 luz verde esquerda - o aparelho está ligado. 
2. 1 luz verde esquerda e LED central piscando verde - a autenticação está em andamento. 
3. Os três LEDs mudarão para azul assim que o dispositivo for autenticado e pronto a ser utilizado.

|Estado LED                  |Estado do Som do Ouvido            |
|----------------------------|---------------------------|
|1x verde (LED esquerdo)         |poder em |
|1x verde (LED esquerdo) <br> 1x verde piscando (LED central) |autenticação em curso |
|3x fora                      |inicialização concluída |
|3x azul                     |pronto para ser usado |
|3x azul piscando            |palavra-chave reconhecida |
|3x racing azul              |processamento |
|3x vermelho                      |mudo |

## <a name="next-steps"></a>Passos seguintes

Consulte o [guia geral de resolução de problemas](./troubleshoot-dev-kit.md) para obter mais informações sobre a resolução de problemas do seu Azure Percept DK.
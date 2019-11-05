---
title: Usar o gêmeos do dispositivo do Azure para controlar o LED de usuário do MXChip IoT DevKit | Microsoft Docs
description: Neste tutorial, saiba como monitorar os Estados do DevKit e controlar o LED do usuário com o dispositivo gêmeos do Hub IoT do Azure.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/04/2018
ms.author: liydu
ms.openlocfilehash: deb1ea8c7b41ad48bddebfbed1b15c667ee0071a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73483938"
---
# <a name="mxchip-iot-devkit"></a>MXChip IoT DevKit

Você pode usar este exemplo para monitorar as informações do MXChip IoT DevKit WiFi e os Estados do sensor e controlar a cor do LED do usuário usando o dispositivo gêmeos do Hub IoT do Azure.

## <a name="what-you-learn"></a>O que irá aprender

- Como monitorar os Estados do sensor do MXChip IoT DevKit.

- Como usar o dispositivo gêmeos do Azure para controlar a cor do LED RGB do DevKit.

## <a name="what-you-need"></a>Do que precisa

- Configure seu ambiente de desenvolvimento seguindo o [Guia de introdução](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started).

- Na janela do terminal do GitBash (ou outra interface de linha de comando do git), digite os seguintes comandos:

   ```bash
   git clone https://github.com/DevKitExamples/DevKitState.git
   cd DevKitState
   code .
   ```

## <a name="provision-azure-services"></a>Provisionar serviços do Azure

1. Clique no menu suspenso **tarefas** no Visual Studio Code e selecione **Executar tarefa...**  - **provisionar nuvem**.

2. Seu progresso é exibido na guia **terminal** do painel de **boas-vindas** .

3. Quando receber a mensagem que a *assinatura deseja escolher*, selecione uma assinatura.

4. Selecione ou escolha um grupo de recursos. 
 
   > [!NOTE]
   > Se você já tiver um hub IoT gratuito, poderá ignorar esta etapa.

5. Quando receber a mensagem que o *Hub IOT deseja escolher*, selecione ou crie um hub IOT.

6. Algo semelhante ao *aplicativo de funções: nome do aplicativo de funções: xxx*, é exibido. Anote o nome do aplicativo de funções; Ele será usado em uma etapa posterior.

7. Aguarde até que a implantação do modelo de Azure Resource Manager seja concluída, o que é indicado quando a *implantação do modelo do Resource Manager de mensagem: concluído* é exibido.

## <a name="deploy-function-app"></a>Implantar Aplicativo de funções

1. Clique no menu suspenso **tarefas** no Visual Studio Code e selecione **Executar tarefa...**  - **nuvem-implantar**.

2. Aguarde a conclusão do processo de carregamento do código do aplicativo de funções; o aplicativo de função de mensagem *implanta: Done* é exibido.

## <a name="configure-iot-hub-device-connection-string-in-devkit"></a>Configurar a cadeia de conexão do dispositivo do Hub IoT no DevKit

1. Conecte seu MXChip IoT DevKit ao seu computador.

2. Clique no menu suspenso **tarefas** no Visual Studio Code e selecione **Executar tarefa...**  - **config-Device-Connection**

3. No MXChip IoT DevKit, pressione e segure o botão **a**, pressione o botão **Redefinir** e solte o botão **a** para fazer com que o DekKit entre no modo de configuração.

4. Aguarde o processo de configuração da cadeia de conexão ser concluído.

## <a name="upload-arduino-code-to-devkit"></a>Carregar código Arduino para DevKit

Com seu MXChip IoT DevKit conectado ao seu computador:

1. Clique no menu suspenso **tarefas** no Visual Studio Code e selecione **Executar tarefa de compilação...** O esboço Arduino é compilado e carregado no DevKit.

2. Quando o esboço foi carregado com êxito, uma mensagem *criar & carregar esboço: êxito* é exibida.

## <a name="monitor-devkit-state-in-browser"></a>Monitorar o estado do DevKit no navegador

1. Em um navegador da Web, abra o arquivo `DevKitState\web\index.html`, que foi criado durante a etapa o que você precisa.

2. A seguinte página da Web é exibida:![Especifique o nome do aplicativo de funções.](media/iot-hub-arduino-iot-devkit-az3166-devkit-state/devkit-state-function-app-name.png)

3. Insira o nome do aplicativo de funções que você anotou anteriormente.

4. Clique no botão **conectar**

5. Em alguns segundos, a página é atualizada e exibe o status da conexão WiFi do DevKit e o estado de cada um dos sensores integrados.

## <a name="control-the-devkits-user-led"></a>Controlar o LED do usuário do DevKit

1. Clique no gráfico de LED do usuário na ilustração da página da Web.

2. Em alguns segundos, a tela é atualizada e mostra o status de cor atual do LED do usuário.

3. Tente alterar o valor de cor do LED RGB clicando em vários locais nos controles deslizantes RGB.

## <a name="example-operation"></a>Operação de exemplo

![Exemplo de procedimento de teste](media/iot-hub-arduino-iot-devkit-az3166-devkit-state/devkit-state.gif)

> [!NOTE]
> Você pode ver dados brutos do dispositivo de entrada no portal do Azure: Hub IoT-\> dispositivos IoT-\> *\<seu dispositivo\>*  -dispositivo de \>.

## <a name="next-steps"></a>Passos seguintes

Você aprendeu como:
- Conecte um dispositivo MXChip IoT DevKit ao acelerador de solução de monitoramento remoto do Azure IoT.
- Use a função gêmeos do dispositivo IoT do Azure para detectar e controlar a cor do LED RGB do DevKit.

Aqui estão as próximas etapas sugeridas:

* [Visão geral do acelerador de solução de monitoramento remoto do Azure IoT](https://docs.microsoft.com/azure/iot-suite/)
* [Conectar um dispositivo MXChip IoT DevKit ao aplicativo IoT Central do Azure](/azure/iot-central/core/howto-connect-devkit)

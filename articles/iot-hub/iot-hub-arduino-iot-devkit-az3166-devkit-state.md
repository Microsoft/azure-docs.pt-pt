---
title: Use gémeos dispositivo Azure para controlar o LED do utilizador MXChip IoT DevKit ! Microsoft Docs
description: Neste tutorial, aprenda a monitorizar os estados do DevKit e controle o LED do utilizador com gémeos do dispositivo Azure IoT Hub.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/04/2018
ms.author: liydu
ms.openlocfilehash: 8c43c8a0b9fdf30b5ce5ae6ecbf123b563099ff6
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92148783"
---
# <a name="mxchip-iot-devkit"></a>MXChip IoT DevKit

Pode utilizar este exemplo para monitorizar os estados de informação e sensor wi-fi do MXChip IoT DevKit e controlar a cor do LED do utilizador utilizando gémeos do dispositivo Azure IoT Hub.

## <a name="what-you-learn"></a>O que irá aprender

- Como monitorizar os estados do sensor MXChip IoT DevKit.

- Como utilizar gémeos dispositivoS Azure para controlar a cor do LED RGB do DevKit.

## <a name="what-you-need"></a>O que precisa

- Configurar o seu ambiente de desenvolvimento seguindo o [Guia de Arranque.](./iot-hub-arduino-iot-devkit-az3166-get-started.md)

- A partir da sua janela de terminal GitBash (ou outra interface de linha de comando Git), digite os seguintes comandos:

   ```bash
   git clone https://github.com/DevKitExamples/DevKitState.git
   cd DevKitState
   code .
   ```

## <a name="provision-azure-services"></a>Prestação de Serviços Azure

1. Clique no menu de down-down **tasks** no Código do Estúdio Visual e selecione **Executar Tarefa...**  -  **provisão para nuvens**.

2. O seu progresso é apresentado no **separador TERMINAL** do painel **Welcome.**

3. Quando solicitado com a mensagem *Que subscrição gostaria de escolher,* selecione uma subscrição.

4. Selecione ou escolha um grupo de recursos. 
 
   > [!NOTE]
   > Se já tem um Hub IoT gratuito, pode saltar este passo.

5. Quando solicitado com a mensagem *O hub IoT gostaria de escolher,* selecione ou crie um Hub IoT.

6. Algo semelhante à *aplicação de função: nome da aplicação da função: xxx,* é exibido. Anota o nome da aplicação de função; será usado num passo posterior.

7. Aguarde o fim da implementação do modelo do Gestor de Recursos Azure, que é indicado quando a *implementação* do modelo do Gestor de Recursos de mensagem: Feito é exibido.

## <a name="deploy-function-app"></a>Implementar app de função

1. Clique no menu de down-down **tasks** no Código do Estúdio Visual e selecione **Executar Tarefa...**  -  **implantação em nuvens**.

2. Aguarde o processo de carregamento do código de aplicação da função para terminar; a *aplicação da função* de mensagem implementa: O feito é exibido.

## <a name="configure-iot-hub-device-connection-string-in-devkit"></a>Configurar a cadeia de ligação do dispositivo do hub IoT em DevKit

1. Ligue o seu MXChip IoT DevKit ao seu computador.

2. Clique no menu de down-down **tasks** no Código do Estúdio Visual e selecione **Executar Tarefa...**  -  **ligação config-dispositivo**

3. No MXChip IoT DevKit, prima e mantenha o botão **A**, pressione o botão **Reset** e, em seguida, desbloqueie o botão **A** para fazer com que o DekKit introduza o modo de configuração.

4. Aguarde que o processo de configuração da cadeia de ligação seja concluído.

## <a name="upload-arduino-code-to-devkit"></a>Faça upload do Código Arduino para DevKit

Com o seu MXChip IoT DevKit ligado ao seu computador:

1. Clique no menu de down-down **tasks** no Código do Estúdio Visual e selecione **Run Build Task...** O esboço do Arduino é compilado e enviado para o DevKit.

2. Quando o esboço tiver sido carregado com sucesso, é apresentada uma *Build & Upload Sketch:* mensagem de sucesso.

## <a name="monitor-devkit-state-in-browser"></a>Monitor DevKit Estado no Browser

1. Num navegador Web, abra o `DevKitState\web\index.html` ficheiro que foi criado durante o passo que precisa.

2. Aparece a seguinte página Web:![Especifique o nome da aplicação de função.](media/iot-hub-arduino-iot-devkit-az3166-devkit-state/devkit-state-function-app-name.png)

3. Insira o nome da aplicação de função que escreveu anteriormente.

4. Clique no botão **'Ligar'**

5. Em poucos segundos, a página atualiza e exibe o estado de ligação Wi-Fi do DevKit e o estado de cada um dos sensores de bordo.

## <a name="control-the-devkits-user-led"></a>Controle o LED de utilizador do DevKit

1. Clique no gráfico LED do utilizador na ilustração da página Web.

2. Em poucos segundos, o ecrã atualiza-se e mostra o estado atual da cor do LED do utilizador.

3. Tente alterar o valor de cor do LED RGB clicando em vários locais nos comandos de slider RGB.

## <a name="example-operation"></a>Operação exemplo

![Procedimento de teste de exemplo](media/iot-hub-arduino-iot-devkit-az3166-devkit-state/devkit-state.gif)

> [!NOTE]
> Pode ver dados brutos do dispositivo twin no portal Azure: IoT Hub - \> dispositivos IoT - \> *\<your device\>*  - \> Device Twin.

## <a name="next-steps"></a>Passos seguintes

Aprendeu a:
- Ligue um dispositivo MXChip IoT DevKit ao seu acelerador de solução de monitorização remota Azure IoT.
- Utilize o dispositivo Azure IoT que os gémeos funcionam para sentir e controlar a cor do LED RGB do DevKit.

Eis o próximo passo sugerido: Visão geral do [acelerador de solução de monitorização remota Azure IoT](/azure/iot-suite/)
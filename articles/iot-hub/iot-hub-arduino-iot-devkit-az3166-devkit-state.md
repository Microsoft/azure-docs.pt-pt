---
title: Utilize gémeos de dispositivo Saque para controlar o LED do utilizador MXChip IoT DevKit [ Microsoft Docs
description: Neste tutorial, aprenda a monitorizar os estados de DevKit e controle o LED do utilizador com gémeos dispositivos Azure IoT Hub.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/04/2018
ms.author: liydu
ms.openlocfilehash: deb1ea8c7b41ad48bddebfbed1b15c667ee0071a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73483938"
---
# <a name="mxchip-iot-devkit"></a>MXChip IoT DevKit

Pode utilizar este exemplo para monitorizar as informações e os estados de sensores WiFi MXChip IoT DevKit e para controlar a cor do LED do utilizador utilizando gémeos dispositivos Azure IoT Hub.

## <a name="what-you-learn"></a>O que irá aprender

- Como monitorizar os estados do sensor MXChip IoT DevKit.

- Como utilizar gémeos do dispositivo Azure para controlar a cor do LED RGB do DevKit.

## <a name="what-you-need"></a>Do que precisa

- Instale o seu ambiente de desenvolvimento seguindo o [Guia de Início de Partida](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started).

- A partir da janela do terminal GitBash (ou outra interface de linha de comando Git), escreva os seguintes comandos:

   ```bash
   git clone https://github.com/DevKitExamples/DevKitState.git
   cd DevKitState
   code .
   ```

## <a name="provision-azure-services"></a>Serviços Azure de Prestação

1. Clique no menu de entrega de **Tarefas** no Código do Estúdio Visual e selecione **Executar Tarefa...**  -  **fornecimento de nuvens.**

2. O seu progresso é apresentado sob o **separador TERMINAL** do painel **Welcome.**

3. Quando solicitado com a mensagem *Que subscrição gostaria*de escolher, selecione uma subscrição.

4. Selecione ou escolha um grupo de recursos. 
 
   > [!NOTE]
   > Se já tem um Hub IoT gratuito, pode saltar este passo.

5. Quando solicitado com a mensagem *O hub IoT gostaria*de escolher, selecione ou crie um Hub IoT.

6. Algo semelhante à *aplicação de funções: nome da aplicação de função: xxx,* é apresentado. Escreva o nome da aplicação de função; será usado num passo posterior.

7. Aguarde a implementação do modelo do Gestor de Recursos Azure para terminar, o que é indicado quando a implementação do modelo do Gestor de *Recursos da mensagem: Está apresentada.*

## <a name="deploy-function-app"></a>Implementar app de funções

1. Clique no menu de entrega de **Tarefas** no Código do Estúdio Visual e selecione **Executar Tarefa...**  -  **cloud-deploy**.

2. Aguarde o processo de upload do código da aplicação de função para terminar; a *aplicação* de função de mensagem é implementada: É apresentado.

## <a name="configure-iot-hub-device-connection-string-in-devkit"></a>Configure IoT Hub Device Connection String em DevKit

1. Ligue o seu MXChip IoT DevKit ao seu computador.

2. Clique no menu de entrega de **Tarefas** no Código do Estúdio Visual e selecione **Executar Tarefa...**  -  **config-dispositivo-ligação**

3. No MXChip IoT DevKit, prima e segure o botão **A,** prima o botão **Reset** e, em seguida, descarregue o botão **A** para que o DekKit introduza o modo de configuração.

4. Aguarde que o processo de configuração da cadeia de ligação esteja concluído.

## <a name="upload-arduino-code-to-devkit"></a>Faça upload do Código Arduino para DevKit

Com o seu MXChip IoT DevKit ligado ao seu computador:

1. Clique no menu de entrega de **tarefas** no Código do Estúdio Visual e selecione **Executar Build Task...** O esboço arduino é compilado e enviado para o DevKit.

2. Quando o esboço tiver sido carregado com sucesso, é apresentado um *Esboço de Upload de & Build:* mensagem de sucesso.

## <a name="monitor-devkit-state-in-browser"></a>Monitor DevKit State in Browser

1. Num navegador Web, `DevKitState\web\index.html` abra o ficheiro -- que foi criado durante o passo o que precisa.

2. Aparece a seguinte página Web:![Especifique o nome da aplicação de função.](media/iot-hub-arduino-iot-devkit-az3166-devkit-state/devkit-state-function-app-name.png)

3. Insera o nome da aplicação de função que escreveste anteriormente.

4. Clique no botão **Ligar**

5. Em poucos segundos, a página atualiza e exibe o estado de ligação Wi-Fi do DevKit e o estado de cada um dos sensores a bordo.

## <a name="control-the-devkits-user-led"></a>Controlar o LED de Utilizador do DevKit

1. Clique no gráfico LED do utilizador na ilustração da página Web.

2. Em poucos segundos, o ecrã atualiza-se e mostra o estado atual da cor do LED do utilizador.

3. Tente alterar o valor de cor do LED RGB clicando em vários locais nos comandos de slider RGB.

## <a name="example-operation"></a>Operação de exemplo

![Procedimento de teste de exemplo](media/iot-hub-arduino-iot-devkit-az3166-devkit-state/devkit-state.gif)

> [!NOTE]
> Pode ver dados brutos do dispositivo twin no portal\> Azure: IoT Hub - dispositivos IoT -\> * \<o seu dispositivo\> *  - \> Device Twin.

## <a name="next-steps"></a>Passos seguintes

Aprendeu a:
- Ligue um dispositivo MXChip IoT DevKit ao seu acelerador de solução de monitorização remota Azure IoT.
- Utilize a função de gémeos do dispositivo Azure IoT para sentir e controlar a cor do LED RGB do DevKit.

Aqui estão os próximos passos sugeridos:

* [Visão geral do acelerador de solução de monitorização remota Azure IoT](https://docs.microsoft.com/azure/iot-suite/)
* [Ligue um dispositivo MXChip IoT DevKit à sua aplicação Central Azure IoT](/azure/iot-central/core/howto-connect-devkit)

---
title: Utilizar dispositivos duplos do Azure para controlar MXChip IoT DevKit LED de utilizador | Documentos da Microsoft
description: Neste tutorial, saiba como monitorizar os Estados de DevKit e controlar o utilizador LED com gémeos de dispositivo do IoT Hub do Azure.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/04/2018
ms.author: liydu
ms.openlocfilehash: e955d21132dda6caa137ad3b5de9d00ccf7ed1b4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61369853"
---
# <a name="mxchip-iot-devkit"></a>MXChip IoT DevKit

Pode utilizar este exemplo, para monitorizar os Estados de informações e sensor MXChip IoT DevKit Wi-Fi e controlar a cor do LED de utilizador através de gémeos de dispositivo do IoT Hub do Azure.

## <a name="what-you-learn"></a>O que irá aprender

- Como monitorizar os Estados de sensor MXChip IoT DevKit.

- Como utilizar dispositivos duplos do Azure para controlar a cor do DevKit RGB LED.

## <a name="what-you-need"></a>Do que precisa

- Configurar o ambiente de desenvolvimento ao seguir a [guia de introdução](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started).

- Na janela de terminal do GitBash (ou outra interface de linha de comandos do Git), escreva os seguintes comandos:

   ```bash
   git clone https://github.com/DevKitExamples/DevKitState.git
   cd DevKitState
   code .
   ```

## <a name="provision-azure-services"></a>Aprovisionar serviços do Azure

1. Clique nas **tarefas** menu pendente no Visual Studio Code e selecione **executar tarefa...**   -  **cloud-aprovisionar**.

2. O progresso é apresentado no **TERMINAL** separador da **boas-vindas** painel.

3. Quando lhe for pedido com a mensagem *que subscrição gostaria de escolha*, selecione uma subscrição.

4. Selecione ou escolha um grupo de recursos. 
 
   > [!NOTE]
   > Se já tiver um IoT Hub gratuito, pode ignorar este passo.

5. Quando lhe for pedido com a mensagem *o hub IoT que gostaria de escolha*, selecione ou crie um IoT Hub.

6. Algo semelhante à *aplicação de funções: nome da aplicação: xxx*, é apresentada. Anote o nome da aplicação de função; será utilizado num passo posterior.

7. Aguarde que a implementação do modelo do Azure Resource Manager concluir, que é indicado quando a mensagem *implementação de modelo do Resource Manager: Feito* é apresentado.

## <a name="deploy-function-app"></a>Implementar a aplicação de função

1. Clique nas **tarefas** menu pendente no Visual Studio Code e selecione **executar tarefa...**   -  **cloud-implementar**.

2. Aguarde para carregar o processo terminar; código de aplicação de função a mensagem *implementa a aplicação de funções: Feito* é apresentado.

## <a name="configure-iot-hub-device-connection-string-in-devkit"></a>Configurar a cadeia de ligação de dispositivos do IoT Hub DevKit

1. Ligar o seu MXChip IoT DevKit ao seu computador.

2. Clique nas **tarefas** menu pendente no Visual Studio Code e selecione **executar tarefa...**   -  **ligação de dispositivos de configuração**

3. Sobre o MXChip IoT DevKit, prima e botão de suspensão **uma**, prima a **repor** botões e, em seguida, versão **A** para tornar o DekKit entrar no modo de configuração.

4. A aguardar que o processo de configuração de cadeia de ligação a ser concluída.

## <a name="upload-arduino-code-to-devkit"></a>Carregar o código de Arduino para DevKit

Com o seu MXChip IoT DevKit ligado ao seu computador:

1. Clique nas **tarefas** menu pendente no Visual Studio Code e selecione **executar tarefa de criar...** O esboço de Arduino é compilado e carregado para o DevKit.

2. Quando o esboço tiver sido carregado com êxito, um *criar e carregar criar esboços: êxito* é apresentada.

## <a name="monitor-devkit-state-in-browser"></a>Estado do monitor DevKit no Browser

1. Num browser, abra o `DevKitState\web\index.html` file--o que foi criado durante o qual tem de passo.

2. É apresentada a página Web seguinte:![Especifique o nome de aplicação de função.](media/iot-hub-arduino-iot-devkit-az3166-devkit-state/devkit-state-function-app-name.png)

3. Introduza o nome da aplicação de função que escrevi, anteriormente.

4. Clique nas **Connect** botão

5. Dentro de alguns segundos, a página é atualizada e apresenta o estado da ligação Wi-Fi a DevKit e o estado de cada um dos sensores de carregar.

## <a name="control-the-devkits-user-led"></a>Controlar utilizador o DevKit LED

1. Clique no gráfico de LED de usuário na ilustração da página da Web.

2. Dentro de alguns segundos, a tela é atualizada e mostra o estado de cor atual do utilizador LED.

3. Tente alterar o valor de cor do RGB LED ao clicar em vários locais nos controles de controlo de deslize RGB.

## <a name="example-operation"></a>Operação de exemplo

![Procedimento de teste de exemplo](media/iot-hub-arduino-iot-devkit-az3166-devkit-state/devkit-state.gif)

> [!NOTE]
> Pode ver dados não processados do dispositivo duplo no portal do Azure: O IoT Hub -\> dispositivos de IoT -\> *\<seu dispositivo\>*  - \> dispositivo duplo.

## <a name="next-steps"></a>Passos Seguintes

Sabe como:
- Ligue um dispositivo de MXChip IoT DevKit ao seu acelerador de solução de monitorização remota do Azure IoT.
- Utilize a função de gémeos de dispositivo do IoT do Azure para detetar e controlar a cor do DevKit RGB LED.

Eis os passos sugeridos seguintes:

* [Monitorização remota do IoT solution accelerator descrição geral do Azure](https://docs.microsoft.com/azure/iot-suite/)
* [Ligar um dispositivo de MXChip IoT DevKit a sua aplicação do Azure IoT Central](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit)

---
ms.openlocfilehash: 94044e95e83742487a0d4d650814a5324f07011a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101749959"
---
O manifesto de implantação define quais os módulos que são implantados num dispositivo de borda. Também define definições de configuração para esses módulos. 

Siga estes passos para gerar o manifesto a partir do ficheiro do modelo e, em seguida, implante-o para o dispositivo de borda.

1. Abra o Visual Studio Code.
1. Ao lado do painel **AZURE IOT HUB,** selecione o ícone **Mais ações** para definir a cadeia de ligação IoT Hub. Pode copiar o string a partir da *src/cloud-to-device-console-app/appsettings.jsno* ficheiro. 

    ![Definir cadeia de conexão IOT](../../../media/quickstarts/set-iotconnection-string.png)

> [!NOTE]
> Pode ser-lhe pedido que forneça informações sobre o ponto final incorporado para o IoT Hub. Para obter essa informação, no portal Azure, navegue até ao seu Hub IoT e procure a opção **de pontos finais incorporados** no painel de navegação esquerdo. Clique lá e procure o **ponto final compatível com o Event Hub** na secção de ponto final **compatível com o Event Hub.** Copie e use o texto na caixa. O ponto final será mais ou menos assim:  
    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```

1. Clique à direita **src/edge/deployment.template.js** e **selecione Generate IoT Edge Deployment Manifest**.

    ![Gere o manifesto de implantação IoT Edge](../../../media/quickstarts/generate-iot-edge-deployment-manifest.png)

    Esta ação deve criar um ficheiro manifesto denominado *deployment.amd64.jsna* pasta *src/edge/config.*
1. Clique com o **botão direito src/edge/config/deployment.amd64.jsligado**, selecione **Criar Implementação para Dispositivo Único** e, em seguida, selecione o nome do seu dispositivo de borda.

    ![Criar uma implementação para um único dispositivo](../../../media/quickstarts/create-deployment-single-device.png)

1. Quando lhe for solicitado que selecione um dispositivo IoT Hub, escolha o **dispositivo de amostra de Lva** no menu suspenso.
1. Após cerca de 30 segundos, no canto inferior esquerdo da janela, refresque o Azure IoT Hub. O dispositivo de borda mostra agora os seguintes módulos implantados:

    * Vídeo ao vivo Analytics no IoT Edge (nome do `lvaEdge` módulo)
    * Real-Time simulador de protocolo de streaming (RTSP) (nome do `rtspsim` módulo)

O módulo simulador RTSP simula uma transmissão de vídeo ao vivo utilizando um ficheiro de vídeo que foi copiado para o seu dispositivo de borda quando executou o [script de configuração de recursos live video analytics](https://github.com/Azure/live-video-analytics/tree/master/edge/setup). 

> [!NOTE]
> Se estiver a utilizar o seu próprio dispositivo de borda em vez do previsto no nosso script de configuração, vá ao seu dispositivo de borda e execute os seguintes comandos com **direitos de administração**, para puxar e armazenar o ficheiro de vídeo de amostra utilizado para este arranque rápido:  

```
mkdir /home/lvaedgeuser/samples      
mkdir /home/lvaedgeuser/samples/input    
curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaedgeuser/samples/input/camera-300s.mkv  
chown -R lvalvaedgeuser:localusergroup /home/lvaedgeuser/samples/  
```
Nesta fase, os módulos são implantados, mas nenhum gráfico de mídia está ativo.

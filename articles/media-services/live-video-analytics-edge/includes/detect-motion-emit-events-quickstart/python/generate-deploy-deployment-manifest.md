---
ms.openlocfilehash: 38d96ccb9f2c7b24e57b1096996df1ea760aca37
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88691132"
---
## <a name="generate-and-deploy-the-deployment-manifest"></a>Gerar e implementar o manifesto de implantação

O manifesto de implantação define quais os módulos que são implantados num dispositivo de borda. Também define definições de configuração para esses módulos. 

Siga estes passos para gerar o manifesto a partir do ficheiro do modelo e, em seguida, implante-o para o dispositivo de borda.

1. Abra o Visual Studio Code.
1. Ao lado do painel **AZURE IOT HUB,** selecione o ícone **Mais ações** para definir a cadeia de ligação IoT Hub. Pode copiar o string a partir da *src/cloud-to-device-console-app/appsettings.jsno* ficheiro. 

    ![Definir cadeia de conexão IOT](../../../media/quickstarts/set-iotconnection-string.png)
1. Clique à direita **src/edge/deployment.template.js** e **selecione Generate IoT Edge Deployment Manifest**.

    ![Gere o manifesto de implantação IoT Edge](../../../media/quickstarts/generate-iot-edge-deployment-manifest.png)

    Esta ação deve criar um ficheiro manifesto denominado *deployment.amd64.jsna* pasta *src/edge/config.*
1. Clique com o **botão direito src/edge/config/deployment.amd64.jsligado**, selecione **Criar Implementação para Dispositivo Único**e, em seguida, selecione o nome do seu dispositivo de borda.

    ![Criar uma implementação para um único dispositivo](../../../media/quickstarts/create-deployment-single-device.png)

1. Quando lhe for solicitado que selecione um dispositivo IoT Hub, escolha o **dispositivo de amostra de Lva** no menu suspenso.
1. Após cerca de 30 segundos, no canto inferior esquerdo da janela, refresque o Azure IoT Hub. O dispositivo de borda mostra agora os seguintes módulos implantados:

    * Vídeo ao vivo Analytics no IoT Edge (nome do `lvaEdge` módulo)
    * Simulador de protocolo de streaming em tempo real (RTSP) (nome do `rtspsim` módulo)

O módulo simulador RTSP simula uma transmissão de vídeo ao vivo utilizando um ficheiro de vídeo que foi copiado para o seu dispositivo de borda quando executou o [script de configuração de recursos live video analytics](https://github.com/Azure/live-video-analytics/tree/master/edge/setup). 

> [!NOTE]
> Se estiver a utilizar o seu próprio dispositivo de borda em vez do previsto no nosso script de configuração, vá ao seu dispositivo de borda e execute os seguintes comandos com **direitos de administração**, para puxar e armazenar o ficheiro de vídeo de amostra utilizado para este arranque rápido:  

```
mkdir /home/lvaadmin/samples      
mkdir /home/lvaadmin/samples/input    
curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaadmin/samples/input/camera-300s.mkv  
chown -R lvaadmin /home/lvaadmin/samples/  
```
Nesta fase, os módulos são implantados, mas nenhum gráfico de mídia está ativo.

---
ms.openlocfilehash: e46a56742ab8b98c53c1cd05e840e8ad4b8a73da
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "88682305"
---
No [Modo de Geração e implementação do passo manifesto de implantação IoT Edge,](../../../detect-motion-emit-events-quickstart.md#generate-and-deploy-the-deployment-manifest) no Código do Estúdio Visual, expanda o nó **lva-sample-device** sob **AZURE IOT HUB** (na secção inferior esquerda). Deve ver os seguintes módulos implantados:

* O módulo live video analytics, nomeado `lvaEdge`
* O `rtspsim` módulo, que simula um servidor RTSP que funciona como a fonte de um feed de vídeo ao vivo

  ![Módulos](../../../media/quickstarts/lva-sample-device-node.png)

> [!NOTE]
> Se estiver a utilizar o seu próprio dispositivo de borda em vez do previsto no nosso script de configuração, vá ao seu dispositivo de borda e execute os seguintes comandos com **direitos de administração**, para puxar e armazenar o ficheiro de vídeo de amostra utilizado para este arranque rápido:  

```
mkdir /home/lvaadmin/samples
mkdir /home/lvaadmin/samples/input    
curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaadmin/samples/input/camera-300s.mkv  
chown -R lvaadmin /home/lvaadmin/samples/  
```

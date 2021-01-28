---
ms.openlocfilehash: 53052097fa6616f889b710c58488a9f7a616168d
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98956338"
---
No [Modo de Geração e implementação do passo manifesto de implantação IoT Edge,](../../../detect-motion-emit-events-quickstart.md#generate-and-deploy-the-deployment-manifest) no Código do Estúdio Visual, expanda o nó **lva-sample-device** sob **AZURE IOT HUB** (na secção inferior esquerda). Deve ver os seguintes módulos implantados:

* O módulo live video analytics, nomeado `lvaEdge`
* O `rtspsim` módulo, que simula um servidor RTSP que funciona como a fonte de um feed de vídeo ao vivo

  ![Módulos](../../../media/quickstarts/lva-sample-device-node.png)

> [!NOTE]
> Os passos acima estão assumindo que está a usar a máquina virtual criada pelo script de configuração. Se estiver a utilizar o seu próprio dispositivo de borda, vá ao seu dispositivo de borda e execute os seguintes comandos com **direitos de administração**, para puxar e armazenar o ficheiro de vídeo de amostra utilizado para este arranque rápido:  

```
mkdir /home/lvaadmin/samples
mkdir /home/lvaadmin/samples/input    
curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaadmin/samples/input/camera-300s.mkv  
chown -R lvaadmin /home/lvaadmin/samples/  
```

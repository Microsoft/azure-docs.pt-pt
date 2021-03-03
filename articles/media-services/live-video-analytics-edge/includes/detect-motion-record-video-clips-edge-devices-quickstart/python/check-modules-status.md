---
ms.openlocfilehash: 3f92bae608284c8b619be34a0e08f15e831bf88e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750444"
---
No [Modo de Geração e implementação do passo manifesto de implantação IoT Edge,](../../../detect-motion-emit-events-quickstart.md#generate-and-deploy-the-deployment-manifest) no Código do Estúdio Visual, expanda o nó **lva-sample-device** sob **AZURE IOT HUB** (na secção inferior esquerda). Deve ver os seguintes módulos implantados:

* O módulo live video analytics, nomeado `lvaEdge`
* O `rtspsim` módulo, que simula um servidor RTSP que funciona como a fonte de um feed de vídeo ao vivo

  ![Módulos](../../../media/quickstarts/lva-sample-device-node.png)

> [!NOTE]
> Os passos acima estão assumindo que está a usar a máquina virtual criada pelo script de configuração. Se estiver a utilizar o seu próprio dispositivo de borda, vá ao seu dispositivo de borda e execute os seguintes comandos com **direitos de administração**, para puxar e armazenar o ficheiro de vídeo de amostra utilizado para este arranque rápido:  

```
mkdir /home/lvaedgeuser/samples
mkdir /home/lvaedgeuser/samples/input    
curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaedgeuser/samples/input/camera-300s.mkv  
chown -R lvalvaedgeuser:localusergroup /home/lvaedgeuser/samples/  
```

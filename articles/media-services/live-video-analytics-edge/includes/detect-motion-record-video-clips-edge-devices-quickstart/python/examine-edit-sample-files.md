---
ms.openlocfilehash: 6ea9be8e7e67a8e52412e7011cfb1d33c9929191
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97486732"
---
Como parte dos pré-requisitos para este arranque rápido, descarregou o código de amostra para uma pasta. Siga estes passos para examinar e editar o código de amostra.

1. No Código do Estúdio Visual, vá ao *src/edge*. Você vê o seu ficheiro *.env* e alguns ficheiros de modelo de implementação.

    O modelo de implantação refere-se ao manifesto de implantação do dispositivo de borda, onde são utilizadas variáveis para algumas propriedades. O ficheiro *.env* inclui os valores para essas variáveis.
1. Aceda à pasta *src/cloud-to-device-app.* Aqui vê-se a *appsettings.jsficheiro* e alguns outros ficheiros:
    * ***operations.jsem*** - A lista de operações que quer que o programa seja executado.
    * ***main.py*** - O código do programa de amostra. Este código:

        * Carrega as definições da aplicação.
        * Invoca métodos diretos que o live video analytics no módulo IoT Edge expõe. Pode utilizar o módulo para analisar streams de vídeo ao vivo invocando os seus [métodos diretos.](../../../direct-methods.md) 
        * Pausa para que possa examinar a saída do programa na janela **TERMINAL** e examinar os eventos gerados pelo módulo na janela **OUTPUT.**
        * Invoca métodos diretos para limpar recursos.

1. Editar o *operations.jsno* ficheiro:
    * Altere a ligação para a topologia do gráfico:

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-motion-files/2.0/topology.json"`
    * Em `GraphInstanceSet` , editar o nome da topologia do gráfico para corresponder ao valor no link anterior:
    
      `"topologyName" : "EVRToFilesOnMotionDetection"`
    * Edite o URL RTSP para apontar para o ficheiro de vídeo:

        `"value": "rtsp://rtspsim:554/media/lots_015.mkv"`
    * Em `GraphTopologyDelete` , editar o nome:

        `"name": "EVRToFilesOnMotionDetection"`

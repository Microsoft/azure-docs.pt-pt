---
ms.openlocfilehash: 426c735dfd0d015cdc1a734edde9d336fb88cfbc
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/14/2020
ms.locfileid: "97486728"
---
Como parte dos pré-requisitos para este arranque rápido, descarregou o código de amostra para uma pasta. Siga estes passos para examinar e editar o código de amostra.

1. No Código do Estúdio Visual, vá ao *src/edge*. Você vê o seu ficheiro *.env* e alguns ficheiros de modelo de implementação.

    O modelo de implantação refere-se ao manifesto de implantação do dispositivo de borda, onde são utilizadas variáveis para algumas propriedades. O ficheiro *.env* inclui os valores para essas variáveis.
1. Aceda à pasta *src/cloud-to-device-app.* Aqui vê-se a *appsettings.jsficheiro* e alguns outros ficheiros:
    * ***c2d-console-app.csproj** _ - O ficheiro do projeto para Visual Studio Code.
    _ ***operations.jsem** _ - A lista de operações que pretende que o programa seja executado.
    _ ***Program.cs** _ - O código do programa de amostra. Este código:

        _ Carrega as definições da aplicação.
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

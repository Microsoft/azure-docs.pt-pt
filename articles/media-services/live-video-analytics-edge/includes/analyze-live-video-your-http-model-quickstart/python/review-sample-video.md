---
ms.openlocfilehash: 3c0d6dbba6ecda7ef87e4978d522da27cb6f9833
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98061174"
---
Quando configura os recursos Azure, um pequeno vídeo de tráfego rodoviário é copiado para o Linux VM em Azure que você está usando como o dispositivo IoT Edge. Este quickstart utiliza o ficheiro de vídeo para simular uma transmissão em direto.

Abra uma aplicação como o [leitor de mídia VLC](https://www.videolan.org/vlc/). Selecione Ctrl+N e, em seguida, cole um link para [o vídeo](https://lvamedia.blob.core.windows.net/public/camera-300s.mkv) para iniciar a reprodução. Vê-se as imagens de muitos veículos a moverem-se no trânsito rodoviário.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LTY4]

Neste arranque rápido, você usará Live Video Analytics no IoT Edge para detetar objetos como veículos e pessoas. Publicará eventos de inferência associados ao IoT Edge Hub.

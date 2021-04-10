---
ms.openlocfilehash: df6137e4e00ebd089ef7fb5efa163a513fb30728
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98061149"
---
Ao configurar os recursos Azure para este quickstart, um pequeno vídeo de um estacionamento é copiado para o Linux VM em Azure que é usado como o dispositivo IoT Edge. Este ficheiro de vídeo será usado para simular um live stream para este tutorial.

Abra uma aplicação como [o leitor de mídia VLC](https://www.videolan.org/vlc/), selecione Ctrl+N e cole [esta ligação](https://lvamedia.blob.core.windows.net/public/lots_015.mkv) ao vídeo do estacionamento para iniciar a reprodução. Com cerca de 5 segundos, um carro branco move-se pelo estacionamento.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LUbN]

Complete os seguintes passos para utilizar o Live Video Analytics no IoT Edge para detetar o movimento do carro e gravar um videoclip a partir da marca de 5 segundos.

---
ms.openlocfilehash: 46e8609be2ec57600e666ef9dab33eae2900e1ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88691123"
---
1. No Código do Estúdio Visual, vá ao *src/edge*. Você verá o ficheiro *.env* e alguns ficheiros de modelo de implementação.

    O modelo de implantação refere-se ao manifesto de implantação do dispositivo de borda, onde são utilizadas variáveis para algumas propriedades. O ficheiro *.env* contém os valores para essas variáveis.
1. Aceda à pasta *src/cloud-to-device-app.* Aqui vê-se a *appsettings.jsficheiro* e alguns outros ficheiros:

    * ***operations.jsem*** - Uma lista das operações que quer que o programa seja executado.
    * **main.py** - O código do programa de amostra. Este código:
    
      * Carrega as definições da aplicação.
      * Invoca métodos diretos que são expostos pelo Live Video Analytics no módulo IoT Edge. Pode utilizar o módulo para analisar streams de vídeo ao vivo invocando os seus [métodos diretos.](../../../direct-methods.md)
      * Pausa para que possa examinar a saída do programa na janela **TERMINAL** e examinar os eventos gerados pelo módulo na janela **OUTPUT.**
      * Invoca métodos diretos para limpar recursos.   


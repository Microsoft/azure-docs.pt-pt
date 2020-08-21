---
ms.openlocfilehash: 1e3ba4b39baa045f35c232fa97c14bc78d8de5ca
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88691114"
---
1. No Código do Estúdio Visual, vá ao *src/edge*. Você verá o ficheiro *.env* e alguns ficheiros de modelo de implementação.

    O modelo de implantação refere-se ao manifesto de implantação do dispositivo de borda, onde são utilizadas variáveis para algumas propriedades. O ficheiro *.env* contém os valores para essas variáveis.
1. Aceda à pasta *src/cloud-to-device-app.* Aqui vê-se a *appsettings.jsficheiro* e alguns outros ficheiros:

    * ***c2d-console-app.csproj*** - O ficheiro do projeto para Visual Studio Code.
    * ***operations.jsem*** - Uma lista das operações que quer que o programa seja executado.
    * ***Program.cs*** - O código do programa de amostra. Este código:
    
      * Carrega as definições da aplicação.
      * Invoca métodos diretos que são expostos pelo Live Video Analytics no módulo IoT Edge. Pode utilizar o módulo para analisar streams de vídeo ao vivo invocando os seus [métodos diretos.](../../../direct-methods.md)
      * Pausa para que possa examinar a saída do programa na janela **TERMINAL** e examinar os eventos gerados pelo módulo na janela **OUTPUT.**
      * Invoca métodos diretos para limpar recursos.   

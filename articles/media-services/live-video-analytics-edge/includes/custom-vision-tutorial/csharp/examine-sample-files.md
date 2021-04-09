---
author: russell-cooks
ms.service: media-services
ms.topic: include
ms.date: 10/05/2020
ms.author: russellcooks
ms.openlocfilehash: 359c5f93516ea6f0561865bd86e4f51dedb4c3a5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "94358288"
---
1. No Código do Estúdio Visual, navegue para src/edge. Verá o ficheiro .env que criou juntamente com alguns ficheiros de modelos de implementação.

    O modelo de implantação refere-se ao manifesto de implantação do dispositivo de borda com alguns valores de espaço reservado. O ficheiro .env tem os valores para essas variáveis.
1. Em seguida, navegue na pasta src/cloud-to-device-console-app. Aqui verá o appsettings.jsno ficheiro que criou juntamente com outros ficheiros:

    * c2d-console-app.csproj: Este é o ficheiro do projeto para o Código do Estúdio Visual.
    * operations.jsem: Este ficheiro lista as diferentes operações que pretende que o programa seja executado.
    * Programa.cs: Este código de programa de amostras:

        * Carrega as definições da aplicação.
        * Invoca o Live Video Analytics nos métodos diretos do módulo IoT Edge para criar topologia, instantaneamente o gráfico e ativar o gráfico.
        * Pausas para examinar a saída do gráfico na janela **TERMINAL** e os eventos enviados para o hub IoT na janela **OUTPUT.**
        * Desativa a instância do gráfico, elimina a instância do gráfico e elimina a topologia do gráfico.

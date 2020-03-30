---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/06/2019
ms.author: crdun
ms.openlocfilehash: 8d7731480b6239c572d39f52b6a0217d2ac48d25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "66240247"
---
1. Navegue para o ficheiro de solução no projeto do cliente (.sln) e abra-o usando o Visual Studio.

2. No Visual Studio, escolha a plataforma de solução (Android, iOS ou Windows) a partir da lista pendente junto à seta de início. Selecione um dispositivo de implementação específico ou emulador ao clicar na lista pendente na seta verde. Pode utilizar a plataforma Android e o emulador Ripple predefinidos. Tutoriais mais avançados (por exemplo, notificações push) irão requerer que selecione um dispositivo ou emulador suportados.

3. Abra o `ToDoActivity.java` ficheiro nesta pasta - ZUMOAPPNAME/app/src/main/java/com/example/zumoappname. O nome `ZUMOAPPNAME`da aplicação é .

4. Vá ao [portal Azure](https://portal.azure.com/) e navegue para a aplicação móvel que criou. Na `Overview` lâmina, procure o URL que é o ponto final público da sua aplicação móvel. Exemplo - o nome de site para o meu https://test123.azurewebsites.netnome de aplicação "test123" será .

5. Vá ao `index.js` ficheiro em ZUMOAPPNAME/www/js/index.js e no `onDeviceReady()` método, substitua `ZUMOAPPURL` o parâmetro por ponto final público acima.

    `client = new WindowsAzure.MobileServiceClient('ZUMOAPPURL');`
    
    torna-se
    
    `client = new WindowsAzure.MobileServiceClient('https://test123.azurewebsites.net');`
    
6. Para criar e executar a aplicação Cordova, prima F5 ou clique na seta verde. Se for apresentada uma caixa de diálogo de segurança no emulador a solicitar acesso à rede, aceite.

7. Depois de a aplicação ser iniciada no dispositivo ou emulador, digite texto significativo em **novo texto**, como completar *o tutorial* e, em seguida, clique no botão **Adicionar.**

Os dados de inserções de back-end do pedido encontram-se na tabela TodoItem na Base de Dados SQL e devolvem informações sobre os itens recentemente armazenados na aplicação móvel. A aplicação móvel apresenta estes dados na lista.

Pode repetir os passos 3 a 5 para outras plataformas.
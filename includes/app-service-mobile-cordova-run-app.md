---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/06/2019
ms.author: crdun
ms.openlocfilehash: 8d7731480b6239c572d39f52b6a0217d2ac48d25
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66240247"
---
1. Navegue para o ficheiro de solução do projeto de cliente (. sln) e abra-o com o Visual Studio.

2. No Visual Studio, escolha a plataforma de solução (Android, iOS ou Windows) a partir da lista pendente junto à seta de início. Selecione um dispositivo de implementação específico ou emulador ao clicar na lista pendente na seta verde. Pode utilizar a plataforma Android e o emulador Ripple predefinidos. Tutoriais mais avançados (por exemplo, notificações push) irão requerer que selecione um dispositivo ou emulador suportados.

3. Abra o ficheiro `ToDoActivity.java` nesta pasta - ZUMOAPPNAME/aplicação/src/main/java/com/exemplo/zumoappname. O nome da aplicação é `ZUMOAPPNAME`.

4. Vá para o [portal do Azure](https://portal.azure.com/) e navegue para a aplicação móvel que criou. Sobre o `Overview` painel, procure o URL que é o ponto final público para a sua aplicação móvel. Exemplo - sitename para meu nome de aplicação "test123" será https://test123.azurewebsites.net.

5. Vá para o `index.js` arquivo no ZUMOAPPNAME/www/js/index.js e, no `onDeviceReady()` método, substitua `ZUMOAPPURL` parâmetro com o ponto final público acima.

    `client = new WindowsAzure.MobileServiceClient('ZUMOAPPURL');`
    
    torna-se
    
    `client = new WindowsAzure.MobileServiceClient('https://test123.azurewebsites.net');`
    
6. Para criar e executar a aplicação Cordova, prima F5 ou clique na seta verde. Se for apresentada uma caixa de diálogo de segurança no emulador a solicitar acesso à rede, aceite.

7. Depois da aplicação é iniciada no dispositivo ou emulador, introduza texto relevante em **introduzir novo texto**, tal como *concluir o tutorial* e, em seguida, clique nas **Add** botão.

Os dados de inserções de back-end do pedido encontram-se na tabela TodoItem na Base de Dados SQL e devolvem informações sobre os itens recentemente armazenados na aplicação móvel. A aplicação móvel apresenta estes dados na lista.

Pode repetir os passos 3 a 5 para outras plataformas.
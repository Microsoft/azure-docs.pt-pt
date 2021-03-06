---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/09/2019
ms.author: crdun
ms.openlocfilehash: 63c54f8af91b6b4a76ba49d5e6fc7b3cda9f5b98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "66240320"
---
1. Abra o projeto com o **Android Studio**, utilizando **Importar projeto (Eclipse ADT, Gradle, etc.)**. Verifique se seleciona esta importação para evitar eventuais erros do JDK.

2. Abra o `ToDoActivity.java` ficheiro nesta pasta - ZUMOAPPNAME/app/src/main/java/com/example/zumoappname. O nome `ZUMOAPPNAME`da aplicação é .

3. Vá ao [portal Azure](https://portal.azure.com/) e navegue para a aplicação móvel que criou. Na `Overview` lâmina, procure o URL que é o ponto final público da sua aplicação móvel. Exemplo - o nome de site para o meu https://test123.azurewebsites.netnome de aplicação "test123" será .

4. No `onCreate()` método, `ZUMOAPPURL` substitua o parâmetro por ponto final público acima.
    
    `new MobileServiceClient("ZUMOAPPURL", this).withFilter(new ProgressFilter());` 
    
    torna-se
    
    `new MobileServiceClient("https://test123.azurewebsites.net", this).withFilter(new ProgressFilter());`
    
5. Prima o botão **Executar “aplicação”** para criar o projeto e iniciar a aplicação no simulador do Android.

4. Na aplicação, digite um texto significativo, como *Concluir o tutorial* e, em seguida, clique no botão “Adicionar”. Esta ação envia um pedido POST ao back-end do Azure que implementou anteriormente. Os dados de inserções de back-end do pedido encontram-se na tabela TodoItem SQL e devolve informações sobre os itens recentemente armazenados na aplicação móvel. A aplicação móvel apresenta estes dados na lista.
    ![Quickstart Android](./media/app-service-mobile-android-quickstart/mobile-quickstart-startup-android.png)
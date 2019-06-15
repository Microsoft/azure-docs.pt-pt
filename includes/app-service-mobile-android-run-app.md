---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/09/2019
ms.author: crdun
ms.openlocfilehash: 63c54f8af91b6b4a76ba49d5e6fc7b3cda9f5b98
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66240320"
---
1. Abra o projeto com o **Android Studio**, utilizando **Importar projeto (Eclipse ADT, Gradle, etc.)** . Verifique se seleciona esta importação para evitar eventuais erros do JDK.

2. Abra o ficheiro `ToDoActivity.java` nesta pasta - ZUMOAPPNAME/aplicação/src/main/java/com/exemplo/zumoappname. O nome da aplicação é `ZUMOAPPNAME`.

3. Vá para o [portal do Azure](https://portal.azure.com/) e navegue para a aplicação móvel que criou. Sobre o `Overview` painel, procure o URL que é o ponto final público para a sua aplicação móvel. Exemplo - sitename para meu nome de aplicação "test123" será https://test123.azurewebsites.net.

4. Na `onCreate()` método, substitua `ZUMOAPPURL` parâmetro com o ponto final público acima.
    
    `new MobileServiceClient("ZUMOAPPURL", this).withFilter(new ProgressFilter());` 
    
    torna-se
    
    `new MobileServiceClient("https://test123.azurewebsites.net", this).withFilter(new ProgressFilter());`
    
5. Prima o botão **Executar “aplicação”** para criar o projeto e iniciar a aplicação no simulador do Android.

4. Na aplicação, digite um texto significativo, como *Concluir o tutorial* e, em seguida, clique no botão “Adicionar”. Esta ação envia um pedido POST ao back-end do Azure que implementou anteriormente. Os dados de inserções de back-end do pedido encontram-se na tabela TodoItem SQL e devolve informações sobre os itens recentemente armazenados na aplicação móvel. A aplicação móvel apresenta estes dados na lista.
    ![Android de início rápido](./media/app-service-mobile-android-quickstart/mobile-quickstart-startup-android.png)
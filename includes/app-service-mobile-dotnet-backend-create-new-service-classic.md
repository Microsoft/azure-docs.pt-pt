---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: e087a1db008422aeec8fd4e073a7476eebe4d54b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "67184941"
---
1. Inicie sessão no [portal do Azure].
2. Selecione **+NEW** > **Web + Mobile** > **Mobile App,** e, em seguida, forneça um nome para as suas Aplicações Móveis na extremidade.
3. Para **o Grupo de Recursos**, selecione um grupo de recursos existente, ou crie um novo (utilizando o mesmo nome que a sua app). 
4. Para o plano de serviço de **aplicações,** o plano predefinido (no [nível Standard)](https://azure.microsoft.com/pricing/details/app-service/)é selecionado. Pode ainda selecionar um plano diferente ou [criar um novo](../articles/app-service/app-service-plan-manage.md#create-an-app-service-plan). 

   As definições do plano de Serviço de Aplicações determinam a [localização, funcionalidades, custos e recursos de computação](https://azure.microsoft.com/pricing/details/app-service/) associados à sua aplicação. Para mais informações sobre os planos do App Service e como criar um novo plano num nível de preços diferente e na sua localização desejada, consulte os [planos do Azure App Service em profundidade.](../articles/app-service/overview-hosting-plans.md)
   
5. Selecione **Criar**. Este passo cria as Aplicações Móveis no final. 
6. No painel **Definições** para as novas Aplicações Móveis, selecione **Quick start** > plataforma de aplicações do cliente > **Connect uma base de dados**. 
   
   ![Seleções para ligar uma base de dados](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-data-connection.png)
7. No painel de **ligação de dados Adicionar,** selecione Base de Dados >  **SQL****Criar uma nova base de dados**. Introduza o nome da base de dados, escolha um nível de preços e, em seguida, selecione **Server**. Pode reutilizar esta nova base de dados. Se já tiver uma base de dados na mesma localização, pode escolher **Utilizar uma base de dados existente**. Não recomendamos o uso de uma base de dados num local diferente, devido aos custos de largura de banda e à latência mais elevada.
   
   ![Selecionar uma base de dados](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png)
8. No painel do **novo servidor,** introduza um nome de servidor único na caixa de **nome** sinuosa, forneça um login e senha, selecione **os serviços De acesso do Iado Azure e**selecione **OK**. Este passo cria a nova base de dados.
9. Volte ao painel de ligação de dados Adicionar, selecione **a cadeia de ligação,** introduza os valores de login e palavra-passe para a sua base de dados e selecione **OK**. **Add data connection** 

   Aguarde alguns minutos para que a base de dados seja implantada com sucesso antes de prosseguir.

<!-- URLs. -->
[Portal Azure]: https://portal.azure.com/

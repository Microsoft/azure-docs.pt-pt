---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 30b5ae499d29b8b78b5852074362841ac1ceb49f
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50134243"
---
1. Inicie sessão no [portal do Azure].
2. Selecione **+ novo** > **Web + móvel** > **aplicação móvel**e, em seguida, forneça um nome para as suas aplicações de Mobile back-end.
3. Para **grupo de recursos**, selecione um grupo de recursos existente ou crie um novo (utilizando o mesmo nome que a sua aplicação). 
4. Para **plano do App Service**, o plano predefinido (no [escalão Standard](https://azure.microsoft.com/pricing/details/app-service/)) está selecionada. Pode ainda selecionar um plano diferente ou [criar um novo](../articles/app-service/app-service-plan-manage.md#create-an-app-service-plan). 

   Definições do plano de serviço de aplicações determinam a [localização, funcionalidades, custo e recursos de computação](https://azure.microsoft.com/pricing/details/app-service/) associados à aplicação. Para obter mais informações sobre o App Service os planos e como criar um novo plano num preço diferente da camada e na localização pretendida, consulte [descrição geral aprofundada dos planos do App Service do Azure](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
   
5. Selecione **Criar**. Este passo cria o back-end de aplicações móveis. 
6. Na **configurações** painel para as novas aplicações móveis de back-end, selecione **início rápido** > sua plataforma de aplicação de cliente > **ligar uma base de dados**. 
   
   ![Seleções para ligar uma base de dados](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-data-connection.png)
7. Na **adicionar ligação de dados** painel, selecione **base de dados SQL** > **criar uma nova base de dados**. Introduza o nome de base de dados, escolha um escalão de preço e, em seguida, selecione **servidor**. Pode reutilizar esta nova base de dados. Se já tiver uma base de dados na mesma localização, pode escolher **Utilizar uma base de dados existente**. Não é recomendada a utilização de uma base de dados numa localização diferente, devido a custos de largura de banda e latência superior.
   
   ![Selecionar uma base de dados](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png)
8. Na **novo servidor** painel, introduza um nome de servidor exclusivo na **nome do servidor de** caixa, forneça um início de sessão e palavra-passe, selecione **dos serviços do Azure permitem ao servidor de acesso**e selecione  **OK**. Este passo cria uma nova base de dados.
9. De volta a **adicionar ligação de dados** painel, selecione **cadeia de ligação**, introduza os valores de início de sessão e palavra-passe para a base de dados e selecione **OK**. 

   Aguarde alguns minutos para a base de dados a serem implantados com êxito antes de continuar.

<!-- URLs. -->
[Portal do Azure]: https://portal.azure.com/

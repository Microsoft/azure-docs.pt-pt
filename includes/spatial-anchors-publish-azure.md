---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 0e6f68daf22659b880827ad027268d9757c60063
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91971430"
---
### <a name="open-the-publish-wizard"></a>Abra o assistente de publicação

No **Solution Explorer,** clique à direita no projeto **SharingService** e, em seguida, selecione **Publicar**.

O Assistente de Publicação começa. 

Selecione **App Service**  >  **Publish** para abrir o painel **de Serviço de Aplicações Create.**

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure.

No painel **'Criar Serviço de Aplicações',** selecione **Adicionar uma conta**e, em seguida, iniciar sessão na sua subscrição Azure. Se já fez a sua assinatura, selecione a conta que pretende da lista de entrega.

   > [!NOTE]
   > Se já tiver sessão iniciada, não selecione ainda **Criar**.
   >

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

[!INCLUDE [resource group intro text](resource-group.md)]

Junto a **Grupo de recursos**, selecione **Novo**.

Nomeie o grupo de recursos **myResourceGroup**e, em seguida, selecione **OK**.

### <a name="create-an-app-service-plan"></a>Crie um plano do Serviço de Aplicações

[!INCLUDE [app-service-plan](app-service-plan.md)]

Junto a **Plano de Alojamento**, selecione **Novo**.

No painel do **Plano de Alojamento configurar,** utilize estas definições:

| Definição | Valor sugerido | Descrição |
|-|-|-|
|Plano do Serviço de Aplicações| MySharingServicePlan | Nome do plano de Serviço de Aplicações |
| Localização | E.U.A. Oeste | O datacenter onde a aplicação web está hospedada |
| Tamanho | Gratuito | O [nível de preços](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) que determina as funcionalidades de hospedagem |

Selecione **OK**.

### <a name="create-and-publish-the-web-app"></a>Criar e publicar a aplicação Web

No **Nome da App,** insira um nome de aplicação único. Os caracteres válidos são a-z, 0-9 e traços (-), ou aceitam o nome único gerado automaticamente. O URL da aplicação Web é `https://<app_name>.azurewebsites.net`, em que `<app_name>` é o nome da aplicação.

Selecione **Criar** para começar a criar os recursos do Azure.

   Depois de o assistente terminar, publica a aplicação web Core ASP.NET para o Azure e, em seguida, abre a aplicação no seu navegador padrão.

  ![Screenshot de uma aplicação web ASP.NET publicada em Azure.](./media/spatial-anchors-azure/web-app-running-live.png)

O nome da aplicação que usou nesta secção é usado como prefixo URL no formato `https://<app_name>.azurewebsites.net` . Copie este URL para um editor de texto para posterior utilização.

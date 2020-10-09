---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 0dab71b6d169e26a3d7dc208dd09efe1143fbe13
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "67183948"
---
### <a name="open-the-publish-wizard"></a>Abra o assistente de publicação

No **Solution Explorer,** clique com o botão direito no projeto **SharingService** e selecione **Publicar**.

O Assistente de Publicação começa. Selecione **App Service**  >  **Publish** para abrir a caixa de diálogo Do Serviço de **Aplicações Create.**

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Na caixa de diálogo **'Serviço de Aplicação Create',** selecione **Adicionar uma conta** e iniciar sessão na sua subscrição Azure. Se já fez a sua assinatura, selecione a conta que pretende da lista de entrega.

> [!NOTE]
> Se já tiver sessão iniciada, não selecione ainda **Criar**.
>

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

[!INCLUDE [resource group intro text](resource-group.md)]

Junto a **Grupo de recursos**, selecione **Novo**.

Atribua ao grupo de recursos o nome **myResourceGroup** e selecione **OK**.

### <a name="create-an-app-service-plan"></a>Crie um plano do Serviço de Aplicações

[!INCLUDE [app-service-plan](app-service-plan.md)]

Junto a **Plano de Alojamento**, selecione **Novo**.

Na caixa de diálogo **do Plano de Alojamento Configurado,** utilize estas definições:

| Definição | Valor sugerido | Descrição |
|-|-|-|
|Plano do Serviço de Aplicações| MySharingServicePlan | Nome do plano de serviço de aplicações. |
| Localização | E.U.A. Oeste | O centro de dados onde o a aplicação Web está alojada. |
| Tamanho | Gratuito | O [nível de preços](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) que determina as funcionalidades de hospedagem. |

Selecione **OK**.

### <a name="create-and-publish-the-web-app"></a>Criar e publicar a aplicação Web

No **Nome da App,** insira um nome de aplicação único (caracteres válidos são `a-z` , e ) ou aceite o nome único gerado `0-9` `-` automaticamente. O URL da aplicação Web é `https://<app_name>.azurewebsites.net`, em que `<app_name>` é o nome da aplicação.

Selecione **Criar** para começar a criar os recursos do Azure.

Depois de o assistente terminar, publica a aplicação web Core ASP.NET para o Azure e, em seguida, abre a aplicação no seu navegador padrão.

![Aplicação Web ASP.NET publicada no Azure](./media/spatial-anchors-azure/web-app-running-live.png)

O nome da aplicação que usou nesta secção é usado como prefixo URL no formato `https://<app_name>.azurewebsites.net` . Tome nota desta URL porque vai precisar.

---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 0dab71b6d169e26a3d7dc208dd09efe1143fbe13
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "67183948"
---
### <a name="open-the-publish-wizard"></a>Abra o assistente de publicação

No **Solution Explorer,** clique no projeto **SharingService** e selecione **Publicar**.

Começa o Assistente editorial. Selecione **App Service** > **Publish** para abrir a caixa de diálogo Create **App Service.**

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Na caixa de diálogo **Create App Service,** selecione **Adicionar uma conta** e iniciar sessão na subscrição do Azure. Se já se inscreveu, selecione a conta que deseja da lista de entregas.

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

Na caixa de diálogo do Plano de **Hospedagem Configurar,** utilize estas definições:

| Definição | Valor sugerido | Descrição |
|-|-|-|
|Plano do Serviço de Aplicações| MySharingServicePlan | Nome do plano de serviço de aplicações. |
| Localização | E.U.A. Oeste | O centro de dados onde o a aplicação Web está alojada. |
| Tamanho | Gratuito | O [nível de preços](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) que determina as características de hospedagem. |

Selecione **OK**.

### <a name="create-and-publish-the-web-app"></a>Criar e publicar a aplicação Web

No Nome da **Aplicação,** introduza `a-z`um `0-9`nome `-`de aplicação único (caracteres válidos são, e , ou aceite o nome único gerado automaticamente. O URL da aplicação Web é `https://<app_name>.azurewebsites.net`, em que `<app_name>` é o nome da aplicação.

Selecione **Criar** para começar a criar os recursos do Azure.

Depois de o assistente terminar, publica a ASP.NET aplicação web Core para o Azure e, em seguida, abre a aplicação no seu navegador predefinido.

![Aplicação Web ASP.NET publicada no Azure](./media/spatial-anchors-azure/web-app-running-live.png)

O nome da aplicação que usou nesta secção é `https://<app_name>.azurewebsites.net`usado como prefixo URL no formato . Tome nota desta URL porque vai precisar.

---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 0dab71b6d169e26a3d7dc208dd09efe1143fbe13
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58305290"
---
### <a name="open-the-publish-wizard"></a>Abrir o Assistente de publicação

Na **Explorador de soluções**, clique com botão direito a **SharingService** do projeto e selecione **publicar**.

Inicia o Assistente de publicação. Selecione **serviço de aplicações** > **publicar** para abrir o **criar serviço de aplicações** caixa de diálogo.

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Na **criar serviço de aplicações** caixa de diálogo, selecione **adicionar uma conta** e inicie sessão na sua subscrição do Azure. Se ainda tiver iniciado sessão, selecione a conta que pretende que o da lista pendente.

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

Na **configurar o plano de alojamento** diálogo caixa, utilize estas definições:

| Definição | Valor sugerido | Descrição |
|-|-|-|
|Plano do Serviço de Aplicações| MySharingServicePlan | Nome do plano de serviço de aplicações. |
| Localização | EUA Oeste | O centro de dados onde o a aplicação Web está alojada. |
| Tamanho | Gratuito | O [escalão de preço](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) determina que recursos de alojamento. |

Selecione **OK**.

### <a name="create-and-publish-the-web-app"></a>Criar e publicar a aplicação Web

Na **nome da aplicação**, introduza um nome de aplicação exclusivo (carateres válidos são `a-z`, `0-9`, e `-`), ou aceite o nome exclusivo gerado automaticamente. O URL da aplicação Web é `https://<app_name>.azurewebsites.net`, em que `<app_name>` é o nome da aplicação.

Selecione **Criar** para começar a criar os recursos do Azure.

Depois de concluído o assistente, publica a aplicação web do ASP.NET Core no Azure e, em seguida, abre-se a aplicação no seu browser predefinido.

![Aplicação Web ASP.NET publicada no Azure](./media/spatial-anchors-azure/web-app-running-live.png)

O nome da aplicação utilizados nesta secção é utilizado como o prefixo de URL no formato `https://<app_name>.azurewebsites.net`. Tome nota deste URL, porque irá precisar das mesmas.

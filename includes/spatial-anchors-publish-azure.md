---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 3ee9aca1ee86b04998e357875b98b14782b3e56d
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56963318"
---
### <a name="launch-the-publish-wizard"></a>Iniciar o assistente de publicação

Na **Explorador de soluções**, clique com botão direito a **SharingService** do projeto e selecione **publicar**.

O assistente de publicação é iniciado automaticamente. Selecione **Serviço de Aplicações** > **Publicar** para abrir a caixa de diálogo **Criar Serviço de Aplicações**.

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Na caixa de diálogo **Criar Serviço de Aplicações**, clique em **Adicionar uma conta** e inicie sessão na sua subscrição do Azure. Se ainda tiver a sessão iniciada, seleciona a conta que quer a partir da lista pendente.

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

Na **configurar o plano de alojamento** caixa de diálogo, utilize as definições da tabela.

| Definição | Valor sugerido | Descrição |
|-|-|-|
|Plano do Serviço de Aplicações| MySharingServicePlan | Nome do plano de serviço de aplicações. |
| Localização | EUA Oeste | O centro de dados onde o a aplicação Web está alojada. |
| Tamanho | Gratuito | O [escalão de preço](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) determina as funcionalidades do alojamento. |

Selecione **OK**.

### <a name="create-and-publish-the-web-app"></a>Criar e publicar a aplicação Web

No **Nome da Aplicação**, escreva um nome único para a aplicação (os carateres válidos são `a-z`, `0-9` e `-`) ou aceite o nome exclusivo gerado automaticamente. O URL da aplicação Web é `https://<app_name>.azurewebsites.net`, em que `<app_name>` é o nome da aplicação.

Selecione **Criar** para começar a criar os recursos do Azure.

Depois de concluir o assistente, este publica a aplicação Web ASP.NET Core no Azure e, em seguida, inicia a aplicação no browser predefinido.

![Aplicação Web ASP.NET publicada no Azure](./media/spatial-anchors-azure/web-app-running-live.png)

O nome da aplicação especificado no [passo para criar e publicar](#create-and-publish-the-web-app) é utilizado como o prefixo de URL no formato `https://<app_name>.azurewebsites.net`. Tome nota deste URL que será utilizado mais tarde.

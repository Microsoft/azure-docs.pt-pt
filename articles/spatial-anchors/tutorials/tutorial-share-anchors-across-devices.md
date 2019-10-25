---
title: Tutorial – compartilhar âncoras espaciais do Azure entre sessões e dispositivos | Microsoft Docs
description: Neste tutorial, você aprenderá a compartilhar identificadores de âncora espacial do Azure entre dispositivos Android/iOS no Unity com um serviço de back-end.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: e8a60d5d90b684698d6fcb612278bcae6d4ed08e
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72882308"
---
# <a name="tutorial-share-azure-spatial-anchors-across-sessions-and-devices"></a>Tutorial: compartilhar âncoras espaciais do Azure entre sessões e dispositivos

Neste tutorial, você aprenderá a usar as [âncoras espaciais do Azure](../overview.md) para criar âncoras durante uma sessão e, em seguida, localizá-las, no mesmo dispositivo ou em uma diferente. Essas mesmas âncoras também podem ser localizadas por vários dispositivos no mesmo local e ao mesmo tempo.

![Persistência](./media/persistence.gif)

As âncoras espaciais do Azure são um serviço de desenvolvedor de plataforma cruzada que permite que você crie experiências de realidade mista usando objetos que persistem seu local entre dispositivos ao longo do tempo. Quando tiver terminado, você terá um aplicativo que pode ser implantado em dois ou mais dispositivos. As âncoras espaciais do Azure criadas por uma instância podem ser compartilhadas com as outras.

Vai aprender a:

> [!div class="checklist"]
> * Implante um aplicativo Web ASP.NET Core no Azure que possa ser usado para compartilhar âncoras, armazenando-os na memória por um período de tempo.
> * Configure a cena AzureSpatialAnchorsLocalSharedDemo dentro do exemplo de Unity de nossos guias de início rápido para aproveitar o aplicativo Web de ancoragem de compartilhamento.
> * Implante e execute em um ou mais dispositivos.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

Vale a pena notar que, embora você esteja usando o Unity e um ASP.NET Core aplicativo Web neste tutorial, é apenas mostrar um exemplo de como compartilhar identificadores de âncora espacial do Azure em outros dispositivos. Você pode usar outras linguagens e tecnologias de back-end para atingir o mesmo objetivo. Além disso, o aplicativo Web ASP.NET Core usado neste tutorial tem uma dependência do SDK do .NET Core 2,2. Ele funciona bem em aplicativos Web regulares do Azure (para Windows), mas não funcionará atualmente nos aplicativos Web do Azure para Linux.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-the-sample-project"></a>Baixar o projeto de exemplo

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

## <a name="deploy-your-sharing-anchors-service"></a>Implantar o serviço de âncoras de compartilhamento

## <a name="visual-studiotabvs"></a>[Visual Studio](#tab/VS)

Abra o Visual Studio e abra o projeto na pasta `Sharing\SharingServiceSample`.

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

## <a name="visual-studio-codetabvsc"></a>[Visual Studio Code](#tab/VSC)

Você precisará criar um grupo de recursos e um plano do serviço de aplicativo antes de implantar o serviço no VS Code.

### <a name="sign-in-to-azure"></a>Entrar no Azure

Navegue até o <a href="https://portal.azure.com/" target="_blank">portal do Azure</a> e entre em sua assinatura do Azure.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

[!INCLUDE [resource group intro text](../../../includes/resource-group.md)]

Junto a **Grupo de recursos**, selecione **Novo**.

Atribua ao grupo de recursos o nome **myResourceGroup** e selecione **OK**.

### <a name="create-an-app-service-plan"></a>Crie um plano do Serviço de Aplicações

[!INCLUDE [app-service-plan](../../../includes/app-service-plan.md)]

Junto a **Plano de Alojamento**, selecione **Novo**.

Na caixa de diálogo **Configurar plano de hospedagem** , use estas configurações:

| Definição | Valor sugerido | Descrição |
|-|-|-|
|Plano do Serviço de Aplicações| MySharingServicePlan | Nome do plano de serviço de aplicações. |
| Localização | Oeste dos E.U.A. | O centro de dados onde o a aplicação Web está alojada. |
| Tamanho | Gratuito | O [tipo de preço](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) que determina os recursos de hospedagem. |

Selecione **OK**.

Abra Visual Studio Code e abra o projeto na pasta `Sharing\SharingServiceSample`. Siga <a href="https://docs.microsoft.com/aspnet/core/tutorials/publish-to-azure-webapp-using-vscode?view=aspnetcore-2.2#open-it-with-visual-studio-code" target="_blank">este tutorial</a> para implantar o serviço de compartilhamento por meio do Visual Studio Code. Você pode seguir as etapas a partir da seção "abrir com Visual Studio Code". Não crie outro projeto do MVC, conforme explicado na etapa acima, pois você já tem o projeto que precisa ser implantado e publicado-o SharingServiceSample.

---

## <a name="deploy-the-sample-app"></a>Implantar o aplicativo de exemplo

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você implantou um aplicativo Web ASP.NET Core no Azure e, em seguida, configurou e implantou um aplicativo do Unity. Você criou âncoras espaciais com o aplicativo e os compartilhou com outros dispositivos usando seu ASP.NET Core aplicativo Web.

Você pode melhorar seu aplicativo Web ASP.NET Core para que ele use Azure Cosmos DB para persistir o armazenamento de seus identificadores de âncora espacial compartilhados. Adicionar Azure Cosmos DB suporte permitirá que seu aplicativo Web do ASP.NET Core crie uma âncora hoje e volte a dias depois para conseguir localizá-la novamente, usando o identificador de âncora armazenado em seu aplicativo Web.

> [!div class="nextstepaction"]
> [Usar o Azure Cosmo DB para armazenar âncoras](./tutorial-use-cosmos-db-to-store-anchors.md)


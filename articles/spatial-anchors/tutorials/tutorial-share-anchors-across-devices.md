---
title: 'Tutorial: Partilhar âncoras em sessões e dispositivos'
description: Neste tutorial, aprende-se a partilhar identificadores de Âncora Espacial Azure entre dispositivos Android/iOS em Unidade com um serviço back-end.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 3b377f87bdba40c90cb3af6caef2c089d7b7de49
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77615475"
---
# <a name="tutorial-share-azure-spatial-anchors-across-sessions-and-devices"></a>Tutorial: Partilhe âncoras espaciais Azure em sessões e dispositivos

Neste tutorial, você aprenderá a usar [âncoras espaciais Azure](../overview.md) para criar âncoras durante uma sessão e, em seguida, localizá-las, no mesmo dispositivo ou em outra. Estas mesmas âncoras também podem ser localizadas por vários dispositivos no mesmo local e ao mesmo tempo.

![Persistência](./media/persistence.gif)

O Azure Spatial Anchors é um serviço de desenvolvimento de plataformas cruzadas que permite criar experiências de realidade mista utilizando objetos que persistem a sua localização através de dispositivos ao longo do tempo. Quando terminar, terá uma aplicação que pode ser implementada em dois ou mais dispositivos. As Âncoras Espaciais Azure criadas por um exemplo podem ser partilhadas com as outras.

Vai aprender a:

> [!div class="checklist"]
> * Implemente uma ASP.NET Core Web App em Azure que possa ser usada para partilhar âncoras, armazenando-as na memória por um período de tempo.
> * Configure a cena AzureSpatialAnchorsLocalSharedDemo dentro da Amostra de Unidade dos nossos Quickstarts para tirar partido da Aplicação Web De Partilha de Âncoras.
> * Desloque e corra para um ou mais dispositivos.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

Vale a pena notar que, embora esteja a usar a Unidade e uma ASP.NET Core Web App neste Tutorial, é apenas para mostrar um exemplo de como partilhar identificadores de Âncora Espacial Azure em outros dispositivos. Você pode usar outras línguas e tecnologias de back-end para alcançar o mesmo objetivo. Além disso, o ASP.NET Core Web App utilizado neste Tutorial tem uma dependência de .NET Core 2.2 SDK. Funciona bem em aplicações web do Azure regulares (para Windows), mas atualmente não funcionará em Aplicações Web Azure para Linux.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-the-sample-project"></a>Descarregue o projeto da amostra

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

## <a name="deploy-your-sharing-anchors-service"></a>Implante o seu serviço de âncoras de partilha

## <a name="visual-studio"></a>[Visual Studio](#tab/VS)

Open Visual Studio, e abra o projeto na pasta `Sharing\SharingServiceSample`.

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

## <a name="visual-studio-code"></a>[Visual Studio Code](#tab/VSC)

Terá de criar um grupo de recursos e um Plano de Serviço de Aplicações antes de implementar o serviço em Código VS.

### <a name="sign-in-to-azure"></a>Inscreva-se no Azure

Navegue para o <a href="https://portal.azure.com/" target="_blank">portal Azure</a> e inscreva-se na subscrição do Azure.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos:

[!INCLUDE [resource group intro text](../../../includes/resource-group.md)]

Junto a **Grupo de recursos**, selecione **Novo**.

Atribua ao grupo de recursos o nome **myResourceGroup** e selecione **OK**.

### <a name="create-an-app-service-plan"></a>Crie um plano do Serviço de Aplicações

[!INCLUDE [app-service-plan](../../../includes/app-service-plan.md)]

Junto a **Plano de Alojamento**, selecione **Novo**.

Na caixa de diálogo do Plano de **Hospedagem Configurar,** utilize estas definições:

| Definição | Valor sugerido | Descrição |
|-|-|-|
|Plano do Serviço de Aplicações| MySharingServicePlan | Nome do plano de serviço de aplicações. |
| Localização | E.U.A. Oeste | O centro de dados onde o a aplicação Web está alojada. |
| Tamanho | Gratuito | O [nível de preços](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) que determina as características de hospedagem. |

Selecione **OK**.

Abra o Código do Estúdio Visual e abra o projeto na pasta `Sharing\SharingServiceSample`. Siga <a href="https://docs.microsoft.com/aspnet/core/tutorials/publish-to-azure-webapp-using-vscode?view=aspnetcore-2.2#open-it-with-visual-studio-code" target="_blank">este tutorial</a> para implementar o serviço de partilha através do Visual Studio Code. Pode seguir os passos a partir da secção 'Abra-o com Código de Estúdio Visual'. Não crie outro projeto de mvc, como explicado na etapa acima, já que já tem o projeto que precisa de ser implementado e publicado, o SharingServiceSample.

---

## <a name="deploy-the-sample-app"></a>Implementar a aplicação de amostra

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="unity-20193"></a>Unidade 2019.3

Devido a alterações de rutura, a Unidade 2019.3 não é atualmente apoiada. Utilize a Unidade 2019.1 ou 2019.2.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, implementou uma ASP.NET Core Web App em Azure, e depois configurado e implementado uma App de Unidade. Criou as Âncoras Espaciais com a aplicação e partilhou-as com outros dispositivos utilizando a sua ASP.NET Core Web App.

Pode melhorar o seu ASP.NET Core Web App para que utilize o Azure Cosmos DB para persistir no armazenamento dos seus identificadores de Âncora Espacial partilhadas. A adição de suporte Azure Cosmos DB permitirá ao seu ASP.NET Core Web App criar uma âncora hoje, e voltar dias depois para poder localizá-la novamente, utilizando o identificador de âncora armazenado na sua aplicação web.

> [!div class="nextstepaction"]
> [Use azure Cosmo DB para armazenar âncoras](./tutorial-use-cosmos-db-to-store-anchors.md)


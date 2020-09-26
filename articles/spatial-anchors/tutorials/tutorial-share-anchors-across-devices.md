---
title: 'Tutorial: Partilhar âncoras em sessões e dispositivos'
description: Neste tutorial, aprende-se a partilhar identificadores Azure Spatial Anchor entre dispositivos Android/iOS em Unidade com um serviço back-end.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 07/31/2020
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 8b6c3608165ed592cc2f0daf475226c9d35de012
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91358824"
---
# <a name="tutorial-share-azure-spatial-anchors-across-sessions-and-devices"></a>Tutorial: Partilhar Âncoras Espaciais Azure em sessões e dispositivos

Neste tutorial, você aprenderá a usar [âncoras espaciais Azure](../overview.md) para criar âncoras durante uma sessão e depois localizá-las, no mesmo dispositivo ou em outra. Estas mesmas âncoras também podem ser localizadas por vários dispositivos no mesmo local e ao mesmo tempo.

![A animação mostra âncoras espaciais Azure criadas com um dispositivo móvel e usadas com um dispositivo diferente ao longo dos dias.](./media/persistence.gif)

Azure Spatial Anchors é um serviço de desenvolvedor de plataformas cruzadas que permite criar experiências de realidade mista usando objetos que persistem a sua localização através dos dispositivos ao longo do tempo. Quando terminar, terá uma aplicação que pode ser implantada em dois ou mais dispositivos. Âncoras Espaciais Azure criadas por um exemplo podem ser partilhadas com as outras.

Vai aprender a:

> [!div class="checklist"]
> * Implemente uma ASP.NET Core Web App em Azure que pode ser usada para partilhar âncoras, armazenando-as na memória durante um período de tempo.
> * Configure a cena AzureSpatialAnchorsLocalSharedDemo dentro da Amostra de Unidade dos nossos Quickstarts para tirar partido da App Web de Âncoras partilhadas.
> * Desdobre e corra para um ou mais dispositivos.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

Vale a pena notar que, embora esteja a usar a Unidade e uma ASP.NET Core Web App neste Tutorial, é apenas para mostrar um exemplo sobre como partilhar identificadores de Âncora Espacial Azure em outros dispositivos. Você pode usar outras línguas e tecnologias de back-end para alcançar o mesmo objetivo.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-the-sample-project"></a>Descarregue o projeto de amostra

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

## <a name="deploy-your-sharing-anchors-service"></a>Implemente o seu serviço de âncoras de partilha

## <a name="visual-studio"></a>[Visual Studio](#tab/VS)

Abra o Estúdio Visual e abra o projeto na `Sharing\SharingServiceSample` pasta.

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

## <a name="visual-studio-code"></a>[Visual Studio Code](#tab/VSC)

Terá de criar um grupo de recursos e um Plano de Serviço de Aplicações antes de implementar o serviço no Código VS.

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Navegue até ao <a href="https://portal.azure.com/" target="_blank">portal Azure</a> e inscreva-se na sua subscrição do Azure.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

[!INCLUDE [resource group intro text](../../../includes/resource-group.md)]

Junto a **Grupo de recursos**, selecione **Novo**.

Atribua ao grupo de recursos o nome **myResourceGroup** e selecione **OK**.

### <a name="create-an-app-service-plan"></a>Crie um plano do Serviço de Aplicações

[!INCLUDE [app-service-plan](../../../includes/app-service-plan.md)]

Junto a **Plano de Alojamento**, selecione **Novo**.

Na caixa de diálogo **do Plano de Alojamento Configurado,** utilize estas definições:

| Definição | Valor sugerido | Descrição |
|-|-|-|
|Plano do Serviço de Aplicações| MySharingServicePlan | Nome do plano de serviço de aplicações. |
| Localização | E.U.A. Oeste | O centro de dados onde o a aplicação Web está alojada. |
| Tamanho | Gratuito | O [nível de preços](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) que determina as funcionalidades de hospedagem. |

Selecione **OK**.

Abra o Código do Estúdio Visual e abra o projeto na `Sharing\SharingServiceSample` pasta. Siga <a href="https://docs.microsoft.com/aspnet/core/tutorials/publish-to-azure-webapp-using-vscode?view=aspnetcore-2.2#open-it-with-visual-studio-code" target="_blank">este tutorial</a> para implementar o serviço de partilha através do Código do Estúdio Visual. Pode seguir os passos a partir da secção 'Abra-o com Código do Estúdio Visual'. Não crie outro projeto ASP.NET como explicado no degrau acima, já que já tem o projeto que precisa de ser implementado e publicado - o SharingServiceSample.

---

## <a name="deploy-the-sample-app"></a>Implementar a aplicação da amostra

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, implementou uma ASP.NET Core Web App em Azure, e depois configurado e implementado uma App de Unidade. Criou âncoras espaciais com a aplicação e partilhou-as com outros dispositivos usando o seu ASP.NET Core Web App.

Pode melhorar a sua ASP.NET Core Web App para que utilize o Azure Cosmos DB para persistir no armazenamento dos seus identificadores de Âncora Espacial partilhados. Adicionar suporte DB Azure Cosmos permitirá que o seu ASP.NET Core Web App crie uma âncora hoje, e volte dias depois para ser capaz de localizá-lo novamente, usando o identificador de âncora armazenado na sua aplicação web.

> [!div class="nextstepaction"]
> [Use Azure Cosmo DB para armazenar âncoras](./tutorial-use-cosmos-db-to-store-anchors.md)


---
title: 'Tutorial: Partilhar âncoras em sessões e dispositivos'
description: Neste tutorial, aprende-se a partilhar identificadores Azure Spatial Anchor entre dispositivos Android/iOS em Unidade com um serviço back-end.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 3048eb1ca7f9312a43bc1ab5885bf19d4c2e7931
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185392"
---
# <a name="tutorial-share-spatial-anchors-across-sessions-and-devices"></a>Tutorial: Partilhar âncoras espaciais em sessões e dispositivos

Azure Spatial Anchors é um serviço de desenvolvedores de plataformas cruzadas com o qual pode criar experiências de realidade mista utilizando objetos que persistem a sua localização através dos dispositivos ao longo do tempo. 

Neste tutorial, você usa [Âncoras Espaciais Azure](../overview.md) para criar âncoras durante uma sessão e, em seguida, localizá-las no mesmo dispositivo ou em outra. As mesmas âncoras também podem ser localizadas por vários dispositivos no mesmo local e ao mesmo tempo.

![Animação mostrando âncoras espaciais que são criadas com um dispositivo móvel e usadas com um dispositivo diferente ao longo dos dias.](./media/persistence.gif)


Neste tutorial, irá aprender a:

> [!div class="checklist"]
> * Implemente uma aplicação web Core ASP.NET em Azure que pode usar para partilhar âncoras e armazenar as âncoras na memória por um período de tempo especificado.
> * Configure a cena AzureSpatialAnchorsLocalSharedDemo dentro da amostra De Unidade a partir dos nossos quickstarts para tirar partido da aplicação web Sharing Anchors.
> * Desdobre e corde as âncoras num ou mais dispositivos.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

> [!NOTE]
> Você vai usar a Unidade e uma aplicação web core ASP.NET neste tutorial, mas a abordagem aqui é apenas para fornecer um exemplo de como compartilhar identificadores Azure Spatial Anchors em outros dispositivos. Você pode usar outras línguas e tecnologias de back-end para alcançar o mesmo objetivo.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-the-sample-project"></a>Descarregue o projeto de amostra

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

## <a name="deploy-the-sharing-anchors-service"></a>Implementar o serviço De Âncoras partilhadas

## <a name="visual-studio"></a>[Visual Studio](#tab/VS)

Abra o Estúdio Visual e, em seguida, abra o projeto na pasta *Sharing\SharingServiceSample.*

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

## <a name="visual-studio-code"></a>[Visual Studio Code](#tab/VSC)

Precisa de criar um grupo de recursos e um plano de Serviço de Aplicações antes de implementar o serviço no Código do Estúdio Visual.

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Vá ao <a href="https://portal.azure.com/" target="_blank">portal Azure</a>e, em seguida, inscreva-se na sua subscrição Azure.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

[!INCLUDE [resource group intro text](../../../includes/resource-group.md)]

Junto a **Grupo de recursos**, selecione **Novo**.

Nomeie o grupo de recursos **myResourceGroup** e, em seguida, selecione **OK**.

### <a name="create-an-app-service-plan"></a>Crie um plano do Serviço de Aplicações

[!INCLUDE [app-service-plan](../../../includes/app-service-plan.md)]

Junto a **Plano de Alojamento**, selecione **Novo**.

No painel do **Plano de Alojamento configurar,** utilize estas definições:

| Definição | Valor sugerido | Descrição |
|-|-|-|
|Plano do Serviço de Aplicações| MySharingServicePlan | Nome do plano de Serviço de Aplicações |
| Localização | E.U.A. Oeste | O datacenter onde a aplicação web está hospedada |
| Tamanho | Gratuito | O [nível de preços](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) que determina as funcionalidades de hospedagem |

Selecione **OK**.

Abra o Código do Estúdio Visual e, em seguida, abra o projeto na pasta *Sharing\SharingServiceSample.* 

Para implementar o serviço de partilha através do Código do Estúdio Visual, siga as instruções em <a href="/aspnet/core/tutorials/publish-to-azure-webapp-using-vscode?view=aspnetcore-2.2#open-it-with-visual-studio-code" target="_blank">Publicar uma aplicação Core ASP.NET para Azure com Visual Studio Code</a>. Comece na secção "Abra-o com Código de Estúdio Visual". Não crie outro projeto ASP.NET como explicado no passo anterior, porque já tem um projeto a ser implementado e publicado: SharingServiceSample.

---

## <a name="deploy-the-sample-app"></a>Implementar a aplicação da amostra

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, implementou uma aplicação web core ASP.NET em Azure, e configuraram e implementaram uma aplicação Unity. Criou âncoras espaciais com a aplicação e partilhou-as com outros dispositivos usando a sua aplicação web Core ASP.NET.

Pode melhorar a sua aplicação web Core ASP.NET para que utilize o Azure Cosmos DB para persistir no armazenamento dos seus identificadores de âncoras espaciais partilhadas. Ao adicionar suporte DB Azure Cosmos, você pode ter o seu ASP.NET web app Core criar uma âncora hoje. Em seguida, usando o identificador de âncora que está armazenado na sua aplicação web, pode ter a aplicação de volta dias depois para localizar a âncora novamente.

> [!div class="nextstepaction"]
> [Use a Azure Cosmos DB para armazenar âncoras](./tutorial-use-cosmos-db-to-store-anchors.md)
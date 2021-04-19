---
title: Gerir a IoT Central a partir do portal Azure | Microsoft Docs
description: Este artigo descreve como criar e gerir as suas aplicações IoT Central a partir do portal Azure.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 04/17/2021
ms.topic: how-to
ms.openlocfilehash: 3e5e126815d0171a6c1627a08419b05b9a3c0c23
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107719210"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>Gerir a IoT Central a partir do portal Azure

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Pode utilizar o [portal Azure](https://portal.azure.com) para criar e gerir aplicações IoT Central.

## <a name="create-iot-central-applications"></a>Criar aplicações IoT Central

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]

Para criar uma aplicação, navegue para a página [de Aplicação Central IoT](https://ms.portal.azure.com/#create/Microsoft.IoTCentral) no portal Azure:

![Criar formulário central IoT](media/howto-manage-iot-central-from-portal/image6a.png)

* **O nome de recurso** é um nome único que pode escolher para a sua aplicação IoT Central no seu grupo de recursos Azure.

* **O URL de aplicação** é o URL que pode usar para aceder à sua aplicação.

* **A localização** é a [geografia](https://azure.microsoft.com/global-infrastructure/geographies/) onde gostaria de criar a sua aplicação. Normalmente, deve escolher o local mais próximo fisicamente dos seus dispositivos para obter um desempenho ideal. A Azure IoT Central está atualmente disponível nos seguintes locais:

  * Ásia-Pacífico
  * Austrália
  * Europa
  * Japão
  * Reino Unido
  * Estados Unidos da América

  Uma vez que você escolhe um local, você não pode mais tarde mover a sua aplicação para um local diferente.

Depois de preencher todos os campos, selecione **Criar**. Para saber mais, consulte o Arranque rápido da [aplicação Create a IoT Central.](quick-deploy-iot-central.md)

## <a name="manage-existing-iot-central-applications"></a>Gerir as aplicações existentes da IoT Central

Se já tiver uma aplicação Azure IoT Central, pode eliminá-la ou movê-la para um grupo de subscrição ou recursos diferente no portal Azure.

> [!NOTE]
> As aplicações criadas através do plano *gratuito* não requerem uma subscrição do Azure, pelo que não as encontrará listadas na subscrição do Azure no portal Azure. Só é possível ver e gerir aplicações gratuitas a partir do portal IoT Central.

Para começar, procure a sua aplicação na barra de pesquisa no topo do portal Azure. Também pode ver todas as suas aplicações procurando _aplicações centrais IoT_ e selecionando o serviço:

![Screenshot que mostra os resultados da pesquisa de "IoT Central Applications" com o primeiro serviço selecionado.](media/howto-manage-iot-central-from-portal/search-iot-central.png)

Quando seleciona uma aplicação nos resultados da pesquisa, o portal Azure mostra-lhe a sua visão geral. Pode navegar para a aplicação selecionando o **URL de aplicação central IoT:**

![Screenshot que mostra a página "Overview" com o "IoT Central Application URL" realçado.](media/howto-manage-iot-central-from-portal/image3.png)

Para mover a aplicação para um grupo de recursos diferente, selecione **a alteração** ao lado do grupo de recursos. Na página **de recursos Move,** escolha o grupo de recursos que pretende mover esta aplicação para:

![Screenshot que mostra a página "Visão Geral" com o "Grupo de Recursos (alteração)" realçado.](media/howto-manage-iot-central-from-portal/image4a.png)

Para mover a aplicação para uma subscrição diferente, selecione  **alterar** ao lado da subscrição. Na página **de recursos Move,** escolha a subscrição que pretende mover esta aplicação para:

![Portal de gestão: gestão de recursos](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a gerir as aplicações da Azure IoT Central a partir do portal Azure, eis o próximo passo sugerido:

> [!div class="nextstepaction"]
> [Administrar a sua aplicação](howto-administer.md)
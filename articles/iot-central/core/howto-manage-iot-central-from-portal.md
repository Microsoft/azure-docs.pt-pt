---
title: Gerir a IoT Central a partir do portal Azure  Microsoft Docs
description: Este artigo descreve como criar e gerir as suas aplicações IoT Central a partir do portal Azure.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 02/11/2020
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 27517c375265b552d2e1dec4d8c167d1bc86549d
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137665"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>Gerir a IoT Central a partir do portal Azure

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Em vez de criar e gerir aplicações IoT Central no site do gestor de [aplicações Azure IoT Central,](https://aka.ms/iotcentral) pode utilizar o [portal Azure](https://portal.azure.com) para gerir as suas aplicações.

## <a name="create-iot-central-applications"></a>Criar aplicações IoT Central

Para criar uma aplicação, navegue para o [portal Azure](https://ms.portal.azure.com) e selecione **Criar um recurso.**

Em **Search the Marketplace** bar, type *IoT Central:*

![Portal de gestão: pesquisa](media/howto-manage-iot-central-from-portal/image0a1.png)

Selecione o azulejo **ioT Central Application** nos resultados da pesquisa:

![Portal de Gestão: resultados da pesquisa](media/howto-manage-iot-central-from-portal/image0b1.png)

Agora, selecione **Criar:**

![Portal de gestão: Recurso Central IoT](media/howto-manage-iot-central-from-portal/image0c1.png)

Preencha todos os campos no formulário. Este formulário é semelhante ao formulário que preenche para criar aplicações no site do gestor de [aplicações Azure IoT Central.](https://aka.ms/iotcentral) Para mais informações, consulte o início rápido da [aplicação Create a IoT Central.](quick-deploy-iot-central.md)

![Criar forma central iot](media/howto-manage-iot-central-from-portal/image6a.png)

**Localização** é a [geografia](https://azure.microsoft.com/global-infrastructure/geographies/) onde você gostaria de criar a sua aplicação. Normalmente, deve escolher o local fisicamente mais próximo dos seus dispositivos para obter um desempenho ideal. A Azure IoT Central está atualmente disponível nos **Estados Unidos**, **Austrália,** **Ásia-Pacífico,** ou na **Europa.**  Uma vez que você escolhe um local, você não pode mover a sua aplicação para um local diferente mais tarde.


Depois de preencher todos os campos, selecione **Criar**.

## <a name="manage-existing-iot-central-applications"></a>Gerir as aplicações existentes da IoT Central

Se já tem uma aplicação Azure IoT Central pode apagá-la ou movê-la para um grupo de subscrição ou recursos diferente no portal Azure.

> [!NOTE]
> Não é possível ver aplicações criadas no plano de preços gratuitos no portal Azure porque não estão associadas à sua subscrição.

Para começar, selecione **Todos os recursos** no portal. Selecione **Mostrar tipos ocultos** e comece a digitar o nome da sua aplicação no **Filtro pelo nome** para encontrá-lo. Em seguida, selecione a aplicação IoT Central que gostaria de gerir.

Para navegar na aplicação, selecione o URL de **Aplicação Central IoT:**

![Portal de gestão: gestão de recursos](media/howto-manage-iot-central-from-portal/image3.png)

Para mover a aplicação para um grupo de recursos diferente, selecione **alterações** ao lado do grupo de recursos. Na página **de recursos Move,** escolha o grupo de recursos que deseja mover esta aplicação para:

![Portal de gestão: gestão de recursos](media/howto-manage-iot-central-from-portal/image4a.png)

Para mover a aplicação para uma subscrição diferente, selecione **alterações** ao lado da subscrição. Na página **de recursos move,** escolha a subscrição que deseja mover esta aplicação para:

![Portal de gestão: gestão de recursos](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a gerir as aplicações azure IoT Central do portal Azure, eis o próximo passo sugerido:

> [!div class="nextstepaction"]
> [Administrar a sua aplicação](howto-administer.md)
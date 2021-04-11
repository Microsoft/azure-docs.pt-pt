---
title: Gerir a IoT Central a partir do portal Azure | Microsoft Docs
description: Este artigo descreve como criar e gerir as suas aplicações IoT Central a partir do portal Azure.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 02/11/2020
ms.topic: how-to
manager: philmea
ms.openlocfilehash: 2af97206db00d683ab409710bc71a3b5048bf6ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "104658470"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>Gerir a IoT Central a partir do portal Azure

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Em vez de criar e gerir aplicações IoT Central no site do [gestor de aplicações Azure IoT Central,](https://aka.ms/iotcentral) pode utilizar o [portal Azure](https://portal.azure.com) para gerir as suas aplicações.

## <a name="create-iot-central-applications"></a>Criar aplicações IoT Central

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]


Para criar uma aplicação, navegue no [portal Azure](https://ms.portal.azure.com) e selecione **Criar um recurso.**

Em Pesquisa na barra **Marketplace,** tipo *IoT Central:*

![Portal de gestão: pesquisa](media/howto-manage-iot-central-from-portal/image0a1.png)

Selecione o azulejo da **Aplicação Central IoT** nos resultados da pesquisa:

![Portal de Gestão: resultados de pesquisa](media/howto-manage-iot-central-from-portal/image0b1.png)

Agora, selecione **Criar:**

![Portal de gestão: IoT Central recurso](media/howto-manage-iot-central-from-portal/image0c1.png)

Preencha todos os campos no formulário. Este formulário é semelhante ao formulário que preenche para criar aplicações no site do [gestor de aplicações Azure IoT Central.](https://aka.ms/iotcentral) Para obter mais informações, consulte o Arranque rápido da [aplicação Create a IoT Central.](quick-deploy-iot-central.md)

![Criar formulário central IoT](media/howto-manage-iot-central-from-portal/image6a.png)

**A localização** é a [geografia](https://azure.microsoft.com/global-infrastructure/geographies/) onde gostaria de criar a sua aplicação. Normalmente, deve escolher o local mais próximo fisicamente dos seus dispositivos para obter um desempenho ideal. A azure IoT Central está atualmente disponível nas geografias da **Austrália**, **Ásia Pacífico**, **Europa,** **Estados Unidos,** **Reino Unido** e **Japão.** Uma vez que você escolhe um local, você não pode mover a sua aplicação para um local diferente mais tarde.

Depois de preencher todos os campos, selecione **Criar**.

## <a name="manage-existing-iot-central-applications"></a>Gerir as aplicações existentes da IoT Central

Se já tiver uma aplicação Azure IoT Central, pode eliminá-la ou movê-la para um grupo de subscrição ou recursos diferente no portal Azure.

> [!NOTE]
> As aplicações criadas através do plano *gratuito* não requerem uma subscrição do Azure, pelo que não as encontrará listadas na subscrição do Azure no portal Azure. Só é possível ver e gerir aplicações gratuitas a partir do portal IoT Central.

Para começar, selecione **Todos os recursos** no portal. Selecione **Mostrar tipos ocultos** e comece a digitar o nome da sua aplicação no **Filter para** encontrá-lo. Em seguida, selecione a aplicação IoT Central que gostaria de gerir.

Para navegar para a aplicação, selecione o **URL de aplicação central IoT:**

![Screenshot que mostra a página "Overview" com o "IoT Central Application URL" realçado.](media/howto-manage-iot-central-from-portal/image3.png)

Para mover a aplicação para um grupo de recursos diferente, selecione **a alteração** ao lado do grupo de recursos. Na página **de recursos Move,** escolha o grupo de recursos que pretende mover esta aplicação para:

![Screenshot que mostra a página "Visão Geral" com o "Grupo de Recursos (alteração)" realçado.](media/howto-manage-iot-central-from-portal/image4a.png)

Para mover a aplicação para uma subscrição diferente, selecione  **alterar** ao lado da subscrição. Na página **de recursos Move,** escolha a subscrição que pretende mover esta aplicação para:

![Portal de gestão: gestão de recursos](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a gerir as aplicações da Azure IoT Central a partir do portal Azure, eis o próximo passo sugerido:

> [!div class="nextstepaction"]
> [Administrar a sua aplicação](howto-administer.md)
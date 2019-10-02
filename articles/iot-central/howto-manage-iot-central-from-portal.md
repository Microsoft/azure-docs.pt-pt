---
title: Gerenciar IoT Central do portal do Azure | Microsoft Docs
description: Gerencie IoT Central da portal do Azure.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 10/02/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: a8a2ff5e98948030609328a3de33399ede154a3d
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71815693"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>Gerenciar IoT Central do portal do Azure

[!INCLUDE [iot-central-selector-manage](../../includes/iot-central-selector-manage.md)]

Em vez de criar e gerenciar aplicativos IoT Central no site [do Azure IOT central Application Manager](https://aka.ms/iotcentral) , você pode usar o [portal do Azure](https://portal.azure.com) para gerenciar seus aplicativos.

## <a name="create-iot-central-applications"></a>Criar IoT Central aplicativos

Para criar um aplicativo, navegue até a [portal do Azure](https://ms.portal.azure.com) e selecione **criar um recurso** no menu de navegação principal à esquerda.

![Portal de gerenciamento: menu de navegação](media/howto-manage-iot-central-from-portal/image0.png)

Na barra de pesquisa, digite **IOT central**.

![Portal de gerenciamento: Pesquisar](media/howto-manage-iot-central-from-portal/image0a1.png)

Selecione a **IOT central** item de linha de aplicativo nos resultados da pesquisa.

![Portal de Gerenciamento: resultados da pesquisa](media/howto-manage-iot-central-from-portal/image0b1.png)

Agora, selecione **criar**.

![Portal de gerenciamento: IoT Central recurso](media/howto-manage-iot-central-from-portal/image0c1.png)

Preencha todos os campos no formulário. Esse formulário é semelhante ao formulário que você preenche para criar aplicativos no site [do Azure IOT central Application Manager](https://aka.ms/iotcentral) . Para obter mais informações, consulte o guia de início rápido [criar um aplicativo IOT central](quick-deploy-iot-central.md) .

**Local** é o local físico ou a [geografia](https://azure.microsoft.com/global-infrastructure/geographies/) onde você gostaria de criar seu aplicativo. Normalmente, você deve escolher o local que está fisicamente mais próximo de seus dispositivos para obter o desempenho ideal. Você pode ver as regiões nas quais o Azure IoT Central está disponível na página [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=iot-central) . Depois de escolher um local, você não poderá mover seu aplicativo para um local diferente mais tarde.

> [!NOTE]
> No momento, o modelo de **aplicativo de visualização** só está disponível nas regiões **Europa setentrional** e **EUA Central** .

![Portal de gerenciamento: criar IoT Central recurso](media/howto-manage-iot-central-from-portal/image1a.png)  

Depois de preencher todos os campos, selecione **criar**.

## <a name="manage-existing-iot-central-applications"></a>Gerenciar aplicativos IoT Central existentes

Se você já tiver um aplicativo de IoT Central do Azure, poderá excluí-lo ou movê-lo para uma assinatura ou grupo de recursos diferente no portal do Azure.

> [!NOTE]
> Você não pode ver os aplicativos de avaliação no portal do Azure porque eles não estão associados à sua assinatura.

Para começar, selecione **todos os recursos** no menu de navegação principal à esquerda. Use a caixa de pesquisa para digitar o nome do seu aplicativo para localizá-lo em sua lista de recursos. Em seguida, selecione o aplicativo IoT Central que você gostaria de gerenciar.

![Portal de gerenciamento: gerenciamento de recursos](media/howto-manage-iot-central-from-portal/image2a.png)

Para navegar até o aplicativo, selecione a URL do aplicativo IoT Central.

![Portal de gerenciamento: gerenciamento de recursos](media/howto-manage-iot-central-from-portal/image3.png)

Para mover o aplicativo para um grupo de recursos diferente, selecione **alterar** ao lado do grupo de recursos. Na página **mover recursos** , escolha o grupo de recursos para o qual você gostaria de migrar este aplicativo.

![Portal de gerenciamento: gerenciamento de recursos](media/howto-manage-iot-central-from-portal/image4a.png)

Para mover o aplicativo para uma assinatura diferente, selecione o link **alterar** ao lado da assinatura. Escolha a assinatura para a qual você gostaria de migrar esse aplicativo na caixa de diálogo que aparece.

![Portal de gerenciamento: gerenciamento de recursos](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>Passos seguintes

Agora que você aprendeu a gerenciar os aplicativos do Azure IoT Central do portal do Azure, aqui está a próxima etapa sugerida:

> [!div class="nextstepaction"]
> [Administre seu aplicativo](howto-administer.md)
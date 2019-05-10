---
title: Gerir o Centro de IoT a partir do portal do Azure | Documentos da Microsoft
description: Gerir o IoT Central do portal do Azure.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 01/14/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: c72de0ef874659a5d7840689e38bd7857c25b840
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65464158"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>Gerir o Centro de IoT a partir do portal do Azure

[!INCLUDE [iot-central-selector-manage](../../includes/iot-central-selector-manage.md)]

Em vez de criar e gerir aplicações de IoT Central a partir do Centro de IoT [Gerenciador de aplicativos](https://aka.ms/iotcentral) página, pode utilizar os [portal do Azure](https://portal.azure.com) para gerir as suas aplicações.

## <a name="create-iot-central-applications"></a>Criar aplicações de IoT Central

Para criar uma aplicação, navegue para o [portal do Azure](https://ms.portal.azure.com) e selecione **criar um recurso** no menu de navegação principal à esquerda.

![Portal de gestão: o menu de navegação](media/howto-manage-iot-central-from-portal/image0.png)

Na barra de pesquisa, escreva **IoT Central**.

![Portal de gestão: pesquisa](media/howto-manage-iot-central-from-portal/image0a1.png)

Selecione o **aplicação de IoT Central** item de linha nos resultados da pesquisa.

![Portal de gestão: os resultados da pesquisa](media/howto-manage-iot-central-from-portal/image0b1.png)

Agora, selecione **criar**.

![Portal de gestão: Recursos de IoT Central](media/howto-manage-iot-central-from-portal/image0c1.png)

Preencha todos os campos no formulário. Esse formulário é semelhante ao formulário que preenche para criar aplicações no Centro de IoT [Gerenciador de aplicativos](https://aka.ms/iotcentral) página. Para obter mais informações, consulte a [criar uma aplicação IoT Central](quick-deploy-iot-central.md) início rápido.

![Portal de gestão: criar recursos de IoT Central](media/howto-manage-iot-central-from-portal/image1a.png)  

Depois de preencher todos os campos, selecione **criar**.

## <a name="manage-existing-iot-central-applications"></a>Gerir aplicações de IoT Central existentes

Se já tiver uma aplicação do Azure IoT Central, pode eliminá-lo ou movê-lo para um grupo diferente de subscrição ou ao recurso no portal do Azure.

> [!NOTE]
> Não é possível ver aplicações de versão de avaliação no portal do Azure porque não estão associados com a sua subscrição.

Para começar a utilizar, selecione **todos os recursos** no menu de navegação principal à esquerda. Utilize a caixa de pesquisa para digite o nome da sua aplicação para encontrá-lo na sua lista de recursos. Em seguida, selecione a aplicação do Centro de IoT que pretende gerir.

![Portal de gestão: gestão de recursos](media/howto-manage-iot-central-from-portal/image2a.png)

Para navegar para a aplicação, selecione o URL da aplicação IoT Central.

![Portal de gestão: gestão de recursos](media/howto-manage-iot-central-from-portal/image3.png)

Para mover a aplicação a um grupo de recursos diferente, selecione **alterar** junto ao grupo de recursos. Sobre o **mover recursos** página, escolha o grupo de recursos que pretende migrar esta aplicação.

![Portal de gestão: gestão de recursos](media/howto-manage-iot-central-from-portal/image4a.png)

Para mover a aplicação para uma subscrição diferente, selecione o **alterar** ligação junto a subscrição. Escolha a subscrição à qual pretende migrar esta aplicação na caixa de diálogo que aparece.

![Portal de gestão: gestão de recursos](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu como gerir aplicações do Azure IoT Central do portal do Azure, este é o passo seguinte sugerido:

> [!div class="nextstepaction"]
> [Administrar a sua aplicação](howto-administer.md)
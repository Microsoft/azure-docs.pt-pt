---
title: Criar dashboards pessoais Azure IoT Central | Documentos da Microsoft
description: Como um utilizador, saiba como criar e gerir os seus dashboards pessoais.
author: dominicbetts
ms.author: dobett
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: c048ae8c0daba0e467a9243f4dd83f8d95921e10
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67502644"
---
# <a name="create-and-manage-personal-dashboards"></a>Criar e gerir dashboards pessoais

O **Dashboard** é a página que carrega ao navegar pela primeira vez para seu aplicativo. R **builder** em seu aplicativo define o dashboard de aplicações predefinido para todos os utilizadores. Pode substituir este dashboard predefinido com seu próprios, dashboard de aplicações personalizadas. Pode ter vários dashboards que exibam dados diferentes e alternar entre elas.

## <a name="create-dashboard"></a>criar dashboard

Captura de ecrã seguinte mostra o dashboard numa aplicação criada a partir da **exemplo Contoso** modelo. Pode substituir o dashboard de aplicações predefinido com um dashboard pessoal. Para tal, selecione **+ novo** na parte superior direita da página.

![Dashboard de aplicações com base no modelo de "Exemplo Contoso"](media/howto-personalize-dashboard/defaultdashboard.png)

Selecionando **+ novo**, abre o editor de dashboard. No editor, pode dar o dashboard com um nome e escolher itens da biblioteca. A biblioteca contém os mosaicos e primitivos de dashboard, que pode utilizar para personalizar o dashboard.

![Biblioteca do dashboard](media/howto-personalize-dashboard/dashboardeditor.png)

Por exemplo, pode adicionar um **as definições e propriedades** mosaico para mostrar os valores de propriedades e definições para um dos dispositivos que gere. Para tal, primeiro selecione um **modelo de dispositivo** , em seguida, selecione um **dispositivo instância**. Em seguida, dar o mosaico a um título e selecione um **definição** ou uma **propriedade** para apresentar. A captura de ecrã a seguir mostra a **fã velocidade** definição selecionada para adicionar ao mosaico. Selecione **feito** para guardar a alteração ao seu dashboard.

![Formulário de "Configurar os detalhes do dispositivo" com detalhes para as definições e propriedades](media/howto-personalize-dashboard/dashboardsetting.png)

Agora, quando vir o seu dashboard pessoa, pode ver o novo mosaico com o **fã velocidade** definição para o dispositivo:

![Separador "Dashboard" com as definições apresentadas e propriedades para o mosaico](media/howto-personalize-dashboard/personaldashboard.png)

Pode explorar outros tipos de mosaico na biblioteca para saber como personalizar ainda mais os seus dashboards pessoais.

Para saber mais sobre como utilizar mosaicos no Azure IoT Central, consulte [utilizar mosaicos de dashboard](howto-use-tiles.md).

## <a name="manage-dashboards"></a>Gerir dashboards

Pode ter vários painéis pessoais e alternar entre elas, ou escolher o dashboard de aplicações predefinido:

![Dashboard de comutador](media/howto-personalize-dashboard/switchdashboards.png)

Pode editar os seus dashboards pessoais e eliminá-los que já não precisar de:

![Eliminar dashboard](media/howto-personalize-dashboard/managedashboards.png)

## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu como criar e gerir dashboards pessoais, pode:

> [!div class="nextstepaction"]
> [Saiba como gerir as suas preferências de aplicação](howto-manage-preferences.md)

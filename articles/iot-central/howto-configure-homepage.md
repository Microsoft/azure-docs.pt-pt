---
title: Configurar o dashboard de aplicações do Azure IoT Central | Documentos da Microsoft
description: Como um construtor, saiba como configurar o dashboard de aplicações do Azure IoT Central do padrão.
author: dominicbetts
ms.author: dobett
ms.date: 02/13/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 033a7207765f2ee6712f8552d5c1585280250d5e
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57312074"
---
# <a name="configure-the-application-dashboard"></a>Configurar o dashboard de aplicações

O **Dashboard** é a página que carrega quando os utilizadores que têm acesso à aplicação, navegue até ao URL da aplicação. Se tiver selecionado um a **Contoso de exemplo** ou **exemplo Devkits** o modelo de aplicativo para criar a sua aplicação, seu aplicativo tem um dashboard predefinido. Se tiver escolhido o **aplicativo personalizado** o modelo de aplicativo, o seu dashboard está em branco.

> [!NOTE]
> Os utilizadores também podem [criar seus próprios dashboards pessoais](howto-personalize-dashboard.md) para utilizar em vez do dashboard de aplicações predefinido.

## <a name="add-tiles"></a>Adicionar mosaicos

Captura de ecrã seguinte mostra o dashboard numa aplicação criada a partir da **exemplo Contoso** modelo. Para personalizar o dashboard predefinido para a sua aplicação, selecione **editar** na parte superior direita da página.

![Dashboard de aplicações com base no modelo de "Exemplo Contoso"](media/howto-configure-homepage/image1.png)

Selecionando **editar**, abre o painel de biblioteca do dashboard. A biblioteca contém os mosaicos e primitivos de dashboard, que pode utilizar para personalizar o dashboard.

![Biblioteca do dashboard](media/howto-configure-homepage/image2.png)

Por exemplo, pode adicionar um **as definições e propriedades** mosaico para mostrar uma seleção dos valores de propriedades e definições atuais de um dispositivo. Para tal, primeiro selecione um **modelo de dispositivo** , em seguida, selecione um **dispositivo instância**. Depois que fornecem o mosaico a um título e selecione um **definição** ou uma **propriedade** para apresentar. A captura de ecrã a seguir mostra a **definir a temperatura** definição selecionada para adicionar ao mosaico. Selecione **feito** para guardar a alteração ao dashboard.

![Formulário de "Configurar os detalhes do dispositivo" com detalhes para as definições e propriedades](media/howto-configure-homepage/image3.png)

Agora, quando um operador visualiza o dashboard de aplicações predefinido, verão o mosaico novo com o **definir a temperatura** definição para o dispositivo:

![Separador "Dashboard" com as definições apresentadas e propriedades para o mosaico](media/howto-configure-homepage/image4.png)

Pode explorar outros tipos de mosaico na biblioteca para saber como personalizar ainda mais o dashboard de aplicações predefinido.

## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu como configurar o seu dashboard de aplicações padrão do Azure IoT Central, pode:

> [!div class="nextstepaction"]
> [Saiba como preparar e carregar imagens](howto-prepare-images.md)

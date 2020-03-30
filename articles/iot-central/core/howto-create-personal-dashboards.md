---
title: Crie dashboards pessoais Azure IoT Central [ Microsoft Docs
description: Como utilizador, aprenda a criar e gerir os seus dashboards pessoais.
author: mavoge
ms.author: mavoge
ms.date: 10/17/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: db886006ff5b9adf3de0932951f6cce4958e8ebd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158134"
---
# <a name="create-and-manage-multiple-dashboards"></a>Criar e gerir vários dashboards

O **Dashboard** é a página que carrega quando navega pela primeira vez para a sua aplicação. Um **construtor** na sua aplicação define o dashboard de aplicação padrão para todos os utilizadores. Pode substituir este dashboard predefinido por um painel de aplicação personalizado. Pode ter vários dashboards que exibem diferentes dados e alternam entre eles. 

Se for **administrador** da aplicação, também pode criar até 10 dashboards de nível de aplicação para partilhar com outros utilizadores da aplicação. Apenas **os administradores** têm a capacidade de criar, editar e eliminar os dashboards de nível de aplicação. 

## <a name="create-dashboard"></a>Criar painel de instrumentos

A imagem seguinte mostra o dashboard numa aplicação criada a partir do modelo de **aplicação personalizada.** Pode substituir o painel de aplicações predefinido por um dashboard pessoal, ou se for administrador, outro painel de instrumentos de nível de aplicação. Para isso, selecione **+ Novo** na parte superior à esquerda da página.
 
> [!div class="mx-imgBorder"]
> ![Painel de instrumentos para aplicações com base no modelo "Aplicação Personalizada"](media/howto-create-personal-dashboards/dashboard-custom-app.png)

Selecionar **+ Nova** abre o editor do dashboard. No editor, pode dar um nome ao seu painel de instrumentos e escolher artigos da biblioteca. A biblioteca contém os azulejos e os primitivos do painel que pode usar para personalizar o painel de instrumentos.

> [!div class="mx-imgBorder"]
> ![Biblioteca do painel de instrumentos](media/howto-create-personal-dashboards/dashboard-library.png)

Se for **administrador** da aplicação, terá a opção de alternar se quiser criar um painel de instrumentos de nível pessoal ou um painel de instrumentos de nível de aplicação. Se criar um painel de instrumentos de nível pessoal, só poderá vê-lo. Se criar um painel de instrumentos de nível de aplicação, todos os utilizadores da aplicação poderão vê-lo. Depois de introduzir um título e selecionar o tipo de painel que pretende criar, pode guardar e adicionar azulejos mais tarde. Ou se estiver pronto agora e tiver adicionado um modelo de dispositivo e uma instância de dispositivo, pode ir em frente e criar o seu primeiro azulejo. 

> [!div class="mx-imgBorder"]
> ![Configurar detalhes do dispositivo" formulário com detalhes para temperatura](media/howto-create-personal-dashboards/device-details.png)

Por exemplo, pode adicionar um azulejo **telemetria** para a temperatura atual do dispositivo. Para tal:
1. Selecione um **modelo de dispositivo**
1. Selecione uma Instância de **Dispositivo** para o dispositivo que pretende ver num azulejo do painel de instrumentos. Em seguida, você verá uma lista das propriedades do dispositivo que pode ser usada no azulejo.
1. Para criar o azulejo no painel de instrumentos, clique em **Temperatura** e arraste-o para a área do painel de instrumentos. Também pode clicar na caixa de verificação ao lado da **Temperatura** e clicar em **Combine**. A imagem seguinte mostra selecionando um modelo de dispositivo e instância de dispositivo, criando um azulejo de telemetria de temperatura no painel de instrumentos.
1. Selecione **Guardar** na parte superior esquerda para guardar o azulejo para o tablier.

> [!div class="mx-imgBorder"]
> ![Tab dashboard com detalhes para o azulejo temperatura](media/howto-create-personal-dashboards/temperature-tile-edit.png)

Agora, quando vê o seu painel pessoal, vê o novo azulejo com a definição **de temperatura** para o dispositivo:

> [!div class="mx-imgBorder"]
> ![Tab dashboard com detalhes para o azulejo temperatura](media/howto-create-personal-dashboards/temperature-tile-complete.png)

Você pode explorar outros tipos de azulejos na biblioteca para descobrir como personalizar ainda mais seus dashboards pessoais.

Para saber mais sobre como usar azulejos em Azure IoT Central, consulte [Adicionar azulejos ao seu Dashboard](howto-add-tiles-to-your-dashboard.md).

## <a name="manage-dashboards"></a>Gerir dashboards

Pode ter vários dashboards pessoais e alternar entre eles ou escolher entre um dos dashboards de aplicação padrão:

> [!div class="mx-imgBorder"]
> ![Alternar entre dashboards](media/howto-create-personal-dashboards/switch-dashboards.png)

Pode editar os seus dashboards pessoais e eliminar quaisquer painéis de que já não necessite. Se for **administrador,** também tem a capacidade de editar ou eliminar os dashboards de nível de aplicação.

> [!div class="mx-imgBorder"]
> ![Eliminar dashboards](media/howto-create-personal-dashboards/delete-dashboards.png)

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a criar e gerir dashboards pessoais, pode [aprender a gerir as suas preferências](howto-manage-preferences.md) de candidatura

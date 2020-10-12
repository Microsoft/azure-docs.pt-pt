---
title: Criar dashboards pessoais Azure IoT Central Microsoft Docs
description: Como utilizador, aprenda a criar e gerir os seus dashboards pessoais.
author: mavoge
ms.author: mavoge
ms.date: 10/17/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: f7dca7352a49e668231f64632371a445985e4de3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "83634563"
---
# <a name="create-and-manage-multiple-dashboards"></a>Criar e gerir vários dashboards

O **Dashboard** é a página que carrega quando navega pela primeira vez para a sua aplicação. Um **construtor** na sua aplicação define o painel de aplicações predefinido para todos os utilizadores. Pode ainda criar o seu próprio painel de aplicações personalizado. Pode ter vários dashboards que exibem dados diferentes e alternam entre eles.

Se for **um administrador** da aplicação, também pode criar até 10 dashboards de nível de aplicação para compartilhar com outros utilizadores da aplicação. Apenas **os administradores** têm a capacidade de criar, editar e eliminar os dashboards de nível de aplicação.  

## <a name="create-dashboard"></a>Criar painel de instrumentos

A imagem que se segue mostra o painel de instrumentos numa aplicação criada a partir do modelo **de Aplicação Personalizada.** Pode substituir o painel de aplicações predefinido por um painel de instrumentos pessoal, ou se for administrador, outro painel de instrumentos de nível de aplicação. Para tal, selecione **+ Novo** no topo esquerdo da página.

> [!div class="mx-imgBorder"]
> ![Painel de aplicações baseado no modelo "Aplicação Personalizada"](media/howto-create-personal-dashboards/dashboard-custom-app.png)

Selecionando **+ Novo** abre o editor do painel de instrumentos. No editor, pode dar um nome ao seu painel de instrumentos e escolher itens da biblioteca. A biblioteca contém os azulejos e os primitivos do painel de instrumentos que pode usar para personalizar o tablier.

> [!div class="mx-imgBorder"]
> ![Biblioteca do painel](media/howto-create-personal-dashboards/dashboard-library.png)

Se for **um administrador** da aplicação, terá a opção de criar um dashboard de nível pessoal ou um painel de instrumentos de nível de aplicação. Se criar um dashboard de nível pessoal, só poderá vê-lo. Se criar um dashboard de nível de aplicação, todos os utilizadores da aplicação poderão vê-la. Depois de introduzir um título e selecionar o tipo de painel de instrumentos que pretende criar, pode guardar e adicionar azulejos mais tarde. Ou se estiver pronto agora e tiver adicionado um modelo de dispositivo e uma instância do dispositivo, pode ir em frente e criar o seu primeiro azulejo.  

> [!div class="mx-imgBorder"]
> ![Configure detalhes do dispositivo" formulário com detalhes para temperatura](media/howto-create-personal-dashboards/device-details.png)

Por exemplo, pode adicionar um **azulejo de telemetria** para a temperatura atual do dispositivo. Para tal:

1. Selecione um **modelo de dispositivo**
1. Selecione um dispositivo de **Dispositivos** para o dispositivo que pretende ver num azulejo do painel de instrumentos. Em seguida, verá uma lista das propriedades do dispositivo que podem ser usadas no azulejo.
1. Para criar o azulejo no painel de instrumentos, clique na **Temperatura** e arraste-o para a área do painel de instrumentos. Também pode clicar na caixa de verificação ao lado **da Temperatura** e clicar em **adicionar azulejo**. A imagem que se segue mostra a seleção de um modelo e dispositivo do dispositivo do dispositivo, em seguida, criar um azulejo de telemetria de temperatura no painel de instrumentos.
1. **Selecione Guarde** na parte superior esquerda para guardar as alterações no painel de instrumentos.

> [!div class="mx-imgBorder"]
> ![Separador do painel com detalhes para o azulejo de temperatura](media/howto-create-personal-dashboards/temperature-tile-edit.png)

Agora, quando vê o seu painel pessoal, vê o novo azulejo com a definição **de temperatura** para o dispositivo:

> [!div class="mx-imgBorder"]
> ![Separador do painel com detalhes para o azulejo de temperatura](media/howto-create-personal-dashboards/temperature-tile-complete.png)

Você pode explorar outros tipos de azulejos na biblioteca para descobrir como personalizar ainda mais seus dashboards pessoais.

Para saber mais sobre como usar azulejos no Azure IoT Central, consulte [Adicionar Telhas ao seu Painel de Instrumentos](howto-add-tiles-to-your-dashboard.md).

## <a name="manage-dashboards"></a>Gerir dashboards

Pode ter vários dashboards pessoais e alternar entre eles ou escolher entre um dos dashboards de aplicação predefinidos:

> [!div class="mx-imgBorder"]
> ![Alternar entre tabliers](media/howto-create-personal-dashboards/switch-dashboards.png)

Pode editar os seus dashboards pessoais e eliminar os dashboards de que já não necessita. Se for **um administrador,** também tem a capacidade de editar ou eliminar dashboards de nível de aplicação.

> [!div class="mx-imgBorder"]
> ![Apagar dashboards](media/howto-create-personal-dashboards/delete-dashboards.png)

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a criar e gerir dashboards pessoais, pode [aprender a gerir as suas preferências de candidatura.](howto-manage-preferences.md)

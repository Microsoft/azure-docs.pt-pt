---
title: Crie painéis de azure IoT Central | Microsoft Docs
description: Aprenda a criar e gerir os seus dashboards.
author: mavoge
ms.author: mavoge
ms.date: 10/17/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 6fc99470fdc52a2dc6553056f305226f8348550c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100366726"
---
# <a name="create-and-manage-multiple-dashboards"></a>Criar e gerir vários dashboards

O **Dashboard** é a página que carrega quando navega pela primeira vez para a sua aplicação. Um **construtor** na sua aplicação define o painel de aplicações predefinido para todos os utilizadores. Pode ainda criar o seu próprio painel de aplicações personalizado. Pode ter vários dashboards que exibem dados diferentes e alternam entre eles.

Se for **um administrador** da aplicação, também pode criar até 10 dashboards de nível de aplicação para compartilhar com outros utilizadores da aplicação. Apenas **os administradores** podem criar, editar e eliminar os dashboards de nível de aplicação.  

## <a name="create-dashboard"></a>Criar painel de instrumentos

A imagem que se segue mostra o painel de instrumentos numa aplicação criada a partir do modelo **de Aplicação Personalizada.** Pode substituir o painel de aplicações predefinido por um painel de instrumentos pessoal, ou se for administrador, outro painel de aplicação. Para tal, selecione **+ Novo** no topo esquerdo da página.

> [!div class="mx-imgBorder"]
> ![Painel de aplicações baseado no modelo "Aplicação Personalizada"](media/howto-create-personal-dashboards/dashboard-custom-app.png)

Selecionando **+ Novo** abre o editor do painel de instrumentos. No editor, pode dar um nome ao seu painel de instrumentos e escolher itens da biblioteca. A biblioteca contém os azulejos e os primitivos do painel de instrumentos que pode usar para personalizar o tablier.

> [!div class="mx-imgBorder"]
> ![Biblioteca do painel](media/howto-create-personal-dashboards/dashboard-library.png)

Se for **um administrador** da aplicação, terá a opção de criar um dashboard de nível pessoal ou um painel de aplicação. Se criar um painel de nível pessoal, só você pode vê-lo. Se criar um painel de aplicação, todos os utilizadores da aplicação podem vê-lo. Depois de introduzir um título e selecionar o tipo de painel de instrumentos que pretende criar, pode guardar e adicionar azulejos mais tarde. Ou se estiver pronto agora e tiver adicionado um modelo de dispositivo e uma instância do dispositivo, pode ir em frente e criar o seu primeiro azulejo.  

> [!div class="mx-imgBorder"]
> ![Configure detalhes do dispositivo" formulário com detalhes para temperatura](media/howto-create-personal-dashboards/device-details.png)

Por exemplo, pode adicionar um **azulejo de telemetria** para a temperatura atual do dispositivo. Para tal:

1. Selecione um **modelo de dispositivo**
1. Selecione um dispositivo de **Dispositivos** para o dispositivo que pretende ver num azulejo do painel de instrumentos. Em seguida, verá uma lista das propriedades do dispositivo que podem ser usadas no azulejo.
1. Para criar o azulejo no painel de instrumentos, selecione **Temperatura** e arraste-o para a área do painel de instrumentos. Também pode selecionar a caixa de verificação ao lado **da Temperatura** e selecionar **Adicionar azulejo**. A imagem que se segue mostra a seleção de um modelo e dispositivo do dispositivo do dispositivo, em seguida, criar um azulejo de telemetria de temperatura no painel de instrumentos.
1. **Selecione Guarde** na parte superior esquerda para guardar as alterações no painel de instrumentos.

> [!div class="mx-imgBorder"]
> ![Separador do painel com detalhes para o azulejo de temperatura](media/howto-create-personal-dashboards/temperature-tile-edit.png)

Agora, quando vê o seu painel pessoal, vê o novo azulejo com a definição **de temperatura** para o dispositivo:

> [!div class="mx-imgBorder"]
> ![Screenshot que mostra o novo azulejo com a definição de temperaturas para o dispositivo.](media/howto-create-personal-dashboards/temperature-tile-complete.png)

Você pode explorar outros tipos de azulejos na biblioteca para descobrir como personalizar ainda mais seus dashboards pessoais.

Para saber mais sobre como usar azulejos no Azure IoT Central, consulte [Adicionar Telhas ao seu Painel de Instrumentos](howto-add-tiles-to-your-dashboard.md).

## <a name="manage-dashboards"></a>Gerir dashboards

Pode ter vários dashboards pessoais e alternar entre eles ou escolher entre um dos dashboards de aplicação predefinidos:

> [!div class="mx-imgBorder"]
> ![Alternar entre tabliers](media/howto-create-personal-dashboards/switch-dashboards.png)

Pode editar os seus dashboards pessoais e eliminar os dashboards de que já não necessita. Se for **um administrador,** pode editar ou eliminar os dashboards de nível de aplicação também.

> [!div class="mx-imgBorder"]
> ![Apagar dashboards](media/howto-create-personal-dashboards/delete-dashboards.png)

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a criar e gerir dashboards pessoais, pode [aprender a gerir as suas preferências de candidatura.](howto-manage-preferences.md)

---
title: Registar a sua aplicação - serviço de decisão personalizada
titlesuffix: Azure Cognitive Services
description: Um passo a passo guiam como registar uma nova aplicação com o serviço de decisão personalizada do Azure.
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: slivkins
ms.reviewer: marcozo
ms.openlocfilehash: c56aef180f5e16d2ec1931caabe04295ef288ca9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60509788"
---
# <a name="register-your-application"></a>Registar a sua aplicação

Para utilizar o serviço de decisão personalizada para a sua aplicação, tem de registá-lo no portal. Este artigo explica como.

1. Vá para o [página frontal](https://portal.ds.microsoft.com/) do serviço de decisão personalizada. No Friso, clique em **meu Portal**, conforme destacado na imagem:

    ![Meu Portal](./media/portal.png)

    Se não tiver iniciado sessão, o portal irá pedir-lhe para iniciar sessão com sua [conta Microsoft](https://account.microsoft.com/account). Depois de iniciar sessão, o portal apresenta sua conta Microsoft no canto superior direito da página.

2. Para registar a sua aplicação, clique nas **nova aplicação** botão.

3. Na caixa de diálogo, escolha um ID de aplicação para a sua aplicação. Serviço de decisão personalizada requer um ID exclusivo para cada aplicativo. Se alguém já tiver atribuído este ID, o sistema solicita que escolha outro.

4. Especifique um conjunto de ação API. Esta definição é um RSS ou Atom feed que comunica o conteúdo disponível para a sua aplicação para o serviço de decisão personalizada. Introduza um nome para o feed e introduza o URL a partir do qual ele é fornecido. Para efetuar este passo mais tarde, clique nas **Feeds** botão e, em seguida, clique nas **novo feed** botão. Um exemplo que cria um feed RSS é descrito mais à frente.

5. Para registar a sua aplicação, selecione o **aplicação personalizada** caixa de verificação no canto inferior esquerdo. Introduza um [cadeia de ligação](../../storage/common/storage-configure-connection-string.md) para a conta de armazenamento do Azure onde os dados da sua aplicação são registados. Para obter mais informações sobre como criar uma conta de armazenamento, consulte [como criar, gerir ou eliminar uma conta de armazenamento](../../storage/common/storage-create-storage-account.md).

### <a name="next-steps"></a>Passos Seguintes

* Começar a otimizar [uma página Web](custom-decision-service-get-started-browser.md) ou [uma aplicação de smartphone](custom-decision-service-get-started-app.md).
* Trabalhar com um [tutorial](custom-decision-service-tutorial-news.md) para obter um exemplo mais aprofundado.
* Veja a [Referência à API](custom-decision-service-api-reference.md) para saber mais sobre a funcionalidade fornecida.

---
title: Registrar seu aplicativo-Serviço de Decisão Personalizada
titlesuffix: Azure Cognitive Services
description: Um guia passo a passo sobre como registrar um novo aplicativo com o Azure Serviço de Decisão Personalizada.
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: slivkins
ms.reviewer: marcozo
ROBOTS: NOINDEX
ms.openlocfilehash: 4a8e4e91e768e3ad28ea9ff88c18515db4a40323
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704557"
---
# <a name="register-your-application"></a>Registar a sua aplicação

Para usar Serviço de Decisão Personalizada para seu aplicativo, registre-o no Portal. Este artigo explica como.

1. Vá para a [página frontal](https://portal.ds.microsoft.com/) de serviço de decisão personalizada. Na faixa de faixas, clique em **meu portal**, conforme realçado na imagem:

    ![Meu Portal](./media/portal.png)

    Se você ainda não tiver entrado, o portal solicitará que você entre com seu [conta Microsoft](https://account.microsoft.com/account). Depois de entrar, o portal exibe o conta Microsoft no canto superior direito da página.

2. Para registrar seu aplicativo, clique no botão **novo aplicativo** .

3. Na caixa de diálogo, escolha uma ID de aplicativo para seu aplicativo. Serviço de Decisão Personalizada requer uma ID exclusiva para cada aplicativo. Se outra pessoa já tiver usado essa ID, o sistema solicitará que você escolha uma diferente.

4. Especifique uma API de conjunto de ações. Essa configuração é um feed RSS ou Atom que comunica o conteúdo disponível para o seu aplicativo para Serviço de Decisão Personalizada. Insira um nome para o feed e insira a URL da qual ele é atendido. Para fazer essa etapa mais tarde, clique no botão feeds e, em seguida, clique no botão **novo feed** . Um exemplo que cria um RSS feed é descrito posteriormente.

5. Para registrar seu aplicativo, marque a caixa de seleção **aplicativo personalizado** no canto inferior esquerdo. Insira uma [cadeia de conexão](../../storage/common/storage-configure-connection-string.md) para a conta de armazenamento do Azure onde os dados do aplicativo são registrados. Para obter mais informações sobre como criar uma conta de armazenamento, consulte [como criar, gerenciar ou excluir uma conta de armazenamento](../../storage/common/storage-create-storage-account.md).

### <a name="next-steps"></a>Passos Seguintes

* Começar a otimizar [uma página Web](custom-decision-service-get-started-browser.md) ou [uma aplicação de smartphone](custom-decision-service-get-started-app.md).
* Trabalhe em um [tutorial](custom-decision-service-tutorial-news.md) para obter um exemplo mais aprofundado.
* Veja a [Referência à API](custom-decision-service-api-reference.md) para saber mais sobre a funcionalidade fornecida.

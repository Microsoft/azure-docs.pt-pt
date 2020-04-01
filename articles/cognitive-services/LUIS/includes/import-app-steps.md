---
title: Passos de aplicações de importação
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/30/2020
ms.author: diberry
ms.openlocfilehash: b7b8befa0f5871b65b9b5621bfb99c659bf07235
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422761"
---
1. Na [pré-visualização do portal LUIS](https://preview.luis.ai), na página **my apps,** selecione **+ Nova aplicação para conversação,** em **seguida, Import as JSON**. Encontre o ficheiro JSON guardado do passo anterior. Não precisa de mudar o nome da aplicação. Selecione **Feito**

1. A partir da secção **Gerir,** no `0.1` separador **Versões,** selecione a versão e, em seguida, `ml-entity`selecione **Clone** para clonar a versão e dê-lhe um novo nome de 10 caracteres, em seguida, selecione **Done** para terminar o processo de clone. Como o nome da versão é utilizado como parte da rota de URL, o nome não pode conter carateres que não sejam válidos num URL.

    > [!TIP]
    > Clonar numa nova versão é uma boa prática antes de modificar a sua aplicação. Quando terminar com uma alteração para uma versão, exporte a versão (como um ficheiro .json ou .lu) e verifique o ficheiro no seu sistema de controlo de origem.

1. Selecione **Build** then **Intents** para ver as intenções, os principais blocos de construção de uma app LUIS.

    ![Mude da página Versões para a página De Intenções.](../media/tutorial-machine-learned-entity/new-version-imported-app.png)
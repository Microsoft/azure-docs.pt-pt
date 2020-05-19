---
title: Passos de aplicações de importação
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/07/2020
ms.author: diberry
ms.openlocfilehash: 4c87ea9ebb2dd7aec36025e4b45f74e186ca1cd4
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588797"
---
1. No [portal LUIS](https://www.luis.ai), na página **my apps,** selecione **+ Nova aplicação para conversação,** em **seguida, Importar como JSON**. Encontre o ficheiro JSON guardado do passo anterior. Não precisa de mudar o nome da aplicação. Selecione **Feito**

1. A partir da secção **Gerir,** no separador **Versões,** selecione a `0.1` versão e, em seguida, selecione **Clone** para clonar a versão e dê-lhe um novo nome de `ml-entity` , em seguida, selecione **Done** para terminar o processo de clone. Como o nome da versão é utilizado como parte da rota de URL, o nome não pode conter carateres que não sejam válidos num URL.

    > [!TIP]
    > Clonar numa nova versão é uma boa prática antes de modificar a sua aplicação. Quando terminar com uma alteração para uma versão, exporte a versão (como um ficheiro .json ou .lu) e verifique o ficheiro no seu sistema de controlo de origem.

1. Selecione **Build** then **Intents** para ver as intenções, os principais blocos de construção de uma app LUIS.

    ![Mude da página Versões para a página De Intenções.](../media/tutorial-machine-learned-entity/new-version-imported-app.png)
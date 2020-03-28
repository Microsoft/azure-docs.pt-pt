---
title: Passos de aplicações de importação
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 12/03/2019
ms.author: diberry
ms.openlocfilehash: b0d1735df7a3ac36e20af771939aec6b8013f2df
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74840821"
---
1. Na [pré-visualização](https://preview.luis.ai)do portal LUIS , na página **my apps,** selecione **Import,** em **seguida, Import as JSON**. Encontre o ficheiro JSON guardado do passo anterior. Não precisa de mudar o nome da aplicação. Selecione **Feito**

1. A partir da secção **Gerir,** no separador **Versões,** selecione a versão e, em seguida, selecione **Clone** para clonar a versão e dê-lhe um novo nome de 10 caracteres, em seguida, selecione **Done** para terminar o processo de clone. Como o nome da versão é utilizado como parte da rota de URL, o nome não pode conter carateres que não sejam válidos num URL.

    > [!TIP]
    > Clonar numa nova versão é uma boa prática antes de modificar a sua aplicação. Quando terminar uma versão, exporte a versão (como um ficheiro .json ou .lu) e verifique o ficheiro no seu sistema de controlo de origem.

1. Selecione **Build** then **Intents** para ver as intenções, os principais blocos de construção de uma app LUIS.

    ![Mude da página Versões para a página De Intenções.](../media/tutorial-machine-learned-entity/new-version-imported-app.png)
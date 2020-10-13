---
title: Passos de aplicação de importação
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 06/22/2020
ms.openlocfilehash: aa0ba1de63cbcf1d9485c71b6bec7a29afb5140f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91545602"
---
1. No [portal LUIS,](https://www.luis.ai)na página **My apps,** selecione **+ Nova aplicação para conversação,** em seguida **Import as JSON**. Encontre o ficheiro JSON guardado do passo anterior. Não precisa de mudar o nome da aplicação. Selecione **Feito**

1. A partir da secção **Gerir,** no separador **Versões,** selecione a `0.1` versão e, em seguida, selecione **Clone** para clonar a versão e dê-lhe um novo nome de `ml-entity` , em seguida, selecione **Feito** para terminar o processo de clone. Como o nome da versão é utilizado como parte da rota de URL, o nome não pode conter carateres que não sejam válidos num URL.

    > [!TIP]
    > A clonagem numa nova versão é uma boa prática antes de modificar a sua aplicação. Quando terminar com uma alteração para uma versão, exporte a versão (como ficheiro .json ou .lu) e verifique o ficheiro no seu sistema de controlo de origem.

1. Selecione **Build** então **Intenções** para ver as intenções, os principais blocos de construção de uma aplicação LUIS.

    ![Altere da página Versões para a página Intenções.](../media/tutorial-machine-learned-entity/new-version-imported-app.png)

---
title: Passos de aplicação de importação
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/22/2020
ms.author: diberry
ms.openlocfilehash: 2cf588ed120b353958cc708189c86481cd247d8e
ms.sourcegitcommit: 74ba70139781ed854d3ad898a9c65ef70c0ba99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85445933"
---
1. No [portal LUIS,](https://www.luis.ai)na página **My apps,** selecione **+ Nova aplicação para conversação,** em seguida **Import as JSON**. Encontre o ficheiro JSON guardado do passo anterior. Não precisa de mudar o nome da aplicação. Selecione **Feito**

1. A partir da secção **Gerir,** no separador **Versões,** selecione a `sentiment` versão e, em seguida, selecione **Clone** para clonar a versão e dê-lhe um novo nome de `ml-entity` , em seguida, selecione **Feito** para terminar o processo de clone. Como o nome da versão é utilizado como parte da rota de URL, o nome não pode conter carateres que não sejam válidos num URL.

    > [!TIP]
    > A clonagem numa nova versão é uma boa prática antes de modificar a sua aplicação. Quando terminar com uma alteração para uma versão, exporte a versão (como ficheiro .json ou .lu) e verifique o ficheiro no seu sistema de controlo de origem.

1. Selecione **Build** então **Intenções** para ver as intenções, os principais blocos de construção de uma aplicação LUIS.

    ![Altere da página Versões para a página Intenções.](../media/tutorial-machine-learned-entity/new-version-imported-app.png)
---
title: Passos de aplicação de importação
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 10/16/2020
ms.openlocfilehash: b70a22e502de5441b5c91a8f33357375834f54e1
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130759"
---
1. No [portal LUIS,](https://www.luis.ai)na página **My apps,** selecione **+ Nova aplicação para conversação,** em seguida **Import as JSON** . Encontre o ficheiro JSON guardado do passo anterior. Não precisa de mudar o nome da aplicação. Selecione **Feito**

1. A partir da secção **Gerir,** no separador **Versões,** selecione a `0.1` versão e, em seguida, selecione **Clone** para clonar a versão e dê-lhe um novo nome de `ml-entity` , em seguida, selecione **Feito** para terminar o processo de clone. Como o nome da versão é utilizado como parte da rota de URL, o nome não pode conter carateres que não sejam válidos num URL.

    > [!TIP]
    > A clonagem numa nova versão é uma boa prática antes de modificar a sua aplicação. Quando terminar com uma alteração para uma versão, exporte a versão (como ficheiro .json ou .lu) e verifique o ficheiro no seu sistema de controlo de origem.

1. Selecione **Construir** na parte superior do ecrã e, em seguida, clique em **Intenções** no menu de navegação à esquerda. Você verá as intenções da app, que são os principais blocos de construção de uma app LUIS.

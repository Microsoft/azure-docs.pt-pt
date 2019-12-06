---
title: Importar etapas do aplicativo
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 12/03/2019
ms.author: diberry
ms.openlocfilehash: b0d1735df7a3ac36e20af771939aec6b8013f2df
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74840821"
---
1. No [portal do Luis de visualização](https://preview.luis.ai), na página **meus aplicativos** , selecione **importar**e **importar como JSON**. Localize o arquivo JSON salvo da etapa anterior. Você não precisa alterar o nome do aplicativo. Selecionar **concluído**

1. Na seção **gerenciar** , na guia **versões** , selecione a versão, selecione **clonar** para clonar a versão e dê a ela um novo nome de 10 caracteres e, em seguida, selecione **concluído** para concluir o processo de clonagem. Como o nome da versão é utilizado como parte da rota de URL, o nome não pode conter carateres que não sejam válidos num URL.

    > [!TIP]
    > A clonagem em uma nova versão é uma prática recomendada antes de modificar seu aplicativo. Quando você concluir uma versão, exporte a versão (como um arquivo. JSON ou. Lu) e verifique o arquivo em seu sistema de controle do código-fonte.

1. Selecione **Compilar** e, em seguida, para ver **as intenções,** os principais blocos de construção de um aplicativo Luis.

    ![Altere da página versões para a página de tentativas.](../media/tutorial-machine-learned-entity/new-version-imported-app.png)
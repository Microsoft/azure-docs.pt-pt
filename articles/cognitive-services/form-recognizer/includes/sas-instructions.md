---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 12/11/2020
ms.author: pafarley
ms.openlocfilehash: 3f4272f5445c8530e97726bd4fdca6bf662719c2
ms.sourcegitcommit: 02ed9acd4390b86c8432cad29075e2204f6b1bc3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/29/2020
ms.locfileid: "97807812"
---
Para recuperar o URL SAS para os seus dados de treino de modelo personalizados, vá ao seu recurso de armazenamento no portal Azure e selecione o **separador Explorer de Armazenamento.** Navegue para o seu recipiente, clique com o botão direito e selecione **Obtenha a assinatura de acesso partilhado**. É importante obter o SAS para o seu contentor, não para a conta de armazenamento em si. Certifique-se de que as permissões **de Leitura** e **Lista** são verificadas e clique em **Criar**. Em seguida, copie o valor na secção **URL** para uma localização temporária. Deve ter o formato: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.
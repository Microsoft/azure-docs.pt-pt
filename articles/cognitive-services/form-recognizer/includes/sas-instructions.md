---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 12/11/2020
ms.author: pafarley
ms.openlocfilehash: 45ac0e74733ade9801a6f3624d6200c594eff698
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102623397"
---
Para recuperar o URL SAS para os seus dados de treino de modelo personalizados, vá ao seu recurso de armazenamento no portal Azure e selecione o **separador Explorer de Armazenamento.** Navegue para o seu recipiente, clique com o botão direito e selecione **Obtenha a assinatura de acesso partilhado**. É importante obter o SAS para o seu contentor, não para a conta de armazenamento em si. Certifique-se de que as permissões **de Ler,** **Escrever,** **Eliminar** e **Lista** são verificadas e clique em **Criar**. Em seguida, copie o valor na secção **URL** para uma localização temporária. Deve ter o formato: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.

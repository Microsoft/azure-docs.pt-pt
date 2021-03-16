---
author: laujan
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 12/11/2020
ms.author: lajanuar
ms.openlocfilehash: 8bf78d4c2f703ee10b26b1936620f7cf23ed7c14
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/15/2021
ms.locfileid: "103467209"
---
Para recuperar o URL SAS para os seus dados de treino de modelo personalizados, vá ao seu recurso de armazenamento no portal Azure e selecione o **separador Explorer de Armazenamento.** Navegue para o seu recipiente, clique com o botão direito e selecione **Obtenha a assinatura de acesso partilhado**. É importante obter o SAS para o seu contentor, não para a conta de armazenamento em si. Certifique-se de que as permissões **de Ler,** **Escrever,** **Eliminar** e **Lista** são verificadas e clique em **Criar**. Em seguida, copie o valor na secção **URL** para uma localização temporária. Deve ter o formato: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.

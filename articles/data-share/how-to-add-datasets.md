---
title: Adicione conjuntos de dados a uma partilha de dados azure existente
description: Aprenda a adicionar conjuntos de dados a uma quota de dados existente na Partilha de Dados do Azure e partilhe com os mesmos destinatários.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 00c96950565b077e65f84e2d8b4977092df5e317
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73490546"
---
# <a name="how-to-add-datasets-to-an-existing-share-in-azure-data-share"></a>Como adicionar conjuntos de dados a uma participação existente na Partilha de Dados do Azure

Este artigo explica como adicionar conjuntos de dados a uma partilha de dados pré-existente usando a Partilha de Dados do Azure. Isto permite-lhe partilhar mais dados com os mesmos destinatários sem ter de criar uma nova quota.

Para obter informações sobre como adicionar conjuntos de dados à medida que cria uma partilha, consulte o tutorial de [dados da Partilha.](share-your-data.md)

## <a name="navigate-to-a-sent-data-share"></a>Navegue para uma partilha de dados enviada

No Azure Data Share, navegue para a sua parte enviada e selecione o separador **Datasets.** Clique + Adicione o botão **Datasets** para adicionar mais conjuntos de dados.

![Adicionar Conjuntos de Dados](./media/how-to/how-to-add-datasets/add-datasets.png)

No painel à direita, selecione o tipo de conjunto de dados que gostaria de adicionar e, em seguida, clique **em Seguinte**. Selecione o grupo de subscrição e recursos dos dados que gostaria de adicionar. Utilizando as setas de dropdown, encontre e, em seguida, verifique a caixa ao lado dos dados a adicionar.

![Adicionar Conjuntos de Dados](./media/how-to/how-to-add-datasets/add-datasets-side.png)

Assim que clicar em **Adicionar Conjuntos**de Dados, os conjuntos de dados serão adicionados à sua parte. Nota: Os seus consumidores devem ser acionados um instantâneo para que possam ver os novos conjuntos de dados. Se houver configurações instantâneas configuradas, os consumidores verão os novos conjuntos de dados assim que o próximo instantâneo agendado estiver concluído. Sem configurações instantâneas configuradas, o consumidor deve acionar manualmente uma cópia completa ou incremental dos dados para receber as atualizações. Para obter mais informações sobre instantâneos, consulte [Snapshots](terminology.md).

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como [adicionar destinatários a uma partilha](how-to-add-recipients.md)de dados existente.
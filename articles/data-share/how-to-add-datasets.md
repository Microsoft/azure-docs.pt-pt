---
title: Adicione conjuntos de dados a uma partilha de dados Azure existente
description: Saiba como adicionar conjuntos de dados a uma partilha de dados existente na Azure Data Share e partilhe com os mesmos destinatários.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: how-to
ms.date: 07/10/2019
ms.openlocfilehash: c4aee1eb268c124586388eacf8c908c20d799bc9
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86110900"
---
# <a name="how-to-add-datasets-to-an-existing-share-in-azure-data-share"></a>Como adicionar conjuntos de dados a uma participação existente na Azure Data Share

Este artigo explica como adicionar conjuntos de dados a uma partilha de dados pré-existente usando a Azure Data Share. Isto permite-lhe partilhar mais dados com os mesmos destinatários sem ter de criar uma nova quota.

Para obter informações sobre como adicionar conjuntos de dados à medida que cria uma partilha, consulte o tutorial [de dados partilhar.](share-your-data.md)

## <a name="navigate-to-a-sent-data-share"></a>Navegue para uma partilha de dados enviada

No Azure Data Share, navegue para a sua partilha enviada e selecione o **separador Datasets.** Clique no botão **+ Adicionar Conjuntos de Dados** para adicionar mais conjuntos de dados.

![Adicionar conjuntos de dados](./media/how-to/how-to-add-datasets/add-datasets.png)

No painel à direita, selecione o tipo de conjunto de dados que pretende adicionar e, em seguida, clique em **Seguinte**. Selecione o grupo de subscrição e recursos dos dados que pretende adicionar. Utilizando as setas de dropdown, encontre e verifique a caixa ao lado dos dados a adicionar.

![Adicionar conjuntos de dados](./media/how-to/how-to-add-datasets/add-datasets-side.png)

Assim que clicar em **Adicionar Conjuntos de Dados,** os conjuntos de dados serão adicionados à sua parte. Nota: Um instantâneo deve ser desencadeado pelos seus consumidores para que estes possam ver os novos conjuntos de dados. Se houver configurações instantâneas configuradas, os consumidores verão os novos conjuntos de dados assim que o próximo instantâneo programado estiver concluído. Sem configurar as definições de instantâneo, o consumidor deve ativar manualmente uma cópia completa ou incremental dos dados para receber as atualizações. Para obter mais informações sobre instantâneos, consulte [Snapshots](terminology.md).

## <a name="next-steps"></a>Próximos passos
Saiba mais sobre como [adicionar destinatários a uma partilha de dados existente.](how-to-add-recipients.md)
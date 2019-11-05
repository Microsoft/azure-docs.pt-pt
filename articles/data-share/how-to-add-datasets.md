---
title: Adicionar conjuntos de dados a um compartilhamento existente do Azure data
description: Saiba como adicionar conjuntos de dados a um compartilhamento de dado existente no compartilhamento de dados do Azure e compartilhar com os mesmos destinatários.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 00c96950565b077e65f84e2d8b4977092df5e317
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490546"
---
# <a name="how-to-add-datasets-to-an-existing-share-in-azure-data-share"></a>Como adicionar conjuntos de dados a um compartilhamento existente no Azure data share

Este artigo explica como adicionar conjuntos de dados a um compartilhamento de dado pré-existente usando o compartilhamento de dados do Azure. Isso permite que você compartilhe mais dados com os mesmos destinatários sem precisar criar um novo compartilhamento.

Para obter informações sobre como adicionar conjuntos de dados à medida que você cria um compartilhamento, consulte o tutorial [compartilhar data](share-your-data.md) .

## <a name="navigate-to-a-sent-data-share"></a>Navegar até um compartilhamento de dados enviado

No compartilhamento de dados do Azure, navegue até o compartilhamento enviado e selecione a guia **conjuntos** de dados. Clique no botão **+ Adicionar conjuntos** de dados para adicionar mais conjuntos de dados.

![Adicionar conjuntos de os](./media/how-to/how-to-add-datasets/add-datasets.png)

No painel à direita, selecione o tipo de conjunto de texto que você deseja adicionar e clique em **Avançar**. Selecione a assinatura e o grupo de recursos dos dados que você deseja adicionar. Usando as setas suspensas, localize e marque a caixa ao lado dos dados a serem adicionados.

![Adicionar conjuntos de os](./media/how-to/how-to-add-datasets/add-datasets-side.png)

Depois de clicar em **Adicionar conjuntos**de os, os conjuntos de valores serão adicionados ao seu compartilhamento. Observação: um instantâneo deve ser disparado por seus consumidores para que eles vejam os novos conjuntos de clientes. Se houver configurações de instantâneo configuradas, os consumidores verão os novos conjuntos de clientes assim que o próximo instantâneo agendado for concluído. Sem configurações de instantâneo configuradas, o consumidor deve disparar manualmente uma cópia completa ou incremental dos dados para receber as atualizações. Para obter mais informações sobre instantâneos, consulte [instantâneos](terminology.md).

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como [Adicionar destinatários a um compartilhamento de dados existente](how-to-add-recipients.md).
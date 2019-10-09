---
title: Adicionar conjuntos de dados a uma versão prévia existente do Azure data share
description: Saiba como adicionar conjuntos de dados a um compartilhamento de dado existente no compartilhamento de dados do Azure e compartilhar com os mesmos destinatários.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 8cc61307ae6664889f60469cc3ca65c840c43b86
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72169197"
---
# <a name="how-to-add-datasets-to-an-existing-share-in-azure-data-share-preview"></a>Como adicionar conjuntos de dados a um compartilhamento existente na versão prévia do compartilhamento do Azure data share

Este artigo explica como adicionar conjuntos de dados a um compartilhamento de dado pré-existente usando a visualização do compartilhamento de dados do Azure. Isso permite que você compartilhe mais dados com os mesmos destinatários sem precisar criar um novo compartilhamento.

Para obter informações sobre como adicionar conjuntos de dados à medida que você cria um compartilhamento, consulte o tutorial [compartilhar data](share-your-data.md) .

## <a name="navigate-to-a-sent-data-share"></a>Navegar até um compartilhamento de dados enviado

Na visualização do compartilhamento de dados do Azure, navegue até o compartilhamento enviado e selecione a guia **conjuntos** de dados. Clique no botão **+ Adicionar conjuntos** de itens para adicionar mais conjuntos de informações.

![Adicionar conjuntos de os](./media/how-to/how-to-add-datasets/add-datasets.png)

No painel à direita, selecione o tipo de conjunto de texto que você deseja adicionar e clique em **Avançar**. Selecione a assinatura e o grupo de recursos dos dados que você deseja adicionar. Usando as setas suspensas, localize e marque a caixa ao lado dos dados a serem adicionados.

![Adicionar conjuntos de os](./media/how-to/how-to-add-datasets/add-datasets-side.png)

Depois de clicar em **Adicionar conjuntos**de os, os conjuntos de valores serão adicionados ao seu compartilhamento. Nota: Um instantâneo deve ser disparado por seus consumidores para que eles vejam os novos conjuntos de clientes. Se houver configurações de instantâneo configuradas, os consumidores verão os novos conjuntos de clientes assim que o próximo instantâneo agendado for concluído. Sem configurações de instantâneo configuradas, o consumidor deve disparar manualmente uma cópia completa ou incremental dos dados para receber as atualizações. Para obter mais informações sobre instantâneos, consulte [instantâneos](terminology.md).

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como [Adicionar destinatários a um compartilhamento de dados existente](how-to-add-recipients.md).
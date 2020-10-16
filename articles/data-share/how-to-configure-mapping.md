---
title: Configure um mapeamento de conjunto de dados em Azure Data Share
description: Saiba como configurar um mapeamento de conjunto de dados para uma partilha recebida usando a Azure Data Share.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 08/14/2020
ms.openlocfilehash: eed3e8275400a3e677df53b9d62cf0e0bc70271c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88257839"
---
# <a name="how-to-configure-a-dataset-mapping-for-a-received-share-in-azure-data-share"></a>Como configurar um mapeamento de conjunto de dados para uma participação recebida no Azure Data Share

Este artigo explica como configurar um mapeamento de conjunto de dados para uma Partilha Recebida usando a Azure Data Share. Pode querer configurar um mapeamento de conjunto de dados para especificar uma loja de dados-alvo onde pretende receber dados, ou se precisa de alterar a loja de dados-alvo.

## <a name="navigate-to-a-received-data-share"></a>Navegue para uma partilha de dados recebida

No serviço Azure Data Share, navegue para a sua partilha recebida e selecione o **separador Datasets.** 

![Mapeamento do conjunto de dados](./media/dataset-mapping.png "Mapeamento do conjunto de dados") 

Verifique a caixa ao lado do conjunto de dados para o qual pretende atribuir um destino. Selecione **+ Mapa para o destino** para escolher uma nova loja de destino. Selecione **Unmap** primeiro se o conjunto de dados já estiver mapeado e pretende alterar a loja de dados-alvo.

![Mapa para alvo](./media/dataset-map-target.png "Mapa para alvo") 

## <a name="select-a-target-store"></a>Selecione uma loja-alvo

Selecione um tipo de loja de dados alvo que gostaria que os dados aterrassem. Para a partilha baseada em instantâneos, quaisquer dados que já existam em quaisquer contas de armazenamento previamente mapeadas não serão automaticamente transferidos para a nova loja-alvo. Para a partilha no local, selecione uma loja de dados na Localização especificada. A Localização é o centro de dados Azure onde a loja de dados do fornecedor de dados está localizada.

![Conta de armazenamento alvo](./media/dataset-map-target-sql.png "Armazenamento de destino") 

## <a name="select-a-file-format-sql-sources-only"></a>Selecione um formato de ficheiro (apenas fontes SQL)

Se os dados de origem forem de uma fonte baseada em SQL e quiser recebê-lo como ficheiro, pode escolher em que formato é recebido. 

![Escolha o formato](./media/sql-file-formats.png "Formatos de ficheiros SQL")

## <a name="next-steps"></a>Passos seguintes

Para aprender a partilhar dados, continue a partilhar o seu tutorial [de dados.](share-your-data.md)




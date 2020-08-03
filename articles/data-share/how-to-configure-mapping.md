---
title: Configure um mapeamento de conjunto de dados em Azure Data Share
description: Saiba como configurar um mapeamento de conjunto de dados para uma partilha recebida usando a Azure Data Share.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 07/30/2020
ms.openlocfilehash: 82de05e7169c5803dd999521f61a33b9dd17b567
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/02/2020
ms.locfileid: "87511960"
---
# <a name="how-to-configure-a-dataset-mapping-for-a-received-share-in-azure-data-share"></a>Como configurar um mapeamento de conjunto de dados para uma participação recebida no Azure Data Share

Este artigo explica como configurar um mapeamento de conjunto de dados para uma Partilha Recebida usando a Azure Data Share. Você vai querer fazê-lo se você aceitou um convite de partilha de dados, mas optou por "Aceitar e configurar mais tarde", ou se os dados são partilhados no local. Pode querer configurar um mapeamento de conjunto de dados se precisar de alterar o destino para que os dados sejam partilhados consigo ou se pretender receber dados num SqL Server. 

## <a name="navigate-to-a-received-data-share"></a>Navegue para uma partilha de dados recebida

No serviço Azure Data Share, navegue para a sua partilha recebida e selecione o separador **Detalhes.** 

![Mapeamento do conjunto de dados](./media/dataset-mapping.png "Mapeamento do conjunto de dados") 

Verifique a caixa ao lado do conjunto de dados para o qual pretende atribuir um destino. Selecione **Unmap** para desmaplê-lo ao mapeamento existente. Selecione **+ Mapa para o destino** para escolher uma nova loja de destino. 

![Mapa para alvo](./media/dataset-map-target.png "Mapa para alvo") 

## <a name="select-a-new-target-store"></a>Selecione uma nova loja-alvo

Selecione um tipo de dados-alvo que gostaria que os dados aterrassem. Para a partilha baseada em instantâneos, quaisquer dados que já existam em quaisquer contas de armazenamento previamente mapeadas não serão automaticamente transferidos para a nova loja-alvo. Para a partilha no local, selecione uma loja de dados na Localização especificada. A Localização é o centro de dados Azure onde a loja de dados do fornecedor de dados está localizada.

![Conta de armazenamento alvo](./media/dataset-map-target-sql.png "Armazenamento de destino") 

## <a name="select-a-file-format-sql-sources-only"></a>Selecione um formato de ficheiro (apenas fontes SQL)

Se os dados de origem forem de uma fonte baseada em SQL, pode escolher em que formato é recebido. 

![Escolha o formato](./media/sql-file-formats.png "Formatos de ficheiros SQL")

## <a name="next-steps"></a>Passos seguintes

Para aprender a partilhar dados, continue a partilhar o seu tutorial [de dados.](share-your-data.md)




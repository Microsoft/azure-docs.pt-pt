---
title: Configurar um mapeamento de conjunto de dados no Azure data share
description: Saiba como configurar um mapeamento de DataSet para um compartilhamento recebido usando o compartilhamento de dados do Azure.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 754977788c5f6e5e574500552f670ba9083cf683
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490618"
---
# <a name="how-to-configure-a-dataset-mapping-for-a-received-share-in-azure-data-share"></a>Como configurar um mapeamento de DataSet para um compartilhamento recebido no compartilhamento de dados do Azure

Este artigo explica como configurar um mapeamento de DataSet para um compartilhamento recebido usando o compartilhamento de dados do Azure. Você desejará fazer isso se tiver aceitado um convite de compartilhamento de dados, mas tiver optado por "aceitar e configurar mais tarde". Talvez você queira configurar um mapeamento de conjunto de dados se precisar alterar o destino para que sejam compartilhados com você ou se desejar receber dados em um SQL Server. 

## <a name="navigate-to-a-received-data-share"></a>Navegar até um compartilhamento de dados recebido

No serviço de compartilhamento de dados do Azure, navegue até o compartilhamento recebido e selecione a guia **detalhes** . 

![Mapeamento de DataSet](./media/dataset-mapping.png "Mapeamento de DataSet") 

Marque a caixa ao lado do conjunto de um para o qual você gostaria de atribuir um destino. Selecione **desmapear** para desmapear o mapeamento existente. Selecione **+ mapear para destino** para escolher um novo repositório de destino. 

![Mapear para o destino](./media/dataset-map-target.png "Mapear para o destino") 

## <a name="select-a-new-destination-store"></a>Selecione um novo repositório de destino

Selecione um tipo de dados de destino no qual você deseja que os dados entrem. Observe que todos os dados que já existem em todas as contas de armazenamento mapeadas anteriormente não serão automaticamente movidos para o novo destino.

![Conta de armazenamento de destino](./media/dataset-map-target-sql.png "Armazenamento de destino") 

## <a name="select-a-file-format-sql-sources-only"></a>Selecionar um formato de arquivo (somente fontes SQL)

Se os dados de origem forem de uma fonte baseada em SQL, você poderá escolher em qual formato ele será recebido. 

![Escolher formato](./media/sql-file-formats.png "Formatos de arquivo SQL")

## <a name="next-steps"></a>Passos seguintes

Para saber como começar a compartilhar dados, continue no tutorial [compartilhar seus dados](share-your-data.md) .




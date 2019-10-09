---
title: Configurar um mapeamento de conjunto de dados na visualização do compartilhamento de data do Azure
description: Saiba como configurar um mapeamento de conjunto de dados para um compartilhamento recebido usando o Azure data share Preview.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 035235116240bdc6de3bc689c2430fee018b202d
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72169139"
---
# <a name="how-to-configure-a-dataset-mapping-for-a-received-share-in-azure-data-share-preview"></a>Como configurar um mapeamento de conjunto de dados para um compartilhamento recebido na visualização do compartilhamento de data do Azure

Este artigo explica como configurar um mapeamento de conjunto de dados para um compartilhamento recebido usando o Azure data share Preview. Você desejará fazer isso se tiver aceitado um convite de compartilhamento de dados, mas tiver optado por "aceitar e configurar mais tarde". Caso contrário, talvez você queira simplesmente alterar a conta de armazenamento de destino para os dados recebidos. 

## <a name="navigate-to-a-received-data-share"></a>Navegar até um compartilhamento de dados recebido

No serviço de compartilhamento de dados do Azure, navegue até o compartilhamento recebido e selecione a guia **detalhes** . 

![](./media/dataset-mapping.png "Mapeamento do conjunto") de conjunto de DataSet 

Marque a caixa ao lado do conjunto de um para o qual você deseja atribuir um destino e clique em **+ mapear para o destino**. Talvez seja necessário desmapear primeiro se você já tiver configurado uma conta de armazenamento de destino e desejar alterar o mapeamento para uma conta de armazenamento diferente. 

![Mapear para]o mapa de destino(./media/dataset-map-target.png "para o destino") 

## <a name="select-a-new-storage-account"></a>Selecione uma nova conta de armazenamento 

Selecione uma conta de armazenamento na qual você deseja que os dados entrem. Observe que todos os dados que já existem em todas as contas de armazenamento mapeadas anteriormente não serão automaticamente movidos para a nova conta de armazenamento.

![](./media/map-target.png "Armazenamento de destino") da conta de armazenamento de destino 

## <a name="next-steps"></a>Passos seguintes

Para saber como começar a compartilhar dados, continue no tutorial [compartilhar seus dados](share-your-data.md) .




---
title: Configurar um mapeamento de conjunto de dados
description: Configurar um mapeamento de conjunto de dados
author: joannapea
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: 581e1eef5f1d64e68a6501f56ce60218281c605d
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789244"
---
# <a name="how-to-configure-a-dataset-mapping-for-a-received-share-in-azure-data-share-preview"></a>Como configurar um mapeamento de conjunto de dados para uma partilha de recebido na pré-visualização de partilha de dados do Azure

Este artigo explica como configurar um mapeamento de conjunto de dados para uma partilha recebido com a pré-visualização de partilha de dados do Azure. Vai querer fazer isso, se aceitar um convite de partilha de dados, mas optou por "Accept e configurar mais tarde". Caso contrário, pode simplesmente querer alterar a conta de armazenamento de destino para os seus dados recebidos. 

## <a name="navigate-to-a-received-data-share"></a>Navegue para uma partilha de dados recebidos

No serviço de partilha de dados do Azure, navegue para a partilha de recebido e selecione o **detalhes** separador. 

![Mapeamento do conjunto de dados](./media/dataset-mapping.png "mapeamento do conjunto de dados") 

Marque a caixa junto ao conjunto de dados que pretende atribuir um destino para e clique em **+ o mapa para destino**. Terá de anular o mapeamento primeiro se já tiver configurado uma conta de armazenamento de destino e se pretender alterar o mapeamento para uma conta de armazenamento diferente. 

![Mapa de destino](./media/dataset-map-target.png "mapa para o destino") 

## <a name="select-a-new-storage-account"></a>Selecione uma nova conta de armazenamento 

Selecione uma conta de armazenamento que gostaria de ter os dados para apresentar o. Tenha em atenção que todos os dados que já existe em qualquer já mapeou a contas de armazenamento não será movida automaticamente para a nova conta de armazenamento.

![Conta de armazenamento de destino](./media/map-target.png "armazenamento de destino") 

## <a name="next-steps"></a>Passos Seguintes

Para saber como começar a partilha de dados, avance para o [partilhar os seus dados](share-your-data.md) tutorial.




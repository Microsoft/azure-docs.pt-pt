---
title: Configure um mapeamento de conjunto de dados na Partilha de Dados do Azure
description: Saiba como configurar um mapeamento de conjunto de dados para uma parte recebida usando a Partilha de Dados do Azure.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 1ff432c2073536448c8194ebe537c8bf8cf00663
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76964248"
---
# <a name="how-to-configure-a-dataset-mapping-for-a-received-share-in-azure-data-share"></a>Como configurar um mapeamento de conjunto de dados para uma participação recebida na Partilha de Dados do Azure

Este artigo explica como configurar um mapeamento de dataset para uma Partilha de Dados Recebida sem acordo. Você vai querer fazê-lo se você aceitou um convite de partilha de dados mas optou por "Aceitar e configurar mais tarde", ou se os dados são partilhados no local. Pode querer configurar um mapeamento de dataset se precisar de alterar o destino para os dados que estão a ser partilhados consigo, ou se quiser receber dados num Servidor SQL. 

## <a name="navigate-to-a-received-data-share"></a>Navegar para uma partilha de dados recebida

No serviço Azure Data Share, navegue para a sua parte recebida e selecione o separador **Detalhes.** 

![Mapeamento de conjunto de dados](./media/dataset-mapping.png "Mapeamento de conjunto de dados") 

Verifique a caixa ao lado do conjunto de dados a que gostaria de atribuir um destino. Selecione **Unmap** para desmapear o mapeamento existente. Selecione **+ Map para escolher** uma nova loja de destino. 

![Mapa para o alvo](./media/dataset-map-target.png "Mapa para o alvo") 

## <a name="select-a-new-target-store"></a>Selecione uma nova loja-alvo

Selecione um tipo de dados-alvo que gostaria que os dados aterrassem. Para a partilha baseada em instantâneos, quaisquer dados que já existam em quaisquer contas de armazenamento previamente mapeadas não serão automaticamente transferidos para a nova loja-alvo. Para partilha no local, selecione uma loja de dados no Local especificado. O Local é o centro de dados Azure onde está localizada a loja de dados de fonte do fornecedor de dados.

![Conta de armazenamento alvo](./media/dataset-map-target-sql.png "Armazenamento de destino") 

## <a name="select-a-file-format-sql-sources-only"></a>Selecione um formato de ficheiro (apenas fontes SQL)

Se os dados de origem forem de uma fonte baseada em SQL, pode escolher em que formato é recebido. 

![Escolha o formato](./media/sql-file-formats.png "Formatos de ficheiroS SQL")

## <a name="next-steps"></a>Passos seguintes

Para aprender a começar a partilhar dados, continue a partilhar o seu tutorial de [dados.](share-your-data.md)




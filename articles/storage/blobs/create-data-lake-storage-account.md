---
title: Criar uma conta de armazenamento para Azure Data Lake Storage Gen2
description: Saiba como criar uma conta de armazenamento para uso com a Azure Data Lake Storage Gen2.
author: normesta
ms.topic: how-to
ms.author: normesta
ms.date: 08/31/2020
ms.service: storage
ms.reviewer: stewu
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 712f1dc0679ee49791831e782fb68c39a757870a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98624342"
---
# <a name="create-a-storage-account-to-use-with-azure-data-lake-storage-gen2"></a>Criar uma conta de armazenamento para usar com Azure Data Lake Storage Gen2

Para utilizar as capacidades da Data Lake Storage Gen2, crie uma conta de armazenamento que tenha um espaço hierárquico de nomes.

## <a name="choose-a-storage-account-type"></a>Escolha um tipo de conta de armazenamento

As capacidades de armazenamento do Data Lake são suportadas nos seguintes tipos de contas de armazenamento:

- Fins gerais v2
- BlockBlobStorage

Para obter informações sobre como escolher entre eles, consulte [a visão geral da conta de armazenamento.](../common/storage-account-overview.md)

## <a name="create-a-storage-account-with-a-hierarchical-namespace"></a>Criar uma conta de armazenamento com um espaço hierárquico de nomes

Crie uma [conta V2 de uso geral](../common/storage-account-create.md) ou uma conta [BlockBlobStorage](storage-blob-create-account-block-blob.md) com a definição **de espaço de nome hierárquico** ativada.

Desbloqueie as capacidades de Armazenamento do Lago de Dados quando criar a conta, permitindo a definição **de espaço de nome hierárquico** no separador **Avançado** da página de conta de **armazenamento Criar.** Tem de ativar esta definição quando criar a conta. Não pode permitir depois.

A imagem a seguir mostra esta definição na página de **conta de armazenamento Criar.**

> [!div class="mx-imgBorder"]
> ![Definição hierárquica do espaço de nome](./media/create-data-lake-storage-account/hierarchical-namespace-feature.png)

Se tiver uma conta de armazenamento existente que pretende utilizar com o Armazenamento do Lago de Dados e a definição hierárquica do espaço de nome estiver desativada, tem de migrar os dados para uma nova conta de armazenamento que tenha a definição ativada.

> [!NOTE]
> **A proteção de dados** e **o espaço hierárquico** não podem ser ativados simultaneamente.

## <a name="next-steps"></a>Passos seguintes

- [Descrição geral da conta de armazenamento](../common/storage-account-overview.md)
- [Usando Azure Data Lake Storage Gen2 para grandes requisitos de dados](data-lake-storage-data-scenarios.md)
- [Access control in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md) (Controlo de acesso no Azure Data Lake Storage Gen2)

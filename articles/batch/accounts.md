---
title: Contas de lote e contas de armazenamento Azure
description: Saiba mais sobre as contas do Azure Batch e como são usadas do ponto de vista do desenvolvimento.
ms.topic: conceptual
ms.date: 05/12/2020
ms.openlocfilehash: 20a2a28d0eaa2c7997ea93e66d07ecb99bf297a2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "83791149"
---
# <a name="batch-accounts-and-azure-storage-accounts"></a>Contas de lote e contas de armazenamento Azure

Uma conta Azure Batch é uma entidade identificada exclusivamente dentro do serviço Batch. A maioria das soluções batch usam [O Armazenamento Azure](../storage/index.yml) para armazenar ficheiros de recursos e ficheiros de saída, pelo que cada conta Batch está geralmente associada a uma conta de armazenamento correspondente.

## <a name="batch-accounts"></a>Contas do Batch

Todo o processamento e recursos estão associados a uma conta Batch. Quando a aplicação faz um pedido com o serviço do Batch, autentica o pedido com o nome de conta do Azure Batch, o URL da conta e uma tecla de acesso ou um token do Azure Active Directory.

Pode executar várias cargas de trabalho num único Lote. Também pode distribuir as suas cargas de trabalho entre as contas batch que estão na mesma subscrição, mas localizadas em diferentes regiões do Azure.

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]

Pode criar uma conta Batch utilizando o [portal Azure](batch-account-create-portal.md) ou programáticamente, como na [biblioteca Batch Management .NET](batch-management-dotnet.md). Ao criar a conta, pode associar uma conta de armazenamento do Azure para armazenar aplicações ou dados de entrada e saída relacionados com tarefas.

## <a name="azure-storage-accounts"></a>Contas de Armazenamento do Azure

A maioria das soluções do Batch utilizam o Armazenamento do Azure para armazenar ficheiros de recursos e ficheiros de saída. Por exemplo, as suas tarefas do Batch (incluindo tarefas standard, tarefas de início, tarefas de preparação de trabalhos e tarefas de lançamento de trabalhos), normalmente, especificam os ficheiros de recursos que residem numa contas de armazenamento. As contas de armazenamento também armazenam esses dados que são processados e quaisquer dados de saída que são gerados.

O Batch suporta os seguintes tipos de contas de Armazenamento do Azure:

- Contas para Fins gerais v2 (GPv2)
- Contas para Fins gerais v1 (GPv1)
- Contas de armazenamento de blobs (atualmente suportadas para conjuntos na configuração da Máquina Virtual)

Para obter mais informações sobre contas de armazenamento, consulte [a visão geral da conta de armazenamento Azure](../storage/common/storage-account-overview.md).

Pode associar uma conta de armazenamento à sua conta do Batch ao criar a conta do Batch, ou posteriormente. Considere os requisitos de desempenho e custo ao escolher uma conta de armazenamento. Por exemplo, as opções de conta de armazenamento GPv2 e BLOBs suportam [limites de escalabilidade e capacidade](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/) mais elevados em comparação com a GPv1. (Contacte o Suporte Azure para solicitar um aumento do limite de armazenamento.) Estas opções de conta podem melhorar o desempenho de soluções Batch que contêm um grande número de tarefas paralelas que lêem ou escrevem na conta de armazenamento.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [nós e piscinas.](nodes-and-pools.md)
- Saiba como criar uma conta Batch utilizando o [portal Azure](batch-account-create-portal.md).

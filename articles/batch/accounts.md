---
title: Contas de lote e contas de armazenamento azure
description: Saiba mais sobre as contas do Lote Azure e como são usadas do ponto de vista do desenvolvimento.
ms.topic: conceptual
ms.date: 05/12/2020
ms.openlocfilehash: 20a2a28d0eaa2c7997ea93e66d07ecb99bf297a2
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83791149"
---
# <a name="batch-accounts-and-azure-storage-accounts"></a>Contas de lote e contas de armazenamento azure

Uma conta Azure Batch é uma entidade exclusivamente identificada dentro do serviço Batch. A maioria das soluções de Lote utiliza [o Armazenamento Azure](../storage/index.yml) para armazenar ficheiros de recursos e ficheiros de saída, pelo que cada conta de Lote está geralmente associada a uma conta de armazenamento correspondente.

## <a name="batch-accounts"></a>Contas de Batch

Todos os processamentos e recursos estão associados a uma conta Batch. Quando a aplicação faz um pedido com o serviço do Batch, autentica o pedido com o nome de conta do Azure Batch, o URL da conta e uma tecla de acesso ou um token do Azure Active Directory.

Pode executar várias cargas de trabalho do Lote numa única conta de Lote. Também pode distribuir as suas cargas de trabalho entre as contas do Lote que estão na mesma subscrição, mas localizadas em diferentes regiões do Azure.

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]

Pode criar uma conta Batch utilizando o [portal Azure](batch-account-create-portal.md) ou programáticamente, como na [biblioteca Batch Management .NET](batch-management-dotnet.md). Ao criar a conta, pode associar uma conta de armazenamento do Azure para armazenar aplicações ou dados de entrada e saída relacionados com tarefas.

## <a name="azure-storage-accounts"></a>Contas de Armazenamento do Azure

A maioria das soluções do Batch utilizam o Armazenamento do Azure para armazenar ficheiros de recursos e ficheiros de saída. Por exemplo, as suas tarefas do Batch (incluindo tarefas standard, tarefas de início, tarefas de preparação de trabalhos e tarefas de lançamento de trabalhos), normalmente, especificam os ficheiros de recursos que residem numa contas de armazenamento. As contas de armazenamento também armazenam esses dados que são processados e quaisquer dados de saída que sejam gerados.

O Batch suporta os seguintes tipos de contas de Armazenamento do Azure:

- Contas para Fins gerais v2 (GPv2)
- Contas para Fins gerais v1 (GPv1)
- Contas de armazenamento de blobs (atualmente suportadas para conjuntos na configuração da Máquina Virtual)

Para obter mais informações sobre contas de armazenamento, consulte a visão geral da conta de [armazenamento do Azure.](../storage/common/storage-account-overview.md)

Pode associar uma conta de armazenamento à sua conta do Batch ao criar a conta do Batch, ou posteriormente. Considere os requisitos de desempenho e custo ao escolher uma conta de armazenamento. Por exemplo, as opções de conta de armazenamento GPv2 e BLOBs suportam [limites de escalabilidade e capacidade](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/) mais elevados em comparação com a GPv1. (Contacte o Suporte Azure para solicitar um aumento do limite de armazenamento.) Estas opções de conta podem melhorar o desempenho das soluções do Lote que contêm um grande número de tarefas paralelas que lêem ou escrevem para a conta de armazenamento.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [Nós e piscinas.](nodes-and-pools.md)
- Aprenda a criar uma conta Batch utilizando o [portal Azure](batch-account-create-portal.md).

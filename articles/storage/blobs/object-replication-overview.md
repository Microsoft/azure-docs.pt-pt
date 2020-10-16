---
title: Visão geral da replicação do objeto
titleSuffix: Azure Storage
description: A replicação de objetos assíncroneamente copia blobs de bloco entre uma conta de armazenamento de origem e uma conta de destino. Use a replicação de objetos para minimizar a latência em pedidos de leitura, para aumentar a eficiência das cargas de trabalho computacional, para otimizar a distribuição de dados e para minimizar os custos.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4105698198e6fb7f4e3d3526ff9590ebca4898f1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91612171"
---
# <a name="object-replication-for-block-blobs"></a>Replicação de objeto para bolhas de bloco

A replicação de objetos assíncroneamente copia blobs de bloco entre uma conta de armazenamento de origem e uma conta de destino. Alguns cenários suportados pela replicação de objetos incluem:

- **Minimizar a latência.** A replicação de objetos pode reduzir a latência para pedidos de leitura, permitindo que os clientes consumam dados de uma região que está em proximidade física mais próxima.
- **Aumentar a eficiência para cargas de trabalho de computação.** Com a replicação de objetos, as cargas de trabalho computativas podem processar os mesmos conjuntos de blobs de blocos em diferentes regiões.
- **Otimizar a distribuição de dados.** Pode processar ou analisar dados num único local e depois replicar apenas os resultados em regiões adicionais.
- **Otimizar custos.** Depois de os seus dados terem sido replicados, pode reduzir custos movendo-os para o nível de arquivo usando políticas de gestão do ciclo de vida.

O diagrama seguinte mostra como a replicação de objetos replica blobs de uma conta de armazenamento de origem numa região para contas de destino em duas regiões diferentes.

:::image type="content" source="media/object-replication-overview/object-replication-diagram.svg" alt-text="Diagrama mostrando como funciona a replicação do objeto":::

Para aprender a configurar a replicação de objetos, consulte a replicação do [objeto configurado.](object-replication-configure.md)

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="prerequisites-for-object-replication"></a>Pré-requisitos para a replicação de objetos

A replicação de objetos requer que as seguintes funcionalidades de Armazenamento Azure também estejam ativadas:

- [Alteração do feed](storage-blob-change-feed.md): Deve ser ativado na conta de origem. Para aprender a ativar o feed de mudança, consulte [Ativar e desativar o feed de alteração](storage-blob-change-feed.md#enable-and-disable-the-change-feed).
- [Versão blob](versioning-overview.md): Deve ser ativado tanto nas contas de origem como de destino. Para aprender a ativar a versão, consulte [Ativar e gerir a versão blob](versioning-enable.md).

Permitir a alteração do feed e da versão blob pode incorrer em custos adicionais. Para mais detalhes, consulte a [página de preços do Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

## <a name="object-replication-policies-and-rules"></a>Políticas e regras de replicação de objetos

Ao configurar a replicação de objetos, cria uma política de replicação que especifica a conta de armazenamento de origem e a conta de destino. Uma política de replicação inclui uma ou mais regras que especificam um contentor de origem e um contentor de destino e indicam quais as bolhas de bloqueio no recipiente de origem que serão replicadas.

Depois de configurar a replicação de objetos, o Azure Storage verifica periodicamente e assincroticamente o feed da conta de origem e reproduz assíncronamente qualquer gravação ou exclusão de operações para a conta de destino. A latência da replicação depende do tamanho da bolha do bloco ser replicada.

> [!IMPORTANT]
> Como os dados do blob de bloco são replicados assíncroniamente, a conta de origem e a conta de destino não estão imediatamente sincronizadas. Atualmente não há SLA sobre o tempo que leva para replicar dados para a conta de destino.

### <a name="replication-policies"></a>Políticas de replicação

Ao configurar a replicação de objetos, é criada uma política de replicação tanto na conta de origem como na conta de destino através do fornecedor de recursos de armazenamento Azure. A política de replicação é identificada por uma identificação de política. A política das contas de origem e de destino deve ter a mesma identificação política para que a replicação se realize.

Uma conta de armazenamento pode servir como a conta de origem de até duas contas de destino. As contas de origem e destino podem estar na mesma região ou em diferentes regiões. Podem também residir em diferentes subscrições e em diferentes inquilinos do Azure Ative Directory (Azure AD). Apenas uma política de replicação pode ser criada para cada par de conta de conta de origem/destino.

### <a name="replication-rules"></a>Regras de replicação

As regras de replicação especificam como o Azure Storage irá replicar bolhas de um recipiente de origem para um recipiente de destino. Pode especificar até 10 regras de replicação para cada política de replicação. Cada regra de replicação define um único recipiente de origem e destino, e cada recipiente de origem e destino pode ser utilizado numa única regra.

Quando se cria uma regra de replicação, por padrão apenas são copiadas novas bolhas de blocos que são posteriormente adicionadas ao recipiente de origem. Pode especificar que tanto as bolhas de blocos novas como as existentes são copiadas, ou pode definir um âmbito de cópia personalizado que copia blobs de blocos criados a partir de um tempo especificado.

Também pode especificar um ou mais filtros como parte de uma regra de replicação para filtrar bolhas de bloqueio por prefixo. Quando especificar um prefixo, apenas as bolhas correspondentes ao prefixo no recipiente de origem serão copiadas para o recipiente de destino.

Os recipientes de origem e destino devem existir antes de os especificar numa regra. Após criar a política de replicação, o contentor de destino torna-se só de leitura. Qualquer tentativa de escrever no contentor de destino falhará com o código de erro 409 (Conflito). No entanto, pode ligar para a operação [set Blob Tier](/rest/api/storageservices/set-blob-tier) numa bolha no recipiente de destino para movê-la para o nível de arquivo. Para obter mais informações sobre o nível de arquivo, consulte o [armazenamento Azure Blob: níveis de acesso quentes, frescos e de arquivo.](storage-blob-storage-tiers.md#archive-access-tier)

## <a name="billing"></a>Faturação

A replicação de objetos incorre em custos adicionais na leitura e na escrita de transações contra as contas de origem e destino, bem como encargos de erupção para a replicação de dados da conta de origem para a conta de destino e taxas de leitura para processar a alteração de feed.

## <a name="next-steps"></a>Passos seguintes

- [Configurar a replicação de objetos](object-replication-configure.md)
- [Alterar suporte de alimentação no armazenamento de blob Azure](storage-blob-change-feed.md)
- [Ativar e gerir a versão blob](versioning-enable.md)

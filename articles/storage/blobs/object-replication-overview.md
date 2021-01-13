---
title: Visão geral da replicação do objeto
titleSuffix: Azure Storage
description: A replicação de objetos assíncroneamente copia blobs de bloco entre uma conta de armazenamento de origem e uma conta de destino. Use a replicação de objetos para minimizar a latência em pedidos de leitura, para aumentar a eficiência das cargas de trabalho computacional, para otimizar a distribuição de dados e para minimizar os custos.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/13/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ff2408e35d76a6ea0d5221e04c7a41ed6cde7ac9
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/13/2021
ms.locfileid: "98178981"
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

## <a name="how-object-replication-works"></a>Como funciona a replicação de objetos

A replicação de objetos copia assincroticamente bolhas de bloco num recipiente de acordo com as regras que configura. O conteúdo da bolha, quaisquer versões associadas à bolha, e os metadados e propriedades da bolha são todos copiados do recipiente de origem para o recipiente de destino.

> [!IMPORTANT]
> Como os dados do blob de bloco são replicados assíncroniamente, a conta de origem e a conta de destino não estão imediatamente sincronizadas. Atualmente não há SLA sobre o tempo que leva para replicar dados para a conta de destino. Pode verificar o estado de replicação na bolha de origem para determinar se a replicação está completa. Para obter mais informações, [consulte o estado de replicação de uma bolha](object-replication-configure.md#check-the-replication-status-of-a-blob).

### <a name="blob-versioning"></a>Versão blob

A replicação do objeto requer que a versão blob esteja ativada tanto nas contas de origem como de destino. Quando uma bolha replicada na conta de origem é modificada, uma nova versão do blob é criada na conta de origem que reflete o estado anterior da bolha, antes da modificação. A versão atual (ou blob base) na conta de origem reflete as atualizações mais recentes. Tanto a versão atual atualizada como a nova versão anterior são replicadas na conta de destino. Para obter mais informações sobre como as operações de escrita afetam as versões blob, consulte [a versão em operações de escrita](versioning-overview.md#versioning-on-write-operations).

Quando uma bolha na conta de origem é eliminada, a versão atual do blob é capturada numa versão anterior e, em seguida, eliminada. Todas as versões anteriores do blob persistem mesmo depois da versão atual ser eliminada. Este estado é replicado na conta de destino. Para obter mais informações sobre como eliminar as operações afetam as versões blob, consulte [a versão em operações de eliminação](versioning-overview.md#versioning-on-delete-operations).

### <a name="snapshots"></a>Instantâneos

A replicação do objeto não suporta instantâneos blob. Quaisquer instantâneos numa bolha na conta de origem não são replicados na conta de destino.

### <a name="blob-tiering"></a>Camada de bolha

A replicação do objeto é suportada quando as contas de origem e destino estão no nível quente ou fresco. As contas de origem e destino podem estar em diferentes níveis. No entanto, a replicação do objeto falhará se uma bolha na conta de origem ou destino tiver sido transferida para o nível de arquivo. Para obter mais informações sobre os níveis blob, consulte [os níveis de acesso para armazenamento Azure Blob - quente, fresco e arquivo](storage-blob-storage-tiers.md).

### <a name="immutable-blobs"></a>Blobs imutáveis

A replicação do objeto não suporta bolhas imutáveis. Se um recipiente de origem ou destino tiver uma política de retenção baseada no tempo ou uma posição legal, então a replicação do objeto falha. Para obter mais informações sobre bolhas imutáveis, consulte [os dados de blob críticos de negócio da Loja com armazenamento imutável](storage-blob-immutable-storage.md).

## <a name="object-replication-policies-and-rules"></a>Políticas e regras de replicação de objetos

Ao configurar a replicação de objetos, cria uma política de replicação que especifica a conta de armazenamento de origem e a conta de destino. Uma política de replicação inclui uma ou mais regras que especificam um contentor de origem e um contentor de destino e indicam quais as bolhas de bloqueio no recipiente de origem que serão replicadas.

Depois de configurar a replicação de objetos, o Azure Storage verifica periodicamente e assincroticamente o feed da conta de origem e reproduz assíncronamente qualquer gravação ou exclusão de operações para a conta de destino. A latência da replicação depende do tamanho da bolha do bloco ser replicada.

### <a name="replication-policies"></a>Políticas de replicação

Ao configurar a replicação de objetos, é criada uma política de replicação tanto na conta de origem como na conta de destino através do fornecedor de recursos de armazenamento Azure. A política de replicação é identificada por uma identificação de política. A política das contas de origem e de destino deve ter a mesma identificação política para que a replicação se realize.

Uma conta de armazenamento pode servir como a conta de origem de até duas contas de destino. As contas de origem e destino podem estar na mesma região ou em diferentes regiões. Podem também residir em diferentes subscrições e em diferentes inquilinos do Azure Ative Directory (Azure AD). Apenas uma política de replicação pode ser criada para cada par de conta de conta de origem/destino.

### <a name="replication-rules"></a>Regras de replicação

As regras de replicação especificam como o Azure Storage irá replicar bolhas de um recipiente de origem para um recipiente de destino. Pode especificar até 10 regras de replicação para cada política de replicação. Cada regra de replicação define um único recipiente de origem e destino, e cada recipiente de origem e destino pode ser utilizado numa única regra.

Quando se cria uma regra de replicação, por padrão apenas são copiadas novas bolhas de blocos que são posteriormente adicionadas ao recipiente de origem. Pode especificar que tanto as bolhas de blocos novas como as existentes são copiadas, ou pode definir um âmbito de cópia personalizado que copia blobs de blocos criados a partir de um tempo especificado.

Também pode especificar um ou mais filtros como parte de uma regra de replicação para filtrar bolhas de bloqueio por prefixo. Quando especificar um prefixo, apenas as bolhas correspondentes ao prefixo no recipiente de origem serão copiadas para o recipiente de destino.

Os recipientes de origem e destino devem existir antes de os especificar numa regra. Após criar a política de replicação, o contentor de destino torna-se só de leitura. Qualquer tentativa de escrever no contentor de destino falhará com o código de erro 409 (Conflito). No entanto, pode ligar para a operação [set Blob Tier](/rest/api/storageservices/set-blob-tier) numa bolha no recipiente de destino para movê-la para o nível de arquivo. Para obter mais informações sobre o nível de arquivo, consulte o [armazenamento Azure Blob: níveis de acesso quentes, frescos e de arquivo.](storage-blob-storage-tiers.md#archive-access-tier)

## <a name="replication-status"></a>Estado de replicação

Pode verificar o estado de replicação de uma bolha na conta de origem. Para obter mais informações, [consulte o estado de replicação de uma bolha](object-replication-configure.md#check-the-replication-status-of-a-blob).

Se o estado de replicação de uma bolha na conta de origem indicar falha, então investigue as seguintes causas possíveis:

- Certifique-se de que a política de replicação do objeto está configurada na conta de destino.
- Verifique se o contentor de destino ainda existe.
- Se a bolha de origem tiver sido encriptada com uma chave fornecida pelo cliente como parte de uma operação de escrita, então a replicação do objeto falhará. Para obter mais informações sobre as chaves fornecidas pelo cliente, consulte fornecer uma chave de [encriptação sobre um pedido de armazenamento Blob](encryption-customer-provided-keys.md).

## <a name="billing"></a>Faturação

A replicação de objetos incorre em custos adicionais na leitura e na escrita de transações contra as contas de origem e destino, bem como encargos de erupção para a replicação de dados da conta de origem para a conta de destino e taxas de leitura para processar a alteração de feed.

## <a name="next-steps"></a>Passos seguintes

- [Configurar a replicação de objetos](object-replication-configure.md)
- [Alterar suporte de alimentação no armazenamento de blob Azure](storage-blob-change-feed.md)
- [Ativar e gerir a versão blob](versioning-enable.md)

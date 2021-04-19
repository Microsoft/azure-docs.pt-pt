---
title: Descrição geral da conta de armazenamento
titleSuffix: Azure Storage
description: Saiba mais sobre os diferentes tipos de contas de armazenamento no Azure Storage. Rever nomeação de conta, níveis de desempenho, níveis de acesso, redundância, encriptação, pontos finais e muito mais.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 04/19/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: d4874ad6688fa85f0c511632498938817bb218f7
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714205"
---
# <a name="storage-account-overview"></a>Descrição geral da conta de armazenamento

Uma conta de armazenamento Azure contém todos os seus objetos de dados de armazenamento Azure: bolhas, ficheiros, filas, tabelas e discos. A conta de armazenamento fornece um espaço de nome único para os seus dados de Armazenamento Azure que estão acessíveis a partir de qualquer parte do mundo em HTTP ou HTTPS. Os dados na sua conta de armazenamento Azure são duráveis e altamente disponíveis, seguros e massivamente escaláveis.

Para saber como criar uma conta de armazenamento do Azure, veja [Criar uma conta de armazenamento](storage-account-create.md).

## <a name="types-of-storage-accounts"></a>Tipos de contas de armazenamento

O Azure Storage oferece vários tipos de contas de armazenamento. Cada tipo suporta diferentes funcionalidades e tem o seu próprio modelo de preços. Considere estas diferenças antes de criar uma conta de armazenamento para determinar o tipo de conta que é melhor para as suas aplicações.

A tabela a seguir descreve os tipos de contas de armazenamento recomendadas pela Microsoft para a maioria dos cenários:

| Tipo de conta de armazenamento | Serviços suportados | Opções de redundância | Modelo de implementação | Utilização |
|--|--|--|--|--|
| V2 de finalidade geral padrão | Blob, Arquivo, Fila, Tabela e Armazenamento do Lago de Dados<sup>1</sup> | LRS/GRS/RA-GRS<br /><br />ZRS/GZRS/RA-GZRS<sup>2</sup> | Gestor de Recursos<sup>3</sup> | Tipo básico de conta de armazenamento para bolhas, ficheiros, filas e tabelas. Recomendado para a maioria dos cenários usando O Armazenamento Azure. |
| Bolhas de bloco premium<sup>4</sup> | Bloco apenas bolhas | LRS<br /><br />ZRS<sup>2</sup> | Gestor de Recursos<sup>3</sup> | Contas de armazenamento com características de desempenho premium para bolhas de bloco e bolhas de apêndice. Recomendado para cenários com altas taxas de transações, ou cenários que usem objetos menores ou exijam uma latência de armazenamento consistentemente baixa.<br />[Saiba mais...](../blobs/storage-blob-performance-tiers.md) |
| Ações de ficheiro premium<sup>4</sup> | Apenas ações de ficheiros | LRS<br /><br />ZRS<sup>2</sup> | Gestor de Recursos<sup>3</sup> | Contas de armazenamento só com ficheiros com características de desempenho premium. Recomendado para aplicações de escala de empresa ou de alto desempenho.<br />[Saiba mais...](../files/storage-files-planning.md#management-concepts) |
| Bolhas de página premium<sup>4</sup> | Apenas bolhas de página | LRS | Gestor de Recursos<sup>3</sup> | Tipo de conta de armazenamento premium apenas para bolhas de página.<br />[Saiba mais...](../blobs/storage-blob-pageblob-overview.md) |

<sup>1</sup> Azure Data Lake Storage é um conjunto de capacidades dedicadas à análise de big data, construídas no armazenamento Azure Blob. O Armazenamento do Lago de Dados só é suportado em contas de armazenamento V2 de uso geral com um espaço hierárquico habilitado. Para obter mais informações sobre data lake storage gen2, consulte [introdução à Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md).

<sup>2</sup> O armazenamento redundante de zona (ZRS) e o armazenamento de geo-zonas redundantes (GZRS/RA-GZRS) só estão disponíveis para as contas de v2, bloco de blocos premium e contas de ações de ficheiros premium em determinadas regiões. Para obter mais informações sobre as opções de redundância do [Azure Storage, consulte a redundância do Azure Storage](storage-redundancy.md).

<sup>3</sup> O Gestor de Recursos Azure é o modelo de implementação recomendado para recursos Azure, incluindo contas de armazenamento. Para obter mais informações, consulte [a visão geral do Gestor de Recursos.](../../azure-resource-manager/management/overview.md)

<sup>4</sup> As contas de armazenamento num nível de desempenho premium utilizam discos de estado sólido (SSDs) para baixa latência e produção elevada.

As contas de armazenamento antigas também são suportadas. Para obter mais informações, consulte [os tipos de conta de armazenamento Legacy](#legacy-storage-account-types).

## <a name="storage-account-endpoints"></a>Pontos finais da conta do Storage

Uma conta de armazenamento fornece um espaço de nome único em Azure para os seus dados. Cada objeto que armazena no Azure Storage tem um endereço que inclui o nome da sua conta única. A combinação do nome da conta e do ponto final do serviço Azure Storage forma os pontos finais da sua conta de armazenamento.

Para atribuir um nome à sua conta de armazenamento, mantenha estas regras em mente:

- Os nomes das contas de armazenamento têm de ter entre 3 e 24 carateres de comprimento e apenas podem conter números e letras minúsculas.
- O nome da sua conta do Storage tem de ser exclusivo no Azure. Duas contas de armazenamento não podem ter o mesmo nome.

A tabela que se segue lista o formato do ponto final para cada um dos serviços de Armazenamento Azure.

| Serviço de armazenamento | Ponto final |
|--|--|
| Armazenamento de blobs | `https://<storage-account>.blob.core.windows.net` |
| Data Lake Storage Gen2 | `https://<storage-account>.dfs.core.windows.net` |
| Ficheiros do Azure | `https://<storage-account>.file.core.windows.net` |
| Armazenamento de filas | `https://<storage-account>.queue.core.windows.net` |
| Table Storage | `https://<storage-account>.table.core.windows.net` |

Construa o URL para aceder a um objeto numa conta de armazenamento, anexando a localização do objeto na conta de armazenamento para o ponto final. Por exemplo, o URL para uma bolha será semelhante a:

`http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*`

Também pode configurar a sua conta de armazenamento para usar um domínio personalizado para bolhas. Para mais informações, consulte [configurar um nome de domínio personalizado para a sua conta de Armazenamento Azure.](../blobs/storage-custom-domain-name.md)  

## <a name="migrating-a-storage-account"></a>Migrar uma conta de armazenamento

O quadro que se segue resume e aponta para orientações sobre a mudança, a atualização ou migração de uma conta de armazenamento:

| Cenário de migração | Detalhes |
|--|--|
| Mover uma conta de armazenamento para uma subscrição diferente | O Azure Resource Manager oferece opções para mover um recurso para uma subscrição diferente. Para obter mais informações, consulte [mover recursos para um novo grupo de recursos ou subscrição.](../../azure-resource-manager/management/move-resource-group-and-subscription.md) |
| Mover uma conta de armazenamento para um grupo de recursos diferente | O Azure Resource Manager oferece opções para mover um recurso para um grupo de recursos diferente. Para obter mais informações, consulte [mover recursos para um novo grupo de recursos ou subscrição.](../../azure-resource-manager/management/move-resource-group-and-subscription.md) |
| Mover uma conta de armazenamento para uma região diferente | Para mover uma conta de armazenamento, crie uma cópia da conta de armazenamento noutra região. Em seguida, mova os seus dados para essa conta usando o AzCopy, ou outra ferramenta à sua escolha. Para obter mais informações, consulte [uma conta de Armazenamento Azure para outra região.](storage-account-move.md) |
| Atualizar para uma conta de armazenamento v2 para fins gerais | Pode atualizar uma conta de armazenamento v1 ou blob para uma conta v2 para fins gerais. Note que esta ação não pode ser desfeita. Para obter mais informações, consulte [upgrade para uma conta de armazenamento v2 para fins gerais.](storage-account-upgrade.md) |
| Migrar uma conta de armazenamento clássica para O Gestor de Recursos Azure | O modelo de implementação do Azure Resource Manager é superior ao modelo clássico de implementação em termos de funcionalidade, escalabilidade e segurança. Para obter mais informações sobre a migração de uma conta de armazenamento clássica para Azure Resource Manager, consulte [a migração de contas de armazenamento](../../virtual-machines/migration-classic-resource-manager-overview.md#migration-of-storage-accounts) em suporte de plataforma de recursos **IaaS do clássico para o Azure Resource Manager.** |

## <a name="transferring-data-into-a-storage-account"></a>Transferir dados para uma conta de armazenamento

A Microsoft fornece serviços e utilitários para importar os seus dados de dispositivos de armazenamento no local ou fornecedores de armazenamento em nuvem de terceiros. A solução que usa depende da quantidade de dados que está a transferir. Para mais informações, consulte [a visão geral da migração do Azure Storage](storage-migration-overview.md).

## <a name="storage-account-encryption"></a>Encriptação de contas de armazenamento

Todos os dados da sua conta de armazenamento são automaticamente encriptados no lado do serviço. Para obter mais informações sobre encriptação e gestão de chaves, consulte [a encriptação do Azure Storage para obter dados em repouso](storage-service-encryption.md).

## <a name="storage-account-billing"></a>Faturação da conta do Storage

Faturas de Armazenamento Azure com base no uso da sua conta de armazenamento. Todos os objetos numa conta do Storage são faturados em conjunto como um grupo. Os custos de armazenagem são calculados de acordo com os seguintes fatores:

- **Região** refere-se à região geográfica em que a sua conta está sediada.
- **O tipo de conta** refere-se ao tipo de conta de armazenamento que está a utilizar.
- **O nível de acesso** refere-se ao padrão de utilização de dados especificado para a sua conta de armazenamento v2 ou Blob para fins gerais.
- **A capacidade** refere-se à quantidade de loteamento da sua conta de armazenamento que está a usar para armazenar dados.
- **A redundância** determina quantas cópias dos seus dados são mantidas ao mesmo tempo e em que locais.
- **As transações** referem-se a todas as operações de leitura e escrita para o Azure Storage.
- **A saída de dados** refere-se a quaisquer dados transferidos de uma região de Azure. Quando os dados na sua conta de armazenamento são acedidos por uma aplicação que não está a funcionar na mesma região, é cobrado por saída de dados. Para obter informações sobre a utilização de grupos de recursos para agrupar os seus dados e serviços na mesma região para limitar as taxas de saída, veja [o que é um grupo de recursos Azure?](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group)

A [página de preços do Azure Storage](https://azure.microsoft.com/pricing/details/storage/) fornece informações detalhadas sobre preços com base no tipo de conta, capacidade de armazenamento, replicação e transações. Os [detalhes dos preços das transferências de dados](https://azure.microsoft.com/pricing/details/data-transfers/) fornecem informações detalhadas sobre preços para a saída de dados. Pode utilizar a [calculadora de preços do Azure Storage](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) para ajudar a estimar os seus custos.

[!INCLUDE [cost-management-horizontal](../../../includes/cost-management-horizontal.md)]

## <a name="legacy-storage-account-types"></a>Tipos de conta de armazenamento de legado

A tabela seguinte descreve os tipos de conta de armazenamento legado. Estes tipos de conta não são recomendados pela Microsoft, mas podem ser utilizados em certos cenários:

| Tipo de conta de armazenamento de legado | Serviços suportados | Opções de redundância | Modelo de implementação | Utilização |
|--|--|--|--|--|
| Padrão de finalidade geral v1 | Blob, Arquivo, Fila, Tabela e Armazenamento do Lago de Dados | LRS/GRS/RA-GRS | Gestor de Recursos, Clássico | As contas v1 de uso geral podem não ter as funcionalidades mais recentes ou o preço mais baixo por gigabyte. Considere a utilização para estes cenários:<br /><ul><li>As suas aplicações requerem o modelo de implementação clássico do Azure.</li><li>As suas aplicações são intensivas em transações ou utilizam largura de banda de geo-replicação significativa, mas não requerem grande capacidade. Neste caso, o v1 de uso geral pode ser a escolha mais económica.</li><li>Utiliza uma versão da Azure Storage REST API que é anterior a 2014-02-14 ou uma biblioteca de clientes com uma versão inferior a 4.x, e não pode atualizar a sua aplicação.</li></ul> |
| Armazenamento Standard Blob | Blob (blobs de bloco e apenas bolhas de apêndice) | LRS/GRS/RA-GRS | Resource Manager | A Microsoft recomenda a utilização de contas v2 de uso geral padrão, quando possível. |

## <a name="next-steps"></a>Passos seguintes

- [Criar uma conta de armazenamento](storage-account-create.md)
- [Atualizar para uma conta de armazenamento v2 para fins gerais](storage-account-upgrade.md)
- [Recuperar uma conta de armazenamento eliminada](storage-account-recover.md)
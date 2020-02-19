---
title: Opções de desempenho – Hiperescala (Citus) - Base de Dados Azure para PostgreSQL
description: Opções para um grupo de servidores de hiperescala (Citus), incluindo computação de nó, armazenamento e regiões.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 2/18/2020
ms.openlocfilehash: 1c9b4b1099bda69764aa7a1a5a984a6316e1047d
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77462416"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-performance-options"></a>Base de Dados Azure para opções de desempenho PostgreSQL – Hiperescala (Citus)

## <a name="compute-and-storage"></a>Computação e armazenamento
 
Pode selecionar as definições de cálculo e armazenamento de forma independente para os nós dos trabalhadores e o nó coordenador num grupo de servidores de Hiperescala (Citus).  Os recursos computacionais são fornecidos como vCores, que representam o CPU lógico do hardware subjacente. O tamanho do armazenamento para o fornecimento refere-se à capacidade disponível para o coordenador e nódos de trabalhador no seu grupo de servidores Hyperscale (Citus). O armazenamento inclui ficheiros de base de dados, ficheiros temporários, registos de transações e registos de servidores do Postgres. A quantidade total de armazenamento que disponibiliza define também a capacidade de I/S disponível para cada nó de trabalhador e coordenador.
 
|                       | Nó operário           | Nó coordenador      |
|-----------------------|-----------------------|-----------------------|
| Compute, vCores       | 4, 8, 16, 32, 64      | 4, 8, 16, 32, 64      |
| Memória por vCore, GiB | 8                     | 4                     |
| Tamanho do armazenamento, TiB     | 0.5, 1, 2             | 0.5, 1, 2             |
| Tipo de armazenamento          | Finalidade geral (SSD) | Finalidade geral (SSD) |
| IOPS                  | Até 3 IOPS/GiB      | Até 3 IOPS/GiB      |


## <a name="regions"></a>Regiões
Os grupos de servidores de hiperescala (Citus) estão disponíveis nas seguintes regiões do Azure:

* Américas:
    * Canadá Central
    * E.U.A. Central
    * E.U.A. Leste
    * E.U.A. Leste 2
    * E.U.A. Centro-Norte
    * E.U.A.Oeste 2
* Ásia-Pacífico:
    * Leste da Austrália
    * Leste do Japão
    * Coreia do Sul Central
    * Ásia Sudeste
* Europa:
    * Europa do Norte
    * Sul do Reino Unido
    * Europa Ocidental

Algumas destas regiões podem não ser inicialmente ativadas em todas as subscrições do Azure. Se quiser utilizar uma região da lista acima e não a vir na sua subscrição, ou se quiser utilizar uma região que não esteja nesta lista, abra um pedido de [apoio.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="pricing"></a>Preços
Para obter as informações mais atualizadas sobre preços, consulte a página de [preços](https://azure.microsoft.com/pricing/details/postgresql/)do serviço .
Para ver o custo da configuração que deseja, o [portal Azure](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) mostra o custo mensal no **separador Configure** com base nas opções que selecionar. Se não tiver uma subscrição Azure, pode utilizar a calculadora de preços Azure para obter um preço estimado. No site da calculadora de [preços Azure,](https://azure.microsoft.com/pricing/calculator/) selecione **Adicionar itens,** expandir a categoria Bases de **Dados** e escolher a Base de **Dados Azure para PostgreSQL – Hyperscale (Citus)** para personalizar as opções.
 
## <a name="next-steps"></a>Passos seguintes
Aprenda a criar um grupo de [servidores de hiperescala (Citus) no portal](quickstart-create-hyperscale-portal.md).

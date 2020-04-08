---
title: Opções de configuração - Hiperescala (Citus) - Base de Dados Azure para PostgreSQL
description: Opções para um grupo de servidores de hiperescala (Citus), incluindo computação de nó, armazenamento e regiões.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 4/6/2020
ms.openlocfilehash: a2c376ec2bd1f03b626c11b0d6a6c3850c9ef8c4
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804593"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-configuration-options"></a>Base de Dados Azure para opções de configuração PostgreSQL – Hiperescala (Citus)

## <a name="compute-and-storage"></a>Computação e armazenamento
 
Pode selecionar as definições de cálculo e armazenamento de forma independente para os nós dos trabalhadores e o nó coordenador num grupo de servidores de Hiperescala (Citus).  Os recursos computacionais são fornecidos como vCores, que representam o CPU lógico do hardware subjacente. O tamanho do armazenamento para o fornecimento refere-se à capacidade disponível para o coordenador e nódos de trabalhador no seu grupo de servidores Hyperscale (Citus). O armazenamento inclui ficheiros de base de dados, ficheiros temporários, registos de transações e registos de servidores do Postgres.
 
|                       | Nó operário           | Nó coordenador      |
|-----------------------|-----------------------|-----------------------|
| Compute, vCores       | 4, 8, 16, 32, 64      | 4, 8, 16, 32, 64      |
| Memória por vCore, GiB | 8                     | 4                     |
| Tamanho do armazenamento, TiB     | 0.5, 1, 2             | 0.5, 1, 2             |
| Tipo de armazenamento          | Finalidade geral (SSD) | Finalidade geral (SSD) |
| IOPS                  | Até 3 IOPS/GiB      | Até 3 IOPS/GiB      |

A quantidade total de RAM num único nó de Hiperescala (Citus) baseia-se no número selecionado de vCores.

| vCores | Um nó de trabalhador, GiB RAM | Nó coordenador, GiB RAM |
|--------|--------------------------|---------------------------|
| 4      | 32                       | 16                        |
| 8      | 64                       | 32                        |
| 16     | 128                      | 64                        |
| 32     | 256                      | 128                       |
| 64     | 432                      | 256                       |

A quantidade total de armazenamento que disponibiliza define também a capacidade de I/S disponível para cada nó de trabalhador e coordenador.

| Tamanho do armazenamento, TiB | IOPS máximos |
|-------------------|--------------|
| 0,5               | 1,536        |
| 1                 | 3,072        |
| 2                 | 6,148        |

Para todo o cluster de Hiperescala (Citus), o IOPS agregado trabalha para os seguintes valores:

| Nódosos operários | 0.5 TiB, iOPS total | 1 TiB, iOPS total | 2 TiB, iOPS total |
|--------------|---------------------|-------------------|-------------------|
| 2            | 3,072               | 6,144             | 12,296            |
| 3            | 4,608               | 9,216             | 18,444            |
| 4            | 6,144               | 12,288            | 24,592            |
| 5            | 7,680               | 15,360            | 30,740            |
| 6            | 9,216               | 18,432            | 36,888            |
| 7            | 10,752              | 21,504            | 43,036            |
| 8            | 12,288              | 24,576            | 49,184            |
| 9            | 13,824              | 27,648            | 55,332            |
| 10           | 15,360              | 30,720            | 61,480            |
| 11           | 16,896              | 33,792            | 67,628            |
| 12           | 18,432              | 36,864            | 73,776            |
| 13           | 19,968              | 39,936            | 79,924            |
| 14           | 21,504              | 43,008            | 86,072            |
| 15           | 23,040              | 46,080            | 92,220            |
| 16           | 24,576              | 49,152            | 98,368            |
| 17           | 26,112              | 52,224            | 104,516           |
| 18           | 27,648              | 55,296            | 110,664           |
| 19           | 29,184              | 58,368            | 116,812           |
| 20           | 30,720              | 61,440            | 122,960           |

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
    * Europa ocidental

Algumas destas regiões podem não ser inicialmente ativadas em todas as subscrições do Azure. Se quiser utilizar uma região da lista acima e não a vir na sua subscrição, ou se quiser utilizar uma região que não esteja nesta lista, abra um pedido de [apoio.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="pricing"></a>Preços
Para obter as informações mais atualizadas sobre preços, consulte a página de [preços](https://azure.microsoft.com/pricing/details/postgresql/)do serviço .
Para ver o custo da configuração que deseja, o [portal Azure](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) mostra o custo mensal no **separador Configure** com base nas opções que selecionar. Se não tiver uma subscrição Azure, pode utilizar a calculadora de preços Azure para obter um preço estimado. No site da calculadora de [preços Azure,](https://azure.microsoft.com/pricing/calculator/) selecione **Adicionar itens,** expandir a categoria Bases de **Dados** e escolher a Base de **Dados Azure para PostgreSQL – Hyperscale (Citus)** para personalizar as opções.
 
## <a name="next-steps"></a>Passos seguintes
Aprenda a criar um grupo de [servidores de hiperescala (Citus) no portal](quickstart-create-hyperscale-portal.md).

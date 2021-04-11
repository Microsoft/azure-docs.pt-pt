---
title: Opções de configuração - Hiperescala (Citus) - Base de Dados Azure para PostgreSQL
description: Opções para um grupo de servidores Hyperscale (Citus), incluindo computação de nó, armazenamento e regiões.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.custom: references_regions
ms.date: 04/07/2021
ms.openlocfilehash: ae416c9acd03b3ee239a858aae550fb87293465a
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012790"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-configuration-options"></a>Azure Database para opções de configuração pós-escala (Citus)

## <a name="compute-and-storage"></a>Computação e armazenamento
 
Pode selecionar as definições de cálculo e armazenamento de forma independente para os nós dos trabalhadores e o nó coordenador num grupo de servidores Hyperscale (Citus).  Os recursos compute são fornecidos como vCores, que representam o CPU lógico do hardware subjacente. O tamanho de armazenamento para provisionamento refere-se à capacidade disponível para o coordenador e os nós dos trabalhadores no seu grupo de servidorEs Hyperscale (Citus). O armazenamento inclui ficheiros de base de dados, ficheiros temporários, registos de transações e registos de servidores postgres.

### <a name="standard-tier"></a>Escalão standard
 
| Recurso              | Nó do trabalhador           | Nó coordenador      |
|-----------------------|-----------------------|-----------------------|
| Compute, vCores       | 4, 8, 16, 32, 64      | 4, 8, 16, 32, 64      |
| Memória por vCore, GiB | 8                     | 4                     |
| Tamanho de armazenamento, TiB     | 0.5, 1, 2             | 0.5, 1, 2             |
| Tipo de armazenamento          | Finalidade geral (SSD) | Finalidade geral (SSD) |
| IOPS                  | Até 3 IOPS/GiB      | Até 3 IOPS/GiB      |

A quantidade total de RAM num único nó de Hiperescala (Citus) baseia-se no número selecionado de vCores.

| vCores | Um nó operário, GiB RAM | Nó coordenador, GiB RAM |
|--------|--------------------------|---------------------------|
| 4      | 32                       | 16                        |
| 8      | 64                       | 32                        |
| 16     | 128                      | 64                        |
| 32     | 256                      | 128                       |
| 64     | 432                      | 256                       |

A quantidade total de armazenamento que fornece também define a capacidade de E/O disponível para cada trabalhador e nó coordenador.

| Tamanho de armazenamento, TiB | IOPS máximos |
|-------------------|--------------|
| 0,5               | 1,536        |
| 1                 | 3,072        |
| 2                 | 6,148        |

Para todo o cluster Hyperscale (Citus), o IOPS agregado trabalha para os seguintes valores:

| Nódoas operárias | 0.5 TiB, total de IOPS | 1 TiB, IOPS total | 2 TiB, IOPS total |
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

### <a name="basic-tier-preview"></a>Nível básico (pré-visualização)

> [!IMPORTANT]
> O nível básico de Hiperescala (Citus) está atualmente em pré-visualização.  Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
>
> Pode ver uma lista completa de outras novidades em [funcionalidades de pré-visualização para Hyperscale (Citus)](hyperscale-preview-features.md).

O [nível básico](concepts-hyperscale-tiers.md) de Hiperescala (Citus) é um grupo de servidor com apenas um nó.  Como não há distinção entre coordenador e nós operários, é menos complicado escolher recursos de computação e armazenamento.

| Recurso              | Opções disponíveis     |
|-----------------------|-----------------------|
| Compute, vCores       | 2, 4, 8               |
| Memória por vCore, GiB | 4                     |
| Tamanho de armazenamento, GiB     | 128, 256, 512         |
| Tipo de armazenamento          | Finalidade geral (SSD) |
| IOPS                  | Até 3 IOPS/GiB      |

A quantidade total de RAM num único nó de Hiperescala (Citus) baseia-se no número selecionado de vCores.

| vCores | GiB RAM |
|--------|---------|
| 2      | 8       |
| 4      | 16      |
| 8      | 32      |

A quantidade total de armazenamento que fornece também define a capacidade de E/S disponível para o nó de nível básico.

| Tamanho de armazenamento, GiB | IOPS máximos |
|-------------------|--------------|
| 128               | 384          |
| 256               | 768          |
| 512               | 1,536        |

## <a name="regions"></a>Regiões
Os grupos de servidores de hiperescala (Citus) estão disponíveis nas seguintes regiões de Azure:

* Américas:
    * Canadá Central
    * E.U.A. Central
    * Leste dos EUA *
    * E.U.A. Leste 2
    * E.U.A. Centro-Norte
    * E.U.A. Oeste 2
* Ásia-Pacífico:
    * Leste da Austrália
    * Leste do Japão
    * Coreia do Sul Central
    * Sudeste Asiático
* Europa:
    * Europa do Norte
    * Sul do Reino Unido
    * Europa Ocidental

(= \* suporta [funcionalidades de pré-visualização)](hyperscale-preview-features.md)

Algumas destas regiões podem não ser inicialmente ativadas em todas as subscrições do Azure. Se quiser utilizar uma região da lista acima e não a vir na sua subscrição, ou se quiser utilizar uma região que não esteja nesta lista, abra um pedido de [apoio](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="pricing"></a>Preços
Para obter as informações de preços mais atualizadas, consulte a [página de preços do](https://azure.microsoft.com/pricing/details/postgresql/)serviço .
Para ver o custo da configuração desejada, o [portal Azure](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) mostra o custo mensal no **separador Configurar** com base nas opções selecionadas. Se não tiver uma subscrição do Azure, pode utilizar a calculadora de preços Azure para obter um preço estimado. No site da [calculadora de preços Azure,](https://azure.microsoft.com/pricing/calculator/) selecione **Adicionar itens,** expandir a categoria **Base de Dados** e escolher a Base de **Dados Azure para PostgreSQL – Hyperscale (Citus)** para personalizar as opções.
 
## <a name="next-steps"></a>Passos seguintes
Saiba como criar um grupo de [servidores Hyperscale (Citus) no portal](quickstart-create-hyperscale-portal.md).

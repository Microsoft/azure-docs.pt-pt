---
title: Opções de configuração - Hiperescala (Citus) - Base de Dados Azure para PostgreSQL
description: Opções para um grupo de servidores Hyperscale (Citus), incluindo computação de nó, armazenamento e regiões.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 1/12/2021
ms.openlocfilehash: 48537483501165d4a978afdbd05560613170d187
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98165616"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-configuration-options"></a>Azure Database para opções de configuração pós-escala (Citus)

## <a name="compute-and-storage"></a>Computação e armazenamento
 
Pode selecionar as definições de cálculo e armazenamento de forma independente para os nós dos trabalhadores e o nó coordenador num grupo de servidores Hyperscale (Citus).  Os recursos compute são fornecidos como vCores, que representam o CPU lógico do hardware subjacente. O tamanho de armazenamento para provisionamento refere-se à capacidade disponível para o coordenador e os nós dos trabalhadores no seu grupo de servidorEs Hyperscale (Citus). O armazenamento inclui ficheiros de base de dados, ficheiros temporários, registos de transações e registos de servidores postgres.
 
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

## <a name="regions"></a>Regiões
Os grupos de servidores de hiperescala (Citus) estão disponíveis nas seguintes regiões de Azure:

* Américas:
    * Canadá Central
    * E.U.A. Central
    * E.U.A. Leste
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

Algumas destas regiões podem não ser inicialmente ativadas em todas as subscrições do Azure. Se quiser utilizar uma região da lista acima e não a vir na sua subscrição, ou se quiser utilizar uma região que não esteja nesta lista, abra um pedido de [apoio](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="limits-and-limitations"></a>Limites e limitações

A secção seguinte descreve a capacidade e os limites funcionais no serviço Hyperscale (Citus).

### <a name="maximum-connections"></a>Número máximo de ligações

Todas as ligações PostgreSQL (mesmo as ociosas) utilizam pelo menos 10 MB de memória, por isso é importante limitar as ligações simultâneas. Aqui estão os limites que escolhemos para manter os nós saudáveis:

* Nó coordenador
   * Ligações máximas: 300
   * Ligações máximas do utilizador: 297
* Nó do trabalhador
   * Ligações máximas: 600
   * Ligações máximas do utilizador: 597

As tentativas de ligação para além destes limites falharão com um erro. O sistema reserva três ligações para os nós de monitorização, razão pela qual existem menos três ligações disponíveis para consultas de utilizador do que as ligações totais.

Estabelecer novas ligações leva tempo. Isso funciona contra a maioria das aplicações, que solicitam muitas ligações de curta duração. Recomendamos a utilização de um pooler de ligação, tanto para reduzir as transações ociosas como para reutilizar as ligações existentes. Para saber mais, visite o nosso [blog post.](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717)

### <a name="storage-scaling"></a>Dimensionamento de armazenamento

O armazenamento em coordenadores e nós de trabalhadores pode ser aumentado (aumentado) mas não pode ser reduzido (diminuído).

### <a name="storage-size"></a>Tamanho do armazenamento

Até 2 TiB de armazenamento é suportado em coordenadores e nós de trabalhadores. Consulte as opções de armazenamento disponíveis e o cálculo do IOPS [acima](#compute-and-storage) para tamanhos de nó e cluster.

### <a name="database-creation"></a>Criação de base de dados

O portal Azure fornece credenciais para ligar exatamente a uma base de dados por grupo de servidores Hyperscale (Citus), a `citus` base de dados. Atualmente, não é permitida a criação de outra base de dados e o comando CREATE DATABASE falhará com um erro.

## <a name="pricing"></a>Preços
Para obter as informações de preços mais atualizadas, consulte a [página de preços do](https://azure.microsoft.com/pricing/details/postgresql/)serviço .
Para ver o custo da configuração desejada, o [portal Azure](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) mostra o custo mensal no **separador Configurar** com base nas opções selecionadas. Se não tiver uma subscrição do Azure, pode utilizar a calculadora de preços Azure para obter um preço estimado. No site da [calculadora de preços Azure,](https://azure.microsoft.com/pricing/calculator/) selecione **Adicionar itens,** expandir a categoria **Base de Dados** e escolher a Base de **Dados Azure para PostgreSQL – Hyperscale (Citus)** para personalizar as opções.
 
## <a name="next-steps"></a>Passos seguintes
Saiba como criar um grupo de [servidores Hyperscale (Citus) no portal](quickstart-create-hyperscale-portal.md).

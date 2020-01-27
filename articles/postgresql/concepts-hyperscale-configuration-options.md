---
title: Opções de desempenho – Citus (hiperescala) – banco de dados do Azure para PostgreSQL
description: Opções para um grupo de servidores de hiperescala (Citus), incluindo computação de nó, armazenamento e regiões.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: 2ee3e661d6c01aa2e4f37ac9a70e00be5da5f794
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975640"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-performance-options"></a>Banco de dados do Azure para PostgreSQL – opções de desempenho de Citus (hiperescala)

## <a name="compute-and-storage"></a>Computação e armazenamento
 
Você pode selecionar as configurações de computação e armazenamento independentemente para nós de trabalho e o nó de coordenador em um grupo de servidores de hiperescala (Citus).  Os recursos de computação são fornecidos como vCores, que representam a CPU lógica do hardware subjacente. O tamanho do armazenamento para provisionamento refere-se à capacidade disponível para os nós de coordenador e de trabalho em seu grupo de servidores de hiperescala (Citus). O armazenamento inclui arquivos de banco de dados, arquivos temporários, logs de transações e logs do servidor Postgres. A quantidade total de armazenamento que você provisiona também define a capacidade de e/s disponível para cada nó de trabalho e coordenador.
 
|                       | Nó de trabalho           | Nó de coordenador      |
|-----------------------|-----------------------|-----------------------|
| Computação, vCores       | 4, 8, 16, 32, 64      | 4, 8, 16, 32, 64      |
| Memória por vCore, GiB | 8                     | 4                     |
| Tamanho do armazenamento, TiB     | 0,5, 1, 2             | 0,5, 1, 2             |
| Tipo de armazenamento          | Uso geral (SSD) | Uso geral (SSD) |
| IOPS                  | Até 3 IOPS/GiB      | Até 3 IOPS/GiB      |


## <a name="regions"></a>Regiões
Os grupos de servidores de hiperescala (Citus) estão disponíveis nas seguintes regiões do Azure:

* Américas
    * Centro-Canadá *
    * E.U.A. Leste
    * E.U.A. Leste 2
    * EUA Central norte *
    * E.U.A. Oeste 2
* Pacífico Asiático:
    * Leste da Austrália *
    * Sudeste Asiático
* Européia
    * Europa do Norte
    * Sul do Reino Unido
    * Europa Ocidental

As regiões com um asterisco (\*) ainda não dão suporte à [alta disponibilidade](concepts-hyperscale-high-availability.md).

## <a name="pricing"></a>Preços
Para obter as informações de preços mais atualizadas, consulte a página de [preços](https://azure.microsoft.com/pricing/details/postgresql/)do serviço.
Para ver o custo da configuração desejada, o [portal do Azure](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) mostra o custo mensal na guia **Configurar** com base nas opções selecionadas. Se você não tiver uma assinatura do Azure, poderá usar a calculadora de preços do Azure para obter um preço estimado. No site da [calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/) , selecione **Adicionar itens**, expanda a categoria **bancos** de dados e escolha **banco de dados do Azure para PostgreSQL – Citus (hiperescala)** para personalizar as opções.
 
## <a name="next-steps"></a>Passos seguintes
Saiba como [criar um grupo de servidores do Citus (hiperescala) no portal](quickstart-create-hyperscale-portal.md).

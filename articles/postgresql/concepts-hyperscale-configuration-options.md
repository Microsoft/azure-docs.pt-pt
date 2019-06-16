---
title: Base de dados do Azure para PostgreSQL – Hiperescala (Citus) (pré-visualização) opções de desempenho
description: Opções para um grupo de servidores de Hiperescala (Citus), incluindo a computação de nó, armazenamento e regiões.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: e8c1c2e51ca14ae9b17f0d7efb20552cdd55139b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65077294"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-preview-performance-options"></a>Base de dados do Azure para PostgreSQL – Hiperescala (Citus) (pré-visualização) opções de desempenho

## <a name="compute-and-storage"></a>Computação e armazenamento
 
Pode selecionar as definições de computação e armazenamento de forma independente para nós de trabalho e o nó coordenador num grupo de servidor Hiperescala (Citus).  Computação foram fornecidos recursos como vCores, que representam a CPU lógica do hardware subjacente. O tamanho de armazenamento para o aprovisionamento refere-se à capacidade disponível para os nós de coordenador e de trabalho no seu grupo de servidor de Hiperescala (Citus). O armazenamento inclui ficheiros de base de dados, arquivos temporários, registos de transações e os registos do servidor Postgres. A quantidade total de armazenamento que aprovisionar também define a capacidade de e/s disponível para cada nó de trabalho e o coordenador.
 
|                       | Nó de trabalho           | Nó coordenador      |
|-----------------------|-----------------------|-----------------------|
| Computação, vCores       | 4, 8, 16, 32          | 4, 8, 16, 32          |
| Memória por vCore, GiB | 8                     | 4                     |
| Tamanho de armazenamento, TiB     | 0.5, 1, 2             | 0.5, 1, 2             |
| Tipo de armazenamento          | Fins gerais (SSD) | Fins gerais (SSD) |
| IOPS                  | Máximo de 3 IOPS/GiB      | Máximo de 3 IOPS/GiB      |


## <a name="regions"></a>Regiões
Grupos de servidores de grande escala (Citus) estão disponíveis nas seguintes regiões do Azure:
* EUA Leste 2
* Sudeste Asiático
* Europa Ocidental
* EUA Oeste 2

## <a name="pricing"></a>Preços
Para informações mais atualizado sobre preços, consulte o serviço [página de preços](https://azure.microsoft.com/pricing/details/postgresql/).
Para ver o custo para a configuração que desejar, o [portal do Azure](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) mostra o custo mensal sobre o **configurar** separador com base nas opções que selecionar. Se não tiver uma subscrição do Azure, pode utilizar a Calculadora de preços do Azure para obter um preço estimado. Sobre o [Calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/) Web site, selecione **adicionar itens**, expanda o **bases de dados** categoria e escolha **base de dados do Azure para PostgreSQL – Hiperescala (Citus)** para personalizar as opções.
 
## <a name="next-steps"></a>Passos Seguintes
Saiba como [criar um grupo de servidores de grande escala (Citus) no portal do](quickstart-create-hyperscale-portal.md).

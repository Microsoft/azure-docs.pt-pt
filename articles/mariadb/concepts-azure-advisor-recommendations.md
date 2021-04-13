---
title: Conselheiro Azure para MariaDB
description: Saiba mais sobre as recomendações do Azure Advisor para a MariaDB.
author: alau-ms
ms.author: alau
ms.service: mariadb
ms.topic: conceptual
ms.date: 04/12/2021
ms.openlocfilehash: 60a0a5763085de38dbfd4dabcb65dc24b299b29a
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107368653"
---
# <a name="azure-advisor-for-mariadb"></a>Conselheiro Azure para MariaDB
Saiba como o Azure Advisor é aplicado na Base de Dados Azure para MariaDB e obtenha respostas a perguntas comuns.
## <a name="what-is-azure-advisor-for-mariadb"></a>O que é Azure Advisor for MariaDB?
O sistema Azure Advisor utiliza telemetria para emitir recomendações de desempenho e fiabilidade para a sua base de dados MariaDB. 

Algumas recomendações são comuns a múltiplas ofertas de produtos, enquanto outras recomendações são baseadas em otimizações específicas do produto.
## <a name="where-can-i-view-my-recommendations"></a>Onde posso ver as minhas recomendações?
As recomendações estão disponíveis na barra lateral de navegação do **Overview** no portal Azure. Uma pré-visualização aparecerá como uma notificação de banner, e os detalhes podem ser vistos na secção **de Notificações** localizada logo abaixo dos gráficos de utilização de recursos.

:::image type="content" source="./media/concepts-azure-advisor-recommendations/advisor-example.png" alt-text="Screenshot do portal Azure mostrando uma recomendação do Azure Advisor.":::

## <a name="recommendation-types"></a>Tipos de recomendação
A base de dados Azure para MariaDB prioriza os seguintes tipos de recomendações:
* **Desempenho**: Para melhorar a velocidade do seu servidor MariaDB. Isto inclui a utilização do CPU, a pressão da memória, a utilização do disco e os parâmetros do servidor específicos do produto. Para mais informações, consulte [recomendações de Desempenho Do Conselheiro](../advisor/advisor-performance-recommendations.md).
* **Fiabilidade**: Para garantir e melhorar a continuidade das suas bases de dados críticas ao negócio. Isto inclui recomendações de limite de armazenamento e limite de ligação. Para mais informações, consulte [recomendações de Fiabilidade do Conselheiro](../advisor/advisor-high-availability-recommendations.md).
* **Custo**: Para otimizar e reduzir os seus gastos globais do Azure. Isto inclui recomendações de tamanho certo do servidor. Para mais informações, consulte [recomendações do Advisor Cost](../advisor/advisor-cost-recommendations.md).

## <a name="understanding-your-recommendations"></a>Compreender as suas recomendações
* **Horário diário**: Para bases de dados Azure MariaDB, verificamos a telemetria do servidor e emitimos recomendações num horário diário. Se fizer uma alteração na configuração do servidor, as recomendações existentes permanecerão visíveis até reexaminarmos a telemetria no dia seguinte. 
* **Histórico de** desempenho : Algumas das nossas recomendações baseiam-se no histórico de desempenho. Estas recomendações só aparecerão depois de um servidor estar a operar com a mesma configuração durante 7 dias. Isto permite-nos detetar padrões de utilização pesada (por exemplo, alta atividade de CPU ou elevado volume de conexão) durante um período de tempo sustentado. Se forja um novo servidor ou se alterar para uma nova configuração vCore, estas recomendações serão interrompidas temporariamente. Isto evita que a telemetria legacy desencadeie recomendações num servidor recentemente reconfigurado. No entanto, isto também significa que as recomendações baseadas no desempenho não podem ser identificadas imediatamente.

## <a name="next-steps"></a>Passos seguintes
Para mais informações, consulte [a Visão Geral do Conselheiro Azure](../advisor/advisor-overview.md).

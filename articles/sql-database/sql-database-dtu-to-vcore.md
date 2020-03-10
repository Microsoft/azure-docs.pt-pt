---
title: Migrar de DTU para vCore
description: Migrar do modelo DTU para o modelo vCore. Migrar para vCore é semelhante à atualização ou degradação entre os níveis padrão e premium.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 03/09/2020
ms.openlocfilehash: 693065046f92e0e9eade14c43e9942772440937d
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2020
ms.locfileid: "78945408"
---
# <a name="migrate-from-the-dtu-based-model-to-the-vcore-based-model"></a>Migrar do modelo baseado em DTU para o modelo baseado em vCore

## <a name="migrate-a-database"></a>Migrar uma base de dados

A migração de uma base de dados do modelo de compra baseado em DTU para o modelo de compra baseado em vCore é semelhante à atualização ou degradação entre os níveis de serviço padrão e premium no modelo de compra baseado em DTU.

## <a name="migrate-databases-that-use-geo-replication"></a>Migrar bases de dados que usam geo-replicação

A migração do modelo baseado em DTU para o modelo de compra baseado em vCore é semelhante à atualização ou redução das relações de geo-replicação entre bases de dados nos níveis de serviço padrão e premium. Durante a migração, não é preciso parar a geo-replicação, mas tem de seguir estas regras de sequenciação:

- Ao atualizar, tem de atualizar primeiro a base de dados secundária e, em seguida, atualizar a principal.
- Ao desaparar, inverta a ordem: primeiro deve desvalorizar a base de dados primária e, em seguida, desvalorizar a secundária.

Quando você está usando geo-replicação entre duas piscinas elásticas, recomendamos que designe uma piscina como a primária e a outra como a secundária. Nesse caso, quando se migra piscinas elásticas, deve usar a mesma orientação de sequenciação. No entanto, se tiver piscinas elásticas que contenham bases de dados primárias e secundárias, trate a piscina com maior utilização como primária e siga as regras de sequenciação em conformidade.  

O quadro seguinte fornece orientações para cenários específicos de migração:

|Nível de serviço atual|Nível de serviço-alvo|Tipo de migração|Ações do utilizador|
|---|---|---|---|
|Standard|Fins gerais|Lateral|Pode migrar em qualquer ordem, mas precisa garantir o tamanho vCore apropriado*|
|Premium|Crítico de negócios|Lateral|Pode migrar em qualquer ordem, mas precisa garantir o tamanho vCore apropriado*|
|Standard|Crítico de negócios|Atualização|Deve migrar secundário primeiro|
|Crítico de negócios|Standard|Mudar para uma versão anterior|Deve migrar primeiro as primárias|
|Premium|Fins gerais|Mudar para uma versão anterior|Deve migrar primeiro as primárias|
|Fins gerais|Premium|Atualização|Deve migrar secundário primeiro|
|Crítico de negócios|Fins gerais|Mudar para uma versão anterior|Deve migrar primeiro as primárias|
|Fins gerais|Crítico de negócios|Atualização|Deve migrar secundário primeiro|
||||

\* Regra geral, cada 100 DTUs no nível padrão requerem pelo menos 1 vCore, e cada 125 DTUs no nível premium requerem pelo menos 1 vCore. Para mais informações, consulte o [modelo de compra baseado em vCore](https://docs.microsoft.com/azure/sql-database/sql-database-purchase-models#vcore-based-purchasing-model).

## <a name="migrate-failover-groups"></a>Grupos de failover migratórios

A migração de grupos failover com múltiplas bases de dados requer migração individual das bases de dados primárias e secundárias. Durante este processo, aplicam-se as mesmas considerações e regras de sequenciação. Após a conversão das bases de dados para o modelo de compra baseado em vCore, o grupo failover permanecerá em vigor com as mesmas definições políticas.

### <a name="create-a-geo-replication-secondary-database"></a>Criar uma base de dados secundária de geo-replicação

Só pode criar uma base de dados secundária de geo-replicação (uma geo-secundária) utilizando o mesmo nível de serviço utilizado para a base de dados primária. Para bases de dados com uma alta taxa de produção de log, recomendamos a criação do geo-secundário com o mesmo tamanho de computação que o primário.

Se estiver a criar um geo-secundário na piscina elástica para uma única base de dados primária, certifique-se de que a definição de `maxVCore` para a piscina corresponde ao tamanho da base de dados primária. Se você está criando um geo-secundário para uma primária em outra piscina elástica, recomendamos que as piscinas tenham as mesmas configurações `maxVCore`.

## <a name="use-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>Utilize cópia da base de dados para converter uma base de dados baseada em DTU numa base de dados baseada em vCore

Pode copiar qualquer base de dados com um tamanho de computação baseado em DTU para uma base de dados com um tamanho de computação baseado em vCore sem restrições ou sequenciação especial, desde que o tamanho da computação-alvo suporte o tamanho máximo da base de dados da base de dados fonte. A cópia da base de dados cria uma imagem instantânea dos dados a partir do momento de início da operação de cópia e não sincroniza os dados entre a fonte e o alvo.

## <a name="next-steps"></a>Passos Seguintes

- Para obter os tamanhos específicos da computação e as escolhas de tamanho de armazenamento disponíveis para bases de dados únicas, consulte [os limites de recursos baseados na Base de Dados SQL vCore para bases de dados únicas](sql-database-vcore-resource-limits-single-databases.md).
- Para os tamanhos específicos da computação e escolhas de tamanho de armazenamento disponíveis para piscinas elásticas, consulte [os limites de recursos baseados em Bases de Dados SQL vCore para piscinas elásticas](sql-database-vcore-resource-limits-elastic-pools.md).

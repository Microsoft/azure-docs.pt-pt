---
title: Banco de dados SQL do Azure – migrar de DTU para vCore | Microsoft Docs
description: Migre do modelo de DTU para o modelo vCore. A migração para o vCore é semelhante à atualização ou ao downgrade entre as camadas Standard e Premium.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 10/08/2019
ms.openlocfilehash: c93d14797bb22e71879e6f80d8bbc94a7ecce6ac
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73520965"
---
# <a name="migrate-from-the-dtu-based-model-to-the-vcore-based-model"></a>Migrar do modelo baseado em DTU para o modelo baseado em vCore

## <a name="migrate-a-database"></a>Migrar uma base de dados

A migração de um banco de dados do modelo de compra baseado em DTU para o modelo de compra baseado em vCore é semelhante à atualização ou ao downgrade entre as camadas de serviço Standard e Premium no modelo de compra baseado em DTU.

## <a name="migrate-databases-that-use-geo-replication"></a>Migrar bancos de dados que usam replicação geográfica

A migração do modelo baseado em DTU para o modelo de compra baseado em vCore é semelhante à atualização ou ao downgrade de relações de replicação geográfica entre bancos de dados nas camadas de serviço Standard e Premium. Durante a migração, você não precisa parar a replicação geográfica, mas deve seguir estas regras de sequenciamento:

- Ao atualizar, você deve atualizar o banco de dados secundário primeiro e, em seguida, atualizar o primário.
- Ao fazer downgrade, inverta o pedido: você deve fazer o downgrade primeiro do banco de dados primário e, em seguida, efetuar o downgrade do secundário.

Quando você estiver usando a replicação geográfica entre dois pools elásticos, recomendamos que você designe um pool como o primário e o outro como o secundário. Nesse caso, quando estiver migrando pools elásticos, você deverá usar as mesmas diretrizes de sequenciamento. No entanto, se você tiver pools elásticos que contêm bancos de dados primários e secundários, trate o pool com a utilização mais alta como a primária e siga as regras de sequenciamento adequadamente.  

A tabela a seguir fornece orientação para cenários de migração específicos:

|Camada de serviço atual|Camada de serviço de destino|Tipo de migração|Ações do usuário|
|---|---|---|---|
|Standard|Fins gerais|Lateral|Pode migrar em qualquer ordem, mas precisa garantir um dimensionamento adequado de vCore *|
|Premium|Crítico para a empresa|Lateral|Pode migrar em qualquer ordem, mas precisa garantir um dimensionamento adequado de vCore *|
|Standard|Crítico para a empresa|Atualizar|Deve migrar o secundário primeiro|
|Crítico para a empresa|Standard|Mudar para uma versão anterior|Deve migrar primeiro o primário|
|Premium|Fins gerais|Mudar para uma versão anterior|Deve migrar primeiro o primário|
|Fins gerais|Premium|Atualizar|Deve migrar o secundário primeiro|
|Crítico para a empresa|Fins gerais|Mudar para uma versão anterior|Deve migrar primeiro o primário|
|Fins gerais|Crítico para a empresa|Atualizar|Deve migrar o secundário primeiro|
||||

\* a cada 100 DTUs na camada Standard exigem pelo menos 1 vCore e cada 125 DTUs na camada Premium requer pelo menos 1 vCore.

## <a name="migrate-failover-groups"></a>Migrar grupos de failover

A migração de grupos de failover com vários bancos de dados requer a migração individual dos bancos de dados primários e secundários. Durante esse processo, as mesmas considerações e regras de sequenciamento se aplicam. Depois que os bancos de dados são convertidos para o modelo de compra baseado em vCore, o grupo de failover permanecerá em vigor com as mesmas configurações de política.

### <a name="create-a-geo-replication-secondary-database"></a>Criar um banco de dados secundário de replicação geográfica

Você pode criar um banco de dados secundário de replicação geográfica (um secundário geográfico) somente usando a mesma camada de serviço usada para o banco de dados primário. Para bancos de dados com uma alta taxa de geração de logs, é recomendável criar o secundário geográfico com o mesmo tamanho de computação que o primário.

Se você estiver criando um secundário geográfico no pool elástico para um único banco de dados primário, verifique se a configuração `maxVCore` para o pool corresponde ao tamanho de computação do banco de dados primário. Se você estiver criando um secundário geográfico para um primário em outro pool elástico, recomendamos que os pools tenham as mesmas configurações `maxVCore`.

## <a name="use-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>Use a cópia do banco de dados para converter um banco de dados baseado em DTU em um banco de dados baseado em vCore

Você pode copiar qualquer banco de dados com um tamanho de computação baseado em DTU para um banco de dados com um tamanho de computação baseado em vCore sem restrições ou sequenciamento especial, contanto que o tamanho de computação de destino dê suporte ao tamanho máximo do banco de dados de origem. A cópia de banco de dados cria um instantâneo dos dados a partir da hora de início da operação de cópia e não sincroniza os dados entre a origem e o destino.

## <a name="next-steps"></a>Passos seguintes

- Para obter os tamanhos de computação específicos e as opções de tamanho de armazenamento disponíveis para bancos de dados individuais, consulte [limites de recursos baseados em vCore do banco de dados SQL para bancos de dados individuais](sql-database-vcore-resource-limits-single-databases.md).
- Para obter os tamanhos de computação específicos e as opções de tamanho de armazenamento disponíveis para pools elásticos, consulte [limites de recursos baseados em vCore do banco de dados SQL para pools elásticos](sql-database-vcore-resource-limits-elastic-pools.md)

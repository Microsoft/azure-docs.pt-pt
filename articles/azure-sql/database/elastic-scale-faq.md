---
title: FAQ de escala elástica
description: Perguntas frequentes sobre a balança elástica da base de dados Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 51e15a8dc5e9f918c630397d6d6593f5bf561755
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92786909"
---
# <a name="elastic-database-tools-frequently-asked-questions-faq"></a>Ferramentas de base de dados elásticas frequentemente feitas perguntas (FAQ)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

## <a name="if-i-have-a-single-tenant-per-shard-and-no-sharding-key-how-do-i-populate-the-sharding-key-for-the-schema-info"></a>Se eu tenho um único inquilino por fragmento e sem chave de caco, como povoar a chave de caco para a informação de esquema

O objeto de informação de esquema é usado apenas para dividir cenários de fusão. Se uma aplicação é inerentemente um único inquilino, então não requer a ferramenta Split Merge e, portanto, não há necessidade de preencher o objeto de informação de esquema.

## <a name="ive-provisioned-a-database-and-i-already-have-a-shard-map-manager-how-do-i-register-this-new-database-as-a-shard"></a>Eu provisei uma base de dados e já tenho um Shard Map Manager, como posso registar esta nova base de dados como um fragmento

Consulte [adicionar um fragmento a uma aplicação utilizando a biblioteca de clientes de base de dados elástica](elastic-scale-add-a-shard.md).

## <a name="how-much-do-elastic-database-tools-cost"></a>Quanto custam as ferramentas elásticas de base de dados

A utilização da biblioteca de clientes de base de dados elástica não incorre em quaisquer custos. Os custos acumulam-se apenas para as bases de dados na Base de Dados Azure SQL que utiliza para fragmentos e para o Shard Map Manager, bem como para as funções web/trabalhador que fornece para a ferramenta Split Merge.

## <a name="why-are-my-credentials-not-working-when-i-add-a-shard-from-a-different-server"></a>Porque é que as minhas credenciais não funcionam quando adiciono um fragmento de um servidor diferente

Não utilize credenciais sob a forma de "User ID=", username@servername em vez de utilizar simplesmente "User ID = username".  Além disso, certifique-se de que o login de "username" tem permissões no shard.

## <a name="do-i-need-to-create-a-shard-map-manager-and-populate-shards-every-time-i-start-my-applications"></a>Preciso de criar um Shard Map Manager e povoar fragmentos sempre que começo as minhas aplicações

Não — a criação do Shard Map Manager (por exemplo, [ShardMapManagerFactory.CreateSqlShardMapManager](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager)) é uma operação única.  A sua aplicação deve utilizar a chamada [ShardMapManagerFactory.TryGetSqlShardMapManager()](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager) na hora de arranque da aplicação.  Deve haver apenas uma chamada por domínio de aplicação.

## <a name="i-have-questions-about-using-elastic-database-tools-how-do-i-get-them-answered"></a>Tenho perguntas sobre usar ferramentas elásticas de base de dados, como é que as respondo

Contacte-nos na [página de perguntas do Microsoft Q&Uma página de perguntas para a Base de Dados SQL](/answers/topics/azure-sql-database.html).

## <a name="when-i-get-a-database-connection-using-a-sharding-key-i-can-still-query-data-for-other-sharding-keys-on-the-same-shard--is-this-by-design"></a>Quando tenho uma ligação de base de dados usando uma chave de caco, ainda posso consultar dados para outras chaves de caco no mesmo fragmento.  Isto é por design

As APIs de Escala Elástica dão-lhe uma ligação à base de dados correta para a sua chave de cacimento, mas não fornecem a filtragem da chave de fragmentos.  Adicione cláusulas **WHERE** à sua consulta para restringir o âmbito à chave de fragmentos fornecida, se necessário.

## <a name="can-i-use-a-different-sql-database-edition-for-each-shard-in-my-shard-set"></a>Posso usar uma edição de base de dados SQL diferente para cada fragmento no meu conjunto de fragmentos

Sim, um fragmento é uma base de dados individual, e assim um fragmento pode ser uma edição Premium enquanto outro ser uma edição Standard. Além disso, a edição de um fragmento pode escalar para cima ou para baixo várias vezes durante a vida do fragmento.

## <a name="does-the-split-merge-tool-provision-or-delete-a-database-during-a-split-or-merge-operation"></a>A ferramenta Split Merge (ou apagar) uma base de dados durante uma operação de divisão ou fusão

Não. Para operações **divididas,** a base de dados-alvo deve existir com o esquema apropriado e ser registada no Shard Map Manager.  Para operações **de fusão,** deve eliminar o fragmento do gestor do mapa de fragmentos e, em seguida, eliminar a base de dados.

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]
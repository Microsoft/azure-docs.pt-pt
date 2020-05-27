---
title: FAQ de escala elástica
description: Perguntas frequentes sobre a escala elástica azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: b83dbdb928b7b5810864a9202134dee30f9582df
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83834255"
---
# <a name="elastic-database-tools-frequently-asked-questions-faq"></a>Ferramentas de base de dados elásticas frequentemente colocadas (FAQ)

## <a name="if-i-have-a-single-tenant-per-shard-and-no-sharding-key-how-do-i-populate-the-sharding-key-for-the-schema-info"></a>Se eu tenho um único inquilino por fragmento e nenhuma chave sharding, como eu povoo a chave sharding para a informação de esquemas

O objeto de informação de esquema sinuoso só é usado para dividir cenários de fusão. Se uma aplicação é inerentemente um inquilino único, então não requer a ferramenta Split Merge e, portanto, não há necessidade de povoar o objeto de informação schema.

## <a name="ive-provisioned-a-database-and-i-already-have-a-shard-map-manager-how-do-i-register-this-new-database-as-a-shard"></a>Formei uma base de dados e já tenho um Shard Map Manager, como posso registar esta nova base de dados como um fragmento

Consulte [Adicionar um fragmento a uma aplicação utilizando a biblioteca de clientes de base](sql-database-elastic-scale-add-a-shard.md)de dados elástica .

## <a name="how-much-do-elastic-database-tools-cost"></a>Quanto custam as ferramentas de base de dados elásticas

A utilização da biblioteca de clientes de base de dados elástica não incorre em quaisquer custos. Os custos acumulam-se apenas para as bases de dados Azure SQL que utiliza para fragmentos e para o Shard Map Manager, bem como para as funções web/trabalhador que você provisão para a ferramenta Split Merge.

## <a name="why-are-my-credentials-not-working-when-i-add-a-shard-from-a-different-server"></a>Porque é que as minhas credenciais não funcionam quando adiciono um fragmento de um servidor diferente?

Não utilize credenciais sob a forma de "ID do utilizador= username@servername ", em vez de utilizar simplesmente "ID do utilizador = nome de utilizador".  Além disso, certifique-se de que o login "username" tem permissões no fragmento.

## <a name="do-i-need-to-create-a-shard-map-manager-and-populate-shards-every-time-i-start-my-applications"></a>Preciso criar um Gestor de Mapas e preencher fragmentos cada vez que começo as minhas aplicações

Não — a criação do Shard Map Manager (por exemplo, [ShardMapManagerFactory.CreateSqlShardMapManager)](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager)é uma operação única.  A sua aplicação deve utilizar a chamada [ShardMapManagerFactory.TryGetSqlShardMapManager()](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager) no momento do arranque da aplicação.  Só deve haver uma chamada por domínio de aplicação.

## <a name="i-have-questions-about-using-elastic-database-tools-how-do-i-get-them-answered"></a>Tenho perguntas sobre usar ferramentas de base de dados elásticas, como as consigo responder

Contacte-nos no Microsoft Q&Uma página de perguntas para a Base de [Dados SQL](https://docs.microsoft.com/answers/topics/azure-sql-database.html).

## <a name="when-i-get-a-database-connection-using-a-sharding-key-i-can-still-query-data-for-other-sharding-keys-on-the-same-shard--is-this-by-design"></a>Quando tenho uma ligação de base de dados usando uma chave de sharding, ainda posso consultar dados para outras chaves no mesmo fragmento.  É isto por design

As APIs de escala elástica proporcionam-lhe uma ligação à base de dados correta para a sua chave sharding, mas não fornece filtragem da chave de sharding.  Adicione as cláusulas **DE ONDE** à sua consulta para restringir o âmbito à chave de sharding fornecida, se necessário.

## <a name="can-i-use-a-different-sql-database-edition-for-each-shard-in-my-shard-set"></a>Posso usar uma edição de Base de Dados SQL diferente para cada fragmento no meu conjunto de fragmentos

Sim, um fragmento é uma base de dados individual, e assim um fragmento poderia ser uma edição Premium enquanto outro seria uma edição Standard. Além disso, a edição de um fragmento pode escalar para cima ou para baixo várias vezes durante a vida do fragmento.

## <a name="does-the-split-merge-tool-provision-or-delete-a-database-during-a-split-or-merge-operation"></a>A provisão da ferramenta Split Merge (ou apagar) uma base de dados durante uma operação de divisão ou fusão

Não. Para operações **divididas,** a base de dados-alvo deve existir com o esquema apropriado e ser registada no Shard Map Manager.  Para operações de **fusão,** deve eliminar o fragmento do gestor de mapas de fragmentos e, em seguida, apagar a base de dados.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
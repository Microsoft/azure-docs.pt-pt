---
title: Dimensionamento elástico de SQL do Azure FAQ | Documentos da Microsoft
description: Perguntas mais frequentes sobre o dimensionamento elástico de base de dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: b5ba5fadd229fa7119f9af791f7eaedbc984c92a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60584952"
---
# <a name="elastic-database-tools-frequently-asked-questions-faq"></a>Ferramentas de bases de dados elásticas perguntas mais frequentes (FAQ)

## <a name="if-i-have-a-single-tenant-per-shard-and-no-sharding-key-how-do-i-populate-the-sharding-key-for-the-schema-info"></a>Se eu tiver um inquilino individual por partição horizontal e nenhuma chave de fragmentação, como posso preencher a chave de fragmentação para as informações de esquema

O objeto de informações de esquema só é utilizado para dividir os cenários de intercalação. Se um aplicativo for inerentemente de inquilino único, em seguida, não requer a ferramenta de intercalação de divisão e, portanto, não é necessário para preencher o objeto de informações de esquema.

## <a name="ive-provisioned-a-database-and-i-already-have-a-shard-map-manager-how-do-i-register-this-new-database-as-a-shard"></a>Posso ter aprovisionado um banco de dados e já tenho um Gestor de mapas de partições horizontais, como Registro esta nova base de dados como uma partição horizontal

Veja [adicionando uma partição horizontal a um aplicativo usando a biblioteca de clientes de bases de dados elásticas](sql-database-elastic-scale-add-a-shard.md).

## <a name="how-much-do-elastic-database-tools-cost"></a>Quanto o custo de ferramentas de bases de dados elásticas

Usando a biblioteca de cliente da base de dados elástica não incorre em quaisquer custos. Os custos acumulam-se apenas para as bases de dados SQL do Azure que utilizar para as partições horizontais e o Gestor de mapas de partições horizontais, bem como as funções da web/trabalho que aprovisionar para a ferramenta de intercalação de divisão.

## <a name="why-are-my-credentials-not-working-when-i-add-a-shard-from-a-different-server"></a>Por que as minhas credenciais não estão a funcionar quando adiciono uma partição horizontal a partir de um servidor diferente

Não utilizar credenciais na forma de "ID de utilizador =username@servername", em vez disso, simplesmente use "ID de utilizador = o nome de utilizador".  Além disso, certifique-se de que o início de sessão "nomedeutilizador" tem permissões sobre a partição horizontal.

## <a name="do-i-need-to-create-a-shard-map-manager-and-populate-shards-every-time-i-start-my-applications"></a>É necessário criar um Gestor de mapas de partições horizontais e preencher as partições horizontais, sempre começo as minhas aplicações

Não, a criação de Gestor de mapas de partições horizontais (por exemplo, [ShardMapManagerFactory.CreateSqlShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager)) é uma operação única.  Seu aplicativo deve usar a chamada [ShardMapManagerFactory.TryGetSqlShardMapManager()](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager) em tempo de inicialização do aplicativo.  Deve existir apenas uma tal chamada por domínio de aplicativo.

## <a name="i-have-questions-about-using-elastic-database-tools-how-do-i-get-them-answered"></a>Tenho dúvidas sobre como utilizar ferramentas de bases de dados elásticas, como posso obter respostas para

Contacte-nos sobre o [fórum de base de dados SQL](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted).

## <a name="when-i-get-a-database-connection-using-a-sharding-key-i-can-still-query-data-for-other-sharding-keys-on-the-same-shard--is-this-by-design"></a>Quando receber uma ligação de base de dados com uma chave de fragmentação, eu ainda pode consultar os dados para outras chaves de fragmentação na mesma partição horizontal.  Isso é por design

As APIs de dimensionamento elástico dão-lhe uma ligação à base de dados correto para a sua chave de fragmentação, mas não fornece filtragem de chave de fragmentação.  Adicione **onde** cláusulas para sua consulta para restringir o âmbito para a chave de fragmentação fornecido, se necessário.

## <a name="can-i-use-a-different-sql-database-edition-for-each-shard-in-my-shard-set"></a>Posso utilizar uma edição de base de dados SQL diferente para cada partição horizontal em meu conjunto de partições horizontais

Sim, uma partição horizontal é uma base de dados individual e, portanto, uma partição pode ser uma edição Premium enquanto outro ser uma edição Standard. Além disso, a edição de uma partição horizontal pode aumentar ou reduzir verticalmente várias vezes durante a duração de partição horizontal.

## <a name="does-the-split-merge-tool-provision-or-delete-a-database-during-a-split-or-merge-operation"></a>Não a provisão de ferramenta de intercalação de divisão (ou elimina) uma base de dados durante uma operação de intercalação ou divisão

Não. Para **dividir** operações, a base de dados de destino tem de existir com o esquema apropriado e ser registado com o Gestor de mapas de partições horizontais.  Para **intercalação** operações, tem de eliminar a partição horizontal a partir do Gestor de mapas de partições horizontais e, em seguida, eliminar a base de dados.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
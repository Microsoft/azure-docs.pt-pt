---
title: Perguntas frequentes sobre escala elástica do SQL do Azure
description: Perguntas frequentes sobre a escala elástica do banco de dados SQL do Azure.
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
ms.openlocfilehash: 8ef99d6d752f1e9474fccea6c00b51b61ae1304b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73690270"
---
# <a name="elastic-database-tools-frequently-asked-questions-faq"></a>Perguntas frequentes sobre ferramentas de banco de dados elástico

## <a name="if-i-have-a-single-tenant-per-shard-and-no-sharding-key-how-do-i-populate-the-sharding-key-for-the-schema-info"></a>Se eu tiver um único locatário por fragmento e nenhuma chave de fragmentação, como preencher a chave de fragmentação para as informações de esquema

O objeto de informações de esquema só é usado para dividir cenários de mesclagem. Se um aplicativo for inerentemente de locatário único, ele não precisará da ferramenta de mesclagem de divisão e, portanto, não há necessidade de preencher o objeto de informações de esquema.

## <a name="ive-provisioned-a-database-and-i-already-have-a-shard-map-manager-how-do-i-register-this-new-database-as-a-shard"></a>Eu provisionei um banco de dados e já tenho um Gerenciador de mapa de fragmentos, como registrar esse novo banco de dados como um fragmento

Consulte [adicionando um fragmento a um aplicativo usando a biblioteca de cliente do banco de dados elástico](sql-database-elastic-scale-add-a-shard.md).

## <a name="how-much-do-elastic-database-tools-cost"></a>Quanto custam as ferramentas de banco de dados elástico

Usar a biblioteca de cliente do banco de dados elástico não incorre em custos. Os custos se acumulam somente para os bancos de dados SQL do Azure que você usa para fragmentos e o Gerenciador de mapa de fragmentos, bem como as funções Web/de trabalho que você provisiona para a ferramenta de mesclagem de divisão.

## <a name="why-are-my-credentials-not-working-when-i-add-a-shard-from-a-different-server"></a>Por que minhas credenciais não estão funcionando quando adiciono um fragmento de um servidor diferente

Não use credenciais na forma de "User ID =username@servername", em vez disso, basta usar "User ID = username".  Além disso, certifique-se de que o logon "username" tenha permissões no fragmento.

## <a name="do-i-need-to-create-a-shard-map-manager-and-populate-shards-every-time-i-start-my-applications"></a>Preciso criar um Gerenciador de mapa de fragmentos e preencher os fragmentos toda vez que eu iniciar meus aplicativos

Não — a criação do Gerenciador de mapa de fragmentos (por exemplo, [ShardMapManagerFactory. CreateSqlShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager)) é uma operação única.  Seu aplicativo deve usar a chamada [ShardMapManagerFactory. TryGetSqlShardMapManager ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager) na hora de início do aplicativo.  Deve haver apenas uma única chamada por domínio de aplicativo.

## <a name="i-have-questions-about-using-elastic-database-tools-how-do-i-get-them-answered"></a>Tenho dúvidas sobre o uso de ferramentas de banco de dados elástico, como posso obtê-las respondidas

Entre em contato conosco no fórum do [banco de dados SQL](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted).

## <a name="when-i-get-a-database-connection-using-a-sharding-key-i-can-still-query-data-for-other-sharding-keys-on-the-same-shard--is-this-by-design"></a>Quando obtenho uma conexão de banco de dados usando uma chave de fragmentação, eu ainda posso fazer consultas para outras chaves de fragmentação no mesmo fragmento.  É isso por design

As APIs de escala elástica fornecem uma conexão com o banco de dados correto para sua chave de fragmentação, mas não fornecem filtragem de chave de fragmentação.  Adicione cláusulas **Where** à sua consulta para restringir o escopo à chave de fragmentação fornecida, se necessário.

## <a name="can-i-use-a-different-sql-database-edition-for-each-shard-in-my-shard-set"></a>Posso usar uma edição de banco de dados SQL diferente para cada fragmento em meu conjunto de fragmentos

Sim, um fragmento é um banco de dados individual e, portanto, um fragmento pode ser uma edição Premium, enquanto outro é uma edição padrão. Além disso, a edição de um fragmento pode escalar ou reduzir verticalmente várias vezes durante o tempo de vida do fragmento.

## <a name="does-the-split-merge-tool-provision-or-delete-a-database-during-a-split-or-merge-operation"></a>A ferramenta de mesclagem de divisão provisiona (ou exclui) um banco de dados durante uma operação de divisão ou mesclagem

Não. Para operações de **divisão** , o banco de dados de destino deve existir com o esquema apropriado e ser registrado com o Gerenciador de mapa de fragmentos.  Para operações de **mesclagem** , você deve excluir o fragmento do Gerenciador de mapa de fragmentos e, em seguida, excluir o banco de dados.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
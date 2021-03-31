---
title: Resolução de problemas uma mensagem "Pedido de cabeçalho demasiado grande" ou 400 mau pedido no Azure Cosmos DB
description: Saiba como diagnosticar e corrigir o cabeçalho do pedido uma exceção demasiado grande.
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: a14503bc56777563b6360143efaa86a136b22278
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93340524"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-header-too-large-message"></a>Diagnosticar e resolver problemas Azure Cosmos DB "Pedir cabeçalho demasiado grande"
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

A mensagem "Pedido demasiado grande" é lançada com um código de erro HTTP 400. Este erro ocorre se o tamanho do cabeçalho de pedido cresceu tão grande que excede o tamanho máximo permitido. Recomendamos que utilize a versão mais recente do SDK. Utilize pelo menos a versão 3.x ou 2.x, porque estas versões adicionam o tamanho do cabeçalho à mensagem de exceção.

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas
A mensagem "Pedido de cabeçalho demasiado grande" ocorre se a sessão ou o token de continuação for demasiado grande. As seguintes secções descrevem a causa do problema e a sua solução em cada categoria.

### <a name="session-token-is-too-large"></a>O token da sessão é muito grande

#### <a name="cause"></a>Causa:
Um pedido de 400 maus pedidos provavelmente ocorre porque o token da sessão é muito grande. Se as seguintes declarações forem verdadeiras, o token da sessão é demasiado grande:

* O erro ocorre em operações pontuais como criar, ler e atualizar onde não há um sinal de continuação.
* A exceção começou sem fazer quaisquer alterações à aplicação. O token da sessão aumenta à medida que o número de divisórias aumenta no recipiente. O número de divisórias aumenta à medida que a quantidade de dados aumenta ou se a produção é aumentada.

#### <a name="temporary-mitigation"></a>Atenuação temporária: 
Reinicie a aplicação do seu cliente para redefinir todos os tokens da sessão. Eventualmente, o token da sessão vai voltar ao tamanho anterior que causou o problema. Para evitar completamente este problema, utilize a solução na secção seguinte.

#### <a name="solution"></a>Solução:
1. Siga as orientações nos artigos de dicas de desempenho [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) ou [.NET v2.](performance-tips.md) Converta a aplicação para utilizar o modo de ligação direta com o Protocolo de Controlo de Transmissão (TCP). O modo de ligação direta com o protocolo TCP não tem a restrição de tamanho do cabeçalho como o protocolo HTTP, evitando este problema. Certifique-se de utilizar a versão mais recente do SDK, que tem uma correção para operações de consulta quando o interop de serviço não está disponível.
1. Se o modo de ligação direta com o protocolo TCP não for uma opção para a sua carga de trabalho, atenue-a alterando o [nível de consistência](how-to-manage-consistency.md)do cliente . O token da sessão é usado apenas para a consistência da sessão, que é o nível de consistência padrão para Azure Cosmos DB. Outros níveis de consistência não usam o token da sessão.

### <a name="continuation-token-is-too-large"></a>O token de continuação é muito grande

#### <a name="cause"></a>Causa:
O pedido de 400 maus pedidos ocorre em operações de consulta onde o token de continuação é usado se o token de continuação cresceu muito grande ou se diferentes consultas têm diferentes tamanhos de token de continuação.
    
#### <a name="solution"></a>Solução:
1. Siga as orientações nos artigos de dicas de desempenho [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) ou [.NET v2.](performance-tips.md) Converta a aplicação para utilizar o modo de ligação direta com o protocolo TCP. O modo de ligação direta com o protocolo TCP não tem a restrição de tamanho do cabeçalho como o protocolo HTTP, evitando este problema. 
1. Se o modo de ligação direta com o protocolo TCP não for uma opção para a sua carga de trabalho, desa um ajuste a `ResponseContinuationTokenLimitInKb` opção. Pode encontrar esta opção `FeedOptions` em V2 ou `QueryRequestOptions` em V3.

## <a name="next-steps"></a>Passos seguintes
* [Diagnosticar e resolver problemas](troubleshoot-dot-net-sdk.md) quando utilizar o Azure Cosmos DB .NET SDK.
* Saiba mais sobre as diretrizes de desempenho para [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) e [.NET v2](performance-tips.md).

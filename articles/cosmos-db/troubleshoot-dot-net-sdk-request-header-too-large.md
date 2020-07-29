---
title: Cabeçalho de pedido de resolução de problemas demasiado grande ou 400 mau pedido em Azure Cosmos DB
description: Como diagnosticar e corrigir o cabeçalho de pedido demasiado grande exceção
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 942af52c43a1d4526a42f20df40abf3f510e0ee6
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294669"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-header-too-large-message"></a>Diagnosticar e resolver problemas Azure Cosmos DB pedir cabeçalho demasiado grande
O cabeçalho do pedido é lançado com um código de erro HTTP 400. Este erro ocorre se o tamanho do cabeçalho de pedido tiver crescido demasiado grande e exceder o tamanho máximo permitido. Recomendamos que utilize a versão mais recente do SDK. Certifique-se de que utiliza pelo menos a versão 3.x ou 2.x, porque estas versões adicionam o tamanho do cabeçalho à mensagem de exceção.

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas
O cabeçalho do pedido ocorre se a sessão ou o token de continuação for muito grande. As seguintes secções descrevem a causa e a solução para o problema em cada categoria.

### <a name="1-session-token-too-large"></a>1. Ficha de sessão demasiado grande

#### <a name="cause"></a>Causa:
400 maus pedidos são provavelmente causados pelo token da sessão ser grande. Se as seguintes declarações forem verdadeiras, então confirma que o token da sessão é demasiado grande.

* O erro ocorre no ponto de funcionamento como criar, ler, atualizar e etc. quando não há um sinal de continuação.
* A exceção começou sem fazer quaisquer alterações à aplicação. O token da sessão aumenta à medida que o número de divisórias aumenta no contentor. O número de divisórias aumenta à medida que a quantidade de dados aumenta ou se a produção é aumentada.

#### <a name="temporary-mitigation"></a>Atenuação temporária: 
Reinicie a aplicação do seu cliente para redefinir todos os tokens da sessão. O token da sessão acabará por voltar ao tamanho anterior que causou o problema. Utilize a solução na secção seguinte para evitar completamente este problema.

#### <a name="solution"></a>Solução:
1. Siga as orientações em artigo de dicas de desempenho [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) ou [.NET V2](performance-tips.md) e converta a aplicação para utilizar o modo de ligação direta com o protocolo TCP. O modo direto com o protocolo TCP não tem a restrição de tamanho do cabeçalho como o protocolo HTTP, pelo que evita este problema. Certifique-se de que utiliza a versão mais recente da SDK, que tem uma correção para operações de consulta quando o interop de serviço não está disponível.
2. Se o modo de ligação direta com o protocolo TCP não for uma opção para a sua carga de trabalho, atenue-a alterando o [nível de consistência](how-to-manage-consistency.md)do cliente . O token da sessão é usado apenas para a consistência da sessão, que é o nível de consistência padrão para Azure Cosmos DB. Outros níveis de consistência não usam o token da sessão.

### <a name="2-continuation-token-too-large"></a>2. Símbolo de continuação demasiado grande

#### <a name="cause"></a>Causa:
O mau pedido de 400 está a acontecer nas operações de consulta onde é usado o sinal de continuação. Se o token de continuação cresceu muito grande ou se diferentes consultas têm diferentes tamanhos de token de continuação.
    
#### <a name="solution"></a>Solução:
1. Siga as orientações em artigo de dicas de desempenho [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) ou [.NET V2](performance-tips.md) e converta a aplicação para utilizar o modo de ligação direta com o protocolo TCP. O modo direto com o protocolo TCP não tem a restrição de tamanho do cabeçalho como o protocolo HTTP, pelo que evita este problema. 
3. Se o modo de ligação direta com o protocolo TCP não for uma opção para a sua carga de trabalho, tente definir a `ResponseContinuationTokenLimitInKb` opção. Pode encontrar esta opção no `FeedOptions` para v2 ou `QueryRequestOptions` no in v3.

## <a name="next-steps"></a>Passos seguintes
* [Diagnosticar e resolver problemas](troubleshoot-dot-net-sdk.md) ao utilizar a Azure Cosmos DB .NET SDK
* Conheça as diretrizes de desempenho para [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) e [.NET V2](performance-tips.md)

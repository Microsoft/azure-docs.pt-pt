---
title: Resolução de problemas Azure Cosmos DB exceções não autorizadas
description: Saiba como diagnosticar e corrigir exceções não autorizadas.
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: aa9bf1fd706ccf6064893f1141be5e5b2f185ff3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94411172"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-unauthorized-exceptions"></a>Diagnosticar e resolver problemas Azure Cosmos DB exceções não autorizadas
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

HTTP 401: A assinatura MAC encontrada no pedido HTTP não é a mesma que a assinatura computada.
Se recebeu a mensagem de erro 401 "A assinatura MAC encontrada no pedido HTTP não é a mesma que a assinatura computada", pode ser causada pelos seguintes cenários.

Para os SDKs mais antigos, a exceção pode aparecer como uma exceção JSON inválida em vez da exceção não autorizada correta 401. Os SDKs mais recentes lidam corretamente com este cenário e dão uma mensagem de erro válida.

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas
A lista que se segue contém causas e soluções conhecidas para exceções não autorizadas.

### <a name="the-key-wasnt-properly-rotated-is-the-most-common-scenario"></a>A chave não foi devidamente girada é o cenário mais comum
A assinatura 401 MAC é vista pouco depois de uma rotação da chave e acaba por parar sem alterações. 

#### <a name="solution"></a>Solução:
A chave foi girada e não seguiu as [melhores práticas.](secure-access-to-data.md#key-rotation) A rotação da chave da conta DB Azure Cosmos pode demorar entre alguns segundos e possivelmente dias, dependendo do tamanho da conta DB do Azure Cosmos.

### <a name="the-key-is-misconfigured"></a>A chave está mal configurada 
A emissão de assinaturas 401 MAC será consistente e acontece para todas as chamadas que usam essa chave.

#### <a name="solution"></a>Solução:
A chave está mal configurada na aplicação e está a usar a chave errada para a conta, ou a chave inteira não foi copiada.

### <a name="the-application-is-using-the-read-only-keys-for-write-operations"></a>A aplicação está a usar as chaves apenas de leitura para operações de escrita
A emissão de assinatura 401 MAC ocorre apenas para operações de escrita como criar ou substituir, mas os pedidos de leitura são bem sucedidos.

#### <a name="solution"></a>Solução:
Mude a aplicação para utilizar uma chave de leitura/escrita para permitir que as operações terminem com sucesso.

### <a name="race-condition-with-create-container"></a>Condição de corrida com criar recipiente
A emissão de assinaturas 401 MAC é vista pouco depois de uma criação de contentores. Esta questão só ocorre até que a criação do contentor esteja concluída.

#### <a name="solution"></a>Solução:
Há uma condição de corrida com criação de contentores. Uma instância de aplicação está a tentar aceder ao contentor antes da criação do contentor estar completa. O cenário mais comum para esta condição de regata é se a aplicação estiver em execução e o recipiente for eliminado e recriado com o mesmo nome. O SDK tenta usar o novo recipiente, mas a criação do contentor ainda está em andamento para não ter as chaves.

## <a name="next-steps"></a>Passos seguintes
* [Diagnosticar e resolver problemas](troubleshoot-dot-net-sdk.md) quando utilizar o Azure Cosmos DB .NET SDK.
* Saiba mais sobre as diretrizes de desempenho para [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) e [.NET v2](performance-tips.md).
* [Diagnosticar e resolver problemas](troubleshoot-java-sdk-v4-sql.md) quando utilizar o Azure Cosmos DB Java v4 SDK.
* Saiba mais sobre as diretrizes de desempenho para [Java v4 SDK](performance-tips-java-sdk-v4-sql.md).
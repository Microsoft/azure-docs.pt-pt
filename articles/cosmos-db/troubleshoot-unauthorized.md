---
title: Resolução de problemas Azure Cosmos DB exceção não autorizada
description: Como diagnosticar e corrigir exceções não autorizadas
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 872780ff65dabe5a931ae712c0f99b6e4f3e726d
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294515"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-unauthorized-exception"></a>Diagnosticar e resolver problemas A azure Cosmos DB exceção não autorizada

HTTP 401: A assinatura MAC encontrada no pedido HTTP não é a mesma que a assinatura computada.
Se recebeu a seguinte mensagem de erro 401: “A assinatura MAC encontrada no pedido HTTP não é igual à assinatura calculada.” pode ser causado pelos seguintes cenários.

SDKs mais antigos a exceção pode aparecer como uma exceção inválida json em vez da exceção correta 401 não autorizada. Os SDKs mais recentes lidam corretamente com este cenário e dão uma mensagem de erro válida.

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas
A lista que se segue contém causas e soluções conhecidas para exceção não autorizada.

### <a name="1-key-was-not-properly-rotated-is-the-most-common-scenario"></a>1. A chave não foi corretamente rodada é o cenário mais comum.
A assinatura MAC 401 é vista pouco depois de uma rotação da chave e acaba por parar sem alterações. 

#### <a name="solution"></a>Solução:
A chave foi rodada e não seguiu as [melhores práticas](secure-access-to-data.md#key-rotation). A rotação da chave da conta Cosmos DB pode demorar entre alguns segundos e possivelmente dias, dependendo do tamanho da conta Dessíts Cosmos.

### <a name="2-the-key-is-misconfigured"></a>2. A chave está mal configurada 
401 Emissão de assinatura MAC será consistente e acontece para todas as chamadas usando esta chave

#### <a name="solution"></a>Solução:
A chave está mal configurada na aplicação, e está a usar a chave errada para a conta ou a chave inteira não foi copiada.

### <a name="3-the-application-is-using-the-read-only-keys-for-write-operations"></a>3. A aplicação está a utilizar as chaves apenas de leitura para operações de escrita
401 A emissão de assinatura MAC ocorre apenas para operações de escrita como criar ou substituir, mas o pedido de leitura tem sucesso.

#### <a name="solution"></a>Solução:
Mude a aplicação para utilizar uma chave de leitura/escrita para permitir que as operações terminem com sucesso.

### <a name="4-race-condition-with-create-container"></a>4. Condição de corrida com criar recipiente
401 A emissão de assinatura MAC é vista pouco depois da criação de um contentor. Isto só ocorre até que a criação do recipiente esteja concluída.

#### <a name="solution"></a>Solução:
Existe uma condição race com a criação do contentor. Uma instância da aplicação está a tentar aceder ao contentor antes de a criação do mesmo estar concluída. O cenário mais comum para esta condição de corrida é se a aplicação estiver em execução, e o recipiente for eliminado e recriado com o mesmo nome. O SDK tentará utilizar o novo contentor, mas a criação do contentor ainda está em curso, pelo que não tem as chaves.

## <a name="next-steps"></a>Passos seguintes
* [Diagnosticar e resolver problemas](troubleshoot-dot-net-sdk.md) ao utilizar a Azure Cosmos DB .NET SDK
* Conheça as diretrizes de desempenho para [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) e [.NET V2](performance-tips.md)
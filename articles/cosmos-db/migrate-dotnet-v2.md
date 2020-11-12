---
title: Migrar a sua aplicação para utilizar o Azure Cosmos DB .NET SDK 2.0 (Microsoft.Azure.Cosmos)
description: Saiba como atualizar a sua aplicação .NET existente a partir do V1 SDK para .NET SDK v2 para Core (SQL) API.
author: stefArroyo
ms.author: esarroyo
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/15/2020
ms.openlocfilehash: 88c40452da72ed4ab43d2d7613636136a5b78cfe
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94550088"
---
# <a name="migrate-your-application-to-use-the-azure-cosmos-db-net-sdk-v2"></a>Migrar a sua aplicação para utilizar o Azure Cosmos DB .NET SDK v2

> [!IMPORTANT]
> É importante notar que o v3 do .NET SDK está atualmente disponível e um plano de migração de V2 a V3 está disponível [aqui](migrate-dotnet-v3.md). Para saber mais sobre o Azure Cosmos DB .NET SDK v2, consulte as [notas de lançamento](sql-api-sdk-dotnet.md), o [repositório .NET GitHub](https://github.com/Azure/azure-cosmos-dotnet-v2), .NET SDK v2 [Performance Tips](performance-tips.md), e o guia [de resolução de problemas](troubleshoot-dot-net-sdk.md).
>

Este artigo destaca algumas das considerações para atualizar a sua aplicação v1 .NET existente para Azure Cosmos DB .NET SDK v2 para Core (SQL) API. Azure Cosmos DB .NET SDK v2 corresponde ao `Microsoft.Azure.DocumentDB` espaço de nomes. Pode utilizar as informações fornecidas neste documento se estiver a migrar a sua aplicação a partir de qualquer uma das seguintes plataformas Azure Cosmos DB .NET para utilizar o v2 `Microsoft.Azure.Cosmos` SDK:

* Azure Cosmos DB .NET Framework v1 SDK para API SQL
* Azure Cosmos DB .NET Core SDK v1 para SQL API

## <a name="whats-available-in-the-net-v2-sdk"></a>O que está disponível no .NET v2 SDK

O v2 SDK contém muitas melhorias de usabilidade e desempenho, incluindo:

* Suporte para modo direto TCP para clientes não Windows
* Suporte de escrita multi-região
* Melhorias no desempenho da consulta
* Suporte para coleções geoespaciais/geometria e indexação
* Melhorias acrescidas nos diagnósticos de transporte direto/TCP
* Atualizações sobre a pilha de transportes TCP direta para reduzir o número de ligações estabelecidas
* Melhorias na redução da latência no RequestTimeout

A maior parte da lógica de retíria e os níveis mais baixos do SDK permanecem em grande parte inalterados.

## <a name="why-migrate-to-the-net-v2-sdk"></a>Por que migrar para o .NET v2 SDK

Além das inúmeras melhorias de desempenho, os novos investimentos de recurso realizados nos mais recentes SDK não serão devolvidos às versões mais antigas.

Além disso, os SDKs mais antigos serão substituídos por versões mais recentes e o V1 SDK entrará em [modo de manutenção](sql-api-sdk-dotnet.md). Para uma melhor experiência de desenvolvimento, recomendamos migrar a sua aplicação para uma versão posterior do SDK.

## <a name="major-changes-from-v1-sdk-to-v2-sdk"></a>Grandes alterações de v1 SDK para v2 SDK

### <a name="direct-mode--tcp"></a>Modo direto + TCP

O .NET v2 SDK suporta agora o modo direto e o modo gateway. O modo direto suporta a conectividade através do protocolo TCP e oferece um melhor desempenho, uma vez que se liga diretamente às réplicas de backend com menos lúpulo de rede.

Para mais detalhes, leia o guia de [modos de conectividade Azure Cosmos DL SDK](sql-sdk-connection-modes.md).

### <a name="session-token-formatting"></a>Formatação token de sessão

O v2 SDK já não usa o *formato simples* de símbolo de sessão como usado em v1, em vez disso o SDK usa formatação *vetorial.* O formato deve ser convertido ao passar para a aplicação do cliente com versões diferentes, uma vez que os formatos não são permutáveis.

Para obter mais informações, consulte [a conversão de formatos simbólicos de sessão no .NET SDK](how-to-convert-session-token.md).

### <a name="using-the-net-change-feed-processor-sdk"></a>Utilizando o processador de alimentação de alteração .NET SDK

A biblioteca do processador de alimentação de alteração .NET 2.1.x requer `Microsoft.Azure.DocumentDB` 2.0 ou mais tarde.

A biblioteca 2.1.x tem as seguintes alterações de teclas:

* Melhorias da estabilidade e da diagnosticabilidade
* Melhor tratamento dos erros e exceções
* Apoio adicional para coleções de arrendamento dividido
* Extensões avançadas para implementar a `ChangeFeedDocument` interface e classe para manipulação e rastreio de erros adicionais
* Suporte adicional para usar uma loja personalizada para persistir tokens de continuação por partição

Para obter mais informações, consulte as notas de [lançamento](sql-api-sdk-dotnet-changefeed.md)do processador de alteração do processador .

### <a name="using-the-bulk-executor-library"></a>Usando a biblioteca de executor a granel

A biblioteca executora a granel v2 tem atualmente uma dependência do Azure Cosmos DB .NET SDK 2.5.1 ou posterior.

Para obter mais informações, consulte a visão geral da biblioteca do [executor a granel Azure Cosmos](bulk-executor-overview.md) e as notas de [lançamento](sql-api-sdk-bulk-executor-dot-net.md)da biblioteca do executor a granel .NET .

## <a name="next-steps"></a>Passos seguintes

* Leia sobre [dicas de desempenho adicionais](sql-api-get-started.md) usando Azure Cosmos DB para SQL API v2 para otimizar a sua aplicação para alcançar o desempenho máximo
* Saiba mais sobre [o que pode fazer com o V2 SDK](sql-api-dotnet-samples.md)
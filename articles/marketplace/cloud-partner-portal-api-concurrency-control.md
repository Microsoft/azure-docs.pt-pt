---
title: Controlo de concurrency - Azure Marketplace
description: Estratégias de controlo da concurrency para o Portal do Parceiro cloud publicando APIs.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: e468898daffe8fc42250575d3efa42f99279c410
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88031653"
---
# <a name="concurrency-control"></a>Controlo de simultaneidade

> [!NOTE]
> As APIs do Portal do Parceiro Cloud estão integradas e continuarão a trabalhar no Partner Center. A transição introduz pequenas mudanças. Reveja as alterações listadas no [Cloud Partner Portal API Reference](./cloud-partner-portal-api-overview.md) para garantir que o seu código continua a funcionar após a transição para o Partner Center. As APIs de CPP só devem ser utilizadas para produtos já integrados antes da transição para o Partner Center; novos produtos devem utilizar APIs de submissão do Partner Center.

Cada chamada para o Portal de Web Partner publicando APIs deve especificar explicitamente qual a estratégia de controlo da concordância a utilizar. A não disponibilização do **cabeçalho Se-Match** resultará numa resposta de erro HTTP 400. Oferecemos duas estratégias para o controlo da concordância.

-   **Otimista** - O cliente que executa a atualização verifica se os dados mudaram desde a última leitura dos dados.
-   **O último ganha** - O cliente atualiza diretamente os dados, independentemente de outra aplicação os ter modificado desde a última vez que leu.

<a name="optimistic-concurrency-workflow"></a>Fluxo de trabalho de concordância otimista
-------------------------------

Recomendamos a utilização da estratégia de concordância otimista, com o seguinte fluxo de trabalho, para garantir que não sejam feitas edições inesperadas aos seus recursos.

1.  Recupere uma entidade utilizando as APIs. A resposta inclui um valor ETag que identifica a versão atualmente armazenada da entidade (no momento da resposta).
2.  No momento da atualização, inclua este mesmo valor ETag no cabeçalho obrigatório do pedido **If-Match.**
3.  A API compara o valor ETag recebido no pedido com o valor ETag atual da entidade numa transação atómica.
    *   Se os valores ETag forem diferentes, a API devolve uma `412 Precondition Failed` resposta HTTP. Este erro indica que qualquer outro processo atualizou a entidade desde a última vez que o cliente o recuperou, ou que o valor ETag especificado no pedido está incorreto.
    *  Se os valores ETag forem os mesmos, ou o cabeçalho **If-Match** contiver o carácter wildcard asterisco `*` (), a API executa a operação solicitada. A operação API também atualiza o valor ETag armazenado da entidade.


> [!NOTE]
> Especificar o carácter wildcard (*) no cabeçalho **If-Match** resulta na API utilizando a estratégia de concuência de última vitória. Neste caso, a comparação ETag não ocorre e o recurso é atualizado sem quaisquer verificações. 

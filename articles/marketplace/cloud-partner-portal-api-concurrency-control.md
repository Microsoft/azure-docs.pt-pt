---
title: Controlo de Concurrency - Azure Marketplace
description: Estratégias de controlo da concurrency para o Portal do Parceiro cloud publicando APIs.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: dec18daea2f4f257e3bb21ee0477e3629b2e630b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85516373"
---
# <a name="concurrency-control"></a>Controlo da Concurrency

> [!NOTE]
> As APIs do Portal do Parceiro cloud estão integradas no Partner Center e continuarão a funcionar após as suas ofertas serem migradas para o Partner Center. A integração introduz pequenas mudanças. Reveja as alterações listadas no [Cloud Partner Portal API Reference](./cloud-partner-portal-api-overview.md) para garantir que o seu código continua a funcionar após a migração para o Partner Center.

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

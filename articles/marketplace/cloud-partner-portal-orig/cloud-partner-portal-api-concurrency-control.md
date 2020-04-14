---
title: Controlo de Concurrency [ Controle concurrency ] Mercado Azure
description: Estratégias de controlo de moedas para o Portal do Parceiro cloud que publica APIs.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 302ba8d550f5e91efe12c620d766550958d3bf68
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81256404"
---
# <a name="concurrency-control"></a>Controlo de Moedas

> [!NOTE]
> As APIs do Portal do Parceiro cloud estão integradas no Partner Center e continuarão a trabalhar após as suas ofertas serem migradas para o Partner Center. A integração introduz pequenas alterações. Reveja as alterações listadas no [Portal do Parceiro cloud Referência API](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) para garantir que o seu código continua a funcionar após a migração para partner Center.

Todas as chamadas para o Portal do Parceiro cloud que publicam APIs devem especificar explicitamente qual a estratégia de controlo de moedas a utilizar. Se não fornecer o cabeçalho **If-Match** resultará numa resposta de erro HTTP 400. Oferecemos duas estratégias para o controlo da moeda.

-   **Otimista** - O cliente que executa a atualização verifica se os dados mudaram desde a última leitura dos dados.
-   **Última vitória** - O cliente atualiza diretamente os dados, independentemente de outra aplicação os ter modificado desde a última leitura.

<a name="optimistic-concurrency-workflow"></a>Fluxo de trabalho con-currency otimista
-------------------------------

Recomendamos a utilização da estratégia de conmoeda otimista, com o seguinte fluxo de trabalho, para garantir que não são feitas edições inesperadas aos seus recursos.

1.  Recupere uma entidade usando as APIs. A resposta inclui um valor ETag que identifica a versão atualmente armazenada da entidade (no momento da resposta).
2.  No momento da atualização, inclua este mesmo valor ETag no cabeçalho obrigatório do pedido **If-Match.**
3.  A API compara o valor ETag recebido no pedido com o valor atual do ETag da entidade numa transação atómica.
    *   Se os valores do ETag forem diferentes, a API devolve uma `412 Precondition Failed` resposta HTTP. Este erro indica que ou outro processo atualizou a entidade desde que o cliente o recuperou pela última vez, ou que o valor ETag especificado no pedido está incorreto.
    *  Se os valores do ETag forem os mesmos, ou`*`se o cabeçalho **If-Match** contiver o caracteres de asterisco wildcard (), a API executa a operação solicitada. A operação DaAI também atualiza o valor ETag armazenado da entidade.


> [!NOTE]
> Especificar o carácter wildcard (*) no cabeçalho **If-Match** resulta na API utilizando a estratégia de conmoeda do Último-um-ganha. Neste caso, a comparação ETag não ocorre e o recurso é atualizado sem qualquer controlo. 

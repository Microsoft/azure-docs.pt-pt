---
title: Controle de simultaneidade | Azure Marketplace
description: Estratégias de controle de simultaneidade para as APIs de publicação de Portal do Cloud Partner.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 6e2f8922d42e40d14338f06be983d3913b20859d
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819753"
---
# <a name="concurrency-control"></a>Controle de simultaneidade

Cada chamada para as APIs de publicação de Portal do Cloud Partner deve especificar explicitamente a estratégia de controle de simultaneidade a ser usada. A falha em fornecer o cabeçalho **If-Match** resultará em uma resposta de erro http 400. Oferecemos duas estratégias para o controle de simultaneidade.

-   **Otimista** – o cliente que executa a atualização verifica se os dados foram alterados desde a última leitura dos dados.
-   **Último WINS** -o cliente atualiza diretamente os dados, independentemente de outro aplicativo ter sido modificado desde o último tempo de leitura.

<a name="optimistic-concurrency-workflow"></a>Fluxo de trabalho de simultaneidade otimista
-------------------------------

É recomendável usar a estratégia de simultaneidade otimista, com o fluxo de trabalho a seguir, para garantir que nenhuma edição inesperada seja feita em seus recursos.

1.  Recupere uma entidade usando as APIs. A resposta inclui um valor de ETag que identifica a versão atualmente armazenada da entidade (no momento da resposta).
2.  No momento da atualização, inclua esse mesmo valor de ETag no cabeçalho de solicitação obrigatório **If-Match** .
3.  A API compara o valor de ETag recebido na solicitação com o valor de ETag atual da entidade em uma transação atômica.
    *   Se os valores de ETag forem diferentes, a API retornará uma `412 Precondition Failed` resposta HTTP. Esse erro indica que outro processo atualizou a entidade desde que o cliente a recuperou pela última vez ou que o valor de ETag especificado na solicitação está incorreto.
    *  Se os valores de ETag forem iguais ou o cabeçalho **If-Match** contiver o caractere curinga asterisco (`*`), a API executará a operação solicitada. A operação de API também atualiza o valor de ETag armazenado da entidade.


> [!NOTE]
> A especificação do caractere curinga (*) no cabeçalho **If-Match** resulta na API usando a estratégia de simultaneidade Last-One-WINS. Nesse caso, a comparação de ETag não ocorre e o recurso é atualizado sem nenhuma verificação. 

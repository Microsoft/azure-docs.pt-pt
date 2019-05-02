---
title: Controlo de simultaneidade | O Azure Marketplace
description: Estratégias de controle de simultaneidade para o Portal do Cloud Partner publicar APIs.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 8cdcfd84a2f3bd4f920b97392255237db173cbf9
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64935607"
---
# <a name="concurrency-control"></a>Controlo de Simultaneidade

Todas as chamadas para o Portal de parceiro de Cloud publicar APIs tem de especificar explicitamente que estratégia de controlo de simultaneidade a utilizar. Falha ao fornecer o **If-Match** cabeçalho resultará numa resposta de erro de HTTP 400. Oferecemos duas estratégias para controlo de simultaneidade.

-   **Otimista** -o executando a atualização de cliente verifica se os dados foram alterados, uma vez que ele pela última vez, ler os dados.
-   **Uma última wins** -o cliente atualizar diretamente os dados, independentemente se outra aplicação modificou-lo uma vez que o último tempo de leitura.

<a name="optimistic-concurrency-workflow"></a>Fluxo de trabalho de simultaneidade otimista
-------------------------------

Recomendamos que utilize a estratégia de simultaneidade otimista, com o seguinte fluxo de trabalho, para garantir que nenhum inesperadas edições são feitas aos seus recursos.

1.  Obter uma entidade com as APIs. A resposta inclui um valor de ETag que identifica a versão atualmente armazenada da entidade (no momento da resposta).
2.  Durante a atualização, inclua este valor de ETag mesmo nos obrigatórias **If-Match** cabeçalho do pedido.
3.  A API compara o valor de ETag recebido no pedido com o valor de ETag atual da entidade numa transação atômica.
    *   Se os valores de ETag forem diferentes, a API devolve uma `412 Precondition Failed` resposta HTTP. Este erro indica que qualquer outro processo tenha atualizado a entidade, uma vez que o cliente o obtida pela última vez, ou que o valor de ETag especificado no pedido está incorreto.
    *  Se os valores de ETag são os mesmos, ou o **If-Match** cabeçalho contém o caráter de asterisco de caráter universal (`*`), a API realiza a operação pedida. A operação de API também atualiza o valor de ETag armazenado da entidade.


> [!NOTE]
> Especificar o caráter universal (*) na **If-Match** cabeçalho resulta na API usando a estratégia de simultaneidade de última-um-wins. Neste caso, a comparação de ETag não ocorre e o recurso será atualizado sem nenhuma verificação. 

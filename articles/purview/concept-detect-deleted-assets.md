---
title: Como as análises detetam ativos eliminados
description: Este artigo explica como uma conta Azure Purview deteta ativos eliminados durante as digitalizações.
author: yaronyg
ms.author: yarong
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/16/2020
ms.openlocfilehash: 9b1515ef00355c831e161c01227678197792cc20
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96555386"
---
# <a name="how-scans-detect-deleted-assets"></a>Como as análises detetam ativos eliminados

Este artigo descreve como o Azure Purview utiliza os resultados da varredura para detetar ativos eliminados.

## <a name="background-info"></a>Informação de fundo

Um catálogo Azure Purview só tem conhecimento do estado de uma loja de dados quando o digitaliza. Para que o catálogo saiba se um ficheiro, tabela ou recipiente foi eliminado, compara a última saída de digitalização com a saída de verificação atual. Por exemplo, suponha que a última vez que digitalizou uma conta Azure Data Lake Storage Gen2, incluía uma pasta chamada *pasta1*. Quando a mesma conta for novamente digitalizada, falta *a pasta1.* Portanto, o catálogo assume que a pasta foi eliminada.

## <a name="detecting-deleted-files"></a>Deteção de ficheiros eliminados

A lógica de detetar ficheiros em falta funciona para múltiplas verificações pelo mesmo utilizador, bem como por diferentes utilizadores. Por exemplo, suponha que um utilizador executa uma verificação única numa loja de dados da Data Lake Storage Gen2 nas pastas A, B e C. Mais tarde, um utilizador diferente na mesma conta executa uma digitalização única diferente nas pastas C, D e E da mesma loja de dados. Como a pasta C foi digitalizada duas vezes, o catálogo verifica-a para possíveis eliminações. As pastas A, B, D e E, no entanto, foram digitalizadas apenas uma vez, e o catálogo não as verifica para obter ativos apagados.

Para manter os ficheiros apagados fora do seu catálogo, é importante fazer exames regulares. O intervalo de digitalização é importante, porque o catálogo não consegue detetar ativos eliminados até que outra digitalização seja executada. Então, se fizer sondagens uma vez por mês numa determinada loja, o catálogo não pode detetar nenhum dado apagado nessa loja até executar a próxima digitalização um mês depois.

Quando enumera grandes lojas de dados como a Data Lake Storage Gen2, existem várias formas (incluindo erros de enumeração e eventos abandonados) de perder informações. Uma verificação específica pode não ter em atenção que um ficheiro foi criado ou apagado. Portanto, a menos que o catálogo tenha a certeza de que um ficheiro foi apagado, não o apagará do catálogo. Esta estratégia significa que pode haver erros quando um ficheiro que não existe na loja de dados digitalizado ainda existe no catálogo. Em alguns casos, uma loja de dados pode ter de ser digitalizada duas ou três vezes antes de capturar certos ativos eliminados.

## <a name="next-steps"></a>Passos seguintes

Para começar com os catálogos Azure Purview, consulte [Quickstart: Create a Purview account](create-catalog-portal.md).

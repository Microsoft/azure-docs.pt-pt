---
title: Azure Monitor vê designer para opções de conversão de livros
description: Opções de conversão para transição de pontos de vista para livros de trabalho no Azure Monitor.
author: austonli
ms.author: aul
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: b8b6b8b41c729c3cbb6cf4589d679e93149e5314
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102043410"
---
# <a name="azure-monitor-view-designer-to-workbooks-conversion-options"></a>Azure Monitor vê designer para opções de conversão de livros
[O view designer](view-designer.md) é uma funcionalidade do Azure Monitor que lhe permite criar visualizações personalizadas para o ajudar a visualizar dados no seu espaço de trabalho Log Analytics, com gráficos, listas e linhas de tempo. Estão a ser eliminados e substituídos por livros que fornecem funcionalidades adicionais. Este artigo compara conceitos fundamentais entre os dois e opções para converter vistas em livros de trabalho.

## <a name="basic-workbook-designs"></a>Desenhos básicos de livros

O view designer tem um estilo estático fixo de representação, enquanto os livros permitem a liberdade de incluir e modificar a forma como os dados são representados. As imagens abaixo mostram dois exemplos de como pode organizar livros de trabalho ao converter vistas.

[Livro vertical](view-designer-conversion-examples.md#vertical) 
 ![ Vertical](media/view-designer-conversion-options/view-designer-vertical.png)

[Livro de tabbed](view-designer-conversion-examples.md#tabbed) 
 ![ Separador de dados de distribuição ](media/view-designer-conversion-options/distribution-tab.png)
 ![ de dados Tipos de dados ao longo do separador de tempo](media/view-designer-conversion-options/over-time-tab.png)

## <a name="tile-conversion"></a>Conversão de azulejos
O designer usa a função de azulejo geral para representar e resumir o estado geral. Estes estão representados em sete azulejos, que vão de números a gráficos. Nos livros de trabalho, os utilizadores podem criar visualizações semelhantes e fixá-las para se assemelharem ao estilo original de azulejos de visão geral. 

![Galeria](media/view-designer-conversion-options/overview.png)


## <a name="view-dashboard-conversion"></a>Ver conversão do painel de instrumentos
Ver azulejos de design normalmente consistem em duas secções, uma visualização e uma lista que corresponde aos dados da visualização, por exemplo o **azulejo da Lista de & donut.**

![Anel](media/view-designer-conversion-options/donut-example.png)

Com os livros de trabalho, permitimos que o utilizador opte por consultar uma ou ambas as secções da vista. Formular consultas em livros de trabalho é um processo simples em duas etapas. Primeiro, os dados são gerados a partir da consulta, e em segundo lugar, os dados são renderizados como uma visualização.  Um exemplo de como esta visão seria recriada em livros de trabalho é o seguinte:

![Converter](media/view-designer-conversion-options/convert-donut.png)


## <a name="next-steps"></a>Passos seguintes
- [Acesso a livros de trabalho & permissões](view-designer-conversion-access.md)
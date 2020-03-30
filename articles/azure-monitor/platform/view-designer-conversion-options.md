---
title: Azure Monitor vê designer para opções de conversão de livros de livros
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 7bfa831332451718c0c9c05023b90104d2b8b02b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658715"
---
# <a name="azure-monitor-view-designer-to-workbooks-conversion-options"></a>Azure Monitor vê designer para opções de conversão de livros de livros
O designer de [visualização](view-designer.md) é uma funcionalidade do Azure Monitor que lhe permite criar vistas personalizadas para o ajudar a visualizar dados no seu espaço de trabalho Log Analytics, com gráficos, listas e cronologias. Estão a ser gradualmente eliminados e substituídos por livros de trabalho que fornecem funcionalidades adicionais. Este artigo compara conceitos fundamentais entre os dois e opções para converter pontos de vista em livros de trabalho.

## <a name="basic-workbook-designs"></a>Desenhos básicos de livro

O designer de visualização tem um estilo de representação estático fixo, enquanto os livros permitem a liberdade de incluir e modificar a forma como os dados são representados. As imagens abaixo retratam dois exemplos de como pode organizar livros de trabalho ao converter vistas.

[Livro vertical](view-designer-conversion-examples.md#vertical)Vertical![
](media/view-designer-conversion-options/view-designer-vertical.png)

[Separador livro](view-designer-conversion-examples.md#tabbed)
![Data type](media/view-designer-conversion-options/distribution-tab.png)
![separado separado Tipos de dados ao longo do separador de tempo](media/view-designer-conversion-options/over-time-tab.png)

## <a name="tile-conversion"></a>Conversão de azulejos
Ver designer usa a função de azulejo sintetizador para representar e resumir o estado geral. Estes são representados em sete azulejos, que vão de números a gráficos. Nos livros de livros, os utilizadores podem criar visualizações semelhantes e fixá-las para se assemelharem ao estilo original dos azulejos de visão geral. 

![Galeria](media/view-designer-conversion-options/overview.png)


## <a name="view-dashboard-conversion"></a>Ver conversão do dashboard
Ver azulejos de design tipicamente consistem em duas secções, uma visualização e uma lista que corresponde aos dados da visualização, por exemplo, o azulejo **Donut & List.**

![Anel](media/view-designer-conversion-options/donut-example.png)

Com livros de reposição, permitimos que o utilizador opte por consultar uma ou ambas as secções da vista. Formular consultas em livros de trabalho é um processo simples em duas etapas. Em primeiro lugar, os dados são gerados a partir da consulta, e em segundo lugar, os dados são renderizados como uma visualização.  Um exemplo de como esta visão seria recriada nos livros de trabalho é a seguinte:

![Converter](media/view-designer-conversion-options/convert-donut.png)


## <a name="next-steps"></a>Passos seguintes
- [Acesso a livros de & permissões](view-designer-conversion-access.md)
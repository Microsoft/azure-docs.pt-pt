---
title: Azure Monitor vê designer para opções de conversão de livros de livros
description: ''
author: austonli
ms.author: aul
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: f04ebc1a4a53825709479ca3f1dc7ce1245fc67f
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77171556"
---
# <a name="azure-monitor-view-designer-to-workbooks-conversion-options"></a>Azure Monitor vê designer para opções de conversão de livros de livros
O designer de [visualização](view-designer.md) é uma funcionalidade do Azure Monitor que lhe permite criar vistas personalizadas para o ajudar a visualizar dados no seu espaço de trabalho Log Analytics, com gráficos, listas e cronologias. Estão a ser gradualmente eliminados e substituídos por livros de trabalho que fornecem funcionalidades adicionais. Este artigo compara conceitos fundamentais entre os dois e opções para converter pontos de vista em livros de trabalho.

## <a name="basic-workbook-designs"></a>Desenhos básicos de livro

O designer de visualização tem um estilo de representação estático fixo, enquanto os livros permitem a liberdade de incluir e modificar a forma como os dados são representados. As imagens abaixo retratam dois exemplos de como pode organizar livros de trabalho ao converter vistas.

[Livro vertical](view-designer-conversion-examples.md#vertical)
![](media/view-designer-conversion-options/view-designer-vertical.png) vertical

[Separador](view-designer-conversion-examples.md#tabbed) livro
![Separador de tipo de dados](media/view-designer-conversion-options/distribution-tab.png)
tipos de dados ![ao longo do tempo](media/view-designer-conversion-options/over-time-tab.png)

## <a name="tile-conversion"></a>Conversão de azulejos
Ver designer usa a função de azulejo sintetizador para representar e resumir o estado geral. Estes são representados em sete azulejos, que vão de números a gráficos. Nos livros de livros, os utilizadores podem criar visualizações semelhantes e fixá-las para se assemelharem ao estilo original dos azulejos de visão geral. 

![Galeria](media/view-designer-conversion-options/overview.png)


## <a name="view-dashboard-conversion"></a>Ver conversão do dashboard
Ver azulejos de design tipicamente consistem em duas secções, uma visualização e uma lista que corresponde aos dados da visualização, por exemplo o azulejo **Donut & List.**

![donut](media/view-designer-conversion-options/donut-example.png)

Com livros de reposição, permitimos que o utilizador opte por consultar uma ou ambas as secções da vista. Formular consultas em livros de trabalho é um processo simples em duas etapas. Em primeiro lugar, os dados são gerados a partir da consulta, e em segundo lugar, os dados são renderizados como uma visualização.  Um exemplo de como esta visão seria recriada nos livros de trabalho é a seguinte:

![Converter](media/view-designer-conversion-options/convert-donut.png)


## <a name="next-steps"></a>Passos seguintes
- [Acesso a livros e permissões](view-designer-conversion-access.md)
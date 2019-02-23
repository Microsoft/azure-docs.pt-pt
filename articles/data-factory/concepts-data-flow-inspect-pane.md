---
title: Fluxo de dados de mapeamento de fábrica de dados do Azure Inspecione e pré-visualização de dados
description: O Azure Data Factory mapeamento de fluxo de dados tem um painel que mostra os metadados de fluxo de dados (Inspect) e pré-visualização de dados quando no modo de depuração (dados pré-visualização)
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 47cde50e0874f0f73523925b6d1b2f8ee4abaea9
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56727830"
---
# <a name="azure-data-factory-mapping-data-flow-transformation-inspect-tab"></a>Transformação de fluxo de dados de mapeamento de fábrica de dados do Azure Inspecione o separador

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Inspecione o painel](media/data-flow/agg3.png "inspecionar o painel")

O painel de inspecionar fornece uma vista para os metadados do fluxo de dados que estamos a transformar. Será capaz de ver as contagens de coluna, colunas foi alterada, colunas adicionadas, tipos de dados, ordenação de coluna e as referências de coluna. "Inspecionar" é uma vista só de leitura de seus metadados. Não é necessário ter o modo de depuração ativado para poder ver metadate no painel de inspecionar.

À medida que altera a forma dos seus dados por meio de transformações, verá os metadados do flow através do painel de inspecionar é alterado. Se não houver um esquema definido na sua transformação de origem, em seguida, metadados não serão visíveis no painel de inspecionar. Falta de metadados é comum em cenários de desvios de esquema.

![Pré-visualização de dados](media/data-flow/datapreview.png "pré-visualização de dados")

Pré-visualização de dados é um painel que fornece uma vista só de leitura dos seus dados, como ele está sendo transformado. Pode ver a saída da transformação e expressões para validar o fluxo de dados. Tem de ter o modo de depuração switched-on para ver as pré-visualizações de dados. Quando clica em colunas na grelha de pré-visualização de dados, verá um painel subsequente para a direita. O painel de pop-out mostrará as estatísticas de perfil sobre cada uma das colunas que selecionar.

![Gráfico de pré-visualização de dados](media/data-flow/chart.png "gráfico de pré-visualização de dados")


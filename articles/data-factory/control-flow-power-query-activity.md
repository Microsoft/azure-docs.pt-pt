---
title: Atividade de consulta de energia na Fábrica de Dados Azure
description: Saiba como utilizar a atividade de consulta de energia para funcionalidades de conflito de dados num pipeline da Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/18/2021
ms.openlocfilehash: fc4f20db55f8e7e0b2f92cb8309c1c128b235089
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100385460"
---
# <a name="power-query-activity-in-data-factory"></a>Atividade de consulta de energia na fábrica de dados

A atividade de Consulta de Energia permite-lhe construir e executar mash-ups de consulta de energia para executar a luta de dados à escala num oleoduto da Data Factory. Pode criar um novo mash-up power query a partir da opção menu de novos recursos ou adicionando uma Atividade de Energia ao seu pipeline.

![Screenshot que mostra Power Consultary no painel de recursos da fábrica.](media/data-flow/power-query-wrangling.png)

Anteriormente, a disputa de dados na Azure Data Factory foi da autoria da opção menu Data Flow. Isto foi alterado para a autoria de uma nova atividade de Power Query. Pode trabalhar diretamente dentro do editor de mash-up power query para realizar a exploração interativa de dados e, em seguida, salvar o seu trabalho. Uma vez concluída, pode pegar na sua atividade de Consulta de Energia e adicioná-la a um oleoduto. A Azure Data Factory irá automaticamente escaloná-lo e operacionalizar os seus dados usando o ambiente de fluxo de dados da Azure Data Factory.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4MFYn]

## <a name="translation-to-data-flow-script"></a>Tradução para script de fluxo de dados

Para atingir a escala com a sua atividade de Consulta de Energia, a Azure Data Factory traduz o seu ```M``` script num script de fluxo de dados para que possa executar a sua Consulta de Energia em escala utilizando o ambiente de fluxo de dados Azure Data Factory Spark. Autore o fluxo de dados de luta utilizando a preparação de dados sem código. Para a lista de funções disponíveis, consulte [as funções de transformação](wrangling-functions.md).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre conceitos de conflito de dados usando [a Power Query na Azure Data Factory](wrangling-tutorial.md)

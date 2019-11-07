---
title: Criação visual no Azure Data Factory
description: Saiba como usar a criação visual no Azure Data Factory
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/09/2019
author: djpmsft
ms.author: daperlov
ms.reviewer: ''
manager: craigg
ms.openlocfilehash: 71f0e283c9609e949cc9b09f20a16c6f5c9c3e50
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681603"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Criação visual no Azure Data Factory

O Azure Data Factory experiência de interface do usuário (UX) permite que você crie visualmente e implante recursos para seu data factory sem precisar escrever nenhum código. Você pode arrastar atividades para uma tela de pipeline, executar execuções de teste, depurar iterativamente e implantar e monitorar as execuções de pipeline.

## <a name="authoring-canvas"></a>Tela de criação

Para abrir a **tela de criação**, clique no ícone de lápis. 

![Tela de criação](media/author-visually/authoring-canvas.png)

Aqui, você criará os pipelines, as atividades, os conjuntos de dados, os serviços vinculados, os fluxos, os gatilhos e os tempos de execução de integração que compõem sua fábrica. Para começar a criar um pipeline usando a tela de criação, consulte [copiar dados usando a atividade de cópia](tutorial-copy-data-portal.md). 

A experiência de criação visual padrão está trabalhando diretamente com o serviço de Data Factory. Azure Repos a integração do git ou do GitHub também tem suporte para permitir o controle do código-fonte e a colaboração para o trabalho em seus pipelines de data factory. Para saber mais sobre as diferenças entre essas experiências de criação, consulte [controle do código-fonte em Azure data Factory](source-control.md).

## <a name="expressions-and-functions"></a>Expressões e funções

As expressões e funções podem ser usadas em vez de valores estáticos para especificar muitas propriedades em Azure Data Factory.

Para especificar uma expressão para um valor de propriedade, selecione **adicionar conteúdo dinâmico** ou clique em **ALT + P** enquanto estiver focalizando o campo.

![Adicionar conteúdo dinâmico](media/author-visually/dynamic-content-1.png)

Isso abre o **Data Factory Construtor de expressões** , em que é possível criar expressões de variáveis de sistema com suporte, saída de atividade, funções e variáveis ou parâmetros especificados pelo usuário. 

![Construtor de expressões](media/author-visually/dynamic-content-2.png)

Para obter informações sobre a linguagem de expressão, consulte [expressões e funções no Azure data Factory](control-flow-expression-language-functions.md).

## <a name="provide-feedback"></a>Enviar comentários

Selecione **comentários** para comentar sobre recursos ou notificar a Microsoft sobre problemas com a ferramenta:

![Comentários](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como monitorar e gerenciar pipelines, confira [monitorar e gerenciar pipelines programaticamente](monitor-programmatically.md).

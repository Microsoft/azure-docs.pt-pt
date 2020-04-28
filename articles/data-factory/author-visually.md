---
title: Autoria visual
description: Saiba como usar a autoria visual na Azure Data Factory
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: djpmsft
ms.author: daperlov
ms.reviewer: ''
manager: anandsub
ms.date: 12/19/2019
ms.openlocfilehash: e7de92878dac72470c0b65d1cf18c1a2d526a0bb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81418495"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Autoria visual na Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A experiência de interface de utilizador da Azure Data Factory (UX) permite-lhe autor visualmente e implementar recursos para a sua fábrica de dados sem ter de escrever qualquer código. Você pode arrastar atividades para uma tela de gasoduto, realizar ensaios, depurar iterativamente e implantar e monitorizar as suas corridas de gasodutos.

Atualmente, o Azure Data Factory UX só é suportado no Microsoft Edge e no Google Chrome.

## <a name="authoring-canvas"></a>Tela de autoria

Para abrir a **tela de autor,** clique no ícone do lápis. 

![Tela de autoria](media/author-visually/authoring-canvas.png)

Aqui, você será o autor dos oleodutos, atividades, conjuntos de dados, serviços ligados, fluxos de dados, gatilhos e tempos de execução de integração que compõem a sua fábrica. Para começar a construir um oleoduto utilizando a tela de autor, consulte [os dados da Cópia utilizando a atividade da cópia](tutorial-copy-data-portal.md). 

A experiência de autor visual padrão está a trabalhar diretamente com o serviço Data Factory. A integração azure Repos Git ou GitHub também é suportada para permitir o controlo de fontes e colaboração para o trabalho nos seus oleodutos de fábrica de dados. Para saber mais sobre as diferenças entre estas experiências de autoria, consulte o [controlo de origem na Azure Data Factory.](source-control.md)

## <a name="expressions-and-functions"></a>Expressões e funções

Expressões e funções podem ser usadas em vez de valores estáticos para especificar muitas propriedades na Azure Data Factory.

Para especificar uma expressão para um valor de propriedade, **selecione Adicionar Conteúdo Dinâmico** ou clique em Alt + **P** enquanto se concentra no campo.

![Adicionar Conteúdo Dinâmico](media/author-visually/dynamic-content-1.png)

Isto abre o **Data Factory Expression Builder** onde pode construir expressões a partir de variáveis de sistema suportadas, saída de atividade, funções e variáveis ou parâmetros especificados pelo utilizador. 

![Construtor de expressões](media/author-visually/dynamic-content-2.png)

Para obter informações sobre a linguagem de expressão, consulte [Expressões e funções na Azure Data Factory](control-flow-expression-language-functions.md).

## <a name="provide-feedback"></a>Enviar comentários

Selecione **Feedback** para comentar sobre funcionalidades ou para notificar a Microsoft sobre problemas com a ferramenta:

![Comentários](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre monitorização e gestão de gasodutos, consulte [monitor e gerenciar os gasodutos programáticamente](monitor-programmatically.md).

---
title: Autoria visual
description: Saiba como utilizar a autoria visual na Azure Data Factory
ms.service: data-factory
ms.topic: conceptual
author: dcstwh
ms.author: weetok
ms.date: 09/08/2020
ms.openlocfilehash: 704360ac37e016de9efe2248181f7db358f5a7cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100371486"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Autoria visual na Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A experiência de interface de utilizador da Azure Data Factory (UX) permite-lhe autoria visual e implanta recursos para a sua fábrica de dados sem ter de escrever nenhum código. Pode arrastar as atividades para uma tela do pipeline, executar testes, depurar iterativamente e implementar e monitorizar as execuções de pipelines.

Atualmente, o Azure Data Factory UX só é suportado no Microsoft Edge e no Google Chrome.

## <a name="authoring-canvas"></a>Tela de autoria

Para abrir **a tela de autoria,** clique no ícone do lápis. 

![Tela de Autoria](media/author-visually/authoring-canvas.png)

Aqui, você autoriza os oleodutos, atividades, conjuntos de dados, serviços ligados, fluxos de dados, gatilhos e tempos de integração que compõem a sua fábrica. Para começar a construir um oleoduto utilizando a tela de autoria, consulte [os dados do Copy utilizando a atividade de cópia](tutorial-copy-data-portal.md). 

A experiência de autoria visual padrão está a trabalhar diretamente com o serviço Data Factory. A integração do Azure Repos Git ou GitHub também é apoiada para permitir o controlo de fontes e colaboração para o trabalho nos seus oleodutos de fábrica de dados. Para saber mais sobre as diferenças entre estas experiências de autoria, consulte [o controlo de origem na Azure Data Factory.](source-control.md)

### <a name="properties-pane"></a>Painel Propriedades

Para recursos de alto nível, tais como oleodutos, conjuntos de dados e fluxos de dados, propriedades de alto nível são editáveis no painel de propriedades no lado direito da tela. O painel de propriedades contém propriedades como nome, descrição, anotações e outras propriedades de alto nível. Subreufontes como atividades de pipeline e transformações de fluxo de dados são editados usando o painel na parte inferior da tela. 

![Painel Propriedades](media/author-visually/properties-pane.png)

O painel de propriedades só abre por defeito na criação de recursos. Para editá-lo, clique no ícone do painel de propriedades localizado no canto superior direito da tela.

### <a name="related-resources"></a>Recursos relacionados

No painel de propriedades, pode ver quais os recursos que dependem do recurso selecionado selecionando o separador **Relacionado.** Qualquer recurso que faça referência ao recurso atual será listado aqui.

![Recursos relacionados](media/author-visually/related-resources.png)

Por exemplo, na imagem acima, um oleoduto e dois fluxos de dados utilizam o conjunto de dados atualmente selecionado.

## <a name="management-hub"></a>Hub de gestão

O centro de gestão, acedido pelo *separador Manage* no Azure Data Factory UX, é um portal que acolhe ações de gestão global para a sua fábrica de dados. Aqui, pode gerir as suas ligações a lojas de dados e cálculos externos, configuração de controlo de fontes e definições de gatilho. Para mais informações, saiba mais sobre as capacidades do centro de [gestão.](author-management-hub.md)

![Gerir serviços ligados](media/author-management-hub/management-hub-linked-services.png)

## <a name="expressions-and-functions"></a>Expressões e funções

Expressões e funções podem ser usadas em vez de valores estáticos para especificar muitas propriedades na Azure Data Factory.

Para especificar uma expressão para um valor de propriedade, **selecione Add Dynamic Content** ou clique em Alt + **P** enquanto se foca no campo.

![Adicionar Conteúdo Dinâmico](media/author-visually/dynamic-content-1.png)

Isto abre o **Data Factory Expression Builder** onde pode construir expressões a partir de variáveis de sistema suportadas, saída de atividade, funções e variáveis ou parâmetros especificados pelo utilizador. 

![Construtor de expressões](media/author-visually/dynamic-content-2.png)

Para obter informações sobre a linguagem de expressão, consulte [Expressões e funções na Azure Data Factory](control-flow-expression-language-functions.md).

## <a name="provide-feedback"></a>Enviar comentários

Selecione **Feedback** para comentar funcionalidades ou para notificar a Microsoft sobre problemas com a ferramenta:

![Comentários](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre monitorização e gestão de gasodutos, consulte [o Monitor e gere os gasodutos programáticamente.](monitor-programmatically.md)
